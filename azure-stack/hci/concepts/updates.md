---
title: Mises à jour et mises à niveau
description: Vue d’ensemble de l’application des mises à jour et des mises à niveau à Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: f56853ce64575d32ae10ccf7ac2aa0a1a640ec5b
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86868019"
---
# <a name="updates-and-upgrades"></a>Mises à jour et mises à niveau

> S’applique à : Azure Stack HCI, version 20H2

Les solutions Azure Stack HCI sont conçues pour offrir une expérience de mise à jour et de mise à niveau prévisible.

## <a name="support-period"></a>Période de support

Vous bénéficiez de réparations matérielles, d’une offre de support et de mises à jour de la sécurité fournies par un partenaire de solutions matérielles Microsoft pendant au moins cinq ans. Le système d’exploitation Azure Stack HCI est pris en charge par Microsoft pendant deux ans. Les clients ont un an pour déployer les mises à jour annuelles des fonctionnalités.

:::image type="content" source="media/updates/lifecycle-versions.png" alt-text="Feuille de route des versions annuelles d’Azure Stack HCI" border="false":::

## <a name="updating-azure-stack-hci"></a>Mise à jour d’Azure Stack HCI

Comme Windows 10, Azure Stack HCI bénéficie de mises à jour publiées chaque mois par Microsoft pour améliorer la qualité et la sécurité. Le lancement des mises à jour s’effectue à l’aide de la fonctionnalité de mise à jour adaptée aux clusters, comme sur Windows Server.

Les fabricants de matériel et les créateurs de solutions peuvent se connecter à Windows Admin Center et développer des extensions pour tenir à jour les microprogrammes, pilotes et BIOS des serveurs et assurer leur cohérence entre les nœuds de cluster. Les clients qui achètent un système intégré avec Azure Stack HCI préinstallé peuvent installer des mises à jour de solution par le biais de ces extensions. Les clients qui achètent simplement des nœuds matériels validés devront peut-être effectuer les mises à jour séparément selon les recommandations du fabricant du matériel.

Les machines virtuelles doivent également être mises à jour régulièrement. En plus de Windows Update et de Windows Server Update Services, vous pouvez utiliser Azure Update Management pour mettre à jour les machines virtuelles.

## <a name="upgrading-to-azure-stack-hci"></a>Mise à niveau vers Azure Stack HCI

Vous pouvez mettre à niveau Windows Server 2019 avec Azure Stack HCI, mais l’opération n’est pas transparente. Il n’y a pas de mise à niveau sur place pour l’instant. La mise à niveau nécessite une fenêtre de maintenance et une nouvelle installation sur chaque serveur. Chaque nœud est reconstruit individuellement dans le cadre d’une mise à niveau propagée du cluster.

## <a name="cluster-aware-updating"></a>Mise à jour adaptée aux clusters

La mise à jour d’un cluster nécessite une orchestration. En effet, certaines mises à jour nécessitent un redémarrage du système d’exploitation et les serveurs doivent être redémarrés un par un pour garantir la disponibilité du cluster. La mise à jour adaptée aux clusters est l’orchestrateur de cluster par défaut de Microsoft : vous pouvez soit effectuer les mises à jour dans une expérience de mise à jour intégrée dans Windows Admin Center, soit les lancer manuellement au moyen de commandes PowerShell. La mise à jour adaptée aux clusters est extensible et prend en charge les plug-ins de partenaires qui récupèrent les mises à jour des microprogrammes et des pilotes.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez également :

- [Mettre à jour des clusters Azure Stack HCI](../manage/update-cluster.md)
