---
title: Stocker les informations d’identification du principal de service dans un Key Vault Azure Stack Hub
description: Découvrez comment Key Vault stocke les informations d’identification du principal de service sur Azure Stack Hub
author: sethmanheim
ms.topic: article
ms.date: 06/09/2020
ms.author: sethm
ms.lastreviewed: 01/16/2020
ms.openlocfilehash: c17ba69fedfd51681c5038efde591115ce54bddf
ms.sourcegitcommit: 0aa5f7f20690839661c8bb3bfdbe32f82bec0c64
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86567516"
---
# <a name="store-service-principal-credentials-in-azure-stack-hub-key-vault"></a>Stocker les informations d’identification du principal de service dans un Key Vault Azure Stack Hub

Le développement d’applications sur Azure Stack Hub nécessite généralement la création d’un principal de service et l’utilisation des informations d’identification de celui-ci pour s’authentifier avant le déploiement. Toutefois, les informations d’identification stockées pour le principal de service sont parfois égarées. Cet article explique comment créer un principal de service et stocker les valeurs dans Azure Key Vault en vue de leur récupération ultérieure.

Pour plus d’informations sur le Key Vault, voir [Introduction à Key Vault dans Azure Stack Hub](azure-stack-key-vault-intro.md).

## <a name="prerequisites"></a>Conditions préalables requises

- Abonnement à une offre qui inclut le service Azure Key Vault.
- PowerShell installé et configuré pour une utilisation avec Azure Stack Hub.

## <a name="key-vault-in-azure-stack-hub"></a>Key Vault dans Azure Stack Hub

Un Key Vault dans Azure Stack Hub permet de protéger les clés de chiffrement et les secrets qu’utilisent les services et applications cloud. Key Vault vous permet de chiffrer des clés et des secrets.

Pour créer un coffre de clés, procédez comme suit :

1. Connectez-vous au portail Azure Stack Hub.

2. Sur le tableau de bord, sélectionnez **+ Créer une ressource**, **Sécurité + Identité**, puis **Coffre de clés**.

   ![Création d’un coffre de clés](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. Dans le volet **Créer un coffre de clés**, affectez un **Nom** à votre coffre. Les noms de coffre peuvent contenir uniquement des caractères alphanumériques et des traits d’union (-). Ils ne peuvent pas commencer par un chiffre.

4. Choisissez un abonnement dans la liste des abonnements disponibles.

5. Sélectionnez un groupe de ressources existant ou créez-en un.

6. Sélectionnez le niveau tarifaire.

7. Choisissez l’une des stratégies d’accès existantes ou créez-en une. Une stratégie d’accès vous donne les moyens d’accorder des autorisations à un utilisateur, à une application ou à un groupe de sécurité pour effectuer des opérations avec ce coffre.

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

1. Connectez-vous au portail utilisateur pour Azure Stack Hub, sélectionnez le coffre de clés que vous avez créé précédemment, puis sélectionnez la vignette **Secret**.

2. Dans le volet **Secret**, sélectionnez **Générer/importer**.

3. Dans la liste d’options du volet **Créer un secret**, sélectionnez **Manuel**. Si vous avez créé le principal de service à l’aide de certificats, sélectionnez les certificats dans la liste déroulante, puis chargez le fichier.

4. Entrez l’**ID d’application** copié à partir du principal de service en tant que nom de votre clé. Le nom de la clé peut contenir uniquement des caractères alphanumériques et des traits d’union (-).

5. Collez la valeur de votre clé de principal de service dans l’onglet **Valeur**.

6. Sélectionnez **Principal du service** comme **Type de contenu**.

7. Définissez les valeurs de **date d’activation** et de **date d’expiration** pour votre clé.

8. Sélectionnez **Créer** pour démarrer le déploiement.

Une fois le secret créé, les informations du principal de service y sont stockées. Vous pouvez le sélectionner à tout moment sous **Secrets**, et afficher ou modifier ses propriétés. La section **Propriétés** contient l’identificateur de secret, qui est un URI par lequel les applications externes peuvent accéder à ce secret.

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser les principaux de service](../operator/azure-stack-create-service-principals.md?view=azs-2002)
- [Gérer Key Vault dans Azure Stack Hub par le biais du portail](azure-stack-key-vault-manage-portal.md)  
- [Gérer Key Vault dans Azure Stack Hub à l’aide de PowerShell](azure-stack-key-vault-manage-powershell.md)
