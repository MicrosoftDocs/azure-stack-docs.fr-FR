---
title: Planifier un test dans le portail Validation Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Découvrez comment gérer un test dans le portail Validation Azure Stack Hub.
author: mattbriggs
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: e41fe02946b3f08d34cdb0a6d81c08885ef9d71b
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77704605"
---
# <a name="scheduling-a-test"></a>Planification d’un test

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Planifiez un test dans le portail Validation Microsoft Azure Stack pour votre solution Azure Stack Hub. Une solution de validation en tant que service (VaaS) est une solution Azure Stack Hub associée à une nomenclature matérielle particulière. Vous pouvez planifier un test pour vérifier que votre matériel peut exécuter Azure Stack Hub.

Pour vérifier votre solution, créez le flux de travail pour un test. Un flux de travail VaaS intervient dans le contexte d’une solution VaaS. Il représente un ensemble de batterie de tests qui testent les fonctionnalités d’un déploiement Azure Stack Hub sur votre matériel. Ajoutez les paramètres de l’environnement de votre solution et sélectionnez un ou plusieurs tests à exécuter sur votre solution.

Si le workflow Passe de test peut être utilisé pour exécuter n’importe quel test fourni par VaaS, y compris des tests des workflows de validation, les résultats du workflow Passe de test ne sont pas considérés comme *officiels*. Pour plus d’informations sur les workflows de validation officiels, consultez [Flux de travail](azure-stack-vaas-key-concepts.md#workflows).

## <a name="prerequisites"></a>Conditions préalables requises

Avant de suivre ce guide de démarrage rapide, terminez les tâches suivantes :

- [Configurez les ressources de la validation en tant que service](azure-stack-vaas-set-up-resources.md).
- [Déployez l’agent local](azure-stack-vaas-local-agent.md) (obligatoire).
- [Concepts clés de la validation en tant que service](azure-stack-vaas-key-concepts.md) (obligatoire).

## <a name="start-a-workflow"></a>Démarrer un flux de travail

![Se connecter au portail VaaS](media/vaas_portalsignin.png)

Connectez-vous au portail, sélectionnez ou créez une solution, puis sélectionnez la solution.

1. Connectez-vous au [portail VaaS](https://azurestackvalidation.com).
2. Tapez le nom d’une solution existante ou sélectionnez **Nouvelle solution** pour créer une nouvelle solution. Pour obtenir des instructions, consultez [Create a solution in the VaaS portal](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-azure-stack-hub-validation-portal) (Créer une solution dans le portail VaaS).
3. Sélectionnez **Démarrer** au niveau de la vignette **Passes de Test**.

## <a name="specify-parameters"></a>Spécifier des paramètres

![Spécifier des paramètres dans le portail VaaS](media/vaas_test_pass_parameters.png)

Spécifiez des paramètres qui s’appliquent à tous les tests dans le flux de travail.

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. Sélectionnez **Suivant** pour sélectionner les tests à planifier.

## <a name="select-tests-to-run"></a>Sélectionner les tests à exécuter

Les tests que vous sélectionnez seront planifiés une fois le flux de travail créé.

1. Sélectionnez le ou les tests à exécuter dans votre flux de travail.

    Si vous souhaitez remplacer les paramètres communs (les paramètres fournis dans la section précédente) pour tous les tests, sélectionnez le lien **Modifier** pour spécifier les nouvelles valeurs.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

1. Sélectionnez **Suivant** pour réviser le flux de travail.

## <a name="review-and-submit"></a>Passer en revue et soumettre

Terminez la création du flux de travail.

1. Vérifiez les informations affichées.

    Le service crée votre flux de travail avec les informations fournies et les tests sélectionnés seront planifiés.

    Si vous voyez une information incorrecte, utilisez les boutons **Précédent** pour accéder à une section antérieure.

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Surveiller et gérer les tests dans le portail VaaS](azure-stack-vaas-monitor-test.md)
