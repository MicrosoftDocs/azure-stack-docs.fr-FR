---
title: Gérer Key Vault dans Azure Stack Hub à l'aide du portail | Microsoft Docs
description: Apprenez à gérer Key Vault dans Azure Stack Hub à l'aide du portail Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: D4300668-461F-45F6-BF3B-33B502C39D17
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2020
ms.author: sethm
ms.lastreviewed: 1/10/2020
ms.openlocfilehash: 1ba2be2d37cfa0b0d850f67124440fb9de13a23f
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75879153"
---
# <a name="manage-key-vault-in-azure-stack-hub-using-the-portal"></a>Gérer Key Vault dans Azure Stack Hub à l'aide du portail

Cet article explique comment créer et gérer un coffre de clés dans Azure Stack Hub à l'aide du portail Azure Stack Hub.

## <a name="prerequisites"></a>Conditions préalables requises

Vous devez vous abonner à une offre qui inclut le service Azure Key Vault.

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

1. Connectez-vous au [portail utilisateur](https://portal.local.azurestack.external).

2. Sur le tableau de bord, sélectionnez **+ Créer une ressource**, **Sécurité + Identité** et **Coffre de clés**.

    ![Écran Coffre de clés](media/azure-stack-key-vault-manage-portal/image1.png)

3. Dans le volet **Créer un coffre de clés**, affectez un **Nom** à votre coffre. Les noms de coffre peuvent contenir uniquement des caractères alphanumériques et des traits d’union (-). Ils ne peuvent pas commencer par un chiffre.

4. Choisissez un **Abonnement** dans la liste des abonnements disponibles. Tous les abonnements qui offrent le service Key Vault sont affichés dans la liste déroulante.

5. Sélectionnez un **Groupe de ressources** existant ou créez-en un.

6. Sélectionnez le **Niveau tarifaire**. Dans le Kit de développement Azure Stack, les coffres de clés prennent uniquement en charge les références SKU **Standard**.

7. Choisissez une des **Stratégies d’accès** existantes ou créez-en une. Une stratégie d’accès vous permet d’accorder des autorisations à un utilisateur, à une app ou à un groupe de sécurité pour effectuer des opérations avec ce coffre.

8. Choisissez éventuellement une **Stratégie d’accès avancé** pour activer l’accès aux fonctionnalités. Par exemple : machines virtuelles (VM) pour le déploiement, Gestionnaire de ressources pour le déploiement d’un modèle et accès à Azure Disk Encryption pour le chiffrement de volumes.

9. Après avoir configuré les paramètres, sélectionnez **OK**, puis sélectionnez **Créer**. Cette étape lance le déploiement du coffre de clés.

## <a name="manage-keys-and-secrets"></a>Gérer les clés et les secrets

Après avoir créé un coffre de clés, procédez comme suit pour y créer et y gérer des clés et des secrets :

### <a name="create-a-key"></a>Créer une clé

1. Connectez-vous au portail utilisateur [Azure Stack Hub](https://portal.local.azurestack.external).

2. Dans le tableau de bord, sélectionnez **Toutes les ressources**, sélectionnez le coffre de clés que vous avez créé, puis sélectionnez la vignette **Clés**.

3. Dans le volet **Clés**, sélectionnez **Générer/importer**.

4. Dans le volet **Créer une clé**, dans la liste **Options**, choisissez la méthode que vous voulez utiliser pour créer une clé. Vous pouvez **Générer** une nouvelle clé, **Charger** une clé existante ou utiliser **Restaurer la sauvegarde** pour sélectionner une sauvegarde d’une clé.

5. Entrez un **Nom** pour votre clé. Le nom de la clé peut contenir uniquement des caractères alphanumériques et des traits d’union (-).

6. Si vous le souhaitez, configurez les valeurs **Définir la date d’activation** et **Définir la date d’expiration** pour votre clé.

7. Sélectionnez **Créer** pour démarrer le déploiement.

Une fois la clé créée, vous pouvez la sélectionner sous **Clés** et afficher ou modifier ses propriétés. La section Propriétés contient l’**identificateur de clé**, qui est un URI (Uniform Resource Identifier) dont se servent les apps externes pour accéder à cette clé. Pour limiter les opérations sur cette clé, configurez les paramètres sous **Opérations autorisées**.

![URI de clé](media/azure-stack-key-vault-manage-portal/image4.png)

### <a name="create-a-secret"></a>Créer un secret

1. Connectez-vous au [portail utilisateur](https://portal.local.azurestack.external).

2. Dans le tableau de bord, sélectionnez **Toutes les ressources**, sélectionnez le coffre de clés que vous avez créé, puis sélectionnez la vignette **Secrets**.

3. Sous **Secrets**, sélectionnez **Ajouter**.

4. Sous **Créer un secret**, accédez à la liste **Options de chargement** et choisissez l’option avec laquelle vous voulez créer un secret. Vous pouvez créer un secret **manuellement** en entrant une valeur pour le secret, ou en chargeant un **certificat** à partir de votre machine locale.

5. Entrez un **Nom** pour le secret. Le nom du secret peut contenir uniquement des caractères alphanumériques et des traits d’union (-).

6. Si vous le souhaitez, spécifiez le **Type de contenu** et configurez des valeurs pour **Définir la date d’activation** et **Définir la date d’expiration** pour le secret.

7. Sélectionnez **Créer** pour démarrer le déploiement.

Une fois le secret créé, vous pouvez le sélectionner sous **Secrets**, et afficher ou modifier ses propriétés. **L’identificateur de clé secrète** est un URI que les apps externes peuvent utiliser pour accéder à cette clé secrète.

![URI de secret](media/azure-stack-key-vault-manage-portal/image5.png)

## <a name="next-steps"></a>Étapes suivantes

* [Déployer une machine virtuelle en récupérant le mot de passe stocké dans Key Vault](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Déployer une machine virtuelle avec un certificat stocké dans un coffre de clés](azure-stack-key-vault-push-secret-into-vm.md)
