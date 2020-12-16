---
title: Déploiement et configuration d’Azure Stack Hub renforcé pour le serveur d’administration HLH (hôte de cycle de vie du matériel) d’Azure Stack Hub | Microsoft Docs
description: Découvrez comment réussir un déploiement local d’Azure Stack Hub renforcé, de la planification au postdéploiement.
services: azure-stack
documentationcenter: ''
author: ashika789
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: justinha
ms.reviewer: asganesh
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 0d788e1a41ed512a1d39c7a3e3680d193fdaecf2
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941001"
---
# <a name="azure-stack-hub-ruggedized-deployment-overview"></a>Vue d’ensemble du déploiement d’Azure Stack Hub renforcé

Ce guide de déploiement décrit les étapes d’installation et de configuration d’Azure Stack Hub renforcé. 

Les objectifs de ce guide sont les suivants :

- fournir une liste de vérification de prédéploiement pour vérifier que toutes les conditions préalables ont été remplies avant l’installation des composants ;
- présenter les composants clés d’Azure Stack Hub renforcé ;
- décrire l’installation et la configuration des composants clés ;
- valider le déploiement du client.

Ce guide de déploiement est destiné à l’équipe de professionnels de terrain Microsoft, responsable du déploiement d’Azure Stack Hub renforcé sur le site d’un client.

L’expérience technique de la virtualisation, des serveurs, des systèmes d’exploitation, de la mise en réseau et des solutions de stockage est nécessaire pour bien comprendre le contenu de ce guide. L’ingénieur de déploiement doit connaitre Microsoft Windows Server 2019 avec Hyper-V, Azure Stack Hub, Azure et Microsoft PowerShell.

Ce guide se concentre sur le déploiement des composants de base de Microsoft Azure Stack Hub ainsi que sur les spécificités de la solution Azure Stack Hub renforcé. Ce guide n’explique pas les procédures de fonctionnement d’Azure Stack Hub et ne couvre pas toutes les fonctionnalités disponibles dans Azure Stack Hub. Pour plus d’informations, consultez le [Guide de l’opérateur Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/).

## <a name="introduction"></a>Introduction

Azure Stack Hub renforcé est une offre renforcée et déployable sur le terrain pour Microsoft Azure Stack Hub. Les composants de base, par exemple les serveurs et les commutateurs, sont contenus dans des boîtiers de transport appelés pods.

Un pod est un conteneur rack 4U dont les dimensions sont inférieures à celles d’un rack 4U normal. Il existe un pod de gestion et deux pods SU (unité d’échelle). Le pod de gestion comprend l’hôte HLH (hôte de cycle de vie du matériel), deux commutateurs ToR (top-of-rack) 25 GbE et un commutateur BMC (contrôleur de gestion de la carte de base).

Chaque pod SU contient deux serveurs R640 SU Azure Stack Hub renforcé. Un serveur R640 occupe l’espace d’un rack 2U dans le pod. Pendant le déploiement, les serveurs des pods SU sont connectés aux commutateurs BMC et ToR du pod de gestion.

## <a name="terminology"></a>Terminologie

Le tableau suivant répertorie certains des termes utilisés dans ce guide.

|Terme   | Définition |
|-------|------------|
|Hôte du cycle de vie du matériel (HLH)| HLH est le serveur physique utilisé pour le démarrage initial du déploiement, ainsi que la gestion du matériel en cours, le support et la sauvegarde de l’infrastructure Azure Stack Hub. HLH exécute Windows Server 2019 avec l’Expérience utilisateur et le rôle Hyper-V. Le serveur est utilisé pour héberger les outils de gestion de matériel, les outils de gestion de commutateur, Azure Stack Hub Partner Toolkit et la machine virtuelle de déploiement. |
|Machine virtuelle de déploiement (DVM)|  DVM est une machine virtuelle qui est créée sur le HLH pour la durée du déploiement du logiciel Azure Stack Hub. Le DVM exécute le moteur d’orchestration du logiciel Azure Stack Hub appelé Enterprise Cloud Engine (ECE) pour installer et configurer des logiciels d’infrastructure Azure Stack Hub sur tous les serveurs d’unités d’échelle Azure Stack Hub sur le réseau.|
|Azure Stack Hub Partner Toolkit|   Ensemble d’outils logiciels utilisés pour capturer des paramètres d’entrée spécifiques au client et initier l’installation et la configuration d’Azure Stack Hub. Il comprend la feuille de travail de déploiement, qui est un outil d’interface utilisateur graphique (GUI) utilisé pour capturer et stocker des paramètres configurables pour l’installation d’Azure Stack Hub. Il comprend également l’outil de génération de configuration réseau qui utilise les entrées de la feuille de calcul de déploiement pour produire des fichiers de configuration réseau pour tous les périphériques réseau physiques de la solution.|
|Package d’extension OEM  |Package de microprogrammes, pilotes de périphériques et outils de gestion du matériel dans un format spécialisé utilisé par Azure Stack Hub lors du déploiement et de la mise à jour initiaux.|
|Contrôleur iDRAC (Integrated Dell Remote Access Controller)|  Un contrôleur iDRAC avec Lifecycle Controller est un contrôleur de gestion de la carte de base intégré à chaque serveur R640 Azure Stack Hub renforcé. iDRAC fournit des fonctionnalités de gestion hors bande pour faciliter le déploiement, la mise à jour, la supervision et la maintenance des serveurs Azure Stack Hub.|
|Unité d'échelle |Composant principal d’Azure Stack Hub qui fournit des ressources de calcul et de stockage à l’infrastructure de structure et aux charges de travail Azure Stack Hub. Se compose de quatre serveurs R640 Azure Stack Hub renforcé (également appelés nœuds), et peut être mis à l’échelle de manière dynamique jusqu’à 16 nœuds.|
|Pod    |Dans le contexte d’Azure Stack Hub renforcé, un pod est un conteneur physique robuste conçu pour être transporté par deux personnes. Il contient des supports de montage en rack et des amortisseurs pour protéger le matériel Azure Stack Hub renforcé des contraintes physiques liées à l’environnement. Il comprend des panneaux avant et arrière de protection du boîtier de transport, qui peuvent être installées et scellées pour permettre le transport du matériel. Une solution complète en configuration minimale comprend trois pods.|


## <a name="deployment-overflow"></a>Dépassement du déploiement

De manière générale, le processus de déploiement d’Azure Stack Hub renforcé comprend les étapes suivantes.

1. Phase de planification :
   1. Planification de l’alimentation électrique et du refroidissement du centre de données.
   1. Planification de la configuration du réseau logique d’Azure Stack Hub.
   1. Planification de l’intégration du réseau du centre de données.
   1. Planification de l’intégration des identités et de la sécurité.
   1. Vérification des certificats PKI.
1. Phase de préparation :
   1. Déballage et collecte de l’inventaire.
   1. Connexion de l’alimentation et mise sous tension de la solution.
   1. Validation de l'intégrité du matériel physique.
1. Phase d’exécution :
   1. Configuration de l’hôte du cycle de vie du matériel.
   1. Configuration des commutateurs réseau.
   1. Intégration du réseau du centre de données.
   1. Configuration des paramètres matériels physiques.
   1. Déploiement de l’infrastructure de la structure Azure Stack Hub.
   1. Intégration des identités au centre de données.
   1. Installation des modules complémentaires pour les fonctionnalités étendues.
1. Phase de validation :
   1. Validation de l’intégrité après le déploiement.
   1. Inscription d’Azure Stack Hub auprès de Microsoft.
   1. Transfert des opérateurs Azure Stack Hub.
   
Chacun des sujets ci-dessus est expliqué en détail dans ce guide.
