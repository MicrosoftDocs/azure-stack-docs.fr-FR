---
title: Stocker les informations d’identification du principal de service dans Azure Stack Key Vault | Microsoft Docs
description: Découvrez comment Key Vault stocke les informations d’identification du principal de service sur Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2019
ms.author: sethm
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: efa8dda8061ce81d751e9cce47c5e81a3917f2bf
ms.sourcegitcommit: ad2f2cb4dc8d5cf0c2c37517d5125921cff44cdd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67138839"
---
# <a name="store-service-principal-credentials-in-azure-stack-key-vault"></a>Stocker les informations d’identification du principal de service dans Azure Stack Key Vault

Le développement d’applications sur Azure Stack nécessite généralement la création d’un principal de service et l’utilisation de ces identifiants pour s’authentifier avant le déploiement. Toutefois, les informations d’identification stockées pour le principal de service sont parfois égarées. Cet article explique comment créer un principal de service et stocker les valeurs dans Azure Key Vault en vue de leur récupération ultérieure.

Pour plus d’informations sur Key Vault, consultez [cet article](azure-stack-key-vault-intro.md).

## <a name="prerequisites"></a>Prérequis

- Abonnement à une offre qui inclut le service Azure Key Vault.
- PowerShell est configuré pour une utilisation avec Azure Stack.

## <a name="key-vault-in-azure-stack"></a>Key Vault dans Azure Stack

Key Vault dans Azure Stack permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Key Vault vous permet de chiffrer des clés et des secrets.

Pour créer un coffre de clés, procédez comme suit :

1. Connectez-vous au portail Azure Stack.

2. Sur le tableau de bord, sélectionnez **+ Créer une ressource**, **Sécurité + Identité**, puis **Coffre de clés**.

   ![Création d’un coffre de clés](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. Dans le volet **Créer un coffre de clés**, affectez un **Nom** à votre coffre. Les noms de coffre peuvent contenir uniquement des caractères alphanumériques et des traits d’union (-). Ils ne peuvent pas commencer par un chiffre.

4. Choisissez un abonnement dans la liste des abonnements disponibles.

5. Sélectionnez un groupe de ressources existant ou créez-en un.

6. Sélectionnez le niveau tarifaire.

7. Choisissez une des stratégies d’accès existantes ou créez-en une. Une stratégie d’accès vous donne les moyens d’accorder des autorisations à un utilisateur, à une application ou à un groupe de sécurité pour effectuer des opérations avec ce coffre.

8. Choisissez éventuellement une stratégie d’accès avancé pour activer l’accès aux fonctionnalités.

9. Après avoir configuré les paramètres, sélectionnez **OK**, puis sélectionnez **Créer**. Le déploiement du coffre de clés commence.

## <a name="create-a-service-principal"></a>Créer un principal du service

1. Connectez-vous à votre compte Azure via le portail Azure.

2. Sélectionnez **Azure Active Directory**, puis **Inscription des applications** et **Ajouter**.

3. Fournissez un nom et une URL pour l’application. Sélectionnez **Application Web/API** ou **Native** pour le type d’application que vous souhaitez créer. Après avoir défini les valeurs, sélectionnez **Créer**.

4. Sélectionnez **Active Directory**, puis **Inscriptions d’applications** et sélectionnez ensuite votre application.

5. Copiez l’**ID d’application** et stockez-le dans votre code d’application. Les exemples d’applications utilisent un **ID de client** pour faire référence à l’**ID d’application**.

6. Pour générer une clé d’authentification, sélectionnez **Clés**.

7. Fournissez une description et une durée pour la clé.

8. Sélectionnez **Enregistrer**.

9. Copiez la **clé** qui devient disponible après avoir cliqué sur **Enregistrer**.

## <a name="store-the-service-principal-inside-key-vault"></a>Stocker le principal de service dans Key Vault

1. Connectez-vous au portail utilisateur pour Azure Stack, puis sélectionnez le coffre de clés que vous avez créé précédemment, puis sélectionnez la vignette **Secret**.

2. Dans le volet **Secret**, sélectionnez **Générer/importer**.

3. Dans la liste d’options du volet **Créer un secret**, sélectionnez **Manuel**. Si vous avez créé le principal de service à l’aide de certificats, sélectionnez les certificats dans la liste déroulante, puis chargez le fichier.

4. Entrez **l’ID d’application** copié à partir du principal de service en tant que nom de votre clé. Le nom de la clé ne peut contenir que des caractères alphanumériques et des traits d'union (-).

5. Collez la valeur de votre clé de principal de service dans l’onglet **Valeur**.

6. Sélectionnez **Principal du service** comme **Type de contenu**.

7. Définissez les valeurs de **date d’activation** et de **date d’expiration** pour votre clé.

8. Sélectionnez **Créer** pour démarrer le déploiement.

Une fois le secret créé, les informations du principal de service y sont stockées. Vous pouvez le sélectionner à tout moment sous **Secrets**, et afficher ou modifier ses propriétés. La section Propriétés contient l’identificateur de secret, qui est un URI par lequel les applications externes peuvent accéder à ce secret.

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser les principaux de service](azure-stack-create-service-principals.md)
- [Gérer Key Vault dans Azure Stack via le portail](azure-stack-key-vault-manage-portal.md)  
- [Gérer Key Vault dans Azure Stack avec PowerShell](azure-stack-key-vault-manage-powershell.md)