---
title: Récupérer des données dans Azure Stack avec le service Infrastructure Backup - MDC
description: Découvrez comment sauvegarder et restaurer la configuration et les données du service dans Azure Stack en utilisant le service Infrastructure Backup. Pour un Modular Data Center (MDC).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 5a9e8615168714b3346f735d9182c38b2c604cca
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910803"
---
# <a name="recover-data-in-azure-stack-with-the-infrastructure-backup-service---modular-data-center-mdc"></a>Récupérer des données dans Azure Stack avec le service Infrastructure Backup - Modular Data Center (MDC)

*S’applique à : Modular Data Center et Azure Stack Hub renforcé*

Vous pouvez sauvegarder les métadonnées du service d'infrastructure en utilisant le service Infrastructure Backup d'Azure Stack. Ces sauvegardes permettent de réparer les services d'infrastructure détériorés. La sauvegarde comprend uniquement les données des services d'infrastructure internes au système. Les données de sauvegarde ne comprennent pas les données relatives aux utilisateurs et aux applications. Ces dernières doivent être protégées séparément.

Par défaut, la sauvegarde de l'infrastructure est activée au moment du déploiement. Ces sauvegardes sont stockées dans le système et uniquement accessibles dans le cadre des cas de support avancés. Si le système a accès à un emplacement de stockage externe, le service de sauvegarde de l'infrastructure peut être invité à exporter une sauvegarde vers cet emplacement de stockage en tant que copie secondaire.

Avant d’activer votre service de sauvegarde, vérifiez que [la configuration requise est en place](../../operator/azure-stack-backup-reference.md#backup-controller-requirements).

> [!NOTE]
> Le service Infrastructure Backup n’inclut pas les données utilisateur ni les applications. Pour plus d'informations sur la protection des applications basées sur des machines virtuelles IaaS, consultez les articles suivants :
>
> - [Protéger des machines virtuelles déployées sur Azure Stack](../../user/azure-stack-manage-vm-protect.md)
> - Protéger les données des séries chronologiques Event Hubs sur Azure Stack
> - Protéger les données des objets blob sur Azure Stack

## <a name="the-infrastructure-backup-service"></a>Le service Infrastructure Backup

Le service contient les fonctionnalités suivantes :

| Fonctionnalité                                            | Description                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Services d’infrastructure de sauvegarde                     | Coordonne la sauvegarde sur une partie des services d'infrastructure d'Azure Stack. |
| Compression et chiffrement des données de sauvegarde | Les données de sauvegarde sont compressées et chiffrées par le système avant d'être stockées en interne ou exportées vers l'emplacement de stockage externe fourni par l'opérateur.                |
| Supervision des travaux de sauvegarde                              | Le système vous notifie quand des travaux de sauvegarde échouent et vous indique comment résoudre le problème.                                                                                                |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Vérifier la configuration requise pour le service Infrastructure Backup

- **Emplacement de stockage** S'il existe un accès fiable à un emplacement de stockage externe, il vous faut un partage de fichiers accessible depuis l'infrastructure Azure Stack et capable de stocker plusieurs sauvegardes. Pour plus d'informations sur la sélection d'un emplacement de stockage pour le service Infrastructure Backup, consultez [Configuration requise pour le contrôleur de sauvegarde](../../operator/azure-stack-backup-reference.md#backup-controller-requirements).

- **Informations d'identification** Vous devez disposer des informations d'identification du compte d'utilisateur pour accéder à l'emplacement de stockage.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [activer la sauvegarde d’Azure Stack à partir du portail administrateur](../../operator/azure-stack-backup-enable-backup-console.md).

- Découvrez comment [activer la sauvegarde d’Azure Stack avec PowerShell](../../operator/azure-stack-backup-enable-backup-powershell.md).
