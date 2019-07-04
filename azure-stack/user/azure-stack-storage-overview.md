---
title: Présentation du stockage Azure Stack
description: En savoir plus sur le stockage Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 9abfeecfba0e159a5e402bb5d49a58e0cd2945e2
ms.sourcegitcommit: ca46bef5d5f824d22bdbc00605eb881410b1ffd0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67041987"
---
# <a name="introduction-to-azure-stack-storage"></a>Présentation du stockage Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

## <a name="overview"></a>Vue d'ensemble

Le stockage Azure Stack est un ensemble de services de stockage cloud incluant des objets blob, des tables et des files d’attente qui sont compatibles avec les services Stockage Azure.

## <a name="azure-stack-storage-services"></a>Services de stockage Azure Stack

Le stockage Azure Stack fournit les trois services suivants :

- **Stockage d’objets blob**

    Le stockage Blob stocke les données d’objets non structurées. Un objet blob peut correspondre à n'importe quel type de données texte ou binaires, par exemple, un document, un fichier multimédia ou un programme d'installation d'application.

- **Stockage de table**

    Le stockage de tables stocke les jeux de données structurés. Le stockage de tables est un magasin de données de clés-attributs NoSQL qui permet le développement rapide et l'accès rapide à de grosses quantités de données.

- **Stockage de files d’attente**

    Le stockage de files d’attente fournit une messagerie fiable pour le traitement du workflow et pour la communication entre les composants des services cloud.

Un compte de stockage Azure Stack est un compte sécurisé qui vous donne accès à des services dans le stockage Azure Stack. Votre compte de stockage fournit les espaces de noms uniques pour vos ressources de stockage. Le diagramme suivant montre les relations entre les ressources de stockage Azure Stack dans un compte de stockage :

![Vue d’ensemble du stockage Azure Stack](media/azure-stack-storage-overview/AzureStackStorageOverview.png)

### <a name="blob-storage"></a>Stockage d'objets blob

Pour les utilisateurs qui doivent stocker de grandes quantités de données d’objets non structurées dans le cloud, le stockage Blob offre une solution efficace et scalable. Vous pouvez utiliser le stockage blob pour stocker les contenus suivants :

- Documents
- Données sociales telles que photos, vidéos, musique et blogs
- Sauvegardes de fichiers, d'ordinateurs, de bases de données et d'appareils
- Images et textes pour applications Web
- Données de configuration pour applications cloud
- Big Data tels que journaux d’activité et autres jeux de données volumineux

Chaque objet blob est organisé dans un conteneur. Les conteneurs fournissent également un moyen utile d'affecter des stratégies de sécurité à des groupes d'objets. Un compte de stockage peut contenir un nombre quelconque de conteneurs, et un conteneur peut contenir un nombre quelconque d'objets blob, jusqu'à la limite du compte de stockage.

Le stockage Blob offre trois types d’objets blob :

- **Objets blob de blocs**

    Les objets blob de blocs sont optimisés pour la diffusion en continu et le stockage d’objets cloud. Ils constituent une solution de choix pour stocker des documents, des fichiers multimédias, des sauvegardes, etc.

- **Objets blob d’ajout**

    Les objets blob d’ajout sont similaires aux objets blob de blocs, mais ils sont optimisés pour les opérations d’ajout. Un objet blob d’ajout ne peut être mis à jour qu’en lui ajoutant un nouveau bloc à la fin. Les objets blob d’ajout sont idéaux pour des opérations telles que la journalisation, où les nouvelles données ne doivent être écrites qu’à la fin de l’objet blob.

- **Objets blob de pages**

    Les objets blob de pages sont optimisés pour la représentation des disques IaaS et la prise en charge des écritures aléatoires dont la taille peut atteindre jusqu’à 1 To. Un disque IaaS attaché à une machine virtuelle Azure Stack est un disque dur virtuel stocké comme un objet blob de pages.

### <a name="table-storage"></a>Stockage de tables

Les applications modernes exigent souvent des magasins de données avec plus d'évolutivité et de souplesse que ne l'exigeaient les précédentes générations de logiciels. Le stockage de tables offre un stockage hautement disponible et massivement évolutif, de telle sorte que votre application puisse être mise à l'échelle automatiquement pour répondre à la demande des utilisateurs. Le Stockage Table est le magasin de clés/attributs NoSQL de Microsoft ; il a une conception sans schéma, ce en quoi il diffère des bases de données relationnelles classiques. Avec un magasin de données sans schéma, il est facile d'adapter vos données au fur et à mesure que les besoins de votre application évoluent. Facile à utiliser, le stockage de tables permet aux développeurs de créer rapidement des applications.

Le stockage de tables est un magasin de clés/attributs, ce qui signifie que chaque valeur figurant dans une table est stockée avec un nom de propriété typé. Le nom de propriété peut être utilisé pour filtrer et spécifier des critères de sélection. Une collection de propriétés et leurs valeurs constituent une entité. Dans la mesure où le stockage de tables est sans schéma, deux entités d’une même table contiennent différentes collections de propriétés, et ces propriétés peuvent être de différents types.

Vous pouvez utiliser le stockage de tables pour stocker des jeux de données flexibles, par exemple, des données utilisateur pour des applications Web, des carnets d’adresses, des informations sur les périphériques et tout autre type de métadonnées requis par votre service. Pour les applications Internet actuelles, les bases de données NoSQL telles que le stockage de tables offrent une alternative populaire aux bases de données relationnelles classiques.

Un compte de stockage peut contenir un nombre quelconque de tables, et une table peut contenir un nombre quelconque d’entités, jusqu'à la limite de capacité du compte de stockage.

### <a name="queue-storage"></a>Stockage de files d'attente

Lors de la conception d'applications pour la mise à l'échelle, des composants d'application sont souvent découplés, de sorte qu'ils peuvent être mis à l'échelle indépendamment. Le stockage de files d’attente offre une solution de messagerie fiable pour la communication asynchrone entre les composants d’application, qu’ils soient exécutés dans le cloud, sur le bureau, sur un serveur local ou sur un appareil mobile. Le Stockage File d'attente prend également en charge la gestion des tâches asynchrones et la création des workflows de processus.

Un compte de stockage peut contenir un nombre quelconque de files d’attente, et une file d’attente peut contenir un nombre quelconque de messages, jusqu'à la limite de capacité du compte de stockage. La taille maximale des messages individuels est de 64 Ko.

## <a name="next-steps"></a>Étapes suivantes

- [Stockage Azure : Différences et considérations](azure-stack-acs-differences.md)

- Pour en savoir plus sur le stockage Azure, consultez [Présentation du stockage Microsoft Azure](/azure/storage/common/storage-introduction)
