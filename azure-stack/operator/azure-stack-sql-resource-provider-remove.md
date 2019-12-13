---
title: Supprimer le fournisseur de ressources SQL
titleSuffix: Azure Stack
description: Découvrez comment supprimer le fournisseur de ressources SQL de votre déploiement Azure Stack.
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
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: a172b56c43dafd637a66fa8354c2e06e06a67b98
ms.sourcegitcommit: 08d2938006b743b76fba42778db79202d7c3e1c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74954449"
---
# <a name="remove-the-sql-resource-provider"></a>Supprimer le fournisseur de ressources SQL

Avant de supprimer le fournisseur de ressources SQL, vous devez supprimer toutes les dépendances de fournisseurs. Vous aurez également besoin d’une copie du package de déploiement qui a été utilisé pour installer le fournisseur de ressources.

> [!NOTE]
> Les liens de téléchargement des programmes d’installation du fournisseur de ressources sont disponibles dans les [Conditions préalables pour le déploiement du fournisseur de ressources](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Le fait de supprimer le fournisseur de ressources SQL ne supprime pas les bases de données locataires des serveurs d’hébergement.

## <a name="dependency-cleanup"></a>Nettoyage de la dépendance

Vous devez effectuer plusieurs tâches de nettoyage avant d’exécuter le script DeploySqlProvider.ps1 pour supprimer le fournisseur de ressources.

L’opérateur Azure Stack est responsable des tâches de nettoyage suivantes :

* Supprimer tous les plans qui référencent l’adaptateur SQL.
* Supprimer tous les quotas associés à l’adaptateur SQL.

## <a name="to-remove-the-sql-resource-provider"></a>Pour supprimer le fournisseur de ressources SQL

1. Vérifiez que vous avez supprimé toutes les dépendances existantes du fournisseur de ressources SQL.

   > [!NOTE]
   > La désinstallation du fournisseur de ressources SQL est effectuée même si des ressources dépendantes utilisent le fournisseur de ressources.
  
2. Obtenez une copie du package d’installation du fournisseur de ressources SQL, puis exécutez l’auto-extracteur pour extraire le contenu dans un répertoire temporaire.

3. Ouvrez une nouvelle fenêtre de console PowerShell avec élévation de privilèges, puis basculez vers le répertoire où vous avez extrait les fichiers d’installation du fournisseur de ressources SQL.

4. Exécutez le script DeploySqlProvider.ps1 à l’aide des paramètres suivants :

    * **Désinstaller** : Supprime le fournisseur de ressources et toutes les ressources associées.
    * **PrivilegedEndpoint** : Adresse IP ou nom DNS du point de terminaison privilégié.
    * **AzureEnvironment** : L’environnement Azure utilisé pour le déploiement d’Azure Stack. Nécessaire uniquement pour les déploiements Azure AD.
    * **CloudAdminCredential** : Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié.
    * **AzCredential** : Informations d’identification du compte d’administration de service Azure Stack. Utilisez les mêmes informations d’identification que celles utilisées pour le déploiement d’Azure Stack.

## <a name="next-steps"></a>Étapes suivantes

[Offrir App Services en tant que PaaS](azure-stack-app-service-overview.md)
