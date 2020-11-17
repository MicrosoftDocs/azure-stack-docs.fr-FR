---
title: Vue d’ensemble de Modular Data Center |
description: Modular Data Center est un centre de données portable, rapidement déployable, adapté à la prise en charge d’opérations de combat à grande échelle dans des postes de commandement temporaires et fixes.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: overview
ms.date: 11/06/2020
ms.reviewer: prchint
ms.lastreviewed: 11/06/2020
ms.openlocfilehash: eaa84fb673863935dc4778f34a6fce592590a7a8
ms.sourcegitcommit: ce864e1d86ad05a03fe896721dea8f0cce92085f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94383401"
---
# <a name="modular-data-center-overview"></a>Vue d’ensemble de Modular Data Center 

MDC (Modular Data Center) est basé sur Azure Stack Hub. MDC est un centre de données portable, rapidement déployable, adapté à la prise en charge d’opérations de combat à grande échelle dans des postes de commandement temporaires et fixes.

Azure Stack Hub est une solution scalable horizontalement et verticalement qui fournit des fonctionnalités cloud hybrides natives et à plusieurs locataires pour les services IaaS et PaaS aux environnements en périphérie et prend en charge un large éventail de scénarios modulaires pour les postes de commandement temporaires et fixes ainsi que les corps expéditionnaires. Azure Stack Hub est une appliance matérielle et logicielle intégrée que vous trouvez sur le marché dans une large gamme de capacités basées sur la mise à l’échelle par l’intermédiaire d’unités d’échelle de nœuds et qui est disponible avec les extensions, notamment les configurations compatibles avec un processeur graphique (GPU, Graphics Processing Unit) universel et le stockage externe extensible.

## <a name="how-you-can-use-the-mdc"></a>Utilisation de MDC

Azure Stack remplit quatre principes fondamentaux qui sont cohérents avec les fonctionnalités d’Azure et les étendent pour des scénarios de périphérie modulaire. 

### <a name="develop-and-deliver-apps-with-a-common-devops-model-including-api-symmetry-with-azure"></a>Développer et proposer des applications avec un modèle DevOps commun, notamment la symétrie d’API avec Azure

La cohérence entre Azure et Azure Stack signifie que les solutions sont développées une seule fois, déployées pour prendre en charge un large éventail de cas d’usage, puis sécurisées et soutenues à l’aide d’un ensemble commun d’outils comme Azure Key Vault pour la gestion des clés et Azure Monitor pour la supervision et la gestion des ressources. Azure Stack est interopérable avec les données, les applications et les outils locaux pour DevOps et les opérations sécurisées, par exemple la gestion des clés.

### <a name="deliver-azure-services-on-premises"></a>Fournir des services Azure en local

Azure Stack s’exécute dans des environnements de communication défavorisés (contestés, encombrés ou refusés) ainsi que dans des environnements de communication robustes et ne dépend pas de la connectivité à Azure pour exécuter des applications de missions et activer des opérations locales. 

### <a name="use-integrated-hardware-and-software-delivery-experience"></a>Utiliser l’expérience intégrée de distribution de matériel et de logiciels

La périphérie modulaire requiert une gamme de fonctionnalités qui fournissent non seulement des services de calcul et de stockage de base de référence, mais également des fonctionnalités avancées pour Machine Learning, l’intelligence artificielle et l’analytique. La possibilité de se connecter à votre cloud sécurisé à partir de la périphérie modulaire quand cela est possible ou, inversement, de fonctionner indépendamment de celui-ci dans des milieux austères est essentielle pour fournir un accès aux données requis pour prendre des décisions.

### <a name="keep-your-datacenter-secure-and-available-with-hybrid-cloud-security-operations"></a>Assurer la sécurité et la disponibilité de votre centre de données avec les opérations de sécurité cloud hybrides

La conception native cloud d’Azure Stack supprime la complexité opérationnelle des environnements de virtualisation traditionnels en permettant aux administrateurs de choisir le moment auquel appliquer les correctifs et en orchestrant l’ensemble de l’opération via le processus d’installation et la structure de gestion Azure Stack générée en mode natif dans le système.

L’administration assistée à distance par Microsoft ou une offre gérée par Microsoft dans le domaine des appareils Azure Stack peut être activée en fournissant aux utilisateurs l’accès approprié par le biais de RBAC pour effectuer des actions via le portail d’administration, le point de terminaison de gestion privilégié ou l’interface de ligne de commande. Cela permet à Microsoft d’effectuer toutes les mises à jour correctives et autres activités d’administration et de supervision. Il est possible de mettre à jour et de mettre à niveau Azure Stack sur site via le portail d’administration sécurisé ou les commandes de l’interface de ligne de commande (CLI) qui appliquent les mises à jour de sécurité ainsi que les mises à jour des fonctionnalités IaaS et PaaS pour maintenir la parité commerciale avec Azure, le cas échéant, depuis des réseaux locaux ou distants. 

## <a name="benefits-of-using-the-mdc"></a>Avantages de l’utilisation de MDC

MDC prend en charge un environnement cohérent avec Azure dans des environnements de communication défavorisés :
 - Des centres de données statiques, modulaires et rapidement déployables avec Azure Cloud Services pour alimenter des applications analytiques volumineuses dans des centres d’opérations modulaires (TOC).
 - Les offres de périphérie modulaire d’Azure assurent la cohérence entre le cloud et la périphérie par le biais d’une approche singulière pour la prise en charge des primitives IaaS telles que les machines virtuelles, le stockage et les réseaux virtuels.
 - Prise en charge d’Azure Active Directory et du contrôle d’accès en fonction du rôle (RBAC)
 - Interfaces d’administration courantes
 - Symétrie d’API et prise en charge des outils DevOps Microsoft, tiers et open source
 - Gestion et supervision via Azure Log Analytics et Azure Security Center
 - Mise en place de l’agilité du cloud computing dans votre environnement local et sa périphérie en activant un cloud hybride.<br>Vous pouvez :
     - Réutiliser le code et exécuter des applications cloud natives de façon cohérente dans Azure et dans vos environnements locaux.
     - Exécuter des charges de travail virtualisées traditionnelles avec des connexions facultatives aux services Azure.
     - Transférer des données dans le cloud ou les conserver dans votre centre de données souverain pour maintenir la conformité.
     - Exécuter des charges de travail d’apprentissage automatique accélérée par le matériel, en conteneur ou virtualisées, le tout à la périphérie intelligente.

## <a name="next-steps"></a>Étapes suivantes

[Planification de la capacité Azure Stack Hub](../operator/azure-stack-capacity-planning-overview.md)
