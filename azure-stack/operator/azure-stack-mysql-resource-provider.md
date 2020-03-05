---
title: Utiliser des bases de données MySQL en tant que PaaS sur Azure Stack Hub
description: Découvrez comment déployer le fournisseur de ressources MySQL et fournir des bases de données MySQL en tant que service sur Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2019
ms.openlocfilehash: 73c916a193492662cdbb3ba20031c58932721053
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698587"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack-hub"></a>Utiliser des bases de données MySQL sur Microsoft Azure Stack Hub

Utilisez le fournisseur de ressources MySQL pour fournir des bases de données MySQL sur [Azure Stack Hub](azure-stack-overview.md). Après avoir déployé le fournisseur de ressources et l’avoir connecté à une ou plusieurs instances de serveur MySQL, vous pouvez créer :

* des bases de données MySQL pour les applications cloud natives ;
* des bases de données MySQL pour les applications web.  

Il existe plusieurs limitations à prendre en compte avant d’installer le fournisseur de ressources MySQL :

- Les utilisateurs peuvent uniquement créer et gérer des bases de données individuelles. L’instance de serveur de base de données n’est pas accessible aux utilisateurs finaux. Cela peut limiter la compatibilité avec les applications de base de données locales qui doivent accéder à la base de données master, temporaire, ou aux bases de données gérées de manière dynamique.
- Votre opérateur Azure Stack Hub est responsable du déploiement, de la mise à jour, de la sécurisation, de la configuration et de la maintenance des serveurs et hôtes de base de données MySQL. Le service RP ne fournit aucune fonctionnalité de gestion des instances de serveur de base de données ni d’hôte. 
- Les bases de données de différents utilisateurs dans divers abonnements peuvent se trouver sur la même instance de serveur de base de données. Le RP ne fournit aucun mécanisme pour isoler les bases de données sur des hôtes ou des instances de serveur de base de données différents.
- Le RP ne fournit pas de rapports sur l’utilisation des bases de données par les locataires.

## <a name="mysql-resource-provider-adapter-architecture"></a>Architecture de l’adaptateur de fournisseur de ressources MySQL

Le fournisseur de ressources comprend les éléments suivants :

* **La machine virtuelle de l’adaptateur du fournisseur de ressources MySQL**, une machine virtuelle Windows Server exécutant les services fournisseur.
* **Le fournisseur de ressources**, qui traite les requêtes et accède aux ressources de la base de données.
* **Les serveurs qui hébergent MySQL Server**, qui fournissent de la capacité pour les bases de données, appelés serveurs d’hébergement. Vous pouvez créer vous-même des instances MySQL ou bien accorder l’accès à des instances MySQL externes. La [Galerie de démarrage rapide Azure Stack Hub](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) comporte un exemple de modèle permettant d’effectuer les opérations suivantes :

  * Créez un serveur MySQL pour vous.
  * Téléchargez et déployez un serveur MySQL à partir de la Place de marché Azure.

> [!NOTE]
> Les serveurs d’hébergement installés sur des systèmes intégrés Azure Stack Hub doivent être créés à partir d’un abonnement de locataire. Ils ne peuvent pas être créés à partir de l’abonnement du fournisseur par défaut. Ils doivent être créés à partir du portail utilisateur ou à partir d’une session PowerShell avec une connexion appropriée. Tous les serveurs d’hébergement sont des machines virtuelles facturables et doivent être sous licence. L’administrateur de service peut être le propriétaire de l’abonnement locataire.

### <a name="required-privileges"></a>Privilèges requis

Le compte système doit disposer des privilèges suivants :

* **Base de données :** créer, supprimer
* **Connexion :** créer, définir, supprimer, accorder, révoquer  

## <a name="next-steps"></a>Étapes suivantes

[Déployer le fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md)
