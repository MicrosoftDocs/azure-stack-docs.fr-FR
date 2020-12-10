---
title: Parcours utilisateur de l’achat au post-déploiement Azure Stack Hub | Microsoft Docs
description: Découvrez comment réussir un déploiement local d’Azure Modular Data Center (MDC), de la planification au post-déploiement.
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
ms.date: 11/04/2020
ms.author: justinha
ms.reviewer: asganesh
ms.lastreviewed: 11/04/2020
ms.openlocfilehash: 2d65f96cb7e64b22949150a246326d620ea66f56
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96615503"
---
# <a name="modular-datacenter-integration-overview"></a>Vue d’ensemble de l’intégration de Modular Data Center

Cet article décrit le processus complet d’intégration d’Azure Modular Data Center, depuis l’achat jusqu’au post-déploiement. L’intégration est un projet collaboratif entre le client et Microsoft. Les sections suivantes couvrent différentes phases de la chronologie du projet et des étapes spécifiques pour les membres du projet.

## <a name="introduction"></a>Introduction

La table suivante répertorie ce que vous pouvez attendre au cours des différentes phases du déploiement.

| Participant |Traitement des commandes |Prédéploiement |Intégration, validation, transport |Déploiement local |Après le déploiement |
|---|---------------|---------------|-----------------------------------|--------------------|----------------|
|Microsoft  | Signal de livraison aux États-Unis.    |Fournir les outils et documentation nécessaires pour recueillir les exigences de centre de données. |- Valider les artefacts de configuration et vérifier les résultats de validation.<br>- Vérifier que le matériel est livré.    |- Assembler le matériel.<br>- Intégration réseau.<br>- Déploiement d’Azure Stack Hub.<br>- Livraison au client.    |Inscription et syndication de la Place de marché Azure Stack Hub.|
|Client   |Signal d’achat.   |- Renseigner les détails réseau dans la feuille de travail de déploiement.<br>- Collecter les certificats.<br>- Obtenir les comptes Azure Active Directory (Azure AD).<br>- Exécuter tous les outils de validation fournis.   |Vérifier que le site répond aux prérequis de réseau, d’alimentation et de refroidissement.   |- Être prêt au niveau des artefacts de configuration de déploiement.<br>- Vérifier que l’ingénieur réseau du client est disponible.   |     |


## <a name="order-process"></a>Traitement des commandes

Votre organisation utilise Microsoft pour passer une commande pour un nombre de systèmes alloués. Une fois votre commande passée, Microsoft livre MDC à votre adresse aux États-Unis. Microsoft s’assure que toutes les exigences de la chaîne d’approvisionnement sécurisée sont respectées.

## <a name="hardware-delivery"></a>Livraison de matériel

Microsoft collaborera avec vous pour s’assurer que tous le matériel nécessaire arrive à l’adresse aux États-Unis dans le temps imparti.

Il est *essentiel* que toutes les données prérequises soient verrouillées et disponibles *avant que l’ingénieur arrive pour déployer la solution.*

- La feuille de travail de déploiement contient toutes les données renseignées.
- Tous les certificats doivent être validés et prêts.
- Le nom de région doit être choisi.
- Tous les paramètres d’intégration réseau sont finalisés.

>[!Tip]
>Si l’une de ces informations a changé, veillez à utiliser votre organisation interne pour vous assurer que les informations sont mises à jour avant l’arrivée de l’ingénieur de déploiement sur site. La mise à jour de vos informations empêchera tout retard dans le processus de déploiement.

## <a name="predeployment"></a>Prédéploiement

Vous déterminez la façon d’intégrer Azure Stack Hub à votre centre de données. Microsoft a publié une [feuille de calcul de déploiement](../operator/azure-stack-deployment-worksheet.md) qui vous guide tout au long de la collecte de toutes les informations nécessaires pour une intégration correcte dans votre centre de données. De plus, un certain ensemble de certificats est nécessaire au moment du déploiement. Pour vous aider à obtenir ces certificats, Microsoft met à votre disposition [Azure Stack Hub Readiness Checker](../operator/azure-stack-validation-report.md). Cet outil vous permet de créer des demandes de signature de certificat (CSR) à fournir à votre autorité de certification interne.

>[!Important]
>Toutes les composants requis sont validés afin d’éviter les retards de déploiement. La vérification des prérequis peut prendre du temps. Elle peut nécessiter une coordination et une collecte de données auprès de différents services de votre organisation.

Vous allez choisir les éléments suivants :

- **Fournisseur d’identité et modèle de connexion Azure Stack Hub.** Vous pouvez choisir de déployer Azure Stack Hub [connecté à Internet (et à Azure)](../operator/azure-stack-connected-deployment.md) ou [déconnecté](../operator/azure-stack-disconnected-deployment.md). Pour tirer le meilleur parti d’Azure Stack Hub, y compris dans le cadre de scénarios hybrides, vous pouvez opérer le déploiement connecté à Azure. Vous devez sélectionner définitivement les services de fédération Active Directory (AD FS) ou Azure AD au moment du déploiement. *Vous ne pouvez pas changer de fournisseur d’identité ultérieurement sans redéployer l’intégralité du système.*
- **Intégration réseau.** L’[intégration réseau](../operator/azure-stack-network.md) est cruciale pour le déploiement, les opérations et la gestion des systèmes Azure Stack Hub. Plusieurs facteurs permettent de s’assurer que la solution Azure Stack Hub est résiliente et dispose d’une infrastructure physique à haut niveau de disponibilité pour prendre en charge ses opérations.
- **Intégration du pare-feu.** Nous vous recommandons d’[utiliser un pare-feu](../operator/azure-stack-firewall.md) pour sécuriser Azure Stack Hub. Les pare-feu permettent de prévenir les attaques DDoS, de détecter les intrusions et d’inspecter le contenu. Notez que les pare-feu peuvent devenir un goulot d’étranglement du débit nécessaire aux services de stockage Azure.
- **Conditions requises des certificats.** Il est essentiel que tous les [certificats nécessaires ](../operator/azure-stack-pki-certs.md) soient disponibles avant qu’un ingénieur sur site accède à votre centre de données pour le déploiement.

Une fois que toutes les informations prérequises sont recueillies dans la feuille de travail de déploiement, Microsoft s’assure que nous vérifions que tous les outils de validation ont été exécutés et répond à toute autre question que vous êtes susceptible d’avoir.

## <a name="onsite-deployment"></a>Déploiement local

Pour déployer Azure Stack Hub, un ingénieur de déploiement Microsoft sera présent pour lancer le déploiement. Nous demandons aussi à un ingénieur réseau de votre organisation d’être disponible pendant la durée du déploiement local.

L’ingénieur sur site doit normalement vérifier les points suivants durant l’expérience de déploiement :

- Déballage et inventaire du matériel.
- Raccordement à l’alimentation et mise sous tension de la solution.
- Validation de l’intégrité du matériel physique.
- Vérification du câblage et de la connectivité de frontières pour s’assurer que la solution est correctement mise en place et répond à vos besoins.
- Configuration de la solution HLH (Hardware Lifecycle Host).
- Intégration du réseau du centre de données.
- Vérifiez que tous les paramètres du matériel physique sont corrects.
- Vérification du fait que la version du microprogramme de tous les composants est la dernière version approuvée par la solution.
- Démarrage du déploiement.

## <a name="post-deployment"></a>Après le déploiement

L’ingénieur de déploiement Microsoft doit effectuer plusieurs étapes avant que la solution soit remise au client. Lors de cette phase, la validation est importante afin de s’assurer que le système est déployé et fonctionne correctement.

Actions qui doivent être effectuées par l’ingénieur de déploiement Microsoft :

- Activation des fournisseurs de ressources à valeur ajoutée.
- Exécuter [test-azurestack](../operator/azure-stack-diagnostic-test.md).
- [Inscription](../operator/azure-stack-registration-role.md) auprès d’Azure.
- Vérification de la [syndication de la Place de marché Azure Stack Hub](../operator/azure-stack-marketplace.md).
- Sauvegarde des fichiers de configuration de commutateur et des fichiers de configuration HLH.
- Préparer une synthèse client pour le déploiement.
- [Vérifier les mises à jour](../operator/azure-stack-updates.md) pour garantir que les logiciels de la solution sont mis à jour vers la dernière version.

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble du déploiement de Modular Data Center](deployment-overview.md)

