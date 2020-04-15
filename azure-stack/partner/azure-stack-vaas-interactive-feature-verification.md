---
title: Test de vérification des fonctionnalités interactives
titleSuffix: Azure Stack Hub
description: Découvrez comment créer des tests de vérification des fonctionnalités interactives pour Azure Stack Hub avec la validation en tant que service.
author: mattbriggs
ms.topic: tutorial
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a1ef4a77bdc4f4dd83a7bced78c85d64ae7836c3
ms.sourcegitcommit: bdd4d529bd3e115a9f76eece62b1613448d5d020
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "77704724"
---
# <a name="interactive-feature-verification-testing"></a>Test de vérification des fonctionnalités interactives  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Vous pouvez utiliser le framework de test de vérification des fonctionnalités interactives pour demander des tests pour votre système. Quand vous demandez un test, Microsoft utilise le framework pour préparer des tests qui nécessitent des étapes interactives manuelles. Microsoft peut utiliser le framework pour chaîner plusieurs tests automatisés autonomes.

Cet article décrit un scénario manuel simple. Le test vérifie le remplacement d’un disque dans Azure Stack Hub. Le framework collecte des journaux de diagnostic à chaque étape. Vous pouvez déboguer les problèmes à mesure que vous les rencontrez. Le framework permet également le partage de journaux d’activité produits par d’autres outils ou processus, et vous permet de fournir des commentaires sur le scénario.

> [!Important]  
> Cet article fait référence aux étapes pour effectuer un test d’identification de disque. Il s’agit simplement d’une démonstration et tous les résultats collectés à partir du workflow de passe de test ne peuvent pas servir pour la vérification de la nouvelle solution.

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

    ![Test d’identification du disque — Tests interactifs dans Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image4.png)

2. Sélectionnez **Modifier** et fournissez le nom d’utilisateur administrateur de domaine et le mot de passe.

3. Sélectionnez l’agent/le DVM d’exécution de test appropriés pour lancer le test.

    ![Sélectionner un agent d’exécution de test — Test interactif dans Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image5.png)

4. Sélectionnez **Envoyer** pour démarrer le test.

    ![Réviser et envoyer le test — Tests interactifs dans Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image6.png)

5. Accédez à l’interface utilisateur pour le test interactif à partir de l’agent sélectionné à l’étape précédente.

    ![Test d’identification du disque — Tests interactifs dans Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image8.png)

6. Suivez les liens **Documentation** et **Validation** pour passer en revue les instructions de Microsoft sur l’exécution de ce scénario.

    ![Liens Documentation et Validation dans le test d’identification du disque](media/azure-stack-vaas-interactive-feature-verification/image9.png)

7. Sélectionnez **Suivant**.

    ![Sélectionner Suivant — Tests interactifs dans Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image10.png)

8. Suivez les instructions pour exécuter le script de prévérification.

    ![Exécuter le script de prévérification — Tests interactifs dans Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image11.png)

9. Une fois le script de prévérification correctement effectué, exécutez le scénario manuel (Remplacement de disque) tel qu’il est décrit dans les liens **Documentation** et **Validation** sous l’onglet **Informations**.

    ![Exécuter le scénario manuel — Tests interactifs dans Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > Ne fermez pas la boîte de dialogue pendant que vous effectuez le scénario manuel.

10. Une fois que vous avez terminé le scénario manuel, suivez les instructions pour exécuter le script de post-vérification.

    ![Exécuter le script de post-vérification — Tests interactifs dans Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image13.png)

11. En cas de réussite du scénario manuel (Remplacement de disque), sélectionnez **Envoyer**.

    ![Envoyer le test d’identification du disque — Tests interactifs dans Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > Si vous fermez la fenêtre, le test s’arrête avant d’être terminé.

12. Fournissez des commentaires pour l’expérience de test. Ces questions aident Microsoft à évaluer le taux de réussite et la qualité de mise sur le marché du scénario.

    ![Donner votre avis sur l’expérience des tests interactifs dans Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image15.png)

13. Attachez tous les fichiers journaux que vous voulez envoyer à Microsoft.

    ![Attacher les fichiers journaux — Tests interactifs dans Azure Stack Hub](media/azure-stack-vaas-interactive-feature-verification/image16.png)

14. Acceptez le CLUF pour l’envoi de commentaires.

15. Sélectionnez **Envoyer** pour envoyer les résultats à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- [Superviser et gérer les tests dans le portail Validation Azure Stack Hub](azure-stack-vaas-monitor-test.md)
