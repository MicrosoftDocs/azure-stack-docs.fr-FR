---
title: Valider les mises à jour logicielles issues de Microsoft
titleSuffix: Azure Stack Hub
description: Découvrez comment valider les mises à jour logicielles de Microsoft avec la validation en tant que service Azure Stack Hub.
author: mattbriggs
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 46618ee2ab650842ebab9fc90b18b24131ac5d07
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661124"
---
# <a name="validate-software-updates-from-microsoft"></a>Valider les mises à jour logicielles issues de Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft publie régulièrement des mises à jour du logiciel Azure Stack Hub. Ces mises à jour sont fournies à nos partenaires de co-ingénierie Azure Stack Hub. Les mises à jour sont fournies avant d’être mises à la disposition du grand public. Vous pouvez vérifier les mises à jour par rapport à votre solution et fournir des commentaires à Microsoft.

Les mises à jour logicielles Microsoft pour Azure Stack Hub sont nommées conformément à une convention de nommage. Par exemple, 1803 indique que la mise à jour date de mars 2018. Pour plus d’informations sur la stratégie de maintenance et les notes de publication d’Azure Stack Hub, consultez [Stratégie de maintenance Azure Stack Hub](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Prérequis

Avant de vous exercer au processus de mise à jour mensuelle dans la validation en tant que service (VaaS), vous devez être familiarisé avec les éléments suivants :

- [Concepts clés de la validation en tant que service](azure-stack-vaas-key-concepts.md)

## <a name="required-tests"></a>Tests requis

Les tests ci-dessous doivent être exécutés dans l’ordre suivant pour la validation de logiciel mensuelle :

- Workflow de validation OEM

## <a name="validating-software-updates"></a>Validation des mises à jour logicielles

1. Créez un workflow de **validation de package**.
1. Pour les tests requis ci-dessus, suivez les instructions dans [Exécution des tests de validations du package](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Consultez la section ci-dessous pour obtenir plus d’instructions sur le test **Vérification de la mise à jour mensuelle Azure Stack Hub**.

Si vous avez des questions ou rencontrez des problèmes, contactez le [support technique VaaS](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Étapes suivantes

- [Surveiller et gérer les tests dans le portail VaaS](azure-stack-vaas-monitor-test.md)