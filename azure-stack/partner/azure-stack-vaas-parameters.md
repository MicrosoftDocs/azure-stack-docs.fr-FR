---
title: Paramètres de workflow communs dans VaaS
titleSuffix: Azure Stack Hub
description: Découvrez les paramètres de workflow communs pour la validation en tant que service Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a9fce93fb66793ec933d2a8182811bd0577a2588
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79295088"
---
# <a name="workflow-common-parameters-for-azure-stack-hub-validation-as-a-service"></a>Paramètres de workflow communs pour la validation en tant que service Azure Stack Hub

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Les paramètres communs comprennent des valeurs telles que les variables d’environnement et les informations d’identification utilisateur, qui sont exigées par tous les tests de la validation en tant que service (VaaS). Ces valeurs sont définies au niveau du flux de travail lorsque vous créez ou modifiez un flux de travail. Lors de la planification des tests, ces valeurs sont transmises en tant que paramètres pour chaque test dans le flux de travail.

> [!NOTE]
> Chaque test définit son propre ensemble de paramètres. Lors de la planification, un test peut vous obliger à entrer une valeur indépendamment des paramètres communs, ou vous autoriser à remplacer la valeur du paramètre commun.

## <a name="environment-parameters"></a>Paramètres d’environnement

Les paramètres d’environnement décrivent l’environnement Azure Stack Hub testé. Ces valeurs doivent être fournies en générant et en chargeant un fichier d’informations d’horodatage Azure Stack Hub pour l’instance que vous testez.

> [!NOTE]
> Dans les workflows de validation officielle, les paramètres d’environnement ne peuvent pas être modifiés après la création du workflow.

### <a name="generate-the-stamp-information-file"></a>Générer le fichier d’informations d’horodatage

1. Connectez-vous à la machine DVM ou à n’importe quelle machine ayant accès à l’environnement Azure Stack Hub.
2. Exécutez les commandes suivantes dans une fenêtre PowerShell avec élévation de privilèges :

    ```powershell  
    $CloudAdminUser = "<cloud admin username>"
    $CloudAdminPassword = ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
    $stampInfoCreds = New-Object System.Management.Automation.PSCredential($CloudAdminUser, $CloudAdminPassword)
    $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation' -Credential $stampInfoCreds
    ConvertTo-Json $params > stampinfoproperties.json
    ```

### <a name="locate-values-in-the-ece-configuration-file"></a>Rechercher les valeurs dans le fichier de configuration ECE

Les valeurs de paramètre d’environnement peuvent également être recherchées de façon manuelle dans le **fichier de configuration ECE** situé dans `C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787` sur la machine DVM.

## <a name="test-parameters"></a>Paramètres de test

Les paramètres de test communs comportent des informations sensibles qui ne peuvent pas être stockées dans des fichiers de configuration. Ces paramètres doivent être fournis manuellement.

Paramètre    | Description
-------------|-----------------
Tenant Administrator User (Utilisateur administrateur de locataire)                            | Administrateur de locataire Azure Active Directory (Azure AD) qui a été provisionné par l’administrateur de services fédérés dans l’annuaire AAD. Cet utilisateur exécute des actions au niveau du locataire, comme le déploiement de modèles pour configurer des ressources (machines virtuelles, comptes de stockage, etc.) et l’exécution de charges de travail. Pour plus d’informations sur le provisionnement du compte de locataire, consultez [Ajouter un nouveau locataire Azure Stack Hub](../operator/azure-stack-add-new-user-aad.md).
Utilisateur administrateur de service             | Administrateur Azure AD du locataire de l’annuaire Azure AD spécifié au cours du déploiement Azure Stack Hub. Recherchez `AADTenant` dans le fichier config ECE et sélectionnez la valeur dans l’élément `UniqueName`.
Cloud Administrator User (Utilisateur administrateur de cloud)               | Compte administrateur de domaine Azure Stack Hub (par exemple, `contoso\cloudadmin`). Recherchez `User Role="CloudAdmin"` dans le fichier config ECE et sélectionnez la valeur dans l’élément `UserName`.
Chaîne de connexion des diagnostics          | Une URL de signature d’accès partagé d’un compte Stockage Azure dans lequel les journaux de diagnostic sont copiés pendant l’exécution des tests. Pour obtenir des instructions sur la génération de l’URL de signature d’accès partagé, consultez [Générer la chaîne de connexion des diagnostics](#generate-the-diagnostics-connection-string). |

> [!IMPORTANT]
> La **chaîne de connexion des diagnostics** doit être valide pour pouvoir continuer.

### <a name="generate-the-diagnostics-connection-string"></a>Générer la chaîne de connexion des diagnostics

La chaîne de connexion des diagnostics est requise pour le stockage des journaux de diagnostics pendant l’exécution du test. Utilisez le compte Stockage Azure créé pendant la configuration (voir [Configurer des ressources pour la validation en tant que service](azure-stack-vaas-set-up-resources.md)) pour créer une URL de signature d’accès partagé (SAS) et accorder l’accès VaaS permettant de charger les journaux dans votre compte de stockage.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Sélectionnez l’option **Blob** dans **Services autorisés**. Désélectionnez les autres options.

1. Sélectionnez **Service**, **Conteneur** et **Objet** dans **Types de ressources autorisés**.

1. Sélectionnez **Lire**, **Écrire**, **Lister**, **Ajouter**, **Créer**  dans **Permissions autorisées**. Désélectionnez les autres options.

1. Définissez **Heure de début** sur l’heure actuelle et **Heure de fin** sur trois mois à partir de l’heure actuelle.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]

> [!NOTE]  
> L’URL de signature d’accès partagé expire à l’heure de fin définie lors de la génération de l’URL. Lors de la planification des tests, assurez-vous que l’URL est valide pendant au moins 30 jours plus le temps nécessaire pour l’exécution des tests (nous vous recommandons une durée de trois mois).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [concepts clés concernant la validation en tant que service](azure-stack-vaas-key-concepts.md)