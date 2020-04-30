---
title: Application multiniveau Windows sur Azure Stack Hub avec SQL Server
description: Apprenez à exécuter une application multiniveau Windows sur Azure Stack Hub avec SQL Server.
author: mattbriggs
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: e331be14abdeceeb2fef462fba47c4871a320e7f
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81659891"
---
# <a name="windows-n-tier-application-on-azure-stack-hub-with-sql-server"></a>Application multiniveau Windows sur Azure Stack Hub avec SQL Server

Cette architecture de référence montre comment déployer des machines virtuelles et un réseau virtuel configuré pour une application [multiniveau](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/n-tier) à l'aide de SQL Server sous Windows pour la couche Données. 

## <a name="architecture"></a>Architecture

L’architecture possède les composants suivants :

![](./media/iaas-architecture-windows-sql-n-tier/image1.png)

## <a name="general"></a>Général

-   **Groupe de ressources**. Les [groupes de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) permettent de regrouper des ressources Azure afin de pouvoir les gérer selon leur durée de vie, leur propriétaire ou d'autres critères.

-   **Groupe à haute disponibilité.** Un groupe à haute disponibilité est une configuration de centre de données destinée à assurer la redondance et la disponibilité des machines virtuelles. Cette configuration au sein d’un tampon Azure Stack Hub assure la disponibilité d’au moins une machine virtuelle pendant un événement de maintenance planifié ou non. Les machines virtuelles sont placées dans un groupe à haute disponibilité qui les répartit sur différents domaines d’erreur (hôtes Azure Stack Hub)

## <a name="networking-and-load-balancing"></a>Mise en réseau et équilibrage de charge

-   **Réseau virtuel et sous-réseaux**. Chaque machine virtuelle Azure est déployée dans un réseau virtuel qui peut être segmenté en sous-réseaux. Créez un sous-réseau distinct pour chaque niveau.

-   **Équilibreur de charge de couche 7.** Application Gateway n’étant pas encore disponible sur Azure Stack Hub, des alternatives sont proposées sur la [Place de marché Azure Stack Hub ](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1908), par exemple : [Commutateur de contenu ADC Load Balancer KEMP LoadMaster](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure)/ [f5 Big-IP Virtual Edition](https://azuremarketplace.microsoft.com/marketplace/apps/f5-networks.f5-big-ip-best) ou [A10 vThunder ADC](https://azuremarketplace.microsoft.com/marketplace/apps/a10networks.vthunder-414-gr1)

-   **Équilibreurs de charge** : Utilisez [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) pour répartir le trafic réseau de la couche Web vers la couche Entreprise, et de la couche Entreprise vers SQL Server.

-   **Groupes de sécurité réseau (NSG) :** Utilisez des groupes de sécurité réseau pour limiter le trafic réseau au sein du réseau virtuel. Par exemple, dans l’architecture à trois niveaux illustrée ici, le niveau base de données n’accepte pas le trafic en provenance du serveur web frontal, mais uniquement du niveau Business et du sous-réseau de gestion.

-   **DNS (Domain Name System)** . Azure Stack Hub ne disposant pas de son propre service d’hébergement DNS, utilisez le serveur DNS de votre service AD DS.

**Machines virtuelles**

-   **Groupe de disponibilité SQL Server Always On**. Fournit une haute disponibilité du niveau Données, en activant la réplication et le basculement. Il utilise la technologie du WSFC (Cluster de basculement Windows Server) pour le basculement.

-   **Serveurs AD DS (Active Directory Domain Services)** . Les objets ordinateur pour le cluster de basculement et ses rôles en cluster associés sont créés dans AD DS (Active Directory Domain Services). La configuration de serveurs AD DS sur les machines virtuelles du même réseau virtuel est la méthode recommandée pour joindre d'autres machines virtuelles à AD DS. Vous pouvez également joindre les machines virtuelles à des services AD DS d'entreprise existants en connectant le réseau virtuel au réseau d'entreprise à l'aide d'une connexion VPN. Dans les deux cas, vous devez remplacer le serveur DNS du réseau virtuel par votre serveur DNS AD DS (dans un réseau virtuel ou dans un réseau d'entreprise existant) pour résoudre le nom de domaine complet du domaine AD DS.

-   **Témoin de cloud**. Un cluster de basculement nécessite plus de la moitié de ses nœuds pour fonctionner, on dit alors qu’il a un quorum. Si le cluster possède seulement deux nœuds, une partition de réseau peut mener chaque nœud à penser qu’il est le nœud principal. Dans ce cas, vous avez besoin d’un *témoin* pour arbitrer et établir le quorum. Un témoin est une ressource telle qu’un disque partagé qui peut arbitrer pour établir le quorum. Le témoin de cloud est un type de témoin qui utilise le stockage Blob Azure. Pour en savoir plus sur le concept de quorum, consultez [Comprendre les quorums de cluster et de pool](https://docs.microsoft.com/windows-server/storage/storage-spaces/understand-quorum). Pour plus d’informations sur les témoins de cloud, consultez [Déployer un témoin de cloud pour un cluster de basculement](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness). Dans Azure Stack Hub, le point de terminaison du témoin cloud diffère de celui d’Azure global. 

Il peut se présenter comme suit :

- Azure global :  
  `https://mywitness.blob.core.windows.net/`

- Pour Azure Stack Hub :  
  `https://mywitness.blob.<region>.<FQDN>`

-   **Jumpbox**. Également appelée [hôte bastion](https://en.wikipedia.org/wiki/Bastion_host). Machine virtuelle sécurisée sur le réseau, utilisée par les administrateurs pour se connecter aux autres machines virtuelles. La jumpbox a un groupe de sécurité réseau qui autorise le trafic distant provenant uniquement d’adresses IP publiques figurant sur une liste verte. Le groupe de sécurité réseau doit autoriser le trafic RDP (Bureau à distance).

## <a name="recommendations"></a>Recommandations

Vos exigences peuvent différer de celles de l’architecture décrite ici. Utilisez ces recommandations comme point de départ.

### <a name="virtual-machines"></a>Machines virtuelles

Pour obtenir des recommandations sur la configuration des machines virtuelles, voir [Exécuter une machine virtuelle Windows sur Azure Stack Hub](iaas-architecture-vm-windows.md).

### <a name="virtual-network"></a>Réseau virtuel

Lorsque vous créez le réseau virtuel, déterminez le nombre d'adresses IP dont vos ressources ont besoin sur chaque sous-réseau. Spécifiez un masque de sous-réseau et une plage d'adresses de réseau suffisamment large pour les adresse IP requises, à l'aide de la notation [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). Utilisez un espace d'adressage conforme aux [blocs d'adresses IP privées](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces) standard, à savoir 10.0.0.0/8, 172.16.0.0/12 et 192.168.0.0/16.

Choisissez une plage d'adresses qui ne se chevauche pas avec votre réseau local, au cas où vous auriez ultérieurement besoin de configurer une passerelle entre le réseau virtuel et votre réseau local. Une fois le réseau virtuel créé, vous ne pouvez plus modifier la plage d'adresses.

Concevez les sous-réseaux en tenant compte des exigences en matière de sécurité et de fonctionnalités. Toutes les machines virtuelles du même niveau ou rôle doivent être placées sur le même sous-réseau, qui peut constituer une limite de sécurité. Pour plus d'informations sur la conception de réseaux virtuels et de sous-réseaux, consultez [Planifier et concevoir des réseaux virtuels Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).

### <a name="load-balancers"></a>Équilibreurs de charge

N’exposez pas les machines virtuelles directement à Internet. Attribuez plutôt à chaque machine virtuelle une adresse IP privée. Les clients se connectent à l'aide de l'adresse IP publique associée à l'équilibreur de charge de couche 7.

Définissez des règles d’équilibreur de charge pour diriger le trafic réseau vers les machines virtuelles. Par exemple, pour activer le trafic HTTP, mappez le port 80 de la configuration du serveur frontal au port 80 dans le pool d’adresses principales. Lorsqu'un client envoie une requête HTTP au port 80, l'équilibreur de charge sélectionne une adresse IP principale à l'aide d'un [algorithme de hachage](/azure/load-balancer/concepts-limitations#load-balancer-concepts) qui inclue l'adresse IP source. Les requêtes des clients sont réparties entre toutes les machines virtuelles dans le pool d’adresses principales.

### <a name="network-security-groups"></a>Groupes de sécurité réseau

Utilisez des règles de groupe de sécurité réseau pour limiter le trafic entre les niveaux. Par exemple, dans l’architecture à trois niveaux ci-dessus, le niveau Web ne communique pas directement avec le niveau Base de données. Pour appliquer cette règle, la couche Base de données doit bloquer le trafic entrant provenant du sous-réseau de la couche Web.

1.  Interdisez tout le trafic entrant provenant du réseau virtuel. (Utilisez la balise VIRTUAL_NETWORK dans la règle.)

2.  Autorisez le trafic entrant à partir du sous-réseau du niveau Business.

3.  Autorisez le trafic entrant à partir du sous-réseau du niveau Base de données. Cette règle autorise la communication entre les machines virtuelles de la base de données, qui est nécessaire pour la réplication de base de données et le basculement.

4.  Autorisez le trafic RDP (port 3389) à partir du sous-réseau du serveur de rebond. Cette règle permet aux administrateurs de se connecter au niveau Base de données à partir du serveur de rebond.

Créez les règles 2 à 4 en leur attribuant une priorité plus élevée qu'à la première règle afin qu'elles l'ignorent.

## <a name="sql-server-always-on-availability-groups"></a>Groupes de disponibilité SQL Server Always On

Nous vous recommandons d'utiliser des [groupes de disponibilité AlwaysOn](https://msdn.microsoft.com/library/hh510230.aspx) pour la haute disponibilité de SQL Server. Avec les versions antérieures à Windows Server 2016, les groupes de disponibilité AlwaysOn nécessitent un contrôleur de domaine et tous les nœuds du groupe de disponibilité doivent être dans le même domaine Active Directory.

Pour la haute disponibilité de la couche Machines virtuelles, toutes les machines virtuelles SQL doivent se trouver dans un groupe à haute disponibilité.

Les autres couches se connectent à la base de données par le biais d'un [écouteur de groupe de disponibilité](https://msdn.microsoft.com/library/hh213417.aspx). L’écouteur permet à un client SQL de se connecter sans connaître le nom de l’instance physique de SQL Server. Les machines virtuelles qui accèdent à la base de données doivent être jointes au domaine. Le client (dans ce cas, un autre niveau) utilise le système DNS pour résoudre le nom du réseau virtuel de l’écouteur en adresses IP.

Configurez le groupe de disponibilité SQL Server AlwaysOn comme suit :

1.  Créez un cluster WSFC (clustering de basculement Windows Server), un groupe de disponibilité SQL Server AlwaysOn et un réplica principal. Pour plus d'informations, consultez [Prise en main des groupes de disponibilité AlwaysOn](https://msdn.microsoft.com/library/gg509118.aspx).

2.  Créez un équilibreur de charge interne avec une adresse IP privée statique.

3.  Créez un écouteur de groupe de disponibilité et mappez le nom DNS de l’écouteur à l’adresse IP d’un équilibreur de charge interne.

4.  Créez une règle d’équilibreur de charge pour le port d’écoute SQL Server (port TCP 1433 par défaut). La règle d’équilibreur de charge doit activer *l’adresse IP flottante*, également appelé Retour direct du serveur. Cela force la machine virtuelle à répondre directement au client, ce qui permet de bénéficier d’une connexion directe au réplica principal.

> [!Note]
> Quand l’adresse IP flottante est activée, le numéro de port frontend doit être identique au numéro de port backend dans la règle d’équilibreur de charge.

Quand un client SQL tente de se connecter, l’équilibreur de charge achemine la demande de connexion au réplica principal. En cas de basculement vers un autre réplica, l’équilibreur de charge achemine automatiquement les nouvelles requêtes à un nouveau réplica principal. Pour plus d'informations, consultez [Configurer un écouteur à équilibrage de charge interne pour des groupes de disponibilité SQL Server AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

Lors d’un basculement, les connexions clientes existantes sont fermées. Une fois le basculement terminé, les nouvelles connexions sont acheminées vers le nouveau réplica principal.

Si votre application effectue plus de lectures que d'écritures, vous pouvez décharger certaines des requêtes en lecture seule vers un réplica secondaire. Voir [Utilisation d'un écouteur pour se connecter à un réplica secondaire en lecture seule (routage en lecture seule)](https://technet.microsoft.com/library/hh213417.aspx#ConnectToSecondary).

Testez votre déploiement en [forçant un basculement manuel](https://msdn.microsoft.com/library/ff877957.aspx) du groupe de disponibilité.

Pour optimiser les performances de SQL, vous pouvez également consulter l’article [Meilleures pratiques SQL Server pour optimiser les performances dans Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-sql-server-vm-considerations).

**Serveur de rebond (jumpbox)**

N’autorisez pas l’accès RDP à partir de l’Internet public aux machines virtuelles qui exécutent la charge de travail d’application. Au lieu de cela, tous les accès RDP à ces machines virtuelles doivent transiter par le serveur de rebond. Un administrateur se connecte au serveur de rebond, puis se connecte à l’autre machine virtuelle à partir du serveur de rebond. Le serveur de rebond autorise le trafic SSH à partir d’Internet, mais uniquement à partir d’adresses IP connues et sûres.

Le serveur de rebond a des exigences de performances minimales, donc sélectionnez une machine virtuelle de petite taille. Créez-lui une [adresse IP publique](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). Placez-le sur le même réseau virtuel que les autres machines virtuelles, mais sur un sous-réseau de gestion distinct.

Pour sécuriser le serveur de rebond, ajoutez une règle de groupe de sécurité réseau qui autorise les connexions RDP provenant uniquement d’un ensemble sûr d’adresses IP publiques. Configurez les groupes de sécurité réseau pour les autres sous-réseaux de façon à autoriser le trafic RDP provenant du sous-réseau de gestion.

## <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité

### <a name="scale-sets"></a>Groupes identiques

Pour les couches Web et Entreprise, envisagez d'utiliser des [groupes de machines virtuelles identiques](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview) au lieu de déployer des machines virtuelles distinctes. Un groupe identique facilite le déploiement et la gestion d'un ensemble de machines virtuelles identiques. Les groupes identiques sont parfaits si vous devez rapidement effectuer un scale-out des machines virtuelles.

Il existe deux façons de configurer des machines virtuelles déployées dans un groupe identique :

-   Utiliser des extensions pour configurer la machine virtuelle après son déploiement. Avec cette approche, le démarrage des nouvelles instances de machine virtuelle peut être plus long que pour une machine virtuelle sans extension.

-   Déployer un [disque managé](https://docs.microsoft.com/azure-stack/user/azure-stack-managed-disk-considerations) avec une image de disque personnalisée. Cette option peut être plus rapide à déployer. Toutefois, elle vous oblige à tenir l’image à jour.

Pour plus d'informations, consultez [Considérations relatives à la conception des groupes de machines virtuelles identiques](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview). Cette considération de conception s’applique tout essentiellement à Azure Stack Hub, mais sous certaines réserves :

-   Sur Azure Stack Hub, les groupes de machines virtuelles identiques ne prennent pas en charge le surapprovisionnement ou les mises à niveau propagées.

-   Sur Azure Stack Hub, les groupes de machines virtuelles identiques ne peuvent pas faire l’objet d’une mise à l’échelle automatique.

-   Sur Azure Stack Hub, nous vous recommandons vivement d’utiliser des disques managés plutôt que non managés pour les groupes de machines virtuelles identiques

-   Actuellement, une limite de 700 machines virtuelles s’applique à Azure Stack Hub, en comptant toutes les machines virtuelles de l’infrastructure, les machines virtuelles individuelles et les instances de groupes identiques.

## <a name="subscription-limits"></a>Limites d’abonnement

Chaque abonnement de locataire Azure Stack Hub présente des limites par défaut, notamment un nombre maximal de machines virtuelles par région configurées par l’opérateur Azure Stack Hub. Pour plus d’informations, voir [Présentation des services, plans, offres et abonnements Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/service-plan-offer-subscription-overview). Reportez-vous également à [Types de quotas dans Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-quota-types).

## <a name="security-considerations"></a>Considérations relatives à la sécurité

Les réseaux virtuels sont une limite d’isolation du trafic dans Azure. Par défaut, les machines virtuelles d'un réseau virtuel ne peuvent pas communiquer directement avec celles d'un autre réseau virtuel.

**NSG**. Utilisez des [groupes de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) pour limiter le trafic vers et depuis Internet. Pour plus d'informations, consultez [Services de cloud computing Microsoft et sécurité réseau](https://docs.microsoft.com/azure/best-practices-network-security).

**DMZ**. Ajoutez une appliance virtuelle réseau (NVA) pour créer un réseau de périmètre (DMZ) entre Internet et le réseau virtuel Azure. NVA est un terme générique décrivant une appliance virtuelle qui peut effectuer des tâches liées au réseau, telles que pare-feu, inspection des paquets, audit et routage personnalisé.

**Chiffrement**. Chiffrez les données sensibles au repos et utilisez [Key Vault dans Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal) pour gérer les clés de chiffrement de la base de données. Pour plus d’informations, consultez [Configurer l’intégration d’Azure Key Vault pour SQL Server sur des machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-sql-keyvault). Il est également recommandé pour stocker des secrets de l’application, comme des chaînes de connexion de base de données, dans le coffre de clés.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les modèles Azure Cloud, consultez [Modèles de conception cloud](https://docs.microsoft.com/azure/architecture/patterns).
