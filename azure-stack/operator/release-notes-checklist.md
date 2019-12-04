---
title: Azure Stack - check-list des activités de mise à jour | Microsoft Docs
description: Check-list pour préparer votre système à la dernière mise à jour Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2019
ms.author: sethm
ms.reviewer: ppacent
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 077d50da935a3ee8de0967c51920ede69125c816
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74299037"
---
# <a name="azure-stack-update-activity-checklist"></a>Check-list des activités de mise à jour Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Consultez cette check-list pour préparer une mise à jour Azure Stack. Cet article contient une check-list des activités liées aux mises à jour pour les opérateurs Azure Stack.

## <a name="prepare-for-azure-stack-update"></a>Se préparer à la mise à jour d’Azure Stack

| Activité                     | Détails                                                   |
|------------------------------|-----------------------------------------------------------|
| Passer en revue les problèmes connus     | [Liste des problèmes connus](known-issues.md).                |
| Passer en revue les mises à jour de sécurité | [Liste des mises à jour de sécurité](release-notes-security-updates.md).      |
| Appliquer le dernier package OEM | Contactez votre OEM pour vous assurer que votre système répond aux exigences requises pour le package OEM pour la version d’Azure Stack sur laquelle votre système est mis à jour. Vérifiez que votre package OEM est compatible avec la version d’Azure Stack que vous mettez à jour. Si votre package OEM n’est pas compatible avec la version d’Azure Stack que vous mettez à jour, vous devrez effectuer une mise à jour du package OEM avant d’exécuter une mise à jour Azure Stack. Pour plus d’instructions, consultez « Appliquer des mises à jour de fabricants d’ordinateurs (OEM) à Azure Stack. » |
| Appliquer les derniers correctifs | Appliquez les derniers correctifs qui s’appliquent à la version actuellement installée. Pour obtenir la liste des derniers correctifs, consultez la section Correctifs logiciels des notes de publication. |
| Exécuter l’outil de planification des capacités | Veillez à utiliser la dernière version de l’outil [Azure Stack Capacity Planner](azure-stack-capacity-planning-overview.md) pour planifier et dimensionner votre charge de travail. La dernière version contient des corrections de bogues et fournit de nouvelles fonctionnalités publiées à chaque mise à jour d’Azure Stack. |
| Exécuter l’applet de commande Test-AzureStack | Exécutez `Test-AzureStack -Group UpdateReadiness` pour identifier les problèmes fonctionnels. L’applet de commande est accessible via la session PEP (session de point de terminaison privilégié). Pour plus d’informations, consultez [Valider l’état du système Azure Stack](azure-stack-diagnostic-test.md). |
| Résoudre les problèmes | Résolvez les problèmes fonctionnels identifiés par `Test-AzureStack`. |
| Mise à jour disponible | Dans les scénarios connectés, et uniquement dans ceux-ci, les déploiements Azure Stack consultent régulièrement un point de terminaison sécurisé et vous avertissent automatiquement si une mise à jour est disponible pour votre cloud. Les clients déconnectés peuvent télécharger et importer de nouveaux packages en suivant la [procédure décrite ici](azure-stack-apply-updates.md). |
| Notifier vos utilisateurs | Informez les utilisateurs de toutes les opérations de maintenance, et de planifier dans la mesure du possible les fenêtres de maintenance ordinaire pendant les heures creuses. Les opérations de maintenance peuvent affecter les opérations de portail et les charges de travail de locataire. |

## <a name="during-azure-stack-update"></a>Pendant la mise à jour d’Azure Stack

| Activité | Détails |
|--------------------|------------------------------------------------------------------------------------------------------|
| Gérer la mise à jour |[Gérez les mises à jour dans Azure Stack en utilisant le portail de l’opérateur](azure-stack-updates.md). |
|  |  |
| Superviser la mise à jour | Si le portail de l’opérateur n’est pas disponible, [supervisez les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](azure-stack-monitor-update.md). |
|  |  |
| Reprendre les mises à jour | Après avoir résolu une mise à jour ayant échoué, [reprenez les mises à jour dans Azure Stack en utilisant le point de terminaison privilégié](azure-stack-monitor-update.md). |

> [!IMPORTANT]  
> N’exécutez pas **Test-AzureStack** pendant une mise à jour, sinon celle-ci se bloque.

## <a name="after-azure-stack-update"></a>Après la mise à jour d’Azure Stack

| Activité | Détails |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Appliquer les derniers correctifs | Appliquez les derniers correctifs applicables à la version mise à jour. |
| Récupérer les clés de chiffrement | Récupérez les clés de chiffrement de données au repos et stockez-les en toute sécurité hors de votre déploiement Azure Stack. Suivez les [instructions sur la façon de récupérer les clés](azure-stack-security-bitlocker.md). |
|  |  |
| Réactiver l’architecture mutualisée | Dans une instance Azure Stack mutualisée, [assurez-vous de configurer tous les locataires d'annuaire invités](azure-stack-enable-multitenancy.md#configure-guest-directory) après une mise à jour réussie. |

## <a name="next-steps"></a>Étapes suivantes

- [Consulter la liste des problèmes connus](known-issues.md)
- [Consulter la liste des mises à jour de sécurité](release-notes-security-updates.md)
