---
title: Surveiller et gérer des tests dans le portail Azure Stack VaaS | Microsoft Docs
description: Surveillez et gérez des tests dans le portail Azure Stack VaaS.
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
ms.openlocfilehash: 5d3d32df25aeba90ef065fc4363f0887dcbc36e5
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418354"
---
# <a name="monitor-and-manage-tests-in-the-vaas-portal"></a>Surveiller et gérer les tests dans le portail VaaS

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Suite à la planification de tests relatifs à votre solution Azure Stack, Validation en tant que Service (VaaS) vous communiquera l’état d’exécution des tests. Vous trouverez ces informations, ainsi que des actions telles que la replanification et l’annulation de tests, dans le portail VaaS.

## <a name="navigate-to-the-workflow-tests-summary-page"></a>Accéder à la page de résumé de tests de workflow

1. Dans le tableau de bord de solutions, sélectionnez une solution existante qui dispose d’au moins un workflow.

    ![Vignettes workflow](media/tile_all-workflows.png)

1. Sélectionnez **Gérer** au niveau de la vignette de workflow. La page suivante répertorie les workflows créés pour la solution sélectionnée.

1. Sélectionnez un workflow pour ouvrir les résumés des tests correspondants.

## <a name="change-workflow-parameters"></a>Modifier les paramètres de workflow

Vous pouvez modifier les [paramètres de test](azure-stack-vaas-parameters.md#test-parameters) spécifiés lors de la création du workflow pour n’importe quel type de workflow.

1. Dans la page de résumé de tests, sélectionnez le bouton **Modifier**.

1. Pour plus d’informations, consultez l’article [Paramètres de flux de travail communs dans la validation en tant que service Azure Stack](azure-stack-vaas-parameters.md).

1. Sélectionnez **Envoyer** pour enregistrer les valeurs.

> [!NOTE]
> Dans le workflow **Passe de Test**, vous devez terminer la sélection de test et accéder à la page de vérification avant de pouvoir enregistrer les nouvelles valeurs de paramètre.

### <a name="add-tests-test-pass-only"></a>Ajouter des tests (passe de test uniquement)

Dans les workflows **Passe de Test**, les boutons **Ajouter des tests** et **Modifier** permettent de planifier les nouveaux tests dans le workflow.

> [!TIP]
> Sélectionnez **Ajouter des tests** si vous souhaitez uniquement planifier des nouveaux tests et que vous n’avez pas besoin de modifier les paramètres pour un workflow **Passe de test**.

## <a name="managing-test-instances"></a>Gestion des instances de test

Pour les exécutions non officielles (par exemple le workflow **Passe de Test**), la page de résumé des tests répertorie les tests planifiés relatifs à la solution Azure Stack.

Pour les exécutions officielles (par exemple, les workflows **Validation**), la page de résumé des tests répertorie les tests planifiés nécessaires à la validation de la solution Azure Stack. Les tests de validation sont planifiés à partir de cette page.

Chaque instance de test planifiée affiche les informations suivantes :

| Colonne | Description |
| --- | --- |
| Nom du test | Le nom et la version du test. |
| Catégorie | L’objectif du test. |
| Date de création | L’heure à laquelle le test a été planifié. |
| Démarré | L’heure à laquelle l’exécution de la tâche a commencé. |
| Duration | La durée d’exécution du test. |
| Statut | L’état ou le résultat du test. Les états des tests avant exécution ou en cours d’exécution sont : `Pending`, `Running`. Les états des test terminés sont : `Cancelled`, `Failed`, `Aborted`, `Succeeded`. |
| Nom de l’agent | Le nom de l’agent qui a exécuté le test. |
| Nombre total d’opérations | Le nombre total de tentatives d’opérations lors du test. |
| Passed operations (Opérations réussies) | Le nombre d’opérations réussies lors du test. |
|  Failed Operations (Opérations ayant échoué) | Le nombre d’opérations échouées lors du test. |

### <a name="actions"></a>Actions

Chaque instance de test répertorie les actions disponibles que vous pouvez effectuer lorsque vous sélectionnez le menu contextuel **[...]**  dans la table des instances de test.

#### <a name="view-information-about-the-test-definition"></a>Afficher des informations sur la définition du test

Sélectionnez **Afficher les informations** dans le menu contextuel pour afficher les informations générales sur la définition du test. Toutes les instances de test avec le même nom et la même version partagent ces données.

| Propriété du test | Description |
| -- | -- |
| Nom du test | Nom du test. |
| Test version (Version du test) | La version du test. |
| Publisher | L’éditeur du test. |
| Catégorie |  L’objectif du test. |
| Target services (Services cibles) | Les services Azure Stack en cours de test. |
| Description | Description du test. |
| Estimated duration (minutes) (Durée estimée [en minutes]) | Le runtime attendu du test. |
| Liens | Toutes les informations pertinentes concernant le test ou les points de contact. |

#### <a name="view-test-instance-parameters"></a>Afficher les paramètres d’instance de test

Sélectionnez **Afficher les paramètres** dans le menu contextuel pour afficher les paramètres fournis à l’instance de test au moment de la planification. Les chaînes sensibles, comme les mots de passe, ne sont pas affichées. Cette action est disponible uniquement pour les tests qui ont été planifiés.

Cette fenêtre inclut les métadonnées suivantes pour toutes les instances de test :

| Propriété d’instance de test | Description |
| -- | -- |
| Nom du test | Nom du test. |
| Test version (Version du test) | La version du test. |
| Test instance ID (ID d’instance de test) | Un GUID identifiant l’instance spécifique du test. |

#### <a name="view-test-instance-operations"></a>Afficher les opérations d’instance de test

Sélectionnez **Affichage des opérations** à partir du menu contextuel pour afficher un état détaillé des opérations effectuées pendant le test. Cette action est disponible uniquement pour les tests qui ont été planifiés.

![Affichage des opérations](media/manage-test_context-menu-operations.png)

#### <a name="download-logs-for-a-completed-test-instance"></a>Télécharger les journaux d’activité pour une instance de test terminée

Sélectionnez **Télécharger les journaux d’activité** dans le menu contextuel pour télécharger un fichier `.zip` de la sortie des journaux d’activité pendant l’exécution du test. Cette action est disponible uniquement pour les tests terminés, par exemple, un test avec un statut `Cancelled`, `Failed`, `Aborted` ou `Succeeded`.

#### <a name="reschedule-a-test-instance-or-schedule-a-test"></a>Replanifier une instance de test ou planifier un test

La planification des tests à partir de la page de gestion varie selon le type de workflow qui régit le test.

##### <a name="test-pass-workflow"></a>Workflow de passe de test

Dans le workflow Passe de test, la **replanification** d’une instance de test réutilise le même ensemble de paramètres que l’instance de test d’origine et *remplace* le résultat d’origine, y compris ses journaux d’activité. Vous devrez entrer à nouveau les chaînes sensibles comme les mots de passe lorsque vous replanifiez.

1. Sélectionnez **Replanifier** dans le menu contextuel pour ouvrir une invite de replanification de l’instance de test.

1. Entrez les paramètres applicables.

1. Sélectionnez **Envoyer** pour replanifier l’instance de test et remplacer l’instance existante.

##### <a name="validation-workflows"></a>Workflows de validation

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

#### <a name="cancel-a-test-instance"></a>Annuler une instance de test

Un test planifié peut être annulé si son état est `Pending` ou `Running`.  

1. Sélectionnez **Annuler** dans le menu contextuel pour ouvrir une invite d’annulation de l’instance de test.

1. Select **Envoyer** pour annuler l’instance de test.

## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les problèmes de validation en tant que service](azure-stack-vaas-troubleshoot.md)
