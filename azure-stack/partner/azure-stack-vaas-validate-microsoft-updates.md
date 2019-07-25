---
title: Valider les mises à jour logicielles de Microsoft dans le service Validation en tant que service pour Azure Stack | Microsoft Docs
description: Découvrez comment valider les mises à jour logicielles de Microsoft avec le service Validation en tant que service.
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
ms.openlocfilehash: 0a507b7488a34715e528b6bbf291fec9832ef027
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418288"
---
# <a name="validate-software-updates-from-microsoft"></a>Valider les mises à jour logicielles issues de Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft publie régulièrement des mises à jour du logiciel Azure Stack. Ces mises à jour sont fournies à nos ingénieurs partenaires Azure Stack. Les mises à jour sont fournies avant d’être mises à la disposition du grand public. Vous pouvez vérifier les mises à jour par rapport à votre solution et fournir des commentaires à Microsoft.

Les mises à jour logicielles Microsoft pour Azure Stack sont désignées conformément à une convention d’affectation de noms ; par exemple, 1803 indique que la mise à jour date de mars 2018. Pour plus d’informations sur la stratégie de mise à jour, la cadence des mises à jour et les notes de publication concernant Azure Stack, consultez l’article [Stratégie de maintenance Azure Stack](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Prérequis

Avant de vous exercer au processus de mise à jour mensuelle dans VaaS, vous devez être familiarisé avec les éléments suivants :

- [Validation as a Service key concepts](azure-stack-vaas-key-concepts.md) (Concepts clés à propos de la validation en tant que service)
- [Test de vérification des fonctionnalités interactives](azure-stack-vaas-interactive-feature-verification.md)

## <a name="required-tests"></a>Tests requis

Les tests ci-dessous doivent être exécutés dans l’ordre suivant pour la validation de logiciel mensuelle :

1. Vérification de la mise à jour mensuelle Azure Stack
2. Cloud Simulation Engine

## <a name="validating-software-updates"></a>Validation des mises à jour logicielles

1. Créez un workflow de **validation de package**.
1. Pour les tests requis ci-dessus, suivez les instructions dans [Exécution des tests de validations du package](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Consultez la section ci-dessous pour obtenir plus d’instructions sur le test **Vérification de la mise à jour mensuelle Azure Stack**.

### <a name="apply-the-monthly-update"></a>Appliquer la mise à jour mensuelle

1. Sélectionnez un agent sur lequel exécuter des tests.
1. Planifiez la **Vérification de la mise à jour mensuelle Azure Stack**.
1. Indiquez l’emplacement au package d’extension OEM actuellement déployé sur l’horodatage et l’emplacement au package d’extension OEM qui sera appliqué pendant la mise à jour. Pour configurer les URL pour ces packages, consultez [Gestion des packages pour validation](azure-stack-vaas-validate-oem-package.md#managing-packages-for-validation).
1. Suivez les étapes de l’interface utilisateur à partir de l’agent sélectionné.

Si vous avez des questions ou rencontrez des problèmes, contactez le [support technique VaaS](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Étapes suivantes

- [Surveiller et gérer les tests dans le portail VaaS](azure-stack-vaas-monitor-test.md)