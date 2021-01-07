---
title: Liste de contrôle des activités de mise à jour Azure Stack Hub
description: Liste de contrôle pour préparer votre système à la dernière mise à jour Azure Stack Hub.
author: PatAltimore
ms.topic: article
ms.date: 08/10/2020
ms.author: patricka
ms.reviewer: TBD
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: 53e34636ab29194f1c99fdb86aaf0f17315163c9
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868384"
---
# <a name="azure-stack-hub-update-activity-checklist"></a>Liste de contrôle des activités de mise à jour Azure Stack Hub

Passez en revue cette liste de contrôle pour préparer une mise à jour Azure Stack Hub. Cet article contient une liste de contrôle des activités liées aux mises à jour pour les opérateurs Azure Stack Hub.

## <a name="prepare-for-azure-stack-hub-update"></a>Préparer la mise à jour Azure Stack Hub

| &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Activité &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;                   | Détails                                                   |
|------------------------------|-----------------------------------------------------------|
| Passer en revue les problèmes connus     | [Liste des problèmes connus](known-issues.md).                |
| Passer en revue les mises à jour de sécurité | [Liste des mises à jour de sécurité](release-notes-security-updates.md).      |
| Passer en revue les mises à jour complémentaires des fournisseurs de ressources | [App Service](azure-stack-app-service-update.md)<br>[Hubs d'événements](resource-provider-apply-updates.md)<br> [MySQL](azure-stack-mysql-resource-provider-update.md)<br>[SQL](azure-stack-sql-resource-provider-update.md)<br>  |
| Appliquer le dernier package OEM | Contactez votre OEM afin de vérifier que votre système répond aux exigences minimales du package OEM pour la version Azure Stack Hub vers laquelle votre système est mis à jour. Vérifiez que votre package OEM est compatible avec la version d’Azure Stack Hub vers laquelle vous effectuez la mise à jour. Si votre package OEM n’est pas compatible avec la version d’Azure Stack Hub vers laquelle vous effectuez la mise à jour, vous devez effectuer une mise à jour du package OEM avant d’exécuter une mise à jour Azure Stack Hub. Pour obtenir des instructions, consultez « Appliquer des mises à jour de fabricants d’ordinateurs (OEM) à Azure Stack Hub ». |
| Facultatif : Configurer la collecte automatique des journaux | Il est recommandé de configurer la collecte automatique des journaux sur votre environnement Azure Stack Hub pour simplifier le processus de collecte des journaux système dans le cas où vous devriez ouvrir un ticket de support. Pour configurer la collecte automatique des journaux, consultez les instructions fournies dans [Envoyer les journaux de manière proactive](./azure-stack-diagnostic-log-collection-overview.md#send-logs-proactively). |
| Appliquer les derniers correctifs | Appliquez les derniers correctifs qui s’appliquent à la version actuellement installée. Pour obtenir la liste des derniers correctifs, consultez la section [Correctifs logiciels des notes de publication](release-notes.md). |
| Exécuter l’outil de planification des capacités | Veillez à utiliser la dernière version de l’[outil Azure Stack Hub Capacity Planner](azure-stack-capacity-planning-overview.md) pour effectuer la planification et le dimensionnement de votre charge de travail. La dernière version contient des résolutions de bogues et fournit de nouvelles fonctionnalités publiées avec chaque mise à jour Azure Stack Hub. |
| Exécuter **Test-AzureStack** | Exécutez `Test-AzureStack -Group UpdateReadiness` pour identifier les problèmes fonctionnels. L’applet de commande est accessible via la session PEP (session de point de terminaison privilégié). Pour plus d’informations, consultez [Valider l’état du système Azure Stack Hub](azure-stack-diagnostic-test.md). |
| Résoudre les problèmes | Résolvez les problèmes fonctionnels identifiés par `Test-AzureStack`. |
| Mise à jour disponible | Dans les scénarios connectés uniquement, les déploiements Azure Stack Hub vérifient périodiquement un point de terminaison sécurisé et vous notifient automatiquement l’existence d’une mise à jour pour votre cloud. Les clients déconnectés peuvent télécharger et importer de nouveaux packages en suivant la [procédure décrite ici](azure-stack-apply-updates.md). |
| Planifier une fenêtre de maintenance et informer vos utilisateurs | Informez les utilisateurs de toutes les opérations de maintenance et planifiez dans la mesure du possible les fenêtres de maintenance ordinaire pendant les heures creuses. Les opérations de maintenance peuvent affecter les charges de travail de locataires existantes et provoquer l’échec des nouvelles opérations de locataires (par exemple, la création, la reconfiguration ou la suppression de machines virtuelles), si l’opération est lancée à partir du portail ou par programme à partir de l’API Azure Resource Manager. D’autres opérations, comme la sauvegarde, peuvent également ne pas être disponibles tant que la mise à jour n’est pas terminée. Pour les mises à jour express et complètes d’Azure Stack Hub, vous pouvez consulter les [notes de publication](release-notes.md) afin d’avoir une estimation du délai d’attente pour recevoir la mise à jour de la version que vous appliquez. |

## <a name="during-azure-stack-hub-update"></a>Durant l’application de la mise à jour Azure Stack Hub

| Activité | Détails |
|--------------------|------------------------------------------------------------------------------------------------------|
| Gérer la mise à jour |[Gérez les mises à jour dans Azure Stack Hub à l’aide du portail opérateur](azure-stack-updates.md). |
|  |  |
| Superviser la mise à jour | Si le portail opérateur n’est pas disponible, [supervisez les mises à jour dans Azure Stack Hub à l’aide du point de terminaison privilégié](azure-stack-monitor-update.md). |
|  |  |
| Reprendre les mises à jour | Après avoir corrigé une mise à jour non réussie, [reprenez les mises à jour dans Azure Stack Hub à l’aide du point de terminaison privilégié](azure-stack-monitor-update.md). |

> [!IMPORTANT]  
> N'exécutez pas **Test-AzureStack** pendant une mise à jour car celle-ci se bloquera.
>
>N'exécutez aucune réparation de nœud pendant une mise à jour, quel que soit son état.
>Veuillez contacter le Support Microsoft si une réparation de nœud est nécessaire pendant la mise à jour.

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
