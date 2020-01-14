---
title: Liste de contrôle des activités de mise à jour Azure Stack Hub | Microsoft Docs
description: Liste de contrôle pour préparer votre système à la dernière mise à jour Azure Stack Hub.
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
ms.date: 12/10/2019
ms.author: sethm
ms.reviewer: ppacent
ms.lastreviewed: 12/10/2019
ms.openlocfilehash: a626b50a742d3219d7934e4cc3f77cd890ea1b93
ms.sourcegitcommit: c3be6b2e962c5905eb3c54f9555e13095f6b4d40
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2019
ms.locfileid: "75303720"
---
# <a name="azure-stack-hub-update-activity-checklist"></a>Liste de contrôle des activités de mise à jour Azure Stack Hub

*S’applique à : Systèmes intégrés Azure Stack Hub*

Passez en revue cette liste de contrôle pour préparer une mise à jour Azure Stack Hub. Cet article contient une liste de contrôle des activités liées aux mises à jour pour les opérateurs Azure Stack Hub.

## <a name="prepare-for-azure-stack-hub-update"></a>Préparer la mise à jour Azure Stack Hub

| Activité                     | Détails                                                   |
|------------------------------|-----------------------------------------------------------|
| Passer en revue les problèmes connus     | [Liste des problèmes connus](known-issues.md).                |
| Passer en revue les mises à jour de sécurité | [Liste des mises à jour de sécurité](release-notes-security-updates.md).      |
| Appliquer le dernier package OEM | Contactez votre OEM afin de vérifier que votre système répond aux exigences minimales du package OEM pour la version Azure Stack Hub vers laquelle votre système est mis à jour. Vérifiez que votre package OEM est compatible avec la version d’Azure Stack Hub vers laquelle vous effectuez la mise à jour. Si votre package OEM n’est pas compatible avec la version d’Azure Stack Hub vers laquelle vous effectuez la mise à jour, vous devez effectuer une mise à jour du package OEM avant d’exécuter une mise à jour Azure Stack Hub. Pour obtenir des instructions, consultez « Appliquer des mises à jour de fabricants d’ordinateurs (OEM) à Azure Stack Hub ». |
| Facultatif : Configurer la collecte automatique des journaux | Il est recommandé de configurer la collecte automatique des journaux sur votre environnement Azure Stack Hub pour simplifier le processus de collecte des journaux système dans le cas où vous devriez ouvrir un ticket de support. Pour configurer la collecte automatique des journaux, consultez les instructions figurant dans [Configurer la collecte automatique des journaux de diagnostic Azure Stack Hub](azure-stack-configure-automatic-diagnostic-log-collection.md). |
| Appliquer les derniers correctifs | Appliquez les derniers correctifs qui s’appliquent à la version actuellement installée. Pour obtenir la liste des derniers correctifs, consultez la section Correctifs logiciels des notes de publication. |
| Exécuter l’outil de planification des capacités | Veillez à utiliser la dernière version de l’[outil Azure Stack Hub Capacity Planner](azure-stack-capacity-planning-overview.md) pour effectuer la planification et le dimensionnement de votre charge de travail. La dernière version contient des résolutions de bogues et fournit de nouvelles fonctionnalités publiées avec chaque mise à jour Azure Stack Hub. |
| Exécuter l’applet de commande Test-AzureStack | Exécutez `Test-AzureStack -Group UpdateReadiness` pour identifier les problèmes fonctionnels. L’applet de commande est accessible via la session PEP (session de point de terminaison privilégié). Pour plus d’informations, consultez [Valider l’état du système Azure Stack Hub](azure-stack-diagnostic-test.md). |
| Résoudre les problèmes | Résolvez les problèmes fonctionnels identifiés par `Test-AzureStack`. |
| Mise à jour disponible | Dans les scénarios connectés uniquement, les déploiements Azure Stack Hub vérifient périodiquement un point de terminaison sécurisé et vous notifient automatiquement l’existence d’une mise à jour pour votre cloud. Les clients déconnectés peuvent télécharger et importer de nouveaux packages en suivant la [procédure décrite ici](azure-stack-apply-updates.md). |
| Notifier vos utilisateurs | Informez les utilisateurs de toutes les opérations de maintenance, et de planifier dans la mesure du possible les fenêtres de maintenance ordinaire pendant les heures creuses. Les opérations de maintenance peuvent affecter les opérations de portail et les charges de travail de locataire. |

## <a name="during-azure-stack-hub-update"></a>Durant l’application de la mise à jour Azure Stack Hub

| Activité | Détails |
|--------------------|------------------------------------------------------------------------------------------------------|
| Gérer la mise à jour |[Gérez les mises à jour dans Azure Stack Hub à l’aide du portail opérateur](azure-stack-updates.md). |
|  |  |
| Superviser la mise à jour | Si le portail opérateur n’est pas disponible, [supervisez les mises à jour dans Azure Stack Hub à l’aide du point de terminaison privilégié](azure-stack-monitor-update.md). |
|  |  |
| Reprendre les mises à jour | Après avoir corrigé une mise à jour non réussie, [reprenez les mises à jour dans Azure Stack Hub à l’aide du point de terminaison privilégié](azure-stack-monitor-update.md). |

> [!IMPORTANT]  
> N’exécutez pas **Test-AzureStack** pendant une mise à jour, sinon celle-ci se bloque.

## <a name="after-azure-stack-hub-update"></a>Après l’application de la mise à jour Azure Stack Hub

| Activité | Détails |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Appliquer les derniers correctifs | Appliquez les derniers correctifs applicables à la version mise à jour. |
| Récupérer les clés de chiffrement | Récupérez les clés de chiffrement des données au repos, et stockez-les de manière sécurisée hors de votre déploiement Azure Stack Hub. Suivez les [instructions sur la façon de récupérer les clés](azure-stack-security-bitlocker.md). |
|  |  |
| Réactiver l’architecture mutualisée | Dans le cas d’une infrastructure Azure Stack Hub multilocataire, [veillez à configurer tous les locataires d’annuaire invités](azure-stack-enable-multitenancy.md#configure-guest-directory) après la réussite d’une mise à jour. |

## <a name="next-steps"></a>Étapes suivantes

- [Consulter la liste des problèmes connus](known-issues.md)
- [Consulter la liste des mises à jour de sécurité](release-notes-security-updates.md)
