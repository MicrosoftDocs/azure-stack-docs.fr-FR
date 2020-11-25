---
title: Problèmes connus d’Azure Stack Hub
description: Découvrez les problèmes connus des versions d’Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 11/16/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ms.openlocfilehash: da21b724e914527ef2a4d5065d1d83a30ad3bb85
ms.sourcegitcommit: 2562b86f47db20e2652d4636227afb9cfd0e03ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94785767"
---
# <a name="azure-stack-hub-known-issues"></a>Problèmes connus d’Azure Stack Hub

Cet article liste les problèmes connus des versions d’Azure Stack Hub. La liste est mise à jour lorsque de nouveaux problèmes sont identifiés.

Pour accéder aux problèmes connus d'une autre version, utilisez le menu déroulant de sélection de la version, situé au-dessus de la table des matières à gauche.

::: moniker range=">=azs-2002"
> [!IMPORTANT]  
> Passez en revue cette section avant d’appliquer la mise à jour.
::: moniker-end
::: moniker range="<azs-2002"
> [!IMPORTANT]  
> Si votre instance d’Azure Stack Hub a plus de deux mises à jour de retard, elle est considérée comme non conforme. Pour bénéficier de la prise en charge, vous devez [mettre à jour avec au moins la version minimale prise en charge](azure-stack-servicing-policy.md#keep-your-system-under-support). 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-2008"
## <a name="update"></a>Update

Pour plus d’informations sur les problèmes connus de mise à jour d’Azure Stack Hub, consultez [Résolution des problèmes des mises à jour dans Azure Stack Hub](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>Échec de la mise à jour visant à installer Microsoft.AzureStack.Compute.Installer sur une machine virtuelle d’autorité de certification

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Lors de la mise à jour, un processus acquiert un verrou sur du nouveau contenu qui doit être copié sur une machine virtuelle d’autorité de certification. En cas d’échec de la mise à jour, le verrou est libéré.
- Correction : Reprenez la mise à jour.
- Occurrence : Rare

## <a name="portal"></a>Portail

### <a name="administrative-subscriptions"></a>Abonnements d’administration

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les deux abonnements d’administration qui ont été introduits avec la version 1804 ne doivent pas être utilisés. Les types d’abonnements sont **Metering (Compteur)** et **Consumption (Consommation)** .
- Correction : Si vous disposez de ressources s’exécutant sur ces deux abonnements, recréez-les dans des abonnements utilisateur.
- Occurrence : Courant

## <a name="networking"></a>Mise en réseau

### <a name="network-security-groups"></a>Network Security Group

#### <a name="denyalloutbound-rule-cannot-be-created"></a>Impossible de créer la règle DenyAllOutbound

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Il n’est pas possible de créer une règle **DenyAllOutbound** explicite dans un groupe de sécurité réseau pendant la création d’une machine virtuelle. En effet, elle empêcherait toute communication nécessaire au bon déploiement de la machine virtuelle. Elle refuserait également les deux adresses IP essentielles requises pour déployer des machines virtuelles : Adresse IP DHCP : 169.254.169.254 et adresse IP DNS : 168.63.129.16.

- Correction : Autorisez le trafic sortant vers Internet lors de la création de la machine virtuelle, puis modifiez le groupe de sécurité réseau pour bloquer le trafic requis une fois la création de la machine virtuelle terminée.
- Occurrence : Courant

### <a name="virtual-network-gateway"></a>Passerelle de réseau virtuel

#### <a name="documentation"></a>Documentation

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les liens vers la documentation dans la page de vue d’ensemble de la passerelle de réseau virtuel pointent vers la documentation spécifique à Azure au lieu d’Azure Stack Hub. Utilisez les liens suivants pour accéder à la documentation d’Azure Stack Hub :

  - [SKU de passerelle](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Connexions hautement disponibles](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configurer le protocole BGP sur Azure Stack Hub](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuits ExpressRoute](azure-stack-connect-expressroute.md)
  - [Spécifier des stratégies IPsec/IKE personnalisées](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>Load Balancer

#### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer dirigeant le trafic vers une machine virtuelle back-end dans des scénarios spécifiques

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge. 
- Cause : Lors de l’activation de l’**affinité de session** sur un équilibreur de charge, le hachage à 2 tuples utilise l’adresse IP physique au lieu des adresses IP privées affectées aux machines virtuelles. Dans les scénarios où le trafic dirigé vers l’équilibreur de charge arrive via un VPN, ou si toutes les machines virtuelles clientes (adresses IP sources) se trouvent sur le même nœud et que l’affinité de session est activée, tout le trafic est dirigé vers une seule machine virtuelle back-end.
- Occurrence : Courant

<!-- ## Compute -->

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-2005"
## <a name="update"></a>Update

Pour plus d’informations sur les problèmes connus de mise à jour d’Azure Stack Hub, consultez [Résolution des problèmes des mises à jour dans Azure Stack Hub](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>Échec de la mise à jour visant à installer Microsoft.AzureStack.Compute.Installer sur une machine virtuelle d’autorité de certification

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Lors de la mise à jour, un processus acquiert un verrou sur du nouveau contenu qui doit être copié sur une machine virtuelle d’autorité de certification. En cas d’échec de la mise à jour, le verrou est libéré.
- Correction : Reprenez la mise à jour.
- Occurrence : Rare

## <a name="portal"></a>Portail

### <a name="administrative-subscriptions"></a>Abonnements d’administration

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les deux abonnements d’administration qui ont été introduits avec la version 1804 ne doivent pas être utilisés. Les types d’abonnements sont **Metering (Compteur)** et **Consumption (Consommation)** .
- Correction : Si vous disposez de ressources s’exécutant sur ces deux abonnements, recréez-les dans des abonnements utilisateur.
- Occurrence : Courant

## <a name="networking"></a>Mise en réseau

### <a name="network-security-groups"></a>Network Security Group

#### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>Impossible de supprimer un groupe de sécurité réseau si des cartes réseau ne sont pas associées à une machine virtuelle en cours d’exécution

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Lors de la dissociation d’un groupe de sécurité réseau et d’une carte réseau qui n’est pas associée à une machine virtuelle en cours d’exécution, l’opération de mise à jour (PUT) de cet objet échoue au niveau de la couche du contrôleur de réseau. Le groupe de sécurité réseau est mis à jour au niveau de la couche du fournisseur de ressources réseau, mais pas sur le contrôleur de réseau, de sorte que le groupe de sécurité réseau passe à l’état d’échec.
- Correction : Attachez les cartes réseau associées au groupe de sécurité réseau devant être supprimé avec des machines virtuelles en cours d’exécution, et dissociez le groupe de sécurité réseau ou retirez toutes les cartes réseau associées au groupe de sécurité réseau.
- Occurrence : Courant

#### <a name="denyalloutbound-rule-cannot-be-created"></a>Impossible de créer la règle DenyAllOutbound

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Il n’est pas possible de créer une règle **DenyAllOutbound** explicite dans un groupe de sécurité réseau pendant la création d’une machine virtuelle. En effet, elle empêcherait toute communication nécessaire au bon déploiement de la machine virtuelle.
- Correction : Autorisez le trafic sortant vers Internet lors de la création de la machine virtuelle, puis modifiez le groupe de sécurité réseau pour bloquer le trafic requis une fois la création de la machine virtuelle terminée.
- Occurrence : Courant

### <a name="virtual-network-gateway"></a>Passerelle de réseau virtuel

#### <a name="documentation"></a>Documentation

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les liens vers la documentation dans la page de vue d’ensemble de la passerelle de réseau virtuel pointent vers la documentation spécifique à Azure au lieu d’Azure Stack Hub. Utilisez les liens suivants pour accéder à la documentation d’Azure Stack Hub :

  - [SKU de passerelle](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Connexions hautement disponibles](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configurer le protocole BGP sur Azure Stack Hub](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuits ExpressRoute](azure-stack-connect-expressroute.md)
  - [Spécifier des stratégies IPsec/IKE personnalisées](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)
  
### <a name="load-balancer"></a>Load Balancer

#### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer dirigeant le trafic vers une machine virtuelle back-end dans des scénarios spécifiques

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge. 
- Cause : Lors de l’activation de l’**affinité de session** sur un équilibreur de charge, le hachage à 2 tuples utilise l’adresse IP physique au lieu des adresses IP privées affectées aux machines virtuelles. Dans les scénarios où le trafic dirigé vers l’équilibreur de charge arrive via un VPN, ou si toutes les machines virtuelles clientes (adresses IP sources) se trouvent sur le même nœud et que l’affinité de session est activée, tout le trafic est dirigé vers une seule machine virtuelle back-end.
- Occurrence : Courant

#### <a name="public-ip"></a>Adresse IP publique

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : La valeur **IdleTimeoutInMinutes** pour une adresse IP publique associée à un équilibreur de charge ne peut pas être changée. L’opération place l’adresse IP publique dans un état d’échec.
- Correction : Pour rétablir l’adresse IP publique dans un état correct, définissez le paramètre **IdleTimeoutInMinutes** de la règle de l’équilibreur de charge qui référence à nouveau l’adresse IP publique sur la valeur d’origine (la valeur par défaut est 4 minutes).
- Occurrence : Courant

## <a name="compute"></a>Calcul

### <a name="issues-deploying-virtual-machine-scale-set-with-standard_ds2_v2-size-using-the-portal"></a>Problèmes de déploiement du groupe de machines virtuelles identiques avec la taille Standard_DS2_v2 à l’aide du portail

- Champ d’application : Ce problème s’applique à la version 2005.
- Cause : Un bogue de portail entraîne l’échec de la création de groupes identiques avec la taille Standard_DS2_v2.
- Correction : Utilisez PowerShell ou l’interface CLI pour déployer la taille de machine virtuelle du groupe de machines virtuelles identiques.

### <a name="issues-using-vm-extensions-in-ubuntu-server-2004"></a>Problèmes d’utilisation d’extensions de machine virtuelle dans le Serveur Ubuntu 20.04

- Champ d’application : Ce problème s’applique à **Serveur Ubuntu 20.04 LTS**.
- Cause : Certaines distributions Linux ont été transférées vers Python 3.8 et ont supprimé complètement le point d’entrée `/usr/bin/python` hérité pour Python. Les utilisateurs de la distribution Linux qui ont effectué la transition vers Python 3.x doivent vérifier l’existence du point d’entrée `/usr/bin/python` hérité avant de tenter de déployer ces extensions sur leurs machines virtuelles. Dans le cas contraire, le déploiement de l’extension risque d’échouer.
- Correction : Suivez les étapes de résolution décrites dans [Problèmes d’utilisation d’extensions de machine virtuelle dans des systèmes de machines virtuelles Azure Linux compatibles Python 3](/azure/virtual-machines/extensions/issues-using-vm-extensions-python-3), mais ignorez l’étape 2, car Azure Stack Hub ne dispose pas de la fonctionnalité **Run Command**.

### <a name="nvv4-vm-size-on-portal"></a>Taille de machine virtuelle NVv4 sur le portail

- Champ d’application : Ce problème concerne les versions 2002 et ultérieures.
- Cause : Lors de la création de la machine virtuelle, vous verrez la taille suivante : NV4as_v4. Les clients qui disposent du matériel nécessaire à la préversion du processeur Azure Stack Hub AMD Mi25 verront réussir le déploiement de leur machine virtuelle. Tous les autres clients verront le déploiement de leur machine virtuelle échouer avec cette taille.
- Correction : Ceci est normal dans le cadre de la préparation à la préversion du GPU Azure Stack Hub.

### <a name="consumed-compute-quota"></a>Quota de calcul consommé

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Lors de la création d’une machine virtuelle, vous pouvez recevoir une erreur telle que **Cet abonnement a atteint sa capacité maximale pour Total des processeurs virtuels régionaux à cet emplacement. Cet abonnement utilise l’ensemble des 50 Total des processeurs virtuels régionaux disponibles.** . Cela indique que le quota pour le nombre total de cœurs à votre disposition a été atteint.
- Correction : Demandez à votre opérateur un plan additionnel avec un quota supplémentaire. La modification du quota du plan actuel ne fonctionne pas ou ne reflète pas l’augmentation du quota.
- Occurrence : Rare

### <a name="vm-overview-blade-does-not-show-correct-computer-name"></a>Le panneau de vue d’ensemble de la machine virtuelle n’affiche pas le nom correct de l’ordinateur

- Champ d’application : Ce problème concerne toutes les versions.
- Cause : Quand vous affichez les détails d’une machine virtuelle dans le panneau de vue d’ensemble, le nom de l’ordinateur s’affiche comme **(non disponible)** . Il s’agit du paramètre par défaut pour les machines virtuelles créées à partir de disques spécialisés ou d’instantanés de disque, ainsi que pour les images de la Place de marché.
- Correction : Affichez le panneau **Propriétés** sous **Paramètres**.

### <a name="virtual-machine-scale-set"></a>Jeu de mise à l’échelle de machine virtuelle

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Créer des échecs durant l’application des correctifs logiciels et des mises à jour dans les environnements Azure Stack Hub à 4 nœuds

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Échec de la création de machines virtuelles dans un groupe à haute disponibilité de 3 domaines d’erreur, et échec de la création d’une instance de groupe de machines virtuelles identiques avec l’erreur **FabricVmPlacementErrorUnsupportedFaultDomainSize** pendant le processus de mise à jour sur un environnement Azure Stack Hub à 4 nœuds.
- Correction : Vous pouvez réussir à créer des machines virtuelles uniques dans un groupe à haute disponibilité comprenant 2 domaines d’erreur. Toutefois, la création d’instances de groupes identiques n’est toujours pas disponible pendant le processus de mise à jour sur un déploiement Azure Stack Hub à 4 nœuds.

## <a name="storage"></a>Stockage

### <a name="retention-period-reverts-to-0"></a>La période de rétention revient à 0

- Champ d’application : Ce problème s’applique aux versions 2002 et 2005.
- Cause : si vous spécifiez une période autre que 0 dans le paramètre de période de rétention, cette valeur revient à 0 (valeur par défaut de ce paramètre) pendant la mise à jour 2002 ou 2005. Le paramètre 0 jour prend effet immédiatement après la fin de la mise à jour. Il entraîne l’arrêt immédiat de la rétention de tous les comptes de stockage supprimés présents et à venir, et leur marquage pour le nettoyage périodique de la mémoire (exécuté toutes les heures).
- Correction : Spécifiez manuellement une période de rétention correcte. Tout compte de stockage collecté pour le nettoyage avant que la nouvelle période de rétention soit spécifiée n’est pas récupérable.  

## <a name="resource-providers"></a>Fournisseurs de ressources

### <a name="sqlmysql"></a>SQL/MySQL

- Champ d’application : Ce problème concerne la version 2002.
- Cause : Si l’empreinte contient la version 1.1.33.0 ou antérieure du fournisseur de ressources SQL, lors de la mise à jour de l’empreinte, les panneaux pour SQL/MySQL ne sont pas chargés.
- Correction : Mettre à jour le fournisseur de ressources vers la version 1.1.47.0.

### <a name="app-service"></a>App Service

- Champ d’application : Ce problème concerne la version 2002.
- Cause : Si l’empreinte contient les versions 1.7 et antérieures du fournisseur de ressources App Service, lors de la mise à jour de l’empreinte, les panneaux pour App Service ne sont pas chargés.
- Correction : Mettez à jour le fournisseur de ressources avec la version 2002 Q2.

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-2002"
## <a name="update"></a>Update

Après l’application de la mise à jour 2002, une alerte pour une « source de temps non valide » peut s’afficher à tort dans le portail administrateur. Vous pouvez ignorer cette alerte de faux positif, qui sera corrigée dans une prochaine version. 

Pour plus d’informations sur les problèmes connus de mise à jour d’Azure Stack Hub, consultez [Résolution des problèmes des mises à jour dans Azure Stack Hub](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>Échec de la mise à jour visant à installer Microsoft.AzureStack.Compute.Installer sur une machine virtuelle d’autorité de certification

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Lors de la mise à jour, un processus acquiert un verrou sur du nouveau contenu qui doit être copié sur une machine virtuelle d’autorité de certification. En cas d’échec de la mise à jour, le verrou est libéré.
- Correction : Reprenez la mise à jour.
- Occurrence : Rare

## <a name="portal"></a>Portail

### <a name="administrative-subscriptions"></a>Abonnements d’administration

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les deux abonnements d’administration qui ont été introduits avec la version 1804 ne doivent pas être utilisés. Les types d’abonnements sont **Metering (Compteur)** et **Consumption (Consommation)** .
- Correction : Si vous disposez de ressources s’exécutant sur ces deux abonnements, recréez-les dans des abonnements utilisateur.
- Occurrence : Courant

### <a name="subscription-permissions"></a>Autorisations d’abonnement

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Vous ne pouvez pas voir les autorisations définies pour votre abonnement à l’aide des portails Azure Stack Hub.
- Correction : Utilisez [PowerShell pour vérifier les autorisations](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Occurrence : Courant

### <a name="storage-account-options"></a>Options de compte de stockage

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, le nom des comptes de stockage est affiché en tant que **Compte de stockage - objet blob, fichier, table, file d’attente**. Toutefois, **fichier** n’est pas pris en charge dans Azure Stack Hub.
- Occurrence : Courant

### <a name="create-managed-disk-snapshot"></a>Créer un instantané de disque managé

- Champ d’application : Ce problème concerne la version 2002.
- Cause : Dans le portail de l’utilisateur, lors de la création d’un instantané de disque managé, la zone **Type de compte** est vide. Quand vous sélectionnez le bouton **Créer** avec un type de compte vide, la création de l’instantané échoue.
- Correction : Sélectionnez un type de compte dans la liste déroulante **Type de compte**, puis créez l’instantané.
- Occurrence : Courant

### <a name="alert-for-network-interface-disconnected"></a>Alerte relative à l’interface réseau déconnectée

- Champ d’application : Ce problème concerne les versions 1908 et ultérieures.
- Cause : Quand un câble est déconnecté d’une carte réseau, aucune alerte ne s’affiche dans le portail administrateur. Ce problème est dû au fait que cette erreur est désactivée par défaut dans Windows Server 2019.
- Occurrence : Courant

## <a name="networking"></a>Mise en réseau

### <a name="denyalloutbound-rule-cannot-be-created"></a>Impossible de créer la règle DenyAllOutbound

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Il n’est pas possible de créer une règle **DenyAllOutbound** explicite dans un groupe de sécurité réseau pendant la création d’une machine virtuelle. En effet, elle empêcherait toute communication nécessaire au bon déploiement de la machine virtuelle.
- Correction : Autorisez le trafic sortant vers Internet lors de la création de la machine virtuelle, puis modifiez le groupe de sécurité réseau pour bloquer le trafic requis une fois la création de la machine virtuelle terminée.
- Occurrence : Courant

### <a name="icmp-protocol-not-supported-for-nsg-rules"></a>Protocole ICMP non pris en charge pour les règles de groupe de sécurité réseau

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge. 
- Cause : Lors de la création d’une règle de sécurité réseau entrante ou sortante, l’option **Protocole** affiche une option **ICMP**. Cette option n’est pas prise en charge dans Azure Stack Hub. Ce problème est résolu et n’apparaîtra pas dans la prochaine version d’Azure Stack Hub.
- Occurrence : Courant

### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>Impossible de supprimer un groupe de sécurité réseau si des cartes réseau ne sont pas associées à une machine virtuelle en cours d’exécution

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Lors de la dissociation d’un groupe de sécurité réseau et d’une carte réseau qui n’est pas associée à une machine virtuelle en cours d’exécution, l’opération de mise à jour (PUT) de cet objet échoue au niveau de la couche du contrôleur de réseau. Le groupe de sécurité réseau est mis à jour au niveau de la couche du fournisseur de ressources réseau, mais pas sur le contrôleur de réseau, de sorte que le groupe de sécurité réseau passe à l’état d’échec.
- Correction : Attachez les cartes réseau associées au groupe de sécurité réseau devant être supprimé avec des machines virtuelles en cours d’exécution, et dissociez le groupe de sécurité réseau ou retirez toutes les cartes réseau associées au groupe de sécurité réseau.
- Occurrence : Courant

### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer dirigeant le trafic vers une machine virtuelle back-end dans des scénarios spécifiques 

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge. 
- Cause : Lors de l’activation de l’**affinité de session** sur un équilibreur de charge, le hachage à 2 tuples utilise l’adresse IP physique au lieu des adresses IP privées affectées aux machines virtuelles. Dans les scénarios où le trafic dirigé vers l’équilibreur de charge arrive via un VPN, ou si toutes les machines virtuelles clientes (adresses IP sources) se trouvent sur le même nœud et que l’affinité de session est activée, tout le trafic est dirigé vers une seule machine virtuelle back-end.
- Occurrence : Courant

### <a name="network-interface"></a>interface réseau

#### <a name="addingremoving-network-interface"></a>Ajout / suppression de l’interface réseau

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Une nouvelle interface réseau ne peut pas être ajoutée à une machine virtuelle qui se trouve dans l’état **en cours d’exécution**.
- Correction : Arrêtez la machine virtuelle avant d’ajouter ou de supprimer une interface réseau.
- Occurrence : Courant

#### <a name="primary-network-interface"></a>Interface réseau principale

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : La carte réseau principale d’une machine virtuelle ne peut pas être changée. La suppression ou le détachement de la carte réseau principale entraînerait des problèmes de démarrage de la machine virtuelle.
- Occurrence : Courant

### <a name="public-ip"></a>Adresse IP publique

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : La valeur **IdleTimeoutInMinutes** pour une adresse IP publique associée à un équilibreur de charge ne peut pas être changée. L’opération place l’adresse IP publique dans un état d’échec.
- Correction : Pour rétablir l’adresse IP publique dans un état correct, définissez le paramètre **IdleTimeoutInMinutes** de la règle d’équilibreur de charge qui référence l’adresse IP publique sur la valeur d’origine (la valeur par défaut est 4 minutes).
- Occurrence : Courant

### <a name="virtual-network-gateway"></a>Passerelle de réseau virtuel

#### <a name="documentation"></a>Documentation

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les liens vers la documentation dans la page de vue d’ensemble de la passerelle de réseau virtuel pointent vers la documentation spécifique à Azure au lieu d’Azure Stack Hub. Utilisez les liens suivants pour accéder à la documentation d’Azure Stack Hub :

  - [SKU de passerelle](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Connexions hautement disponibles](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configurer le protocole BGP sur Azure Stack Hub](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuits ExpressRoute](azure-stack-connect-expressroute.md)
  - [Spécifier des stratégies IPsec/IKE personnalisées](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Calcul

### <a name="cannot-create-a-virtual-machine-scale-set-with-standard_ds2_v2-vm-size-on-portal"></a>Impossible de créer un groupe identique de machines virtuelles avec la taille de machine virtuelle Standard_DS2_v2 sur le portail

- Champ d’application : Ce problème s’applique à la version 2002.
- Cause : Il existe un bogue de portail qui empêche la création du groupe identique de machines virtuelles avec la taille de machine virtuelle Standard_DS2_v2. La création de celui-ci génère une erreur avec : "{"code":"DeploymentFailed","message": « Au moins une opération de déploiement de ressource n’a pas réussi. Dressez la liste des opérations de déploiement pour plus d’informations. Pour plus d’informations sur l’utilisation, consultez https://aka.ms/arm-debug.","details":[{"code":"BadRequest","message":"{\r\n \" error\": {\r\n \" code\": \" NetworkProfileValidationError\" ,\r\n \" message\": \" La taille de machine virtuelle Standard_DS2_v2 ne figure pas dans la liste autorisée de tailles de machine virtuelle pour que la mise en réseau accélérée soit activée sur la machine virtuelle à l’index 0 pour le groupe identique de machines virtuelles /subscriptions/x/resourceGroups/RGVMSS/providers/Microsoft.Compute/virtualMachineScaleSets/vmss. Tailles autorisées : .\"\r\n }\r\n}"}]}"
- Correction : Créer un groupe de machines virtuelles identiques avec PowerShell ou un modèle de gestionnaire des ressources.

### <a name="vm-overview-blade-does-not-show-correct-computer-name"></a>Le panneau de vue d’ensemble de la machine virtuelle n’affiche pas le nom correct de l’ordinateur

- Champ d’application : Ce problème concerne toutes les versions.
- Cause : Quand vous affichez les détails d’une machine virtuelle dans le panneau de vue d’ensemble, le nom de l’ordinateur s’affiche comme **(non disponible)** . Il s’agit du paramètre par défaut pour les machines virtuelles créées à partir de disques spécialisés ou d’instantanés de disque, ainsi que pour les images de la Place de marché.
- Correction : Affichez le panneau **Propriétés** sous **Paramètres**.

### <a name="nvv4-vm-size-on-portal"></a>Taille de machine virtuelle NVv4 sur le portail

- Champ d’application : Ce problème s’applique à la version 2002 et aux suivantes.
- Cause : Lors de la création de la machine virtuelle, vous verrez la taille suivante : NV4as_v4. Les clients qui disposent du matériel nécessaire à la préversion du processeur Azure Stack Hub AMD Mi25 verront réussir le déploiement de leur machine virtuelle. Tous les autres clients verront le déploiement de leur machine virtuelle échouer avec cette taille.
- Correction : Ceci est normal dans le cadre de la préparation à la préversion du GPU Azure Stack Hub.

### <a name="vm-boot-diagnostics"></a>Diagnostics de démarrage de machine virtuelle

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Quand vous créez une machine virtuelle, l’erreur suivante peut s’afficher : **Échec du démarrage de la machine virtuelle « vm-name ». Erreur : Failed to update serial output settings for VM 'vm-name' (Impossible de mettre à jour les paramètres de sortie en série de la machine virtuelle « vm-name »)** . Cette erreur se produit si vous activez les diagnostics de démarrage sur une machine virtuelle alors que vous avez supprimé votre compte de stockage des diagnostics de démarrage.
- Correction : Recréez le compte de stockage avec le même nom que celui utilisé précédemment.
- Occurrence : Courant

### <a name="vm-boot-diagnostics"></a>Diagnostics de démarrage de machine virtuelle

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Lorsque vous essayez de démarrer une machine virtuelle arrêtée-libérée, l’erreur suivante peut s’afficher : **Le compte de stockage des diagnostics de machine virtuelle « diagnosticstorageaccount » est introuvable. Vérifiez que le compte de stockage n’est pas supprimé**. L’erreur se produit si vous tentez de démarrer une machine virtuelle avec les diagnostics de démarrage activés, mais que le compte de stockage des diagnostics de démarrage référencé est supprimé.
- Correction : Recréez le compte de stockage avec le même nom que celui utilisé précédemment.
- Occurrence : Courant

### <a name="consumed-compute-quota"></a>Quota de calcul consommé

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Lors de la création d’une machine virtuelle, vous pouvez recevoir une erreur telle que **Cet abonnement a atteint sa capacité maximale pour Total des processeurs virtuels régionaux à cet emplacement. Cet abonnement utilise l’ensemble des 50 Total des processeurs virtuels régionaux disponibles.** . Cela indique que le quota pour le nombre total de cœurs à votre disposition a été atteint.
- Correction : Demandez à votre opérateur un plan additionnel avec un quota supplémentaire. La modification du quota du plan actuel ne fonctionne pas ou ne reflète pas l’augmentation du quota.
- Occurrence : Rare

### <a name="virtual-machine-scale-set"></a>Jeu de mise à l’échelle de machine virtuelle

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Créer des échecs durant l’application des correctifs logiciels et des mises à jour dans les environnements Azure Stack Hub à 4 nœuds

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Échec de la création de machines virtuelles dans un groupe à haute disponibilité de 3 domaines d’erreur, et échec de la création d’une instance de groupe de machines virtuelles identiques avec l’erreur **FabricVmPlacementErrorUnsupportedFaultDomainSize** pendant le processus de mise à jour sur un environnement Azure Stack Hub à 4 nœuds.
- Correction : Vous pouvez réussir à créer des machines virtuelles uniques dans un groupe à haute disponibilité comprenant 2 domaines d’erreur. Toutefois, la création d’instances de groupes identiques n’est toujours pas disponible pendant le processus de mise à jour sur un déploiement Azure Stack Hub à 4 nœuds.

### <a name="sql-vm"></a>Machine virtuelle SQL

#### <a name="storage-account-creating-failure-when-configuring-auto-backup"></a>Échec de création du compte de stockage lors de la configuration de la sauvegarde automatique

- Champ d’application : Ce problème s’applique à la version 2002.
- Cause : Lors de la configuration de la sauvegarde automatisée des machines virtuelles SQL avec un nouveau compte de stockage, elle échoue avec l’erreur **Échec de la validation du modèle de déploiement. Le paramètre de modèle pour « SqlAutobackupStorageAccountKind » est introuvable.**
- Correction : Appliquez le dernier correctif logiciel 2002.

#### <a name="auto-backup-cannot-be-configured-with-tls-12-enabled"></a>La sauvegarde automatique ne peut pas être configurée avec TLS 1.2 activé

- Champ d’application : Ce problème s’applique aux nouvelles installations de 2002 et versions ultérieures, ou à toute version précédente avec TLS 1.2 activé.
- Cause : Lors de la configuration de la sauvegarde automatisée des machines virtuelles SQL avec un compte de stockage existant, elle échoue avec l’erreur **SQL Server IaaS Agent : Le serveur a clos la connexion sous-jacente : Une erreur inattendue s’est produite lors de l’envoi.**
- Occurrence : Courant

## <a name="storage"></a>Stockage

### <a name="retention-period-revert-to-0"></a>La période de rétention revient à 0

- Champ d’application : Ce problème concerne les versions 2002 et 2005.
- Cause : Si vous avez précédemment paramétré une période de rétention autre que 0, celle-ci est restaurée à 0 (valeur par défaut de ce paramètre) pendant les mises à jour 2002 et 2005. Et le paramètre 0 jour prend effet immédiatement après la fin de la mise à jour. Cela entraîne l’arrêt immédiat de la rétention de tous les comptes de stockage supprimés présents et à venir, et leur marquage pour le nettoyage périodique de la mémoire (exécuté toutes les heures). 
- Correction : Spécifiez manuellement une période de rétention correcte. Cependant, tout compte de stockage collecté pour le nettoyage avant que la nouvelle période de rétention soit spécifiée n’est pas récupérable.  

## <a name="resource-providers"></a>Fournisseurs de ressources

### <a name="sqlmysql"></a>SQL/MySQL

- Champ d’application : Ce problème concerne la version 2002. 
- Cause : Si l’empreinte contient la version 1.1.33.0 ou antérieure du fournisseur de ressources SQL, lors de la mise à jour de l’empreinte, les panneaux pour SQL/MySQL ne sont pas chargés.
- Correction : Mettre à jour le fournisseur de ressources vers la version 1.1.47.0

### <a name="app-service"></a>App Service

- Champ d’application : Ce problème concerne la version 2002.
- Cause : Si l’empreinte contient les versions 1.7 et antérieures du fournisseur de ressources App Service, lors de la mise à jour de l’empreinte, les panneaux pour App Service ne sont pas chargés.
- Correction : Mettez à jour le fournisseur de ressources avec la version [2020 Q2](azure-stack-app-service-update.md).

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range=">=azs-2002"
## <a name="archive"></a>Archivage

Pour accéder aux problèmes connus archivés d'une ancienne version, utilisez le menu déroulant de sélection de la version, situé au-dessus de la table des matières à gauche, puis sélectionnez la version que vous voulez afficher.

## <a name="next-steps"></a>Étapes suivantes

- [Passer en revue la check-list des activités de mise à jour](release-notes-checklist.md)
- [Consulter la liste des mises à jour de sécurité](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1910"
## <a name="1910-archived-known-issues"></a>Problèmes connus archivés 1910
::: moniker-end
::: moniker range="azs-1908"
## <a name="1908-archived-known-issues"></a>Problèmes connus archivés 1908
::: moniker-end
::: moniker range="azs-1907"
## <a name="1907-archived-known-issues"></a>Problèmes connus archivés 1907
::: moniker-end
::: moniker range="azs-1906"
## <a name="1906-archived-known-issues"></a>Problèmes connus archivés 1906
::: moniker-end
::: moniker range="azs-1905"
## <a name="1905-archived-known-issues"></a>Problèmes connus archivés 1905
::: moniker-end
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

::: moniker range="<azs-2002"
Vous pouvez accéder aux [versions antérieures des problèmes connus d’Azure Stack Hub dans la galerie TechNet](https://aka.ms/azsarchivedrelnotes). Ces documents archivés sont fournis uniquement pour référence et n’impliquent aucune prise en charge de ces versions. Pour plus d’informations sur le support d’Azure Stack Hub, consultez [Stratégie de maintenance Azure Stack Hub](azure-stack-servicing-policy.md). Pour obtenir de l’aide, contactez les services de support technique Microsoft.
::: moniker-end
