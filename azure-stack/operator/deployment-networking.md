---
title: Trafic réseau du déploiement d’Azure Stack | Microsoft Docs
description: Cet article décrit ce à quoi vous pouvez vous attendre concernant les processus réseau du déploiement d’Azure Stack.
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
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 08/30/2018
ms.openlocfilehash: 20ec9e6ea8831e6667a282074ba28b16da3180f9
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618986"
---
# <a name="about-deployment-network-traffic"></a>À propos du trafic réseau de déploiement
Pour que le déploiement d’Azure Stack fonctionne, vous devez comprendre le flux du trafic réseau inhérent. Cet article décrit le trafic réseau attendu pendant le processus de déploiement pour que vous compreniez ce à quoi vous pouvez vous attendre.

Cette illustration montre toutes les connexions et composants impliqués dans le processus de déploiement :

![Topologie de réseau du déploiement d’Azure Stack](media/deployment-networking/figure1.png)

> [!NOTE]
> Cet article décrit les composants requis d’un déploiement connecté ; pour en savoir plus sur les autres méthodes de déploiement, consultez [Modèles de connexion pour le déploiement d’Azure Stack](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>Machine virtuelle de déploiement
La solution Azure Stack inclut un groupe de serveurs qui sont utilisés pour héberger les composants Azure Stack et un serveur supplémentaire appelé HLH (Hardware Lifecycle Host, Hôte du cycle de vie du matériel). Ce serveur est utilisé pour déployer et gérer le cycle de vie de votre solution et héberge la machine virtuelle de déploiement (DVM, Deployment VM) durant le déploiement.

## <a name="deployment-requirements"></a>Composants requis pour le déploiement
Avant le démarrage du déploiement, votre fabricant OEM peut valider un nombre minimal de composants requis pour garantir le bon déroulement du déploiement. Il est important de bien les comprendre pour préparer l’environnement et assurer la réussite de la validation. Les voici :

-   [Certificates](azure-stack-pki-certs.md)
-   [Abonnement Azure](https://azure.microsoft.com/free/?b=17.06)
-   Accès à Internet
-   DNS
-   NTP

> [!NOTE]
> Cet article se concentre sur les trois derniers composants requis. Pour plus d’informations sur les deux premières, consultez les liens ci-dessus.

## <a name="deployment-network-traffic"></a>Trafic réseau de déploiement
La machine DVM est configurée avec une adresse IP à partir du réseau BMC et nécessite un accès réseau à Internet. Même si les composants du réseau BMC n’exigent pas tous un routage externe ou un accès à Internet, certains composants spécifiques au fabricant OEM utilisant des adresses IP de ce réseau peuvent en avoir besoin.

Pendant le déploiement, la machine DVM s’authentifie auprès d’Azure Active Directory (Azure AD) à l’aide d’un compte Azure à partir de votre abonnement. Pour ce faire, la machine DVM nécessite un accès Internet à une liste d’URL et de ports spécifiques. Vous trouverez la liste complète dans la documentation [Publier des points de terminaison](azure-stack-integrate-endpoints.md). La machine DVM utilise un serveur DNS pour transférer les demandes DNS effectuées par les composants internes aux URL externes. Le serveur DNS interne transmet ces demandes à l’adresse du redirecteur DNS que vous fournissez au fabricant OEM avant le déploiement. Il en va de même pour le serveur NTP ; un serveur de temps fiable est nécessaire pour maintenir la cohérence et la synchronisation de l’heure de tous les composants Azure Stack.

L’accès à Internet qu’exige la machine DVM durant le déploiement est sortant uniquement. Aucun appel entrant n’est effectué au cours du déploiement. Gardez à l’esprit qu’elle utilise son adresse IP en tant que source et qu’Azure Stack ne prend pas en charge les configurations de proxy. Ainsi, vous pouvez être amené à fournir un proxy transparent ou une traduction NAT pour accéder à Internet. Durant le déploiement, certains composants internes commencent à accéder à Internet via le réseau externe en utilisant des adresses IP virtuelles publiques. Une fois le déploiement terminé, toutes les communications entre Azure et Azure Stack sont effectuées via le réseau externe à l’aide d’adresses IP virtuelles publiques.

Les configurations réseau sur les commutateurs Azure Stack contiennent des listes de contrôle d’accès (ACL) qui limitent le trafic entre certaines sources et destinations réseau. La machine DVM est le seul composant sans restriction d’accès ; même le serveur HLH est très limité. Vous pouvez contacter votre fabricant OEM pour en savoir plus sur les options de personnalisation permettant de faciliter la gestion et l’accès à partir de vos réseaux. En raison de ces listes ACL, il est important d’éviter de changer les adresses serveur NTP et DNS au moment du déploiement. Si vous le faites, vous devrez reconfigurer tous les commutateurs de la solution.

Une fois le déploiement terminé, les adresses serveur DNS et NTP fournies continuent d’être utilisées directement par les composants du système. Par exemple, si vous vérifiez les demandes DNS une fois le déploiement terminé, la source passe de l’adresse IP de la machine DVM à une adresse de la plage du réseau externe.

Une fois le déploiement terminé, les adresses de serveur DNS et NTP fournies continuent d’être utilisées par les composants du système via le SDN utilisant le réseau externe. Par exemple, si vous vérifiez les requêtes DNS une fois le déploiement terminé, la source passe de l’adresse IP de la machine DVM à une adresse IP virtuelle publique.

## <a name="next-steps"></a>Étapes suivantes
[Valider l’inscription auprès d’Azure](azure-stack-validate-registration.md)
