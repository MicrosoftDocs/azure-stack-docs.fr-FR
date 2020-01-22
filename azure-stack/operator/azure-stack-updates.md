---
title: Gérer les mises à jour dans Azure Stack Hub | Microsoft Docs
description: Apprenez à gérer les mises à jour dans Azure Stack Hub
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
ms.date: 10/01/2019
ms.author: mabrigg
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: e7f1b45a561d49ffbefd9376308473b225b99fad
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76022481"
---
# <a name="manage-updates-in-azure-stack-hub-overview"></a>Gérer les mises à jour dans Azure Stack Hub - Vue d’ensemble

Les mises à jour complètes et expresses, les correctifs logiciels ainsi que les mises à jour des pilotes et des microprogrammes des fabricants d’ordinateurs (OEM) vous aident à maintenir Azure Stack Hub à jour. Cet article explique les différents types de mises à jour, leur mise à disposition prévue ainsi que l’emplacement des notes de publication de la version actuelle.

> [!Note]  
> Vous ne pouvez pas appliquer de mises à jour Azure Stack Hub au Kit de développement Azure Stack (ASDK). Les packages de mise à jour sont conçues pour les systèmes intégrés. Pour plus d’informations, consultez [Redéployer l’ASDK](https://docs.microsoft.com/azure-stack/asdk/asdk-redeploy).

## <a name="update-package-types"></a>Types de packages de mise à jour

Il existe trois types de packages de mises à jour pour les systèmes intégrés :

-   **Mises à jour logicielles Azure Stack Hub**. Microsoft est responsable du cycle de vie de maintenance de bout en bout pour les packages de mise à jour logicielle Microsoft. Ces packages peuvent inclure les dernières mises à jour de sécurité Windows Server, des mises à jour ne concernant pas la sécurité, et des mises à jour de fonctionnalités Azure Stack Hub. Téléchargez ces packages de mise à jour directement à partir de Microsoft.

    Chaque package de mise à jour a un type correspondant, **Complet** ou **Express**. 
 
    Les packages de mise à jour **Complète** mettent à jour les systèmes d’exploitation hôtes physiques dans l’unité d’échelle et nécessitent une plus grande fenêtre de maintenance. 

    Les packages de mise à jour **Express** ont une étendue limitée et ne mettent pas à jour les systèmes d’exploitation hôtes physiques sous-jacents.

-   **Correctifs logiciels Azure Stack Hub**. Microsoft fournit des correctifs logiciels pour Azure Stack Hub destinés à résoudre un problème spécifique, souvent à titre préventif ou en réponse à une situation d’urgence. Chaque correctif est publié en même temps que son article correspondant dans la Base de connaissances Microsoft, où sont détaillés le problème, la cause et la résolution. Vous téléchargez et installez des correctifs logiciels comme des packages de mises à jour complets pour Azure Stack Hub. Les correctifs sont cumulatifs et peuvent être installés en quelques minutes.

-   **Mises à jour fournies par le fabricant de matériel OEM**. Les fabricants de matériel partenaires d’Azure Stack Hub sont responsables du cycle de vie de maintenance de bout en bout (notamment l’aide) pour les mises à jour des microprogrammes et des pilote liés au matériel. De plus, ces fabricants de matériel partenaires d’Azure Stack Hub détiennent et gèrent l’aide relative à l’ensemble des logiciels et du matériel sur l’hôte du cycle de vie de matériel. Le fabricant de matériel OEM héberge ces packages de mise à jour sur son propre site de téléchargement.

## <a name="when-to-update"></a>Quand mettre à jour

Les trois types de mises à jour sont publiés à la cadence suivante :

-   **Mises à jour logicielles Azure Stack Hub**. Microsoft publie généralement des packages de mises à jour logicielles chaque mois.

-   **Correctifs logiciels Azure Stack Hub**. Les correctifs sont des versions urgentes qui peuvent être publiées à tout moment.

-   **Mises à jour fournies par le fabricant de matériel OEM**. Les fabricants de matériel OEM publient leurs mises à jour en fonction des besoins.

Pour continuer à bénéficier du support, vous devez conserver votre environnement Azure Stack Hub sur une version prise en charge du logiciel Azure Stack Hub. Pour plus d’informations, consultez [Stratégie de maintenance Azure Stack Hub](azure-stack-update-servicing-policy.md).

## <a name="where-to-get-notice-of-an-update"></a>Où recevoir une notification de mise à jour

Les mises à jour varient en fonction de plusieurs facteurs, tels que votre connexion à Internet et le type de mise à jour.

- **Mises à jour et correctifs logiciels Microsoft** 

    Une alerte pour les mises à jour et les correctifs logiciels Microsoft s’affiche dans le panneau Mise à jour pour les instances Azure Stack Hub connectées à Internet. Si le panneau Mettre à jour n’est pas affiché, redémarrez la machine virtuelle du contrôleur de gestion de l’infrastructure.

    Si votre instance n’est pas connectée et que vous souhaitez être notifié chaque fois qu’un correctif logiciel est publié, abonnez-vous au flux [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) ou [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom).

- **Mises à jour fournies par le fabricant de matériel OEM**

    Les mises à jour OEM dépendent du fabricant. Vous devez établir un canal de communication avec votre OEM afin de savoir quand des mises à jour de votre OEM doivent être appliquées. Pour plus d’informations sur les OEM et le processus de mise à jour OEM, voir [Appliquer des mises à jour de fabricants de matériel (OEM) à Azure Stack Hub](azure-stack-update-oem.md).

## <a name="update-processes"></a>Processus de mise à jour

Une fois que vous avez été averti de la disponibilité d’une mise à jour, appliquez la mise à jour en effectuant les étapes décrites ci-après.

![Processus de mise à jour d’Azure Stack Hub](./media/azure-stack-updates/azure-stack-update-process.png)

1. **Planifiez la mise à jour**.

    Préparez votre infrastructure Azure Stack Hub pour que le processus de mise à jour se déroule de manière aussi fluide que possible avec un impact minimal sur vos utilisateurs. Informez vos utilisateurs de toute interruption de service possible, puis suivez les étapes pour préparer votre instance pour la mise à jour. Veillez à suivre **TOUTES** les étapes de la [liste de contrôle préalable à la mise à jour d’Azure Stack Hub](release-notes-checklist.md) pour vous assurer que vous avez accompli les étapes préalables requises pour appliquer une mise à jour, et planifié une fenêtre de maintenance appropriée pour le type de mise à jour en cours d’application.

2. **Télécharger et préparer le package de mise à jour**.

    Pour les environnements Azure Stack Hub connectés à Internet, les mises à jour logicielles et les correctifs logiciels Azure Stack Hub sont importés automatiquement dans le système et préparés pour la mise à jour.

    Pour les environnements Azure Stack Hub déconnectés d’Internet et les environnements ayant une connectivité Internet faible ou intermittente, les mises à jour sont importées dans le stockage Azure Stack Hub via le portail administrateur Azure Stack Hub. Pour plus d’informations sur le chargement et la préparation de la mise à jour, consultez [Charger et préparer une mise à jour Azure Stack Hub](azure-stack-update-prepare-package.md).

    Toutes les mises à jour OEM sont importées manuellement dans votre environnement, quelle que soit la connectivité Internet de votre système Azure Stack Hub. Pour plus d’informations sur l’importation et la préparation de la mise à jour, consultez [Charger et préparer une mise à jour Azure Stack Hub](azure-stack-update-prepare-package.md).

3. **Appliquez la mise à jour**.

    Appliquez la mise à jour à l’aide du panneau **Mise à jour** dans Azure Stack Hub. Pendant la mise à jour, surveillez la progression du processus et corrigez les problèmes éventuels. Pour plus d’informations, consultez [Appliquer une mise à jour Azure Stack Hub](azure-stack-apply-updates.md).

## <a name="the-update-resource-provider"></a>Le fournisseur de ressources de mise à jour

Azure Stack Hub comprend un fournisseur de ressources de mise à jour qui gère l’application des mises à jour logicielles Microsoft. Ce fournisseur vérifie que les mises à jour sont appliquées sur tous les hôtes physiques, les applications et runtimes Service Fabric, et toutes les machines virtuelles d’infrastructure et leurs services associés.

Lors de l’installation des mises à jour, vous pouvez afficher l’état général à mesure que le processus de mise à jour cible les différents sous-systèmes dans Azure Stack Hub (par exemple, des hôtes physiques et des machines virtuelles d’infrastructure).

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer le processus de mise à jour, suivez les étapes décrites dans [Liste de contrôle des activités de mise à jour Azure Stack Hub](release-notes-checklist.md).
- Pour savoir quelles sont les versions d’Azure Stack Hub prises en charge, voir [Stratégie de maintenance Azure Stack Hub](azure-stack-servicing-policy.md).  
- Pour en savoir plus sur les mises à jour actuelles et récentes, consultez les [Notes de publication d’Azure Stack Hub](release-notes.md).
