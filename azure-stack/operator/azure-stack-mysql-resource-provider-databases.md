---
title: Utilisation des bases de données fournies par le fournisseur de ressources de l’adaptateur MySQL sur Azure Stack | Microsoft Docs
description: Guide pratique pour créer et gérer des bases de données MySQL provisionnées avec le fournisseur de ressources de l’adaptateur MySQL
services: azure-stack
documentationCenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 02/12/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.reviewer: quying
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 6eaba728b794c0102ec4e28791b218efa28b51b5
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64309389"
---
# <a name="create-mysql-databases"></a>Créer des bases de données MySQL
Un utilisateur Azure Stack abonné à une offre comprenant le service de base de données MySQL peut créer et gérer des bases de données MySQL libre-service dans le portail de l’utilisateur.

## <a name="create-a-mysql-database"></a>Créer une base de données MySQL

1. Connectez-vous au portail utilisateur Azure Stack.
2. Cliquez sur **+ Créer une ressource** > **Données + Stockage** > **Base de données MySQL** > **Ajouter**.
3. Sous **Créer une base de données MySQL**, entrez le nom de la base de données, puis configurez les autres paramètres en fonction des besoins de votre environnement.

    ![Créer une base de données MySQL test](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Sous **Créer une base de données**, sélectionnez **Référence (SKU)**. Sous **Sélectionner une référence (SKU) MySQL**, choisissez la référence de votre base de données.

    ![Sélectionner une référence (SKU) MySQL](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >Quand ils sont ajoutés à Azure Stack, les serveurs d’hébergement se voient affectés une référence (SKU). Les bases de données sont créées dans ce pool de serveurs d’hébergement d’une référence (SKU).

5. Sous **Connexion**, sélectionnez ***Configurer les paramètres requis***.
6. Sous **Sélectionner une connexion**, vous pouvez choisir une connexion existante ou sélectionner **+ Créer une connexion** pour configurer une nouvelle connexion.  Entrez un nom et un **Mot de passe** de **Connexion à la base de données**, puis sélectionnez **OK**.

    ![Créer une connexion de base de données](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >La longueur du nom de connexion à la base de données ne peut pas dépasser 32 caractères dans MySQL 5.7. Dans les éditions antérieures, il ne devait pas dépasser 16 caractères.

7. Sélectionnez **Créer** pour terminer la configuration de la base de données.

Une fois la base de données déployée, notez la **Chaîne de connexion** sous **Bases**. Vous pouvez utiliser cette chaîne dans n’importe quelle application qui doit accéder à la base de données MySQL.

![Obtenir la chaîne de connexion pour la base de données MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>Mettre à jour le mot de passe d’administration

Vous pouvez modifier le mot de passe en le changeant sur l’instance du serveur MySQL.

1. Sélectionnez **RESSOURCES ADMINISTRATIVES** > **Serveurs d’hébergement MySQL**. Sélectionnez le serveur d’hébergement.
2. Sous **Paramètres**, sélectionnez **Mot de passe**.
3. Sous **Mot de passe**, entrez le nouveau mot de passe, puis sélectionnez **Enregistrer**.

![Mettre à jour le mot de passe de l’administrateur](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>Étapes suivantes

[Mettre à jour le fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-update.md)

<!-- Update_Description: wording update -->