---
title: Résoudre les problèmes liés aux mises à jour dans Azure Stack Hub
description: En tant qu’opérateur Azure Stack Hub, apprenez à résoudre les problèmes liés aux mises à jour afin qu’Azure Stack Hub puisse reprendre la production le plus rapidement possible.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 09/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: aaedd7f4985ae96c051ef2be427002592134e00b
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367304"
---
# <a name="best-practices-for-troubleshooting-azure-stack-hub-patch-and-update-issues"></a>Bonnes pratiques pour résoudre les problèmes liés aux correctifs logiciels et aux mises à jour Azure Stack Hub

Cet article fournit une vue d’ensemble des bonnes pratiques pour la résolution des problèmes liés aux mises à jour et aux correctifs logiciels Azure Stack Hub. Il fournit également les corrections relatives aux problèmes usuels des mises à jour et des correctifs logiciels.


Le processus relatif aux mises à jour et aux correctifs logiciels Azure Stack Hub est conçu pour permettre aux opérateurs d’appliquer les mises à jour de manière cohérente et rationalisée. Bien que cela soit rare, certains problèmes peuvent se produire pendant l’application des correctifs logiciels et des mises à jour. Les étapes suivantes sont recommandées si vous rencontrez un problème pendant le processus d’application des correctifs logiciels et des mises à jour :

0. **Prérequis** : Veillez à suivre la [liste de contrôle des activités de mise à jour](release-notes-checklist.md) et à [configurer la collecte automatique des journaux](azure-stack-configure-automatic-diagnostic-log-collection.md).
1. Suivez les étapes de correction indiquées dans l’alerte d’échec créée au moment de l’échec de votre mise à jour.
2. Passez en revue les [problèmes usuels liés aux correctifs logiciels et mises à jour Azure Stack Hub](#common-azure-stack-hub-patch-and-update-issues), puis effectuez les actions recommandées si votre problème est listé.
3. Si vous n’êtes pas parvenu à résoudre votre problème à l’aide des étapes ci-dessus, créez un [ticket de support Azure Stack Hub](azure-stack-help-and-support-overview.md). Vérifiez que vous disposez des [journaux collectés](https://docs.microsoft.com/azure-stack/operator/azure-stack-configure-on-demand-diagnostic-log-collection) pour la période pendant laquelle le problème s’est produit.

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>Problèmes usuels liés aux correctifs logiciels et mises à jour Azure Stack Hub

*S’applique à : Systèmes intégrés Azure Stack Hub*

### <a name="preparationfailed"></a>PreparationFailed

**Champ d’application** : Ce problème s’applique à toutes les versions prises en charge.

**Cause** : L’installation de la mise à jour d’Azure Stack Hub peut échouer, et son état peut passer à `PreparationFailed`. Pour les systèmes connectés à Internet, cela indique généralement que le package de mise à jour ne peut pas être téléchargé correctement en raison d’une connexion Internet faible. 

**Correction** : Vous pouvez contourner ce problème en cliquant sur **Installer maintenant**. Si le problème persiste, nous vous conseillons de charger manuellement le package de mise à jour comme cela est décrit dans la [section Installer des mises à jour](azure-stack-apply-updates.md?#install-updates-and-monitor-progress).

**Occurrence** : Courant

## <a name="next-steps"></a>Étapes suivantes

- [Mettre à jour Azure Stack Hub](azure-stack-updates.md)  
- [Aide et support de Microsoft Azure Stack Hub](azure-stack-help-and-support-overview.md)
