---
title: Présentation de la validation en tant que service
titleSuffix: Azure Stack Hub
description: Présentation de la validation en tant que service Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 31d47f947c4c71847156ca4e49c4b370c872e920
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77625286"
---
# <a name="validation-as-a-service-for-azure-stack-hub"></a>Validation en tant que service pour Azure Stack Hub

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

La validation en tant que service (VaaS) est un service Azure natif, conçu pour les partenaires de solutions qui conçoivent conjointement les offres Azure Stack Hub avec Microsoft. Les partenaires de solutions peuvent utiliser le service pour vérifier que leurs solutions répondent aux exigences de Microsoft, et qu’elles fonctionnent comme prévu avec Azure Stack Hub.

Les principales utilisations de VaaS consistent à :

- Valider de nouvelles solutions Azure Stack Hub
- Valider les modifications apportées au logiciel Azure Stack Hub
- Signer numériquement les packages des partenaires de solutions utilisés au cours du déploiement
- Utiliser des outils annexes de test en préversion dans la validation en tant que service Azure Stack

## <a name="validate-a-new-azure-stack-hub-solution"></a>Valider une nouvelle solution Azure Stack Hub

Les partenaires utilisent le workflow de **validation de solution** pour valider les nouvelles solutions Azure Stack Hub. La solution doit réussir les tests des composants Azure Stack Hub du Hardware Lab Kit (HKL) exigés. Pour certifier une plage de configurations matérielles, le workflow doit être exécuté deux fois pour chaque nouvelle solution : une fois pour la configuration minimale et une fois pour la configuration maximale.

Pour plus d’informations, consultez [Valider une nouvelle solution Azure Stack Hub](azure-stack-vaas-validate-solution-new.md).

## <a name="validate-changes-to-the-azure-stack-hub-software"></a>Valider les modifications apportées au logiciel Azure Stack Hub

Les partenaires utilisent le workflow de **validation de package** pour vérifier que leur solution fonctionne avec les dernières mises à jour du logiciel Azure Stack Hub. Le workflow de validation de package doit être exécuté sur un environnement matériel recommandé par Microsoft où les correctifs et mises à jour ont été utilisés pour appliquer la mise à jour. Il est recommandé d’exécuter également le workflow sur la build de référence.

Pour plus d’informations, consultez [Valider les mises à jour logicielles issues de Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

## <a name="get-digitally-signed-solution-partner-packages"></a>Obtenir les packages des partenaire de solutions signés numériquement

En plus de la validation des mises à jour Azure Stack Hub, les partenaires utilisent le workflow de **validation de package** pour valider les mises à jour des packages de personnalisation OEM. Ces mises à jour incluent des pilotes et des microprogrammes propres aux partenaires Azure Stack Hub, ainsi que d’autres logiciels utilisés lors du déploiement du logiciel Azure Stack Hub. Déployez le package que vous validez sur la version actuelle du logiciel Azure Stack Hub, avec au moins la solution de taille minimale pouvant être prise en charge. Le package est envoyé à VaaS avant l’exécution de tests. Une fois les tests réussis, informez [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) que le package a passé les tests et qu’il va être approuvé numériquement avec la signature numérique Azure Stack Hub. Microsoft signe le package et notifie le partenaire Azure Stack Hub que celui-ci est disponible en téléchargement dans le portail Validation Azure Stack Hub.

Pour plus d’informations, consultez [Valider les packages OEM](azure-stack-vaas-validate-oem-package.md).

## <a name="preview-vaas-test-collateral"></a>Utiliser des outils annexes de test en préversion dans la validation en tant que service Azure Stack

Microsoft propose régulièrement de nouvelles fonctionnalités dans Azure Stack Hub. Dans le cadre du processus de développement pour la mise sur le marché de ces fonctionnalités, de nouveaux outils de test annexes sont proposés dans le workflow de **passe de test**. Ce workflow comprend des outils de test annexes provenant d’autres workflows pour permettre l’exécution de tests non officiels. N’utilisez pas le workflow de passe de test pour soumettre les résultats en vue d’une approbation. Pour obtenir une approbation officielle de votre solution, utilisez le workflow de validation de solution et de validation de package.

Pour plus d’informations, consultez [Démarrage rapide : Utiliser le portail Validation Azure Stack Hub pour planifier votre premier test](azure-stack-vaas-schedule-test-pass.md).

## <a name="validation-workflow-tests-summary"></a>Résumé de tests de workflow de validation

| Workflow de validation | Tests requis |
|----|------------|
| [Validation de nouvelle solution](azure-stack-vaas-validate-solution-new.md) | Cloud Simulation Engine<br>Compute SDK Operational Suite<br>Disk Identification Test<br>KeyVault Extension SDK Operational Suite<br>KeyVault SDK Operational Suite<br>Network SDK Operational Suite<br>Storage Account SDK Operational Suite<br> |
| [Validation de package OEM](azure-stack-vaas-validate-oem-package.md) | Vérification des packages d’extensions OEM<br>Cloud Simulation Engine |
| [Validation de mise à jour mensuelle](azure-stack-vaas-validate-microsoft-updates.md) | Vérification de la mise à jour mensuelle Azure Stack Hub<br>Cloud Simulation Engine<br> |

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les ressources de la validation en tant que service](azure-stack-vaas-set-up-resources.md)
- Découvrez les [concepts clés concernant la validation en tant que service](azure-stack-vaas-key-concepts.md)
