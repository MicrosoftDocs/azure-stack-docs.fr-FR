---
title: Utiliser des bases de données SQL
titleSuffix: Azure Stack
description: Découvrez comment utiliser le fournisseur de ressources SQL Server pour offrir des bases de données SQL en tant que service dans Azure Stack.
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
ms.openlocfilehash: 9e7800c018816155205569397520271fb86c4c12
ms.sourcegitcommit: dc3d0b77ee77742525fa0cd72d8547d25393022f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/26/2019
ms.locfileid: "75492760"
---
# <a name="use-sql-databases-on-azure-stack"></a>Utiliser des bases de données SQL dans Azure Stack

Utilisez le fournisseur de ressources SQL pour offrir des bases de données SQL en tant que service dans [Azure Stack](azure-stack-overview.md). Une fois le fournisseur de ressources installé et connecté à une ou plusieurs instances de SQL Server, vous et vos utilisateurs pouvez créer :

- des bases de données pour les applications cloud natives ;
- des sites Web qui utilisent SQL ;
- des charges de travail qui utilisent SQL.

Il existe plusieurs limitations à prendre en compte avant d’installer le fournisseur de ressources SQL :

- Les utilisateurs peuvent uniquement créer et gérer des bases de données individuelles. L’instance de serveur de base de données n’est pas accessible aux utilisateurs finaux. Cela peut limiter la compatibilité avec les applications de base de données locales qui doivent accéder à la base de données master, temporaire, ou aux bases de données gérées de manière dynamique.
- Votre opérateur Azure Stack est responsable du déploiement, de la mise à jour, de la sécurisation, de la configuration et de la maintenance des serveurs et hôtes de base de données SQL. Le service RP ne fournit aucune fonctionnalité de gestion des instances de serveur de base de données ni d’hôte. 
- Les bases de données de différents utilisateurs dans divers abonnements peuvent se trouver sur la même instance de serveur de base de données. Le RP ne fournit aucun mécanisme pour isoler les bases de données sur des hôtes ou des instances de serveur de base de données différents.
- Le RP ne fournit pas de rapports sur l’utilisation des bases de données par les locataires.

## <a name="sql-resource-provider-adapter-architecture"></a>Architecture de l’adaptateur de fournisseur de ressources SQL

Le fournisseur de ressources est constitué des composants suivants :

- **La machine virtuelle de l’adaptateur de fournisseur de ressources SQL**, qui est une machine virtuelle Windows Server exécutant les services de fournisseur.
- **Le fournisseur de ressources**, qui traite les requêtes et accède aux ressources de la base de données.
- **Les serveurs qui hébergent SQL Server**, qui offrent de la capacité pour les bases de données, et qui sont appelés serveurs d’hébergement.

Vous devez créer au moins une instance de SQL Server ou fournir un accès aux instances SQL Server externes.

> [!NOTE]
> Les serveurs d’hébergement installés sur des systèmes intégrés Azure Stack doivent être créés à partir d’un abonnement de locataire. Ils ne peuvent pas être créés à partir de l’abonnement du fournisseur par défaut. Ils doivent être créés à partir du portail du locataire, ou en utilisant PowerShell avec le nom de connexion approprié. Tous les serveurs d’hébergement sont des machines virtuelles facturables et doivent être sous licence. L’administrateur de service peut être le propriétaire de l’abonnement locataire.

## <a name="next-steps"></a>Étapes suivantes

[Déployer le fournisseur de ressources SQL Server](azure-stack-sql-resource-provider-deploy.md)
