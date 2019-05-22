---
title: Procédure pas à pas d’intégration d’Azure Stack à un centre de données | Microsoft Docs
description: Découvrez comment réussir un déploiement local d’Azure Stack dans votre centre de données.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: asganesh
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: db75a99f9b8e4b3c0db272ccd0e855ea944b7129
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617889"
---
# <a name="azure-stack-datacenter-integration"></a>Intégration du centre de données Azure Stack

Cet article décrit l’expérience utilisateur Azure Stack de bout en bout, de l’achat d’un système intégré jusqu’au déploiement local par un fournisseur de solutions. Utilisez ces informations pour préparer votre parcours et définir des objectifs pour vous, client Azure Stack.

En tant que client Azure Stack, vous devez anticiper les phases suivantes :

|     |Phase de planification|Processus de commande|Prédéploiement|Processus en usine|Livraison de matériel|Déploiement local|
|-----|-----|-----|-----|-----|-----|-----|
|**Microsoft**|Collaborer avec le partenaire afin de fournir un support prévente.|Préparer les licences de logiciels et les contrats en fonction des besoins.|Fournir les outils requis pour recueillir les exigences d’intégration de centre de données et fournir la documentation au client.|Fournir chaque mois les builds de ligne de base et mises à jour de chaîne d’outils les plus récentes.|N/A|Les ingénieurs du support technique Microsoft fournissent une assistance en cas de problème de déploiement.|
|**Partenaire**|Recommander des options de solution en fonction des exigences du client.<br><br>Proposer une preuve de concept si nécessaire.<br><br>Établir une relation commerciale.<br><br>Choisir le niveau de support.|Préparer les contrats nécessaires avec le client.<br><br>Créer un bon de commande client.<br><br>Décider de la chronologie de livraison.<br><br>Mettre le client en rapport avec Microsoft si nécessaire.|Fournir au client la formation nécessaire pour garantir une bonne compréhension de toutes les options d’intégration au centre de données et de tous les prérequis du déploiement.<br><br>Aider le client avec la validation des données recueillies afin de garantir leur exhaustivité et leur exactitude.|Appliquer la dernière build de ligne de base validée.<br><br>Appliquer la boîte à outils de déploiement Microsoft nécessaire.|Livrer le matériel au site du client.|Déploiement géré par un ingénieur sur site.<br><br>Montage en rack et empilement.<br><br>Déploiement de l’hôte du cycle de vie du matériel.<br><br>Déploiement d’Azure Stack.<br><br>Remise au client.|
|**Client**|Décrire les cas d’utilisation prévue et spécifier les exigences.|Déterminer le modèle de facturation à utiliser, passer en revue et approuver les contrats.|Renseigner la [feuille de calcul de déploiement](azure-stack-deployment-worksheet.md) et vérifier que tous les prérequis au déploiement sont satisfaits et prêts pour le déploiement.|N/A|Préparer le centre de données en s’assurant que toutes les exigences d’intégration de centre de données telles que l’alimentation et le refroidissement, la connectivité de frontière et autres sont en place.|Être disponible pendant le déploiement pour fournir les informations d’identification de l’abonnement et un support en cas de question sur les données fournies.|
| | | | | | | |


## <a name="planning-phase"></a>Phase de planification
La phase de planification correspond à la période durant laquelle Microsoft, ou le partenaire de solution Azure Stack, vous aidera à évaluer et à comprendre vos besoins afin de déterminer si Azure Stack est la solution idéale pour vous :

Ils vous aideront à répondre aux questions suivantes :

-   Azure Stack est-elle une solution adaptée à votre organisation ?

-   Quelle sera la taille de la solution dont vous aurez besoin ?

-   Quel type de modèle de facturation et de gestion des licences conviendra à votre organisation ?

-   Quelles sont les exigences en matière d’alimentation et de refroidissement ?

Pour être certain que la solution matérielle correspondra à vos besoins, vous pouvez utiliser [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) pour vous aider durant la planification préalable à l’achat, afin de déterminer la capacité et la configuration appropriées de votre solution matérielle Azure Stack.

La feuille de calcul n’est *pas* censée se substituer à vos propres recherches et analyses des solutions matérielles correspondant à vos besoins. Lors de la planification d’un déploiement Azure Stack, vous devez également consulter les [considérations générales relatives à l’intégration au centre de données](azure-stack-datacenter-integration.md) pour les systèmes intégrés Azure Stack.

## <a name="order-process-phase"></a>Phase de processus de commande
À ce stade, vous aurez obtenu une réponse à la plupart de vos questions en matière de faisabilité. Une fois la décision d’acheter Azure Stack prise, et après avoir signé tous les contrats et les bons de commande nécessaires, vous devrez fournir les données relatives aux exigences d’intégration à votre fournisseur de solutions.

## <a name="pre-deployment-phase"></a>Phase de prédéploiement
Pendant cette phase, vous devez décider comment vous souhaitez intégrer Azure Stack dans votre centre de données. Pour faciliter ce processus, Microsoft, en collaboration avec les fournisseurs de solutions, a mis au point un modèle d’exigences pour vous aider à rassembler les informations nécessaires à la planification d’un déploiement de système intégré dans votre environnement.

L’article [Considérations relatives à l’intégration au centre de données pour les systèmes intégrés Azure Stack](azure-stack-datacenter-integration.md) fournit des informations qui vous aideront à terminer le modèle, appelé Feuille de calcul de déploiement. 

> [!IMPORTANT]
> Durant cette étape, il est important d’examiner et de prendre une décision concernant tous les prérequis. Sachez que cette étape prend du temps et nécessite une coordination et une collecte de données à partir de plusieurs disciplines au sein de votre organisation. Des informations incorrectes ou incomplètes peuvent allonger le temps de déploiement. 

Durant la phase de prédéploiement, vous devrez déterminer les éléments suivants :

- **Fournisseur d’identité et modèle de connexion Azure Stack**. Vous pouvez choisir de déployer Azure Stack [en le connectant ou non à internet (et à Azure)](azure-stack-connection-models.md). Pour tirer le meilleur parti d’Azure Stack, y compris les scénarios hybrides, il est préférable de le connecter à Azure. Vous devez sélectionner les services de fédération Active Directory (AD FS) ou Azure Active Directory (Azure AD) au moment du déploiement. **Vous ne pouvez pas revenir en arrière sans redéployer l’intégralité du système**.

- **Modèle de licence**. Les options de modèle de licence disponibles varient en fonction du type de déploiement que vous aurez. Votre choix de fournisseur d’identité n’a aucune incidence sur les machines virtuelles du locataire ou sur le système d’identité et les comptes qu’elles utilisent.
    - Les clients qui se trouvent dans un [déploiement déconnecté](azure-stack-disconnected-deployment.md) n’ont qu’une seule option : la facturation basée sur la capacité.

    - Les clients qui se trouvent dans un [déploiement connecté](azure-stack-connected-deployment.md) peut choisir entre la facturation basée sur la capacité et le paiement à l’utilisation. La facturation basée sur la capacité nécessite un abonnement Azure Accord Entreprise à des fins d’inscription. Il est nécessaire pour l’inscription, qui fournit la disponibilité des éléments dans la Place de marché grâce à un abonnement Azure.

- **Intégration réseau**. L’[intégration réseau](azure-stack-network.md) est cruciale pour le déploiement, le fonctionnement et la gestion des systèmes Azure Stack. Plusieurs facteurs permettent de s’assurer que la solution Azure Stack est résiliente et dispose d’une infrastructure physique hautement disponible pour prendre en charge ses opérations.

- **Intégration du pare-feu**. Nous vous recommandons d’[utiliser un pare-feu](azure-stack-firewall.md) pour sécuriser Azure Stack. Les pare-feu peuvent aider à prévenir les attaques DDOS, la détection d’intrusion et l’inspection du contenu. Toutefois, il convient de noter qu’ils peuvent devenir un goulot d’étranglement du débit pour les services de stockage Azure.


- **Exigences en matière de certificats**. Il est essentiel que tous les [certificats requis](azure-stack-pki-certs.md) soient disponibles *avant* qu’un ingénieur sur site accède à votre centre de données pour le déploiement.

Une fois que toutes les informations sur les prérequis ont été recueillies par le biais de la feuille de calcul de déploiement, le fournisseur de solutions lancera le processus en usine en fonction des données collectées, afin de garantir la réussite de l’intégration d’Azure Stack dans votre centre de données.

## <a name="hardware-delivery-phase"></a>Phase de livraison de matériel
Votre fournisseur de solutions discutera avec vous afin de planifier la livraison de la solution à votre site. Une fois celle-ci reçue et mise en place, vous devrez convenir d’un horaire avec le fournisseur de solutions afin qu’un ingénieur se déplace sur le site pour effectuer le déploiement d’Azure Stack.

Il est **essentiel** que toutes les données prérequises soient verrouillées et disponibles *avant que l’ingénieur arrive pour déployer la solution*.

-   Tous les certificats doivent avoir été achetés et être prêts.

-   Le nom de région doit être choisi.

-   Tous les paramètres d’intégration réseau sont finalisés et correspondent à ce que vous avez partagé avec votre fournisseur de solutions.

> [!TIP]
> Si l’une de ces informations a changé, veillez à communiquer le changement au fournisseur de solutions avant de planifier le déploiement proprement dit.

## <a name="onsite-deployment-phase"></a>Phase de déploiement local
Pour déployer Azure Stack, un ingénieur sur site envoyé par votre fournisseur de solutions matérielles devra être présent pour lancer le déploiement. Pour garantir le succès du déploiement, vérifiez qu’aucune des informations fournies à l’aide de la feuille de calcul de déploiement n’a changé. 

L’ingénieur sur site devrait normalement vérifier les points suivants durant l’expérience de déploiement :

- Vérification du câblage et de la connectivité de frontières pour s’assurer que la solution est correctement mise en place et répond à vos besoins.
- Configuration de la solution HLH (Hardware Lifecycle Host), le cas échéant.
- Vérification que tous les paramètres BMC, BIOS et réseau sont corrects
- Vérification que la version du microprogramme de tous les composants est la dernière version approuvée par la solution.
- Démarrage du déploiement.

> [!NOTE]
> Une procédure de déploiement par le technicien sur site peut nécessiter environ une semaine de travail.

## <a name="post-deployment-phase"></a>Phase de post-déploiement
Le partenaire doit effectuer plusieurs étapes avant que la solution soit remise au client durant la phase de post-intégration. Lors de cette phase, la validation est importante afin de s’assurer que le système est déployé et fonctionne correctement. 

Les actions qui doivent être effectuées par le partenaire OEM sont les suivantes :

- [Exécution de l’applet de commande test-azurestack](azure-stack-diagnostic-test.md)

- [Inscription auprès d’Azure](azure-stack-registration.md)

- [Syndication de la Place de marché](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items)

- Sauvegarde des fichiers de configuration de commutateur et des fichiers de configuration HLH

- Suppression de DVM

- Préparation d’une synthèse client pour le déploiement

- [Vérification des mises à jour pour s’assurer que le logiciel de la solution est mis à jour vers la dernière version](./azure-stack-updates.md)

Plusieurs étapes peuvent être obligatoires ou facultatives, en fonction du type d’installation.

- Si le déploiement a été effectué à l’aide d’[AD FS](azure-stack-integrate-identity.md), le tampon Azure Stack devra être intégré aux propres services AD FS du client.

  > [!NOTE]
  > C’est le client qui est responsable de l’exécution de cette étape, bien que le partenaire puisse pour cela choisir de proposer des services.

- Intégration avec un système de supervision existant du partenaire concerné.

  -   L’[intégration de System Center Operations Manager](azure-stack-integrate-monitor.md) prend également en charge les fonctionnalités de gestion de flotte.

  -   [Intégration de Nagios](azure-stack-integrate-monitor.md#integrate-with-nagios)

## <a name="overall-timeline"></a>Chronologie globale

![](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>Support
Azure Stack offre une expérience de support intégrée et cohérente avec Azure qui couvre l’ensemble du cycle de vie du système. Pour bénéficier d’une prise en charge complète des systèmes intégrés Azure Stack, les clients ont besoin de deux contrats de support : un avec Microsoft (ou leur fournisseur de solutions cloud) pour la prise en charge des services Azure, et un avec le fournisseur de matériel pour la prise en charge du système. L’expérience de prise en charge intégrée fournit une coordination de l’escalade et de la résolution des problèmes, afin que les clients bénéficient d’une expérience de support cohérente quel que soit la personne qu’ils appellent en premier. Pour les clients qui disposent déjà d’un support Premier, Azure Standard / ProDirect ou Partenaire avec Microsoft, la prise en charge des logiciels Azure Stack est incluse.

L’expérience de prise en charge intégrée fait appel à un mécanisme d’échange de cas pour le transfert bidirectionnel des cas de support et des mises à jour de cas entre Microsoft et le partenaire matériel. Microsoft Azure Stack suit la [Politique moderne en matière de cycle de vie](https://support.microsoft.com/help/30881).

## <a name="next-steps"></a>Étapes suivantes
Apprenez-en davantage sur les [considérations générales relatives à l’intégration au centre de données](azure-stack-datacenter-integration.md).
