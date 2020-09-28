---
title: Notes de publication d’Azure Stack HCI
description: Notes de publication pour Azure Stack HCI, version 20H2.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/17/2020
ms.openlocfilehash: 57aa49c075afad4f6ccb37802293424d8e0e309f
ms.sourcegitcommit: c45f92186edf62b95b38d1020e0851dd54bc6536
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2020
ms.locfileid: "90765388"
---
# <a name="release-notes-for-azure-stack-hci-public-preview"></a>Notes de publication pour la préversion publique Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2

Cet article décrit le contenu des packages de mise à jour de la préversion publique Azure Stack HCI.

## <a name="september-17-2020-preview-update-kb4577629"></a>Mise à jour de la version préliminaire (KB4577629) du 17 septembre 2020

Cette mise à jour inclut les améliorations et les correctifs logiciels de la dernière version d’Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Améliorations et correctifs
Cette mise à jour non sécurisé comprend des améliorations en matière de qualité. Les changements clé sont notamment :
- A résolu un problème où le trafic du logiciel Software Load Balancer (SLB) passant par le multiplexeur peut être redirigé vers un autre hôte qui peut provoquer un échec de connexion de l’application.

### <a name="known-issues-in-this-update"></a>Problèmes connus dans cette mise à jour
Microsoft ne connaît actuellement aucun problème avec cette mise à jour.

### <a name="how-to-get-this-update"></a>Comment obtenir cette mise à jour
La mise à jour de sécurité du 17 septembre 2020 (KB4577629) pour la [préversion d’Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) est fournie via Windows Update. Pour l’installer sur votre cluster Azure Stack HCI, consultez [Mettre à jour Azure Stack clusters HCI](manage/update-cluster.md).

### <a name="file-information"></a>informations relatives aux fichiers
Pour obtenir la liste des fichiers fournis dans cette mise à jour (version 17784.1259 du système d’exploitation), téléchargez les [informations de fichier pour la mise à jour cumulative 4577629](https://download.microsoft.com/download/9/1/a/91addcbb-2b36-408c-ab88-736de42edb98/4577629.csv)

   > [!NOTE]
   > Certains fichiers affichent par erreur « Non applicable » dans la colonne « Version de fichier » du fichier CSV. Cela peut entraîner des faux positifs ou des faux négatifs lors de l’utilisation d’outils de détection d’analyse tiers pour valider la build.

## <a name="september-8-2020-security-update-kb4577470"></a>Mise à jour de sécurité du 8 septembre 2020 (KB4577470)

Cette mise à jour inclut les améliorations et les correctifs logiciels de la dernière version d’Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Améliorations et correctifs
Cette mise à jour contient diverses améliorations de sécurité pour la fonctionnalité interne du système d’exploitation. Aucun autre problème n’a été documenté pour cette version.

Pour plus d’informations sur les vulnérabilités de sécurité résolues, consultez le [Guide de mise à jour de sécurité](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Problèmes connus dans cette mise à jour
Microsoft ne connaît actuellement aucun problème avec cette mise à jour.

### <a name="how-to-get-this-update"></a>Comment obtenir cette mise à jour
La mise à jour de sécurité du 8 septembre 2020 (KB4577470) pour la [préversion d’Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) est fournie via Windows Update. Pour l’installer sur votre cluster Azure Stack HCI, consultez [Mettre à jour Azure Stack clusters HCI](manage/update-cluster.md).

### <a name="file-information"></a>informations relatives aux fichiers
Pour obtenir la liste des fichiers fournis dans cette mise à jour (version 17784.1226 du système d’exploitation), téléchargez les [informations de fichier pour la mise à jour cumulative 4577470](https://download.microsoft.com/download/3/c/4/3c468525-5867-4cc3-8d34-dba88989adab/4577470.csv).

   > [!NOTE]
   > Certains fichiers affichent par erreur « Non applicable » dans la colonne « Version de fichier » du fichier CSV. Cela peut entraîner des faux positifs ou des faux négatifs lors de l’utilisation d’outils de détection d’analyse tiers pour valider la build.

## <a name="september-8-2020-servicing-stack-update-kb4577558"></a>Mise à jour de la pile de maintenance du 8 septembre 2020 (KB4577558)

Cette mise à jour inclut les améliorations de qualité apportées à la dernière version d’Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Améliorations et correctifs
Cette mise à jour apporte des améliorations en matière de qualité à la pile de maintenance, qui est le composant qui installe les mises à jour. Les mises à jour de la pile de maintenance vous permettent de disposer d’une pile de maintenance robuste et fiable afin que vos appareils puissent recevoir et installer les mises à jour Microsoft.

### <a name="how-to-get-this-update"></a>Comment obtenir cette mise à jour
La mise à jour de la pile de maintenance du 8 septembre 2020 (KB4577558) pour la [préversion d’Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) est fournie via Windows Update. Pour l’installer sur votre cluster Azure Stack HCI, consultez [Mettre à jour Azure Stack clusters HCI](manage/update-cluster.md).

### <a name="restart-information"></a>Informations sur le redémarrage 
Vous n’avez pas besoin de redémarrer l’ordinateur après l’application de cette mise à jour.

### <a name="removal-information"></a>Informations sur la suppression
Les mises à jour de la pile de maintenance apportent des modifications à la façon dont les mises à jour sont installées et ne peuvent pas être désinstallées de l’appareil.

### <a name="file-information"></a>informations relatives aux fichiers
Pour obtenir la liste des fichiers fournis dans cette mise à jour (version 17784,1220 du système d’exploitation), téléchargez les [informations de fichier pour la mise à jour cumulative 4577558](https://download.microsoft.com/download/8/f/6/8f612a9b-cb4e-4832-9397-156760848592/4577558.csv).

### <a name="references"></a>References

Pour plus d’informations sur les mises à jour de la pile de maintenance, consultez les articles suivants :

- [Mises à jour de la pile de maintenance](https://docs.microsoft.com/windows/deployment/update/servicing-stack-updates)
- [Mises à jour de la pile de maintenance : Forum Aux Questions](https://support.microsoft.com/help/4535697)

Informez-vous sur la [terminologie](https://support.microsoft.com/help/824684) que Microsoft utilise pour décrire les mises à jour logicielles.

## <a name="august-11-2020-security-update-kb4574585"></a>Mise à jour de sécurité 11 août 2020 (KB4574585)

Cette mise à jour inclut les améliorations et les correctifs logiciels de la dernière version d’Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Améliorations et correctifs
Cette mise à jour contient diverses améliorations de sécurité pour la fonctionnalité interne du système d’exploitation. Aucun autre problème n’a été documenté pour cette version.

Pour plus d’informations sur les vulnérabilités de sécurité résolues, consultez le [Guide de mise à jour de sécurité](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Problèmes connus dans cette mise à jour
Microsoft ne connaît actuellement aucun problème avec cette mise à jour.

### <a name="how-to-get-this-update"></a>Comment obtenir cette mise à jour
La mise à jour de sécurité du 11 août 2020 (KB4574585) pour la [préversion Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) est fournie via Windows Update. Pour l’installer sur votre cluster Azure Stack HCI, consultez [Mettre à jour Azure Stack clusters HCI](manage/update-cluster.md).

### <a name="file-information"></a>informations relatives aux fichiers
Pour obtenir la liste des fichiers fournis dans cette mise à jour (version 17784,1162 du système d’exploitation), téléchargez les [informations de fichier pour la mise à jour cumulative 4574585](https://download.microsoft.com/download/7/f/4/7f451def-76c5-4cc0-9929-0c5efeb27d2f/4574585.csv).

   > [!NOTE]
   > Certains fichiers affichent par erreur « Non applicable » dans la colonne « Version de fichier » du fichier CSV. Cela peut entraîner des faux positifs ou des faux négatifs lors de l’utilisation d’outils de détection d’analyse tiers pour valider la build.
