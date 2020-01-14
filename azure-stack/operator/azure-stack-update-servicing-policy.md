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
ms.openlocfilehash: 8065b2905adb5298c23cd7c924ccff28fb778efe
ms.sourcegitcommit: b2418661bfa3a791e65b9b487e20982dba3e4c41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75756819"
---
# <a name="azure-stack-hub-servicing-policy"></a>Stratégie de maintenance Azure Stack Hub

*S’applique à : Systèmes intégrés Azure Stack Hub*

Cet article décrit la stratégie de maintenance des systèmes intégrés Azure Stack Hub, la marche à suivre pour conserver votre système dans un état de prise en charge ainsi que la façon d’obtenir un support.

## <a name="keep-your-system-under-support"></a>Conserver votre système dans un état de prise en charge

Pour continuer à bénéficier de la prise en charge, vous devez vous assurer que les mises à jour pour Azure Stack sont actuelles.

Pour que votre système Azure Stack Hub reste dans un état de prise en charge, l’instance doit exécuter la version de mise à jour la plus récente ou l’une des deux versions de mise à jour précédentes.

Les correctifs logiciels ne sont pas considérés comme des versions de mise à jour majeure. Si votre système Azure Stack Hub a *plus de deux mises à jour* de retard, il est considéré comme non conforme. Pour bénéficier de la prise en charge, vous devez mettre à jour avec au moins la version minimale prise en charge.

Par exemple, si la dernière version mise à jour disponible est 1904 et que les deux mises à jour précédentes étaient les versions 1903 et 1902, 1902 et 1903 restent prises en charge. Toutefois, 1901 n’est pas pris en charge. La stratégie reste vraie lorsqu’aucune version n’est pour un ou deux mois. Par exemple, si la version actuelle est 1807 et qu’il n’existait aucune version 1806, les deux mises à jour précédentes des versions 1805 et 1804 restent prises en charge.

Les packages de mise à jour logicielle Microsoft ne sont pas cumulatifs et requièrent le package de mise à jour ou le correctif logiciel précédent. Si vous décidez de reporter une ou plusieurs mises à jour, pensez-y si vous souhaitez obtenir la dernière version.

## <a name="get-support"></a>Obtenir de l’aide

Azure Stack Hub suit le même processus de support qu’Azure. Les clients d’entreprise peuvent suivre le processus décrit dans [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Si vous êtes client d’un fournisseur de solutions cloud (CSP), contactez-le pour obtenir de l’aide. Pour plus d’informations, consultez le [Forum Aux Questions sur le support technique Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Étapes suivantes

[Gérer les mises à jour dans Azure Stack Hub](azure-stack-updates.md)
