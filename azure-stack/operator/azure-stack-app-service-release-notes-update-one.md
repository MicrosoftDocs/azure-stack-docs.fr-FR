---
title: Notes de publication d'App Service sur Azure Stack Hub Update 1
description: Découvrez les améliorations, les correctifs et les problèmes connus concernant la version Update 1 d'App Service pour Azure Stack Hub.
author: bryanla
manager: stefsch
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: 44baf18b4e7b1acb39125a4ccdeb4228557de4d4
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77703517"
---
# <a name="app-service-on-azure-stack-hub-update-1-release-notes"></a>Notes de publication d'App Service sur Azure Stack Hub Update 1

Ces notes de publication décrivent les améliorations, les correctifs et les problèmes connus dans la mise à jour 1 d'Azure App Service sur Azure Stack Hub. Les problèmes connus ont été répartis en trois sections : ceux qui concernent directement le déploiement, ceux qui concernent le processus de mise à jour et ceux qui concernent la build (après l’installation).

> [!IMPORTANT]
> Appliquez la mise à jour 1802 à votre système intégré Azure Stack Hub, ou déployez le dernier Kit de développement Azure Stack Hub (ASDK) avant de déployer Azure App Service.

## <a name="build-reference"></a>Référence de build

Le numéro de build de la mise à jour 1 d'App Service sur Azure Stack Hub est **69.0.13698.9**.

### <a name="prerequisites"></a>Prérequis

> [!IMPORTANT]
> Les nouveaux déploiements d'Azure App Service sur Azure Stack Hub nécessitent désormais un [certificat avec caractères génériques à trois sujets](azure-stack-app-service-before-you-get-started.md#get-certificates) en raison des améliorations apportées à la gestion de l'authentification unique pour Kudu dans Azure App Service. Le nouveau sujet est **\*.sso.appservice.\<région\>.\<nom_domaine\>.\<extension\>**

Avant d'entamer le déploiement, consultez les [Conditions préalables au déploiement d'App Service sur Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).

### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Azure App Service sur Azure Stack Hub Update 1 contient les améliorations et correctifs suivants :

- **Haute disponibilité d'Azure App Service** : la mise à jour 1802 d'Azure Stack Hub a permis le déploiement de charges de travail sur des domaines d'erreur, permettant ainsi à l'infrastructure App Service d'être tolérante aux pannes puisqu'elle est déployée sur des domaines d'erreur. Par défaut, tous les nouveaux déploiements d’Azure App Service disposent de cette fonctionnalité. Pour les déploiements effectués avant la mise à jour 1802 d'Azure Stack Hub, consultez la [documentation relative aux domaines d'erreur App Service](azure-stack-app-service-before-you-get-started.md).

- **Possibilité de procéder au déploiement sur un réseau virtuel existant** : les utilisateurs peuvent désormais déployer App Service sur Azure Stack Hub au sein d'un réseau virtuel existant. Les déploiements effectués sur un réseau virtuel existant permettent aux utilisateurs de se connecter à SQL Server et au serveur de fichiers (qui sont nécessaires pour Azure App Service) via des ports privés. Pendant le déploiement, les clients peuvent choisir d’effectuer le déploiement sur un réseau virtuel existant. Toutefois, ils [doivent créer des sous-réseaux destinés à être utilisés par App Service](azure-stack-app-service-before-you-get-started.md#virtual-network) avant le déploiement.

- Mises à jour des **portails Locataire, Administration et Functions d’App Service, ainsi que des outils Kudu**. Compatible avec la version SDK du portail Azure Stack Hub.

- Mises à jour du **runtime d’Azure Functions** à la **version 1.0.11388**.

- **Mises à jour des outils et frameworks d’applications suivants** :
    - Ajout de la prise en charge de **.NET Core 2.0**.
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
    - Ajout des mises à jour **PHP** :
        - 5.6.32
        - 7.0.26 (x86 et x64)
        - 7.1.12 (x86 et x64)
    - Mise à jour de **Git pour Windows** vers la version 2.14.1
    - Mise à jour de **Mercurial** vers la version 4.5.0

  - Ajout de la prise en charge de la fonctionnalité **HTTPS uniquement** avec la fonctionnalité Domaine personnalisé du portail utilisateur d'App Service.

  - Ajout de la validation de la connexion au stockage dans le sélecteur de stockage personnalisé pour Azure Functions.

#### <a name="fixes"></a>Correctifs

- Lorsque vous créez un package de déploiement hors connexion, les utilisateurs ne reçoivent plus le message d’erreur « Accès refusé » quand ils ouvrent le dossier à partir du programme d’installation App Service.

- Des problèmes liés à l'utilisation de la fonctionnalité Domaines personnalisés sur le portail utilisateur d'App Service ont été résolus.

- Il n’est plus possible pour les utilisateurs de choisir des noms d’administrateur réservés lors de l’installation.

- Il est désormais possible de déployer App Service sur un serveur de fichiers **joint à un domaine**.

- La récupération du certificat racine Azure Stack Hub dans le script a été améliorée et il est désormais possible de valider le certificat racine dans le programme d'installation d'App Service.

- L’état incorrect qui était retourné à Azure Resource Manager lors de la suppression d’un abonnement avec un espace de noms Microsoft.Web contenant des ressources est désormais correct.

### <a name="known-issues-with-the-deployment-process"></a>Problèmes connus liés au processus de déploiement

- Erreurs de validation du certificat.

    Certains clients ont rencontré des problèmes pendant le provisionnement des certificats pour le programme d’installation App Service lors du déploiement sur un système intégré, en raison d’une validation trop restrictive dans le programme d’installation. Le programme d’installation App Service a été republié et les clients doivent [télécharger le programme d’installation mis à jour](https://aka.ms/appsvconmasinstaller). Si vous continuez à rencontrer des problèmes de validation des certificats avec le programme d’installation mis à jour, contactez le support technique.

- Problème de récupération du certificat racine Azure Stack Hub à partir du système intégré.

    Une erreur présente dans Get-AzureStackRootCert.ps1 empêchait les clients de récupérer le certificat racine Azure Stack Hub lors de l'exécution du script sur un ordinateur où aucun certificat racine n'était installé. Le script a également été republié, ce qui a permis de résoudre le problème. [Téléchargez les scripts d’assistance mis à jour](https://aka.ms/appsvconmashelpers). Si vous continuez à rencontrer des problèmes de récupération du certificat racine avec le script mis à jour, contactez le support technique.

### <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour

- Il n'existe aucun problème connu lié à la mise à jour d'Azure App Service sur Azure Stack Hub Update 1.

### <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

- L’échange d’emplacement ne fonctionne pas.

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

2. Sur le portail d'administration d'Azure Stack Hub, sous Machines virtuelles, accédez à la machine virtuelle **CN0-VM**, puis cliquez sur **Se connecter** pour ouvrir une session Bureau à distance à l'aide de l'instance de contrôleur. Utilisez les informations d’identification spécifiées pendant le déploiement d’App Service.
3. Démarrez **PowerShell en tant qu’administrateur** et exécutez le script suivant :

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

Si vous avez choisi de procéder au déploiement dans un réseau virtuel existant et avec une adresse IP interne pour vous connecter à votre serveur de fichiers, vous devez ajouter une règle de sécurité sortante, qui autorise le trafic SMB entre le sous-réseau worker et le serveur de fichiers. Accédez au WorkersNsg dans le portail administrateur, puis ajoutez une règle de sécurité sortante comportant les propriétés suivantes :

- Source : Quelconque
- Plage de ports source : : *
- Destination : Adresses IP
- Plage d’adresses IP de destination : plage d’adresses IP de votre serveur de fichiers
- Plage de ports de destination : 445
- Protocole : TCP
- Action : Allow
- Priorité : 700
- Nom : Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Problèmes connus pour les administrateurs cloud utilisant Azure App Service sur Azure Stack Hub

Reportez-vous à la documentation fournie dans les [Notes de publication d'Azure Stack Hub 1802](azure-stack-update-1903.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation d'Azure App Service, consultez [Vue d'ensemble d'Azure App Service sur Azure Stack Hub](azure-stack-app-service-overview.md).
- Pour plus d'informations sur la préparation au déploiement d'App Service sur Azure Stack Hub, consultez [Conditions préalables au déploiement d'App Service sur Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
