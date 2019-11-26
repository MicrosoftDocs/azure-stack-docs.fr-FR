---
title: Problèmes connus d’Azure Stack
description: Apprenez-en davantage sur les problèmes connus dans les différentes versions d’Azure Stack.
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
ms.date: 09/17/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 09/13/2019
ms.openlocfilehash: 367a2cb5a07c0db2b272b8ffc951f51e5ed9cc0e
ms.sourcegitcommit: bb2bbfad8061f7677954f6ce5a435b4e6f9299b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74100038"
---
# <a name="azure-stack-known-issues"></a>Problèmes connus d’Azure Stack

Cet article liste les problèmes connus dans les versions d’Azure Stack. La liste est mise à jour lorsque de nouveaux problèmes sont identifiés.

Pour accéder aux problèmes connus d'une autre version, utilisez le menu déroulant de sélection de la version, situé au-dessus de la table des matières à gauche.

::: moniker range=">=azs-1905"
> [!IMPORTANT]  
> Passez en revue cette section avant d’appliquer la mise à jour.
::: moniker-end
::: moniker range="<azs-1905"
> [!IMPORTANT]  
> Si votre instance Azure Stack est en retard de plus de deux mises à jour, elle est considérée comme non conforme. Pour bénéficier de la prise en charge, vous devez [mettre à jour avec au moins la version minimale prise en charge](azure-stack-servicing-policy.md#keep-your-system-under-support). 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-1908"
## <a name="1908-update-process"></a>Processus de mise à jour 1908

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : L’installation de la mise à jour d’Azure Stack peut échouer, et son état peut passer à **PreparationFailed**. Cela est dû au fait que le fournisseur de ressources de mise à jour est dans l’impossibilité de transférer correctement les fichiers du conteneur de stockage vers un partage d’infrastructure interne à des fins de traitement.
- Correction : À compter de la version 1901 (1.1901.0.95), vous pouvez contourner ce problème en cliquant sur **Mettre à jour maintenant** à nouveau (et pas sur **Reprendre**). Le fournisseur de ressources de mise à jour (URP) nettoie les fichiers de la tentative précédente, puis redémarre le téléchargement. Si le problème persiste, nous vous conseillons de charger manuellement le package de mise à jour comme cela est décrit dans la [section Installer des mises à jour](azure-stack-apply-updates.md#install-updates-and-monitor-progress).
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

### <a name="subscriptions-lock-blade"></a>Panneau de verrouillage des abonnements

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail administrateur, le panneau de **verrouillage** des abonnements utilisateur comporte deux boutons avec un intitulé **abonnement**.
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

### <a name="network-security-groups"></a>Network Security Group
- Champ d’application : Ce problème s’applique à toutes les versions prises en charge. 
- Cause : Il n’est pas autorisé de créer une règle **DenyAllOutbound** explicite dans un groupe de sécurité réseau. En effet, elle empêcherait toute communication interne avec l’infrastructure, et cette communication est nécessaire au bon déploiement de la machine virtuelle.
- Occurrence : Courant

### <a name="service-endpoints"></a>Points de terminaison de service

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, le panneau **Réseau virtuel** comporte une option pour utiliser les **points de terminaison du service**. Cette fonctionnalité n’est actuellement pas prise en charge dans Azure Stack.
- Occurrence : Courant

### <a name="network-interface"></a>interface réseau

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Une nouvelle interface réseau ne peut pas être ajoutée à une machine virtuelle qui se trouve dans l’état **en cours d’exécution**.
- Correction : Arrêtez la machine virtuelle avant d’ajouter ou de supprimer une interface réseau.
- Occurrence : Courant

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : La carte réseau principale d’une machine virtuelle ne peut pas être changée. La suppression/le détachement de la carte réseau principale entraînerait des problèmes de démarrage de la machine virtuelle.
- Occurrence : Courant

### <a name="virtual-network-gateway"></a>Passerelle de réseau virtuel

#### <a name="local-network-gateway-deletion"></a>Suppression du nom de passerelle de réseau local

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, la suppression de la **passerelle réseau local** affiche le message d’erreur suivant : **Impossible de supprimer une passerelle de réseau local avec une connexion active**, même si aucune connexion n’est active.
- Atténuation : Le correctif pour ce problème sera publié dans la version 1907. Une solution de contournement pour ce problème consiste à créer une passerelle de réseau local avec la même adresse IP, le même espace d’adressage et les mêmes détails de configuration, mais avec un autre nom. L’ancien LNG peut être supprimé une fois que l’environnement a été mis à jour vers la version 1907.
- Occurrence : Courant

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

#### <a name="documentation"></a>Documentation

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les liens vers la documentation dans la page de vue d’ensemble de la Passerelle de réseau virtuel renvoient vers la documentation spécifique à Azure au lieu d’Azure Stack. Utilisez les liens suivants pour accéder à la documentation Azure Stack :

  - [SKU de passerelle](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Connexions hautement disponibles](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configurer le protocole BGP sur Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuits ExpressRoute](azure-stack-connect-expressroute.md)
  - [Spécifier des stratégies IPsec/IKE personnalisées](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Calcul

### <a name="vm-boot-diagnostics"></a>Diagnostics de démarrage de machine virtuelle

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Quand vous créez une machine virtuelle Windows, l’erreur suivante peut s’afficher : **Échec du démarrage de la machine virtuelle « vm-name ». Error: Failed to update serial output settings for VM 'vm-name' (Impossible de mettre à jour les paramètres de sortie en série de la machine virtuelle « vm-name »)** . Cette erreur se produit si vous activez les diagnostics de démarrage sur une machine virtuelle alors que vous avez supprimé votre compte de stockage des diagnostics de démarrage.
- Correction : Recréez le compte de stockage avec le même nom que celui utilisé précédemment.
- Occurrence : Courant

### <a name="virtual-machine-scale-set"></a>Jeu de mise à l’échelle de machine virtuelle

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Créer des échecs au cours des correctifs et mises à jour sur des environnements Azure Stack à 4 nœuds

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : La création de machines virtuelles dans un groupe à haute disponibilité de 3 domaines d’erreur et la création d’une instance de groupe de machines virtuelles identiques échouent avec une erreur **FabricVmPlacementErrorUnsupportedFaultDomainSize** pendant le processus de mise à jour sur un environnement Azure Stack à 4 nœuds.
- Correction : Vous pouvez réussir à créer des machines virtuelles uniques dans un groupe à haute disponibilité comprenant 2 domaines d’erreur. En revanche, la création d’instances n’est toujours pas disponible pendant le processus de mise à jour sur un environnement Azure Stack à 4 nœuds.

### <a name="ubuntu-ssh-access"></a>Accès SSH Ubuntu

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Une machine virtuelle Ubuntu 18.04 créée avec une autorisation SSH activée ne vous permet pas d’utiliser les clés SSH pour vous connecter.
- Correction : Utilisez un accès à la machine virtuelle pour l’extension Linux afin d’implémenter des clés SSH après le provisionnement, ou utilisez une authentification par mot de passe.
- Occurrence : Courant

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>La réinitialisation du mot de passe d’un groupe de machines virtuelles identiques ne fonctionne pas

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Un nouveau panneau de réinitialisation du mot de passe s’affiche dans l’interface utilisateur de groupe identique, mais Azure Stack ne prend pas encore en charge la réinitialisation de mot de passe sur un groupe identique.
- Correction : Aucune.
- Occurrence : Courant

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Nuage qui pleure sur le diagnostic de groupe identique

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : La page de la vue d’ensemble du groupe de machines virtuelles identiques contient un graphique vide. En cliquant sur le graphique vide, un panneau avec un nuage qui pleure. C’est le graphique des informations de diagnostic du groupe identique, comme le pourcentage UC, et ce n’est pas une fonctionnalité prise en charge dans la version Azure Stack actuelle.
- Correction : Aucune.
- Occurrence : Courant

### <a name="virtual-machine-diagnostic-settings-blade"></a>Panneau des paramètres de diagnostic de machine virtuelle

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.    
- Cause : Le panneau des paramètres de diagnostic de machine virtuelle comporte un onglet **Récepteur**, qui demande un **compte Application Insights**. C’est le résultat d’un nouveau panneau et ce n’est pas encore pris en charge dans Azure Stack.
- Correction : Aucune.
- Occurrence : Courant

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-update-process"></a>Processus de mise à jour 1907

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Quand vous tentez d’installer la mise à jour 1907 d’Azure Stack, l’état de la mise à jour peut échouer et passer à **PreparationFailed**. Cela est dû au fait que le fournisseur de ressources de mise à jour est dans l’impossibilité de transférer correctement les fichiers du conteneur de stockage vers un partage d’infrastructure interne à des fins de traitement.
- Correction : À compter de la version 1901 (1.1901.0.95), vous pouvez contourner ce problème en cliquant sur **Mettre à jour maintenant** à nouveau (et pas sur **Reprendre**). Le fournisseur de ressources de mise à jour (URP) nettoie les fichiers de la tentative précédente, puis redémarre le téléchargement. Si le problème persiste, nous vous conseillons de charger manuellement le package de mise à jour comme cela est décrit dans la section [Importer et installer des mises à jour](azure-stack-apply-updates.md).
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

  - [SKU de passerelle](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Connexions hautement disponibles](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configurer le protocole BGP sur Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuits ExpressRoute](azure-stack-connect-expressroute.md)
  - [Spécifier des stratégies IPsec/IKE personnalisées](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Calcul

### <a name="vm-boot-diagnostics"></a>Diagnostics de démarrage de machine virtuelle

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Quand vous créez une machine virtuelle Windows, l’erreur suivante peut s’afficher : **Échec du démarrage de la machine virtuelle « vm-name ». Error: Failed to update serial output settings for VM 'vm-name' (Impossible de mettre à jour les paramètres de sortie en série de la machine virtuelle « vm-name »)** . Cette erreur se produit si vous activez les diagnostics de démarrage sur une machine virtuelle alors que vous avez supprimé votre compte de stockage des diagnostics de démarrage.
- Correction : Recréez le compte de stockage avec le même nom que celui utilisé précédemment.
- Occurrence : Courant

### <a name="virtual-machine-scale-set"></a>Jeu de mise à l’échelle de machine virtuelle

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Créer des échecs au cours des correctifs et mises à jour sur des environnements Azure Stack à 4 nœuds

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : La création de machines virtuelles dans un groupe à haute disponibilité de 3 domaines d’erreur et la création d’une instance de groupe de machines virtuelles identiques échouent avec une erreur **FabricVmPlacementErrorUnsupportedFaultDomainSize** pendant le processus de mise à jour sur un environnement Azure Stack à 4 nœuds.
- Correction : Vous pouvez réussir à créer des machines virtuelles uniques dans un groupe à haute disponibilité comprenant 2 domaines d’erreur. En revanche, la création d’instances n’est toujours pas disponible pendant le processus de mise à jour sur un environnement Azure Stack à 4 nœuds.

### <a name="ubuntu-ssh-access"></a>Accès SSH Ubuntu

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Une machine virtuelle Ubuntu 18.04 créée avec une autorisation SSH activée ne vous permet pas d’utiliser les clés SSH pour vous connecter.
- Correction : Utilisez un accès à la machine virtuelle pour l’extension Linux afin d’implémenter des clés SSH après le provisionnement, ou utilisez une authentification par mot de passe.
- Occurrence : Courant

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>La réinitialisation du mot de passe d’un groupe de machines virtuelles identiques ne fonctionne pas

- Champ d’application : Ce problème s’applique aux versions 1906 et 1907.
- Cause : Un nouveau panneau de réinitialisation du mot de passe s’affiche dans l’interface utilisateur de groupe identique, mais Azure Stack ne prend pas encore en charge la réinitialisation de mot de passe sur un groupe identique.
- Correction : Aucune.
- Occurrence : Courant

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Nuage qui pleure sur le diagnostic de groupe identique

- Champ d’application : Ce problème s’applique aux versions 1906 et 1907.
- Cause : La page de la vue d’ensemble du groupe de machines virtuelles identiques contient un graphique vide. En cliquant sur le graphique vide, un panneau avec un nuage qui pleure. C’est le graphique des informations de diagnostic du groupe identique, comme le pourcentage UC, et ce n’est pas une fonctionnalité prise en charge dans la version Azure Stack actuelle.
- Correction : Aucune.
- Occurrence : Courant

### <a name="virtual-machine-diagnostic-settings-blade"></a>Panneau des paramètres de diagnostic de machine virtuelle

- Champ d’application : Ce problème s’applique aux versions 1906 et 1907.    
- Cause : Le panneau des paramètres de diagnostic de machine virtuelle comporte un onglet **Récepteur**, qui demande un **compte Application Insights**. C’est le résultat d’un nouveau panneau et ce n’est pas encore pris en charge dans Azure Stack.
- Correction : Aucune.
- Occurrence : Courant

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-update-process"></a>Processus de mise à jour 1906

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Quand vous tentez d’installer la mise à jour 1906 d’Azure Stack, l’état de la mise à jour peut échouer et passer à **PreparationFailed**. Cela est dû au fait que le fournisseur de ressources de mise à jour est dans l’impossibilité de transférer correctement les fichiers du conteneur de stockage vers un partage d’infrastructure interne à des fins de traitement. 
- Correction : À compter de la version 1901 (1.1901.0.95), vous pouvez contourner ce problème en cliquant sur **Mettre à jour maintenant** à nouveau (et pas sur **Reprendre**). Le fournisseur de ressources de mise à jour (URP) nettoie les fichiers de la tentative précédente, puis redémarre le téléchargement. Si le problème persiste, nous vous conseillons de charger manuellement le package de mise à jour comme cela est décrit dans la section [Importer et installer des mises à jour](azure-stack-apply-updates.md).
- Occurrence : Courant

## <a name="portal"></a>Portail

### <a name="administrative-subscriptions"></a>Abonnements d’administration

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les deux abonnements d’administration qui ont été introduits avec la version 1804 ne doivent pas être utilisés. Les types d’abonnements sont **Metering (Compteur)** et **Consumption (Consommation)** .
- Correction : Si vous disposez de ressources s’exécutant sur ces deux abonnements, recréez-les dans des abonnements utilisateur.
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

### <a name="subscriptions-properties-blade"></a>Panneau Propriétés des abonnements
- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail administrateur, le panneau **Propriétés** des abonnements ne se charge pas correctement
- Correction : Vous pouvez afficher les propriétés de ces abonnements dans le volet Essentials du panneau Vue d’ensemble des abonnements.
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

### <a name="update"></a>Mettre à jour

- Champ d’application : Ce problème concerne la version 1906.
- Cause : Dans le portail de l’opérateur, l’état de mise à jour du correctif logiciel indique un état incorrect pour la mise à jour. L’état initial indique que l’installation de la mise à jour a échoué, même si elle est toujours en cours.
- Correction : Actualisez le portail pour faire passer l’état à « en cours ».
- Occurrence : De façon intermittente

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

#### <a name="documentation"></a>Documentation

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les liens vers la documentation dans la page de vue d’ensemble de la Passerelle de réseau virtuel renvoient vers la documentation spécifique à Azure au lieu d’Azure Stack. Utilisez les liens suivants pour accéder à la documentation Azure Stack :

  - [SKU de passerelle](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Connexions hautement disponibles](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configurer le protocole BGP sur Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuits ExpressRoute](azure-stack-connect-expressroute.md)
  - [Spécifier des stratégies IPsec/IKE personnalisées](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

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

## <a name="compute"></a>Calcul

### <a name="vm-boot-diagnostics"></a>Diagnostics de démarrage de machine virtuelle

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Quand vous créez une machine virtuelle Windows, l’erreur suivante peut s’afficher : **Échec du démarrage de la machine virtuelle « vm-name ». Error: Failed to update serial output settings for VM 'vm-name' (Impossible de mettre à jour les paramètres de sortie en série de la machine virtuelle « vm-name »)** . Cette erreur se produit si vous activez les diagnostics de démarrage sur une machine virtuelle alors que vous avez supprimé votre compte de stockage des diagnostics de démarrage.
- Correction : Recréez le compte de stockage avec le même nom que celui utilisé précédemment.
- Occurrence : Courant

### <a name="virtual-machine-scale-set"></a>Jeu de mise à l’échelle de machine virtuelle


#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Créer des échecs au cours des correctifs et mises à jour sur des environnements Azure Stack à 4 nœuds

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : La création de machines virtuelles dans un groupe à haute disponibilité de 3 domaines d’erreur et la création d’une instance de groupe de machines virtuelles identiques échouent avec une erreur **FabricVmPlacementErrorUnsupportedFaultDomainSize** pendant le processus de mise à jour sur un environnement Azure Stack à 4 nœuds.
- Correction : Vous pouvez réussir à créer des machines virtuelles uniques dans un groupe à haute disponibilité comprenant 2 domaines d’erreur. En revanche, la création d’instances n’est toujours pas disponible pendant le processus de mise à jour sur un environnement Azure Stack à 4 nœuds.

### <a name="ubuntu-ssh-access"></a>Accès SSH Ubuntu

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Une machine virtuelle Ubuntu 18.04 créée avec une autorisation SSH activée ne vous permet pas d’utiliser les clés SSH pour vous connecter.
- Correction : Utilisez un accès à la machine virtuelle pour l’extension Linux afin d’implémenter des clés SSH après le provisionnement, ou utilisez une authentification par mot de passe.
- Occurrence : Courant

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>La réinitialisation du mot de passe d’un groupe de machines virtuelles identiques ne fonctionne pas

- Champ d’application : Ce problème concerne la version 1906.
- Cause : Un nouveau panneau de réinitialisation du mot de passe s’affiche dans l’interface utilisateur de groupe identique, mais Azure Stack ne prend pas encore en charge la réinitialisation de mot de passe sur un groupe identique.
- Correction : Aucune.
- Occurrence : Courant

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Nuage qui pleure sur le diagnostic de groupe identique

- Champ d’application : Ce problème concerne la version 1906.
- Cause : La page de la vue d’ensemble du groupe de machines virtuelles identiques contient un graphique vide. En cliquant sur le graphique vide, un panneau avec un nuage qui pleure. C’est le graphique des informations de diagnostic du groupe identique, comme le pourcentage UC, et ce n’est pas une fonctionnalité prise en charge dans la version Azure Stack actuelle.
- Correction : Aucune.
- Occurrence : Courant

### <a name="virtual-machine-diagnostic-settings-blade"></a>Panneau des paramètres de diagnostic de machine virtuelle

- Champ d’application : Ce problème concerne la version 1906.
- Cause : Le panneau des paramètres de diagnostic de machine virtuelle comporte un onglet **Récepteur**, qui demande un **compte Application Insights**. C’est le résultat d’un nouveau panneau et ce n’est pas encore pris en charge dans Azure Stack.
- Correction : Aucune.
- Occurrence : Courant

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1905"
## <a name="1905-update-process"></a>Processus de mise à jour 1905

### <a name="host-node-update-prerequisite-failure"></a>Échec lié aux prérequis de mise à jour d’un nœud hôte

- Champ d’application : Ce problème concerne la mise à jour 1905.
- Cause : Quand vous tentez d’installer la mise à jour 1905 d’Azure Stack, l’état de la mise à jour peut échouer à cause des **prérequis de mise à jour d’un nœud hôte**. Cela est généralement dû au manque d’espace disque disponible sur un nœud hôte.
- Correction : Contactez le support technique Azure Stack pour savoir comment libérer de l’espace disque sur le nœud hôte.
- Occurrence : Rare

### <a name="preparation-failed"></a>Échec de la préparation

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Quand vous tentez d’installer la mise à jour 1905 d’Azure Stack, l’état de la mise à jour peut échouer et passer à **PreparationFailed**. Cela est dû au fait que le fournisseur de ressources de mise à jour est dans l’impossibilité de transférer correctement les fichiers du conteneur de stockage vers un partage d’infrastructure interne à des fins de traitement. Ce problème est davantage susceptible de se produire, car le package de mise à jour 1905 est plus volumineux que les packages de mise à jour précédents.
- Correction : À compter de la version 1901 (1.1901.0.95), vous pouvez contourner ce problème en cliquant sur **Mettre à jour maintenant** à nouveau (et pas sur **Reprendre**). Le fournisseur de ressources de mise à jour (URP) nettoie les fichiers de la tentative précédente, puis redémarre le téléchargement. Si le problème persiste, nous vous conseillons de charger manuellement le package de mise à jour comme cela est décrit dans la section [Importer et installer des mises à jour](azure-stack-apply-updates.md).
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
- Cause : Quand vous créez une machine virtuelle Windows, l’erreur suivante peut s’afficher : **Échec du démarrage de la machine virtuelle « vm-name ». Error: Failed to update serial output settings for VM 'vm-name' (Impossible de mettre à jour les paramètres de sortie en série de la machine virtuelle « vm-name »)** .
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
- Cause : La création de machines virtuelles dans un groupe à haute disponibilité de 3 domaines d’erreur et la création d’une instance de groupe de machines virtuelles identiques échouent avec une erreur **FabricVmPlacementErrorUnsupportedFaultDomainSize** pendant le processus de mise à jour sur un environnement Azure Stack à 4 nœuds.
- Correction : Vous pouvez réussir à créer des machines virtuelles uniques dans un groupe à haute disponibilité comprenant 2 domaines d’erreur. En revanche, la création d’instances de groupe identique n’est toujours pas disponible pendant le processus de mise à jour sur un environnement Azure Stack à 4 nœuds.

#### <a name="scale-set-instance-view-blade-doesnt-load"></a>Le panneau de vue des instances de groupe identique ne charge pas

- Champ d’application : Ce problème concerne les versions 1904 et 1905.
- Cause : Le panneau de vue des instances d’un groupe de machines virtuelles identiques situé dans Portail Azure Stack -> Tableau de bord -> Groupes de machines virtuelles identiques -> AnyScaleSet - Instances -> AnyScaleSetInstance ne parvient pas à se charger et affiche l’image d’un nuage qui pleure.
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
::: moniker-end

::: moniker range=">=azs-1905"
## <a name="archive"></a>Archivage

Pour accéder aux problèmes connus archivés d'une ancienne version, utilisez le menu déroulant de sélection de la version, situé au-dessus de la table des matières à gauche, puis sélectionnez la version que vous voulez afficher.

## <a name="next-steps"></a>Étapes suivantes

- [Passer en revue la check-list des activités de mise à jour](release-notes-checklist.md)
- [Consulter la liste des mises à jour de sécurité](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1904"
## <a name="1904-archived-known-issues"></a>Problèmes connus archivés 1904
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-known-issues"></a>Problèmes connus archivés 1903
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-known-issues"></a>Problèmes connus archivés 1902
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-known-issues"></a>Problèmes connus archivés 1901
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-known-issues"></a>Problèmes connus archivés 1811
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-known-issues"></a>Problèmes connus archivés 1809
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-known-issues"></a>Problèmes connus archivés 1808
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-known-issues"></a>Problèmes connus archivés 1807
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-known-issues"></a>Problèmes connus archivés 1805
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-known-issues"></a>Problèmes connus archivés 1804
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-known-issues"></a>Problèmes connus archivés 1803
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-known-issues"></a>Problèmes connus archivés 1802
::: moniker-end

::: moniker range="<azs-1905"
Vous pouvez accéder aux [versions antérieures des problèmes connus d’Azure Stack dans la galerie TechNet](https://aka.ms/azsarchivedrelnotes). Ces documents archivés sont fournis uniquement à des fins de référence et n’impliquent aucune prise en charge de ces versions. Pour plus d’informations sur le support d’Azure Stack, consultez [Stratégie de maintenance Azure Stack](azure-stack-servicing-policy.md). Pour obtenir de l’aide, contactez les services de support technique Microsoft.
::: moniker-end
