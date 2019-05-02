---
title: Notes de publication 1 de mise à jour d’App Service sur Azure Stack | Microsoft Docs
description: Découvrez le contenu de la mise à jour d’App Service sur Azure Stack, les problèmes connus et l’emplacement à partir duquel la télécharger.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digmobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/25/2018
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: sethm
ms.lastreviewed: 03/20/2018
ms.openlocfilehash: 86eb23e57516bc108557bbdc4f2d131153f5e855
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64306485"
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>Notes de publication d’App Service sur Azure Stack Update 1

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Ces notes de publication décrivent les améliorations et les correctifs apportés à Azure App Service sur Azure Stack Update 1, ainsi que les problèmes connus. Les problèmes connus ont été répartis selon qu’ils concernent le déploiement, le processus de mise à jour ou la build (après l’installation).

> [!IMPORTANT]
> Appliquez la mise à jour 1802 à votre système intégré Azure Stack ou déployez le dernier Kit de développement Azure Stack avant de déployer Azure App Service.
>
>

## <a name="build-reference"></a>Référence de build

Le numéro de build d’App Service sur Azure Stack Update 1 est **69.0.13698.9**

### <a name="prerequisites"></a>Prérequis

> [!IMPORTANT]
> Les nouveaux déploiements d’Azure App Service sur Azure Stack nécessitent désormais un [certificat avec caractères génériques à trois sujets](azure-stack-app-service-before-you-get-started.md#get-certificates) en raison des améliorations apportées à la gestion de l’authentification unique pour Kudu dans Azure App Service. Le nouveau sujet est **\*.sso.appservice.\<région\>.\<nom_domaine\>.\<extension\>**
>
>

Avant de passer au déploiement, consultez la [documentation Avant de commencer](azure-stack-app-service-before-you-get-started.md).

### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Azure App Service sur Azure Stack Update 1 inclut les améliorations et correctifs suivants :

- **Haute disponibilité d’Azure App Service** : La mise à jour 1802 Azure Stack permet désormais le déploiement de charges de travail sur des domaines d’erreur. L’infrastructure d’App Service devient donc tolérante aux pannes, puisqu’elle est déployée sur des domaines d’erreur. Par défaut, tous les nouveaux déploiements d’Azure App Service comprennent cette fonctionnalité. Pour les déploiements effectués avant la mise à jour 1802 Azure Stack, consultez la [documentation relative aux domaines d’erreur App Service](azure-stack-app-service-before-you-get-started.md ).

- **Possibilité d’effectuer le déploiement sur un réseau virtuel existant** : Les utilisateurs peuvent désormais déployer App Service sur Azure Stack sur un réseau virtuel existant. Les déploiements effectués sur un réseau virtuel existant permettent aux utilisateurs de se connecter à SQL Server et au serveur de fichiers (qui sont nécessaires à Azure) via des ports privés. Pendant le déploiement, les clients peuvent choisir d’effectuer le déploiement sur un réseau virtuel existant. Toutefois, ils [doivent créer des sous-réseaux destinés à être utilisés par App Service](azure-stack-app-service-before-you-get-started.md#virtual-network) avant le déploiement.

- Mises à jour des **portails Locataire, Administration et Functions d’App Service, ainsi que des outils Kudu**. Cohérentes avec celles de la version du SDK du portail Azure Stack.

- Mises à jour du **runtime d’Azure Functions** à la **version 1.0.11388**.

- **Mises à jour des outils et frameworks d’applications suivants** :
    - Ajout de la prise en charge de **.NET Core 2.0**
    - Ajout des versions **Node.JS** :
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - Ajout des versions **NPM** :
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Ajout des mises à jour **PHP** :
        - 5.6.32
        - 7.0.26 (x86 et x64)
        - 7.1.12 (x86 et x64)
    - Mise à jour de **Git pour Windows** vers la version 2.14.1
    - Mise à jour de **Mercurial** vers la version 4.5.0

  - Ajout de la prise en charge de la fonctionnalité **HTTPS uniquement** avec la fonctionnalité Domaine personnalisé dans le portail Locataire d’App Service. 

  - Ajout de la validation de la connexion au stockage dans le sélecteur de stockage personnalisé pour Azure Functions 

#### <a name="fixes"></a>Correctifs

- Lorsque vous créez un package de déploiement hors connexion, les utilisateurs ne reçoivent plus le message d’erreur « Accès refusé » quand ils ouvrent le dossier à partir du programme d’installation App Service.

- Des problèmes liés à l’utilisation de la fonctionnalité Domaines personnalisés du portail Locataire d’App Service ont été résolus.

- Il n’est plus possible pour les utilisateurs de choisir des noms d’administrateur réservés lors de l’installation.

- Il est désormais possible de déployer App Service sur un serveur de fichiers **joint à un domaine**.

- La récupération du certificat racine Azure Stack dans le script a été améliorée et il est désormais possible de valider le certificat racine dans le programme d’installation d’App Service.

- L’état incorrect qui était retourné à Azure Resource Manager lors de la suppression d’un abonnement avec un espace de noms Microsoft.Web contenant des ressources est désormais correct.

### <a name="known-issues-with-the-deployment-process"></a>Problèmes connus liés au processus de déploiement

- Erreurs de validation du certificat

Certains clients ont rencontré des problèmes lors de l’approvisionnement des certificats pour le programme d’installation de App Service lors du déploiement sur un système intégré, en raison d’une validation trop restrictive dans le programme d’installation. Le programme d’installation App Service a été republié, les clients doivent [télécharger le programme d’installation mis à jour](https://aka.ms/appsvconmasinstaller). Si vous continuez à rencontrer des problèmes de validation des certificats avec le programme d’installation mis à jour, contactez le support technique.

- Problème de récupération du certificat racine Azure Stack à partir du système intégré.

Une erreur dans le Get-AzureStackRootCert.ps1 a provoqué l’échec de récupération par les clients du certificat racine Azure Stack lors de l’exécution du script sur une machine sans certificat racine installé. Le script a également été republié, pour résoudre ce problème et les clients de la requête [téléchargent les scripts d’assistance mis à jour](https://aka.ms/appsvconmashelpers). Si vous continuez à rencontrer des problèmes de récupération du certificat racine avec le script mis à jour, contactez le support technique.

### <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour

- Il n’existe aucun problème connu lié à la mise à jour d’Azure App Service sur Azure Stack Update 1.

### <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

- L’emplacement d'échange ne fonctionne pas

L’emplacement d’échange des sites est rompu dans cette mise en production. Pour restaurer la fonctionnalité, procédez comme suit :

1. Modifiez le groupe de sécurité réseau ControllersNSG pour **Autoriser** des connexions Bureau à distance pour les instances de contrôleur App Service. Remplacez AppService.local par le nom du groupe de ressources dans lequel vous avez déployé App Service.

    ```powershell
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

2. Accédez à la **CN0-VM** sous Machines virtuelles dans le portail d’administrateur Azure Stack et **cliquez sur Se connecter** pour ouvrir une session Bureau à distance avec l’instance de contrôleur. Utilisez les informations d’identification spécifiées pendant le déploiement d’App Service.
3. Démarrez **PowerShell en tant qu'administrateur** et exécutez le script suivant

    ```powershell
        Import-Module appservice

        $sm = new-object Microsoft.Web.Hosting.SiteManager

        if($sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$true)
        {
          $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$false
        #  'Slot swap mode reverted'
        }
        
        # Confirm new setting is false
        $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus
        
        # Commit Changes
        $sm.CommitChanges()

        Get-AppServiceServer -ServerType ManagementServer | ForEach-Object Repair-AppServiceServer
        
    ```

4. Fermez la session Bureau à distance.
5. Restaurez le groupe de sécurité réseau ControllersNSG pour **Refuser** des connexions Bureau à distance aux instances de contrôleur App Service. Remplacez AppService.local par le nom du groupe de ressources dans lequel vous avez déployé App Service.

    ```powershell

        Add-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

6. Les Workers ne peuvent pas atteindre le serveur de fichiers si App Service est déployé dans un réseau virtuel existant et si le serveur de fichiers est uniquement disponible sur le réseau privé.

Si vous avez choisi de procéder au déploiement dans un réseau virtuel existant et une adresse IP interne pour vous connecter à votre serveur de fichiers, vous devez ajouter une règle de sécurité sortante, qui autorise le trafic SMB entre le sous-réseau Worker et le serveur de fichiers. Pour ce faire, accédez au WorkersNsg dans le portail d’administration, puis ajoutez une règle de sécurité sortante comportant les propriétés suivantes :

- Source : Quelconque
- Plage de ports source : : *
- Destination : Adresses IP
- Plage d’adresses IP de destination : plage d’adresses IP de votre serveur de fichiers
- Plage de ports de destination : 445
- Protocole : TCP
- Action : AUTORISER
- Priorité : 700
- Nom : Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problèmes connus des administrateurs cloud utilisant Azure App Service sur Azure Stack

Reportez-vous aux [notes de publication de la mise à jour 1802 Azure Stack](azure-stack-update-1903.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation d’Azure App Service, consultez [Vue d’ensemble d’App Service sur Azure Stack](azure-stack-app-service-overview.md).
- Pour plus d’informations sur la préparation au déploiement d’App Service on Azure Stack, consultez [Avant de commencer avec App Service sur Azure Stack](azure-stack-app-service-before-you-get-started.md).

<!-- Update_Description: wording update -->