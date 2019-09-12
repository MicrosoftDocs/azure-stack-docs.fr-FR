---
title: Appliquer une mise à jour de fabricant d’ordinateurs (OEM) à Azure Stack | Microsoft Docs
description: Apprenez à appliquer une mise à jour de fabricant d’ordinateurs (OEM) à Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: mabrigg
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 70fe7c8cdb43f55c8e013dcca27dcde870b1cc9a
ms.sourcegitcommit: dc633e862d49412a963daee481226c1543287e5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862968"
---
# <a name="azure-stack-servicing-policy"></a>Stratégie de maintenance Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit la stratégie de maintenance pour les systèmes intégrés Azure Stack, la marche à suivre pour conserver votre système dans un état de prise en charge, et comment obtenir du soutien.

## <a name="keep-your-system-under-support"></a>Conserver votre système dans un état de prise en charge

Pour continuer à bénéficier de la prise en charge, vous devez vous assurer que les mises à jour pour Azure Stack sont actuelles.

Pour que votre instance d’Azure Stack continue d’être prise en charge, vous devez exécuter la version de mise à jour la plus récente ou exécuter les deux versions de mise à jour précédentes.

Les correctifs logiciels ne sont pas considérés comme des versions de mise à jour majeure. Si votre instance Azure Stack est en retard de *plus de deux mises à jour*, elle est considérée comme non conforme. Pour bénéficier de la prise en charge, vous devez mettre à jour avec au moins la version minimale prise en charge.

Par exemple, si la dernière version mise à jour disponible est 1904 et que les deux mises à jour précédentes étaient les versions 1903 et 1902, 1902 et 1903 restent prises en charge. Toutefois, 1901 n’est pas pris en charge. La stratégie reste vraie lorsqu’aucune version n’est pour un ou deux mois. Par exemple, si la version actuelle est 1807 et qu’il n’existait aucune version 1806, les deux mises à jour précédentes des versions 1805 et 1804 restent prises en charge.

Les packages de mise à jour logicielle Microsoft ne sont pas cumulatifs et requièrent le package de mise à jour ou le correctif logiciel précédent. Si vous décidez de reporter une ou plusieurs mises à jour, pensez-y si vous souhaitez obtenir la dernière version.

## <a name="get-support"></a>Obtenir de l’aide

Azure Stack suit le même processus de prise en charge qu’Azure. Les clients d’entreprise peuvent suivre le processus décrit dans [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Si vous êtes client d’un fournisseur de services cloud (CSP), contactez-le pour obtenir de l’aide. Pour plus d’informations, consultez le [Forum Aux Questions sur le support technique Azure](https://azure.microsoft.com/support/faq/).

# <a name="next-steps"></a>Étapes suivantes

[Gérer les mises à jour dans Azure Stack](azure-stack-updates.md)
