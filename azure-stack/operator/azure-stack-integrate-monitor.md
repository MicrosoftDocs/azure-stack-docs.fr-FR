---
title: Intégrer une solution de supervision externe à Azure Stack | Microsoft Docs
description: Découvrez comment intégrer Azure Stack à une solution de supervision externe dans votre centre de données.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 02/06/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: 2871b5183833830368307c5d2b5152e3909fd3ea
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618836"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Intégrer une solution de supervision externe à Azure Stack

Pour une surveillance externe de l’infrastructure Azure Stack, vous devez surveiller logiciels, ordinateurs physiques et commutateurs de réseau physique d’Azure Stack. Chacun de ces composants offre une méthode de récupération des informations d’intégrité et d’alerte  :

- Le logiciel Azure Stack offre une API basée sur REST pour récupérer l’intégrité et les alertes. L’utilisation des technologies à définition logicielle, telles que les espaces de stockage direct, les alertes et l’intégrité du stockage fait partie de la surveillance logicielle.
- Les ordinateurs physiques peuvent rendre disponibles des informations relatives à l’intégrité et aux alertes par le biais des contrôleurs de gestion de la carte de base (BMC).
- Les périphériques réseau physiques peuvent mettre à disposition les informations relatives à l’intégrité et aux alertes par le biais du protocole SNMP.

Chaque solution Azure Stack est fournie avec un hôte de cycle de vie du matériel. Cet hôte exécute le logiciel de supervision du fournisseur de matériel OEM (Original Equipment Manufacturer) pour les serveurs physiques et les appareils réseau. Vérifiez auprès de votre fournisseur OEM si ses solutions de surveillance peuvent intégrer directement les solutions de surveillance existantes dans votre centre de données.

> [!IMPORTANT]
> La solution de supervision externe que vous utilisez doit être sans agent. Vous ne pouvez pas installer d’agents tiers à l’intérieur des composants de Azure Stack.

Le diagramme suivant montre le flux de trafic entre un système intégré Azure Stack, l’hôte de cycle de vie du matériel, une solution de supervision externe et un système de collecte de données et de création de tickets externe.

![Schéma montrant le trafic entre Azure Stack, la surveillance et la solution de création de tickets.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

> [!NOTE]
> L’intégration de surveillance externe directement à des serveurs physiques est interdite et bloquée activement par des listes de contrôle d’accès (ACL).  L’intégration de surveillance externe directement aux périphériques réseau physiques étant prise en charge, demandez à votre fournisseur OEM comment activer cette fonctionnalité.

Cet article explique comment intégrer Azure Stack à des solutions de surveillance externes, telles que System Center Operations Manager et Nagios. Il traite également de l’utilisation des alertes par programmation en ayant recours à PowerShell ou à des appels d’API REST.

## <a name="integrate-with-operations-manager"></a>Intégrer Operations Manager

Vous pouvez utiliser Operations Manager pour une surveillance externe d’Azure Stack. Le Pack d’administration de System Center pour Microsoft Azure Stack vous permet de surveiller plusieurs déploiements Azure Stack avec une seule instance d’Operations Manager. Le Pack d’administration utilise les API REST du fournisseur de ressources d’intégrité et du fournisseur de ressources de mise à jour pour communiquer avec Azure Stack. Si vous envisagez de contourner le logiciel de surveillance OEM qui s’exécute sur l’hôte de cycle de vie du matériel, vous pouvez installer les Packs d’administration du revendeur pour surveiller les serveurs physiques. Vous pouvez également utiliser la détection des périphériques réseau Operations Manager pour surveiller les commutateurs réseau.

Le pack d’administration pour Azure Stack offre les fonctionnalités suivantes :

- Possibilité de gérer plusieurs déploiements Azure Stack
- Support pour Azure Active Directory (Azure AD) ou Active Directory Federation Services (AD FS)
- Possibilité de récupérer et de fermer des alertes
- Présence d’un tableau de bord d’intégrité et de capacité
- Détection du mode de maintenance automatique lorsque les correctifs et mises à jour sont en cours
- Tâches de mise à jour forcée pour le déploiement et la région
- Ajout possible d’informations personnalisées à une région
- Prise en charge de la notification et de la création de rapports

Vous pouvez télécharger le Pack d’administration de System Center pour Microsoft Azure Stack et le [guide de l’utilisateur](https://www.microsoft.com/en-us/download/details.aspx?id=55184) associé ici, ou directement à partir d’Operations Manager.

Pour une solution de création de tickets, vous pouvez intégrer Operations Manager à System Center Service Manager. Le connecteur du produit intégré assure la communication bidirectionnelle qui vous permet de fermer une alerte dans Azure Stack et Operations Manager après la résolution d’une demande de service dans Service Manager.

Le schéma suivant illustre l’intégration d’Azure Stack à un déploiement de System Center existant. Vous pouvez automatiser davantage Service Manager avec System Center Orchestrator ou Service Management Automation (SMA) pour effectuer des opérations dans Azure Stack.

![Schéma illustrant l’intégration avec OM, Service Manager et SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Intégrer Nagios

Un plug-in de supervision Nagios a été développé en même temps que les solutions Cloudbase partenaires ; il est disponible sous licence de logiciel libre et permissive - MIT (Massachusetts Institute of Technology).

Le plug-in est écrit dans Python et s’appuie sur l’API REST du fournisseur de ressources d’intégrité. Il offre des fonctionnalités de base pour récupérer et fermer des alertes dans Azure Stack. À l’instar du Pack d’administration de System Center, il vous permet d’ajouter plusieurs déploiements Azure Stack et d’envoyer des notifications.

Le plug-in fonctionne avec Nagios Enterprise et Nagios Core. Vous pouvez le télécharger [ici](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Le site de téléchargement comporte également des informations sur l’installation et la configuration.

### <a name="plugin-parameters"></a>Paramètres du plug-in

Configurez le fichier du plug-in « Azurestack_plugin.py » avec les paramètres suivants :

| Paramètre | Description | Exemples |
|---------|---------|---------|
| *arm_endpoint* | Point de terminaison Azure Resource Manager (administrateur) | https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Point de terminaison Azure Resource Manager (administrateur)  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | ID d’abonnement administrateur | À récupérer via le portail d’administration ou via PowerShell |
| *User_name* | Nom d’utilisateur de l’abonnement opérateur | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Mot de passe de l’abonnement opérateur | monmotdepasse |
| *Client_id* | Client | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Nom de la région Azure Stack | local |
|  |  |

*Le GUID PowerShell qui est fourni est universel. Vous pouvez l’utiliser pour chaque déploiement.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Utiliser PowerShell pour surveiller l’intégrité et les alertes

Si vous n’utilisez pas Operations Manager, Nagios ou une solution s’appuyant sur Nagios, vous pouvez utiliser PowerShell pour activer un large éventail de solutions de surveillance à intégrer à Azure Stack.

1. Si vous voulez utiliser PowerShell, assurez-vous que [PowerShell est installé et configuré](azure-stack-powershell-install.md) pour un environnement d’opérateur Azure Stack. Installez PowerShell sur un ordinateur local qui peut atteindre le point de terminaison Resource Manager (administrateur) (https://adminmanagement.[région].[FQDN_externe]).

2. Exécutez les commandes suivantes pour vous connecter à l’environnement Azure Stack en tant qu’opérateur Azure Stack :

   ```powershell
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https:\//adminmanagement.[Region].[External_FQDN] `
      -AzureKeyVaultDnsSuffix adminvault.[Region].[External_FQDN] `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Utilisez les commandes telles que les exemples suivants pour les alertes :
   ```powershell
    #Retrieve all alerts
    $Alerts = Get-AzsAlert
    $Alerts

    #Filter for active alerts
    $Active = $Alerts | Where-Object { $_.State -eq "active" }
    $Active

    #Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    $RPHealth = Get-AzsRPHealth
    $RPHealth

    #Retrieve infrastructure role instance health
    $FRPID = $RPHealth | Where-Object { $_.DisplayName -eq "Capacity" }
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId
    ```

## <a name="learn-more"></a>En savoir plus

Pour plus d’informations sur la surveillance de l’intégrité intégrée, consultez [Surveiller l’intégrité et les alertes dans Azure Stack](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Étapes suivantes

[Intégration de la sécurité](azure-stack-integrate-security.md)
