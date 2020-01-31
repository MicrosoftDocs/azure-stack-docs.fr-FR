---
title: Qu'est-ce que le Kit de développement Azure Stack (ASDK) ? | Microsoft Docs
description: Découvrez le Kit de développement Azure Stack et son utilisation pour évaluer Azure Stack Hub.
author: justinha
ms.topic: overview
ms.date: 11/27/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: 7d3b4115ea34a185c5535c77cecdddf060ed84eb
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873292"
---
# <a name="what-is-the-azure-stack-development-kit-asdk"></a>Qu'est-ce que le Kit de développement Azure Stack (ASDK) ?
Les [systèmes intégrés Microsoft Azure Stack Hub](../operator/azure-stack-overview.md) ont une taille comprise entre 4 et 16 nœuds, et ils sont pris en charge conjointement par un fournisseur de matériel partenaire et Microsoft. Utilisez des systèmes intégrés Azure Stack Hub afin de permettre de nouveaux scénarios pour vos charges de travail de production. Si vous êtes opérateur Azure Stack Hub gérant l’infrastructure de systèmes intégrés et proposant des offres, consultez notre [documentation destinée à l’opérateur](/azure-stack/operator).

Le kit ASDK est un déploiement mononœud d’Azure Stack Hub que vous pouvez télécharger et utiliser **gratuitement**. Tous les composants ASDK sont installés sur des machines virtuelles s’exécutant sur un ordinateur hôte unique qui doit satisfaire ou dépasser la [configuration matérielle minimale requise](asdk-deploy-considerations.md#hardware). Le kit ASDK est destiné à fournir un environnement où vous pouvez évaluer Azure Stack Hub et développer des applications modernes en utilisant des API et des outils cohérents avec Azure dans un environnement *hors production*. 

> [!IMPORTANT]
> Le Kit ASDK n’est pas destiné à être utilisé ou pris en charge dans un environnement de production.

Étant donné que tous les composants ASDK sont déployés sur un ordinateur hôte unique, les ressources physiques disponibles sont limitées. Avec les déploiements ASDK, les machines virtuelles de l’infrastructure Azure Stack Hub et celles du locataire coexistent sur le même serveur. Cette configuration n’est pas destinée à la mise à l’échelle ou à l’évaluation des performances.

L’ASDK est conçu pour fournir une expérience de cloud hybride cohérente avec Azure pour :
- **Les administrateurs** (opérateurs Azure Stack Hub) : Le Kit ASDK est une ressource utile pour évaluer et découvrir les services Azure Stack Hub disponibles.
- **Les développeurs** : Le Kit ASDK peut être utilisé pour développer des applications hybrides ou modernes en local (environnements de développement/de test). Cette flexibilité permet de répéter l’expérience de développement avant ou pendant les déploiements de production Azure Stack Hub.

Regardez cette courte vidéo pour en savoir plus sur l’ASDK :

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-hub-differences"></a>Différences entre ASDK et les systèmes Azure Stack Hub à plusieurs nœuds
Les déploiements ASDK à nœud unique et les déploiements Azure Stack Hub à plusieurs nœuds présentent quelques différences importantes :

|Description|ASDK|Système Azure Stack Hub à plusieurs nœuds|
|-----|-----|-----|
|**Mettre à l'échelle**|Tous les composants sont installés sur un ordinateur serveur à nœud unique.|Peuvent comprendre entre 4 et 16 nœuds.|
|**Résilience**|La configuration à un seul nœud ne fournit pas une haute disponibilité|Les fonctionnalités de haute disponibilité sont prises en charge.|
|**Mise en réseau**|L’hôte ASDK route tout le trafic réseau ASDK. Il n’existe aucune exigence supplémentaire en matière de commutateur.|Une [infrastructure de routage réseau](../operator/azure-stack-network.md#network-infrastructure) plus complexe est nécessaire dans les déploiements à plusieurs nœuds°: commutateurs TOR (Top-Of-Rack), Baseboard Management Controller (BMC) et commutateurs limite (réseau de centre de données).|
|**Processus de correctif et mise à jour**|Pour passer à une nouvelle version du Kit ASDK, vous devez redéployer le Kit ASDK sur son ordinateur hôte.|Le processus de [correction et de mise à jour](../operator/azure-stack-updates.md) utilisé pour mettre à jour la version installée d’Azure Stack Hub.|
|**Support**|Forum MSDN Azure Stack. Le Support technique et Service clientèle de Microsoft (CSS) n’est *pas* disponible pour les environnements hors production.|[Forum MSDN Azure Stack](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) et support CSS complet.|
| | |

## <a name="learn-about-available-services"></a>Découvrir les services disponibles
En tant qu’opérateur Azure Stack Hub, vous devez savoir quels services vous pouvez rendre disponibles pour vos utilisateurs. Azure Stack Hub prend en charge un sous-ensemble de services Azure ; la liste des services pris en charge continuera d’évoluer au fil du temps.

### <a name="foundational-services"></a>Services fondamentaux
Par défaut, Azure Stack Hub comprend les « services fondamentaux » suivants quand vous déployez le Kit ASDK :
- Calcul
- Stockage
- Mise en réseau
- Key Vault

Avec ces services fondamentaux, vous pouvez proposer une solution IaaS (Infrastructure-as-a-Service) à vos utilisateurs avec une configuration minimale.

### <a name="additional-services"></a>Services supplémentaires
Actuellement, les services PaaS (Platform-as-a-Service) supplémentaires suivants sont pris en charge :
- App Service
- Azure Functions
- Bases de données SQL et MySQL

> [!NOTE]
> Ces services nécessitent une configuration supplémentaire avant que vous puissiez les mettre à disposition de vos utilisateurs et ne sont pas disponibles par défaut quand vous installez le Kit ASDK.

## <a name="service-roadmap"></a>Feuille de route des services
Azure Stack Hub continuera à prendre en charge de nouveaux services Azure. Pour plus d’informations sur les nouveautés Azure Stack Hub à venir, consultez la [feuille de route d’Azure Stack](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>Étapes suivantes
Pour démarrer l’évaluation d’Azure Stack Hub, vous devez d’abord [télécharger le dernier kit ASDK](asdk-download.md) et préparer l’ordinateur hôte ASDK. Vous pouvez ensuite installer le kit ASDK et vous connecter aux portails d’administration et utilisateur pour commencer à utiliser Azure Stack Hub.