---
title: Problèmes connus d’Azure Stack Hub
description: Découvrez les problèmes connus des versions d’Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 09/04/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 08/13/2020
ms.openlocfilehash: d7a9112955891a59e29bfe997888d3e444784494
ms.sourcegitcommit: 01dcda15d88c8d44b4918e2f599daca462a8e3d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2020
ms.locfileid: "89493816"
---
# <a name="azure-stack-hub-known-issues"></a>Problèmes connus d’Azure Stack Hub

Cet article liste les problèmes connus des versions d’Azure Stack Hub. La liste est mise à jour lorsque de nouveaux problèmes sont identifiés.

Pour accéder aux problèmes connus d'une autre version, utilisez le menu déroulant de sélection de la version, situé au-dessus de la table des matières à gauche.

::: moniker range=">=azs-1908"
> [!IMPORTANT]  
> Passez en revue cette section avant d’appliquer la mise à jour.
::: moniker-end
::: moniker range="<azs-1908"
> [!IMPORTANT]  
> Si votre instance d’Azure Stack Hub a plus de deux mises à jour de retard, elle est considérée comme non conforme. Pour bénéficier de la prise en charge, vous devez [mettre à jour avec au moins la version minimale prise en charge](azure-stack-servicing-policy.md#keep-your-system-under-support). 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-2005"
## <a name="update"></a>Update

Pour plus d’informations sur les problèmes connus de mise à jour d’Azure Stack Hub, consultez [Résolution des problèmes des mises à jour dans Azure Stack Hub](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

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

### <a name="cannot-create-a-vmss-with-standard_ds2_v2-vm-size-on-portal"></a>Impossible de créer un groupe identique de machines virtuelles avec la taille de machine virtuelle Standard_DS2_v2 sur le portail

- Champ d’application : Ce problème s’applique à la version 2002.
- Cause : Il existe un bogue de portail qui empêche la création du groupe identique de machines virtuelles avec la taille de machine virtuelle Standard_DS2_v2. La création de celui-ci génère une erreur avec : "{"code":"DeploymentFailed","message": « Au moins une opération de déploiement de ressource n’a pas réussi. Dressez la liste des opérations de déploiement pour plus d’informations. Pour plus d’informations sur l’utilisation, consultez https://aka.ms/arm-debug.","details":[{"code":"BadRequest","message":"{\r\n \" error\": {\r\n \" code\": \" NetworkProfileValidationError\" ,\r\n \" message\": \" La taille de machine virtuelle Standard_DS2_v2 ne figure pas dans la liste autorisée de tailles de machine virtuelle pour que la mise en réseau accélérée soit activée sur la machine virtuelle à l’index 0 pour le groupe identique de machines virtuelles /subscriptions/x/resourceGroups/RGVMSS/providers/Microsoft.Compute/virtualMachineScaleSets/vmss. Tailles autorisées : .\"\r\n }\r\n}"}]}"
- Correction : Créez un groupe identique de machines virtuelles avec PowerShell ou un modèle Resource Manager.

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

::: moniker range="azs-1910"
## <a name="update"></a>Update

Pour plus d’informations sur les problèmes connus de mise à jour d’Azure Stack Hub, consultez [Résolution des problèmes des mises à jour dans Azure Stack Hub](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

## <a name="portal"></a>Portail

### <a name="administrative-subscriptions"></a>Abonnements d’administration

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les deux abonnements d’administration qui ont été introduits avec la version 1804 ne doivent pas être utilisés. Les types d’abonnements sont **Metering (Compteur)** et **Consumption (Consommation)** .
- Correction : Si vous disposez de ressources s’exécutant sur ces deux abonnements, recréez-les dans des abonnements utilisateur.
- Occurrence : Courant

### <a name="duplicate-subscription-button-in-lock-blade"></a>Bouton d’abonnement en double dans le panneau de verrouillage

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail administrateur, le panneau de **verrouillage** des abonnements utilisateur comporte deux boutons dont l’intitulé est **Abonnement**.
- Occurrence : Courant

### <a name="subscription-permissions"></a>Autorisations d’abonnement

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Vous ne pouvez pas voir les autorisations définies pour votre abonnement à l’aide des portails Azure Stack Hub.
- Correction : Utilisez [PowerShell pour vérifier les autorisations](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Occurrence : Courant

### <a name="storage-account-settings"></a>Paramètres du compte de stockage

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, le panneau **Configuration** du compte de stockage présente une option permettant de modifier le **type de transfert de sécurité**. La fonctionnalité n’est pas prise en charge dans Azure Stack Hub.
- Occurrence : Courant

### <a name="upload-blob-with-oauth-error"></a>Erreur de chargement d’objet blob avec OAuth

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail de l’utilisateur, lorsque vous essayez de charger un objet blob à l’aide de l’option **OAuth (préversion)** , la tâche échoue avec un message d’erreur.
- Correction : Chargez le blob à l’aide de l’option SAP.
- Occurrence : Courant

### <a name="upload-blob-option-unsupported"></a>Option de chargement d’objet blob non prise en charge

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, quand vous essayez de charger un objet blob dans le panneau de chargement, vous pouvez sélectionner **AAD** ou **Key Authentication** (Authentification par clé), mais **AAD** n’est pas pris en charge dans Azure Stack Hub.
- Occurrence : Courant

### <a name="alert-for-network-interface-disconnected"></a>Alerte relative à l’interface réseau déconnectée

- Champ d’application : Ce problème concerne les versions 1908 et ultérieures.
- Cause : Quand un câble est déconnecté d’une carte réseau, aucune alerte ne s’affiche dans le portail administrateur. Ce problème est dû au fait que cette erreur est désactivée par défaut dans Windows Server 2019.
- Occurrence : Courant

### <a name="incorrect-tooltip-when-creating-vm"></a>Info-bulle incorrecte lors de la création d’une machine virtuelle

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, lorsque vous sélectionnez un disque managé de type SSD Premium, la liste déroulante affiche **Disque du système d’exploitation**. L’info-bulle en regard de cette option indique **Certaines tailles de disque de système d’exploitation peuvent être disponibles gratuitement avec le compte Azure gratuit**. Toutefois, cela est non valide pour Azure Stack Hub. De plus, la liste inclut **Compte gratuit éligible**, ce qui est également non valide pour Azure Stack Hub.
- Occurrence : Courant

### <a name="delete-a-storage-container"></a>Supprimer un conteneur de stockage

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, lorsqu’un utilisateur tente de supprimer un conteneur de stockage, l’opération échoue lorsque l’utilisateur ne sélectionne pas **Remplacer les paramètres Azure Policy et de rôle RBAC**.
- Correction : Assurez-vous que la case est cochée pour **Remplacer les paramètres Azure Policy et de rôle RBAC**.
- Occurrence : Courant

### <a name="refresh-button-on-virtual-machines-fails"></a>Échec du bouton d’actualisation sur les machines virtuelles

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, lorsque vous accédez à **Machines virtuelles** et tentez d’actualiser à l’aide du bouton situé en haut, les états ne sont pas mis à jour correctement.
- Correction : L’état est automatiquement mis à jour toutes les 5 minutes, que l’utilisateur ait cliqué ou non sur le bouton d’actualisation. Attendez 5 minutes et vérifiez l’état.
- Occurrence : Courant

### <a name="storage-account-options"></a>Options de compte de stockage

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, le nom des comptes de stockage est affiché en tant que **Compte de stockage - objet blob, fichier, table, file d’attente**. Toutefois, **fichier** n’est pas pris en charge dans Azure Stack Hub.
- Occurrence : Courant

### <a name="storage-account-configuration"></a>Configuration du compte de stockage

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, lorsque vous créez un compte de stockage et que vous affichez sa **configuration**, vous ne pouvez pas enregistrer les modifications de configuration, car cela génère une erreur AJAX.
- Occurrence : Courant

### <a name="capacity-monitoring-in-sql-resource-provider-keeps-loading"></a>La supervision de la capacité dans le fournisseur de ressources SQL continue à se charger

- Champ d’application : Ce problème s’applique à la mise à jour 1910 ou ultérieure d’Azure Stack Hub, quand le fournisseur de ressources SQL version 1.1.33.0 ou antérieure est installé.
- Cause : La version actuelle du fournisseur de ressources SQL n’est pas compatible avec certaines des modifications de portail les plus récentes, figurant dans la mise à jour 1910.
- Correction : Suivez le processus de mise à jour du fournisseur de ressources pour appliquer le correctif logiciel du fournisseur de ressources SQL 1.1.47.0 après la mise à niveau d’Azure Stack Hub vers la mise à jour 1910 ([SQL RP version 1.1.47.0](https://aka.ms/azurestacksqlrp11470)). Pour le fournisseur de ressources MySQL, il est également recommandé d’appliquer le correctif logiciel du fournisseur de ressources MySQL 1.1.47.0 après la mise à niveau d’Azure Stack Hub vers la mise à jour 1910 ([MySQL RP version 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)).
- Occurrence : Courant

### <a name="access-control-iam"></a>Contrôle d’accès (IAM)

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : L’extension IAM est obsolète. Le portail Ibiza fourni avec Azure Stack Hub introduit un nouveau comportement qui entraîne l’échec de l’extension RBAC si l’utilisateur ouvre le panneau **Contrôle d’accès (IAM)** pour un abonnement qui n’est pas sélectionné dans le sélecteur d’abonnement global (**Annuaire + abonnement** dans le portail utilisateur). Le panneau affiche **Chargement** en boucle, et l’utilisateur ne peut pas ajouter de nouveaux rôles à l’abonnement. Le panneau **Ajouter** affiche également **Chargement** en boucle.
- Correction : Vérifiez que l’abonnement est coché dans le menu **Répertoire + abonnement**. Le menu est accessible dans le haut du portail, près du bouton **Notifications**, ou via le raccourci du panneau **Toutes les ressources** qui affiche **Vous ne voyez pas d’abonnement ? Ouvrir les paramètres de répertoire et d’abonnement**. L’abonnement doit être sélectionné dans ce menu.

### <a name="sql-resource-provider"></a>Fournisseur de ressources SQL

- Champ d’application : Ce problème s’applique aux empreintes qui exécutent la version 1908 ou une version antérieure.
- Cause : Au moment du déploiement de la version 1.1.47.0 du fournisseur de ressources (RP) SQL, le portail n’affiche aucune autre ressource que celles associées à SQL RP.
- Correction : Supprimez le fournisseur de ressources, mettez à niveau l’empreinte et redéployez le fournisseur de ressources SQL.

### <a name="activity-log-blade"></a>Panneau Journal d’activité

- Champ d’application : Ce problème concerne les empreintes qui exécutent la version 1907 ou une version ultérieure. <!-- Note: Applies to 2002 as well -->
- Cause : Quand vous accédez au journal d’activité, le portail affiche uniquement la première page des entrées. L’option **Charger plus de résultats** ne charge pas d’entrées supplémentaires.
- Correction : Ajustez l’intervalle de temps dans le filtre pour passer en revue les entrées qui se situent après la première page.

## <a name="networking"></a>Mise en réseau

### <a name="load-balancer"></a>Équilibrage de charge

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Lors de l’ajout de machines virtuelles d’un groupe à haute disponibilité au pool de back-ends d’un équilibreur de charge, un message d’erreur s’affiche sur le portail, indiquant **Échec de l’enregistrement du pool principal d’équilibreurs de charge**. Il s’agit d’un problème esthétique sur le portail. Les fonctionnalités sont toujours en place et les machines virtuelles sont correctement ajoutées en interne au pool de back-ends.
- Occurrence : Courant

### <a name="network-security-groups"></a>Network Security Group

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge. 
- Cause : Il n’est pas autorisé de créer une règle **DenyAllOutbound** explicite dans un groupe de sécurité réseau. En effet, elle empêcherait toute communication interne avec l’infrastructure, et cette communication est nécessaire au bon déploiement de la machine virtuelle.
- Occurrence : Courant

### <a name="service-endpoints"></a>Points de terminaison de service

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, le panneau **Réseau virtuel** comporte une option pour utiliser les **points de terminaison du service**. Cette fonctionnalité n’est pas prise en charge dans Azure Stack Hub.
- Occurrence : Courant

### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>Impossible de supprimer un groupe de sécurité réseau si des cartes réseau ne sont pas associées à une machine virtuelle en cours d’exécution

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Lors de la dissociation d’un groupe de sécurité réseau et d’une carte réseau qui n’est pas associée à une machine virtuelle en cours d’exécution, l’opération de mise à jour (PUT) de cet objet échoue au niveau de la couche du contrôleur de réseau. Le groupe de sécurité réseau est mis à jour au niveau de la couche du fournisseur de ressources réseau, mais pas sur le contrôleur de réseau, de sorte que le groupe de sécurité réseau passe à l’état d’échec.
- Correction : Attachez les cartes réseau associées au groupe de sécurité réseau devant être supprimé avec des machines virtuelles en cours d’exécution, et dissociez le groupe de sécurité réseau ou retirez toutes les cartes réseau associées au groupe de sécurité réseau.
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

### <a name="virtual-network-gateway"></a>Passerelle de réseau virtuel

#### <a name="next-hop-type"></a>Type de tronçon suivant

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, quand vous créez une table de routage, **Passerelle de réseau virtuel** apparaît comme l’une des options de type de tronçon suivant. Toutefois, cela n’est pas pris en charge dans Azure Stack Hub.
- Occurrence : Courant

#### <a name="alerts"></a>Alertes

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, le panneau **Passerelle de réseau virtuel** comporte une option pour utiliser les **alertes**. Cette fonctionnalité n’est pas prise en charge dans Azure Stack Hub.
- Occurrence : Courant

#### <a name="active-active"></a>Actif/actif

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, lors de la création, et dans le menu **Passerelle de réseau virtuel**, vous verrez une option permettant d’activer la configuration **Actif/actif**. Cette fonctionnalité n’est pas prise en charge dans Azure Stack Hub.
- Occurrence : Courant

#### <a name="vpn-troubleshooter"></a>Résolution des problèmes de VPN

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, le panneau **Connexions** affiche une fonctionnalité appelée **Résolution des problèmes de VPN**. Cette fonctionnalité n’est pas prise en charge dans Azure Stack Hub.
- Occurrence : Courant

#### <a name="vpn-troubleshooter"></a>Résolution des problèmes de VPN

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, les fonctionnalités **Résolution des problèmes liés au VPN** et **Métriques** figurent dans une ressource de passerelle VPN, mais cela n’est pas pris en charge dans Azure Stack Hub.
- Occurrence : Courant

#### <a name="documentation"></a>Documentation

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les liens vers la documentation dans la page de vue d’ensemble de la passerelle de réseau virtuel pointent vers la documentation spécifique à Azure au lieu d’Azure Stack Hub. Utilisez les liens suivants pour accéder à la documentation d’Azure Stack Hub :

  - [SKU de passerelle](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Connexions hautement disponibles](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configurer le protocole BGP sur Azure Stack Hub](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuits ExpressRoute](azure-stack-connect-expressroute.md)
  - [Spécifier des stratégies IPsec/IKE personnalisées](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Calcul

### <a name="vm-boot-diagnostics"></a>Diagnostics de démarrage de machine virtuelle

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Quand vous créez une machine virtuelle Windows, l’erreur suivante peut s’afficher : **Échec du démarrage de la machine virtuelle « vm-name ». Erreur : Failed to update serial output settings for VM 'vm-name' (Impossible de mettre à jour les paramètres de sortie en série de la machine virtuelle « vm-name »)** . Cette erreur se produit si vous activez les diagnostics de démarrage sur une machine virtuelle alors que vous avez supprimé votre compte de stockage des diagnostics de démarrage.
- Correction : Recréez le compte de stockage avec le même nom que celui utilisé précédemment.
- Occurrence : Courant

### <a name="consumed-compute-quota"></a>Quota de calcul consommé

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Lors de la création d’une machine virtuelle, vous pouvez recevoir une erreur telle que **Cet abonnement a atteint sa capacité maximale pour Total des processeurs virtuels régionaux à cet emplacement. Cet abonnement utilise l’ensemble des 50 Total des processeurs virtuels régionaux disponibles.** . Cela indique que le quota pour le nombre total de cœurs à votre disposition a été atteint.
- Correction : Demandez à votre opérateur un plan additionnel avec un quota supplémentaire. La modification du quota du plan actuel ne fonctionne pas ou ne reflète pas l’augmentation du quota.
- Occurrence : Rare

### <a name="privileged-endpoint"></a>Point de terminaison privilégié

- Champ d’application : Ce problème concerne les versions 1910 et antérieures.
- Cause : Impossible de se connecter au point de terminaison privilégié (machines virtuelles ERC) à partir d’un ordinateur exécutant une version non anglaise de Windows.
- Correction : Il s’agit d’un problème connu qui a été résolu dans les versions postérieures à 1910. En guise de solution de contournement, vous pouvez exécuter les cmdlets **New-PSSession** et **Enter-PSSession** en utilisant la culture **en-US**. Par exemple, définissez la culture avec le script suivant : https://resources.oreilly.com/examples/9780596528492/blob/master/Use-Culture.ps1.
- Occurrence : Rare

### <a name="virtual-machine-scale-set"></a>Jeu de mise à l’échelle de machine virtuelle

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Créer des échecs durant l’application des correctifs logiciels et des mises à jour dans les environnements Azure Stack Hub à 4 nœuds

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Échec de la création de machines virtuelles dans un groupe à haute disponibilité de 3 domaines d’erreur, et échec de la création d’une instance de groupe de machines virtuelles identiques avec l’erreur **FabricVmPlacementErrorUnsupportedFaultDomainSize** pendant le processus de mise à jour sur un environnement Azure Stack Hub à 4 nœuds.
- Correction : Vous pouvez réussir à créer des machines virtuelles uniques dans un groupe à haute disponibilité comprenant 2 domaines d’erreur. Toutefois, la création d’instances de groupes identiques n’est toujours pas disponible pendant le processus de mise à jour sur un déploiement Azure Stack Hub à 4 nœuds.

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-update-process"></a>Processus de mise à jour 1908

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : L’installation de la mise à jour d’Azure Stack Hub peut échouer, et son état peut passer à **PreparationFailed**. Cela est dû au fait que le fournisseur de ressources de mise à jour est dans l’impossibilité de transférer correctement les fichiers du conteneur de stockage vers un partage d’infrastructure interne à des fins de traitement.
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

### <a name="duplicate-subscription-button-in-lock-blade"></a>Bouton d’abonnement en double dans le panneau de verrouillage

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail administrateur, le panneau de **verrouillage** des abonnements utilisateur comporte deux boutons dont l’intitulé est **Abonnement**.
- Occurrence : Courant

### <a name="subscription-permissions"></a>Autorisations d’abonnement

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Vous ne pouvez pas voir les autorisations définies pour votre abonnement à l’aide des portails Azure Stack Hub.
- Correction : Utilisez [PowerShell pour vérifier les autorisations](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Occurrence : Courant

### <a name="storage-account-settings"></a>Paramètres du compte de stockage

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, le panneau **Configuration** du compte de stockage présente une option permettant de modifier le **type de transfert de sécurité**. La fonctionnalité n’est pas prise en charge dans Azure Stack Hub.
- Occurrence : Courant

### <a name="upload-blob"></a>Charger l’objet blob

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail de l’utilisateur, lorsque vous essayez de charger un objet blob à l’aide de l’option **OAuth (préversion)** , la tâche échoue avec un message d’erreur.
- Correction : Chargez le blob à l’aide de l’option SAP.
- Occurrence : Courant

### <a name="alert-for-network-interface-disconnected"></a>Alerte relative à l’interface réseau déconnectée

- Champ d’application : Ce problème s’applique à la version 1908.
- Cause : Quand un câble est déconnecté d’une carte réseau, aucune alerte ne s’affiche dans le portail administrateur. Ce problème est dû au fait que cette erreur est désactivée par défaut dans Windows Server 2019.
- Occurrence : Courant

## <a name="networking"></a>Mise en réseau

### <a name="load-balancer"></a>Load Balancer

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge. 
- Cause : Lors de l'ajout des machines virtuelles d'un groupe à haute disponibilité au pool principal d'un équilibreur de charge, le message d'erreur suivant s'affiche sur le portail : **Échec de l'enregistrement du pool principal d'équilibrage de charge**. Il s’agit d’un problème esthétique sur le portail. Les fonctionnalités sont toujours en place et les machines virtuelles sont correctement ajoutées en interne au pool de back-ends. 
- Occurrence : Courant

### <a name="network-security-groups"></a>Network Security Group

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge. 
- Cause : Il n’est pas autorisé de créer une règle **DenyAllOutbound** explicite dans un groupe de sécurité réseau. En effet, elle empêcherait toute communication interne avec l’infrastructure, et cette communication est nécessaire au bon déploiement de la machine virtuelle.
- Occurrence : Courant

### <a name="service-endpoints"></a>Points de terminaison de service

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, le panneau **Réseau virtuel** comporte une option pour utiliser les **points de terminaison du service**. Cette fonctionnalité n’est pas prise en charge dans Azure Stack Hub.
- Occurrence : Courant

### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>Impossible de supprimer un groupe de sécurité réseau si des cartes réseau ne sont pas associées à une machine virtuelle en cours d’exécution

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Lors de la dissociation d’un groupe de sécurité réseau et d’une carte réseau qui n’est pas associée à une machine virtuelle en cours d’exécution, l’opération de mise à jour (PUT) de cet objet échoue au niveau de la couche du contrôleur de réseau. Le groupe de sécurité réseau est mis à jour au niveau de la couche du fournisseur de ressources réseau, mais pas sur le contrôleur de réseau, de sorte que le groupe de sécurité réseau passe à l’état d’échec.
- Correction : Attachez les cartes réseau associées au groupe de sécurité réseau devant être supprimé avec des machines virtuelles en cours d’exécution, et dissociez le groupe de sécurité réseau ou retirez toutes les cartes réseau associées au groupe de sécurité réseau.
- Occurrence : Courant

### <a name="network-interface"></a>interface réseau

#### <a name="addingremoving-network-interface"></a>Ajout / suppression de l’interface réseau

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Une nouvelle interface réseau ne peut pas être ajoutée à une machine virtuelle qui se trouve dans l’état **en cours d’exécution**.
- Correction : Arrêtez la machine virtuelle avant d’ajouter ou de supprimer une interface réseau.
- Occurrence : Courant

#### <a name="primary-network-interface"></a>Interface réseau principale

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Une nouvelle interface réseau ne peut pas être ajoutée à une machine virtuelle qui se trouve dans l’état **en cours d’exécution**.
- Correction : Arrêtez la machine virtuelle avant d’ajouter ou de supprimer une interface réseau.
- Occurrence : Courant

### <a name="virtual-network-gateway"></a>Passerelle de réseau virtuel

#### <a name="next-hop-type"></a>Type de tronçon suivant

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, quand vous créez une table de routage, **Passerelle de réseau virtuel** apparaît comme l’une des options de type de tronçon suivant. Toutefois, cela n’est pas pris en charge dans Azure Stack Hub.
- Occurrence : Courant

#### <a name="alerts"></a>Alertes

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, le panneau **Passerelle de réseau virtuel** comporte une option pour utiliser les **alertes**. Cette fonctionnalité n’est pas prise en charge dans Azure Stack Hub.
- Occurrence : Courant

#### <a name="active-active"></a>Actif/actif

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, lors de la création, et dans le menu **Passerelle de réseau virtuel**, vous verrez une option permettant d’activer la configuration **Actif/actif**. Cette fonctionnalité n’est pas prise en charge dans Azure Stack Hub.
- Occurrence : Courant

#### <a name="vpn-troubleshooter"></a>Résolution des problèmes de VPN

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Dans le portail utilisateur, le panneau **Connexions** montre une fonctionnalité appelée **Résolution des problèmes de VPN**. Cette fonctionnalité n’est pas prise en charge dans Azure Stack Hub.
- Occurrence : Courant

#### <a name="documentation"></a>Documentation

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Les liens vers la documentation dans la page de vue d’ensemble de la passerelle de réseau virtuel pointent vers la documentation spécifique à Azure au lieu d’Azure Stack Hub. Utilisez les liens suivants pour accéder à la documentation d’Azure Stack Hub :

  - [SKU de passerelle](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Connexions hautement disponibles](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configurer le protocole BGP sur Azure Stack Hub](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuits ExpressRoute](azure-stack-connect-expressroute.md)
  - [Spécifier des stratégies IPsec/IKE personnalisées](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Calcul

### <a name="vm-boot-diagnostics"></a>Diagnostics de démarrage de machine virtuelle

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Quand vous créez une machine virtuelle Windows, l’erreur suivante peut s’afficher : **Échec du démarrage de la machine virtuelle « vm-name ». Erreur : Failed to update serial output settings for VM 'vm-name' (Impossible de mettre à jour les paramètres de sortie en série de la machine virtuelle « vm-name »)** . Cette erreur se produit si vous activez les diagnostics de démarrage sur une machine virtuelle alors que vous avez supprimé votre compte de stockage des diagnostics de démarrage.
- Correction : Recréez le compte de stockage avec le même nom que celui utilisé précédemment.
- Occurrence : Courant

### <a name="virtual-machine-scale-set"></a>Jeu de mise à l’échelle de machine virtuelle

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Créer des échecs durant l’application des correctifs logiciels et des mises à jour dans les environnements Azure Stack Hub à 4 nœuds

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Échec de la création de machines virtuelles dans un groupe à haute disponibilité de 3 domaines d’erreur, et échec de la création d’une instance de groupe de machines virtuelles identiques avec l’erreur **FabricVmPlacementErrorUnsupportedFaultDomainSize** pendant le processus de mise à jour sur un environnement Azure Stack Hub à 4 nœuds.
- Correction : Vous pouvez réussir à créer des machines virtuelles uniques dans un groupe à haute disponibilité comprenant 2 domaines d’erreur. En revanche, la création d’instances de groupe identique n’est toujours pas disponible pendant le processus de mise à jour sur un environnement Azure Stack Hub à 4 nœuds.

### <a name="ubuntu-ssh-access"></a>Accès SSH Ubuntu

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Une machine virtuelle Ubuntu 18.04 créée avec une autorisation SSH activée ne vous permet pas d’utiliser les clés SSH pour vous connecter.
- Correction : Utilisez un accès à la machine virtuelle pour l’extension Linux afin d’implémenter des clés SSH après le provisionnement, ou utilisez une authentification par mot de passe.
- Occurrence : Courant

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>La réinitialisation du mot de passe d’un groupe de machines virtuelles identiques ne fonctionne pas

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : Un nouveau panneau de réinitialisation du mot de passe s’affiche dans l’interface utilisateur de groupe identique, mais Azure Stack Hub ne prend pas encore en charge la réinitialisation de mot de passe sur un groupe identique.
- Correction : Aucun.
- Occurrence : Courant

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Nuage qui pleure sur le diagnostic de groupe identique

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.
- Cause : La page de la vue d’ensemble du groupe de machines virtuelles identiques contient un graphique vide. En cliquant sur le graphique vide, un panneau avec un nuage qui pleure. C’est le graphique des informations de diagnostic du groupe identique, comme le pourcentage UC, et ce n’est pas une fonctionnalité prise en charge dans la version Azure Stack Hub actuelle.
- Correction : Aucun.
- Occurrence : Courant

### <a name="virtual-machine-diagnostic-settings-blade"></a>Panneau des paramètres de diagnostic de machine virtuelle

- Champ d’application : Ce problème s’applique à toutes les versions prises en charge.    
- Cause : Le panneau des paramètres de diagnostic de machine virtuelle comporte un onglet **Récepteur**, qui demande un **compte Application Insights**. C’est le résultat d’un nouveau panneau et ce n’est pas encore pris en charge dans Azure Stack Hub.
- Correction : Aucun.
- Occurrence : Courant

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range=">=azs-1908"
## <a name="archive"></a>Archivage

Pour accéder aux problèmes connus archivés d'une ancienne version, utilisez le menu déroulant de sélection de la version, situé au-dessus de la table des matières à gauche, puis sélectionnez la version que vous voulez afficher.

## <a name="next-steps"></a>Étapes suivantes

- [Passer en revue la check-list des activités de mise à jour](release-notes-checklist.md)
- [Consulter la liste des mises à jour de sécurité](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
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

::: moniker range="<azs-1908"
Vous pouvez accéder aux [versions antérieures des problèmes connus d’Azure Stack Hub dans la galerie TechNet](https://aka.ms/azsarchivedrelnotes). Ces documents archivés sont fournis uniquement pour référence et n’impliquent aucune prise en charge de ces versions. Pour plus d’informations sur le support d’Azure Stack Hub, consultez [Stratégie de maintenance Azure Stack Hub](azure-stack-servicing-policy.md). Pour obtenir de l’aide, contactez les services de support technique Microsoft.
::: moniker-end
