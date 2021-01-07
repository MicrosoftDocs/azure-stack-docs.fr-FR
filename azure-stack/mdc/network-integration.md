---
title: Intégration au réseau Modular Data Center
description: Découvrez-en plus sur l’intégration au réseau Azure Stack pour Azure Modular Data Center.
author: PatAltimore
ms.author: patricka
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/02/2020
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: 3798345a7e7c5180f13c1ae94bc7407de3252e24
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872175"
---
# <a name="network-integration"></a>Intégration réseau

Cet article couvre l’intégration au réseau Azure Stack pour Azure Modular Data Center.

## <a name="border-connectivity-uplink"></a>Connectivité frontalière (liaison montante)

La planification de l’intégration au réseau est un prérequis important pour réussir le déploiement, l’exploitation et la gestion de systèmes intégrés Azure Stack. Pour commencer la planification de la connectivité frontalière, vous devez décider si vous voulez utiliser un routage dynamique avec le protocole BGP (Border Gateway Protocol) ou un routage statique. Le routage dynamique exige l’attribution d’un numéro de système autonome BGP à 16 bits (public ou privé). Le routage statique utilise une route statique par défaut affectée aux appareils frontaliers.

Les commutateurs de périphériques nécessitent des liaisons montantes de couche 3 avec des adresses IP de point à point (réseaux /30) configurées sur les interfaces physiques. Les liaisons montantes de couche 2 avec des commutateurs de périphériques prenant en charge les opérations Azure Stack ne sont pas autorisées.

### <a name="bgp-routing"></a>Routage BGP

L’utilisation d’un protocole de routage dynamique comme BGP garantit que votre système est toujours informé des changements de réseau et facilite l’administration. Pour une meilleure sécurité, vous pouvez définir un mot de passe sur le peering BGP entre la périphérie et la frontière.

Comme indiqué dans le diagramme suivant, la publication de l’espace d’adressage IP privé sur le commutateur TOR (top-of-rack) est bloquée à l’aide d’une liste de préfixes. Celle-ci refuse la publication du réseau privé et s’applique en tant que carte de routage sur la connexion entre le TOR et la périphérie.

L’équilibreur de charge logiciel (SLB, Software Load Balancer) en cours d’exécution dans la solution Azure Stack est appairé aux appareils TOR. Il peut donc publier dynamiquement les adresses IP virtuelles.

Pour garantir la récupération immédiate et transparente du trafic utilisateur en cas d’échec, le cloud privé virtuel ou l’agrégation de liaisons multichâssis (MLAG) configurée entre les appareils TOR permettent l’utilisation de MLAG sur les hôtes et les HSRP ou VRRP qui fournissent la redondance réseau pour les réseaux IP.

### <a name="static-routing"></a>Routage statique

Le routage statique nécessite une configuration supplémentaire pour les appareils qui se trouvent à la frontière. Il nécessite plus d’intervention manuelle et de gestion, ainsi qu’une analyse minutieuse avant toute modification. En fonction des changements apportés, les problèmes causés par une erreur de configuration peuvent accroître la durée de la restauration. Nous déconseillons cette méthode de routage, mais elle est prise en charge.

Pour intégrer Azure Stack dans votre environnement réseau à l’aide d’un routage statique, les quatre liens physiques entre la frontière et le périphérique doivent être connectés. La haute disponibilité ne peut pas être garantie en raison du mode de fonctionnement du routage statique.

L’appareil frontalier doit être configuré avec des routes statiques pointant vers les quatre adresses IP point à point définies entre la périphérie et la frontière pour le trafic destiné aux différents réseaux dans Azure Stack. Toutefois, seul le réseau d’adresses IP virtuelles externe ou public est nécessaire pour l’opération. Des routes statiques vers les réseaux BMC et externes sont nécessaires pour le déploiement initial. Les opérateurs peuvent choisir de laisser des routes statiques à la frontière pour accéder à des ressources d’administration qui se trouvent sur le BMC et le réseau d’infrastructure. L’ajout de routes statiques aux réseaux de l’infrastructure des commutateurs et de la gestion des commutateurs  est facultatif.

Les appareils TOR sont configurés avec un itinéraire statique par défaut qui envoie tout le trafic vers les appareils situés à la frontière. La seule exception à la règle par défaut concerne l’espace privé, qui est bloqué avec une liste ACL appliquée sur la connexion entre le TOR et la frontière.

Le routage statique s’applique seulement aux liaisons montantes entre la périphérie et les commutateurs frontaliers. Le routage dynamique BGP est utilisé dans le rack, car il s’agit d’un outil essentiel pour l’équilibreur de charge logicielle (SLB) et pour d’autres composants. Il ne peut être ni désactivé ni supprimé.

\* Le réseau BMC est facultatif après le déploiement.

\*\* Le réseau d’infrastructure du commutateur est facultatif, car le réseau entier peut être inclus dans le réseau de gestion du commutateur.

\*\*\* Le réseau de gestion du commutateur est nécessaire et peut être ajouté séparément à partir du réseau d’infrastructure du commutateur.

### <a name="transparent-proxy"></a>Proxy transparent

Si votre centre de données exige que l’ensemble du trafic utilise un proxy, vous devez configurer un proxy transparent pour traiter l’ensemble du trafic du rack afin de le gérer conformément à la stratégie. Vous devez séparer le trafic entre les zones de votre réseau.

La solution Azure Stack ne prend pas en charge les proxies web normaux.

Un proxy transparent (également appelé proxy d’interception, en ligne ou forcé) intercepte une communication normale sur la couche réseau sans nécessiter une configuration spéciale du client. Les clients ne doivent pas nécessairement être informés de l’existence du proxy.

L’interception du trafic SSL n’est pas prise en charge et peut entraîner des échecs de service lors de l’accès aux points de terminaison. Le délai d’expiration maximal pris en charge pour communiquer avec les points de terminaison nécessaires à l’identité est de 60 secondes. Trois tentatives sont autorisées.

## <a name="dns"></a>DNS

Cette section traite de la configuration du système DNS (Domain Name System).

### <a name="configure-conditional-dns-forwarding"></a>Configurer la redirection DNS conditionnelle

Cette aide s’applique uniquement à un déploiement des services de fédération Active Directory (AD FS).

Pour activer la résolution de noms de votre infrastructure DNS existante, configurez le transfert conditionnel.

Pour ajouter un redirecteur conditionnel, vous devez utiliser le point de terminaison privilégié.

Pour cette procédure, utilisez un ordinateur de votre réseau de centre de données qui peut communiquer avec le point de terminaison privilégié dans Azure Stack.

1. Ouvrez une session Windows PowerShell avec élévation de privilèges (Exécuter en tant qu’administrateur). Connectez-vous à l’adresse IP du point de terminaison privilégié. Utilisez les informations d’identification pour l’authentification de l’administrateur du cloud.

    ```powershell
    \$cred=Get-Credential Enter-PSSession -ComputerName \<IP Address of ERCS\> -ConfigurationName PrivilegedEndpoint -Credential \$cred 
    ```

1. Une fois connecté au point de terminaison privilégié, exécutez la commande PowerShell suivante. Remplacez les exemples de valeur fournis par votre nom de domaine et les adresses IP des serveurs DNS que vous souhaitez utiliser.

    ```powershell
    Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2" 
    ```

### <a name="resolve-azure-stack-dns-names-from-outside-azure-stack"></a>Résoudre les noms DNS Azure Stack en dehors d’Azure Stack

Ce sont les serveurs faisant autorité qui hébergent les informations des zones DNS externes et toutes les zones créées par l’utilisateur. Effectuez une intégration à ces serveurs afin que la délégation de zone ou le transfert conditionnel puisse résoudre les noms DNS Azure Stack en dehors d’Azure Stack.

### <a name="get-dns-server-external-endpoint-information"></a>Obtenir les informations de point de terminaison externe des serveurs DNS

Pour intégrer votre déploiement d’Azure Stack à votre infrastructure DNS, vous avez besoin des informations suivantes :

- Noms de domaine complets (FQDN) des serveurs DNS
- Adresses IP des serveurs DNS

Les noms de domaine complets des serveurs DNS Azure Stack ont le format suivant :

- *\<NAMINGPREFIX\>-ns01.\<REGION\>.\<EXTERNALDOMAINNAME\>*
- *\<NAMINGPREFIX\>-ns02.\<REGION\>.\<EXTERNALDOMAINNAME\>*

D’après les exemples de valeur, les noms de domaine complets des serveurs DNS sont les suivants :

- *azs-ns01.east.cloud.fabrikam.com*
- *azs-ns02.east.cloud.fabrikam.com*

Ces informations sont disponibles dans le portail d’administration, mais également créées à la fin de tous les déploiements Azure Stack dans un fichier nommé *AzureStackStampInformation.json*. Ce fichier se trouve dans le dossier *C:\\CloudDeployment\\logs* de la machine virtuelle de déploiement. Si vous n’êtes pas sûr des valeurs qui ont été utilisées pour votre déploiement d’Azure Stack, vous pouvez les y retrouver.

Si la machine virtuelle de déploiement n’est plus disponible ou n’est pas accessible, vous pouvez obtenir les valeurs en vous connectant au point de terminaison privilégié, puis en exécutant la cmdlet PowerShell *Get-AzureStackStampInformation*. Pour plus d’informations, voir le point de terminaison privilégié.

### <a name="set-up-conditional-forwarding-to-azure-stack"></a>Configurer le transfert conditionnel vers Azure Stack

La façon la plus simple et la plus sûre d’intégrer Azure Stack à votre infrastructure DNS consiste à effectuer un transfert conditionnel de la zone à partir du serveur qui héberge la zone parente. Nous recommandons cette approche si vous contrôlez directement les serveurs DNS qui hébergent la zone parente de votre espace de noms DNS externe Azure Stack.

Si vous n’êtes pas familiarisé avec la façon d’effectuer un transfert conditionnel avec DNS, consultez l’article TechNet « Assign a Conditional Forwarder for a Domain Name » (Affecter un redirecteur conditionnel pour un nom de domaine) ou la documentation relative à votre solution DNS.

Dans les scénarios où vous avez spécifié votre zone DNS Azure Stack externe de telle façon qu’elle s’apparente à un domaine enfant de votre nom de domaine d’entreprise, vous ne pouvez pas utiliser le transfert conditionnel. Vous devez configurer la délégation DNS.

Exemple :

- Nom de domaine DNS d’entreprise : *contoso.com*
- Nom de domaine DNS externe Azure Stack : *azurestack.contoso.com*

### <a name="edit-dns-forwarder-ips"></a>Modifier les adresses IP du redirecteur DNS

Les adresses IP du redirecteur DNS sont définies pendant le déploiement d’Azure Stack. Si les adresses IP du redirecteur ont besoin d’être mises à jour pour une quelconque raison, vous pouvez modifier les valeurs en vous connectant au point de terminaison privilégié et en exécutant les cmdlets PowerShell *Get-AzSDnsForwarder* et *Set-AzSDnsForwarder [[-IPAddress] \<IPAddress[]\>]* . Pour plus d’informations, voir le point de terminaison privilégié.

### <a name="delegate-the-external-dns-zone-to-azure-stack"></a>Déléguer la zone DNS externe à Azure Stack

Pour permettre la résolution des noms DNS en dehors d’un déploiement d’Azure Stack, vous devez configurer la délégation DNS.

Chaque bureau d’enregistrement a ses propres outils de gestion DNS pour modifier les enregistrements de serveur de noms pour un domaine. Dans la page de gestion du bureau d’enregistrement DNS, modifiez les enregistrements NS et remplacez les enregistrements NS pour la zone par ceux créés dans Azure Stack.

La plupart des bureaux d’enregistrement DNS requièrent que vous fournissiez au minimum deux serveurs DNS pour effectuer la délégation.

### <a name="firewall"></a>Pare-feu

Azure Stack configure des adresses IP virtuelles pour ses rôles d’infrastructure. Ces adresses IP virtuelles sont allouées à partir du pool d’adresses IP publiques. Chaque adresse IP virtuelle est sécurisée à l’aide d’une liste de contrôle d’accès (ACL) dans la couche réseau à définition logicielle. Les listes ACL sont également utilisées dans les commutateurs physiques (TOR et BMC) pour renforcer la solution. Une entrée DNS est créée pour chaque point de terminaison dans la zone DNS externe spécifiée au moment du déploiement. Par exemple, le portail utilisateur se voit attribué l’entrée de portail de l’hôte DNS. *\<region\>.\<fqdn\>* .

Le diagramme architectural suivant montre les différentes couches réseau et les listes de contrôle d’accès (ACL) :

![Le diagramme architectural montre les différentes couches réseau et les listes ACL.](media/network-deployment/network-architecture.png)

### <a name="ports-and-urls"></a>Ports et URL

Pour rendre des services Azure Stack comme les portails, Azure Resource Manager et DNS disponibles pour des réseaux externes, vous devez autoriser le trafic entrant vers ces points de terminaison pour des URL, des ports et des protocoles spécifiques.

Dans un déploiement où un proxy transparent achemine par liaison montante les données à un serveur proxy traditionnel ou un pare-feu protège la solution, vous devez autoriser des URL et des ports spécifiques pour les communications entrantes et sortantes. Parmi les exemples figurent les ports et les URL pour l’identité, la marketplace Azure Stack Hub, les correctifs et les mises à jour, l’inscription ainsi que les données d’utilisation.

### <a name="outbound-communication"></a>Communication sortante

Azure Stack prend en charge uniquement les serveurs proxy transparents. Dans un déploiement avec une liaison montante de proxy transparent vers un serveur proxy traditionnel, vous devez autoriser les communications sortantes des URL et des ports de la table suivante lors du déploiement en mode connecté.

L’interception du trafic SSL n’est pas prise en charge et peut entraîner des échecs de service lors de l’accès aux points de terminaison. Le délai d’expiration maximal pris en charge pour communiquer avec les points de terminaison requis pour l’identité est de 60 secondes.

>[!NOTE]
>Azure Stack ne prend pas en charge l’utilisation d’Azure ExpressRoute pour atteindre les services Azure répertoriés dans la table suivante, car ExpressRoute risque de ne pas pouvoir router le trafic vers tous les points de terminaison.


|Objectif|URL de destination|Protocol|Ports|Réseau source|
|---------|---------|---------|---------|---------|
|Identité|**Microsoft Azure**<br>login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>www.office.com<br>ManagementServiceUri = https:\//management.core.windows.net<br>ARMUri = https:\//management.azure.com<br>https:\//\*.msftauth.net<br>https:\//\*.msauth.net<br>https:\//\*.msocdn.com<br>**Azure Government**<br>https:\//login.microsoftonline.us/<br>https:\//graph.windows.net/<br>**Azure China 21Vianet**<br>https:\//login.chinacloudapi.cn/<br>https:\//graph.chinacloudapi.cn/<br>**Azure Allemagne**<br>https:\//login.microsoftonline.de/<br>https:\//graph.cloudapi.de/|HTTP<br>HTTPS|80<br>443|Adresse IP virtuelle publique - /27<br>Réseau d’infrastructure publique|
|Syndication de Place de marché Azure Stack Hub|**Microsoft Azure**<br>https:\//management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://&#42;.azureedge.net<br>**Azure Government**<br>https:\//management.usgovcloudapi.net/<br>https://&#42;.blob.core.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https:\//management.chinacloudapi.cn/<br>http://&#42;.blob.core.chinacloudapi.cn|HTTPS|443|Adresse IP virtuelle publique - /27|
|Correctifs et mises à jour|https://&#42;.azureedge.net<br>https:\//aka.ms/azurestackautomaticupdate|HTTPS|443|Adresse IP virtuelle publique - /27|
|Inscription|**Microsoft Azure**<br>https:\//management.azure.com<br>**Azure Government**<br>https:\//management.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https:\//management.chinacloudapi.cn|HTTPS|443|Adresse IP virtuelle publique - /27|
|Usage|**Microsoft Azure**<br>https://&#42;.trafficmanager.net<br>**Azure Government**<br>https://&#42;.usgovtrafficmanager.net<br>**Azure China 21Vianet**<br>https://&#42;.trafficmanager.cn|HTTPS|443|Adresse IP virtuelle publique - /27|
|Windows Defender|&#42;.wdcp.microsoft.com<br>&#42;.wdcpalt.microsoft.com<br>&#42;.wd.microsoft.com<br>&#42;.update.microsoft.com<br>&#42;.download.microsoft.com<br>https:\//www.microsoft.com/pkiops/crl<br>https:\//www.microsoft.com/pkiops/certs<br>https:\//crl.microsoft.com/pki/crl/products<br>https:\//www.microsoft.com/pki/certs<br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|Adresse IP virtuelle publique - /27<br>Réseau d’infrastructure publique|
|NTP|Adresse IP du serveur NTP fourni pour le déploiement|UDP|123|Adresse IP virtuelle publique - /27|
|DNS|Adresse IP du serveur DNS fourni pour le déploiement|TCP<br>UDP|53|Adresse IP virtuelle publique - /27|
|CRL|URL sous Points de distribution CRL sur votre certificat|HTTP|80|Adresse IP virtuelle publique - /27|
|LDAP|Forêt Active Directory fournie pour l’intégration d’Azure Graph|TCP<br>UDP|389|Adresse IP virtuelle publique - /27|
|LDAP SSL|Forêt Active Directory fournie pour l’intégration de Graph|TCP|636|Adresse IP virtuelle publique - /27|
|LDAP GC|Forêt Active Directory fournie pour l’intégration de Graph|TCP|3268|Adresse IP virtuelle publique - /27|
|LDAP GC SSL|Forêt Active Directory fournie pour l’intégration de Graph|TCP|3269|Adresse IP virtuelle publique - /27|
|AD FS|Point de terminaison de métadonnées AD FS fourni pour l'intégration AD FS|TCP|443|Adresse IP virtuelle publique - /27|
|Service de collecte des journaux de diagnostic|URL de signature d’accès partagé fournie par le Stockage Blob Azure|HTTPS|443|Adresse IP virtuelle publique - /27|
|     |     |     |     |     |
                                                                                                                                                                
### <a name="inbound-communication"></a>Communication entrante

Un ensemble d’adresses IP virtuelles d’infrastructure est nécessaire pour la publication des points de terminaison Azure Stack sur des réseaux externes. Le tableau Point de terminaison (VIP) affiche chaque point de terminaison, le port requis et le protocole. Pour les points de terminaison nécessitant des fournisseurs de ressources supplémentaires, comme le fournisseur de ressources SQL, consultez la documentation de déploiement du fournisseur de ressources spécifique.

Les adresses IP virtuelles ne sont pas répertoriées car elles ne sont pas requises pour la publication Azure Stack. Les adresses IP virtuelles d’utilisateur sont dynamiques et définies par les utilisateurs eux-mêmes, sans contrôle de la part de l’opérateur Azure Stack.

>[!NOTE]
>
>Le VPN IKEv2 est une solution VPN IPsec basée sur des normes qui utilise les ports UDP 500 et 4500 ainsi que le port TCP 50. Les pare-feux n’ouvrent pas toujours ces ports : un VPN IKEv2 peut donc ne pas être en mesure de traverser les proxys et les pare-feux.

|Point de terminaison (VIP)|Enregistrement A d’hôte DNS|Protocol|Ports|
|---------|---------|---------|---------|
|AD FS|Adfs. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portail Azure (administrateur)|Adminportal. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure Resource Manager (administrateur)|Adminmanagement. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portail Azure (utilisateur)|Portal. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure Resource Manager (utilisateur)|Management. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure Graph|Graph. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Liste de révocation de certificat|Crl. *&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;. *&lt;region>.&lt;fqdn>*|TCP et UDP|53|
|Hébergement | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure Key Vault (utilisateur)|&#42;.vault. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure Key Vault (administrateur)|&#42;.adminvault. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Stockage File d’attente Azure |&#42;.queue. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Stockage de table Azure |&#42;.table. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Stockage Blob Azure |&#42;.blob. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Fournisseur de ressources SQL|sqladapter.dbadapter. *&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Fournisseur de ressources MySQL|mysqladapter.dbadapter. *&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Azure App Service|&#42;.appservice. *&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice. *&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice. *&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |ftp.appservice. *&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|Passerelle VPN Azure|     |     |[Consulter les questions fréquentes (FAQ) sur la passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability)|
|     |     |     |     |

