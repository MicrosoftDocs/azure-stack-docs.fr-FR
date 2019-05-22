---
title: Notes de publication Azure Stack - Problèmes connus dans la version 1904 | Microsoft Docs
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
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 782e0aaea0eadddae24795616aaba6053b728134
ms.sourcegitcommit: 39ba6d18781aed98b29ac5e08aac2d75c37bf18c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65387170"
---
# <a name="azure-stack-1904-known-issues"></a>Problèmes connus dans Azure Stack 1904

Cet article répertorie les problèmes connus dans la version 1904 d’Azure Stack. La liste est mise à jour lorsque de nouveaux problèmes sont identifiés.

> [!IMPORTANT]  
> Passez en revue cette section avant d’appliquer la mise à jour.

## <a name="portal"></a>Portail

### <a name="add-on-plans"></a>Plans d’extension

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les plans ajoutés à un abonnement utilisateur comme plan d’extension ne peuvent pas être supprimés, même quand vous supprimez le plan de l’abonnement utilisateur. Le plan est conservé jusqu’à ce que les abonnements qui référencent le plan d’extension soient aussi supprimés.
- Correction : Aucune atténuation.
- Occurrence : Courant

### <a name="administrative-subscriptions"></a>Abonnements d’administration

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les deux abonnements d’administration qui ont été introduits avec la version 1804 ne doivent pas être utilisés. Les types d’abonnements sont **Metering (Compteur)** et **Consumption (Consommation)**.
- Correction : Ces abonnements seront interrompus à partir de la version 1905 et ensuite supprimés. Si vous disposez de ressources s’exécutant sur ces deux abonnements, recréez-les dans des abonnements utilisateur avant la publication de la version 1905.
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

### <a name="marketplace-management"></a>Gestion de la Place de marché

- Champ d’application : Le problème s’applique à la version 1904.
- Cause : L’écran de gestion de la Place de marché n’est pas visible lorsque vous vous connectez au portail d’administration.
- Correction : Actualisez le navigateur.
- Occurrence : De façon intermittente

### <a name="upload-blob"></a>Charger l’objet blob

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, lorsque vous essayez de charger un blob à l’aide de l’option OAuth (préversion), la tâche échoue avec un message d’erreur.
- Correction : Chargez le blob à l’aide de l’option SAP.
- Occurrence : Courant

## <a name="networking"></a>Mise en réseau

### <a name="load-balancer"></a>Load Balancer

#### <a name="add-backend-pool"></a>Ajouter un pool principal

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, si vous essayez d’ajouter un **pool principal** à un **équilibreur de charge**, l’opération échoue avec le message d’erreur **Failed to update Load Balancer (Impossible de mettre à jour l’équilibreur de charge)**.
- Correction : Utilisez PowerShell, CLI ou un modèle Resource Manager pour associer le pool principal à une ressource d’équilibreur de charge.
- Occurrence : Courant

#### <a name="create-inbound-nat"></a>Créer des règles NAT de trafic entrant

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, si vous essayez de créer une **règle NAT de trafic entrant** pour un **équilibreur de charge**, l’opération échoue avec le message d’erreur **Failed to update Load Balancer (Impossible de mettre à jour l’équilibreur de charge)**.
- Correction : Utilisez PowerShell, CLI ou un modèle Resource Manager pour associer le pool principal à une ressource d’équilibreur de charge.
- Occurrence : Courant

#### <a name="create-load-balancer"></a>Créer un équilibreur de charge

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, la fenêtre **Créer un équilibreur de charge** affiche une option pour créer une référence SKU **Standard Load Balancer**. Cette option n’est pas prise en charge dans Azure Stack.
- Correction : Utilisez les options d’équilibreur de charge De base à la place.
- Occurrence : Courant

#### <a name="public-ip-address"></a>Adresse IP publique

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, la fenêtre **Créer une adresse IP publique** affiche une option pour créer une référence SKU **Standard**. La référence SKU **Standard** n’est pas prise en charge dans Azure Stack.
- Correction : Utilisez la référence SKU De base à la place pour l’adresse IP publique.
- Occurrence : Courant

## <a name="compute"></a>Calcul

### <a name="vm-boot-diagnostics"></a>Diagnostics de démarrage de machine virtuelle

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Lors de la création d'une machine virtuelle Windows, l’erreur suivante peut s’afficher : **Échec du démarrage de la machine virtuelle « vm-name ». Error: Failed to update serial output settings for VM 'vm-name' (Impossible de mettre à jour les paramètres de sortie en série de la machine virtuelle « vm-name »)**.
Cette erreur se produit si vous activez les diagnostics de démarrage sur une machine virtuelle alors que vous avez supprimé votre compte de stockage des diagnostics de démarrage.
- Correction : Recréez le compte de stockage avec le même nom que celui utilisé précédemment.
- Occurrence : Courant

### <a name="virtual-machine-scale-set"></a>Groupe de machines virtuelles identiques

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

### <a name="ubuntu-ssh-access"></a>Accès SSH Ubuntu

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Une machine virtuelle Ubuntu 18.04 créée avec une autorisation SSH activée ne vous permet pas d’utiliser les clés SSH pour vous connecter.
- Correction : Utilisez un accès à la machine virtuelle pour l’extension Linux afin d’implémenter des clés SSH après le provisionnement, ou utilisez une authentification par mot de passe.
- Occurrence : Courant

## <a name="infrastructure-backup"></a>Infrastructure Backup

<!--Bug 3615401 - scheduler config lost; new issue in YYMM;  hectorl-->
Après activation des sauvegardes automatiques, le service du planificateur passe à l’état désactivé de façon inattendue. Le service du contrôleur de sauvegarde détecte que les sauvegardes automatiques sont désactivées et déclenche un avertissement dans le portail administrateur. Cet avertissement est attendu quand les sauvegardes automatiques sont désactivées.

- Champ d’application : Il s’agit d’un problème qui concerne la version 1904.
- Cause : Ce problème est dû à un bogue dans le service qui entraîne la perte de la configuration du planificateur. Ce bogue ne modifie pas l’emplacement de stockage, le nom d’utilisateur, le mot de passe ou la clé de chiffrement.
- Correction : Pour résoudre ce problème, ouvrez le panneau des paramètres du contrôleur de sauvegarde dans le fournisseur de ressources Infrastructure Backup, puis sélectionnez **Activer les sauvegardes automatiques**. Veillez à définir la fréquence et la période de rétention souhaitées.
- Occurrence : Faible

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>Étapes suivantes

- [Passer en revue la check-list des activités de mise à jour](azure-stack-release-notes-checklist.md)
- [Consulter la liste des mises à jour de sécurité](azure-stack-release-notes-security-updates-1904.md)
