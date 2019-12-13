---
title: Utiliser des bases de données MySQL en tant que PaaS sur Azure Stack | Microsoft Docs
description: Découvrez comment déployer le fournisseur de ressources MySQL et fournir des bases de données MySQL en tant que service sur Azure Stack.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: b66a72ce872d64f8fde3cb80ced5e6ad33d80b4d
ms.sourcegitcommit: d619612f54eeba3231ed73ed149ff894f9bf838a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74993782"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Utiliser des bases de données MySQL sur Microsoft Azure Stack

Les bases de données MySQL, courantes sur les sites web, prennent en charge de nombreuses plateformes de sites web. Vous pouvez par exemple créer des sites web WordPress avec le module complémentaire fournisseur de ressources (PaaS) App Services.

Après avoir déployé le fournisseur de ressources et l’avoir connecté à une ou plusieurs instances de serveur MySQL, vous pouvez :

* créer des bases de données MySQL avec des modèles de déploiement Azure Resource Manager ;
* proposer des bases de données MySQL en tant que service.  

Il existe plusieurs limitations à prendre en compte avant d’installer le fournisseur de ressources MySQL :

- Les utilisateurs peuvent uniquement créer et gérer des bases de données individuelles. L’instance de serveur de base de données n’est pas accessible aux utilisateurs finaux. Cela peut limiter la compatibilité avec les applications de base de données locales qui doivent accéder à la base de données master, temporaire, ou aux bases de données gérées de manière dynamique.
- Votre opérateur Azure Stack est responsable du déploiement, de la mise à jour, de la sécurisation, de la configuration et de la maintenance des serveurs et hôtes de base de données MySQL. Le service RP ne fournit aucune fonctionnalité de gestion des instances de serveur de base de données ni d’hôte. 
- Les bases de données de différents utilisateurs dans divers abonnements peuvent se trouver sur la même instance de serveur de base de données. Le RP ne fournit aucun mécanisme pour isoler les bases de données sur des hôtes ou des instances de serveur de base de données différents.
- Le RP ne fournit pas de rapports sur l’utilisation des bases de données par les locataires.

## <a name="mysql-resource-provider-adapter-architecture"></a>Architecture de l’adaptateur de fournisseur de ressources MySQL

Le fournisseur de ressources comprend les éléments suivants :

* **La machine virtuelle de l’adaptateur du fournisseur de ressources MySQL**, une machine virtuelle Windows Server exécutant les services fournisseur.
* **Le fournisseur de ressources**, qui traite les requêtes et accède aux ressources de la base de données.
* **Les serveurs qui hébergent MySQL Server**, qui fournissent de la capacité pour les bases de données, appelés serveurs d’hébergement. Vous pouvez créer vous-même des instances MySQL ou bien accorder l’accès à des instances MySQL externes. La [Galerie de démarrage rapide Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) comporte un exemple de modèle permettant de :

  * Créez un serveur MySQL pour vous.
  * Téléchargez et déployez un serveur MySQL à partir de la Place de marché Azure.

> [!NOTE]
> Les serveurs d’hébergement installés sur des systèmes intégrés Azure Stack doivent être créés à partir d’un abonnement de locataire. Ils ne peuvent pas être créés à partir de l’abonnement du fournisseur par défaut. Ils doivent être créés à partir du portail du locataire ou à partir d’une session PowerShell avec un nom de connexion approprié. Tous les serveurs d’hébergement sont des machines virtuelles facturables et doivent être sous licence. L’administrateur de service peut être le propriétaire de l’abonnement locataire.

### <a name="required-privileges"></a>Privilèges requis

Le compte système doit disposer des privilèges suivants :

* **Base de données :** créer, supprimer
* **Connexion :** créer, définir, supprimer, accorder, révoquer  

## <a name="next-steps"></a>Étapes suivantes

[Déployer le fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md)
