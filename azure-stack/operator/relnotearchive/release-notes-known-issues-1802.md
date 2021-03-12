---
title: Mise à jour 1802 d’Azure Stack | Microsoft Docs
description: Découvrez le contenu de la mise à jour 1802 pour les systèmes intégrés Azure Stack, les problèmes connus et l’emplacement à partir duquel la télécharger.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: brenduns
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 63a42e4b3cab1273c84c52367178c00442bf2cf2
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102187229"
---
# <a name="azure-stack-1802-update"></a>Mise à jour 1802 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit les améliorations et correctifs contenus dans cette mise à jour 1802, les problèmes connus dans cette publication, et l’emplacement de téléchargement de la mise à jour. Les problèmes connus sont divisés en problèmes directement liés au processus de mise à jour et en problèmes propres à la build (après l’installation).

> [!IMPORTANT]        
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="build-reference"></a>Référence de build    
Le numéro de build de mise à jour d’Azure Stack 1802 est **20180302.1**.  


## <a name="before-you-begin"></a>Avant de commencer    
> [!IMPORTANT]    
> Ne tentez pas de créer des machines virtuelles pendant l’installation de cette mise à jour. Pour plus d’informations sur la gestion des mises à jour, consultez [Manage updates in Azure Stack overview](../azure-stack-updates.md) (Vue d’ensemble de la gestion des mises à jour dans Azure Stack).


### <a name="prerequisites"></a>Prérequis
- Installez la mise à jour 1712 d’Azure Stack avant d’appliquer la mise à jour 1802.    

- Installez le **correctif logiciel AzS 1.0.180312.1 - Build 20180222.2** avant d’appliquer la mise à jour 1802 d’Azure Stack. Ce correctif logiciel met à jour Windows Defender et est disponible lorsque vous téléchargez des mises à jour pour Azure Stack.

  Pour installer le correctif logiciel, suivez les procédures normales pour [l’installation des mises à jour d’Azure Stack](../azure-stack-apply-updates.md). La mise à jour s’appelle **Correctif logiciel AzS 1.0.180312.1** et comprend les fichiers suivants : 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  Après avoir chargé ces fichiers dans un compte de stockage et un conteneur, exécutez la programmation d’installation à partir de la vignette Mise à jour du portail d’administration. 
  
  Contrairement aux mises à jour d’Azure Stack, l’installation de cette mise à jour ne modifie pas la version d’Azure Stack.  Pour confirmer l’installation de cette mise à jour, affichez la liste des **mises à jour installées**.
 


### <a name="post-update-steps"></a>Étapes après la mise à jour
Après l’installation de la version 1802, installez les correctifs logiciels applicables. Pour plus d’informations, consultez les articles suivants de la base de connaissances, ainsi que notre [stratégie de maintenance](../azure-stack-servicing-policy.md). 
- Correctif Azure Stack **1.0.180302.4**. [KB 4131152 - Existing Virtual Machine Scale Sets may become unusable]( https://support.microsoft.com/help/4131152) (Les groupes de machines virtuelles identiques existants peuvent devenir inutilisables) 

  Ce correctif résout également les problèmes détaillés dans [KB 4103348 - Network Controller API service crashes when you try to install an Azure Stack update](https://support.microsoft.com/help/4103348) (Le service API du contrôleur réseau se bloque quand vous essayez d’installer une mise à jour Azure Stack).


### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs
Cette mise à jour inclut les améliorations et les correctifs suivants pour Azure Stack.

- **La prise en charge est ajoutée pour les versions d’API de service de stockage Azure suivantes** :
    - 2017-04-17 
    - 2016-05-31 
    - 2015-12-11 
    - 2015-07-08 
    
    Pour plus d’informations, consultez [Stockage Azure Stack : Différences et considérations](../../user/azure-stack-acs-differences.md).

- **Prise en charge des [objets blob de blocs](../../user/azure-stack-acs-differences.md) plus grands**:
    - La taille maximum de bloc autorisée passe de 4 Mo à 100 Mo.
    - La taille maximum d’objet blob passe de 195 Go à 4,75 To.  

- **Infrastructure backup** s’affiche maintenant dans la vignette Fournisseurs de ressources, et des alertes de sauvegarde sont activées. Pour plus d’informations sur le service Infrastructure Backup, consultez [Sauvegarde et récupération de données pour Azure Stack avec le service Infrastructure Backup](../azure-stack-backup-infrastructure-backup.md).

- **Mettez à jour vers la cmdlet *Test-AzureStack*** pour améliorer les diagnostics de stockage. Pour plus d’informations sur cette cmdlet, consultez [Exécuter un test de validation pour Azure Stack](../azure-stack-diagnostic-test.md).

- **Améliorations du contrôle d’accès en fonction du rôle** : vous pouvez maintenant utiliser le contrôle d’accès en fonction du rôle pour déléguer des autorisations à des groupes d’utilisateurs universels lorsqu’Azure Stack est déployé avec AD FS. Pour en savoir plus sur le contrôle d’accès en fonction du rôle, consultez [Gérer le contrôle d’accès en fonction du rôle](../azure-stack-manage-permissions.md).

- **La prise en charge est ajoutée pour plusieurs domaines d’erreur**.  Pour plus d’informations, consultez High availability for Azure Stack (Haute disponibilité pour Azure Stack).

- **Prise en charge des mises à niveau de la mémoire physique** - Vous pouvez maintenant augmenter la capacité de mémoire du système Azure Stack intégré après le déploiement initial. Pour plus d’informations, consultez [Gérer la capacité de mémoire physique pour Azure Stack](../azure-stack-manage-storage-physical-memory-capacity.md).

- **Divers correctifs** pour les performances, la stabilité, la sécurité et le système d’exploitation utilisé par Azure Stack.

<!-- #### New features -->

<!-- #### Fixes -->

### <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour    
*Il n’y a aucun problème connu pour l’installation de la mise à jour 1802.*


### <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)
Les éléments suivants sont des problèmes connus après l’installation pour la build **20180302.1**

#### <a name="portal"></a>Portail
<!-- 2332636 - IS -->  
-   Lorsque vous utilisez AD FS pour votre système d’identité Azure Stack et effectuez la mise à jour vers cette version d’Azure Stack, le propriétaire par défaut de l’abonnement Fournisseur par défaut redevient l’utilisateur **CloudAdmin** intégré.  
  Solution de contournement : pour résoudre ce problème après avoir installé cette mise à jour, utilisez l’étape 3 de la procédure [Déclencher l’automation pour configurer un fournisseur de revendications de confiance dans Azure Stack](../azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub) afin de réinitialiser le propriétaire de l’abonnement Fournisseur par défaut.   

- Vous ne pouvez pas [effectuer une nouvelle demande de support dans la liste déroulante](../azure-stack-manage-portals.md#quick-access-to-help-and-support) à partir du portail administrateur. À la place, utilisez le lien suivant :     
    - Pour les systèmes Azure Stack intégrés, utilisez https://aka.ms/newsupportrequest.

<!-- 2050709 --> 
-  Dans le portail d’administration, il n’est pas possible de modifier les métriques de stockage pour le service Blob, le service de Table ou le service de File d’attente. Lorsque vous accédez au stockage et que vous sélectionnez la vignette du service Blob, de Table ou de File d’attente, un nouveau panneau qui affiche un graphique des métriques pour ce service s’ouvre. Si vous sélectionnez ensuite Modifier en haut de la vignette du graphique des métriques, le panneau Modifier le graphique s’ouvre, mais n’affiche pas les options pour modifier les métriques.

- Il se peut qu’il ne soit pas possible d’afficher les ressources de calcul ou de stockage sur le portail d’administration. Ce problème est dû au fait qu’une erreur s’est produite pendant l’installation de la mise à jour, et que celle-ci a été, à tort, signalée comme réussie. Si ce problème se produit, contactez les services de support technique Microsoft pour obtenir de l’aide.

- Il se peut qu’un tableau de bord vide s’affiche sur le portail. Pour récupérer le tableau de bord, sélectionnez l’icône d’engrenage dans l’angle supérieur droit du portail, puis choisissez **Restaurer les paramètres par défaut**.

- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.

- Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack. Pour résoudre ce problème, utilisez PowerShell pour vérifier les autorisations.

- Dans le tableau de bord du portail d’administration, la vignette Mise à jour ne parvient pas à afficher les informations sur les mises à jour. Pour résoudre ce problème, cliquez sur la vignette pour l’actualiser.

-   Dans le portail d’administration, vous pouvez voir une alerte critique pour le composant Microsoft.Update.Admin. Le nom de l’alerte, la description et la correction s’affichent tous comme suit :  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (ERREUR - Absence du modèle de FaultType ResourceProviderTimeout.)

    Cette alerte peut être ignorée en toute sécurité. 

<!-- 2253274 --> 
- Sur le portail d’administration et le portail utilisateur, le chargement du panneau Paramètres des sous-réseaux virtuels échoue. Pour contourner le problème, utilisez PowerShell et l’applet de commande [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?preserve-view=true&view=azurermps-5.5.0) afin de voir et de gérer ces informations.

- Dans le portail d’administration et le portail utilisateur, le panneau Vue d’ensemble ne parvient pas à charger lorsque vous sélectionnez le panneau Vue d’ensemble des comptes de stockage qui ont été créés avec une ancienne version de l’API (exemple : 2015-06-15). Cela inclut les comptes de stockage de système comme **updateadminaccount** utilisé pendant la mise à jour et le correctif. 

  Pour résoudre ce problème, utilisez PowerShell pour exécuter le script **ResourceSynchronization.ps1** pour restaurer l’accès aux détails du compte de stockage. [Le script est disponible à partir de GitHub](https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts) et doit s’exécuter avec des informations d’identification d’administrateur de service sur le point de terminaison privilégié. 

- Le chargement du panneau **Service Health** a échoué. Si vous ouvrez le panneau Service Health dans le portail d’administration ou utilisateur, Azure Stack affiche une erreur et ne charge pas les informations. Ce comportement est normal. Même s’il est possible de sélectionner et d’ouvrir Service Health, cette fonctionnalité n’est pas encore disponible ; elle sera implémentée dans une prochaine version d’Azure Stack.


#### <a name="health-and-monitoring"></a>Intégrité et surveillance
<!-- 1264761 - IS ASDK -->  
-   Vous risquez de recevoir des alertes pour le composant du *contrôleur d’intégrité* contenant les informations suivantes :  

  Alerte 1 :
   - NOM : Rôle d’infrastructure défectueux
   - GRAVITÉ : Avertissement
   - COMPOSANT : Contrôleur d’intégrité
   - DESCRIPTION : L’analyseur de pulsations du contrôleur d’intégrité n’est pas disponible. Cela peut affecter les rapports et les métriques d’intégrité.  

  Alerte 2 :
   - NOM : Rôle d’infrastructure défectueux
   - GRAVITÉ : Avertissement
   - COMPOSANT : Contrôleur d’intégrité
   - DESCRIPTION : L’analyseur d’erreur du contrôleur d’intégrité n’est pas disponible. Cela peut affecter les rapports et les métriques d’intégrité.

  Ces deux alertes peuvent être ignorées en toute sécurité. Elles se ferment automatiquement dans le temps.  


#### <a name="marketplace"></a>Place de marché
- Les utilisateurs ont la possibilité de parcourir entièrement le marketplace, et peuvent voir des éléments administratifs, tels que des plans et des offres, qui ne sont pas fonctionnels pour eux.

#### <a name="compute"></a>Calcul
- Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

<!-- 2290877  --> 
- Vous ne pouvez pas effectuer de scale-up pour un groupe de machines virtuelles identiques (VMSS) si celui-ci a été créé avec une version d’Azure Stack antérieure à 1802. Ceci est dû à la modification de la prise en charge de l’utilisation des groupes à haute disponibilité avec les groupes de machines virtuelles identiques. Cette prise en charge a été ajoutée avec la version 1802.  Si vous tentez d’ajouter des instances en vue de mettre à l’échelle un groupe VMSS qui a été créé avant l’ajout de cette prise en charge, l’ajout échoue avec le message suivant : *Échec du provisionnement*. 

  Ce problème est résolu dans la version 1803. Pour résoudre ce problème avec la version 1802, installez le correctif logiciel Azure Stack **1.0.180302.4**. Pour plus d’informations, consultez [KB 4131152: Existing Virtual Machine Scale Sets may become unusable](https://support.microsoft.com/help/4131152) (Les groupes de machines virtuelles identiques existants peuvent devenir inutilisables). 

- Azure Stack prend en charge l’utilisation de disques durs virtuels de type fixe uniquement. Certaines images proposées par le biais de la Place de Marché sur Azure Stack utilisent des disques durs virtuels dynamiques, mais elles ont été supprimées. Le redimensionnement d’une machine virtuelle à laquelle un disque dynamique est attaché laisse la machine virtuelle dans un état d’échec.

  Pour résoudre ce problème, supprimez la machine virtuelle sans supprimer son disque (un objet blob VHD dans un compte de stockage). Convertissez ensuite le disque dur virtuel de disque dynamique en disque fixe, puis recréez la machine virtuelle.

- Lorsque vous créez un groupe à haute disponibilité dans le portail en accédant à **Nouveau** > **Compute** > **Groupe à haute disponibilité**, vous pouvez uniquement créer un groupe à haute disponibilité avec un domaine d’erreur et un domaine de mise à jour de 1. Pour contourner ce problème, lors de la création d’une nouvelle machine virtuelle, créez le groupe à haute disponibilité à l’aide de PowerShell, CLI, ou depuis le portail.

- Quand vous créez des machines virtuelles sur le portail utilisateur Azure Stack, ce dernier affiche un nombre incorrect de disques de données que vous pouvez attacher à une machine virtuelle de série D. Toutes les machines virtuelles de série D prises en charge peuvent prendre en charge autant de disques de données que la configuration Azure.

- Lorsqu’une image de machine virtuelle ne peut pas être créée, un élément ayant échoué que vous ne pouvez pas supprimer peut être ajouté au panneau Compute des images de machine virtuelle.

  Pour contourner ce problème, créez une image de machine virtuelle avec un disque dur virtuel factice qui peut être créé via Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Ce processus doit résoudre le problème qui empêche la suppression de l’élément ayant échoué. Ensuite, 15 minutes après avoir créé l’image factice, vous pouvez correctement la supprimer.

  Vous pouvez alors essayer de retélécharger l’image de machine virtuelle ayant précédemment échoué.

-  Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

<!-- 1662991 --> 
- Les diagnostics de machine virtuelle Linux ne sont pas pris en charge dans Azure Stack. Lorsque vous déployez une machine virtuelle Linux en activant les diagnostics de machine virtuelle, le déploiement échoue. Le déploiement échoue également si vous activez les mesures de base de la machine virtuelle Linux dans les paramètres de diagnostic.  




#### <a name="networking"></a>Mise en réseau
- Une fois une machine virtuelle créée et associée à une adresse IP publique, vous ne pouvez pas dissocier cette machine virtuelle de cette adresse IP. La dissociation semble fonctionner, mais l’adresse IP publique qui a été assignée précédemment reste associée à la machine virtuelle d’origine.

  Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour les nouvelles machines virtuelles que vous créez.

  Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est communément appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle associée à l’origine et non à la nouvelle.

- L’équilibrage de charge interne gère incorrectement les adresses MAC des machines virtuelles principales, ce qui le bloque lorsqu’il utilise des instances Linux sur le réseau principal.  L’équilibrage de charge interne fonctionne correctement avec des instances Windows sur le réseau principal.

-   La fonctionnalité de transfert IP est visible dans le portail ; toutefois son activation n’a aucun effet. Cette fonctionnalité n’est pas encore prise en charge.

- Azure Stack prend en charge une seule *passerelle de réseau local* par adresse IP. Cela est vrai pour tous les abonnements de locataire. Après la création de la première connexion de passerelle de réseau local, les tentatives suivantes de création d’une ressource de passerelle de réseau local avec la même adresse IP sont bloquées.

- Sur un réseau virtuel a été créé avec un paramètre de serveur DNS défini sur *Automatique*, vous ne pouvez pas choisir un serveur DNS personnalisé. Les paramètres mis à jour ne sont pas envoyés (par push) aux machines virtuelles dans ce réseau virtuel.

- Azure Stack ne prend pas en charge l’ajout d’interfaces réseau supplémentaires sur une instance de machine virtuelle une fois que la machine virtuelle est déployée. Si la machine virtuelle nécessite plusieurs interfaces réseau, elles doivent être définies au moment du déploiement.

<!-- 2096388 --> 
- Vous ne pouvez pas utiliser le portail d’administration pour mettre à jour les règles d’un groupe de sécurité réseau. 

    Solution de contournement pour App Service : si vous avez besoin d’établir la connexion d’un bureau à distance à des instances de contrôleur, vous modifiez les règles de sécurité dans les groupes de sécurité réseau avec PowerShell.  Voici des exemples montrant comment *autoriser* la configuration, puis la restaurer sur *refuser* :  
    
    - *Autoriser :*
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn�t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
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

    - *Refuser :*

        ```powershell
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn�t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
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





#### <a name="sql-and-mysql"></a>SQL et MySQL
 - Avant de continuer, consultez la remarque importante dans [Avant de commencer](#before-you-begin) vers le début de ces notes de publication.
- Il faut parfois attendre une heure pour que les utilisateurs puissent créer des bases de données dans un nouveau déploiement SQL ou MySQL.

- Seul le fournisseur de ressources est pris en charge pour créer des éléments sur des serveurs qui hébergent SQL ou MySQL. Les éléments créés sur un serveur hôte qui ne sont pas créés par le fournisseur de ressources peuvent entraîner un état qui ne correspond pas.  

<!-- IS, ASDK --> 
- Les caractères spéciaux, notamment les espaces et les points, ne sont pas pris en charge dans les noms de **Famille** lors de la création d’une référence SKU pour les fournisseurs de ressources SQL et MySQL.

> [!NOTE]  
> Après la mise à jour vers Azure Stack 1802, vous pouvez continuer à utiliser les fournisseurs de ressources SQL et MySQL que vous avez déployés précédemment.  Nous vous recommandons de mettre à jour SQL et MySQL lorsqu’une nouvelle version est disponible. Comme Azure Stack, appliquez de façon séquentielle les mises à jour aux fournisseurs de ressources SQL et MySQL.  Par exemple, si vous utilisez la version 1710, commencez par appliquer la version 1711, puis la 1712 et ensuite mettez à jour vers la 1802.      
>   
> L’installation de la mise à jour 1802 n’affecte pas l’utilisation actuelle des fournisseurs de ressources SQL ou MySQL par vos utilisateurs.
> Quelle que soit la version des fournisseurs de ressources que vous utilisez, vos données utilisateur dans leurs bases de données restent intactes et accessibles.    


#### <a name="app-service"></a>App Service
- Les utilisateurs doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.

- Pour monter en puissance l’infrastructure (Workers, gestion, rôles frontaux), vous devez utiliser PowerShell comme décrit dans les notes de publication pour Compute.

<!-- #### Identity -->

#### <a name="downloading-azure-stack-tools-from-github"></a>Téléchargement des outils Azure Stack à partir de GitHub
- Quand vous utilisez l’applet de commande PowerShell *invoke-webrequest* pour télécharger les outils Azure Stack à partir de Github, une erreur s’affiche :     
    -  *invoke-webrequest : La demande a été abandonnée : Impossible de créer un canal sécurisé SSL/TLS.*     

  Cette erreur se produit en raison d’une désapprobation récente de la prise en charge GitHub des normes de chiffrement Tlsv1 et Tlsv1.1 (valeur par défaut pour PowerShell). Pour plus d’informations, consultez [Avis de suppression des normes de chiffrement faible](https://githubengineering.com/crypto-removal-notice/).

  Pour résoudre ce problème, ajoutez `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` au début du script pour forcer la console PowerShell à utiliser TLSv1.2 lors du téléchargement à partir de dépôts GitHub.


## <a name="download-the-update"></a>Télécharger la mise à jour
Vous pouvez télécharger le package de mise à jour Azure Stack 1802 à partir de [cet emplacement](https://aka.ms/azurestackupdatedownload).


## <a name="more-information"></a>Informations complémentaires
Microsoft fournit un moyen de surveiller et de reprendre les mises à jour à l’aide du point de terminaison privilégié (PEP) installé avec la mise à jour 1710.

- Consultez la documentation [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](/azure/azure-stack/azure-stack-monitor-update).

## <a name="see-also"></a>Voir aussi

- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](../azure-stack-updates.md).
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](../azure-stack-apply-updates.md).