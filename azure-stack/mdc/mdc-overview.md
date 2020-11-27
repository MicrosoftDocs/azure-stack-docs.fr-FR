---
title: Vue d’ensemble de Modular Datacenter |
description: Azure Modular Datacenter est un centre de données portable, rapidement déployable et adapté aux opérations de combat à grande échelle dans des postes de commandement temporaires et fixes.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: overview
ms.date: 11/06/2020
ms.reviewer: prchint
ms.lastreviewed: 11/06/2020
ms.openlocfilehash: eabafd31d25a59cd43daa1a0779afed0a80f8354
ms.sourcegitcommit: 25b234330df4e753ed2dd480c208ec88cd90234c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94924314"
---
# <a name="modular-datacenter-overview"></a>Vue d’ensemble de Modular Datacenter

Azure Modular Datacenter (MDC) est basé sur Azure Stack Hub. MDC est un centre de données portable, rapidement déployable et adapté aux opérations de combat à grande échelle dans des postes de commandement temporaires et fixes.

Azure Stack Hub est une solution scalable horizontalement et verticalement qui fournit des fonctionnalités cloud, hybrides natives et multilocataires pour les services IaaS (Infrastructure as a service) et PaaS (Platform as a service) dans les environnements en périphérie. Azure Stack Hub prend en charge un large éventail de scénarios modulaires pour les forces expéditionnaires et les postes de commandement temporaires et fixes.

Azure Stack Hub est une appliance matérielle et logicielle intégrée qui est proposée à la vente dans un choix de capacités basées sur la mise à l’échelle via des unités d’échelle de nœuds. Azure Stack Hub est disponible avec des extensions qui incluent des configurations compatibles avec les processeurs graphiques (GPU) à usage général et un stockage externe extensible.

## <a name="use-mdc"></a>Utiliser MDC

Azure Stack remplit quatre principes fondamentaux qui sont cohérents avec les fonctionnalités d’Azure et les étendent pour des scénarios de périphérie modulaire.

### <a name="develop-and-deliver-apps-with-a-common-devops-model-including-api-symmetry-with-azure"></a>Développer et proposer des applications avec un modèle DevOps commun, notamment la symétrie d’API avec Azure

La cohérence entre Azure et Azure Stack signifie que les solutions sont développées une seule fois, déployées pour prendre en charge un large éventail de cas d’usage, puis sécurisées et maintenues avec un ensemble commun d’outils. Citons par exemple Azure Key Vault pour la gestion des clés et Azure Monitor pour la supervision et la gestion des ressources. Azure Stack est interopérable avec les données, les applications et les outils locaux pour DevOps et les opérations sécurisées, par exemple la gestion des clés.

### <a name="deliver-azure-services-on-premises"></a>Fournir des services Azure en local

Azure Stack s’exécute dans des environnements de communication défavorisés (contestés, encombrés ou refusés) et des environnements de communication robustes. Azure Stack ne dépend pas de la connectivité à Azure pour exécuter des applications de mission et permettre des opérations locales.

### <a name="use-integrated-hardware-and-software-delivery-experience"></a>Utiliser l’expérience intégrée de distribution de matériel et de logiciels

La périphérie modulaire demande une gamme de fonctionnalités qui fournissent des services de calcul et de stockage de référence, ainsi que des fonctionnalités avancées de machine learning, d’IA et d’analytique. La possibilité de se connecter à votre cloud sécurisé à partir de la périphérie modulaire quand cela est possible ou, inversement, de fonctionner indépendamment de celui-ci dans des milieux austères, est essentielle pour fournir un accès aux données nécessaire à la prise de décisions.

### <a name="keep-your-datacenter-secure-and-available-with-hybrid-cloud-security-operations"></a>Assurer la sécurité et la disponibilité de votre centre de données avec les opérations de sécurité cloud hybrides

La conception cloud native d’Azure Stack élimine les complexités opérationnelles des environnements de virtualisation traditionnels. Les administrateurs peuvent choisir quand corriger et orchestrer l’ensemble de l’opération pendant le processus d’installation et via la structure de gestion Azure Stack créée en mode natif dans le système.

L’administration assistée à distance par Microsoft ou une offre gérée par Microsoft dans le domaine des appareils Azure Stack peut être activée en fournissant aux utilisateurs l’accès approprié par le biais du contrôle d’accès en fonction du rôle (RBAC) pour effectuer des actions via le portail d’administration, le point de terminaison de gestion privilégié ou l’interface de ligne de commande (CLI). Cette fonctionnalité permet à Microsoft d’effectuer toutes les mises à jour correctives et autres activités d’administration et de supervision. Il est possible de mettre à jour et de mettre à niveau Azure Stack sur site via le portail d’administration sécurisé ou les commandes CLI qui appliquent les mises à jour de sécurité ainsi que les mises à jour des fonctionnalités IaaS et PaaS pour maintenir la parité commerciale avec Azure, le cas échéant, depuis des réseaux locaux ou distants.

## <a name="benefits-of-using-mdc"></a>Avantages de l'utilisation de MDC

MDC prend en charge un environnement cohérent avec Azure dans des environnements de communication défavorisés :

 - Des centres de données statiques, modulaires et rapidement déployables avec des services cloud Azure pour alimenter de grandes applications analytiques dans des centres d’opérations modulaires.
 - Les offres de périphérie modulaire d’Azure assurent la cohérence entre le cloud et la périphérie par le biais d’une approche singulière pour la prise en charge des primitives IaaS telles que les machines virtuelles, le stockage et les réseaux virtuels.
 - Prise en charge d’Azure Active Directory et de RBAC.
 - Interfaces d’administration courantes.
 - Symétrie d’API et prise en charge des outils open source DevOps Microsoft et tiers.
 - Gestion et supervision via Azure Log Analytics et Azure Security Center.
 - Apport de l’agilité du cloud computing dans votre environnement local et sa périphérie en activant un cloud hybride.<br>Vous pouvez :
     - Réutiliser le code et exécuter des applications cloud natives de façon cohérente dans Azure et dans vos environnements locaux.
     - Exécuter des charges de travail virtualisées traditionnelles avec des connexions facultatives aux services Azure.
     - Transférer des données dans le cloud ou les conserver dans votre centre de données souverain pour maintenir la conformité.
     - Exécuter des charges de travail de machine learning, conteneurisées ou virtualisées dotées de performances matérielles accélérées, le tout à la périphérie intelligente.

## <a name="next-steps"></a>Étapes suivantes

[Planification de la capacité Azure Stack Hub](../operator/azure-stack-capacity-planning-overview.md)
