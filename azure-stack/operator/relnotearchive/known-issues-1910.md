---
title: Problèmes connus d’Azure Stack Hub 1910
description: Découvrez les problèmes connus d’Azure Stack Hub 1910.
author: sethmanheim
ms.topic: article
ms.date: 11/16/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ROBOTS: NOINDEX
ms.openlocfilehash: 0a23951d53d57b103c442ecf2b5b18f894e0f58b
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248642"
---
# <a name="azure-stack-hub-1910-known-issues"></a>Problèmes connus d’Azure Stack Hub 1910

Cet article liste les problèmes connus des versions d’Azure Stack Hub. La liste est mise à jour lorsque de nouveaux problèmes sont identifiés.

> [!IMPORTANT]  
> Si votre instance d’Azure Stack Hub a plus de deux mises à jour de retard, elle est considérée comme non conforme. Pour bénéficier de la prise en charge, vous devez [mettre à jour avec au moins la version minimale prise en charge](../azure-stack-servicing-policy.md#keep-your-system-under-support).

## <a name="update"></a>Update

Pour plus d’informations sur les problèmes connus de mise à jour d’Azure Stack Hub, consultez [Résolution des problèmes des mises à jour dans Azure Stack Hub](../azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

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

  - [SKU de passerelle](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Connexions hautement disponibles](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configurer le protocole BGP sur Azure Stack Hub](../../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuits ExpressRoute](../azure-stack-connect-expressroute.md)
  - [Spécifier des stratégies IPsec/IKE personnalisées](../../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

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
