---
title: Test de vérification des fonctionnalités interactives dans le service VaaS Azure Stack | Microsoft Docs
description: Découvrez comment créer des tests de vérification des fonctionnalités interactives pour Azure Stack avec le service VaaS (validation en tant que service).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d3db8ea8639f73f3522ddaa358195e7c9ef2f9a9
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "63448551"
---
# <a name="interactive-feature-verification-testing"></a>Test de vérification des fonctionnalités interactives  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Vous pouvez utiliser le framework de test de vérification des fonctionnalités interactives pour demander des tests pour votre système. Quand vous demandez un test, Microsoft utilise le framework pour préparer des tests qui nécessitent des étapes interactives manuelles. Microsoft peut utiliser le framework pour chaîner plusieurs tests automatisés autonomes.

Cet article décrit un scénario manuel simple. Le test vérifie le remplacement d’un disque dans Azure Stack. Le framework collecte des journaux de diagnostic à chaque étape. Vous pouvez déboguer les problèmes à mesure que vous les rencontrez. Le framework permet également le partage de journaux d’activité produits par d’autres outils ou processus, et vous permet de fournir des commentaires sur le scénario.

> [!Important]  
> Cet article fait référence aux étapes pour effectuer l’identification de disque. Il s’agit simplement d’une démonstration, car tous les résultats collectés à partir du workflow de passe de test peuvent ne pas servir pour la vérification de la nouvelle solution.

## <a name="overview-of-interactive-testing"></a>Vue d’ensemble du test interactif

Un test du remplacement d’un disque est un scénario courant. Dans cet exemple, le test comprend 5 étapes :

1. Créez un workflow **Test Pass** (passe de test).
2. Sélectionnez le test d’identification de disque **Disk Identification Test**.
3. Effectuez l’étape manuelle quand vous y êtes invité.
4. Vérifiez le résultat du scénario.
5. Envoyez le résultat du test à Microsoft.

## <a name="create-a-new-test-pass"></a>Créer une passe de test

Si aucune passe de test existante n’est disponible, suivez les instructions pour la [planification d’un test](azure-stack-vaas-schedule-test-pass.md).

## <a name="schedule-the-test"></a>Planifier le test

1. Sélectionnez **Disk Identification Test**.

    > [!Note]  
    > La version du test augmente à mesure que des améliorations sont apportées aux documentations et ressources d’accompagnement. La version la plus élevée doit toujours être utilisée, sauf indication contraire de Microsoft.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image4.png)

1. Fournissez le nom d’utilisateur administrateur de domaine et le mot de passe en sélectionnant **Modifier**.

1. Sélectionnez l’agent/le DVM d’exécution de test appropriés pour lancer le test.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image5.png)

1. Sélectionnez **Envoyer** pour démarrer le test.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image6.png)

1. Accédez à l’interface utilisateur pour le test interactif à partir de l’agent sélectionné à l’étape précédente.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image8.png)

1. Suivez les liens **Documentation** et **Validation** pour passer en revue les instructions de Microsoft sur l’exécution de ce scénario.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image9.png)

1. Sélectionnez **Suivant**.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image10.png)

1. Suivez les instructions pour exécuter le script de prévérification.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image11.png)

1. Une fois le script de prévérification correctement effectué, exécutez le scénario manuel (Remplacement de disque) conformément aux liens **Documentation** et **Validation** disponibles sous l’onglet **Informations**.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > Ne fermez pas la boîte de dialogue pendant que vous effectuez le scénario manuel.

1. Une fois le scénario manuel terminé, suivez les instructions pour exécuter le script de post-vérification.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image13.png)

1. En cas de réussite du scénario manuel (Remplacement de disque), sélectionnez **Suivant**.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > Si vous fermez la fenêtre, le test s’arrête avant d’être terminé.

1. Fournissez des commentaires pour l’expérience de test. Ces questions aident Microsoft à évaluer le taux de réussite et la qualité de mise sur le marché du scénario.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image15.png)

1. Attachez tous les fichiers journaux que vous voulez envoyer à Microsoft.

    ![Alt text](media/azure-stack-vaas-interactive-feature-verification/image16.png)

1. Acceptez le CLUF pour l’envoi de commentaires.

1. Sélectionnez **Envoyer** pour envoyer les résultats à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- [Surveiller et gérer les tests dans le portail VaaS](azure-stack-vaas-monitor-test.md)
