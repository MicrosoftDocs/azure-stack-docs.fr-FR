---
title: Supprimer le fournisseur de ressources MySQL dans Azure Stack Hub
description: Découvrez comment supprimer le fournisseur de ressources MySQL de votre déploiement Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2
ms.openlocfilehash: 78dbba26d13914fe54111b315a0f01c48d53a26a
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698672"
---
# <a name="remove-the-mysql-resource-provider-in-azure-stack-hub"></a>Supprimer le fournisseur de ressources MySQL dans Azure Stack Hub

Avant de supprimer le fournisseur de ressources MySQL, vous devez supprimer toutes les dépendances de fournisseurs. Vous aurez également besoin d’une copie du package de déploiement qui a été utilisé pour installer le fournisseur de ressources.

> [!NOTE]
> Les liens de téléchargement des programmes d’installation du fournisseur de ressources sont disponibles dans les [Conditions préalables pour le déploiement du fournisseur de ressources](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Le fait de supprimer le fournisseur de ressources MySQL supprime les plans et les quotas associés gérés par l’opérateur. Mais cela ne supprime pas les bases de données de locataires des serveurs d’hébergement.

## <a name="to-remove-the-mysql-resource-provider"></a>Pour supprimer le fournisseur de ressources MySQL

1. Vérifiez que vous avez supprimé toutes les dépendances existantes du fournisseur de ressources MySQL.

   > [!NOTE]
   > La désinstallation du fournisseur de ressources MySQL est effectuée même si des ressources dépendantes utilisent le fournisseur de ressources.
  
2. Obtenez une copie du package d’installation du fournisseur de ressources MySQL, puis exécutez l’auto-extracteur pour extraire le contenu dans un répertoire temporaire.
3. Ouvrez une nouvelle fenêtre de console PowerShell avec élévation de privilèges, puis basculez vers le répertoire où vous avez extrait les fichiers d’installation du fournisseur de ressources MySQL.
4. Exécutez le script DeployMySqlProvider.ps1 à l’aide des paramètres suivants :
    - **Désinstaller** : Supprime le fournisseur de ressources et toutes les ressources associées.
    - **PrivilegedEndpoint** : Adresse IP ou nom DNS du point de terminaison privilégié.
    - **AzureEnvironment** : Environnement Azure utilisé pour le déploiement d’Azure Stack Hub. Nécessaire uniquement pour les déploiements Azure AD.
    - **CloudAdminCredential** : Informations d’identification de l’administrateur du cloud, nécessaires pour accéder au point de terminaison privilégié.
    - **DirectoryTenantID**
    - **AzCredential** : Informations d’identification du compte d’administration de service Azure Stack Hub. Utilisez les mêmes informations d’identification que celles utilisées pour le déploiement d’Azure Stack Hub.

## <a name="next-steps"></a>Étapes suivantes

[Offrir App Services en tant que PaaS](azure-stack-app-service-overview.md)
