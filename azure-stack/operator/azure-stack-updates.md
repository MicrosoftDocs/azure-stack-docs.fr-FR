---
title: Gérer les mises à jour dans Azure Stack | Microsoft Docs
description: Apprendre à gérer les mises à jour dans Azure Stack
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/03/2019
ms.author: justinha
ms.lastreviewed: 09/03/2019
ms.reviewer: ppacent
ms.openlocfilehash: f34f31ac54881c5911488f5049cc75ffee85bd38
ms.sourcegitcommit: 314fd74caf356b157583d38d2b8b1dee30408b7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70234933"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Gérer les mises à jour dans Azure Stack - Vue d’ensemble

*S’applique à : systèmes intégrés Azure Stack*

Les mises à jour complètes et express, les correctifs logiciels ainsi que les mises à jour des microprogrammes et pilotes des fabricants d’ordinateurs (OEM) vous aident à tenir Azure Stack à jour. Cet article explique les différents types de mises à jour, leur mise à disposition prévue ainsi que l’emplacement des notes de publication de la version actuelle.

> [!Note]  
> Vous ne pouvez pas appliquer des packages de mise à jour Azure Stack au Kit de développement Azure Stack (ASDK). Les packages de mise à jour sont conçues pour les systèmes intégrés. Pour plus d’informations, consultez [Redéployer l’ASDK](https://docs.microsoft.com/azure-stack/asdk/asdk-redeploy).

## <a name="update-package-types"></a>Types de packages de mise à jour

Il existe trois types de packages de mises à jour pour les systèmes intégrés :

-   **Mises à jour de logiciel pour Azure Stack**. Microsoft est responsable du cycle de vie de maintenance de bout en bout pour les packages de mise à jour logicielle Microsoft. Ces packages peuvent inclure les dernières mises à jour de sécurité Windows Server, des mises à jour ne concernant pas la sécurité, et des mises à jour de fonctionnalités Azure Stack. Téléchargez ces packages de mise à jour directement à partir de Microsoft.

    Chaque package de mise à jour a un type correspondant, **Complet** ou **Express**. 
 
    Les packages de mise à jour **Complète** mettent à jour les systèmes d’exploitation hôtes physiques dans l’unité d’échelle et nécessitent une plus grande fenêtre de maintenance. 

    Les packages de mise à jour **Express** ont une étendue limitée et ne mettent pas à jour les systèmes d’exploitation hôtes physiques sous-jacents.

-   **Correctifs logiciels d’Azure Stack**. Microsoft fournit des correctifs logiciels pour Azure Stack qui corrigent un problème précis souvent préventif ou urgent. Chaque correctif est publié en même temps que son article correspondant dans la Base de connaissances Microsoft, où sont détaillés le problème, la cause et la résolution. Les correctifs logiciels sont téléchargés et installés comme les packages de mises à jour complets pour Azure Stack. Les correctifs sont cumulatifs et peuvent être installés en quelques minutes.

-   **Mises à jour fournies par le fabricant de matériel OEM**. Les fabricants de matériel partenaires d’Azure Stack sont responsables du cycle de vie de maintenance de bout en bout (y compris les instructions) pour les mises à jour logicielles des microprogrammes et des pilotes liées au matériel. De plus, ils sont propriétaires et gèrent les instructions pour tous les logiciels et le matériel sur l’hôte du cycle de vie de matériel. Le fabricant de matériel OEM héberge ces packages de mise à jour sur son propre site de téléchargement.

## <a name="when-to-update"></a>Quand mettre à jour

Les trois types de mises à jour sont fournis à la cadence suivante :

-   **Mises à jour de logiciel pour Azure Stack**. Microsoft publie généralement des packages de mises à jour logicielles chaque mois.

-   **Correctifs logiciels d’Azure Stack**. Les correctifs sont des versions urgentes qui peuvent être publiées à tout moment.

-   **Mises à jour fournies par le fabricant de matériel OEM**. Les fabricants de matériel OEM publient leurs mises à jour en fonction des besoins.

Pour continuer à bénéficier de la prise en charge, vous devez conserver votre environnement Azure Stack sur une version logicielle d’Azure Stack prise en charge. Pour plus d’informations, consultez [Stratégie de maintenance Azure Stack](azure-stack-update-servicing-policy.md).

## <a name="where-to-get-notice-of-an-update"></a>Où recevoir une notification de mise à jour

Les mises à jour varient en fonction de plusieurs facteurs, tels que votre connexion à Internet et le type de mise à jour.

- **Mises à jour et correctifs logiciels Microsoft** 

    Une alerte de mise à jour pour les mises à jour et les correctifs logiciels Microsoft s’affiche dans le panneau de mise à jour pour les instances Azure Stack connectées à Internet.

    Si votre instance n’est pas connectée et que vous souhaitez être notifié chaque fois qu’un correctif logiciel est publié, abonnez-vous au flux [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/32d322a8-acae-202d-e9a9-7371dccf381b/rss) ou [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/32d322a8-acae-202d-e9a9-7371dccf381b/atom).

- **Mises à jour fournies par le fabricant de matériel OEM**

    Les mises à jour OEM dépendent du fabricant. Vous devez établir un canal de communication avec votre OEM afin de savoir quand des mises à jour de votre OEM doivent être appliquées. Pour plus d’informations sur les OEM et le processus de mise à jour OEM, consultez [Appliquer des mises à jour de fabricants d’ordinateurs (OEM) à Azure Stack](azure-stack-update-oem.md).

## <a name="update-processes"></a>Processus de mise à jour

Une fois que vous avez été averti de la disponibilité d’une mise à jour, appliquez la mise à jour en effectuant les étapes décrites ci-après.

![Processus de mise à jour d'Azure Stack](./media/azure-stack-updates/azure-stack-update-process.png)

1. **Planifiez la mise à jour**.

    Préparez votre Azure Stack pour que le processus de mise à jour se déroule aussi facilement que possible, afin qu’il ait un impact minimal sur vos utilisateurs. Informez vos utilisateurs de toute interruption de service possible, puis suivez les étapes pour préparer votre instance pour la mise à jour. Pour plus d’informations sur la planification de la mise à jour, consultez [Planifier une mise à jour Azure Stack](azure-stack-update-plan.md).

2. **Télécharger et préparer le package de mise à jour**.

    Pour les environnements Azure Stack connectés à Internet, les mises à jour et les correctifs logiciels Azure Stack sont importés automatiquement dans le système et préparés pour la mise à jour.

    Pour les environnements Azure Stack déconnectés d’Internet ou avec une connectivité Internet faible ou intermittente, les packages de mise à jour sont importés dans le stockage Azure Stack via le portail d’administration Azure Stack. Pour plus d’informations sur la façon de télécharger et de préparer le package de mise à jour, consultez [Télécharger et préparer un package de mise à jour Azure Stack](azure-stack-update-prepare-package.md).

    Tous les packages de mise à jour OEM sont importés manuellement dans votre environnement, quelle que soit la connectivité Internet de votre système Azure Stack. Pour plus d’informations sur la façon d’importer et de préparer le package de mise à jour, consultez [Charger et préparer un package de mise à jour Azure Stack](azure-stack-update-prepare-package.md).

3. **Appliquez la mise à jour**.

    Appliquez la mise à jour à l’aide du panneau **Mise à jour** dans Azure Stack. Pendant la mise à jour, surveillez la progression du processus et corrigez les problèmes éventuels. Pour plus d’informations, consultez [Appliquer une mise à jour Azure Stack](azure-stack-apply-updates.md).

## <a name="the-update-resource-provider"></a>Le fournisseur de ressources de mise à jour

Azure Stack inclut un fournisseur de ressources de mise à jour qui gère l’application des mises à jour logicielles Microsoft. Ce fournisseur vérifie que les mises à jour sont appliquées sur tous les hôtes physiques, les applications et runtimes Service Fabric, et toutes les machines virtuelles d’infrastructure et leurs services associés.

Lors de l’installation des mises à jour, vous pouvez afficher l’état général à mesure que le processus de mise à jour cible les différents sous-systèmes dans Azure Stack (par exemple les hôtes physiques et les machines virtuelles d’infrastructure).

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer le processus de mise à jour, suivez les étapes de la section [Planifier une mise à jour d’Azure Stack](azure-stack-update-plan.md).
- Pour savoir quelles versions d’Azure Stack sont prises en charge, consultez [Stratégie de maintenance Azure Stack](azure-stack-servicing-policy.md).  
- Pour en savoir plus sur les mises à jour actuelles et récentes, consultez les [notes de publication d’Azure Stack](azure-stack-release-notes-security-updates-1907.md).
