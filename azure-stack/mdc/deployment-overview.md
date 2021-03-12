---
title: Présentation du déploiement MDC (Modular Data Center) et configuration du serveur d’administration de l’hôte de cycle de vie du matériel (HLH) Azure Stack Hub | Microsoft Docs
description: Découvrez comment réussir un déploiement local d’un Modular Data Center, de la planification au post-déploiement.
services: azure-stack
documentationcenter: ''
author: asganesh
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2021
ms.author: patricka
ms.reviewer: asganesh
ms.lastreviewed: 02/17/2021
ms.openlocfilehash: 60ff62282f9fadf3b2475cffe057f10a8bbe8b3b
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101839655"
---
# <a name="mdc-requirements-overview"></a>Présentation des exigences en lien avec un MDC

Ce guide décrit les exigences relatives à l’installation et à la configuration d’un Modular Data Center (MDC). 

Les objectifs de ce guide sont les suivants :

- fournir une liste de vérification de prédéploiement pour vérifier que toutes les conditions préalables ont été remplies avant l’installation des composants ;
- présenter les composants clés MDC ;
- valider le déploiement du client.

L’expérience technique de la virtualisation, des serveurs, des systèmes d’exploitation, de la mise en réseau et des solutions de stockage est nécessaire pour bien comprendre le contenu de ce guide. 

Ce guide se concentre sur le déploiement des composants de base de Microsoft Azure Stack Hub et les spécificités de la solution MDC. Ce guide n’explique pas les procédures de fonctionnement d’Azure Stack Hub et ne couvre pas toutes les fonctionnalités disponibles dans Azure Stack Hub. 

## <a name="introduction"></a>Introduction

MDC est une offre intégrée à Azure Stack Hub, empaquetée dans un conteneur de transport métallique standard de 40 mètres. Le conteneur comprend une unité de contrôle climatique, un système d’éclairage et un système d’alerte. Les principaux composants Azure Stack Hub sont installés sous la forme de pods.

## <a name="terminology"></a>Terminologie

Le tableau suivant répertorie certains des termes utilisés dans ce guide.

|Terme    |Définition |
|-------|-----------|
|Hôte du cycle de vie du matériel (HLH)|    HLH est le serveur physique utilisé pour le démarrage initial du déploiement, ainsi que la gestion du matériel en cours, le support et la sauvegarde de l’infrastructure Azure Stack Hub. HLH exécute Windows Server 2019 avec l’Expérience utilisateur et le rôle Hyper-V. Le serveur est utilisé pour héberger les outils de gestion de matériel, les outils de gestion de commutateur, Azure Stack Hub Partner Toolkit et la machine virtuelle de déploiement. |
|Machine virtuelle de déploiement (DVM)|    DVM est une machine virtuelle qui est créée sur le HLH pour la durée du déploiement du logiciel Azure Stack Hub. Le DVM exécute le moteur d’orchestration du logiciel Azure Stack Hub appelé Enterprise Cloud Engine (ECE) pour installer et configurer des logiciels d’infrastructure Azure Stack Hub sur tous les serveurs d’unités d’échelle Azure Stack Hub sur le réseau.|
|Azure Stack Hub Partner Toolkit|    Ensemble d’outils logiciels utilisés pour capturer des paramètres d’entrée spécifiques au client et initier l’installation et la configuration d’Azure Stack Hub. Il comprend la feuille de travail de déploiement, qui est un outil d’interface utilisateur graphique (GUI) utilisé pour capturer et stocker des paramètres configurables pour l’installation d’Azure Stack Hub. Il comprend également l’outil de génération de configuration réseau qui utilise les entrées de la feuille de calcul de déploiement pour produire des fichiers de configuration réseau pour tous les périphériques réseau physiques de la solution.|
|Package d’extension OEM    |Package de microprogrammes, pilotes de périphériques et outils de gestion du matériel dans un format spécialisé utilisé par Azure Stack Hub lors du déploiement et de la mise à jour initiaux.|
|Concentrateur de ports série    |Appareil physique installé dans chaque pod fournissant un accès réseau aux ports série des commutateurs réseau à des fins de déploiement et de gestion.|
|Unité d'échelle    |Composant principal d’Azure Stack Hub qui fournit des ressources de calcul et de stockage à l’infrastructure de structure et aux charges de travail Azure Stack Hub.|
|Stockage Isilon |    Composant Azure Stack Hub spécifique à la solution MDC. Isilon fournit un stockage Blob et de fichiers supplémentaire pour les charges de travail Azure Stack Hub. |
|Pod    |Dans le contexte de MDC, un pod est une unité logique indépendante constituée de deux racks physiques interconnectés.|

## <a name="deployment-workflow"></a>Workflow du déploiement

De manière générale, le processus de déploiement de MDC comprend les phases suivantes :

### <a name="planning-phase"></a>Phase de planification
1. Planification de la puissance du centre de données.
1. Planification de la configuration du réseau logique d’Azure Stack Hub.
1. Planification de l’[intégration du réseau du centre de données](../operator/azure-stack-network.md).
1. Planification de l’intégration de l’[identité](../operator/azure-stack-identity-overview.md).
1. Planification de l’intégration de la [sécurité](../operator/azure-stack-security-foundations.md).
1. Planification des [certificats PKI](../operator/azure-stack-pki-certs.md).

### <a name="preparation-phase"></a>Phase de préparation
1. Collecte d'inventaire.
1. Connexion de l’alimentation et mise sous tension de la solution.
1. Validation de l’intégrité du système HVAC.
1. Validation de l’intégrité du système de surveillance et d’alerte des incendies.
1. Validation de l'intégrité du matériel physique.

### <a name="execution-phase--separately-for-each-of-the-three-pods"></a>Phase d’exécution (séparément pour chacun des trois pods)
1. Configuration de l’hôte du cycle de vie du matériel.
1. Configuration des commutateurs réseau.
1. Intégration du réseau du centre de données.
1. Configuration des paramètres matériels physiques.
1. Configuration du stockage Isilon.
1. Déploiement de l’infrastructure de la structure Azure Stack Hub.
1. Intégration des identités au centre de données.
1. Installation des modules complémentaires pour les fonctionnalités étendues.

### <a name="validation-phase--separately-for-each-of-the-three-pods"></a>Phase de validation (séparément pour chacun des trois pods)
1. Validation de l’intégrité après le déploiement.
1. Inscription d’Azure Stack Hub auprès de Microsoft.
1. Transfert des clients Azure Stack Hub.
