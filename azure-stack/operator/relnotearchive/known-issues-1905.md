---
title: Problèmes connus dans Azure Stack 1905 | Microsoft Docs
description: Apprenez-en davantage sur les problèmes connus dans Azure Stack 1905.
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
ms.date: 06/14/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/14/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1cd35416188cbd10827dd74fa6ba702d67c675ad
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248666"
---
# <a name="azure-stack-1905-known-issues"></a>Problèmes connus dans Azure Stack 1905

Cet article répertorie les problèmes connus dans la version 1905 d’Azure Stack. La liste est mise à jour lorsque de nouveaux problèmes sont identifiés.

> [!IMPORTANT]  
> Passez en revue cette section avant d’appliquer la mise à jour.

## <a name="update-process"></a>Processus de mise à jour

### <a name="host-node-update-prerequisite-failure"></a>Échec lié aux prérequis de mise à jour d’un nœud hôte

- Champ d’application : Ce problème concerne la mise à jour 1905.
- Cause : Quand vous tentez d’installer la mise à jour 1905 d’Azure Stack, l’état de la mise à jour peut échouer à cause des **prérequis de mise à jour d’un nœud hôte**. Cela est généralement dû au manque d’espace disque disponible sur un nœud hôte.
- Correction : Contactez le support technique Azure Stack pour savoir comment libérer de l’espace disque sur le nœud hôte.
- Occurrence : Rare

### <a name="preparation-failed"></a>Échec de la préparation

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Quand vous tentez d’installer la mise à jour 1905 d’Azure Stack, l’état de la mise à jour peut échouer et passer à **PreparationFailed**. Cela est dû au fait que le fournisseur de ressources de mise à jour est dans l’impossibilité de transférer correctement les fichiers du conteneur de stockage vers un partage d’infrastructure interne à des fins de traitement. Ce problème est davantage susceptible de se produire, car le package de mise à jour 1905 est plus volumineux que les packages de mise à jour précédents.
- Correction : À compter de la version 1901 (1.1901.0.95), vous pouvez contourner ce problème en cliquant sur **Mettre à jour maintenant** à nouveau (et pas sur **Reprendre**). Le fournisseur de ressources de mise à jour (URP) nettoie les fichiers de la tentative précédente, puis redémarre le téléchargement. Si le problème persiste, nous vous conseillons de charger manuellement le package de mise à jour comme cela est décrit dans la section [Importer et installer des mises à jour](../azure-stack-apply-updates.md).
- Occurrence : Courant

## <a name="portal"></a>Portail

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

- Champ d’application : Ce problème concerne les mises à jour 1904 et 1905
- Cause : L’écran de gestion de la Place de marché n’est pas visible quand vous vous connectez au portail d’administration.
- Correction : Actualisez le navigateur ou accédez à **Paramètres** et sélectionnez l’option **Rétablir les paramètres par défaut**.
- Occurrence : De façon intermittente

### <a name="docker-extension"></a>Extension Docker

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans les portails d’administration et utilisateur, si vous recherchez **Docker**, l’élément est incorrectement retourné dans les résultats. Il n’est pas disponible dans Azure Stack. Si vous essayez de le créer, une erreur s’affiche.
- Correction : Aucune atténuation.
- Occurrence : Courant

### <a name="upload-blob"></a>Charger l’objet blob

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail de l’utilisateur, lorsque vous essayez de charger un objet blob à l’aide de l’option **OAuth (préversion)** , la tâche échoue avec un message d’erreur.
- Correction : Chargez le blob à l’aide de l’option SAP.
- Occurrence : Courant

### <a name="template"></a>Modèle

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, l’interface utilisateur du déploiement de modèle ne remplit pas les paramètres pour les noms de modèle commençant par « _ » (caractère de soulignement).
- Correction : Supprimez le « _ » (caractère de soulignement) dans le nom du modèle.
- Occurrence : Courant

## <a name="networking"></a>Mise en réseau

### <a name="load-balancer"></a>Équilibrage de charge

#### <a name="add-backend-pool"></a>Ajouter le pool principal

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, si vous essayez d’ajouter un **pool back-end** à un **équilibreur de charge**, l’opération échoue avec un message d’erreur indiquant l’**échec de la mise à jour de l’équilibreur de charge**.
- Correction : Utilisez PowerShell, CLI ou un modèle Resource Manager pour associer le pool principal à une ressource d’équilibreur de charge.
- Occurrence : Courant

#### <a name="create-inbound-nat"></a>Créer des règles NAT de trafic entrant

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, si vous essayez de créer une **règle NAT de trafic entrant** pour un **équilibreur de charge**, l’opération échoue avec le message d’erreur **Failed to update Load Balancer (Impossible de mettre à jour l’équilibreur de charge)** .
- Correction : Utilisez PowerShell, CLI ou un modèle Resource Manager pour associer le pool principal à une ressource d’équilibreur de charge.
- Occurrence : Courant

#### <a name="create-load-balancer"></a>Créer un équilibreur de charge

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, la fenêtre **Créer un équilibreur de charge** affiche une option pour créer une référence SKU d’équilibreur de charge **Standard**. Cette option n’est pas prise en charge dans Azure Stack.
- Correction : Utilisez les options d’équilibreur de charge **De base** à la place.
- Occurrence : Courant

### <a name="public-ip-address"></a>Adresse IP publique

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, la fenêtre **Créer une adresse IP publique** affiche une option pour créer une référence SKU **Standard**. La référence SKU **Standard** n’est pas prise en charge dans Azure Stack.
- Correction : Utilisez la référence SKU **De base** pour l’adresse IP publique.
- Occurrence : Courant

## <a name="compute"></a>Calcul

### <a name="vm-boot-diagnostics"></a>Diagnostics de démarrage de machine virtuelle

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Quand vous créez une machine virtuelle Windows, l’erreur suivante peut s’afficher : **Échec du démarrage de la machine virtuelle « vm-name ». Erreur : Failed to update serial output settings for VM 'vm-name' (Impossible de mettre à jour les paramètres de sortie en série de la machine virtuelle « vm-name »)** .
Cette erreur se produit si vous activez les diagnostics de démarrage sur une machine virtuelle alors que vous avez supprimé votre compte de stockage des diagnostics de démarrage.
- Correction : Recréez le compte de stockage avec le même nom que celui utilisé précédemment.
- Occurrence : Courant

### <a name="vm-resize"></a>Redimensionnement de machine virtuelle

- Champ d’application : Ce problème concerne la version 1905.
- Cause : Impossible de redimensionner une machine virtuelle avec disque managé. La tentative de redimensionnement de la machine virtuelle génère une erreur avec le « code » : "InternalOperationError", "message" : « Une erreur interne s’est produite dans l’opération. »
- Correction : Nous nous efforçons de corriger ce problème dans la prochaine version. Actuellement, vous devez recréer la machine virtuelle avec la nouvelle taille de machine virtuelle.
- Occurrence : Courant

### <a name="virtual-machine-scale-set"></a>Jeu de mise à l’échelle de machine virtuelle

#### <a name="centos"></a>CentOS

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Quand vous créez un groupe identique de machines virtuelles, l’option CentOS 7.2 est proposée pour le déploiement. CentOS 7.2 n’étant pas disponible sur la Place de marché Azure Stack, cela provoque l’échec du déploiement, avec un message indiquant que l’image est introuvable.
- Correction : Sélectionnez un autre système d’exploitation pour votre déploiement, ou choisissez un modèle Azure Resource Manager spécifiant une autre image CentOS qui a été téléchargée par l’opérateur avant le déploiement à partir de la Place de marché.
- Occurrence : Courant

#### <a name="remove-scale-set"></a>Supprimer un groupe identique

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Vous ne pouvez pas supprimer un groupe identique à partir du panneau **Groupes de machines virtuelles identiques**.
- Correction : Sélectionnez le groupe identique que vous souhaitez supprimer, puis cliquez sur le bouton **Supprimer** dans le volet **Vue d’ensemble**.
- Occurrence : Courant

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Créer des échecs au cours des correctifs et mises à jour sur des environnements Azure Stack à 4 nœuds

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : La création de machines virtuelles dans un groupe à haute disponibilité de 3 domaines d’erreur et la création d’une instance de groupe identique de machines virtuelles échouent avec une erreur **FabricVmPlacementErrorUnsupportedFaultDomainSize** pendant le processus de mise à jour sur un environnement Azure Stack à 4 nœuds.
- Correction : Vous pouvez réussir à créer des machines virtuelles uniques dans un groupe à haute disponibilité comprenant 2 domaines d’erreur. En revanche, la création d’instances de groupe identique n’est toujours pas disponible pendant le processus de mise à jour sur un environnement Azure Stack à 4 nœuds.

#### <a name="scale-set-instance-view-blade-doesnt-load"></a>Le panneau de vue des instances de groupe identique ne charge pas

- Champ d’application : Ce problème concerne les versions 1904 et 1905.
- Cause : Le panneau de vue des instances d’un groupe de machines virtuelles identiques situé dans Portail Azure Stack > Tableau de bord -> Groupes de machines virtuelles identiques > AnyScaleSet - Instances > AnyScaleSetInstance ne parvient pas à se charger et affiche l’image d’un nuage qui pleure.
- Correction : Il n’existe actuellement aucune solution à ce problème sur lequel nous continuons de travailler. En attendant, utilisez la commande CLI `az vmss get-instance-view` pour obtenir la vue des instances d’un groupe identique.

### <a name="ubuntu-ssh-access"></a>Accès SSH Ubuntu

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Une machine virtuelle Ubuntu 18.04 créée avec une autorisation SSH activée ne vous permet pas d’utiliser les clés SSH pour vous connecter.
- Correction : Utilisez un accès à la machine virtuelle pour l’extension Linux afin d’implémenter des clés SSH après le provisionnement, ou utilisez une authentification par mot de passe.
- Occurrence : Courant

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>Étapes suivantes

- [Passer en revue la check-list des activités de mise à jour](../release-notes-checklist.md)
- [Consulter la liste des mises à jour de sécurité](../release-notes-security-updates.md)
