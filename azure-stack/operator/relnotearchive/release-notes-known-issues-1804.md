---
title: Mise à jour 1804 d’Azure Stack | Microsoft Docs
description: Découvrez le contenu de la mise à jour 1804 pour les systèmes intégrés Azure Stack, les problèmes connus et l’emplacement de téléchargement de la mise à jour.
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
ms.openlocfilehash: 5d871458a7af4e9233e750b07404bc151274033a
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248629"
---
# <a name="azure-stack-1804-update"></a>Mise à jour 1804 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit les améliorations et correctifs contenus dans le package de mise à jour 1804, les problèmes connus pour cette publication, ainsi que l’emplacement de téléchargement de la mise à jour. Les problèmes connus sont divisés en problèmes directement liés au processus de mise à jour et en problèmes propres à la build (après l’installation).

> [!IMPORTANT]        
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="build-reference"></a>Référence de build    
Le numéro de build de la mise à jour 1804 d’Azure Stack est **20180513.1**.   

### <a name="new-features"></a>Nouvelles fonctionnalités
Cette mise à jour inclut les améliorations suivantes pour Azure Stack.

<!-- 15028744 - IS -->  
- **Prise en charge par Visual Studio des déploiements Azure Stack déconnectés avec AD FS**. Visual Studio permet maintenant d’ajouter des abonnements et de s’authentifier avec des identifiants d’utilisateur fédéré AD FS. 
 
<!-- 1779474, 1779458 - IS --> 
- **Utilisation de machines virtuelles des séries Av2 et F**. Azure Stack peut maintenant utiliser des machines virtuelles de la taille des séries Av2 et F. Pour plus d’informations, voir [Tailles de machines virtuelles prises en charge dans Azure Stack](../../user/azure-stack-vm-sizes.md). 

<!-- 1759172 - IS, ASDK --> 
- **Nouveaux abonnements d’administration**. Avec 1804, deux nouveaux types d’abonnement sont disponibles dans le portail. Ces nouveaux types d’abonnement viennent s’ajouter à l’abonnement Fournisseur par défaut et sont visibles dans les nouvelles installations d’Azure Stack depuis la version 1804. *N’utilisez pas ces nouveaux types d’abonnement avec cette version d’Azure Stack*. Nous annoncerons quand ces types d’abonnement pourront être utilisés dans une mise à jour ultérieure. 

  Si vous mettez à jour Azure Stack vers la version 1804, les deux nouveaux types d’abonnement ne sont pas visibles. Toutefois, les nouveaux déploiements de systèmes intégrés Azure Stack et les installations du Kit de développement Azure Stack versions 1804 ou ultérieures ont accès aux trois types d’abonnement.  

  Ces nouveaux types d’abonnement s’inscrivent dans un changement plus large destiné à sécuriser l’abonnement Fournisseur par défaut et à faciliter le déploiement de ressources partagées telles que les serveurs d’hébergement SQL. À mesure que nous étofferons ce changement au travers de mises à jour d’Azure Stack, les ressources déployées sous ces nouveaux types d’abonnement pourront être perdues. 

  Les trois types d’abonnement maintenant visibles sont les suivants :  
  - Abonnement Fournisseur par défaut : continuez à utiliser ce type d’abonnement. 
  - Abonnement À l’usage : *n’utilisez pas ce type d’abonnement.*
  - Abonnement Consommation : *n’utilisez pas ce type d’abonnement*

  



## <a name="fixed-issues"></a>Problèmes résolus

<!-- IS, ASDK -->  
-   Dans le portail d’administration, vous ne devez plus actualiser la vignette Mise à jour pour afficher des informations.
 
<!-- 2050709  -->  
-   Vous pouvez maintenant utiliser le portail d’administration pour modifier les métriques de stockage pour le service Blob, le service de Table et le service de File d’attente.
 
<!-- IS, ASDK --> 
-  Sous **Mise en réseau**, lorsque vous cliquez sur **Connexion** pour configurer une connexion VPN, **Site à site (IPsec)** est maintenant la seule option disponible.

- **Divers correctifs** pour les performances, la stabilité, la sécurité et le système d’exploitation utilisé par Azure Stack.

## <a name="additional-releases-timed-with-this-update"></a>Autres versions synchronisées avec cette mise à jour  
Les versions suivantes sont maintenant disponibles, mais ne nécessitent pas la mise à jour 1804 d’Azure Stack.
- **Mise à jour avec le Pack d’analyse System Center Operations Manager de Microsoft Azure Stack**. Une nouvelle version (1.0.3.0) du Pack d’analyse System Center Operations Manager de Microsoft Azure Stack est disponible au [téléchargement](https://www.microsoft.com/download/details.aspx?id=55184). Avec cette version, vous pouvez utiliser des principaux de service quand vous ajoutez un déploiement Azure Stack connecté. Cette version propose également une expérience de gestion des mises à jour qui vous permet d’effectuer une action de correction directement à partir d’Operations Manager. Il existe aussi des nouveaux tableaux de bord qui affichent les fournisseurs de ressources, les unités d’échelle et les nœuds d’unités d’échelle.

- **Nouvelle version 1.3.0 d’Azure Stack PowerShell pour les administrateurs**.  Azure Stack PowerShell version 1.3.0 est désormais disponible pour l’installation. Cette version fournit des commandes pour tous les fournisseurs de ressources d’administrateur pour gérer Azure Stack.  Avec cette version, une partie du contenu sera dépréciée dans le [dépôt](https://github.com/Azure/AzureStack-Tools) GitHub d’outils Azure Stack. 

   Pour obtenir les détails de l’installation, suivez les [instructions](../azure-stack-powershell-install.md) ou le contenu d’[aide](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0&preserve-view=true) du Module Azure Stack 1.3.0. 

- **Informations de référence sur la version initiale de l’API REST Azure Stack**. Les [informations de référence sur l’API pour tous les fournisseurs de ressources d’administrateur Azure Stack](https://docs.microsoft.com/rest/api/azure-stack/) sont maintenant publiées. 


## <a name="before-you-begin"></a>Avant de commencer    

### <a name="prerequisites"></a>Prérequis
- Installez la mise à jour 1803 d’Azure Stack avant d’appliquer la mise à jour 1804.  
  
- Installez la dernière mise à jour ou le dernier correctif logiciel disponibles pour la version 1803. 


### <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour   
- Pendant l’installation de la mise à jour 1804, des alertes avec le titre *Erreur - Le modèle de FaultType UserAccounts.New est manquant* peuvent s’afficher.  Vous pouvez ignorer ces alertes de manière sécurisée. Elles se fermeront automatiquement une fois la mise à jour 1804 terminée.   
 
<!-- TBD - IS --> 
- Ne tentez pas de créer des machines virtuelles pendant l’installation de cette mise à jour. Pour plus d’informations sur la gestion des mises à jour, consultez [Manage updates in Azure Stack overview](../azure-stack-updates.md) (Vue d’ensemble de la gestion des mises à jour dans Azure Stack).


### <a name="post-update-steps"></a>Étapes après la mise à jour
Après l’installation de la version 1804, installez les correctifs logiciels applicables. Pour plus d’informations, consultez les articles suivants de la base de connaissances, ainsi que notre [stratégie de maintenance](../azure-stack-servicing-policy.md).  
 - [KB 4344114 – Correctif logiciel Azure Stack 1.0.180527.15](https://support.microsoft.com/help/4344114).




### <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)
Les éléments suivants sont des problèmes connus de post-installation pour la build **20180513.1**.

#### <a name="portal"></a>Portail

- La documentation technique Azure Stack s’applique à la dernière version. En raison des changements apportés au portail entre les versions, ce que vous voyez dans les portails Azure Stack peut différer du contenu de la documentation. 

<!-- TBD - IS ASDK --> 
- Vous ne pouvez pas appliquer les mises à jour de pilote à l’aide d’un package d’extension OEM avec cette version d’Azure Stack.  Il n’existe aucune solution de contournement pour ce problème.

<!-- 1272111 - IS --> 
-  Une fois que vous aurez installé cette version d’Azure Stack ou effectué une mise à jour vers cette version, vous ne pourrez peut-être pas voir les unités d’échelle Azure Stack sur le portail d’administration.  
  Solution de contournement : Utiliser PowerShell pour voir des informations sur les unités d’échelle. Pour plus d’informations, consultez le contenu [d’aide](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0&preserve-view=true) du Module Azure Stack 1.3.0. 

<!-- 2332636 - IS -->  
-   Lorsque vous utilisez AD FS pour votre système d’identité Azure Stack et effectuez la mise à jour vers cette version d’Azure Stack, le propriétaire par défaut de l’abonnement Fournisseur par défaut redevient l’utilisateur **CloudAdmin** intégré.  
  Solution de contournement : pour résoudre ce problème après avoir installé cette mise à jour, utilisez l’étape 3 de la procédure [Déclencher l’automation pour configurer un fournisseur de revendications de confiance dans Azure Stack](../azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub) afin de réinitialiser le propriétaire de l’abonnement Fournisseur par défaut.   

<!-- TBD - IS ASDK --> 
- Certains types d’abonnement d’administration ne sont pas disponibles.  Lorsque vous mettez à niveau Azure Stack vers cette version, les deux types d’abonnements qui ont été [introduits avec la version 1804](#new-features) ne sont pas visibles dans la console. Ceci est normal. Les types d’abonnement indisponibles sont *Abonnement de contrôle*, et *Abonnement de consommation*. Ces types d’abonnements sont visibles dans les nouveaux environnements Azure Stack depuis la version 1804, mais ils ne sont pas encore prêts à être utilisés. Vous devez continuer à utiliser le type d’abonnement *Fournisseur par défaut*.  


<!-- TBD -  IS ASDK -->
- Vous ne pouvez pas [effectuer une nouvelle demande de support dans la liste déroulante](../azure-stack-manage-portals.md#quick-access-to-help-and-support) à partir du portail administrateur. À la place, utilisez le lien suivant :     
    - Pour les systèmes Azure Stack intégrés, utilisez https://aka.ms/newsupportrequest.

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

<!-- TBD - IS ASDK --> 
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
 

#### <a name="compute"></a>Calcul
<!-- TBD - IS --> 
- Quand vous sélectionnez une taille de machine virtuelle pour un déploiement de machine virtuelle, certaines tailles de machine virtuelle de série F ne sont pas visibles dans le sélecteur de taille lorsque vous créez une machine virtuelle. Les tailles de machine virtuelle suivantes n’apparaissent pas dans le sélecteur : *F8s_v2*, *F16s_v2*, *F32s_v2* et *F64s_v2*.  
  Pour résoudre ce problème, utilisez l’une des méthodes suivantes pour déployer une machine virtuelle. Dans chaque méthode, vous devez spécifier la taille de machine virtuelle que vous voulez utiliser.

  - **Modèle Azure Resource Manager :** quand vous utilisez un modèle, définissez la valeur *vmSize* dans le modèle pour qu’elle soit égale à la taille de machine virtuelle souhaitée. Par exemple, le code suivant permet de déployer une machine virtuelle qui utilise la taille *F32s_v2* :  

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
- Quand vous créez des machines virtuelles sur le portail utilisateur Azure Stack, ce dernier affiche un nombre incorrect de disques de données que vous pouvez attacher à une machine virtuelle de série D. Toutes les machines virtuelles de série D prises en charge peuvent prendre en charge autant de disques de données que la configuration Azure.

<!-- TBD - IS ASDK --> 
- Lorsqu’une image de machine virtuelle ne peut pas être créée, un élément ayant échoué que vous ne pouvez pas supprimer peut être ajouté au panneau Compute des images de machine virtuelle.

  Pour contourner ce problème, créez une image de machine virtuelle avec un disque dur virtuel factice qui peut être créé via Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Ce processus doit résoudre le problème qui empêche la suppression de l’élément ayant échoué. Ensuite, 15 minutes après avoir créé l’image factice, vous pouvez correctement la supprimer.

  Vous pouvez alors essayer de retélécharger l’image de machine virtuelle ayant précédemment échoué.

<!-- TBD - IS ASDK --> 
- Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

<!-- 1662991 IS ASDK --> 
- Les diagnostics de machine virtuelle Linux ne sont pas pris en charge dans Azure Stack. Lorsque vous déployez une machine virtuelle Linux en activant les diagnostics de machine virtuelle, le déploiement échoue. Le déploiement échoue également si vous activez les mesures de base de la machine virtuelle Linux dans les paramètres de diagnostic.  


#### <a name="networking"></a>Mise en réseau
<!-- 1766332 - IS ASDK --> 
-  Sous **Mise en réseau**, si vous cliquez sur **Créer une passerelle VPN** pour configurer une connexion VPN, l’option **Basé sur des stratégies** s’affiche dans la liste des types de VPN. Ne sélectionnez pas cette option. Seule l’option **Basé sur itinéraires** est prise en charge dans Azure Stack.

<!-- 2388980 - IS ASDK --> 
- Une fois une machine virtuelle créée et associée à une adresse IP publique, vous ne pouvez pas dissocier cette machine virtuelle de cette adresse IP. La dissociation semble fonctionner, mais l’adresse IP publique qui a été assignée précédemment reste associée à la machine virtuelle d’origine.

  Actuellement, vous devez utiliser uniquement les nouvelles adresses IP publiques pour les nouvelles machines virtuelles que vous créez.

  Ce comportement se produit même si vous réaffectez l’adresse IP à une nouvelle machine virtuelle (ce qui est communément appelé un *échange d’adresses IP virtuelles*). Toutes les futures tentatives de connexion au moyen de cette adresse IP aboutissent à une connexion à la machine virtuelle associée à l’origine et non à la nouvelle.

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
        
        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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

<!-- TBD - IS --> 
- Seul le fournisseur de ressources est pris en charge pour créer des éléments sur des serveurs qui hébergent SQL ou MySQL. Les éléments créés sur un serveur hôte qui ne sont pas créés par le fournisseur de ressources peuvent entraîner un état qui ne correspond pas.  

<!-- IS, ASDK --> 
- Les caractères spéciaux, y compris les espaces et les points, ne sont pas pris en charge dans les noms **Famille** et **Niveau** lors de la création d’une référence SKU pour les fournisseurs de ressources SQL et MySQL.

<!-- TBD - IS -->
> [!NOTE]  
>  Une fois que vous aurez effectué la mise à jour vers Azure Stack 1804, vous pourrez continuer à utiliser les fournisseurs de ressources SQL et MySQL que vous avez déployés précédemment.  Nous vous recommandons de mettre à jour SQL et MySQL lorsqu’une nouvelle version est disponible. Comme Azure Stack, appliquez de façon séquentielle les mises à jour aux fournisseurs de ressources SQL et MySQL.  Par exemple, si vous utilisez la version 1802, commencez par appliquer la version 1803 avant de passer à la version 1804.      
>   
> L’installation de la mise à jour 1804 n’affecte pas l’utilisation actuelle des fournisseurs de ressources SQL ou MySQL par vos utilisateurs.
> Quelle que soit la version des fournisseurs de ressources que vous utilisez, vos données utilisateur dans leurs bases de données restent intactes et accessibles.    



#### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- Les utilisateurs doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.

<!-- TBD - IS ASDK --> 
- Pour monter en puissance l’infrastructure (Workers, gestion, rôles frontaux), vous devez utiliser PowerShell comme décrit dans les notes de publication pour Compute.

<!-- TBD - IS ASDK --> 
- App Service peut uniquement être déployé sur **l’abonnement Fournisseur par défaut** pour l’instant.  Dans une prochaine mise à jour, App Service se déploiera sur le nouvel abonnement À l’usage introduit dans Azure Stack 1804, et tous les déploiements existants seront également migrés vers ce nouvel abonnement.

#### <a name="usage"></a>Utilisation  
<!-- TBD - IS ASDK --> 
-  Les données d’utilisation des adresses IP publiques indiquent une même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>Télécharger la mise à jour
Vous pouvez télécharger le package de mise à jour 1804 d’Azure Stack à partir de [cet emplacement](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Voir aussi
- Pour utiliser le Point de terminaison privilégié (PEP) afin de surveiller et de reprendre les mises à jour, consultez [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](../azure-stack-monitor-update.md).
- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](../azure-stack-updates.md).
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](../azure-stack-apply-updates.md).