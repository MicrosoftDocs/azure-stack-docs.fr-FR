---
title: Présentation du stockage Azure Stack Hub
description: En savoir plus sur les services de stockage Azure Stack Hub.
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: ee08259248a49eef08ce0012d60be2705cc4f877
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77704911"
---
# <a name="introduction-to-azure-stack-hub-storage"></a>Présentation du stockage Azure Stack Hub

## <a name="overview"></a>Vue d’ensemble

Le stockage Azure Stack Hub est un ensemble de services de stockage cloud cohérents avec les services fournis par le stockage Azure. Ces services incluent des blobs, des tables et des files d’attente.

## <a name="azure-stack-hub-storage-services"></a>Services de stockage Azure Stack Hub

Le stockage Azure Stack Hub fournit les trois services suivants :

- **Stockage Blob**

    Le stockage Blob stocke les données d’objets non structurées. Un blob peut correspondre à n'importe quel type de données texte ou binaires, par exemple, un document, un fichier multimédia ou un programme d'installation d'applications.

- **Stockage Table**

    Le stockage de tables stocke les jeux de données structurés. Le stockage Table est un magasin de données de clés-attributs NoSQL qui permet le développement rapide et l'accès rapide à de grosses quantités de données.

- **Stockage de files d’attente**

    Le stockage de files d’attente fournit une messagerie fiable pour le traitement du workflow et pour la communication entre les composants des services cloud.

Un compte de stockage Azure Stack Hub est un compte sécurisé qui vous donne accès à des services dans le stockage Azure Stack Hub. Votre compte de stockage fournit les espaces de noms uniques pour vos ressources de stockage. Le schéma suivant montre les relations entre les ressources de stockage Azure Stack Hub dans un compte de stockage :

![Vue d’ensemble du stockage Azure Stack Hub](media/azure-stack-storage-overview/AzureStackStorageOverview.png)

### <a name="blob-storage"></a>Stockage d'objets blob

Pour les utilisateurs qui doivent stocker de grandes quantités de données d’objets non structurées dans le cloud, le stockage Blob offre une solution efficace et scalable. Utilisez le stockage blob pour stocker le contenu suivant :

- Documents
- Données sociales telles que photos, vidéos, musique et blogs
- Sauvegardes de fichiers, d'ordinateurs, de bases de données et d'appareils
- Images et textes pour applications web
- Données de configuration pour applications cloud
- Big Data tels que journaux d’activité et autres jeux de données volumineux

Chaque objet blob est organisé dans un conteneur. Les conteneurs fournissent également un moyen utile d'affecter des stratégies de sécurité à des groupes d'objets. Un compte de stockage peut contenir un nombre quelconque de conteneurs et un conteneur peut contenir un nombre quelconque de blobs (jusqu'à la limite du compte de stockage).

Le stockage Blob offre trois types d’objets blob :

- **Objets blob de blocs**

    Les objets blob de blocs sont optimisés pour la diffusion en continu et le stockage d’objets cloud. Il s’agit d’un bon choix pour stocker des documents, des fichiers multimédias, des sauvegardes et d’autres fichiers similaires.

- **Objets blob d’ajout**

    Les objets blob d’ajout sont similaires aux objets blob de blocs, mais ils sont optimisés pour les opérations d’ajout. Un objet blob d’ajout ne peut être mis à jour qu’en lui ajoutant un nouveau bloc à la fin. Les objets blob d’ajout sont idéaux pour des opérations telles que la journalisation, où les nouvelles données ne doivent être écrites qu’à la fin de l’objet blob.

- **Objets blob de pages**

    Les objets blob de pages sont optimisés pour la représentation des disques IaaS et la prise en charge des écritures aléatoires dont la taille peut atteindre jusqu’à 1 To. Un disque IaaS attaché à une machine virtuelle Azure Stack Hub est un disque dur virtuel stocké comme un objet blob de pages.

### <a name="table-storage"></a>Stockage de tables

Les applications modernes exigent souvent des magasins de données avec plus d’évolutivité et de souplesse que ne l’exigeaient les précédentes générations de logiciels. Le stockage Table offre un stockage hautement disponible et massivement évolutif, de telle sorte que votre application puisse être mise à l'échelle automatiquement pour répondre à la demande des utilisateurs. Le stockage Table est le magasin de clés/attributs NoSQL de Microsoft ; il a une conception sans schéma, ce en quoi il diffère des bases de données relationnelles classiques. Avec un magasin de données sans schéma, il est facile d'adapter vos données au fur et à mesure que les besoins de votre application évoluent. Facile à utiliser, le stockage Table permet aux développeurs de créer rapidement des applications.

Le stockage de tables est un magasin de clés/attributs, ce qui signifie que chaque valeur figurant dans une table est stockée avec un nom de propriété typé. Le nom de propriété est utilisé pour filtrer et spécifier des critères de sélection. Une collection de propriétés et leurs valeurs constituent une entité. Dans la mesure où le stockage de tables est sans schéma, deux entités d’une même table contiennent différentes collections de propriétés, et ces propriétés peuvent être de différents types.

Vous pouvez utiliser le stockage Table pour stocker des jeux de données flexibles, par exemple, des données utilisateur pour des applications web, des carnets d’adresses, des informations sur les périphériques et tout autre type de métadonnées requis par votre service. Pour les applications basées sur internet actuelles, les bases de données NoSQL, telles que le stockage Table, offrent une alternative populaire aux bases de données relationnelles classiques.

Un compte de stockage peut contenir un nombre quelconque de tables, et une table peut contenir un nombre quelconque d’entités, jusqu'à la limite de capacité du compte de stockage.

### <a name="queue-storage"></a>Stockage de files d'attente

Lors de la conception d’applications pour la mise à l’échelle, des composants d’application sont souvent découplés, de sorte qu’ils peuvent être mis à l’échelle indépendamment. Le stockage File d’attente offre une solution de messagerie fiable pour la communication asynchrone entre les composants d’application, qu’ils soient exécutés dans le cloud, sur le bureau, sur un serveur local ou sur un appareil mobile. Le Stockage File d'attente prend également en charge la gestion des tâches asynchrones et la création des workflows de processus.

Un compte de stockage peut contenir un nombre quelconque de files d’attente et une file d’attente peut contenir un nombre quelconque de messages (jusqu'à la limite de capacité du compte de stockage). La taille maximale des messages individuels est de 64 Ko.

## <a name="next-steps"></a>Étapes suivantes

- [Stockage Azure : Différences et considérations](azure-stack-acs-differences.md)

- Pour en savoir plus sur le stockage Azure, consultez [Présentation du stockage Microsoft Azure](/azure/storage/common/storage-introduction)
