---
title: Mise à jour 1805 d’Azure Stack | Microsoft Docs
description: Découvrez les nouveautés de la mise à jour 1805 pour les systèmes intégrés Azure Stack, y compris les problèmes connus et l’emplacement de téléchargement de la mise à jour.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: sethm
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 16e11fcbc46065f512cc6b887b9c7159f6b0ac65
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248613"
---
# <a name="azure-stack-1805-update"></a>Mise à jour 1805 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit les améliorations et correctifs contenus dans le package de mise à jour 1805, les problèmes connus pour cette version, ainsi que l’emplacement de téléchargement de la mise à jour. Les problèmes connus sont divisés en problèmes directement liés au processus de mise à jour et en problèmes propres à la build (après l’installation).

> [!IMPORTANT]        
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="build-reference"></a>Référence de build    
Le numéro de build de la mise à jour 1805 d’Azure Stack est **1.1805.1.47**.  

> [!TIP]  
> En fonction des commentaires des clients, il existe une mise à jour du schéma de la version en cours d’utilisation pour Microsoft Azure Stack.  À compter de cette mise à jour, 1805, le nouveau schéma représente mieux la version cloud actuelle.  
> 
> Le schéma de version est à présent *Version.YearYearMonthMonth.MinorVersion.BuildNumber* où le deuxième et le troisième ensemble indiquent la version et la publication. Par exemple, 1805.1 représente la *version finalisée* de 1805.  


### <a name="new-features"></a>Nouvelles fonctionnalités
Cette mise à jour inclut les améliorations suivantes pour Azure Stack.
<!-- 2297790 - IS, ASDK --> 
- **Azure Stack inclut désormais un client *Syslog* en tant** que *fonctionnalité préliminaire*. Ce client permet le transfert des journaux d’activité d’audit et de sécurité liés à l’infrastructure d’Azure Stack vers un serveur Syslog ou un logiciel SIEM externes à Azure Stack. Actuellement, le client Syslog ne prend en charge que les connexions UDP non authentifiées via le port par défaut 514. La charge utile de chaque message Syslog est formatée en CEF (Common Event Format). 

  Pour configurer le client Syslog, utilisez la cmdlet **Set-SyslogServer** exposée dans le point de terminaison privilégié. 

  Avec cette préversion, vous pouvez voir les trois alertes suivantes. Présentées par Azure Stack, ces alertes incluent des *descriptions* et des conseils de *correction*. 
  - TITRE : Intégrité du code désactivée  
  - TITRE : Intégrité du code en mode audit 
  - TITRE : Compte d’utilisateur créé

  Tant que cette fonctionnalité est en préversion, vous ne devez pas l’utiliser dans votre environnement de production.   




### <a name="fixed-issues"></a>Problèmes résolus

<!-- # - applicability -->
- Nous avons résolu le problème qui bloquait l’[ouverture d’une nouvelle requête de support dans la liste déroulante](../azure-stack-manage-portals.md#quick-access-to-help-and-support) depuis le portail d’administration. Cette option fonctionne désormais normalement. 

- **Divers correctifs** pour les performances, la stabilité, la sécurité et le système d’exploitation utilisé par Azure Stack.


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Avant de commencer    

### <a name="prerequisites"></a>Prérequis
- Installez la mise à jour 1804 d’Azure Stack avant d’appliquer la mise à jour 1805 d’Azure Stack.  
- Installez la dernière mise à jour ou le dernier correctif logiciel disponibles pour la version 1804.   
- Avant de commencer l’installation de la mise à jour 1805, exécutez [Test-AzureStack](../azure-stack-diagnostic-test.md) pour valider l’état de votre Azure Stack et résoudre les éventuels problèmes opérationnels détectés. Examinez aussi les alertes actives et résolvez toutes celles qui nécessitent une intervention. 

### <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour   
- Pendant l’installation de la mise à jour 1805, des alertes avec le titre *Erreur - Le modèle de FaultType UserAccounts.New est manquant* peuvent s’afficher.  Vous pouvez ignorer ces alertes de manière sécurisée. Elles se fermeront automatiquement une fois la mise à jour 1805 terminée.   

<!-- 2489559 - IS --> 
- Ne tentez pas de créer des machines virtuelles pendant l’installation de cette mise à jour. Pour plus d’informations sur la gestion des mises à jour, consultez [Manage updates in Azure Stack overview](../azure-stack-updates.md) (Vue d’ensemble de la gestion des mises à jour dans Azure Stack).


### <a name="post-update-steps"></a>Étapes après la mise à jour
Après l’installation de la version 1805, installez les correctifs logiciels applicables. Pour plus d’informations, consultez les articles suivants de la base de connaissances, ainsi que notre [stratégie de maintenance](../azure-stack-servicing-policy.md).  
 - [KB 4344102 – Correctif logiciel Azure Stack 1.1805.7.57](https://support.microsoft.com/help/4344102).


## <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)
Les éléments suivants sont des problèmes connus qui apparaissent après l’installation de cette build.

### <a name="portal"></a>Portail  

- La documentation technique Azure Stack s’applique à la dernière version. En raison des changements apportés au portail entre les versions, ce que vous voyez dans les portails Azure Stack peut différer du contenu de la documentation. 

<!-- 2931230 IS  ASDK --> 
- Les plans ajoutés à un abonnement utilisateur comme plan d’extension ne peuvent pas être supprimés, même quand vous supprimez le plan de l’abonnement utilisateur. Le plan est conservé jusqu’à ce que les abonnements qui référencent le plan d’extension soient aussi supprimés. 

<!-- TBD - IS ASDK --> 
- Vous ne pouvez pas appliquer les mises à jour de pilote à l’aide d’un package d’extension OEM avec cette version d’Azure Stack.  Il n’existe aucune solution de contournement pour ce problème.

<!-- 2551834 - IS, ASDK --> 
-  Lorsque vous sélectionnez **Vue d’ensemble** pour un compte de stockage sur le portail d’administration ou utilisateur, les informations du volet *Éléments principaux* ne s’affichent pas.  Le volet Éléments principaux affiche des informations sur le compte comme son *groupe de ressources*, son *emplacement* et son *ID d’abonnement*.  D’autres options de la vue d’ensemble sont accessibles, par exemple *Services* et *Surveillance*, ainsi que des options permettant d’*Ouvrir dans Explorer* ou de *Supprimer le compte de stockage*. 

  Pour afficher les informations non disponibles, utilisez la cmdlet PowerShell [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0&preserve-view=true). 

<!-- 2551834 - IS, ASDK --> 
-  Lorsque vous sélectionnez **Balises** pour un compte de stockage sur le portail d’administration ou utilisateur, les informations ne se chargent pas.  

  Pour afficher les informations non disponibles, utilisez la cmdlet PowerShell [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0&preserve-view=true).


<!-- 2332636 - IS -->  
-   Lorsque vous utilisez AD FS pour votre système d’identité Azure Stack et effectuez la mise à jour vers cette version d’Azure Stack, le propriétaire par défaut de l’abonnement Fournisseur par défaut redevient l’utilisateur **CloudAdmin** intégré.  
  Solution de contournement : pour résoudre ce problème après avoir installé cette mise à jour, utilisez l’étape 3 de la procédure [Déclencher l’automation pour configurer un fournisseur de revendications de confiance dans Azure Stack](../azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub) afin de réinitialiser le propriétaire de l’abonnement Fournisseur par défaut.   

<!-- TBD - IS ASDK --> 
- Certains types d’abonnement d’administration ne sont pas disponibles. Lorsque vous mettez à niveau Azure Stack vers cette version, les deux types d’abonnements qui ont été introduits avec la version 1804 ne sont pas visibles dans la console. Ceci est normal. Les types d’abonnement indisponibles sont *Abonnement de contrôle*, et *Abonnement de consommation*. Ces types d’abonnements sont visibles dans les nouveaux environnements Azure Stack depuis la version 1804, mais ils ne sont pas encore prêts à être utilisés. Vous devez continuer à utiliser le type d’abonnement *Fournisseur par défaut*.  

<!-- 2403291 - IS ASDK --> 
- Il se peut que vous ne puissiez pas utiliser la barre de défilement horizontale au bas du portail d’administration et du portail utilisateur. Si vous ne pouvez pas accéder à la barre de défilement horizontale, utilisez les barres de navigation pour accéder à un panneau précédent dans le portail, en sélectionnant le nom du panneau que vous souhaitez afficher dans la liste des barres de navigation en haut à gauche du portail.

<!-- TBD - IS --> 
- Il se peut qu’il ne soit pas possible d’afficher les ressources de calcul ou de stockage sur le portail d’administration. Ce problème est dû au fait qu’une erreur s’est produite pendant l’installation de la mise à jour, et que celle-ci a été, à tort, signalée comme réussie. Si ce problème se produit, contactez les services de support technique Microsoft pour obtenir de l’aide.

<!-- TBD - IS --> 
- Il se peut qu’un tableau de bord vide s’affiche sur le portail. Pour récupérer le tableau de bord, sélectionnez l’icône d’engrenage dans l’angle supérieur droit du portail, puis choisissez **Restaurer les paramètres par défaut**.

<!-- TBD - IS ASDK --> 
- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.

<!-- TBD - IS ASDK --> 
- Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack. Pour résoudre ce problème, utilisez PowerShell pour vérifier les autorisations.


### <a name="health-and-monitoring"></a>Intégrité et surveillance
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

  Les deux alertes 1 et 2 peuvent être ignorées en toute sécurité. Elles se ferment automatiquement au bout d’un moment. 

  Vous pouvez également voir l’alerte suivante pour *Capacité*. Pour cette alerte, le pourcentage de mémoire disponible identifiée dans la description peut varier :  

  Alerte 3 :
   - NOM : Capacité de mémoire faible
   - GRAVITÉ : Critique
   - COMPOSANT : Capacité
   - DESCRIPTION : La région a consommé plus de 80 % de la mémoire disponible. La création de machines virtuelles avec de grandes quantités de mémoire peut échouer.  

  Dans cette version d’Azure Stack, cette alerte peut se déclencher de façon incorrecte. Si les machines virtuelles de locataire continuent le déploiement avec succès, vous pouvez ignorer cette alerte en toute sécurité. 
  
  L’alerte 3 n’est pas automatiquement fermée. Si vous fermez cette alerte, Azure Stack crée la même alerte dans les 15 minutes qui suivent.  

<!-- 2368581 - IS. ASDK --> 
-  En tant qu’opérateur d’Azure Stack, si vous recevez une alerte d’insuffisance de mémoire et que les machines virtuelles de locataire ne parviennent pas à se déployer à cause d’une *erreur de création de la structure de la machine virtuelle*, il est possible que l’horodatage d’Azure Stack soit à court de mémoire disponible. Utilisez le [Capacity Planner Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) pour mieux comprendre la capacité disponible pour vos charges de travail. 


### <a name="compute"></a>Calcul
<!-- TBD - IS, ASDK --> 
- Quand vous sélectionnez une taille de machine virtuelle pour un déploiement de machine virtuelle, certaines tailles de machine virtuelle de série F ne sont pas visibles dans le sélecteur de taille lorsque vous créez une machine virtuelle. Les tailles de machine virtuelle suivantes n’apparaissent pas dans le sélecteur : *F8s_v2*, *F16s_v2*, *F32s_v2* et *F64s_v2*.  
  Pour résoudre ce problème, utilisez l’une des méthodes suivantes pour déployer une machine virtuelle. Dans chaque méthode, vous devez spécifier la taille de machine virtuelle que vous voulez utiliser.

  - **Modèle Azure Resource Manager :** lorsque vous utilisez un modèle, définissez la valeur *vmSize* dans le modèle pour qu’elle soit égale à la taille de machine virtuelle que vous voulez utiliser. Par exemple, l’entrée suivante permet de déployer une machine virtuelle qui utilise la taille *F32s_v2* :  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI :** Vous pouvez utiliser la commande [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create&preserve-view=true) et spécifiez la taille de machine virtuelle comme paramètre, identique à `--size "Standard_F32s_v2"`.

  - **PowerShell :** avec PowerShell, vous pouvez utiliser [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0&preserve-view=true) avec le paramètre qui spécifie la taille de machine virtuelle, identique à `-VMSize "Standard_F32s_v2"`.


<!-- TBD - IS ASDK --> 
- Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

<!-- TBD - IS --> 
- Lorsque vous créez un groupe à haute disponibilité dans le portail en accédant à **Nouveau** > **Compute** > **Groupe à haute disponibilité**, vous pouvez uniquement créer un groupe à haute disponibilité avec un domaine d’erreur et un domaine de mise à jour de 1. Pour contourner ce problème, lors de la création d’une nouvelle machine virtuelle, créez le groupe à haute disponibilité à l’aide de PowerShell, CLI, ou depuis le portail.

<!-- TBD - IS ASDK --> 
- Lorsque vous créez des machines virtuelles sur le portail utilisateur Azure Stack, le portail affiche un nombre incorrect de disques de données que vous pouvez attacher à une machine virtuelle de la série DS. Les machines virtuelles de série DS peuvent prendre en charge autant de disques de données que la configuration Azure.

<!-- TBD - IS ASDK --> 
- Lorsqu’une image de machine virtuelle ne peut pas être créée, un élément ayant échoué que vous ne pouvez pas supprimer peut être ajouté au panneau Compute des images de machine virtuelle.

  Pour contourner ce problème, créez une image de machine virtuelle avec un disque dur virtuel factice qui peut être créé via Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Ce processus doit résoudre le problème qui empêche la suppression de l’élément ayant échoué. Ensuite, 15 minutes après avoir créé l’image factice, vous pouvez correctement la supprimer.

  Vous pouvez alors essayer de retélécharger l’image de machine virtuelle ayant précédemment échoué.

<!-- TBD - IS ASDK --> 
- Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

<!-- 1662991 IS ASDK --> 
- Les diagnostics de machine virtuelle Linux ne sont pas pris en charge dans Azure Stack. Lorsque vous déployez une machine virtuelle Linux en activant les diagnostics de machine virtuelle, le déploiement échoue. Le déploiement échoue également si vous activez les mesures de base de la machine virtuelle Linux dans les paramètres de diagnostic.  


### <a name="networking"></a>Mise en réseau
<!-- TBD - IS ASDK --> 
- Vous ne pouvez pas créer d’itinéraires définis par l’utilisateur, que ce soit sur le portail d’administration ou le portail utilisateur. Pour résoudre ce problème, utilisez [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

<!-- 1766332 - IS ASDK --> 
-  Sous **Mise en réseau**, si vous cliquez sur **Créer une passerelle VPN** pour configurer une connexion VPN, l’option **Basé sur des stratégies** s’affiche dans la liste des types de VPN. Ne sélectionnez pas cette option. Seule l’option **Basé sur itinéraires** est prise en charge dans Azure Stack.

<!-- 2388980 - IS ASDK --> 
- Une fois une machine virtuelle créée et associée à une adresse IP publique, vous ne pouvez pas dissocier cette machine virtuelle de cette adresse IP. La dissociation semble fonctionner, mais l’adresse IP publique qui a été assignée précédemment reste associée à la machine virtuelle d’origine.

  Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour les nouvelles machines virtuelles que vous créez.

  Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est communément appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle originelle et non à la nouvelle.

<!-- 2292271 - IS ASDK --> 
- Si vous définissez une limite de quota pour une ressource réseau qui fait partie d’une offre et d’un plan associés à un abonnement de locataire, la nouvelle limite n’est pas appliquée à cet abonnement. Toutefois, la nouvelle limite s’applique aux nouveaux abonnements créés après l’augmentation du quota.

  Pour contourner ce problème, utilisez un plan d’extension pour augmenter un quota réseau quand le plan est déjà associé à un abonnement. Pour plus d’informations, découvrez comment [rendre un plan d’extension disponible](../azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

<!-- 2304134 IS ASDK --> 
- Vous ne pouvez pas supprimer un abonnement auquel sont associées des ressources de la zone DNS ou de la table de routage. Pour supprimer l’abonnement, vous devez d’abord supprimer les ressources de la zone DNS ou de la table de routage de l’abonnement de locataire.


<!-- 1902460 - IS ASDK --> 
- Azure Stack prend en charge une seule *passerelle de réseau local* par adresse IP. Cela est vrai pour tous les abonnements de locataire. Après la création de la première connexion de passerelle de réseau local, les tentatives suivantes de création d’une ressource de passerelle de réseau local avec la même adresse IP sont bloquées.

<!-- 16309153 - IS ASDK --> 
- Sur un réseau virtuel a été créé avec un paramètre de serveur DNS défini sur *Automatique*, vous ne pouvez pas choisir un serveur DNS personnalisé. Les paramètres mis à jour ne sont pas envoyés (par push) aux machines virtuelles dans ce réseau virtuel.

<!-- TBD - IS ASDK --> 
- Azure Stack ne prend pas en charge l’ajout d’interfaces réseau supplémentaires sur une instance de machine virtuelle une fois que la machine virtuelle est déployée. Si la machine virtuelle nécessite plusieurs interfaces réseau, elles doivent être définies au moment du déploiement.

<!-- 2096388 IS --> 
- Vous ne pouvez pas utiliser le portail d’administration pour mettre à jour les règles d’un groupe de sécurité réseau.

    Solution de contournement pour App Service : si vous avez besoin d’établir la connexion d’un bureau à distance à des instances de contrôleur, vous modifiez les règles de sécurité dans les groupes de sécurité réseau avec PowerShell.  Voici des exemples montrant comment *autoriser* la configuration, puis la restaurer sur *refuser* :  

    - *Autoriser :*

      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      ##This doesn't work. Need to set properties again even in case of edit

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

        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        ##This doesn't work. Need to set properties again even in case of edit

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


### <a name="sql-and-mysql"></a>SQL et MySQL

<!-- TBD - IS --> 
- Seul le fournisseur de ressources est pris en charge pour créer des éléments sur des serveurs qui hébergent SQL ou MySQL. Les éléments créés sur un serveur hôte qui ne sont pas créés par le fournisseur de ressources peuvent entraîner un état qui ne correspond pas.  

<!-- IS, ASDK --> 
- Les caractères spéciaux, y compris les espaces et les points, ne sont pas pris en charge dans les noms **Famille** et **Niveau** lors de la création d’une référence SKU pour les fournisseurs de ressources SQL et MySQL.

<!-- TBD - IS --> 
> [!NOTE]  
> Après la mise à jour vers Azure Stack 1805, vous pouvez continuer à utiliser les fournisseurs de ressources SQL et MySQL que vous avez déployés précédemment.  Nous vous recommandons de mettre à jour SQL et MySQL lorsqu’une nouvelle version est disponible. Comme Azure Stack, appliquez de façon séquentielle les mises à jour aux fournisseurs de ressources SQL et MySQL. Par exemple, si vous utilisez la version 1803, commencez par appliquer la version 1804, avant de mettre à jour vers la 1805.      
>   
> L’installation de la mise à jour 1805 n’affecte pas l’utilisation actuelle des fournisseurs de ressources SQL ou MySQL par vos utilisateurs.
> Quelle que soit la version des fournisseurs de ressources que vous utilisez, vos données utilisateur dans leurs bases de données restent intactes et accessibles.    



### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- Les utilisateurs doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.

<!-- 2489178 - IS ASDK --> 
- Pour monter en puissance l’infrastructure (Workers, gestion, rôles frontaux), vous devez utiliser PowerShell comme décrit dans les notes de publication pour Compute.

<!-- TBD - IS ASDK --> 
- Le déploiement d’App Service n’est possible à l’heure actuelle que sur l’*abonnement du fournisseur par défaut*. 


### <a name="usage"></a>Utilisation  
<!-- TBD - IS ASDK --> 
-  Les données d’utilisation des adresses IP publiques indiquent une même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Télécharger la mise à jour
Vous pouvez télécharger le package de mise à jour 1805 d’Azure Stack à partir de [cet emplacement](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Voir aussi
- Pour utiliser le Point de terminaison privilégié (PEP) afin de surveiller et de reprendre les mises à jour, consultez [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](../azure-stack-monitor-update.md).
- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](../azure-stack-updates.md).
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](../azure-stack-apply-updates.md).