---
title: Notes de publication Azure Stack - check-list des activités de mise à jour | Microsoft Docs
description: Petite check-list pour préparer votre système à la dernière mise à jour Azure Stack.
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
ms.date: 07/25/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 0c7840c723aedd38b51002e4a1f18f5a87d66ec4
ms.sourcegitcommit: c2690b2dd36918ff3e47e359cac926128bb83101
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68544077"
---
# <a name="azure-stack-update-activity-checklist"></a>Check-list des activités de mise à jour Azure Stack

Cet article contient une check-list des activités liées aux mises à jour pour les opérateurs Azure Stack. Si vous vous préparez à appliquer une mise à jour sur Azure Stack, vous pouvez consulter ces informations.

## <a name="prepare-for-azure-stack-update"></a>Se préparer à la mise à jour d’Azure Stack

| Activité                     | Détails                                                   |
|------------------------------|-----------------------------------------------------------|
| Passer en revue les problèmes connus     | [Liste des problèmes connus](azure-stack-release-notes-known-issues-1906.md).                |
| Passer en revue les mises à jour de sécurité | [Liste des mises à jour de sécurité](azure-stack-release-notes-security-updates-1906.md).      |
| Appliquer le dernier package OEM | Contactez votre OEM pour vous assurer que votre système répond aux exigences requises pour le package OEM pour la version d’Azure Stack sur laquelle votre système est mis à jour. |
| Exécuter l’applet de commande Test-AzureStack     | Exécutez `Test-AzureStack -Group UpdateReadiness` pour identifier les problèmes fonctionnels.      |
| Résoudre les problèmes          | Résolvez les problèmes fonctionnels identifiés par **Test-AzureStack**.                |
| Appliquer les derniers correctifs   | Appliquez les derniers correctifs qui s’appliquent à la version actuellement installée.         |
| Exécuter l’outil de planification des capacités   | Veillez à utiliser la dernière version de l’outil [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) pour planifier et dimensionner votre charge de travail. La dernière version contient des corrections de bogues et fournit de nouvelles fonctionnalités publiées à chaque mise à jour d’Azure Stack. |
| Mise à jour disponible        | Dans les scénarios connectés, et uniquement dans ceux-ci, les déploiements Azure Stack consultent régulièrement un point de terminaison sécurisé et vous avertissent automatiquement si une mise à jour est disponible pour votre cloud. Les clients déconnectés peuvent télécharger et importer le nouveau package 1906 en suivant la [procédure décrite ici](azure-stack-apply-updates.md). |


## <a name="during-azure-stack-update"></a>Pendant la mise à jour d’Azure Stack

| Activité              | Détails                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| Gérer la mise à jour         | [Gérez les mises à jour dans Azure Stack en utilisant le portail de l’opérateur](azure-stack-updates.md). |
| Superviser la mise à jour        | Si le portail de l’opérateur n’est pas disponible, [supervisez les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](azure-stack-monitor-update.md). |
| Reprendre les mises à jour            | Après avoir résolu une mise à jour ayant échoué, [reprenez les mises à jour dans Azure Stack en utilisant le point de terminaison privilégié](azure-stack-monitor-update.md). |

> [!IMPORTANT]  
> N’exécutez pas **Test-AzureStack** pendant une mise à jour, sinon celle-ci se bloque.

## <a name="after-azure-stack-update"></a>Après la mise à jour d’Azure Stack

| Activité              | Détails                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| Appliquer les derniers correctifs | Appliquez les derniers correctifs applicables à la version mise à jour.                          |
| Récupérer les clés de chiffrement | Récupérez les clés de chiffrement de données au repos et stockez-les en toute sécurité hors de votre déploiement Azure Stack. Suivez les [instructions sur la façon de récupérer les clés](azure-stack-security-bitlocker.md). |
| Réactiver l’architecture mutualisée | Dans une instance Azure Stack mutualisée, [assurez-vous de configurer tous les locataires d'annuaire invités](https://docs.microsoft.com/azure-stack/operator/azure-stack-enable-multitenancy#configure-guest-directory) après une mise à jour réussie. |

## <a name="next-steps"></a>Étapes suivantes

- [Consulter la liste des problèmes connus](azure-stack-release-notes-known-issues-1907.md)
- [Consulter la liste des mises à jour de sécurité](azure-stack-release-notes-security-updates-1907.md)
