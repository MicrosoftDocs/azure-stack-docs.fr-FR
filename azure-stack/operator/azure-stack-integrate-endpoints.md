---
title: Publier des services Azure Stack Hub dans votre centre de données
description: Découvrez comment publier des services Azure Stack Hub dans votre centre de données.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 09/22/2020
ms.author: justinha
ms.reviewer: wamota
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: b1b3a267185d9486bdf023f1af7c8e2348e49351
ms.sourcegitcommit: 9a3397f703ff9dd7d539372bd8e5fdbe6d6a0725
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "91019620"
---
# <a name="publish-azure-stack-hub-services-in-your-datacenter"></a>Publier des services Azure Stack Hub dans votre centre de données

Azure Stack Hub configure des adresses IP virtuelles pour ses rôles d’infrastructure. Ces adresses IP virtuelles sont allouées à partir du pool d’adresses IP publiques. Chaque adresse IP virtuelle est sécurisée à l’aide d’une liste de contrôle d’accès (ACL) dans la couche réseau à définition logicielle. Les listes ACL sont également utilisées dans les commutateurs physiques (TOR et BMC) pour renforcer la solution. Une entrée DNS est créée pour chaque point de terminaison dans la zone DNS externe spécifiée au moment du déploiement. Par exemple, le portail utilisateur se voit attribué l’entrée d’hôte DNS portal. *&lt;region>.&lt;fqdn>* .

Le diagramme architectural suivant montre les différentes couches réseau et les listes ACL :

![Diagramme montrant différentes couches réseau et listes de contrôle d’accès](media/azure-stack-integrate-endpoints/integrate-endpoints-01.svg)

## <a name="ports-and-urls"></a>Ports et URL

Pour rendre des services Azure Stack Hub (tels que les portails, Azure Resource Manager, DNS, etc.) disponibles pour des réseaux externes, vous devez autoriser le trafic entrant vers ces points de terminaison pour des URL, des ports et des protocoles spécifiques.

Dans un déploiement où un proxy transparent achemine par liaison montante les données à un serveur proxy traditionnel ou un pare-feu protège la solution, vous devez autoriser des URL et des ports spécifiques pour les communications [entrantes](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound) et [sortantes](azure-stack-integrate-endpoints.md#ports-and-urls-outbound). Cela comprend les ports et les URL pour l’identité, la marketplace, les correctifs et les mises à jour, l’inscription ainsi que les données d’utilisation.

L’interception du trafic SSL n’est [pas prise en charge](azure-stack-firewall.md#ssl-interception) et peut entraîner des échecs de service lors de l’accès aux points de terminaison. 

## <a name="ports-and-protocols-inbound"></a>Ports et protocoles (en entrée)

Un ensemble d’adresses IP virtuelles d’infrastructure est nécessaire pour la publication des points de terminaison Azure Stack Hub sur des réseaux externes. Le tableau *Point de terminaison (VIP)* affiche chaque point de terminaison, le port requis et le protocole. Consultez la documentation de déploiement spécifique au fournisseur de ressources pour les points de terminaison nécessitant des fournisseurs de ressources supplémentaires, comme le fournisseur de ressources SQL.

Les adresses IP virtuelles de l’infrastructure interne ne sont pas répertoriées car elles ne sont pas requises pour la publication d’Azure Stack Hub. Les adresses IP virtuelles d’utilisateur sont dynamiques et définies par les utilisateurs eux-mêmes, sans contrôle de la part de l’opérateur Azure Stack Hub.

> [!Note]  
> Le VPN IKEv2 est une solution VPN IPsec basée sur des normes qui utilise les ports UDP 500 et 4500 ainsi que le port TCP 50. Les pare-feux n’ouvrent pas toujours ces ports : un VPN IKEv2 peut donc ne pas être en mesure de traverser les proxys et les pare-feux.

Avec l’ajout de l’[hôte d’extension](azure-stack-extension-host-prepare.md), les ports de la plage 12495-30015 ne sont pas nécessaires.

|Point de terminaison (VIP)|Enregistrement A d’hôte DNS|Protocol|Ports|
|---------|---------|---------|---------|
|AD FS|Adfs. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portail (administrateur)|Adminportal. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure Resource Manager (administrateur)|Adminmanagement. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portail (utilisateur)|Portal. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Azure Resource Manager (utilisateur)|Management. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Graph|Graph. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Liste de révocation de certificat|Crl. *&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;. *&lt;region>.&lt;fqdn>*|TCP et UDP|53|
|Hébergement | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Key Vault (utilisateur)|&#42;.vault. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Key Vault (administrateur)|&#42;.adminvault. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|File d’attente de stockage|&#42;.queue. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Table de stockage|&#42;.table. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;.blob. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Fournisseur de ressources SQL|sqladapter.dbadapter. *&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Fournisseur de ressources MySQL|mysqladapter.dbadapter. *&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service|&#42;.appservice. *&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice. *&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice. *&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |ftp.appservice. *&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|Passerelles VPN|     |     |[Consultez le FAQ sur la passerelle VPN](/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Ports et URL (en sortie)

Azure Stack Hub prend en charge uniquement les serveurs proxy transparents. Dans un déploiement où un proxy transparent transfère les données vers un serveur proxy traditionnel, vous devez autoriser les URL et les ports du tableau suivant pour les communications sortantes.

L’interception du trafic SSL n’est [pas prise en charge](azure-stack-firewall.md#ssl-interception) et peut entraîner des échecs de service lors de l’accès aux points de terminaison. Le délai d’expiration maximal pris en charge pour communiquer avec les points de terminaison requis pour l’identité est de 60 secondes.

> [!Note]  
> Azure Stack Hub ne prend pas en charge l’utilisation d’ExpressRoute pour atteindre les services Azure répertoriés dans le tableau suivant, car ExpressRoute risque de ne pas pouvoir router le trafic vers tous les points de terminaison.

|Objectif|URL de destination|Protocol|Ports|Réseau source|
|---------|---------|---------|---------|---------|
|Identité|**Microsoft Azure**<br>login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>www.office.com<br>ManagementServiceUri = https:\//management.core.windows.net<br>ARMUri = https:\//management.azure.com<br>https:\//\*.msftauth.net<br>https:\//\*.msauth.net<br>https:\//\*.msocdn.com<br>**Azure Government**<br>https:\//login.microsoftonline.us/<br>https:\//graph.windows.net/<br>**Azure China 21Vianet**<br>https:\//login.chinacloudapi.cn/<br>https:\//graph.chinacloudapi.cn/<br>**Azure Allemagne**<br>https:\//login.microsoftonline.de/<br>https:\//graph.cloudapi.de/|HTTP<br>HTTPS|80<br>443|Adresse IP virtuelle publique - /27<br>Réseau d'infrastructure publique|
|Syndication de Place de marché|**Microsoft Azure**<br>https:\//management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://&#42;.azureedge.net<br>**Azure Government**<br>https:\//management.usgovcloudapi.net/<br>https://&#42;.blob.core.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https:\//management.chinacloudapi.cn/<br>http://&#42;.blob.core.chinacloudapi.cn|HTTPS|443|Adresse IP virtuelle publique - /27|
|Correctif et mise à jour|https://&#42;.azureedge.net<br>https:\//aka.ms/azurestackautomaticupdate|HTTPS|443|Adresse IP virtuelle publique - /27|
|Inscription|**Microsoft Azure**<br>https:\//management.azure.com<br>**Azure Government**<br>https:\//management.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https:\//management.chinacloudapi.cn|HTTPS|443|Adresse IP virtuelle publique - /27|
|Usage|**Microsoft Azure**<br>https://&#42;.trafficmanager.net<br>**Azure Government**<br>https://&#42;.usgovtrafficmanager.net<br>**Azure China 21Vianet**<br>https://&#42;.trafficmanager.cn|HTTPS|443|Adresse IP virtuelle publique - /27|
|Windows Defender|&#42;.wdcp.microsoft.com<br>&#42;.wdcpalt.microsoft.com<br>&#42;.wd.microsoft.com<br>&#42;.update.microsoft.com<br>&#42;.download.microsoft.com<br><br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|Adresse IP virtuelle publique - /27<br>Réseau d'infrastructure publique|
|NTP|(IP du serveur NTP fourni pour le déploiement)|UDP|123|Adresse IP virtuelle publique - /27|
|DNS|(IP du serveur DNS fourni pour le déploiement)|TCP<br>UDP|53|Adresse IP virtuelle publique - /27|
|SYSLOG|(IP du serveur SYSLOG fourni pour le déploiement)|TCP<br>UDP|6514<br>514|Adresse IP virtuelle publique - /27|
|CRL|URL (sous Points de distribution CRL sur votre certificat)<br>http://crl.microsoft.com/pki/crl/products<br>http://mscrl.microsoft.com/pki/mscorp<br>http://www.microsoft.com/pki/certs<br>http://www.microsoft.com/pki/mscorp<br>http://www.microsoft.com/pkiops/crl<br>http://www.microsoft.com/pkiops/certs<br>|HTTP|80|Adresse IP virtuelle publique - /27|
|LDAP|Forêt Active Directory fournie pour l'intégration Graph|TCP<br>UDP|389|Adresse IP virtuelle publique - /27|
|LDAP SSL|Forêt Active Directory fournie pour l'intégration Graph|TCP|636|Adresse IP virtuelle publique - /27|
|LDAP GC|Forêt Active Directory fournie pour l'intégration Graph|TCP|3268|Adresse IP virtuelle publique - /27|
|LDAP GC SSL|Forêt Active Directory fournie pour l'intégration Graph|TCP|3269|Adresse IP virtuelle publique - /27|
|AD FS|Point de terminaison de métadonnées AD FS fourni pour l'intégration AD FS|TCP|443|Adresse IP virtuelle publique - /27|
| Collecte des journaux de diagnostic | https://azsdiagprdlocalwestus02.blob.core.windows.net<br>https://azsdiagprdwestusfrontend.westus.cloudapp.azure.com<br>https://azsdiagprdwestusfrontend.westus.cloudapp.azure.com | HTTPS | 443 | Adresse IP virtuelle publique - /27 |
|     |     |     |     |     |

Les URL sortantes font l’objet d’un équilibrage de charge avec Azure Traffic Manager pour offrir la meilleure connectivité possible en fonction de l’emplacement géographique. Avec des URL faisant l’objet d’un équilibrage de charge, Microsoft peut mettre à jour et modifier les points de terminaison de back-end sans impact sur les clients. Microsoft ne partage pas la liste des adresses IP pour les URL qui font l’objet d’un équilibrage de charge. Utilisez un appareil qui prend en charge le filtrage par URL plutôt que par adresse IP.

Un DNS sortant est toujours nécessaire : ce qui change, c’est la source qui interroge le DNS externe et le type d’intégration d’identité choisi. Dans un scénario de déploiement connecté, la machine virtuelle de déploiement (DVM) qui se trouve sur le réseau BMC a besoin d’un accès sortant. Toutefois, après le déploiement, le service DNS passe à un composant interne qui enverra des requêtes via une adresse IP virtuelle publique. À ce stade, l’accès du DNS sortant par le biais du réseau BMC peut être supprimé, mais l’accès à ce serveur DNS par l’adresse IP virtuelle publique doit être maintenu faute de quoi l’authentification échouera.

## <a name="next-steps"></a>Étapes suivantes

[Exigences relatives à l’infrastructure de clés publiques d’Azure Stack Hub](azure-stack-pki-certs.md)
