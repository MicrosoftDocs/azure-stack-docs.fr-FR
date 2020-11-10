---
title: Parcours utilisateur de l’achat au post-déploiement Azure Stack Hub | Microsoft Docs
description: Découvrez comment réussir un déploiement local d’un Modular Data Center, de la planification au post-déploiement.
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
ms.openlocfilehash: 12631a118600e67611294c87636cd2fa62bfaaea
ms.sourcegitcommit: ecd98662194d2cdb15c22f8b1f99812fc5f4c15a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93344777"
---
# <a name="modular-data-center-integration-overview"></a>Présentation de l’intégration du MDC (Modular Data Center)

Cet article décrit le processus de bout en bout relatif à l’intégration d’un centre de données MDC, de l’achat au post-déploiement. L’intégration est un projet collaboratif entre le client et Microsoft. Les sections suivantes couvrent différentes phases de la chronologie du projet et des étapes spécifiques pour les membres du projet.

## <a name="introduction"></a>Introduction

La table suivante répertorie ce que vous pouvez attendre au cours des différentes phases du déploiement.

|   |Processus de commande  |Prédéploiement |Intégration, validation, transport |Déploiement local  |Post déploiement |
|---|---------------|---------------|-----------------------------------|--------------------|----------------|
|Microsoft  |- Signal à la livraison à l’emplacement des États-Unis    |Fournir les outils et la documentation requis pour recueillir les exigences de centre de données.  |- Valider les artefacts de configuration et vérifier les résultats de validation<br>- Vérifier que le matériel est remis  |- Installation et intégration<br>- Intégration réseau<br>- Déploiement Azure Stack Hub<br>- Transfert au client    |Syndication d’inscription et de Place de marché|
|Customer   |Achat de signaux   |- Renseigne les détails du réseau dans la feuille de travail de déploiement<br>- Collecte les certificats<br>- Invite pour les comptes Azure AD<br>- Exécute tous les outils de validation fournis    |Assurez-vous que le site dispose des composants requis réseau, d’alimentation et de refroidissement    |- Soyez prêt pour les artefacts de configuration de déploiement<br>- Ingénieur réseau du client disponible   |     |


## <a name="order-process"></a>Traitement des commandes

Votre organisation utilise Microsoft pour passer une commande pour un nombre de systèmes alloués. Une fois votre commande passée, Microsoft fournit le centre de données MDC dans votre emplacement aux États-Unis. Microsoft s’assure que toutes les exigences de la chaîne d’approvisionnement sécurisée sont respectées. 

## <a name="hardware-delivery"></a>Livraison de matériel

Microsoft travaillera avec vous pour s’assurer que tous le matériel requis arrive à l’emplacement des États-Unis dans le temps imparti.  

Il est **essentiel** que toutes les données prérequises soient verrouillées et disponibles *avant que l’ingénieur arrive pour déployer la solution.*

- La feuille de calcul de déploiement contient toutes les données renseignées. 
- Tous les certificats doivent être validés et prêts.
- Le nom de région doit être choisi.
- Tous les paramètres d’intégration réseau sont finalisés.

>[!Tip]
>Si l’une de ces informations a changé, veillez à utiliser votre organisation interne pour vous assurer que les informations sont mises à jour avant l’arrivée de l’ingénieur de déploiement sur site. Cela permet d’éviter les retards dans le processus de déploiement.

## <a name="pre-deployment"></a>Prédéploiement

Vous déterminez la façon d’intégrer Azure Stack Hub à votre centre de données. Microsoft a publié une [feuille de calcul de déploiement](../operator/azure-stack-deployment-worksheet.md) qui vous guide tout au long de la collecte de toutes les informations nécessaires pour une intégration correcte dans votre centre de données. En outre, un certain ensemble de certificats est requis au moment du déploiement. Pour vous aider à obtenir ces certificats, Microsoft met à votre disposition un outil appelé [Vérificateur de préparation Azure Stack Hub](../operator/azure-stack-validation-report.md). Cet outil vous permet de créer des demandes de signature de certificat (CSR) à fournir à votre autorité de certification interne. 

>[!Important]
>Toutes les composants requis sont validés afin d’éviter les retards de déploiement. La vérification des prérequis peut prendre du temps. Elle peut nécessiter une coordination et une collecte de données auprès de différents services de votre organisation.

Vous allez choisir les éléments suivants :

- **Fournisseur d’identité et modèle de connexion Azure Stack Hub.** Vous pouvez choisir de déployer Azure Stack Hub [connecté à Internet (et à Azure)](../operator/azure-stack-connected-deployment.md) ou [déconnecté](../operator/azure-stack-disconnected-deployment.md). Pour tirer le meilleur parti d’Azure Stack Hub, y compris dans le cadre de scénarios hybrides, vous pouvez opérer le déploiement connecté à Azure. Vous devez sélectionner les services de fédération Active Directory (AD FS) ou Azure Active Directory (Azure AD) au moment du déploiement. **Vous ne pouvez pas changer de fournisseur d’identité ultérieurement sans redéployer l’intégralité du système.**
- **Intégration réseau.** L’[intégration réseau](../operator/azure-stack-network.md) est cruciale pour le déploiement, les opérations et la gestion des systèmes Azure Stack Hub. Plusieurs facteurs permettent de s’assurer que la solution Azure Stack Hub est résiliente et dispose d’une infrastructure physique à haut niveau de disponibilité pour prendre en charge ses opérations.
- **Intégration du pare-feu.** Nous vous recommandons d’[utiliser un pare-feu](../operator/azure-stack-firewall.md) pour sécuriser Azure Stack Hub. Les pare-feu peuvent aider à prévenir les attaques DDOS, la détection d’intrusion et l’inspection du contenu. Toutefois, il convient de noter qu’ils peuvent devenir un goulot d’étranglement du débit pour les services de stockage Azure.
- **Conditions requises des certificats.** Il est essentiel que tous les [certificats nécessaires ](../operator/azure-stack-pki-certs.md) soient disponibles avant qu’un ingénieur sur site accède à votre centre de données pour le déploiement.

Une fois que toutes les informations prérequises sont rassemblées par le biais de la feuille de travail de déploiement, Microsoft s’assure que nous vérifions que tous les outils de validation aient été exécutés et répondons à toute autre question posée. 

## <a name="onsite-deployment"></a>Déploiement local

Pour déployer Azure Stack Hub, un ingénieur de déploiement Microsoft sera présent pour lancer le déploiement. Un ingénieur réseau de votre organisation doit également être disponible pendant la période de déploiement sur site.

L’ingénieur sur site doit normalement vérifier les points suivants durant l’expérience de déploiement :

- Conversion unboxing et inventaire du matériel
- Connexion de l’alimentation et de la mise sous tension à la solution
- Validation de l'intégrité du matériel physique
- Vérification du câblage et de la connectivité de frontières pour s’assurer que la solution est correctement mise en place et répond à vos besoins
- Configuration de la solution HLH (Hardware Lifecycle Host)
- Intégration du réseau du centre de données
- Vérifier que tous les paramètres du matériel physique sont corrects
- Vérifier que la version du microprogramme de tous les composants est la dernière version approuvée par la solution
- Démarrage du déploiement

## <a name="post-deployment"></a>Postdéploiement

L’ingénieur de déploiement Microsoft doit effectuer plusieurs étapes avant que la solution soit remise au client. Lors de cette phase, la validation est importante afin de s’assurer que le système est déployé et fonctionne correctement.

Actions qui doivent être effectuées par l’ingénieur de déploiement Microsoft :

- Activer les fournisseurs de ressources à valeur ajoutée
- Exécuter [test-azurestack](../operator/azure-stack-diagnostic-test.md)
- [Inscription](../operator/azure-stack-registration-role.md) auprès d’Azure
- [Syndication de Place de marché](../operator/azure-stack-marketplace.md)
- Sauvegarder les fichiers de configuration du commutateur et de configuration de l’hôte du cycle de vie du matériel (HLH)
- Préparation d’une synthèse client pour le déploiement
- [Vérifier les mises à jour](../operator/azure-stack-updates.md) pour garantir que les logiciels de la solution sont mis à jour vers la dernière version

## <a name="next-steps"></a>Étapes suivantes

[Présentation du déploiement de Modular Data Center](deployment-overview.md).

