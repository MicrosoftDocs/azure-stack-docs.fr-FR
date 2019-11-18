---
title: Modèle pour l’implémentation d’une solution de données hiérarchisées à des fins analytiques utilisant Azure et Azure Stack Hub.
description: Découvrez comment utiliser les services Azure et Azure Stack Hub pour implémenter une solution de données hiérarchisées dans le cloud hybride.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: ac2c573e9ee1a2dad3afcdf86c9a6c273fb0f4e5
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73639974"
---
# <a name="tiered-data-for-analytics-pattern"></a>Modèle Données hiérarchisées à des fins analytiques

Ce modèle montre comment utiliser Azure Stack Hub et Azure pour organiser, analyser, traiter, assainir et stocker des données dans plusieurs emplacements locaux et dans le cloud.

## <a name="context-and-problem"></a>Contexte et problème

L’un des problèmes auxquels font face les organisations dans le paysage technologique moderne est lié au stockage, au traitement et à l’analyse sécurisés des données. Éléments à prendre en compte :
- contenu des données
- location
- exigences en matière de sécurité et de confidentialité
- autorisations d’accès
- maintenance
- entreposage du stockage

Azure, en association avec Azure Stack Hub, résout les problèmes liés aux données et offre des solutions à faible coût. Cette solution est idéale pour une entreprise de logistique ou de fabrication distribuée. 

La solution est basée sur le scénario suivant :
- Grande organisation à plusieurs filiales dans le secteur de la fabrication.
- Un stockage, un traitement et une distribution de données rapides et sécurisés entre les emplacements distants à l’échelle mondiale et le siège central sont requis. 
- Les données relatives à l’activité des employés et des machines, aux installations et aux rapports d’entreprise doivent rester sécurisées. Les données doivent être distribuées de manière appropriée et respecter la politique de conformité régionale et les réglementations du secteur.

## <a name="solution"></a>Solution

Utiliser à la fois des environnements locaux et de cloud public répond aux besoins des entreprises disposant de plusieurs sites. Azure Stack Hub offre une solution rapide, sécurisée et flexible pour la collecte, le traitement, le stockage et la distribution des données locales et distantes. Cela est important quand la sécurité, la confidentialité, la stratégie d’entreprise et les exigences réglementaires diffèrent d’un site à l’autre et d’un utilisateur à l’autre. 

![Architecture de la solution Données hiérarchisées à des fins analytiques](media/pattern-tiered-data-analytics/solution-architecture.png)

## <a name="components"></a>Composants

Cette solution utilise les composants suivants :

| Couche | Composant | Description |
|----------|-----------|-------------|
| Azure | Stockage | Un [compte de stockage Azure](/azure/storage/) fournit un point de terminaison de consommation des données stérile. Le Stockage Azure est une solution de stockage cloud de Microsoft pour les scénarios de stockage de données actuels. Le Stockage Azure offre un magasin d’objets hautement scalable pour les objets de données et un service de système de fichiers pour le cloud. Il fournit également une banque de messagerie qui offre une messagerie fiable, ainsi qu’un magasin NoSQL. |
| Azure Stack Hub | Stockage | Un [compte de stockage Azure Stack Hub](/azure-stack/user/azure-stack-storage-overview) est utilisé pour plusieurs services :<br>- **Stockage Blob** pour le stockage des données brutes. Le Stockage Blob peut stocker tout type de données texte ou binaires, par exemple un document, un fichier multimédia ou un programme d’installation d’application. Chaque objet blob est organisé sous un conteneur. Les conteneurs fournissent un moyen utile d’affecter des stratégies de sécurité à des groupes d’objets. Un compte de stockage peut contenir un nombre quelconque de conteneurs, et un conteneur peut contenir un nombre quelconque de blobs, jusqu’à la limite de capacité de 500 To du compte de stockage.<br>- **Stockage Blob** pour l’archivage des données. Il existe des avantages à un stockage à faible coût pour l’archivage des données froides. Parmi les exemples de données froides, citons les sauvegardes, le contenu multimédia, les données scientifiques, la conformité et les données d’archivage. En général, toutes les données qui sont rarement consultées sont considérées comme du stockage froid. Hiérarchisation des données en fonction d’attributs tels que la fréquence d’accès et la période de rétention. Les données client sont rarement consultées, mais elles nécessitent une latence et des performances similaires pour les données chaudes.<br>- **Stockage File d’attente** pour le stockage des données traitées. Le Stockage File d’attente fournit une messagerie cloud entre les composants d’application. Lors de la conception d’applications pour la mise à l’échelle, des composants d’application sont souvent découplés, de sorte qu’ils peuvent être mis à l’échelle indépendamment. Le Stockage File d’attente fournit une messagerie asynchrone pour la communication entre les composants des applications.  Qu’ils soient exécutés dans le cloud, sur le bureau, sur un serveur local ou sur un appareil mobile. Le stockage de files d’attente prend également en charge la gestion des tâches asynchrones et la création des flux de travail de processus. |
| | Azure Functions | Le service [Azure Functions](/azure/azure-functions/) est fourni par le fournisseur de ressources [Azure App Service sur Azure Stack Hub](/azure-stack/operator/azure-stack-app-service-overview). Azure Functions vous permet d’exécuter votre code dans un environnement serverless simple, pour exécuter un script ou du code en réponse à divers événements. Azure Functions effectue une mise à l’échelle pour répondre à la demande sans avoir à créer une machine virtuelle ni à publier une application web, à l’aide du langage de programmation choisi. Les fonctions sont utilisées par la solution aux fins suivantes :<br>- **Admission de données.**<br>- **Stérilisation des données.** Les fonctions déclenchées manuellement peuvent effectuer le traitement, le nettoyage et l’archivage planifiés des données. Il peut s’agir, par exemple, des nettoyages de liste des clients nocturnes et du traitement mensuel des rapports.|

## <a name="issues-and-considerations"></a>Problèmes et considérations

Tenez compte des points suivants lors du choix de l’implémentation de cette solution :

### <a name="scalability"></a>Extensibilité 

Les fonctions Azure et les solutions de stockage évoluent pour s’adapter au volume de données et aux exigences en matière de traitement. Pour des informations sur la scalabilité d’Azure et sur les cibles, consultez la [documentation sur la scalabilité du Stockage Azure](/azure/storage/common/storage-scalability-targets). 

### <a name="availability"></a>Disponibilité

Le stockage est la principale considération relative à la disponibilité pour ce modèle. La connexion via des liens rapides est requise pour le traitement et la distribution d’importants volumes de données. 

### <a name="manageability"></a>Facilité de gestion

La facilité de gestion de cette solution dépend des outils de création en cours d’utilisation et de l’engagement du contrôle de code source. 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les sujets abordés dans cet article :
- Consultez la documentation sur [Stockage Azure](/azure/storage/) et [Azure Functions](/azure/azure-functions/). Ce modèle utilise de façon intensive les comptes de stockage Azure et Azure Functions, à la fois sur Azure et Azure Stack Hub.
- Consultez [Considérations relatives à la conception des applications hybrides](overview-app-design-considerations.md) pour en savoir plus sur les meilleures pratiques, et pour obtenir les réponses à d’autres questions.
- Pour plus d’informations sur l’ensemble du portefeuille de produits et de solutions, consultez [Famille de produits et de solutions d’Azure Stack ](/azure-stack).

Lorsque vous êtes prêt à tester l’exemple de solution, poursuivez avec le [Guide de déploiement de la solution Données hiérarchisées à des fins analytiques](https://aka.ms/tiereddatadeploy). Ce guide de déploiement fournit des instructions pas à pas sur le déploiement et sur le test de ses composants.