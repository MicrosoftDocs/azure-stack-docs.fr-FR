---
title: Notes de publication pour Azure Stack 1908 | Microsoft Docs
description: En savoir plus sur les mises à jour 1908 pour les systèmes intégrés Azure Stack
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
ms.date: 10/29/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6716b31431106e0af43785177376e4980b864f81
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248639"
---
# <a name="azure-stack-updates-1908-release-notes"></a>Mises à jour d’Azure Stack: Notes de publication 1908

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit le contenu des packages de mise à jour d’Azure Stack. La mise à jour inclut des améliorations, nouveautés et correctifs pour cette version d’Azure Stack.

Pour accéder aux notes de publication d'une autre version, utilisez le menu déroulant de sélection de la version, situé au-dessus de la table des matières à gauche.

> [!IMPORTANT]  
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

> [!IMPORTANT]  
> Si votre instance Azure Stack est en retard de plus de deux mises à jour, elle est considérée comme non conforme. Pour bénéficier de la prise en charge, vous devez [mettre à jour avec au moins la version minimale prise en charge](../azure-stack-servicing-policy.md#keep-your-system-under-support).

## <a name="update-planning"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Problèmes connus](known-issues-1908.md)
- [Mises à jour de sécurité](../release-notes-security-updates.md)
- [Liste de vérification des activités avant et après l’application de la mise à jour](../release-notes-checklist.md)

Pour obtenir de l'aide sur le dépannage des mises à jour et le processus de mise à jour, voir [Résolution des problèmes liés aux correctifs et aux mises à jour pour Azure Stack](../azure-stack-updates-troubleshoot.md).

## <a name="1908-build-reference"></a>Référence de build 1908

Le numéro de build de la mise à jour 1908 d’Azure Stack est **1.1908.4.33**.

### <a name="update-type"></a>Type de mise à jour

Pour la version 1908, le système d’exploitation sous-jacent sur lequel Azure Stack s’exécute a été mis à jour vers Windows Server 2019. Cela apporte des améliorations fondamentales, ainsi que la possibilité d’ajouter des fonctionnalités supplémentaires à Azure Stack dans un avenir proche.

Le type de build de la mise à jour 1908 d’Azure Stack est **Complète**. Par conséquent, la mise à jour 1908 prend plus de temps que les mises à jour Express telles que 1906 et 1907. Les runtimes exacts des mises à jour complètes dépendent généralement du nombre de nœuds que votre instance Azure Stack contient, de la capacité utilisée sur votre système par les charges de travail clientes, de la connectivité réseau de votre système (s’il est connecté à Internet) et de la configuration de votre matériel système. La mise à jour 1908 a présenté les durées d’exécution attendues suivantes dans nos tests internes : 4 nœuds - 42 heures, 8 nœuds - 50 heures, 12 nœuds - 60 heures, 16 nœuds - 70 heures. Il n’est pas rare de constater des durées d’exécution des mises à jour plus longues que ces durées prévues, mais cela ne nécessite aucune action de la part des opérateurs Azure Stack, sauf si la mise à jour échoue.

Pour plus d’informations sur les types de build de mise à jour, voir [Gérer les mises à jour dans Azure Stack](../azure-stack-updates.md).

- La durée d’exécution exacte des mises à jour dépend généralement de la capacité utilisée sur votre système par les charges de travail client, de la connectivité réseau de votre système (s’il est connecté à Internet) et de la configuration de votre matériel système.
- Les durées d’exécution plus longues que prévu ne sont pas rares et ne nécessitent aucune action de la part des opérateurs Azure Stack, sauf si la mise à jour échoue.
- Cette approximation d’exécution est propre à la mise à jour 1908 et n’est pas comparable aux autres mises à jour d’Azure Stack.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Nouveautés

<!-- What's new, also net new experiences and features. -->

- Pour la version 1908, le système d’exploitation sous-jacent sur lequel Azure Stack s’exécute a été mis à jour vers Windows Server 2019. Cela apporte des améliorations fondamentales, ainsi que la possibilité d’ajouter des fonctionnalités supplémentaires à Azure Stack dans un avenir proche.
- Tous les composants de l’infrastructure Azure Stack fonctionnent désormais en mode FIPS 140-2.
- Les opérateurs Azure Stack peuvent désormais supprimer les données utilisateur du portail. Pour plus d’informations, consultez [Effacer les données utilisateur du portail dans Azure Stack](../azure-stack-portal-clear.md).

### <a name="improvements"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->
- Le chiffrement des données au repos dans Azure Stack a été amélioré afin de rendre les secrets persistants dans le module matériel TPM des nœuds physiques.

### <a name="changes"></a>Modifications

- Les fournisseurs de matériel publieront le package d’extension OEM version 2.1 ou ultérieure en même temps qu’Azure Stack version 1908. Le package d’extension OEM version 2.1 ou ultérieure est requis pour la version 1908 d’Azure Stack. Pour plus d’informations sur le téléchargement du package d’extension OEM version 2.1 ou ultérieure, contactez le fournisseur de matériel pour votre système, et consultez l’article sur les [mises à jour OEM](../azure-stack-update-oem.md#oem-contact-information).  

### <a name="fixes"></a>Correctifs

- Résolution d’un problème de compatibilité avec les futures mises à jour OEM d’Azure Stack et d’un problème de déploiement de machine virtuelle à l’aide d’images utilisateur client. Ce problème a été détecté dans la version 1907 et résolu avec le correctif logiciel [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44)  
- Résolution d’un problème avec la mise à jour des microprogrammes OEM et correction du diagnostic incorrect dans Test-AzureStack pour Fabric Ring Health. Ce problème a été détecté dans la version 1907 et résolu avec le correctif logiciel [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35)
- Correction d’un problème avec le processus de mise à jour des microprogrammes OEM. Ce problème a été détecté dans la version 1907 et résolu avec le correctif logiciel [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37)

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates"></a>Mises à jour de sécurité

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack, consultez [Mises à jour de sécurité Azure Stack](../release-notes-security-updates.md).

## <a name="download-the-update"></a><a name="download-the-update-1908"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1908 d’Azure Stack sur la [page de téléchargement d’Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Correctifs

Azure Stack publie des correctifs logiciels à intervalles réguliers. Avant d’installer la mise à jour 1908 d’Azure Stack, veillez à installer le dernier correctif logiciel d’Azure Stack pour la build 1907.

Les correctifs logiciels Azure Stack sont uniquement applicables aux systèmes intégrés Azure Stack. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

### <a name="prerequisites-before-applying-the-1908-update"></a>Configuration requise : Avant d’appliquer la mise à jour 1908

La version 1908 d’Azure Stack doit être appliquée sur la version 1907 avec les correctifs logiciels suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack 1.1907.17.54](https://support.microsoft.com/help/4523826)

La mise à jour 1908 d’Azure Stack nécessite la **version 2.1 ou ultérieure d’OEM pour Azure Stack** du fournisseur de matériel de votre système. Les mises à jour OEM appliquent les mises à jour des pilotes et des microprogrammes aux composants matériels de votre système Azure Stack. Pour plus d’informations sur l’application des mises à jour OEM, consultez [Appliquer des mises à jour de fabricants d’ordinateurs à Azure Stack](../azure-stack-update-oem.md)

### <a name="after-successfully-applying-the-1908-update"></a>Après l’application de la mise à jour 1908

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](../azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif logiciel Azure Stack 1.1908.6.37](https://support.microsoft.com/help/4527372)
