---
title: Trafic réseau durant le déploiement d’Azure Stack Hub
description: Cet article décrit ce à quoi vous pouvez vous attendre concernant les processus réseau du déploiement d'Azure Stack Hub.
author: ihenkel
ms.topic: article
ms.date: 12/05/2019
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 12/05/2019
ms.openlocfilehash: bf7504ce1004fdc3c2a00d1b831e6f135218eb0a
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76879905"
---
# <a name="about-deployment-network-traffic"></a>À propos du trafic réseau de déploiement
Pour réussir le déploiement d'Azure Stack Hub, il est important de comprendre comment se déroule le trafic réseau pendant le processus de déploiement. Cet article décrit pas à pas le flux du trafic réseau tout au long du processus de déploiement afin que vous sachiez comment cela va se passer.

Cette illustration montre toutes les connexions et composants impliqués dans le processus de déploiement :

![Topologie de réseau du déploiement d'Azure Stack Hub](media/deployment-networking/figure1.png)

> [!NOTE]
> Cet article décrit les conditions requises pour un déploiement connecté. Pour en savoir plus sur les autres méthodes de déploiement, consultez [Modèles de connexion pour le déploiement d'Azure Stack Hub](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>Machine virtuelle de déploiement
La solution Azure Stack Hub inclut un groupe de serveurs qui sont utilisés pour héberger les composants Azure Stack Hub et un serveur supplémentaire appelé HLH (Hardware Lifecycle Host, Hôte du cycle de vie du matériel). Ce serveur est utilisé pour déployer et gérer le cycle de vie de votre solution et héberge la machine virtuelle de déploiement (DVM, Deployment VM) durant le déploiement.

Les fournisseurs de solutions Azure Stack Hub peuvent approvisionner des machines virtuelles de gestion supplémentaires. Demandez confirmation auprès du fournisseur de solutions avant d’apporter des modifications aux machines virtuelles de gestion qu’il provisionne.

## <a name="deployment-requirements"></a>Composants requis pour le déploiement
Avant le démarrage du déploiement, votre fabricant OEM peut avoir à valider un nombre minimal de composants requis pour garantir le bon déroulement du déploiement :

-   [Certificates](azure-stack-pki-certs.md)
-   [Abonnement Azure](azure-stack-validate-registration.md). Vous devrez peut-être vérifier votre abonnement.
-   Accès à Internet
-   DNS
-   NTP

> [!NOTE]
> Cet article se concentre sur les trois derniers composants requis. Pour plus d’informations sur les deux premières, consultez les liens ci-dessus.

## <a name="deployment-network-traffic"></a>Trafic réseau de déploiement
La machine DVM est configurée avec une adresse IP à partir du réseau BMC et nécessite un accès réseau à Internet. Même si les composants du réseau BMC n’exigent pas tous un routage externe ou un accès à Internet, certains composants spécifiques au fabricant OEM utilisant des adresses IP de ce réseau peuvent en avoir besoin.

Pendant le déploiement, la machine DVM s’authentifie auprès d’Azure Active Directory (Azure AD) à l’aide d’un compte Azure à partir de votre abonnement. Pour ce faire, la machine DVM nécessite un accès Internet à une liste [d’URL et de ports](azure-stack-integrate-endpoints.md) spécifiques. La machine DVM utilise un serveur DNS pour transférer les demandes DNS effectuées par les composants internes aux URL externes. Le serveur DNS interne transmet ces demandes à l’adresse du redirecteur DNS que vous fournissez au fabricant OEM avant le déploiement. Il en va de même pour le serveur NTP. Un serveur de temps fiable est nécessaire pour maintenir la cohérence et la synchronisation de l'heure de tous les composants Azure Stack Hub.

L’accès à Internet qu’exige la machine DVM durant le déploiement est sortant uniquement. Aucun appel entrant n’est effectué au cours du déploiement. Gardez à l'esprit qu'elle utilise son adresse IP en tant que source et qu'Azure Stack Hub ne prend pas en charge les configurations de proxy. Ainsi, vous pouvez être amené à fournir un proxy transparent ou une traduction NAT pour accéder à Internet. Durant le déploiement, certains composants internes commencent à accéder à Internet via le réseau externe en utilisant des adresses IP virtuelles publiques. Au terme du déploiement, toutes les communications entre Azure et Azure Stack Hub sont établies via le réseau externe à l'aide d'adresses IP virtuelles publiques.

Les configurations réseau sur les commutateurs Azure Stack Hub contiennent des listes de contrôle d'accès (ACL) qui limitent le trafic entre certaines sources et destinations réseau. La machine DVM est le seul composant sans restriction d’accès ; même le serveur HLH est limité. Vous pouvez contacter votre fabricant OEM pour en savoir plus sur les options de personnalisation permettant de faciliter la gestion et l’accès à partir de vos réseaux. En raison de ces listes ACL, il est important d’éviter de changer les adresses serveur NTP et DNS au moment du déploiement. Si vous le faites, vous devrez reconfigurer tous les commutateurs de la solution.

Une fois le déploiement terminé, les adresses de serveur DNS et NTP fournies continuent d’être utilisées par les composants du système via le SDN utilisant le réseau externe. Par exemple, si vous vérifiez les requêtes DNS une fois le déploiement terminé, la source passe de l’adresse IP de la machine DVM à une adresse IP virtuelle publique.

## <a name="next-steps"></a>Étapes suivantes
[Valider l’inscription auprès d’Azure](azure-stack-validate-registration.md)
