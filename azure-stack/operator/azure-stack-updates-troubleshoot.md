---
title: Dépannage des mises à jour dans Azure Stack | Microsoft Docs
description: En tant qu'opérateur Azure Stack, apprenez à résoudre les problèmes liés aux mises à jour afin qu’Azure Stack puisse reprendre la production le plus rapidement possible.
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
ms.date: 09/23/2019
ms.author: mabrigg
ms.lastreviewed: 09/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: d5606e7904fe311a54d792a18e5d4029c709b33c
ms.sourcegitcommit: 0866555e0ed240a65595052899ef1b836dd07fbc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72257748"
---
# <a name="troubleshooting-patch-and-update-issues-for-azure-stack"></a>Dépannage des correctifs et des mises à jour pour Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Vous pouvez utiliser les conseils de cet article pour résoudre les problèmes que vous rencontrez lorsque vous mettez à jour Azure Stack.

## <a name="preparationfailed"></a>PreparationFailed

**Champ d’application** : Ce problème s’applique à toutes les versions prises en charge.

**Cause** : L’installation de la mise à jour d’Azure Stack peut échouer, et son état peut passer à `PreparationFailed`. Pour les systèmes connectés à Internet, cela indique généralement que le package de mise à jour ne peut pas être téléchargé correctement en raison d’une connexion Internet faible. 

**Correction** : Vous pouvez contourner ce problème en cliquant sur **Installer maintenant**. Si le problème persiste, nous vous conseillons de charger manuellement le package de mise à jour comme cela est décrit dans la [section Installer des mises à jour](azure-stack-apply-updates.md?#install-updates-and-monitor-progress).

**Occurrence** : Courant

## <a name="next-steps"></a>Étapes suivantes

- [Mettre à jour Azure Stack](azure-stack-updates.md)  
- [Aide et support de Microsoft Azure Stack](azure-stack-help-and-support-overview.md)
