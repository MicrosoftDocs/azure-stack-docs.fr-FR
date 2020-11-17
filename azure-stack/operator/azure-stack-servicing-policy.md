---
title: Stratégie de maintenance Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Découvrez la stratégie de maintenance Azure Stack Hub et comment conserver un système intégré dans un état de prise en charge.
author: sethmanheim
ms.topic: article
ms.date: 09/25/2020
ms.author: sethm
ms.reviewer: niy
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: cff03367985558bd5b9b6786455cf626993836db
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545565"
---
# <a name="azure-stack-hub-servicing-policy"></a>Stratégie de maintenance Azure Stack Hub

Cet article décrit la stratégie de maintenance pour les systèmes intégrés Azure Stack Hub et la marche à suivre pour que votre système continue d’être pris en charge.

## <a name="download-update-packages-for-integrated-systems"></a>Télécharger les mises à jour pour les systèmes intégrés

Microsoft publie à la fois des packages de mises à jour complètes et des packages de correctifs logiciels pour résoudre des problèmes spécifiques.

Les packages de mises à jour complètes sont hébergées dans un point de terminaison Azure sécurisé. Vous pouvez les télécharger manuellement à l’aide de l’[outil de téléchargement des mises à jour Azure Stack Hub](https://aka.ms/azurestackupdatedownload). Si votre unité d’échelle est connectée, la mise à jour s’affiche automatiquement dans le portail Administrateur avec la notification **Mise à jour disponible**. Pour plus d’informations, cliquez sur l’une des versions listées dans la section [Cadence de publication des mises à jour](#update-package-release-cadence) de cet article.

Les correctifs logiciels sont hébergés dans le même point de terminaison Azure sécurisé. Vous pouvez les télécharger en cliquant sur les liens incorporés dans chaque article correspondant de la Base de connaissances ; par exemple, [Correctif logiciel Azure Stack Hub 1.1809.12.114](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114). Comme pour les mises à jour mensuelles complètes, les opérateurs Azure Stack Hub peuvent télécharger les fichiers .xml et .zip et les importer en suivant la procédure décrite dans [Effectuer des mises à jour dans Azure Stack Hub](azure-stack-apply-updates.md). Pour les opérateurs Azure Stack Hub disposant d’unités d’échelle connectées, les correctifs logiciels s’affichent automatiquement dans le portail Administrateur avec le message **Mise à jour disponible**.

Si votre unité d’échelle n’est pas connectée et si vous souhaitez être notifié chaque fois qu’un correctif logiciel est publié, abonnez-vous au flux [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) ou [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom) noté dans chaque version.

## <a name="update-package-types"></a>Types de packages de mise à jour

Il existe deux types de packages de mises à jour pour les systèmes intégrés :

- **Mises à jour logicielles Microsoft**. Microsoft est responsable du cycle de vie de maintenance de bout en bout pour les packages de mise à jour logicielle Microsoft. Ces packages peuvent inclure les dernières mises à jour de sécurité Windows Server, des mises à jour ne concernant pas la sécurité, et des mises à jour de fonctionnalités Azure Stack Hub. Vous pouvez télécharger ces packages de mise à jour directement à partir de Microsoft.

- **Mises à jour fournies par le fabricant de matériel OEM**. Les fabricants de matériel partenaires d’Azure Stack Hub sont responsables du cycle de vie de maintenance de bout en bout (notamment l’aide) pour les mises à jour des microprogrammes et des pilote liés au matériel. De plus, ces fabricants de matériel partenaires d’Azure Stack Hub détiennent et gèrent l’aide relative à l’ensemble des logiciels et du matériel sur l’hôte du cycle de vie de matériel. Le fabricant de matériel OEM héberge ces packages de mise à jour sur son propre site de téléchargement.

## <a name="update-package-release-cadence"></a>Cadence de publication des packages de mise à jour

Microsoft prévoit de publier des packages de mise à jour logicielle chaque mois.

Les fabricants de matériel OEM publient leurs mises à jour en fonction des besoins. Contactez votre fabricant de matériel OEM pour obtenir les dernières mises à jour du matériel.

Pour lire la documentation relative à la planification et à la gestion des mises à jour, et pour savoir comment déterminer la version que vous utilisez, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](azure-stack-updates.md).

Pour en savoir plus sur une mise à jour, notamment comment la télécharger, consultez les notes de publication qui s’y rapportent :

- [Mise à jour Azure Stack Hub 2008](/azure-stack/operator/release-notes?view=azs-2008)
- [Mise à jour Azure Stack Hub 2005](/azure-stack/operator/release-notes?view=azs-2005)
- [Mise à jour Azure Stack Hub 2002](/azure-stack/operator/release-notes?view=azs-2002)

## <a name="hotfixes"></a>Correctifs logiciels

Parfois, Microsoft fournit des correctifs logiciels pour Azure Stack Hub qui corrigent un problème précis, souvent préventif ou urgent. Chaque correctif est publié avec un article de base de connaissances Microsoft correspondant, qui détaille les problèmes résolus par ce correctif.

À partir de la build 2005, lorsque vous mettez à jour vers une nouvelle version principale (par exemple, 1.2002.x vers 1.2005.x), les derniers correctifs (le cas échéant) de la nouvelle version principale sont installés automatiquement. À partir de là, si un correctif est mis en production pour votre build, vous devez l’installer.

Les correctifs logiciels sont téléchargés et installés comme les packages de mises à jour complets pour Azure Stack Hub. Toutefois, contrairement à une mise à jour complète, les correctifs logiciels peuvent s’installer en quelques minutes. Il est recommandé aux opérateurs Azure Stack Hub de définir des fenêtres de maintenance lors de l’installation des correctifs logiciels. Les correctifs mettent à jour la version de votre cloud Azure Stack Hub. Il est donc facile de déterminer si le correctif a été appliqué. Un correctif logiciel distinct est fourni pour chaque version d’Azure Stack Hub prise en charge. **Chaque correctif d’une itération est cumulatif et englobe les correctifs précédents de la même version.** Pour en savoir plus sur la nécessité de l’application d’un correctif logiciel, consultez l’article de la base de connaissances correspondant. Consultez les liens des notes de version dans la section précédente.

Pour plus d’informations sur les correctifs logiciels actuellement disponibles, consultez les notes de publication de cette mise à jour :

- [Correctif logiciel Azure Stack Hub 2005](/azure-stack/operator/release-notes?view=azs-2005#hotfixes)
- [Correctif logiciel Azure Stack Hub 2002](/azure-stack/operator/release-notes?view=azs-2002#hotfixes-1)

## <a name="keep-your-system-under-support"></a>Conserver votre système dans un état de prise en charge

Pour que votre instance Azure Stack Hub continue d’être prise en charge, vous devez exécuter la version de mise à jour la plus récente ou exécuter les deux versions de mise à jour précédentes.

Les correctifs logiciels ne sont pas considérés comme des versions de mise à jour majeures. Si votre instance d’Azure Stack Hub a plus de deux mises à jour de retard, elle est considérée comme non conforme. Pour bénéficier de la prise en charge, vous devez mettre à jour avec au moins la version minimale prise en charge.

Par exemple, si la dernière version mise à jour disponible est 1904 et que les deux mises à jour précédentes étaient les versions 1903 et 1902, 1902 et 1903 restent prises en charge. Toutefois, 1901 n’est pas pris en charge. La stratégie reste appliquée quand aucune version n’est publiée pendant un ou deux mois. Par exemple, si la version actuelle est 1807 et qu’il n’existait aucune version 1806, les deux mises à jour précédentes des versions 1805 et 1804 restent prises en charge.

Les packages de mise à jour logicielle Microsoft ne sont pas cumulatifs et requièrent le package de mise à jour ou le correctif logiciel précédent. Si vous décidez de reporter une ou plusieurs mises à jour, pensez-y si vous souhaitez obtenir la dernière version.

### <a name="resource-provider-version-support"></a>Prise en charge des versions du fournisseur de ressources

Pour les fournisseurs de ressources Azure Stack Hub, il est important de noter que seule la version la plus récente d’un fournisseur de ressources spécifique compatible avec votre version d'Azure Stack Hub prise en charge est prise en charge, même si vous utilisez une version antérieure d'Azure Stack Hub toujours présente dans la fenêtre de support.

Pour plus d’informations sur la compatibilité du fournisseur de ressources, consultez les notes de publication de ce fournisseur de ressources spécifique.

## <a name="get-support"></a>Obtenir de l’aide

Azure Stack Hub suit le même processus de support qu’Azure. Les clients d’entreprise peuvent suivre le processus décrit dans [Création d’une demande de support Azure](/azure/azure-supportability/how-to-create-azure-support-request). Si vous êtes client d’un fournisseur de solutions cloud (CSP), contactez-le pour obtenir de l’aide. Pour plus d’informations, consultez le [Forum Aux Questions sur le support technique Azure](https://azure.microsoft.com/support/faq/).

Pour obtenir de l’aide dans la résolution des problèmes de mise à jour, consultez [Bonnes pratiques pour résoudre les problèmes liés aux correctifs logiciels et aux mises à jour Azure Stack Hub](azure-stack-troubleshooting.md).

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les mises à jour dans Azure Stack Hub](azure-stack-updates.md)
- [Bonnes pratiques pour résoudre les problèmes liés aux correctifs logiciels et aux mises à jour Azure Stack Hub](azure-stack-troubleshooting.md)