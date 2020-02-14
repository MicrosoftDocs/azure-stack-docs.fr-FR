---
title: Tutoriel - Configurer des ressources pour la validation en tant que service
description: Dans ce didacticiel, apprenez à configurer des ressources pour la validation en tant que service.
author: mattbriggs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 7c47c6810802cce31793aae3be3a1502acb5f102
ms.sourcegitcommit: a76301a8bb54c7f00b8981ec3b8ff0182dc606d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77143926"
---
# <a name="tutorial-set-up-resources-for-validation-as-a-service"></a>Tutoriel : Configurer des ressources pour la validation en tant que service

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

La validation en tant que service (VaaS) est un service Azure qui est utilisé pour valider et prendre en charge les solutions Azure Stack Hub présentes sur le marché. Lisez cet article et suivez les indications avant d’utiliser le service pour valider votre solution.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Soyez prêt à utiliser VaaS en configurant votre annuaire Azure Active Directory (AD).
> * Créez un compte de stockage.

## <a name="configure-an-azure-ad-tenant"></a>Configurer un locataire Azure AD

Un locataire Azure AD est utilisé pour inscrire une organisation et authentifier des utilisateurs avec VaaS. Le partenaire utilise les fonctionnalités de contrôle d’accès en fonction du rôle (RBAC) du locataire pour gérer les personnes pouvant utiliser VaaS dans son organisation. Pour plus d’informations, consultez [Qu’est-ce qu’Azure Active Directory ?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).

### <a name="create-a-tenant"></a>Créer un client

Créez un locataire que votre organisation utilisera pour accéder aux services VaaS. Utilisez un nom descriptif, par exemple `ContosoVaaS@onmicrosoft.com`.

1. Créez un locataire Azure Active Directory dans le [portail Azure](https://portal.azure.com) ou utilisez un locataire existant. <!-- For instructions on creating new Azure AD tenants, see [Get started with Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). -->

2. Ajoutez au locataire des membres de votre organisation. Ces utilisateurs auront la responsabilité d’utiliser le service pour afficher ou planifier des tests. Une fois que vous avez terminé l’inscription, vous devez définir les niveaux d’accès des utilisateurs.

    Autorisez les utilisateurs de votre locataire à exécuter des actions dans VaaS en leur attribuant l’un des rôles suivants :

    | Nom du rôle | Description |
    |---------------------|------------------------------------------|
    | Propriétaire | A un accès total à toutes les ressources. |
    | Lecteur | Peut afficher toutes les ressources, mais ne peut ni en créer, ni les gérer. |
    | Contributeur du test | Peut créer et gérer les ressources de test. |

    Pour affecter des rôles dans l’application **Validation en tant que service Azure Stack Hub** :

   1. Connectez-vous au [portail Azure](https://portal.azure.com).
   2. Sélectionnez **Tous les Services** > **Azure Active Directory** dans la section **Identité**.
   3. Sélectionnez **Applications d’entreprise** > **Validation en tant que service Azure Stack Hub**.
   4. Sélectionnez **Utilisateurs et groupes**. Le panneau **Validation en tant que service Azure Stack Hub - Utilisateurs et groupes** liste les utilisateurs qui sont autorisés à utiliser l’application.
   5. Sélectionnez **+ Ajouter un utilisateur** pour ajouter un utilisateur à partir de votre locataire et lui attribuer un rôle.

      Si vous souhaitez isoler des ressources et actions VaaS entre différents groupes au sein d’une organisation, vous pouvez créer plusieurs annuaires de locataire Azure AD.

### <a name="register-your-tenant"></a>Inscrire votre locataire

Ce processus autorise votre locataire à utiliser l’application Azure AD **Validation en tant que service Azure Stack Hub**.

1. Envoyez les informations suivantes sur le locataire à Microsoft à l’adresse [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com).

    | Données | Description |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Nom de l’organisation | Nom officiel de l’organisation. |
    | Nom du répertoire du locataire Azure AD | Nom de l’annuaire du locataire Azure AD en cours d’inscription. |
    | ID de l’annuaire du locataire Azure AD | GUID de l’annuaire du locataire AD Azure associé à l’annuaire. Pour plus d’informations sur la façon de trouver votre ID d’annuaire de locataire Azure AD, consultez [Obtenir l’ID de locataire](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-values-for-signing-in). |

2. Attendez la confirmation de l’équipe de validation Azure Stack Hub pour vérifier que votre locataire peut utiliser le portail VaaS.

### <a name="consent-to-the-vaas-application"></a>Donner son consentement à l’application VaaS

En tant qu’administrateur Azure AD, attribuez à l’application VaaS Azure AD les autorisations requises pour le compte de votre locataire :

1. Utilisez les informations d’identification d’administrateur général pour connecter le locataire au [portail VaaS](https://azurestackvalidation.com/). 

2. Sélectionnez **My Account**.

3 Acceptez les conditions du contrat pour continuer lorsque vous êtes invité à accorder à VaaS les autorisations Azure AD indiquées.

## <a name="create-an-azure-storage-account"></a>Création d'un compte Azure Storage

Pendant l’exécution du test, VaaS génère des journaux de diagnostic vers un compte de stockage Azure. En plus des journaux d’activité de test, le compte de stockage peut également servir à charger les packages d’extension OEM pour le workflow de validation du package.

Le compte de stockage Azure est hébergé dans le cloud public Azure, et non dans votre environnement Azure Stack Hub.

1. Dans le portail Azure, sélectionnez **Tous les services** > **Stockage** > **Comptes de stockage**. Dans le panneau **Comptes de stockage**, sélectionnez **Ajouter**.

2. Sélectionnez l’abonnement dans lequel créer le compte de stockage.

3. Sous **Groupe de ressources**, sélectionnez **Créer**. Entrez le nom de votre nouveau groupe de ressources.

4. Examinez les [conventions de nommage](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#storage) pour les comptes de stockage Azure. Entrez un nom pour votre compte de stockage.

5. Sélectionnez la région **USA Ouest** pour votre compte de stockage.

    Afin d’éviter que des frais de mise en réseau ne vous soient facturés pour le stockage des journaux d’activité, le compte de stockage Azure peut être configuré pour utiliser uniquement la région **USA Ouest**. Les fonctions de réplication des données et de niveau d’accès chaud ne sont pas nécessaires pour ces données. L’activation de ces fonctionnalités augmentera considérablement les coûts.

6. Conservez les paramètres aux valeurs par défaut ,à l’exception de **Type de compte** :

    - Le champ **Modèle de déploiement** est défini par défaut sur **Resource Manager**.
    - Le champ **Performances** est défini par défaut sur **Standard**.
    - Dans le champ **Type de compte**, sélectionnez **Stockage Blob**.
    - Le champ **Réplication** est défini par défaut sur **Stockage localement redondant (LRS)** .
    - Le champ **Niveau d’accès** est défini par défaut sur **Chaud**.

7. Cliquez sur **Vérifier + créer** pour passer en revue vos paramètres de compte de stockage et créer le compte.

## <a name="next-steps"></a>Étapes suivantes

Si votre environnement n’autorise pas les connexions entrantes, suivez le didacticiel sur le déploiement de l’agent local pour exécuter un test sur votre matériel.

> [!div class="nextstepaction"]
> [Déployer l’agent local](azure-stack-vaas-local-agent.md)
