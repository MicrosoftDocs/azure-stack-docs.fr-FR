---
title: Problèmes connus dans Azure Stack 1907
description: Découvrez les problèmes connus d’Azure Stack 1907.
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
ms.date: 10/29/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 435f761633b3513be39cd5912cbcee697003e952
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248654"
---
# <a name="azure-stack-1907-known-issues"></a>Problèmes connus dans Azure Stack 1907

Cet article liste les problèmes connus dans les versions d’Azure Stack. La liste est mise à jour lorsque de nouveaux problèmes sont identifiés.

Pour accéder aux problèmes connus d'une autre version, utilisez le menu déroulant de sélection de la version, situé au-dessus de la table des matières à gauche.

> [!IMPORTANT]  
> Passez en revue cette section avant d’appliquer la mise à jour.

> [!IMPORTANT]  
> Si votre instance Azure Stack est en retard de plus de deux mises à jour, elle est considérée comme non conforme. Pour bénéficier de la prise en charge, vous devez [mettre à jour avec au moins la version minimale prise en charge](../azure-stack-servicing-policy.md#keep-your-system-under-support).

## <a name="1907-update-process"></a>Processus de mise à jour 1907

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Quand vous tentez d’installer la mise à jour 1907 d’Azure Stack, l’état de la mise à jour peut échouer et passer à **PreparationFailed**. Cela est dû au fait que le fournisseur de ressources de mise à jour est dans l’impossibilité de transférer correctement les fichiers du conteneur de stockage vers un partage d’infrastructure interne à des fins de traitement.
- Correction : À compter de la version 1901 (1.1901.0.95), vous pouvez contourner ce problème en cliquant sur **Mettre à jour maintenant** à nouveau (et pas sur **Reprendre**). Le fournisseur de ressources de mise à jour (URP) nettoie les fichiers de la tentative précédente, puis redémarre le téléchargement. Si le problème persiste, nous vous conseillons de charger manuellement le package de mise à jour comme cela est décrit dans la section [Importer et installer des mises à jour](../azure-stack-apply-updates.md).
- Occurrence : Courant

## <a name="portal"></a>Portail

### <a name="administrative-subscriptions"></a>Abonnements d’administration

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les deux abonnements d’administration qui ont été introduits avec la version 1804 ne doivent pas être utilisés. Les types d’abonnements sont **Metering (Compteur)** et **Consumption (Consommation)** .
- Correction : Si vous disposez de ressources s’exécutant sur ces deux abonnements, recréez-les dans des abonnements utilisateur.
- Occurrence : Courant

### <a name="subscriptions-properties-blade"></a>Panneau Propriétés des abonnements

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail administrateur, le panneau **Propriétés** des abonnements ne se charge pas correctement
- Correction : Vous pouvez afficher les propriétés de ces abonnements dans le volet **Essentials** du panneau **Vue d’ensemble des abonnements**.
- Occurrence : Courant

### <a name="subscription-permissions"></a>Autorisations d’abonnement

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack.
- Correction : Utilisez [PowerShell pour vérifier les autorisations](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Occurrence : Courant

### <a name="storage-account-settings"></a>Paramètres du compte de stockage

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, le panneau **Configuration** du compte de stockage présente une option permettant de modifier le **type de transfert de sécurité**. Cette fonctionnalité n’est actuellement pas prise en charge dans Azure Stack.
- Occurrence : Courant

### <a name="upload-blob"></a>Charger l’objet blob

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail de l’utilisateur, lorsque vous essayez de charger un objet blob à l’aide de l’option **OAuth (préversion)** , la tâche échoue avec un message d’erreur.
- Correction : Chargez le blob à l’aide de l’option SAP.
- Occurrence : Courant

## <a name="networking"></a>Mise en réseau

### <a name="service-endpoints"></a>Points de terminaison de service

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, le panneau **Réseau virtuel** comporte une option pour utiliser les **points de terminaison du service**. Cette fonctionnalité n’est actuellement pas prise en charge dans Azure Stack.
- Occurrence : Courant

### <a name="network-interface"></a>interface réseau

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Une nouvelle interface réseau ne peut pas être ajoutée à une machine virtuelle qui se trouve dans l’état **en cours d’exécution**.
- Correction : Arrêtez la machine virtuelle avant d’ajouter ou de supprimer une interface réseau.
- Occurrence : Courant

### <a name="virtual-network-gateway"></a>Passerelle de réseau virtuel

#### <a name="alerts"></a>Alertes

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, le panneau **Passerelle de réseau virtuel** comporte une option pour utiliser les **alertes**. Cette fonctionnalité n’est actuellement pas prise en charge dans Azure Stack.
- Occurrence : Courant

#### <a name="active-active"></a>Actif/actif

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, lors de la création, et dans le menu **Passerelle de réseau virtuel**, vous verrez une option permettant d’activer la configuration **Actif/actif**. Cette fonctionnalité n’est actuellement pas prise en charge dans Azure Stack.
- Occurrence : Courant

#### <a name="vpn-troubleshooter"></a>Résolution des problèmes de VPN

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, le panneau **Connexions** montre une fonctionnalité appelée **Résolution des problèmes de VPN**. Cette fonctionnalité n’est actuellement pas prise en charge dans Azure Stack.
- Occurrence : Courant

### <a name="network-connection-type"></a>Type de connexion réseau

- Champ d’application : Ce problème s’applique aux environnements 1906 et 1907. 
- Cause : Dans le portail administrateur, le panneau **AddConnection** affiche une option permettant d’utiliser **VNet-to-VNet**. Cette fonctionnalité n’est actuellement pas prise en charge dans Azure Stack. 
- Occurrence : Courant 

#### <a name="documentation"></a>Documentation

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les liens vers la documentation dans la page de vue d’ensemble de la Passerelle de réseau virtuel renvoient vers la documentation spécifique à Azure au lieu d’Azure Stack. Utilisez les liens suivants pour accéder à la documentation Azure Stack :

  - [SKU de passerelle](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Connexions hautement disponibles](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configurer le protocole BGP sur Azure Stack](../../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuits ExpressRoute](../azure-stack-connect-expressroute.md)
  - [Spécifier des stratégies IPsec/IKE personnalisées](../../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Calcul

### <a name="vm-boot-diagnostics"></a>Diagnostics de démarrage de machine virtuelle

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Quand vous créez une machine virtuelle Windows, l’erreur suivante peut s’afficher : **Échec du démarrage de la machine virtuelle « vm-name ». Erreur : Failed to update serial output settings for VM 'vm-name' (Impossible de mettre à jour les paramètres de sortie en série de la machine virtuelle « vm-name »)** . Cette erreur se produit si vous activez les diagnostics de démarrage sur une machine virtuelle alors que vous avez supprimé votre compte de stockage des diagnostics de démarrage.
- Correction : Recréez le compte de stockage avec le même nom que celui utilisé précédemment.
- Occurrence : Courant

### <a name="virtual-machine-scale-set"></a>Jeu de mise à l’échelle de machine virtuelle

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Créer des échecs au cours des correctifs et mises à jour sur des environnements Azure Stack à 4 nœuds

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : La création de machines virtuelles dans un groupe à haute disponibilité de 3 domaines d’erreur et la création d’une instance de groupe identique de machines virtuelles échouent avec une erreur **FabricVmPlacementErrorUnsupportedFaultDomainSize** pendant le processus de mise à jour sur un environnement Azure Stack à 4 nœuds.
- Correction : Vous pouvez réussir à créer des machines virtuelles uniques dans un groupe à haute disponibilité comprenant 2 domaines d’erreur. En revanche, la création d’instances de groupe identique n’est toujours pas disponible pendant le processus de mise à jour sur un environnement Azure Stack à 4 nœuds.

### <a name="ubuntu-ssh-access"></a>Accès SSH Ubuntu

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Une machine virtuelle Ubuntu 18.04 créée avec une autorisation SSH activée ne vous permet pas d’utiliser les clés SSH pour vous connecter.
- Correction : Utilisez un accès à la machine virtuelle pour l’extension Linux afin d’implémenter des clés SSH après le provisionnement, ou utilisez une authentification par mot de passe.
- Occurrence : Courant

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>La réinitialisation du mot de passe d’un groupe de machines virtuelles identiques ne fonctionne pas

- Champ d’application : Ce problème s’applique aux versions 1906 et 1907.
- Cause : Un nouveau panneau de réinitialisation du mot de passe s’affiche dans l’interface utilisateur de groupe identique, mais Azure Stack ne prend pas encore en charge la réinitialisation de mot de passe sur un groupe identique.
- Correction : Aucun.
- Occurrence : Courant

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Nuage qui pleure sur le diagnostic de groupe identique

- Champ d’application : Ce problème s’applique aux versions 1906 et 1907.
- Cause : La page de la vue d’ensemble du groupe de machines virtuelles identiques contient un graphique vide. En cliquant sur le graphique vide, un panneau avec un nuage qui pleure. C’est le graphique des informations de diagnostic du groupe identique, comme le pourcentage UC, et ce n’est pas une fonctionnalité prise en charge dans la version Azure Stack actuelle.
- Correction : Aucun.
- Occurrence : Courant

### <a name="virtual-machine-diagnostic-settings-blade"></a>Panneau des paramètres de diagnostic de machine virtuelle

- Champ d’application : Ce problème s’applique aux versions 1906 et 1907.    
- Cause : Le panneau des paramètres de diagnostic de machine virtuelle comporte un onglet **Récepteur**, qui demande un **compte Application Insights**. C’est le résultat d’un nouveau panneau et ce n’est pas encore pris en charge dans Azure Stack.
- Correction : Aucun.
- Occurrence : Courant
