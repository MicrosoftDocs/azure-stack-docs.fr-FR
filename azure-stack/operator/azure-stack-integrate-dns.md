---
title: Intégration des services DNS Azure Stack Hub au DNS du centre de données
description: Découvrez comment intégrer les services DNS Azure Stack Hub au DNS de votre centre de données.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 1/22/2020
ms.author: inhenkel
ms.reviewer: wfayed
ms.lastreviewed: 08/21/2019
ms.openlocfilehash: 91d65a59d8db50162f5cf6c99f8d3ab1b5aeba86
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77699658"
---
# <a name="azure-stack-hub-datacenter-dns-integration"></a>Intégration des services DNS Azure Stack Hub au DNS du centre de données

Pour pouvoir accéder aux points de terminaison Azure Stack Hub (tels que **portal**, **adminportal**, **management** et **adminmanagement**) en dehors d’Azure Stack Hub, vous devez intégrer les services DNS Azure Stack Hub aux serveurs DNS qui hébergent les zones DNS que vous souhaitez utiliser dans Azure Stack Hub.

## <a name="azure-stack-hub-dns-namespace"></a>Espace de noms DNS Azure Stack Hub

Vous devez fournir certaines informations importantes relatives à DNS quand vous déployez Azure Stack Hub.


|Champ  |Description  |Exemple|
|---------|---------|---------|
|Région|Emplacement géographique de votre déploiement d’Azure Stack Hub.|`east`|
|Nom du domaine externe|Nom de la zone à utiliser pour votre déploiement d’Azure Stack Hub.|`cloud.fabrikam.com`|
|Nom de domaine interne|Nom de la zone interne utilisée pour les services d’infrastructure dans Azure Stack Hub. Elle est intégrée au service d’annuaire et privée (inaccessible en dehors du déploiement d’Azure Stack Hub).|`azurestack.local`|
|Redirecteurs DNS|Serveurs DNS qui sont utilisés pour transférer les requêtes DNS, les zones DNS et les enregistrements qui sont hébergés en dehors d’Azure Stack Hub (sur l’intranet d’entreprise ou sur l’Internet public). Vous pouvez modifier la valeur de Redirecteur DNS avec la cmdlet [**Set-AzSDnsForwarder**](#editing-dns-forwarder-ips) après le déploiement. 
|Préfixe de nommage (facultatif)|Préfixe de nommage dont vous souhaitez doter les noms de machine des instances du rôle d’infrastructure d’Azure Stack Hub.  S’il n’est pas fourni, la valeur par défaut est `azs`.|`azs`|

Le nom de domaine complet (FQDN) de votre déploiement d’Azure Stack Hub et des points de terminaison est la combinaison du paramètre de la région et du paramètre du nom de domaine externe. D’après les valeurs des exemples dans le tableau précédent, le nom de domaine complet pour ce déploiement d’Azure Stack Hub serait le suivant :

`east.cloud.fabrikam.com`

Ainsi, certains des points de terminaison pour ce déploiement ressembleraient, par exemple, aux URL suivantes :

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Afin d’utiliser cet exemple d’espace de noms DNS pour un déploiement d’Azure Stack Hub, les conditions suivantes sont requises :

- La zone `fabrikam.com` est inscrite auprès d’un bureau d’enregistrement de domaine et/ou d’un serveur DNS d’entreprise interne, selon vos exigences en termes de résolution de noms.
- Le domaine enfant `cloud.fabrikam.com` existe sous la zone `fabrikam.com`.
- Les serveurs DNS qui hébergent les zones `fabrikam.com` et `cloud.fabrikam.com` sont accessibles depuis le déploiement d’Azure Stack Hub.

Afin de résoudre les noms DNS pour les points de terminaison Azure Stack Hub et les instances en dehors d’Azure Stack Hub, vous devez intégrer les serveurs DNS qui hébergent la zone DNS externe pour Azure Stack Hub aux serveurs DNS qui hébergent la zone parent que vous souhaitez utiliser.

### <a name="dns-name-labels"></a>Étiquettes de nom DNS

Azure Stack Hub prend en charge l’ajout d’une étiquette de nom DNS à une adresse IP publique pour permettre la résolution de noms pour les adresses IP publiques. Les étiquettes DNS permettent aux utilisateurs d’accéder aux applications et services hébergés dans Azure Stack Hub par nom. L’étiquette de nom DNS utilise un espace de noms légèrement différent que les points de terminaison d’infrastructure. Suivant l’exemple d’espace de noms précédent, l’espace de noms pour les étiquettes de nom DNS s’affiche comme suit :

`*.east.cloudapp.cloud.fabrikam.com`

Par conséquent, si un client indique une valeur **Myapp** dans le champ d’étiquette de nom DNS d’une ressource d’adresse IP publique, il crée un enregistrement A pour **myapp** dans la zone **east.cloudapp.cloud.fabrikam.com**  sur le serveur DNS externe Azure Stack Hub. Le nom de domaine complet qui en résulte apparaît comme suit :

`myapp.east.cloudapp.cloud.fabrikam.com`

Si vous voulez tirer parti de cette fonctionnalité et utiliser cet espace de noms, vous devez intégrer les serveurs DNS qui hébergent la zone DNS externe pour Azure Stack Hub aux serveurs DNS qui hébergent la zone parente que vous souhaitez utiliser. Comme il s’agit d’un espace de noms autre que celui pour les points de terminaison de service Azure Stack Hub, vous devez créer une délégation supplémentaire ou une règle de transfert conditionnelle.

Pour plus d’informations sur le fonctionnement de l’étiquette de nom DNS, consultez [Utilisation de DNS dans Azure Stack Hub](../user/azure-stack-dns.md).

## <a name="resolution-and-delegation"></a>Résolution et délégation

Deux types de serveur DNS sont disponibles :

- Un serveur DNS faisant autorité héberge les zones DNS. Il répond aux requêtes DNS pour les enregistrements de ces zones uniquement.
- Un serveur DNS récursif n’héberge pas de zones DNS. Il répond à toutes les requêtes DNS, en appelant des serveurs DNS faisant autorité pour rassembler les données dont il a besoin.

Azure Stack Hub comprend à la fois des serveurs DNS faisant autorité et des serveurs DNS récursifs. Les serveurs récursifs sont utilisés pour résoudre les noms de tous les éléments à l’exception de la zone privée interne et de la zone DNS publique externe pour ce déploiement d’Azure Stack Hub.

![Architecture DNS d’Azure Stack Hub](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack-hub"></a>Résolution des noms DNS externes à partir d’Azure Stack Hub

Afin de résoudre les noms DNS pour les points de terminaison en dehors d’Azure Stack Hub (par exemple : www\.bing.com), vous devez fournir des serveurs DNS qu’Azure Stack Hub peut utiliser pour transférer les requêtes DNS pour lesquelles Azure Stack Hub ne fait pas autorité. Pour le déploiement, les serveurs DNS vers lesquels Azure Stack Hub transfère les requêtes sont requis dans la feuille de calcul de déploiement (dans le champ du redirecteur DNS). Indiquez au moins deux serveurs dans ce champ à des fins de tolérance de panne. En l’absence de ces valeurs, le déploiement d’Azure Stack Hub échoue. Vous pouvez modifier les valeurs de Redirecteur DNS avec la cmdlet [**Set-AzSDnsForwarder**](#editing-dns-forwarder-ips) après le déploiement. 



### <a name="configure-conditional-dns-forwarding"></a>Configurer la redirection DNS conditionnelle

> [!IMPORTANT]
> Cela s’applique uniquement à un déploiement AD FS.

Pour activer la résolution de noms de votre infrastructure DNS existante, configurez le transfert conditionnel.

Pour ajouter un redirecteur conditionnel, vous devez utiliser le point de terminaison privilégié.

Pour cette procédure, utilisez un ordinateur de votre réseau de centre de données qui peut communiquer avec le point de terminaison privilégié dans Azure Stack Hub.

1. Ouvrez une session Windows PowerShell avec élévation de privilèges (exécuter en tant qu’administrateur) et connectez-vous à l’adresse IP du point de terminaison privilégié. Utilisez les informations d’identification pour l’authentification de l’administrateur du cloud.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. Une fois connecté au point de terminaison privilégié, exécutez la commande PowerShell suivante. Remplacez les exemples de valeur fournis par votre nom de domaine et les adresses IP des serveurs DNS que vous souhaitez utiliser.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-hub-dns-names-from-outside-azure-stack-hub"></a>Résolution des noms DNS Azure Stack Hub en dehors d’Azure Stack Hub
Ce sont les serveurs faisant autorité qui hébergent les informations des zones DNS externes et toutes les zones créées par l’utilisateur. Effectuez une intégration à ces serveurs afin que la délégation de zone ou le transfert conditionnel puisse résoudre les noms DNS Azure Stack Hub en dehors d’Azure Stack Hub.

## <a name="get-dns-server-external-endpoint-information"></a>Obtenir les informations de point de terminaison externe des serveurs DNS

Pour intégrer votre déploiement d’Azure Stack Hub à votre infrastructure DNS, vous avez besoin des informations suivantes :

- Noms de domaine complets des serveurs DNS
- Adresses IP des serveurs DNS

Les noms de domaine complets des serveurs DNS Azure Stack Hub ont le format suivant :

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

D’après les exemples de valeur, les noms de domaine complets des serveurs DNS sont les suivants :

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Ces informations sont également créées à la fin de tous les déploiements d’Azure Stack Hub dans un fichier nommé `AzureStackStampInformation.json`. Ce fichier se trouve dans le dossier `C:\CloudDeployment\logs` de la machine virtuelle de déploiement. Si vous n’êtes pas sûr des valeurs qui ont été utilisées pour votre déploiement d’Azure Stack Hub, vous pouvez les y retrouver.

Si la machine virtuelle de déploiement n’est plus disponible ou n’est pas accessible, vous pouvez obtenir les valeurs en vous connectant au point de terminaison privilégié, puis en exécutant l’applet de commande PowerShell `Get-AzureStackStampInformation`. Pour plus d’informations, voir le [point de terminaison privilégié](azure-stack-privileged-endpoint.md).

## <a name="setting-up-conditional-forwarding-to-azure-stack-hub"></a>Configuration du transfert conditionnel vers Azure Stack Hub

La façon la plus simple et la plus sûre d’intégrer Azure Stack Hub à votre infrastructure DNS consiste à effectuer un transfert conditionnel de la zone à partir du serveur qui héberge la zone parente. Nous vous recommandons cette approche si vous contrôlez directement les serveurs DNS qui hébergent la zone parente pour votre espace de noms DNS externe Azure Stack Hub.

Si vous ne savez pas comment effectuer une redirection conditionnelle avec DNS, consultez l’article TechNet suivant : [Assigner un redirecteur conditionnel pour un nom de domaine](https://technet.microsoft.com/library/cc794735), ou reportez-vous à la documentation spécifique à votre solution DNS.

Dans les scénarios où vous avez spécifié votre zone DNS Azure Stack Hub externe de telle façon qu’elle s’apparente à un domaine enfant de votre nom de domaine d’entreprise, vous ne pouvez pas utiliser le transfert conditionnel. Vous devez configurer la délégation DNS.

Exemple :

- Nom de domaine DNS d’entreprise : `contoso.com`
- Nom de domaine DNS externe Azure Stack Hub : `azurestack.contoso.com`

## <a name="editing-dns-forwarder-ips"></a>Modifier les adresses IP du redirecteur DNS

Les adresses IP du redirecteur DNS sont définies pendant le déploiement d’Azure Stack Hub. Cependant, si les adresses IP du redirecteur doivent être modifiées pour une quelconque raison, vous pouvez le faire en vous connectant au point de terminaison privilégié et en exécutant les cmdlets PowerShell `Get-AzSDnsForwarder` et `Set-AzSDnsForwarder [[-IPAddress] <IPAddress[]>]`. Pour plus d’informations, voir le [point de terminaison privilégié](azure-stack-privileged-endpoint.md).

## <a name="delegating-the-external-dns-zone-to-azure-stack-hub"></a>Délégation de la zone DNS externe à Azure Stack Hub

Pour permettre la résolution des noms DNS en dehors d’un déploiement d’Azure Stack Hub, vous devez configurer la délégation DNS.

Chaque bureau d’enregistrement a ses propres outils de gestion DNS pour modifier les enregistrements de serveur de noms pour un domaine. Dans la page de gestion du bureau d’enregistrement DNS, modifiez les enregistrements NS et remplacez les enregistrements NS pour la zone par ceux créés dans Azure Stack Hub.

La plupart des bureaux d’enregistrement DNS requièrent que vous fournissiez au minimum deux serveurs DNS pour effectuer la délégation.

## <a name="next-steps"></a>Étapes suivantes

[Intégration du pare-feu](azure-stack-firewall.md)
