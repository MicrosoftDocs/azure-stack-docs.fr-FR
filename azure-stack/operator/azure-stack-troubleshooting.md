---
title: Résoudre les problèmes d'Azure Stack Hub
titleSuffix: Azure Stack
description: Découvrez comment résoudre les problèmes Azure Stack Hub, y compris les problèmes liés aux machines virtuelles, stockage et App Service.
author: justinha
ms.topic: article
ms.date: 05/13/2020
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 15/13/2020
ms.openlocfilehash: de19e65866413ec4e498c9a21848c1f43af6d65a
ms.sourcegitcommit: 5f4f0ee043ff994efaad44129ce49be43c64d5dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2020
ms.locfileid: "84819518"
---
# <a name="troubleshoot-issues-in-azure-stack-hub"></a>Résoudre les problèmes dans Azure Stack Hub

Ce document fournit des informations de résolution des problèmes pour les environnements intégrés Azure Stack Hub. Pour obtenir de l’aide sur le kit de développement Azure Stack Hub, consultez [Résolution des problèmes d’ASDK](../asdk/asdk-troubleshooting.md) ou contactez les experts du [forum MSDN Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

## <a name="frequently-asked-questions"></a>Forum aux questions

Ces sections contiennent des liens vers des documents qui abordent les questions les plus fréquemment posées au support Microsoft.

### <a name="purchase-considerations"></a>Considérations relatives à l’achat

* [Comment acheter](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Vue d’ensemble d’Azure Stack Hub](azure-stack-overview.md)

### <a name="updates-and-diagnostics"></a>Mises à jour et diagnostics

* [Utiliser les outils de diagnostic dans Azure Stack Hub](azure-stack-diagnostics.md)
* [Valider l’état du système Azure Stack Hub](azure-stack-diagnostic-test.md)
* [Cadence de publication des packages de mises à jour](azure-stack-servicing-policy.md#update-package-release-cadence)
* [Vérifier et résoudre les problèmes liés à l'état du nœud](azure-stack-node-actions.md)

### <a name="supported-operating-systems-and-sizes-for-guest-vms"></a>Systèmes d’exploitation et tailles pris en charge pour les machines virtuelles invitées

* [Systèmes d’exploitation invités pris en charge sur Azure Stack Hub](azure-stack-supported-os.md)
* [Tailles de machine virtuelle prises en charge dans Azure Stack Hub](../user/azure-stack-vm-sizes.md)

### <a name="azure-marketplace"></a>Place de marché Azure

* [Éléments disponibles sur la Place de Marché Azure pour Azure Stack Hub](azure-stack-marketplace-azure-items.md)

### <a name="manage-capacity"></a>Gérer la capacité

#### <a name="memory"></a>Mémoire

Pour augmenter la capacité de mémoire totale disponible pour Azure Stack Hub, vous pouvez ajouter plus de mémoire. Dans Azure Stack Hub, votre serveur physique est également appelé nœud d'unité d'échelle. Tous les nœuds d'unité d'échelle qui sont membres d'une même unité d'échelle doivent avoir [la même quantité de mémoire](azure-stack-manage-storage-physical-memory-capacity.md).

#### <a name="retention-period"></a>Période de rétention

Le paramètre de période de rétention permet à un opérateur cloud de spécifier une période de temps en jours (entre 0 et 9999 jours) pendant laquelle un compte supprimé peut être récupéré. La période de conservation par défaut est définie sur **0** jour. Pour ce paramètre, la valeur **0** signifie qu’un compte supprimé n’est plus conservé et qu’il est marqué comme devant faire l’objet d’un nettoyage périodique de la mémoire.

* [Définir la période de rétention](azure-stack-manage-storage-accounts.md#set-the-retention-period)

### <a name="security-compliance-and-identity"></a>Sécurité, conformité et identité  

#### <a name="manage-rbac"></a>Gérer RBAC

Un utilisateur Azure Stack Hub peut être un lecteur, un propriétaire ou un collaborateur pour chaque instance d’un abonnement, d’un groupe de ressources ou d’un service.

* [Azure Stack Hub - Gérer RBAC](azure-stack-manage-permissions.md)

Si les rôles intégrés des ressources Azure ne répondent pas aux besoins spécifiques de votre organisation, vous pouvez créer vos propres rôles personnalisés. Pour ce tutoriel, vous allez créer un rôle personnalisé nommé Reader Support Tickets à l’aide d’Azure PowerShell.

* [Tutoriel : Créer un rôle personnalisé pour les ressources Azure à l'aide d'Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>Gérer l’utilisation et la facturation en tant que fournisseur de services Cloud

* [Gérer l’utilisation et la facturation en tant que fournisseur de services cloud](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)
* [Créer un abonnement CSP ou APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

Choisissez le type de compte de services partagés que vous utilisez pour Azure Stack Hub. Les types d’abonnements pouvant être utilisés pour l’inscription d’un système Azure Stack Hub mutualisé sont les suivants :

* Fournisseur de solutions cloud
* Abonnement Partner Shared Services

### <a name="get-scale-unit-metrics"></a>Récupérer les métriques d’unité d’échelle

Vous pouvez utiliser PowerShell pour obtenir des informations sur l’utilisation des tampons sans l’aide du support Microsoft. Pour obtenir l’utilisation des tampons :

1. Créez une session PEP.
2. Exécutez `test-azurestack`.
3. Quittez la session PEP.
4. Exécutez `get-azurestacklog -filterbyrole seedring` à l’aide d’un appel invoke-command.
5. Extrayez le fichier seedring.zip. Vous pouvez obtenir le rapport de validation à partir du dossier ERCS dans lequel vous avez exécuté `test-azurestack`.

Pour plus d'informations, consultez [Diagnostics Azure Stack Hub](azure-stack-get-azurestacklog.md).

## <a name="troubleshoot-virtual-machines-vms"></a>Résoudre les problèmes liés aux machines virtuelles

### <a name="default-image-and-gallery-item"></a>Élément de la galerie et image par défaut

Vous devez ajouter un élément de la galerie et une image Windows Server avant de pouvoir déployer des machines virtuelles dans Azure Stack Hub.

### <a name="ive-deleted-some-vms-but-still-see-the-vhd-files-on-disk"></a>J’ai supprimé des machines virtuelles, mais je vois toujours les fichiers de VHD sur le disque.

Ce comportement est normal :

* La suppression d’une machine virtuelle n’entraîne pas celle des VHD. Les disques sont des ressources distinctes dans le groupe de ressources.
* Lorsqu’un compte de stockage est supprimé, la suppression est visible immédiatement via Azure Resource Manager. Cela étant, les disques qu'il contient sont conservés dans le stockage jusqu’au nettoyage de la mémoire.

Si vous voyez des VHD « orphelins », il est important de savoir s’ils font partie du dossier d’un compte de stockage supprimé. Si le compte de stockage n’a pas été supprimé, il est normal qu'ils y soient toujours présents.

Pour en savoir plus sur la configuration du seuil de rétention et de la récupération à la demande, consultez la page [Gérer les comptes de stockage](azure-stack-manage-storage-accounts.md).

## <a name="troubleshoot-storage"></a>Résoudre les problèmes de stockage

### <a name="storage-reclamation"></a>Récupération du stockage

Il peut s’écouler jusqu’à 14 heures avant que la capacité récupérée ne s’affiche dans le portail. La récupération d’espace dépend de différents facteurs, notamment le pourcentage d’utilisation des fichiers conteneurs internes dans le magasin d’objets blob de blocs. Par conséquent, selon la quantité de données supprimées, il n’y a pas de garantie quant à la quantité d’espace récupérable lors de l’exécution du récupérateur de mémoire.

### <a name="azure-storage-explorer-not-working-with-azure-stack-hub"></a>L’Explorateur Stockage Azure ne fonctionne pas avec Azure Stack Hub

Si vous utilisez un système intégré dans un scénario déconnecté, il est recommandé d’utiliser une autorité de certification d’entreprise. Exportez le certificat racine au format base 64, puis importez-le dans l’Explorateur Stockage Azure. Veillez à supprimer la barre oblique finale (`/`) du point de terminaison Resource Manager. Pour plus d’informations, consultez [Se préparer à la connexion à Azure Stack Hub](/azure-stack/user/azure-stack-storage-connect-se).

## <a name="troubleshoot-app-service"></a>Résoudre les problèmes liés à App Service

### <a name="create-aadidentityappps1-script-fails"></a>Le script Create-AADIdentityApp.ps1 échoue

Si le script Create-AADIdentityApp.ps1 nécessaire pour App Service échoue, veillez à inclure le paramètre obligatoire `-AzureStackAdminCredential` lors de l’exécution du script. Pour plus d’informations, consultez [Conditions préalables au déploiement d’App Service sur Azure Stack Hub](azure-stack-app-service-before-you-get-started.md#create-an-azure-ad-app).

## <a name="troubleshoot-azure-stack-hub-updates"></a>Résoudre les problèmes liés à Azure Stack Hub

Le processus relatif aux mises à jour et aux correctifs logiciels Azure Stack Hub est conçu pour permettre aux opérateurs d’appliquer les mises à jour de manière cohérente et rationalisée. Bien que cela soit rare, certains problèmes peuvent se produire pendant l’application des correctifs logiciels et des mises à jour. Les étapes suivantes sont recommandées si vous rencontrez un problème pendant le processus d’application des correctifs logiciels et des mises à jour :

0. **Prérequis** : Veillez à suivre la [check-list des activités de mise à jour](release-notes-checklist.md) et à [activer la collecte proactive des journaux](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md).

1. Suivez les étapes de correction indiquées dans l’alerte d’échec créée au moment de l’échec de votre mise à jour.

2. Si vous n’êtes pas parvenu à résoudre votre problème, créez un [ticket de support Azure Stack Hub](azure-stack-help-and-support-overview-tzl.md). Veillez à disposer des [journaux collectés](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) pour la période pendant laquelle le problème s’est produit.

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>Problèmes usuels liés aux correctifs logiciels et mises à jour Azure Stack Hub

*S’applique à : Systèmes intégrés Azure Stack Hub*

### <a name="preparationfailed"></a>PreparationFailed

**Champ d’application** : Ce problème s’applique à toutes les versions prises en charge.

**Cause** : L’installation de la mise à jour d’Azure Stack Hub peut échouer, et son état peut passer à `PreparationFailed`. Pour les systèmes connectés à Internet, cela indique généralement que le package de mise à jour ne peut pas être téléchargé correctement en raison d’une connexion Internet faible. 

**Correction** : Vous pouvez contourner ce problème en cliquant sur **Installer maintenant**. Si le problème persiste, nous vous conseillons de charger manuellement le package de mise à jour comme cela est décrit dans la [section Installer des mises à jour](azure-stack-apply-updates.md?#install-updates-and-monitor-progress).

**Occurrence** : Courant
