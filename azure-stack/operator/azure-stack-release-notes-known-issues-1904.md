---
title: Problèmes connus dans Azure Stack 1904 | Microsoft Docs
description: En savoir plus sur les problèmes connus dans Azure Stack 1904.
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
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 9b92e6e2e059f4b57742248672751111b504136c
ms.sourcegitcommit: cf9440cd2c76cc6a45b89aeead7b02a681c4628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2019
ms.locfileid: "66469139"
---
# <a name="azure-stack-1904-known-issues"></a>Problèmes connus dans Azure Stack 1904

Cet article répertorie les problèmes connus dans la version 1904 d’Azure Stack. La liste est mise à jour lorsque de nouveaux problèmes sont identifiés.

> [!IMPORTANT]  
> Passez en revue cette section avant d’appliquer la mise à jour.

## <a name="update-process"></a>Processus de mise à jour

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Lorsque vous tentez d’installer une mise à jour Azure Stack, l’état de la mise à jour peut échouer et définir l’état sur **PreparationFailed**. Cela est dû au fait que le fournisseur de ressources de mise à jour est dans l’impossibilité de transférer correctement les fichiers du conteneur de stockage vers un partage d’infrastructure interne à des fins de traitement.
- Correction : À compter de la version 1901 (1.1901.0.95), vous pouvez contourner ce problème en cliquant sur **Mettre à jour maintenant** à nouveau (et pas sur **Reprendre**). Le fournisseur de ressources de mise à jour nettoie les fichiers de la tentative précédente, puis redémarre le téléchargement.
- Occurrence : Courant

## <a name="portal"></a>Portail

### <a name="administrative-subscriptions"></a>Abonnements d’administration

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les deux abonnements d’administration qui ont été introduits avec la version 1804 ne doivent pas être utilisés. Les types d’abonnements sont **Metering (Compteur)** et **Consumption (Consommation)** .
- Correction : Ces abonnements seront interrompus à partir de la version 1906 et ensuite supprimés. Si vous disposez de ressources s’exécutant sur ces deux abonnements, recréez-les dans des abonnements utilisateur avant la publication de la version 1906.
- Occurrence : Courant

### <a name="subscription-resources"></a>Ressources d’abonnement

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : La suppression d’abonnements utilisateur aboutit à des ressources orphelines.
- Correction : Commencez par supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.
- Occurrence : Courant

### <a name="subscription-permissions"></a>Autorisations d’abonnement

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack.
- Correction : Utilisez [PowerShell pour vérifier les autorisations](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Occurrence : Courant

### <a name="docker-extension"></a>Extension Docker

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans les portails d’administration et utilisateur, si vous recherchez **Docker**, l’élément est incorrectement retourné dans les résultats. Il n’est pas disponible dans Azure Stack. Si vous essayez de le créer, une erreur s’affiche.
- Correction : Aucune atténuation.
- Occurrence : Courant

### <a name="marketplace-management"></a>Gestion de la Place de marché

- Champ d’application : Il s’agit d’un problème qui concerne la version 1904.
- Cause : L’écran de gestion de la Place de marché n’est pas visible quand vous vous connectez au portail d’administration.
- Correction : Actualisez le navigateur.
- Occurrence : De façon intermittente

### <a name="marketplace-management"></a>Gestion de la Place de marché

- Champ d’application : Le problème s’applique à la version 1904.
- Cause : Quand vous filtrez les résultats dans le panneau **Ajouter à partir d’Azure** sous l’onglet Gestion de la Place de marché dans le portail d’administration, vous pouvez voir des résultats filtrés incorrects.
- Correction : triez les résultats par la colonne Nom pour les corriger.
- Occurrence : De façon intermittente

### <a name="marketplace-management"></a>Gestion de la Place de marché

- Champ d’application : Le problème s’applique à la version 1904.
- Cause : Quand vous filtrez les résultats dans la gestion de la Place de marché dans le portail de l’administrateur, des noms d’éditeurs apparaissent en double dans la liste déroulante des éditeurs. 
- Correction : Sélectionnez tous les doublons pour avoir la bonne liste de tous les produits de la Place de marché disponibles sous l’éditeur concerné.
- Occurrence : De façon intermittente

### <a name="docker-extension"></a>Extension Docker

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans les portails d’administration et utilisateur, si vous recherchez **Docker**, l’élément est incorrectement retourné dans les résultats. Il n’est pas disponible dans Azure Stack. Si vous essayez de le créer, une erreur s’affiche.
- Correction : Aucune atténuation.
- Occurrence : Courant

### <a name="upload-blob"></a>Charger l’objet blob

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, lorsque vous essayez de charger un blob à l’aide de l’option OAuth (préversion), la tâche échoue avec un message d’erreur.
- Correction : Chargez le blob à l’aide de l’option SAP.
- Occurrence : Courant

## <a name="networking"></a>Mise en réseau

### <a name="load-balancer"></a>Équilibrage de charge

#### <a name="add-backend-pool"></a>Ajouter le pool principal

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, si vous essayez d’ajouter un **pool principal** à un **équilibreur de charge**, l’opération échoue avec le message d’erreur **Failed to update Load Balancer (Impossible de mettre à jour l’équilibreur de charge)** .
- Correction : Utilisez PowerShell, CLI ou un modèle Azure Resource Manager pour associer le pool principal à une ressource d’équilibreur de charge.
- Occurrence : Courant

#### <a name="create-inbound-nat"></a>Créer des règles NAT de trafic entrant

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, si vous essayez de créer une **règle NAT de trafic entrant** pour un **équilibreur de charge**, l’opération échoue avec le message d’erreur **Failed to update Load Balancer (Impossible de mettre à jour l’équilibreur de charge)** .
- Correction : Utilisez PowerShell, CLI ou un modèle Azure Resource Manager pour associer le pool principal à une ressource d’équilibreur de charge.
- Occurrence : Courant

#### <a name="create-load-balancer"></a>Créer un équilibreur de charge

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, la fenêtre **Créer un équilibreur de charge** affiche une option pour créer une référence SKU d’équilibreur de charge **Standard**. Cette option n’est pas prise en charge dans Azure Stack.
- Correction : Utilisez les options d’équilibreur de charge De base à la place.
- Occurrence : Courant

#### <a name="public-ip-address"></a>Adresse IP publique

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, la fenêtre **Créer une adresse IP publique** affiche une option pour créer une référence SKU **Standard**. La référence SKU **Standard** n’est pas prise en charge dans Azure Stack.
- Correction : Utilisez la référence SKU **De base** à la place pour les adresses IP publiques.
- Occurrence : Courant

## <a name="compute"></a>Calcul

### <a name="vm-boot-diagnostics"></a>Diagnostics de démarrage de machine virtuelle

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Lors de la création d'une machine virtuelle Windows, l’erreur suivante peut s’afficher : **Échec du démarrage de la machine virtuelle « vm-name ». Error: Failed to update serial output settings for VM 'vm-name' (Impossible de mettre à jour les paramètres de sortie en série de la machine virtuelle « vm-name »)** .
Cette erreur se produit si vous activez les diagnostics de démarrage sur une machine virtuelle alors que vous avez supprimé votre compte de stockage des diagnostics de démarrage.
- Correction : Recréez le compte de stockage avec le même nom que celui utilisé précédemment.
- Occurrence : Courant

### <a name="virtual-machine-scale-set"></a>Jeu de mise à l’échelle de machine virtuelle

#### <a name="centos"></a>CentOS

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Quand vous créez un groupe de machines virtuelles identiques, l’option CentOS 7.2 est proposée pour le déploiement. CentOS 7.2 n’est pas disponible dans Azure Stack.
- Correction : Sélectionnez un autre système d’exploitation pour votre déploiement, ou choisissez un modèle Azure Resource Manager spécifiant une autre image CentOS qui a été téléchargée par l’opérateur avant le déploiement à partir de la Place de marché.
- Occurrence : Courant

#### <a name="remove-scale-set"></a>Supprimer un groupe identique

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Vous ne pouvez pas supprimer un groupe identique à partir du panneau **Virtual Machine Scale Sets**.
- Correction : Sélectionnez le groupe identique que vous souhaitez supprimer, puis cliquez sur le bouton **Supprimer** dans le volet **Vue d’ensemble**.
- Occurrence : Courant

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Créer des échecs au cours des correctifs et mises à jour sur des environnements Azure Stack à 4 nœuds

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : La création de machines virtuelles dans un groupe à haute disponibilité de 3 domaines d’erreur et la création d’une instance de groupe de machines virtuelles identiques échouent avec une erreur **FabricVmPlacementErrorUnsupportedFaultDomainSize** pendant le processus de mise à jour sur un environnement Azure Stack à 4 nœuds.
- Correction : Vous pouvez réussir à créer des machines virtuelles uniques dans un groupe à haute disponibilité comprenant 2 domaines d’erreur. En revanche, la création d’instances n’est toujours pas disponible pendant le processus de mise à jour sur un environnement Azure Stack à 4 nœuds.

### <a name="ubuntu-ssh-access"></a>Accès SSH Ubuntu

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Une machine virtuelle Ubuntu 18.04 créée avec une autorisation SSH activée ne vous permet pas d’utiliser les clés SSH pour vous connecter.
- Correction : Utilisez un accès à la machine virtuelle pour l’extension Linux afin d’implémenter des clés SSH après le provisionnement, ou utilisez une authentification par mot de passe.
- Occurrence : Courant

### <a name="compute-host-agent-alert"></a>Alerte de l’agent hôte de calcul

- Champ d’application : Il s’agit d’un problème qui concerne la version 1904.
- Cause : Un avertissement d’**agent hôte de calcul** s’affiche après le redémarrage d’un nœud dans l’unité d’échelle. Le redémarrage change le paramétrage par défaut du démarrage du service de l’agent hôte de calcul. Cette alerte ressemble à l’exemple suivant :

   ```shell
   NAME  
   Compute Host Agent is not responding to calls.
   SEVERITY  
   Warning
   STATE  
   Active
   CREATED TIME  
   5/16/2019, 10:08:23 AM
   UPDATED TIME  
   5/22/2019, 12:27:27 PM
   COMPONENT  
   M#####-NODE02
   DESCRIPTION  
   Could not communicate with the Compute Host Agent running on node: M#####-NODE02
   REMEDIATION  
   Please disable Compute Host Agent feature flag and collect logs for further diagnosis.
   ```

- Correction :
  - Cette alerte peut être ignorée. Le fait que l’agent ne réponde pas n’a pas d’impact sur l’opérateur et les opérations utilisateur ou les applications utilisateur. L’alerte réapparaît après 24 heures s’il est fermé manuellement.
  - Le problème est résolu dans le dernier [correctif logiciel Azure Stack pour la version 1904](https://support.microsoft.com/help/4505688).
- Occurrence : Courant

### <a name="virtual-machine-scale-set-instance-view"></a>Affichage des instances d’un groupe de machines virtuelles identiques

- Champ d’application : Ce problème s’applique aux versions 1904 et 1905.
- Cause : Le panneau d’affichage des instances de groupe de machines virtuelles identiques situé dans le portail Azure Stack, dans **Tableau de bord** > **Groupes de machines virtuelles identiques** > **AnyScaleSet - Instances** > **AnyScaleSetInstance** ne parvient pas à se charger.
- Correction : Il n’existe actuellement aucune solution à ce problème sur lequel nous continuons de travailler. En attendant, utilisez l’applet de commande CLI `az vmss get-instance-view` pour obtenir la vue des instances d’un groupe de machines virtuelles identiques.

## <a name="storage"></a>Stockage

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) n’est pas pris en charge dans Azure Stack, ce qui se traduit par la création d’un disque ayant l’ID **$null**. Cela vous empêche d’effectuer des opérations sur la machine virtuelle, comme le démarrage et l’arrêt. Le disque n’apparaît pas dans l’interface utilisateur, ni par le biais de l’API. À ce stade, la machine virtuelle ne peut pas être réparée et doit être supprimée.
- Correction : Pour convertir correctement vos disques, suivez le [guide de conversion de disques managés](../user/azure-stack-managed-disk-considerations.md#convert-to-managed-disks).

## <a name="app-service"></a>App Service

- Les locataires doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.
- Certaines expériences utilisateur du portail de locataire sont interrompues en raison d’une incompatibilité avec le framework du portail dans la version 1903, principalement l’expérience utilisateur pour les emplacements de déploiement, les tests en production et les extensions de site. Pour contourner ce problème, utilisez le [module PowerShell Azure App Service](/azure/app-service/deploy-staging-slots#automate-with-powershell) ou [Azure CLI](/cli/azure/webapp/deployment/slot?view=azure-cli-latest). L’expérience du portail sera restaurée en mettant à niveau votre déploiement d’[Azure App Service sur Azure Stack vers la version 1.6 (mise à jour 6)](azure-stack-app-service-release-notes-update-six.md).

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>Étapes suivantes

- [Passer en revue la check-list des activités de mise à jour](azure-stack-release-notes-checklist.md)
- [Consulter la liste des mises à jour de sécurité](azure-stack-release-notes-security-updates-1904.md)
