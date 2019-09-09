---
title: Stratégie de maintenance Azure Stack | Microsoft Docs
description: Découvrez la stratégie de maintenance Azure Stack et comment conserver un système intégré dans un état de prise en charge.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2019
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 1022ab056157ea1a9bc925d3992a99bd0b395a35
ms.sourcegitcommit: 71d7990a2b21576c44bb2aea13ae2026e9510c55
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70188084"
---
- **Mises à jour fournies par le fabricant de matériel OEM**. Les fabricants de matériel partenaires d’Azure Stack sont responsables du cycle de vie de maintenance de bout en bout (y compris les instructions) pour les mises à jour logicielles des microprogrammes et des pilotes liées au matériel. De plus, ils sont propriétaires et gèrent les instructions pour tous les logiciels et le matériel sur l’hôte du cycle de vie de matériel. Le fabricant de matériel OEM héberge ces packages de mise à jour sur son propre site de téléchargement.

## <a name="update-package-release-cadence"></a>Cadence de publication des packages de mise à jour

Microsoft prévoie de publier des packages de mise à jour logicielle chaque mois. Toutefois, il est possible qu’il y ait plusieurs mises à jour durant un mois donné, ou qu’il n’y en ait aucune. Les fabricants de matériel OEM publient leurs mises à jour en fonction des besoins.

Pour lire la documentation relative à la planification et à la gestion des mises à jour, et pour savoir comment déterminer la version que vous utilisez, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](azure-stack-updates.md).

Pour en savoir plus sur une mise à jour, notamment comment la télécharger, consultez les notes de publication qui s’y rapportent :

- [Mise à jour 1908 d’Azure Stack](azure-stack-release-notes-1908.md)
- [Mise à jour 1907 d’Azure Stack](azure-stack-release-notes-1907.md)
- [Mise à jour 1906 d’Azure Stack](azure-stack-release-notes-1906.md)
- [Mise à jour 1905 d’Azure Stack](azure-stack-release-notes-1905.md)

## <a name="hotfixes"></a>Correctifs logiciels

Parfois, Microsoft fournit des correctifs logiciels pour Azure Stack qui corrigent un problème précis souvent préventif ou urgent.  Chaque correctif est publié en même temps que son article correspondant dans la Base de connaissances Microsoft, où sont détaillés le problème, la cause et la résolution.

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit la stratégie de maintenance pour les systèmes intégrés Azure Stack, la marche à suivre pour conserver votre système dans un état de prise en charge, et comment obtenir du soutien.

## <a name="keep-your-system-under-support"></a>Conserver votre système dans un état de prise en charge

Pour que votre instance d’Azure Stack continue d’être prise en charge, vous devez exécuter la version de mise à jour la plus récente ou exécuter les deux versions de mise à jour précédentes.

Les correctifs logiciels ne sont pas considérés comme des versions de mise à jour majeure. Si votre instance Azure Stack est en retard de *plus de deux mises à jour*, elle est considérée comme non conforme. Pour bénéficier de la prise en charge, vous devez mettre à jour avec au moins la version minimale prise en charge.

Par exemple, si la dernière version mise à jour disponible est 1904 et que les deux mises à jour précédentes étaient les versions 1903 et 1902, 1902 et 1903 restent prises en charge. Toutefois, 1901 n’est pas pris en charge. La stratégie reste vraie lorsqu’aucune version n’est pour un ou deux mois. Par exemple, si la version actuelle est 1807 et qu’il n’existait aucune version 1806, les deux mises à jour précédentes des versions 1805 et 1804 restent prises en charge.

Les packages de mise à jour logicielle Microsoft ne sont pas cumulatifs et requièrent le package de mise à jour ou le correctif logiciel précédent. Si vous décidez de reporter une ou plusieurs mises à jour, pensez-y si vous souhaitez obtenir la dernière version.

## <a name="get-support"></a>Obtenir de l’aide

Azure Stack suit le même processus de prise en charge qu’Azure. Les clients d’entreprise peuvent suivre le processus décrit dans [Création d’une demande de support Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Si vous êtes client d’un fournisseur de services cloud (CSP), contactez-le pour obtenir de l’aide. Pour plus d’informations, consultez le [Forum Aux Questions sur le support technique Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les mises à jour dans Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)
