---
title: Considérations relatives à la planification de l’intégration du centre de données pour les systèmes intégrés Azure Stack | Microsoft Docs
description: Découvrez comment planifier et préparer l’intégration du centre de données avec des systèmes intégrés Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 09/12/2018
ms.openlocfilehash: 1d78696da3ee3e54052cff62bcef490ccbd99925
ms.sourcegitcommit: c2ea4ffb42563c26faaf2993ba7b484bcb6d5cb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71342898"
---
# <a name="datacenter-integration-planning-considerations-for-azure-stack-integrated-systems"></a>Considérations relatives à la planification de l’intégration du centre de données pour les systèmes intégrés Azure Stack

Si vous êtes intéressé par un système intégré Azure Stack, vous devez comprendre les principales considérations qui se rapportent à la planification autour du déploiement, et la façon dont le système s’adapte à votre centre de données. Cet article fournit une vue d’ensemble de ces considérations pour vous aider à prendre des décisions importantes quant à l’infrastructure pour vos systèmes intégrés Azure Stack. Comprendre ces considérations est utile pour collaborer avec votre fournisseur de matériel OEM quand il déploie Azure Stack sur votre centre de données.  

> [!NOTE]  
> Les systèmes intégrés Azure Stack peuvent être achetés seulement auprès de fournisseurs de matériel autorisés.

Pour déployer Azure Stack, vous devez remettre ces informations de planification à votre fournisseur de solutions avant le déploiement pour que le processus soit rapide et simple. Les informations nécessaires incluent la mise en réseau, la sécurité et les informations d’identité avec de nombreuses décisions importantes qui peuvent nécessiter des connaissances dans différents domaines et de différents décideurs. Vous devrez faire appel à des personnes de différentes équipes de votre organisation pour que toutes les informations nécessaires soient prêtes avant le déploiement. Il peut être utile de communiquer avec votre fournisseur de matériel lors de la collecte de ces informations, car il peut vous conseiller de façon avisée.

Lors de la recherche et de la collecte des informations nécessaires, vous devrez peut-être apporter des modifications de configuration avant le déploiement à votre environnement réseau. Ces modifications peuvent inclure la réservation d’espaces d’adresses IP pour la solution Azure Stack ainsi que la configuration de vos routeurs, commutateurs et pare-feux afin de préparer la connectivité aux nouveaux commutateurs de la solution Azure Stack. Assurez-vous que le spécialiste de la zone de l’objet peut vous aider dans votre planification.

## <a name="capacity-planning-considerations"></a>Considérations en matière de planification de capacité
Quand vous évaluez une solution Azure Stack en vue d’une acquisition, vous faites des choix de configuration matérielle qui ont un impact direct sur la capacité globale de la solution Azure Stack. Ces choix portent sur le processeur, la densité de mémoire, la configuration du stockage et la mise à l’échelle globale de la solution (par exemple le nombre de serveurs). Contrairement à une solution de virtualisation traditionnelle, l’arithmétique simple de ces composants ne s’applique pas pour déterminer la capacité utilisable. La première raison est que l’architecture d’Azure Stack permet d’héberger les composants de gestion ou d’infrastructure au sein-même de la solution. La deuxième raison est qu’une partie de la capacité de la solution est réservée pour prendre en charge la résilience en mettant à jour les logiciels de la solution de façon à minimiser l’interruption des charges de travail du locataire.

La [feuille de calcul du planificateur de capacité Azure Stack](https://aka.ms/azstackcapacityplanner) vous aide à prendre des décisions avisées concernant la planification de la capacité de deux façons. La première consiste à sélectionner une offre de matériel et à essayer de mettre en œuvre une combinaison de ressources. La seconde consiste à définir la charge de travail qu’Azure Stack est destinée à exécuter pour voir les références SKU matérielles disponibles qui peuvent la prendre en charge. Enfin, la feuille de calcul est conçue comme un guide pour faciliter la prise de décisions relatives à la planification et à la configuration d’Azure Stack.

La feuille de calcul n’est pas destinée à se substituer à vos propres recherches et à votre propre analyse. Microsoft ne fait aucune déclaration ou n’offre aucune garantie, expresse ou implicite, concernant les informations contenues dans la feuille de caclul.

## <a name="management-considerations"></a>Considérations relatives à la gestion
Azure Stack est un système fermé, dont l’infrastructure est verrouillée à la fois du point de vue des autorisations et du réseau. Les listes de contrôle d’accès réseau (ACL) sont utilisées pour bloquer tout trafic entrant non autorisé et toute communication inutile entre les composants de l’infrastructure. Ce système rend l’accès au système difficile pour les utilisateurs non autorisés.

Pour les opérations et la gestion quotidiennes, il n’existe aucun accès administrateur non restreint à l’infrastructure. Les opérateurs Azure Stack doivent gérer le système via le portail administrateur ou via Azure Resource Manager (via PowerShell ou l’API REST). Aucun accès au système n’est possible via d’autres outils de gestion, comme le Gestionnaire Hyper-V ou le Gestionnaire du cluster de basculement. Pour protéger le système, les logiciels tiers (par exemple, les agents) ne peuvent pas être installés dans les composants de l’infrastructure Azure Stack. L’interopérabilité avec les logiciels de gestion et de sécurité externes est effectuée via PowerShell ou l’API REST.

Contactez le support Microsoft quand vous avez besoin d’un niveau d’accès plus élevé pour résoudre des problèmes qui ne sont pas résolus via les étapes de médiation d’alerte. Les options de support proposent une méthode fournissant un accès administrateur complet temporaire au système pour effectuer des opérations plus avancées.

## <a name="identity-considerations"></a>Identité - Éléments à prendre en compte

### <a name="choose-identity-provider"></a>Choisir un fournisseur d’identité
Vous devez prendre en compte le fournisseur d’identité que vous souhaitez utiliser pour le déploiement de Azure Stack, que ce soit Azure AD ou AD FS. Vous ne pouvez pas changer les fournisseurs d’identité après le déploiement sans effectuer un redéploiement complet du système. Si vous n’avez pas de compte Azure AD et que vous utilisez un compte fourni par votre fournisseur de services cloud, et que vous décidez de changer de fournisseur pour utiliser un autre compte Azure AD, vous devez contacter votre fournisseur de solutions afin qu’il redéploie la solution pour vous, à vos frais.

Votre choix de fournisseur d’identité n’a pas d’incidence sur les machines virtuelles du locataire, le système d’identité et les comptes qu’elles utilisent, la possibilité de joindre un domaine Active Directory, etc. Ces éléments sont distincts.

Pour en savoir plus sur le choix d’un fournisseur d’identité, voir l’[article relatif aux modèles de connexion des systèmes intégrés Azure Stack](./azure-stack-connection-models.md).

### <a name="ad-fs-and-graph-integration"></a>Intégration de AD FS et de Graph
Si vous choisissez de déployer Azure Stack en utilisant AD FS comme fournisseur d’identité, vous devez intégrer l’instance AD FS à Azure Stack avec une instance AD FS existante via une approbation de fédération. Cette intégration permet aux identités d’une forêt Active Directory existante de s’authentifier auprès des ressources dans Azure Stack.

Vous pouvez également intégrer le service Graph dans Azure Stack avec le Active Directory existant. Cette intégration vous permet de gérer le contrôle d’accès en fonction du rôle (RBAC) dans Azure Stack. Lorsque l’accès à une ressource est délégué, le composant Graph recherche le compte d’utilisateur dans la forêt Active Directory existante à l’aide du protocole LDAP.

Le diagramme suivant montre le flux de trafic AD FS et Graph intégré.
![Diagramme montrant le flux de trafic AD FS et de Graph](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Modèle de licence
Vous devez choisir le modèle de licence que vous souhaitez utiliser. Les options disponibles varient selon que vous déployez ou non Azure Stack en étant connecté à Internet :
- Pour un [déploiement connecté](azure-stack-connected-deployment.md), vous pouvez choisir une licence avec paiement à l’utilisation ou selon la capacité. Le paiement à l’utilisation requiert une connexion vers Azure pour rapporter l’utilisation, qui est ensuite facturée via Azure Commerce. 
- Seule la licence basée sur la capacité est prise en charge si vous effectuez un [déploiement en étant déconnecté](azure-stack-disconnected-deployment.md) d’internet. 

Pour plus d’informations sur les modèles de licence, consultez [Empaquetage et tarification de Microsoft Azure Stack](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).


## <a name="naming-decisions"></a>Décisions d’attribution de noms

Vous devez réfléchir à la façon dont vous souhaitez planifier votre espace de noms Azure Stack, notamment le nom de la région et le nom de domaine externe. Le nom de domaine complet (FQDN) de votre déploiement Azure Stack pour les points de terminaison publics est la combinaison de ces deux noms : &lt;*région*&gt;.&lt;*fqdn*&gt;. Par exemple, *east.cloud.fabrikam.com*. Dans cet exemple, les portails de Azure Stack sont accessibles aux URL suivantes :

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> Le nom de région que vous choisissez pour votre déploiement Azure Stack doit être unique. Il apparaîtra dans les adresses de portail. 

Le tableau suivant récapitule ces décisions d’attribution de noms de domaine.

| Nom | Description | 
| -------- | ------------- | 
|Nom de la région | Le nom de votre première région Azure Stack. Ce nom est utilisé comme partie du nom de domaine complet pour les adresses IP virtuelles publiques (VIP) gérées par Azure Stack. En règle générale, le nom de la région est un identificateur d’emplacement physique tel qu’un emplacement de centre de données.<br><br>Le nom de région doit uniquement comporter des lettres et des nombres compris entre 0 et 9. Les caractères spéciaux (comme `-`, `#`, etc.) ne sont pas autorisés.| 
| Nom de domaine externe | Le nom de la zone Domain Name System (DNS) pour les points de terminaison avec des adresses IP virtuelles externes. Utilisé dans le nom de domaine complet pour ces adresses IP virtuelles publiques. | 
| Nom de domaine privé (interne) | Le nom du domaine (et de la zone DNS interne) créé sur Azure Stack pour la gestion de l’infrastructure.
| | |

## <a name="certificate-requirements"></a>Configuration requise des certificats

En ce qui concerne le déploiement, vous devez fournir les certificats SSL (Secure Sockets Layer) pour les points de terminaison publics. À un niveau élevé, les certificats présentent les exigences suivantes :

- Vous pouvez utiliser un certificat à caractères génériques unique, ou vous pouvez utiliser un jeu de certificats dédiés, puis utiliser des certificats à caractères génériques uniquement pour des points de terminaison comme le stockage et Key Vault.
- Les certificats peuvent être émis par une autorité de certification (AC) approuvée publique ou une autorité de certification gérée par le client.

Pour plus d’informations sur les certificats pour infrastructure à clé publique nécessaires pour déployer Azure Stack et comment les obtenir, consultez [Exigences de certificat pour infrastructure à clé publique Azure Stack](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> Les informations de certificat pour infrastructure à clé publique fournies doivent être utilisées comme des conseils d’ordre général. Avant d’acquérir des certificats pour infrastructure à clé publique pour Azure Stack, travaillez avec votre fabricant partenaire de matériel OEM. Ce dernier fournit des conseils et des spécifications plus détaillées en matière de certificat.


## <a name="time-synchronization"></a>Synchronisation temporelle
Vous devez choisir un serveur de temps spécifique, qui est utilisé pour synchroniser Azure Stack. La synchronisation de l’heure est critique pour Azure Stack et ses rôles d’infrastructure, car elle est utilisée pour générer des tickets Kerberos. Les tickets Kerberos sont utilisés pour authentifier les services internes entre eux.

Vous devez spécifier une adresse IP pour le serveur de synchronisation de l’heure. Bien que la plupart des composants de l’infrastructure puissent résoudre une URL, certains prennent en charge seulement les adresses IP. Si vous utilisez l’option de déploiement déconnecté, vous devez spécifier un serveur de temps sur votre réseau d’entreprise, dont vous êtes sûr qu’il est accessible à partir du réseau d’infrastructure dans Azure Stack.

## <a name="connect-azure-stack-to-azure"></a>Connecter Azure Stack à Azure

Pour les scénarios de cloud hybride, vous devez planifier la façon dont vous souhaitez connecter Azure Stack à Azure. Deux méthodes vous permettent de connecter des réseaux virtuels Azure Stack à des réseaux virtuels Azure :

- **Site à site** : Une connexion de réseau privé virtuel (VPN) sur IPsec (IKE v1 et IKE v2). Ce type de connexion requiert un périphérique VPN ou le service de routage et d’accès à distance (RRAS). Pour plus d’informations sur les passerelles VPN dans Azure, consultez l’article [À propos de la passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). La communication via ce tunnel est chiffrée et sécurisée. Toutefois, la bande passante est limitée par le débit maximal du tunnel (100-200 Mbps).

- **NAT de trafic sortant** : Par défaut, toutes les machines virtuelles dans Azure Stack ont une connectivité aux réseaux externes via le NAT de trafic sortant. Chaque réseau virtuel créé dans Azure Stack se voit affecter une adresse IP publique. Si la machine virtuelle se voit directement affecter une adresse IP publique ou se trouve derrière un équilibreur de charge avec une adresse IP publique, elle aura un accès sortant via le NAT de trafic sortant avec l’adresse IP virtuelle du réseau virtuel. Cette méthode fonctionne seulement pour une communication initiée par la machine virtuelle et destinée à des réseaux externes (Internet ou intranet). Elle ne peut pas être utilisée pour communiquer avec la machine virtuelle depuis l’extérieur.

### <a name="hybrid-connectivity-options"></a>Options de connectivité hybride

Pour une connectivité hybride, il est important de savoir quel type de déploiement vous souhaitez proposer et où il sera déployé. Vous devez prendre en compte le fait de devoir peut-être isoler le trafic par locataire, et d’envisager un déploiement intranet ou internet.

- **Azure Stack avec un seul locataire** : Un déploiement d’Azure Stack qui semble, au moins du point de vue de la mise en réseau, être un seul locataire. Il peut y avoir beaucoup d’abonnements clients, mais comme tout service intranet, tout le trafic transite sur les mêmes réseaux. Le trafic provenant d’un abonnement transite sur la même connexion réseau qu’un autre abonnement et n’a pas besoin d’être isolé via un tunnel chiffré.

- **Azure Stack multilocataire** : Un déploiement d’Azure Stack, dans lequel le trafic de l’abonnement de chaque locataire lié pour les réseaux externes à Azure Stack, doit être isolé du trafic des autres locataires.
 
- **Déploiement intranet** : Un déploiement de Azure Stack qui se trouve sur un intranet d’entreprise, en général sur l’espace d’adresse IP privée et derrière un ou plusieurs pare-feu. Les adresses IP publiques ne sont pas réellement publiques, car elles ne peuvent pas être routées directement via l’Internet public.

- **Déploiement Internet** : Un déploiement d’Azure Stack qui est connecté à l’internet public et utilise des adresses IP publiques routables sur internet pour la plage d’adresses IP virtuelles publiques. Le déploiement peut toujours se placer derrière un pare-feu, mais la plage d’adresses IP virtuelles publiques est directement accessible depuis l’internet public et Azure.
 
Le tableau suivant récapitule les scénarios de connectivité hybride, avec les avantages, les inconvénients et les cas d’usage.

| Scénario | Méthode de connectivité | Avantages | Inconvénients | Bien pour |
| -- | -- | --| -- | --|
| Azure Stack avec un seul client, déploiement en intranet | NAT de trafic sortant | Meilleure bande passante pour des transferts plus rapides. Simple à implémenter ; aucune passerelle requise. | Trafic non chiffré ; aucun chiffrement ou isolation en dehors de la pile. | Déploiements d’entreprise où tous les clients sont fiables.<br><br>Entreprises qui ont un circuit Azure ExpressRoute vers Azure. |
| Azure Stack mutualisé, déploiement en intranet | VPN de site à site | Le trafic du réseau virtuel du client vers la destination est sécurisé. | La bande passante est limitée par le tunnel VPN de site à site.<br><br>Requiert une passerelle dans le réseau virtuel et un périphérique VPN sur le réseau de destination. | Les déploiements d’entreprise où certains trafics de client doivent être sécurisés par rapport aux autres clients. |
| Azure Stack avec un seul client, déploiement par internet | NAT de trafic sortant | Meilleure bande passante pour des transferts plus rapides. | Trafic non chiffré ; aucun chiffrement ou isolation en dehors de la pile. | Hébergement des scénarios où le client obtient son propre déploiement de Azure Stack et un circuit dédié à l’environnement Azure Stack. Par exemple, ExpressRoute et MPLS (Multiprotocol Label Switching).
| Azure Stack mutualisé, déploiement par internet | VPN de site à site | Le trafic du réseau virtuel du client vers la destination est sécurisé. | La bande passante est limitée par le tunnel VPN de site à site.<br><br>Requiert une passerelle dans le réseau virtuel et un périphérique VPN sur le réseau de destination. | Hébergement des scénarios où le fournisseur souhaite offrir un cloud multilocataire, où les locataires ne se font pas confiance et où le trafic doit être chiffré.
|  |  |  |  |  |

### <a name="using-expressroute"></a>Utilisation de ExpressRoute

Vous pouvez connecter Azure Stack à Azure via [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) pour un intranet à un client et des scénarios mutualisés. Vous aurez besoin d’un circuit ExpressRoute approvisionné via un [fournisseur de connectivité](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

Le diagramme suivant montre ExpressRoute pour un scénario à un client (où « Connexion du client » est le circuit ExpressRoute).

![Diagramme montrant un scénario ExpressRoute à un client](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

Le diagramme suivant montre ExpressRoute pour un scénario mutualisé.

![Diagramme montrant un scénario ExpressRoute mutualisé](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Surveillance externe
Pour obtenir une vue unique de toutes les alertes provenant de vos appareils et du déploiement d’Azure Stack, et pour intégrer des alertes dans les flux de travail existants de gestion des services informatiques pour la création de tickets, vous pouvez [intégrer Azure Stack à des solutions externes de supervision de centre de données](azure-stack-integrate-monitor.md).

Inclus dans la solution Azure Stack, l’hôte de cycle de vie du matériel est un ordinateur extérieur à Azure Stack qui exécute les outils d’administration fournie par le fabricant OEM pour le matériel. Vous pouvez utiliser ces outils ou d’autres solutions s’intégrant directement avec les solutions de surveillance existantes dans votre centre de données.

Le tableau suivant récapitule la liste des options actuellement disponibles.

| Domaine | Solution de supervision externe |
| -- | -- |
| Logiciel Azure Stack | [Pack d’administration Azure Stack pour Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Plug-in Nagios](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>Appels d’API basés sur REST | 
| Serveurs physiques (BMC via IPMI) | Matériel OEM : pack d’administration de fournisseur Operations Manager<br>Solution fournie par le fabricant de matériel OEM<br>Plu-gins Nagios du fabricant de matériel.<br>Solution de supervision prise en charge par le partenaire OEM (inclus) | 
| Périphériques réseau (SNMP) | Découverte des périphériques réseau Operations Manager<br>Solution fournie par le fabricant de matériel OEM<br>Plug-in de commutateur Nagios |
| Surveillance de l’intégrité de l’abonnement client | [Pack d’administration System Center pour Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Notez les exigences suivantes :
- La solution que vous utilisez doit être sans agent. Vous ne pouvez pas installer d’agents tiers à l’intérieur des composants de Azure Stack. 
- Si vous souhaitez utiliser System Center Operations Manager, Operations Manager 2012 R2 ou Operations Manager 2016 sont requis.

## <a name="backup-and-disaster-recovery"></a>Sauvegarde et récupération d'urgence

La planification de la sauvegarde et de la reprise d’activité implique une planification pour l’infrastructure Azure Stack sous-jacente hébergeant les machines virtuelles IaaS et les services PaaS, et pour les applications et les données du locataire. Planifiez ces éléments séparément.

### <a name="protect-infrastructure-components"></a>Protéger les composants d’infrastructure

Vous pouvez [sauvegarder les composants d’infrastructure Azure Stack](azure-stack-backup-back-up-azure-stack.md) sur un partage SMB que vous spécifiez :

- Vous avez besoin d’un partage de fichiers SMB externe sur un serveur de fichiers Windows existant ou un appareil tiers.
- Utilisez ce même partage pour la sauvegarde des commutateurs réseau et l’hôte de cycle de vie du matériel. Votre fournisseur de matériel OEM peut vous fournir des conseils quant à la sauvegarde et la restauration de ces composants, car ils sont externes à Azure Stack. Vous êtes responsable d’exécuter les workflows de sauvegarde basés sur la recommandation du fabricant OEM.

En cas de perte catastrophique de données, vous pouvez utiliser la sauvegarde de l’infrastructure pour réamorcer les données de déploiement, comme celles-ci : 

- Entrées et identificateurs du déploiement
- Comptes de service
- Certificat racine d’autorité de certification
- Ressources fédérées (dans les déploiements déconnectés)
- Plans, offres, abonnements et quotas
- Stratégie RBAC et attributions de rôles
- Secrets Key Vault

### <a name="protect-tenant-apps-on-iaas-vms"></a>Protéger les applications des locataires sur des machines virtuelles IaaS

Azure Stack ne sauvegarde pas les applications et les données des locataires. Vous devez planifier la sauvegarde et la protection de récupération d’urgence vers une cible externe au Azure Stack. La protection du client est une activité qu’il conduit lui-même. Pour les machines virtuelles IaaS, les locataires peuvent utiliser les technologies intégrées pour protéger les dossiers de fichiers, les données des applications et l’état du système. Toutefois, en tant qu’entreprise ou fournisseur de service, vous voudrez peut-être offrir une solution de sauvegarde et de restauration dans le même centre de données ou à l’extérieur, dans un cloud.

Pour sauvegarder des machines virtuelles IaaS Windows ou Linux, vous devez utiliser des produits de sauvegarde avec un accès au système d’exploitation invité pour protéger les fichiers, les dossiers, l’état du système d’exploitation et les données des applications. Vous pouvez utiliser Azure Backup, System Center Data Protection Center Manager, ou les produits tiers pris en charge.

Pour répliquer des données vers un emplacement secondaire et orchestrer le basculement d’application si un incident se produit, vous pouvez utiliser Azure Site Recovery ou des produits tiers pris en charge. En outre, les applications qui prennent en charge la réplication native, comme Microsoft SQL Server, peuvent répliquer des données vers un autre emplacement où l’application est en cours d’exécution.

## <a name="learn-more"></a>En savoir plus

- Pour plus d’informations sur les cas d’usage, l’achat, les partenaires et les fabricants de matériel OEM, consultez la page produit [Azure Stack](https://azure.microsoft.com/overview/azure-stack/).
- Pour plus d’informations sur la feuille de route et la disponibilité géographique des systèmes intégrés Azure Stack, consultez le livre blanc : [Azure Stack : An extension of Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 

## <a name="next-steps"></a>Étapes suivantes
[Modèles de connexion pour le déploiement d’Azure Stack](azure-stack-connection-models.md)
