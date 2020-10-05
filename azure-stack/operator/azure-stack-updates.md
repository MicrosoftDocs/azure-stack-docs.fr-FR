---
title: Gérer les mises à jour
description: Découvrez comment gérer les mises à jour dans Azure Stack Hub
author: sethmanheim
ms.topic: how-to
ms.date: 09/25/2020
ms.author: sethm
ms.lastreviewed: 07/16/2020
ms.reviewer: niy
ms.openlocfilehash: e769999ce24e1571b93c94a707c62df757460705
ms.sourcegitcommit: bf7b1a394ede397dba2b75f90bdf953b3ff2f2be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91367221"
---
# <a name="manage-updates-in-azure-stack-hub"></a>Gérer les mises à jour dans Azure Stack Hub

Les mises à jour complètes et expresses, les correctifs logiciels ainsi que les mises à jour des pilotes et des microprogrammes des fabricants d’ordinateurs (OEM) vous aident à maintenir Azure Stack Hub à jour. Cet article explique les différents types de mises à jour, leur mise à disposition prévue ainsi que l’emplacement des notes de publication de la version actuelle.

> [!NOTE]  
> Vous ne pouvez pas appliquer les packages de mise à jour Azure Stack Hub au Kit de développement Azure Stack (ASDK). Les packages de mise à jour sont conçues pour les systèmes intégrés. Pour plus d’informations, consultez [Redéployer l’ASDK](../asdk/asdk-redeploy.md).

## <a name="update-package-types"></a>Types de packages de mise à jour

Il existe trois types de packages de mises à jour pour les systèmes intégrés :

- **Mises à jour logicielles Azure Stack Hub**. Microsoft est responsable du cycle de vie de maintenance de bout en bout pour les packages de mise à jour logicielle Microsoft. Ces packages peuvent inclure les dernières mises à jour de sécurité Windows Server, des mises à jour ne concernant pas la sécurité, et des mises à jour de fonctionnalités Azure Stack Hub. Téléchargez ces packages de mise à jour directement à partir de Microsoft.

    Chaque package de mise à jour a un type correspondant : **Complet** ou **Express**.

    Les packages de mise à jour **Complète** mettent à jour les systèmes d’exploitation hôtes physiques dans l’unité d’échelle et nécessitent une plus grande fenêtre de maintenance.

    Les packages de mise à jour **Express** ont une étendue limitée et ne mettent pas à jour les systèmes d’exploitation hôtes physiques sous-jacents.

- **Correctifs logiciels Azure Stack Hub**. Microsoft fournit des [correctifs logiciels pour Azure Stack Hub](azure-stack-servicing-policy.md#hotfixes) destinés à résoudre un problème spécifique, souvent à titre préventif ou en réponse à une situation d’urgence. Chaque correctif est publié en même temps que son article correspondant dans la Base de connaissances Microsoft, où sont détaillés le problème, la cause et la résolution. Vous téléchargez et installez des correctifs logiciels comme des packages de mises à jour complets pour Azure Stack Hub. Les correctifs sont cumulatifs et peuvent être installés en quelques minutes.

   À partir de la build 2005, lorsque vous mettez à jour vers une nouvelle version principale (par exemple, 1.2002.x vers 1.2005.x), les derniers correctifs (le cas échéant) de la nouvelle version principale sont installés automatiquement. À partir de là, si un correctif est mis en production pour votre build, vous devez l’installer.

- **Mises à jour fournies par le fabricant de matériel OEM**. Les fabricants de matériel partenaires d’Azure Stack Hub sont responsables du cycle de vie de maintenance de bout en bout (notamment l’aide) pour les mises à jour des microprogrammes et des pilote liés au matériel. De plus, ces fabricants de matériel partenaires d’Azure Stack Hub détiennent et gèrent l’aide relative à l’ensemble des logiciels et du matériel sur l’hôte du cycle de vie de matériel. Le fabricant de matériel OEM héberge ces packages de mise à jour sur son propre site de téléchargement.

## <a name="when-to-update"></a>Quand mettre à jour

Les trois types de mises à jour sont publiés à la cadence suivante :

- **Mises à jour logicielles Azure Stack Hub**. Microsoft publie plusieurs packages de mises à jour logicielles complets et rapides par an.

- **Correctifs logiciels Azure Stack Hub**. Les correctifs sont des versions urgentes qui peuvent être publiées à tout moment. Si vous effectuez une mise à niveau d’une version principale à une autre, (par exemple, 1.2002.x vers 1.2005.x), les derniers correctifs (le cas échéant) de la nouvelle version principale sont installés automatiquement.

- **Mises à jour fournies par le fabricant de matériel OEM**. Les fabricants de matériel OEM publient leurs mises à jour en fonction des besoins.

Pour continuer à bénéficier du support, vous devez conserver votre environnement Azure Stack Hub sur une version prise en charge du logiciel Azure Stack Hub. Pour plus d’informations, consultez [Stratégie de maintenance Azure Stack Hub](azure-stack-servicing-policy.md).

## <a name="how-to-know-an-update-is-available"></a>Comment savoir si une mise à jour disponible

La notification des mises à jour dépend de plusieurs facteurs, comme votre connexion à Internet et le type de mise à jour.

- **Mises à jour et correctifs logiciels Microsoft**

    Une alerte pour les mises à jour et les correctifs logiciels Microsoft s’affiche dans le panneau **Mise à jour** pour les instances Azure Stack Hub connectées à Internet. Si le panneau **Mettre à jour** n’est pas affiché, redémarrez la machine virtuelle du contrôleur de gestion de l’infrastructure.

    Si votre instance n’est pas connectée et que vous souhaitez être notifié chaque fois qu’un correctif logiciel est publié, abonnez-vous au flux [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) ou [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom).

- **Mises à jour fournies par le fabricant de matériel OEM**

    Les mises à jour OEM dépendent du fabricant. Vous devez établir un canal de communication avec votre OEM afin de savoir quand des mises à jour de votre OEM doivent être appliquées. Pour plus d’informations sur les OEM et le processus de mise à jour OEM, voir [Appliquer des mises à jour de fabricants de matériel (OEM) à Azure Stack Hub](azure-stack-update-oem.md).

### <a name="major-version-to-major-version"></a>Version principale vers version principale

Une mise à jour d’une version principale vers une version principale doit être effectuée étape par étape : l’environnement actuel peut uniquement être mis à jour vers la version principale suivante, et vous ne pouvez pas ignorer une mise à jour de la version principale.

Par exemple, si votre environnement Azure Stack Hub est 1908.x et que la dernière version de mise à jour disponible est 2002.x, vous devez effectuer la mise à jour de 1908 à 1910, puis vers 2002.

À partir de la build 2005, lorsque vous mettez à jour vers une nouvelle version principale (par exemple, 1.2002.x vers 1.2005.x), les derniers correctifs (le cas échéant) de la nouvelle version principale sont installés automatiquement.

### <a name="hotfixes-within-major-versions"></a>Correctifs dans les versions principales

Dans le même numéro de version principale, Azure Stack Hub peut publier plusieurs correctifs. Les correctifs sont cumulatifs ; le package de correctifs le plus récent comprend tous les correctifs précédents pour cette version. Pour plus d’informations, consultez [Correctifs](azure-stack-servicing-policy.md#hotfixes).

## <a name="update-process"></a>Processus de mise à jour

Une fois que vous avez été averti de la disponibilité d’une mise à jour, appliquez la mise à jour en effectuant les étapes décrites ci-après.

![Processus de mise à jour d’Azure Stack Hub](./media/azure-stack-updates/azure-stack-update-process.svg)

1. **Planifiez la mise à jour**

    Préparez votre infrastructure Azure Stack Hub pour que le processus de mise à jour se déroule de manière aussi fluide que possible et avec un impact minimal sur vos utilisateurs. Informez vos utilisateurs du risque d’interruption du service, puis effectuez les étapes indiquées pour préparer votre instance à la mise à jour. Veillez à suivre toutes les étapes de la [liste de vérification avant la mise à jour Azure Stack Hub](release-notes-checklist.md) pour vous assurer que vous avez accompli les étapes requises avant d’appliquer une mise à jour. Veillez aussi à planifier une fenêtre de maintenance appropriée pour le type de la mise à jour appliquée.

2. **Télécharger et préparer le package de mises à jour**

    Pour les environnements Azure Stack Hub connectés à Internet, les mises à jour logicielles et les correctifs logiciels Azure Stack Hub sont importés automatiquement dans le système et préparés pour la mise à jour.

    Pour les environnements Azure Stack Hub déconnectés d’Internet et les environnements ayant une connectivité Internet faible ou intermittente, les packages de mise à jour sont importés dans le stockage Azure Stack Hub à partir du portail d’administration Azure Stack Hub. Pour plus d’informations sur le chargement et la préparation de la mise à jour, consultez [Charger et préparer une mise à jour Azure Stack Hub](azure-stack-update-prepare-package.md).

    Toutes les mises à jour OEM sont importées manuellement dans votre environnement, quelle que soit la connectivité Internet de votre système Azure Stack Hub. Pour plus d’informations sur l’importation et la préparation de la mise à jour, consultez [Charger et préparer une mise à jour Azure Stack Hub](azure-stack-update-prepare-package.md).

3. **Appliquer la mise à jour**

    Appliquez la mise à jour à l’aide du panneau **Mettre à jour** du portail Azure Stack Hub. Pendant la mise à jour, surveillez la progression du processus et résolvez les problèmes éventuels. Pour plus d’informations, consultez [Appliquer une mise à jour Azure Stack Hub](azure-stack-apply-updates.md).

## <a name="the-update-resource-provider"></a>Le fournisseur de ressources de mise à jour

Azure Stack Hub comprend un fournisseur de ressources de mise à jour qui gère l’application des mises à jour logicielles Microsoft. Ce fournisseur vérifie que les mises à jour sont appliquées sur tous les hôtes physiques, sur les applications et runtimes Service Fabric et sur toutes les machines virtuelles d’infrastructure et leurs services associés.

Lors de l’installation des mises à jour, vous pouvez afficher l’état général à mesure que le processus de mise à jour cible les différents sous-systèmes dans Azure Stack Hub (par exemple, des hôtes physiques et des machines virtuelles d’infrastructure).

## <a name="next-steps"></a>Étapes suivantes

- Pour commencer le processus de mise à jour, suivez les étapes décrites dans [Liste de contrôle des activités de mise à jour Azure Stack Hub](release-notes-checklist.md).
- Pour savoir quelles sont les versions d’Azure Stack Hub prises en charge, voir [Stratégie de maintenance Azure Stack Hub](azure-stack-servicing-policy.md).  
- Pour en savoir plus sur les mises à jour actuelles et récentes, consultez les [Notes de publication d’Azure Stack Hub](release-notes.md).
