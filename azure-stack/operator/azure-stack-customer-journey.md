---
title: Procédure pas à pas d’intégration d’Azure Stack Hub dans un centre de données
description: Découvrez comment réussir un déploiement local d’Azure Stack Hub dans votre centre de données, de la planification aux tâches post-déploiement.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: asganesh
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: d55176266fc169b344e7bb43389a46657174b88e
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "78368830"
---
# <a name="azure-stack-hub-datacenter-integration-walkthrough"></a>Procédure pas à pas d’intégration d’Azure Stack Hub dans un centre de données

Cet article décrit le processus de bout en bout relatif à l’intégration du centre de données Azure Stack Hub, de l’achat au support de post-déploiement. L’intégration est un projet collaboratif entre le client, un fournisseur de solutions et Microsoft. Cliquez sur les onglets suivants pour voir les étapes spécifiques à chaque membre du projet, puis consultez les sections suivantes pour obtenir un récapitulatif des différentes phases de la chronologie du projet. 

# <a name="customer"></a>[Client](#tab/customer)

1. Décrire les cas d’usage et les exigences
1. Déterminer le modèle de facturation
1. Passer en revue les contrats et les approuver
1. Compléter la [feuille de déploiement](azure-stack-deployment-worksheet.md)
1. Vérifier le respect des prérequis du déploiement
1. Préparer le centre de données 
1. Fournir des informations relatives à l’abonnement durant le déploiement
1. Résoudre les questions relatives aux données fournies

# <a name="partner"></a>[Partenaire](#tab/partner)

1. Recommander des options de solution en fonction des exigences du client
1. Proposer un POC (preuve de concept) 
1. Déterminer le niveau de support
1. Préparer les contrats avec le client
1. Créer un bon de commande client
1. Déterminer le plan de livraison
1. Mettre le client en rapport avec Microsoft 
1. Former le client au déploiement 
1. Aider le client à valider les données collectées
1. Installer et valider la build de base de référence et le kit de ressources de déploiement Microsoft
1. Livrer le matériel au site du client
1. Fournir un ingénieur sur site
1. Assembler le matériel
1. Déployer l’hôte HLH (hôte du cycle de vie du matériel) 
1. Déployer Azure Stack Hub
1. Effectuer la remise au client

# <a name="microsoft"></a>[Microsoft](#tab/micro)

1. Impliquer le partenaire pour le support avant-vente
2. Préparer la gestion de licences des logiciels et les contrats
3. Fournir les outils de collecte des exigences d’intégration du centre de données
4. Fournir les builds de base de référence mensuelles et les mises à jour de chaîne d’outils
5. Fournir via les ingénieurs du Support Microsoft une assistance en cas de problème de déploiement

---

## <a name="planning"></a>Planification
Microsoft ou un partenaire de solution Azure Stack Hub vous aideront à évaluer vos objectifs. Ils vous aideront à répondre aux questions suivantes :

-   Azure Stack Hub est-il la solution appropriée pour votre organisation ?
-   Quel type de modèle de facturation et de gestion des licences conviendra à votre organisation ?
-   Quelle sera la taille de la solution dont vous aurez besoin ?
-   Quelles sont les exigences en matière d’alimentation et de refroidissement ?

Utilisez [Azure Stack Hub Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) pour rechercher et analyser les fonctionnalités et la configuration matérielles optimales pour vos besoins. 

## <a name="ordering"></a>Commande
Votre organisation s’engage à acheter Azure Stack Hub, à signer des contrats et des bons de commande, ainsi qu’à fournir au fournisseur de solutions les données relatives aux exigences d’intégration.

## <a name="pre-deployment"></a>Prédéploiement
Vous déterminez la façon d’intégrer Azure Stack Hub à votre centre de données. Microsoft a collaboré avec les fournisseurs de solutions pour publier une [feuille de déploiement](azure-stack-deployment-worksheet.md) afin de vous aider à rassembler les informations nécessaires.
L’article [Considérations relatives à l’intégration au centre de données pour les systèmes intégrés Azure Stack](azure-stack-datacenter-integration.md) fournit des informations qui vous aideront à terminer le modèle, appelé Feuille de calcul de déploiement.

> [!IMPORTANT]
> Tous les prérequis sont examinés avant de commander la solution pour éviter les retards de déploiement. La vérification des prérequis peut prendre du temps. Elle peut nécessiter une coordination et une collecte de données auprès de différents services de votre organisation. 

Vous allez choisir les éléments suivants :

- **Fournisseur d’identité et modèle de connexion Azure Stack Hub**. Vous pouvez choisir de déployer Azure Stack Hub [connecté à Internet (et à Azure) ou déconnecté d’Internet](azure-stack-connection-models.md). Pour tirer le meilleur parti d’Azure Stack Hub, y compris dans le cadre de scénarios hybrides, vous pouvez opérer le déploiement connecté à Azure. Vous devez sélectionner les services de fédération Active Directory (AD FS) ou Azure Active Directory (Azure AD) au moment du déploiement. **Vous ne pouvez pas changer de fournisseur d’identité ultérieurement sans redéployer l’intégralité du système**.

- **Modèle de licence**. Les options de modèle de licence disponibles varient en fonction du type de votre déploiement. Votre choix de fournisseur d’identité n’a aucune incidence sur les machines virtuelles du locataire ou sur le système d’identité et les comptes qu’elles utilisent.
    - Les clients qui se trouvent dans un [déploiement déconnecté](azure-stack-disconnected-deployment.md) n’ont qu’une seule option : la facturation basée sur la capacité.

    - Les clients qui se trouvent dans un [déploiement connecté](azure-stack-connected-deployment.md) peut choisir entre la facturation basée sur la capacité et le paiement à l’utilisation. La facturation selon la capacité nécessite un abonnement Azure Contrat Enterprise pour l’inscription. C’est nécessaire pour l’inscription, qui fournit la disponibilité des éléments de la Place de marché Azure via un abonnement Azure.

- **Intégration réseau**. L’[intégration réseau](azure-stack-network.md) est cruciale pour le déploiement, les opérations et la gestion des systèmes Azure Stack Hub. Plusieurs facteurs permettent de s’assurer que la solution Azure Stack Hub est résiliente et dispose d’une infrastructure physique à haut niveau de disponibilité pour prendre en charge ses opérations.

- **Intégration du pare-feu**. Nous vous recommandons d’[utiliser un pare-feu](azure-stack-firewall.md) pour sécuriser Azure Stack Hub. Les pare-feu peuvent aider à prévenir les attaques DDOS, la détection d’intrusion et l’inspection du contenu. Toutefois, il convient de noter qu’ils peuvent devenir un goulot d’étranglement du débit pour les services de stockage Azure.

- **Exigences en matière de certificats**. Il est essentiel que tous les [certificats nécessaires ](azure-stack-pki-certs.md) soient disponibles *avant* qu’un ingénieur sur site accède à votre centre de données pour le déploiement.

Une fois toutes les informations sur les condition préalables recueillies par le biais de la feuille de calcul de déploiement, le fournisseur de solutions lance le d’usine en fonction des données collectées, afin de garantir la réussite de l’intégration d’Azure Stack Hub dans votre centre de données.

## <a name="hardware-delivery"></a>Livraison de matériel 
Votre fournisseur de solutions discutera avec vous afin de planifier la livraison de la solution à votre site. Une fois celle-ci reçue et mise en place, vous devrez convenir d’un horaire avec le fournisseur de solutions afin qu’un ingénieur se déplace sur le site pour effectuer le déploiement Azure Stack Hub.

Il est **essentiel** que toutes les données prérequises soient verrouillées et disponibles *avant que l’ingénieur arrive pour déployer la solution*.

-   Tous les certificats doivent avoir été achetés et être prêts.

-   Le nom de région doit être choisi.

-   Tous les paramètres d’intégration réseau sont finalisés et correspondent à ce que vous avez partagé avec votre fournisseur de solutions.

> [!TIP]
> Si l’une de ces informations a changé, veillez à communiquer le changement au fournisseur de solutions avant de planifier le déploiement proprement dit.

## <a name="onsite-deployment"></a>Déploiement local 
Pour déployer Azure Stack Hub, un ingénieur sur site envoyé par votre fournisseur de solutions matérielles doit être présent pour lancer le déploiement. Pour garantir le succès du déploiement, vérifiez qu’aucune des informations fournies via la feuille de calcul de déploiement n’a changé.

L’ingénieur sur site doit normalement vérifier les points suivants durant l’expérience de déploiement :

- Vérification du câblage et de la connectivité de frontières pour s’assurer que la solution est correctement mise en place et répond à vos besoins.
- Configuration de la solution HLH (Hardware Lifecycle Host), le cas échéant.
- Vérification du fait que tous les paramètres BMC, BIOS et réseau sont corrects.
- Vérification du fait que la version du microprogramme de tous les composants est la dernière version approuvée par la solution.
- Démarrage du déploiement.

> [!NOTE]
> Une procédure de déploiement par le technicien sur site peut nécessiter environ une semaine de travail.

## <a name="post-deployment"></a>Postdéploiement 
Le partenaire doit effectuer plusieurs étapes avant que la solution soit remise au client durant la phase de post-intégration. Lors de cette phase, la validation est importante afin de s’assurer que le système est déployé et fonctionne correctement. 

Les actions qui doivent être effectuées par le partenaire OEM sont les suivantes :

- [Exécuter test-azurestack](azure-stack-diagnostic-test.md).

- [Inscription auprès d’Azure](azure-stack-registration.md).

- [Syndication de la Place de marché](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items).

- Sauvegarder les fichiers de configuration du commutateur et les fichiers de configuration HLH.

- Supprimer DVM.

- Préparer une synthèse client pour le déploiement.

- [Vérifier les mises à jour pour garantir que les logiciels de la solution sont mis à jour vers la dernière version](./azure-stack-updates.md).

Plusieurs étapes peuvent être obligatoires ou facultatives, en fonction du type d’installation.

- Si le déploiement a été effectué à l’aide d’[AD FS](azure-stack-integrate-identity.md), le tampon Azure Stack Hub doit être intégré avec les services de fédération Active Directory (AD FS) du client.

  > [!NOTE]
  > C’est le client qui est responsable de l’exécution de cette étape, bien que le partenaire puisse pour cela choisir de proposer des services.

- Intégration avec un système de supervision existant du partenaire concerné.

  -   L’[intégration de System Center Operations Manager](azure-stack-integrate-monitor.md) prend également en charge les fonctionnalités de gestion de flotte.

  -   [Intégration de Nagios](azure-stack-integrate-monitor.md#integrate-with-nagios)

## <a name="schedule"></a>Planifier

![Chronologie générale du déploiement Azure Stack Hub local](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>Support
Azure Stack Hub offre une expérience de support intégrée et cohérente avec Azure qui couvre l’ensemble du cycle de vie du système. Pour bénéficier d’une prise en charge complète des systèmes intégrés Azure Stack Hub, les clients ont besoin de deux contrats de support : l’un avec Microsoft (ou leur fournisseur de solutions cloud) pour le support des services Azure, et l’autre avec le fournisseur de matériel pour le support du système. L’expérience de prise en charge intégrée fournit une coordination de l’escalade et de la résolution des problèmes, afin que les clients bénéficient d’une expérience de support cohérente, quelle que soit la personne qu’ils appellent en premier. Pour les clients qui disposent déjà d’un support Premier, Azure Standard / ProDirect ou Partenaire avec Microsoft, le support des logiciels Azure Stack Hub est inclus.

L’expérience de prise en charge intégrée fait appel à un mécanisme d’échange de cas pour le transfert bidirectionnel des cas de support et des mises à jour de cas entre Microsoft et le partenaire matériel. Microsoft Azure Stack Hub suit la [stratégie de cycle de vie moderne](https://support.microsoft.com/help/30881).

## <a name="next-steps"></a>Étapes suivantes
Apprenez-en davantage sur les [considérations générales relatives à l’intégration au centre de données](azure-stack-datacenter-integration.md).
