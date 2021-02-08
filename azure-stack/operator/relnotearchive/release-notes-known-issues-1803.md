---
title: Mise à jour 1803 d’Azure Stack | Microsoft Docs
description: Découvrez le contenu de la mise à jour 1803 pour les systèmes intégrés Azure Stack, les problèmes connus et l’emplacement à partir duquel la télécharger.
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
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 6f15453ce20988929728a9d326ab5f7bf901beca
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248631"
---
# <a name="azure-stack-1803-update"></a>Mise à jour 1803 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit les améliorations et correctifs contenus dans cette mise à jour 1803, les problèmes connus dans cette publication, et l’emplacement de téléchargement de la mise à jour. Les problèmes connus sont divisés en problèmes directement liés au processus de mise à jour et en problèmes propres à la build (après l’installation).

> [!IMPORTANT]        
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="build-reference"></a>Référence de build    
Le numéro de build de mise à jour d’Azure Stack 1803 est **20180329.1**.


## <a name="before-you-begin"></a>Avant de commencer    
> [!IMPORTANT]    
> Ne tentez pas de créer des machines virtuelles pendant l’installation de cette mise à jour. Pour plus d’informations sur la gestion des mises à jour, consultez [Manage updates in Azure Stack overview](../azure-stack-updates.md) (Vue d’ensemble de la gestion des mises à jour dans Azure Stack).


### <a name="prerequisites"></a>Prérequis
- Installez la mise à jour 1802 d’Azure Stack avant d’appliquer la mise à jour 1803.   

- Installez le **correctif logiciel AzS 1.0.180312.1 - Build 20180222.2** avant d’appliquer la mise à jour 1803 d’Azure Stack. Ce correctif logiciel met à jour Windows Defender et est disponible lorsque vous téléchargez des mises à jour pour Azure Stack.

  Pour installer le correctif logiciel, suivez les procédures normales pour [l’installation des mises à jour d’Azure Stack](../azure-stack-apply-updates.md). La mise à jour s’appelle **Correctif logiciel AzS 1.0.180312.1** et comprend les fichiers suivants : 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1.bin
    - Metadata.xml

  Après avoir chargé ces fichiers dans un compte de stockage et un conteneur, exécutez la programmation d’installation à partir de la vignette Mise à jour du portail d’administration. 
  
  Contrairement aux mises à jour d’Azure Stack, l’installation de cette mise à jour ne modifie pas la version d’Azure Stack. Pour confirmer l’installation de cette mise à jour, affichez la liste des **mises à jour installées**.



### <a name="new-features"></a>Nouvelles fonctionnalités 
Cette mise à jour inclut les améliorations et les correctifs suivants pour Azure Stack.

- **Mettre à jour les clés secrètes d’Azure Stack** : comptes et certificats. Pour plus d’informations sur la gestion des clés secrètes, consultez [Faire pivoter les clés secrètes dans Azure Stack](../azure-stack-rotate-secrets.md). 

<!-- 1914853 --> 
- **Redirection automatique vers HTTPS** si le protocole HTTP est utilisé pour accéder au portail d’administration ou au portail utilisateur. Cette amélioration a été effectuée à partir de commentaires [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) pour Azure Stack. 

<!-- 2202621  --> 
- **Accès à la Place de marché** : il est maintenant possible d’ouvrir la Place de marché Azure Stack avec l’option [+ Nouveau](https://ms.portal.azure.com/#create/hub) sur le portail d’administration et le portail utilisateur, de la même façon que sur les portails Azure.
 
<!-- 2202621 --> 
- **Azure Monitor** : Azure Stack ajoute [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) au portail d’administration et au portail utilisateur. Il comprend de nouveaux explorateurs de mesures et de journaux d’activité. Le port **13012** doit être ouvert dans les configurations de pare-feu pour qu’Azure Monitor soit accessible à partir de réseaux externes. Pour plus d’informations sur les ports requis par Azure Stack, voir [Intégration de centre de données Azure Stack – Publier des points de terminaison](../azure-stack-integrate-endpoints.md).

   Également dans le cadre de cette modification, sous **Plus de services**, *Journaux d’audits* est désormais remplacé par *Journaux d’activités*. La fonctionnalité est désormais compatible avec le portail Azure. 

<!-- 1664791 --> 
- **Fichiers partiellement alloués** : toute image ajoutée à Azure Stack ou par le biais de la syndication Marketplace est convertie en fichier partiellement alloué. Cette conversion n’est pas possible pour celles qui ont été ajoutées avant la version 1803 d’Azure Stack. Il faut alors utiliser la syndication Marketplace pour renvoyer ces images de façon à tirer parti de cette fonctionnalité. 
 
   Les fichiers partiellement alloués sont un format de fichier efficace utilisé pour réduire l’utilisation de l’espace de stockage et améliorer les E/S. Pour plus d’informations, consultez [Fsutil sparse](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) pour Windows Server. 

### <a name="fixed-issues"></a>Problèmes résolus

<!-- 1739988 --> 
-  À présent, l’équilibrage de charge interne gère correctement les adresses MAC des machines virtuelles principales, ce qui le conduit à déposer des paquets sur le réseau principal lorsque des instances Linux y sont utilisées. L’équilibrage de charge interne fonctionne bien avec des instances Windows sur le réseau principal. 

<!-- 1805496 --> 
-  Les connexions VPN entre Azure Stack étaient interrompues à cause du fait que Azure Stack utilisait des paramètres différents de ceux d’Azure pour la stratégie IKE. Les valeurs de SALifetime (heure) et de SALiftetime (octets) n’étaient pas compatibles avec Azure ; elles ont été modifiées dans la version 1803 pour correspondre aux paramètres Azure. La valeur de SALifetime (secondes) avant la version 1803 était de 14 400 ; elle devient 27 000 dans la version 1803. La valeur de SALifetime (octets) avant la version 1803 était de 819 200 ; elle devient 33 553 408 dans la version 1803.

<!-- 2209262 --> 
-  Les connexions VPN étaient visibles sur le portail ; l’activation du transfert d’adresses IP n’a cependant aucun effet. La fonctionnalité est activée par défaut ; il n’est pas encore possible de modifier ce comportement.  Ce contrôle a été supprimé du portail. 

<!-- 1766332 --> 
-  Azure Stack ne prend pas en charge les passerelles VPN basées sur des stratégies, même si l’option apparaît sur le portail.  Cette option a été supprimée du portail. 

<!-- 1868283 --> 
-  Azure Stack empêche maintenant le redimensionnement d’une machine virtuelle créée avec des disques dynamiques. 

<!-- 1756324 --> 
-  Les données d’utilisation des machines virtuelles sont désormais triées toutes les heures. Ce comportement est cohérent avec Azure. 

<!--  2253274 --> 
-  Sur le portail d’administration et le portail utilisateur, le chargement du panneau Paramètres des sous-réseaux virtuels échoue. Pour contourner le problème, utilisez PowerShell et la cmdlet [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0&preserve-view=true) afin de voir et de gérer ces informations.

- Lorsque vous créez une machine virtuelle, le message *Impossible d’afficher les tarifs* ne s’affiche plus lors du choix d’une taille pour la machine virtuelle.

- Divers correctifs pour les performances, la stabilité, la sécurité et le système d’exploitation utilisé par Azure Stack.


### <a name="changes"></a>Modifications
- La façon de changer l’état d’une offre nouvellement créée de *privé* à *public* ou à *désactivé* a été modifiée. Pour plus d’informations, consultez [Créer une offre](../azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour    
<!-- 2328416 -->  Pendant l’installation de la mise à jour 1803, un temps d’arrêt risque de se produire pour le service BLOB et les services internes qui l’utilisent. Certaines opérations de machine virtuelle sont concernées. Ce temps d’arrêt peut provoquer des échecs d’opérations de locataire ou l’émission d’alertes par des services qui ne parviennent pas à accéder aux données. Le problème se résout de lui-même une fois la mise à jour installée. 



### <a name="post-update-steps"></a>Étapes après la mise à jour
- Après l’installation de la version 1803, installez les correctifs logiciels applicables. Pour plus d’informations, consultez les articles suivants de la base de connaissances, ainsi que notre [stratégie de maintenance](../azure-stack-servicing-policy.md).

  - [KB 4344115 – Correctif logiciel Azure Stack 1.0.180427.15](https://support.microsoft.com/help/4344115).

- Après avoir installé cette mise à jour, vérifiez la configuration du pare-feu pour être sûr que les [ports nécessaires](../azure-stack-integrate-endpoints.md) sont ouverts. Par exemple, cette mise à jour comprend *Azure Monitor*, qui remplace les journaux d’audit par des journaux d’activité. Dans le cadre de cette modification, le port 13012 est désormais utilisé et doit également être ouvert.  


### <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)
Les éléments suivants sont des problèmes connus depuis l’installation du build **20180323.2**.

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

- Dans le portail d’administration, vous pouvez voir une alerte critique pour le composant *Microsoft.Update.Admin*. Le nom de l’alerte, la description et la correction s’affichent tous comme suit :  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (ERREUR - Absence du modèle de FaultType ResourceProviderTimeout.)

  Cette alerte peut être ignorée en toute sécurité. 


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
- Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

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



- Azure Stack prend en charge une seule *passerelle de réseau local* par adresse IP. Cela est vrai pour tous les abonnements de locataire. Après la création de la première connexion de passerelle de réseau local, les tentatives suivantes de création d’une ressource de passerelle de réseau local avec la même adresse IP sont bloquées.

- Sur un réseau virtuel a été créé avec un paramètre de serveur DNS défini sur *Automatique*, vous ne pouvez pas choisir un serveur DNS personnalisé. Les paramètres mis à jour ne sont pas envoyés (par push) aux machines virtuelles dans ce réseau virtuel.

- Azure Stack ne prend pas en charge l’ajout d’interfaces réseau supplémentaires sur une instance de machine virtuelle une fois que la machine virtuelle est déployée. Si la machine virtuelle nécessite plusieurs interfaces réseau, elles doivent être définies au moment du déploiement.

<!-- 2096388 --> 
- Vous ne pouvez pas utiliser le portail d’administration pour mettre à jour les règles d’un groupe de sécurité réseau. 

    Solution de contournement pour App Service : si vous avez besoin d’établir la connexion d’un bureau à distance à des instances de contrôleur, vous modifiez les règles de sécurité dans les groupes de sécurité réseau avec PowerShell.  Voici des exemples montrant comment *autoriser* la configuration, puis la restaurer sur *refuser* :  
    
    - *Autoriser :*
 
      ```powershell    
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Add-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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
> Après la mise à jour vers Azure Stack 1803, vous pouvez continuer à utiliser les fournisseurs de ressources SQL et MySQL que vous avez déployés précédemment.  Nous vous recommandons de mettre à jour SQL et MySQL lorsqu’une nouvelle version est disponible. Comme Azure Stack, appliquez de façon séquentielle les mises à jour aux fournisseurs de ressources SQL et MySQL.  Par exemple, si vous utilisez la version 1711, commencez par appliquer la version 1712, puis la 1802, avant de mettre à jour vers la 1803.      
>   
> L’installation de la mise à jour 1803 n’affecte pas l’utilisation actuelle des fournisseurs de ressources SQL ou MySQL par vos utilisateurs.
> Quelle que soit la version des fournisseurs de ressources que vous utilisez, vos données utilisateur dans leurs bases de données restent intactes et accessibles.    



#### <a name="app-service"></a>App Service
- Les utilisateurs doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.

- Pour monter en puissance l’infrastructure (Workers, gestion, rôles frontaux), vous devez utiliser PowerShell comme décrit dans les notes de publication pour Compute.


#### <a name="usage"></a>Utilisation  
-  Les données d’utilisation des adresses IP publiques indiquent une même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.

<!-- #### Identity -->

#### <a name="downloading-azure-stack-tools-from-github"></a>Téléchargement des outils Azure Stack à partir de GitHub
- Quand vous utilisez l’applet de commande PowerShell *invoke-webrequest* pour télécharger les outils Azure Stack à partir de Github, une erreur s’affiche :     
    -  *invoke-webrequest : La demande a été abandonnée : Impossible de créer un canal sécurisé SSL/TLS.*     

  Cette erreur se produit en raison d’une désapprobation récente de la prise en charge GitHub des normes de chiffrement Tlsv1 et Tlsv1.1 (valeur par défaut pour PowerShell). Pour plus d’informations, consultez [Avis de suppression des normes de chiffrement faible](https://githubengineering.com/crypto-removal-notice/).

  Pour résoudre ce problème, ajoutez `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` au début du script pour forcer la console PowerShell à utiliser TLSv1.2 lors du téléchargement à partir de dépôts GitHub.


## <a name="download-the-update"></a>Télécharger la mise à jour
Vous pouvez télécharger le package de mise à jour Azure Stack 1803 à partir de [cet emplacement](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Voir aussi
- Pour utiliser le Point de terminaison privilégié (PEP) afin de surveiller et de reprendre les mises à jour, consultez [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](../azure-stack-monitor-update.md).
- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](../azure-stack-updates.md).
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](../azure-stack-apply-updates.md).