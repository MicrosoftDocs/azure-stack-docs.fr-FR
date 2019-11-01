---
title: Valider les packages de fabricant d’ordinateurs OEM dans le service Validation en tant que service pour Azure Stack | Microsoft Docs
description: Découvrez comment contrôler les packages de fabricant d’ordinateurs OEM avec le service Validation en tant que service.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 20d5d2d962fbe85db5d4725c864defe84c2c152c
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167363"
---
# <a name="validate-oem-packages"></a>Valider les packages OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Vous pouvez tester un nouveau package OEM lorsqu’une modification a été apportée au microprogramme ou aux pilotes d’une solution ayant fait l’objet d’une validation. Une fois que votre package a réussi le test, il est signé par Microsoft. Votre test doit contenir le package d’extension OEM mis à jour avec les pilotes et le microprogramme qui ont réussi les tests de certification PCS (Partner Certification Services) et du logo Windows Server.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Avant de charger ou soumettre des packages, vérifier [Créer un package OEM](azure-stack-vaas-create-oem-package.md) afin d’obtenir le format et le contenu du package escomptés.

## <a name="managing-packages-for-validation"></a>Gestion des packages pour validation

Lorsque vous utilisez le workflow **Validation de package** pour valider un package, vous devez fournir une URL à **Stockage Blob Azure**. Cet objet blob est le package OEM signé de test qui est installé dans le cadre du processus de mise à jour. Créez le blob à l’aide du compte de stockage Azure que vous avez créé au moment de l’installation (voir [Configurer votre validation en tant que Ressources de service](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>Configuration requise : Provisionner un conteneur de stockage

Créer un conteneur dans votre compte de stockage pour les objets blob de package. Ce conteneur peut être utilisé à chaque fois que la validation de package est exécutée.

1. Accédez au compte de stockage créé dans [Configurer vos ressources de la validation en tant que service](azure-stack-vaas-set-up-resources.md) depuis le [portail Azure](https://portal.azure.com).

2. Dans le panneau de gauche, sous **Service Blob**, sélectionnez **Conteneurs**.

3. Sélectionnez **+ Conteneur** à partir de la barre de menus.
    1. Indiquez un nom pour le conteneur, par exemple `vaaspackages`.
    1. Sélectionnez le niveau d’accès souhaité pour les clients non authentifiés, tels que VaaS. Pour plus d’informations sur la façon d’accorder l’accès VaaS aux packages dans chaque scénario, consultez [Gestion du niveau d’accès du conteneur](#handling-container-access-level).

### <a name="upload-package-to-storage-account"></a>Charger le package pour le compte de stockage

1. Préparer le package que vous voulez valider. Il s’agit d’un fichier `.zip` dont le contenu doit correspondre à la structure décrite dans [Créer un package OEM](azure-stack-vaas-create-oem-package.md).

    > [!NOTE]
    > Veuillez vérifier que les `.zip` contenus soient placés à la racine du `.zip` fichier. Il ne doit y avoir aucun sous-dossier dans le package.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez le conteneur de package et chargez le package en sélectionnant **Charger** dans la barre de menus.

1. Sélectionnez le `.zip`fichier de package à charger. Conservez les valeurs par défaut pour **Type d’objet blob** (par exemple, **Objet blob de blocs**) et **Taille de bloc**.

### <a name="generate-package-blob-url-for-vaas"></a>Générer des URL d’objet blob de package pour VaaS

Lorsque vous créez un workflow **Validation de package** dans le portail VaaS, vous devez fournir une URL au Stockage Blob Azure qui contient votre package. Certains tests *interactifs*, y compris **Vérification de la mise à jour mensuelle AzureStack** et **Vérification des packages d’extensions OEM**, nécessitent également une URL vers des objets blob de package.

#### <a name="handling-container-access-level"></a>Gestion du niveau d’accès du conteneur

Le niveau d’accès minimal exigé par VaaS varie selon que vous créez un workflow de validation de package ou que vous planifiez un test *interactif*.

Dans le cas des niveaux d’accès **Privé** et **Objet blob**, vous devez temporairement accorder l’accès à l’objet blob de package en donnant une [signature d’accès partagé](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1?) (SAP) à VaaS. Le niveau d’accès **Conteneur** n’exige pas que vous génériez des URL SAP, mais il autorise l’accès non authentifié au conteneur et à ses objets blob.

|Niveau d’accès | Exigence de workflow | Exigence de test |
|---|---------|---------|
|Privé | Générez une URL SAP par objet blob de package ([Option 1](#option-1-generate-a-blob-sas-url)). | Générez une URL SAP au niveau du compte et ajoutez manuellement le nom d’objet blob de package ([Option 2](#option-2-construct-a-container-sas-url)). |
|Blob | Indiquez la propriété de l’URL d’objet blob ([Option 3](#option-3-grant-public-read-access)). | Générez une URL SAP au niveau du compte et ajoutez manuellement le nom d’objet blob de package ([Option 2](#option-2-construct-a-container-sas-url)). |
|Conteneur | Indiquez la propriété de l’URL d’objet blob ([Option 3](#option-3-grant-public-read-access)). | Indiquez la propriété de l’URL d’objet blob ([Option 3](#option-3-grant-public-read-access)).

Les options d’octroi d’accès à vos packages sont classées par ordre d’importance croissante.

#### <a name="option-1-generate-a-blob-sas-url"></a>Option 1 : Générer une URL SAP d’objet blob

Utilisez cette option si le niveau d’accès de votre conteneur de stockage est défini sur **Privé**, le conteneur dans ce cas ne permet pas l’accès en lecture public sur le conteneur ou ses objets blob.

> [!NOTE]
> Cette méthode ne fonctionne pas pour les tests *interactifs*. Voir [Option 2 : Construire une URL SAP de conteneur](#option-2-construct-a-container-sas-url).

1. Dans le [portail Azure](https://portal.azure.com/), accédez à votre compte de stockage, puis au fichier .zip contenant votre package.

2. Sélectionnez **Générer une signature d’accès partagé** dans le menu contextuel.

3. Sélectionnez **Lecture** dans **Autorisations**.

4. Définissez **Heure de début** sur l’heure actuelle et **Heure de fin** sur une valeur ultérieure d’au moins 48 heures à la valeur **Heure de fin**. Si vous devez créer d’autres workflows avec le même package, songez à augmenter l’**Heure de fin** pour englober la durée de vos tests.

5. Sélectionnez **Générer un jeton et une URL SAP d’objet blob**.

Utilisez l’**URL SAP d’objet blob** quand vous fournissez des URL d’objet blob de package au portail.

#### <a name="option-2-construct-a-container-sas-url"></a>Option 2 : Construire une URL SAP de conteneur

Utilisez cette option si le niveau d’accès de votre conteneur de stockage est défini sur **Privé** et qu’il vous faut fournir une URL d’objet blob de package à un test *interactif*. Cette URL peut également être utilisée au niveau du workflow.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Sélectionnez l’option **Blob** dans **Services autorisés**. Désélectionnez les autres options.

1. Sélectionnez **Conteneur**, et **Objet** dans **Types de ressources autorisés**.

1. Sélectionnez **Lecture** et **Liste** dans **Permissions autorisées**. Désélectionnez les autres options.

1. Sélectionnez **Début** en tant que date actuelle et définissez **Fin** sur une date ultérieure qui tienne compte d’une durée de 14 jours au moins par rapport à celle du **Début**. Si vous devez exécuter d’autres tests avec le même package, envisagez d’augmenter la valeur **Heure de fin** pour qu’elle corresponde à la durée de vos tests. Tous les tests planifiés par le biais de VaaS après l’**Heure de fin** échouent et une nouvelle signature d’accès partagé (SAP) doit être générée.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    Le format devrait s’afficher comme suit : `https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. Modifier l’URL SAS générée pour inclure le conteneur de packages, `{containername}`et le nom de votre objet blob de package, `{mypackage.zip}`, comme suit :  `https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    Utilisez cette valeur quand vous fournissez des URL d’objet blob de package au portail.

#### <a name="option-3-grant-public-read-access"></a>Option 3 : Accorder un accès en lecture public

Utilisez cette option s’il est acceptable d’autoriser l’accès des objets blob individuels à des clients non authentifiés ou, dans le cas de tests *interactifs*, l’accès du conteneur.

> [!CAUTION]
> Cette option ouvre vos objets blob à un accès anonyme en lecture seule.

1. Définissez le niveau d’accès du conteneur de package sur **Objet blob** ou sur [Conteneur](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs) en suivant les instructions de la section **Accorder à des utilisateurs anonymes des autorisations d’accès aux conteneurs et objets blob**.

    > [!NOTE]
    > Si vous fournissez une URL de package à un test *interactif*, vous devez accorder l’**accès en lecture public complet** au conteneur pour procéder au test.

1. Dans le conteneur de package, sélectionnez l’objet blob de package pour ouvrir le volet Propriétés.

1. Copiez l’**URL**. Utilisez cette valeur quand vous fournissez des URL d’objet blob de package au portail.

## <a name="create-a-package-validation-workflow"></a>Créer un workflow validation de package

1. Se connecter au [portail VaaS](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Sélectionnez **Démarrer** sur la vignette **Validation de package**.

    ![Vignette du workflow des validations du package](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Entrez l’URL de l’objet blob du stockage Azure pour le package OEM signé de test nécessitant une signature de Microsoft. Pour obtenir des instructions, consultez [Générer des URL d’objet blob de package pour VaaS](#generate-package-blob-url-for-vaas).

6. Copiez le dossier du package de mise à jour AzureStack dans un répertoire local sur la DVM. Entrez le chemin du répertoire parent pour 'AzureStack update package folder path'

7. Copiez le dossier du package OEM créé plus haut dans un répertoire local sur la DVM. Entrez le chemin du répertoire parent pour 'OEM update package folder path'

    > [!NOTE]
    > Copiez la mise à jour AzureStack et la mise à jour OEM dans **2 répertoires parents séparés**.

8. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Après la création d’un workflow, les paramètres d’environnement ne peuvent plus être modifiés.

9. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

10. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Vous allez être redirigé vers la page de résumé de tests.

## <a name="required-tests"></a>Tests requis

Les tests suivants doivent être exécutés dans l’ordre spécifié pour la validation de package OEM :

- Étape 1 - Vérification de la mise à jour mensuelle Azure Stack
- Étape 2 - Vérification des packages d’extensions OEM
- Étape 3 - Cloud Simulation Engine

## <a name="run-package-validation-tests"></a>Exécution des tests de validations du package

1. Dans la page **Résumé des tests de validation du package**, vous allez exécuter un sous-ensemble des tests listés appropriés à votre scénario.

    Dans les workflows de validation, la **planification** d’un test utilise des paramètres communs au niveau du workflow que vous avez spécifiés lors de la création de votre workflow (voir [Paramètres de flux de travail communs dans la validation en tant que service Azure Stack](azure-stack-vaas-parameters.md)). Si l’une des valeurs de paramètres de test devient non valide, vous devez les redéfinir comme indiqué dans la section relative à la [modification des paramètres de workflow](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

    > [!NOTE]
    > La planification d’un test de validation sur une instance existante créera une nouvelle instance à la place de l’ancienne contenue dans le portail. Les journaux d’activité de l’ancienne instance sont conservés, mais ne sont pas accessibles à partir du portail.  
    > Dès lors qu’un test est concluant, l’action de **planification** est désactivée.

2. Pour la validation du package, vous allez exécuter les **tests requis** dans l’ordre indiqué.

    > [!CAUTION]
    > VaaS exécutera les tests dans l’ordre dans lequel ils ont été planifiés. Il est nécessaire de planifier les tests dans l’ordre spécifié.

3. Sélectionnez l’agent qui exécutera le test. Pour plus d’informations sur l’ajout d’agents d’exécution de test locaux, consultez [Déployer l’agent local](azure-stack-vaas-local-agent.md).

4. Pour planifier la série de tests, sélectionnez **Planifier** dans le menu contextuel pour ouvrir une invite permettant de planifier l’instance de test.

5. Passez en revue les paramètres de test, puis sélectionnez **Envoyer** pour planifier le test.

6. Vous n’avez pas besoin d’attendre la fin du test avant de planifier le test suivant. Planifiez tous les tests **requis** dans l’ordre indiqué ci-dessus.

7. Passez en revue les résultats des tests **requis**.

Pour soumettre une requête de signature de package, envoyez à [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) le nom de la solution et celui de la validation de package qui sont associés à cette exécution.

## <a name="next-steps"></a>Étapes suivantes

- [Surveiller et gérer les tests dans le portail VaaS](azure-stack-vaas-monitor-test.md)
