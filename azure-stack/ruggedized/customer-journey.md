---
title: Parcours utilisateur de l’achat au post-déploiement Azure Stack Hub | Microsoft Docs
description: Découvrez comment réussir un déploiement local de Microsoft Azure Stack Hub renforcé, de la planification au postdéploiement.
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
ms.openlocfilehash: f34a449490ab7b57beacc942584f9616fd6d543c
ms.sourcegitcommit: 5fbc60b65d27c916ded7a95ba4102328d550c7e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97598315"
---
# <a name="customer-journey"></a>Parcours client

Cet article décrit le processus de bout en bout relatif à l’intégration du centre de données Azure Stack Hub, de l’achat au post-déploiement. L’intégration est un projet collaboratif entre le client et Microsoft. Les sections suivantes couvrent différentes phases de la chronologie du projet et des étapes spécifiques pour les membres du projet.

## <a name="introduction"></a>Introduction

La table suivante décrit ce que vous pouvez attendre au cours des différentes phases du déploiement.

|   |Processus de commande  |Prédéploiement |Intégration, validation, transport |Déploiement local  |Post déploiement |
|---|---------------|---------------|-----------------------------------|--------------------|----------------|
| **Microsoft** |- Signal à la livraison à l’emplacement des États-Unis<br>- Azure Stack Hub renforcé = 10 jours |Fournir les outils et la documentation requis pour recueillir les exigences de centre de données.  |- Valider les artefacts de configuration et vérifier les résultats de validation<br>- Vérifier que le matériel est remis  |- Installation et intégration<br>- Intégration réseau<br>- Déploiement Azure Stack Hub<br>- Transfert au client    |Syndication d’inscription et de Place de marché|
| **Client** |Achat de signaux    |- Renseigne les détails du réseau dans la feuille de travail de déploiement<br>- Collecte les certificats<br>- Invite pour les comptes Azure AD<br>- Exécute tous les outils de validation fournis    |Assurez-vous que le site dispose des composants requis réseau, d’alimentation et de refroidissement    |- Soyez prêt pour les artefacts de configuration de déploiement<br>- Ingénieur réseau du client disponible   |     |


## <a name="order-process"></a>Processus de commande

Votre organisation utilise Microsoft pour passer une commande pour un nombre de systèmes alloués. Une fois la commande passée, Microsoft dispose de 10 jours pour livrer Azure Stack Hub renforcé sur votre site aux États-Unis. Microsoft s’assure que toutes les exigences de la chaîne d’approvisionnement sécurisée sont respectées. 

>[!NOTE] 
>La facturation commence 14 jours après la livraison du matériel.


Pour créer une ressource Azure Stack Hub, suivez ces étapes dans le Portail Azure.

1. Utilisez vos informations d’identification Microsoft Azure pour vous connecter au portail Azure en suivant cette URL : [https://portal.azure.com](https://portal.azure.com).
1. Dans le volet de gauche, sélectionnez **+ Créer une ressource**. Recherchez et sélectionnez **Azure Stack Hub renforcé**. Sélectionnez **Create** (Créer).
1. Sélectionnez l’abonnement que vous souhaitez utiliser pour l’appareil Azure Stack Hub. Sélectionnez le pays où vous souhaitez expédier cet appareil physique. Sélectionnez **Afficher les appareils**.
1. Un formulaire abrégé s’affiche. Remplissez le formulaire et sélectionnez **Envoyer**. Microsoft activera votre abonnement.
1. Une fois l’abonnement activé, vous devriez être en mesure de procéder à la création de la ressource. Dans le panneau **Sélectionner le type d'appareil**, choisissez **Sélectionner**. 
1. Sous l’onglet **Bases**, entrez ou sélectionnez les **détails du projet** suivants.
    
    |Paramètre  |Valeur  |
    |---------|---------|
    |Abonnement    |Ce champ est automatiquement renseigné en fonction de la sélection antérieure. L’abonnement est lié à votre compte de facturation. |
    |Resource group  |Sélectionnez un groupe existant ou créez-en un.   |

1. Entrez ou sélectionnez les **détails de l’instance** suivants.

    |Paramètre  |Valeur  |
    |---------|---------|
    |Nom   | Entrez un nom reconnaissable pour identifier la ressource.<br>Le nom doit être compris entre 2 et 50 caractères, et se composer uniquement de lettres, de chiffres et de traits d’union.<br> Le nom doit commencer et se terminer par une lettre ou un chiffre.        |
    |Région     |Pour obtenir la liste complète des régions où la ressource Azure Stack Hub est disponible, consultez [Disponibilité des produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Si vous utilisez Azure Government, toutes les régions administratives sont disponibles, comme indiqué dans [Régions Azure](https://azure.microsoft.com/global-infrastructure/regions/).<br> Choisissez l’emplacement le plus proche de la région géographique dans laquelle vous souhaitez déployer votre appareil.|


1. Sélectionnez **Suivant : Adresse de livraison**.

    - Si vous disposez déjà d’un appareil, sélectionnez la zone de liste modifiable **J’ai un appareil Azure Stack Hub**.

    - Si vous commandez un nouvel appareil, entrez le nom du contact, la société, l’adresse de livraison de l’appareil et les informations de contact.

1. Sélectionnez **Suivant : Vérifier + créer**.
1. Sous l’onglet **Vérifier + créer**, passez en revue les **Détails de la tarification**, les **Conditions d’utilisation** et les détails de votre ressource. Cochez la case **J’ai pris connaissance des conditions de confidentialité**.
1. Sélectionnez **Create** (Créer).

La création de la ressource prend quelques minutes. Un message vous informe que la ressource a été créée et déployée. Sélectionnez **Accéder à la ressource**.

Une fois la commande passée, Microsoft l’examine et vous communique (par e-mail) les détails de l’expédition.

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

## <a name="hardware-delivery"></a>Livraison de matériel

Microsoft travaillera avec vous pour s’assurer que tous le matériel requis arrive à l’emplacement des États-Unis dans le temps imparti.  

Il est **essentiel** que toutes les données prérequises soient verrouillées et disponibles *avant que l’ingénieur arrive pour déployer la solution.*

- La feuille de calcul de déploiement contient toutes les données renseignées. 
- Tous les certificats doivent être validés et prêts.
- Le nom de région doit être choisi.
- Tous les paramètres d’intégration réseau sont finalisés.

>[!Tip]
>Si l’une de ces informations a changé, veillez à utiliser votre organisation interne pour vous assurer que les informations sont mises à jour avant l’arrivée de l’ingénieur de déploiement sur site. Cela permet d’éviter les retards dans le processus de déploiement.

## <a name="onsite-deployment"></a>Déploiement local

Pour déployer Azure Stack Hub, un ingénieur de déploiement Microsoft sera présent pour lancer le déploiement. Un ingénieur réseau de votre organisation doit également être disponible pendant la période de déploiement sur site.

L’ingénieur sur site doit normalement vérifier les points suivants durant l’expérience de déploiement :

- Conversion unboxing et inventaire du matériel
- Connexion de l’alimentation et de la mise sous tension à la solution
- Validation de l'intégrité du matériel physique
- Vérification du câblage et de la connectivité de frontières pour s’assurer que la solution est correctement mise en place et répond à vos besoins.
- Configurez la solution HLH (Hardware Lifecycle Host).
- Intégration du réseau du centre de données
- Vérifiez que tous les paramètres du matériel physique sont corrects.
- Vérification du fait que la version du microprogramme de tous les composants est la dernière version approuvée par la solution.
- Démarrage du déploiement. 

## <a name="post-deployment"></a>Après le déploiement

L’ingénieur de déploiement Microsoft doit effectuer plusieurs étapes avant que la solution soit remise au client. Lors de cette phase, la validation est importante afin de s’assurer que le système est déployé et fonctionne correctement.

Actions qui doivent être effectuées par l’ingénieur de déploiement Microsoft :

- Activer les fournisseurs de ressources à valeur ajoutée.
- Exécuter [test-azurestack](../operator/azure-stack-diagnostic-test.md).
- [Inscription](../operator/azure-stack-registration-role.md) auprès d’Azure.
- [Syndication de la Place de marché](../operator/azure-stack-marketplace.md).
- Sauvegarder les fichiers de configuration du commutateur et de configuration HLH.
- Préparer une synthèse client pour le déploiement.
- [Vérifier les mises à jour](../operator/azure-stack-updates.md) pour garantir que les logiciels de la solution sont mis à jour vers la dernière version.

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail les [étapes d’installation et de configuration de Microsoft Azure Stack Hub renforcé](deployment-overview.md).

