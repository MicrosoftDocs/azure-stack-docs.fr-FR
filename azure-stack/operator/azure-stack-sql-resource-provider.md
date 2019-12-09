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
ms.openlocfilehash: d2ce6c0af2912a2658db80301c9a64c8e3d5c066
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780726"
---
# <a name="use-sql-databases-on-azure-stack"></a>Utiliser des bases de données SQL dans Azure Stack

Utilisez le fournisseur de ressources SQL Server pour offrir des bases de données SQL en tant que service dans [Azure Stack](azure-stack-overview.md). Une fois le fournisseur de ressources installé et connecté à une ou plusieurs instances de SQL Server, vous et vos utilisateurs pouvez créer :

- des bases de données pour les applications cloud natives ;
- des sites Web qui utilisent SQL ;
- des charges de travail qui utilisent SQL.

Le fournisseur de ressources ne fournit pas toutes les fonctionnalités de gestion de base de données d’[Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Par exemple, les pools élastiques qui allouent automatiquement des ressources ne sont pas pris en charge. Toutefois, le fournisseur de ressources prend en charge des opérations CRUD (créer, lire, mettre à jour et supprimer) similaires sur une base de données SQL Server.

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
