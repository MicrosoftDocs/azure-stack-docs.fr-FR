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
ms.openlocfilehash: 76c6c21c2a728004d2a33c04a02b905ec674b972
ms.sourcegitcommit: d967cf8cae320fa09f1e97eeb888e3db5b6e7972
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301244"
---
# <a name="troubleshooting-patch-and-update-issues-for-azure-stack"></a>Dépannage des correctifs et des mises à jour pour Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Vous pouvez utiliser les conseils de cet article pour résoudre les problèmes que vous rencontrez lorsque vous mettez à jour Azure Stack.

## <a name="preparationfailed"></a>PreparationFailed

**Champ d’application** : Ce problème s’applique à toutes les versions prises en charge.

**Cause** : L’installation de la mise à jour d’Azure Stack peut échouer, et son état peut passer à `PreparationFailed`. Cela est dû au fait que le fournisseur de ressources de mise à jour est dans l’impossibilité de transférer correctement les fichiers du conteneur de stockage vers un partage d’infrastructure interne à des fins de traitement.

**Correction** : À compter de la version 1901 (1.1901.0.95), vous pouvez contourner ce problème en cliquant sur **Mettre à jour maintenant** à nouveau (et pas sur **Reprendre**). Le fournisseur de ressources de mise à jour (URP) nettoie les fichiers de la tentative précédente, puis redémarre le téléchargement. Si le problème persiste, nous vous conseillons de charger manuellement le package de mise à jour comme cela est décrit dans la [section Installer des mises à jour](azure-stack-apply-updates.md?#install-updates-and-monitor-progress).

**Occurrence** : Courant

## <a name="next-steps"></a>Étapes suivantes

- [Mettre à jour Azure Stack](azure-stack-updates.md)  
- [Aide et support de Microsoft Azure Stack](azure-stack-help-and-support-overview.md)
