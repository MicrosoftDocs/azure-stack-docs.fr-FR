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
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: e2d2df6c5791b6a05c31950e69fb58b5d48360b2
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308502"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Utiliser des bases de données MySQL sur Microsoft Azure Stack

Les bases de données MySQL, courantes sur les sites web, prennent en charge de nombreuses plateformes de sites web. Vous pouvez par exemple créer des sites web WordPress avec le module complémentaire fournisseur de ressources (PaaS) App Services.

Une fois que vous aurez déployé le fournisseur de ressources, vous pourrez :

* créer des bases de données et des serveurs MySQL avec des modèles de déploiement Azure Resource Manager ;
* proposer des bases de données MySQL en tant que service.  

## <a name="mysql-resource-provider-adapter-architecture"></a>Architecture de l’adaptateur de fournisseur de ressources MySQL

Le fournisseur de ressources comprend les éléments suivants :

* **La machine virtuelle de l’adaptateur du fournisseur de ressources MySQL**, une machine virtuelle Windows Server exécutant les services fournisseur.
* **Le fournisseur de ressources**, qui traite les requêtes et accède aux ressources de la base de données.
* **Les serveurs qui hébergent MySQL Server**, qui fournissent de la capacité pour les bases de données, appelés serveurs d’hébergement. Vous pouvez créer vous-même des instances MySQL ou bien accorder l’accès à des instances MySQL externes. La [Galerie de démarrage rapide Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) comporte un exemple de modèle permettant de :

  * Créez un serveur MySQL pour vous.
  * Télécharger et déployer un serveur MySQL sur la Place de marché Azure.

> [!NOTE]
> Les serveurs d’hébergement installés sur des systèmes intégrés Azure Stack doivent être créés à partir d’un abonnement de locataire. Ils ne peuvent pas être créés à partir de l’abonnement du fournisseur par défaut. Ils doivent être créés à partir du portail du locataire ou à partir d’une session PowerShell avec un nom de connexion approprié. Tous les serveurs d’hébergement sont des machines virtuelles facturables et doivent être sous licence. L’administrateur de service peut être le propriétaire de l’abonnement locataire.

### <a name="required-privileges"></a>Privilèges requis

Le compte système doit disposer des privilèges suivants :

* **Base de données :** créer, supprimer
* **Connexion :** créer, définir, supprimer, accorder, révoquer  

## <a name="next-steps"></a>Étapes suivantes

[Déployer le fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md)
