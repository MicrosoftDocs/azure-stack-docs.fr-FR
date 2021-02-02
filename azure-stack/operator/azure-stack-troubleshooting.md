---
title: Résoudre les problèmes d'Azure Stack Hub
titleSuffix: Azure Stack
description: Découvrez comment résoudre les problèmes Azure Stack Hub, y compris les problèmes liés aux machines virtuelles, stockage et App Service.
author: PatAltimore
ms.topic: article
ms.date: 01/20/2021
ms.author: patricka
ms.reviewer: prchint
ms.lastreviewed: 12/10/2020
ms.openlocfilehash: 1706f028aff293f85ea5a0c1fb882a5d332d7196
ms.sourcegitcommit: dd34ae1c6207aafb5218c31658123e913f51bf7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98672873"
---
# <a name="troubleshoot-issues-in-azure-stack-hub"></a>Résoudre les problèmes dans Azure Stack Hub

Ce document fournit des informations de résolution des problèmes pour les environnements intégrés Azure Stack Hub. Pour obtenir de l’aide sur le kit de développement Azure Stack Hub, consultez [Résolution des problèmes d’ASDK](../asdk/asdk-troubleshooting.md) ou contactez les experts du [forum MSDN Azure Stack Hub](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

## <a name="frequently-asked-questions"></a>Forum aux questions

Ces sections contiennent des liens vers des documents qui abordent les questions les plus fréquemment posées au support Microsoft.

### <a name="purchase-considerations"></a>Considérations relatives à l’achat

* [Comment acheter](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Vue d’ensemble d’Azure Stack Hub](azure-stack-overview.md)

### <a name="updates-and-diagnostics"></a>Mises à jour et diagnostics

* [Utiliser les outils de diagnostic dans Azure Stack Hub](./diagnostic-log-collection.md)
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

* [Tutoriel : Créer un rôle personnalisé pour les ressources Azure à l'aide d'Azure PowerShell](/azure/role-based-access-control/tutorial-custom-role-powershell)

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

### <a name="reset-linux-vm-password"></a>Réinitialiser le mot de passe Linux VM

Si vous oubliez le mot de passe d’une machine virtuelle Linux et que l’option **Réinitialiser le mot de passe** ne fonctionne pas en raison de problèmes liés à l’extension VMAccess, vous pouvez effectuer une réinitialisation en procédant comme suit :

1. Choisissez une machine virtuelle Linux à utiliser en tant que machine virtuelle de récupération.

1. Connectez-vous au portail utilisateur :
   1. Prenez note de la taille de la machine virtuelle, de la carte réseau, de l’adresse IP publique, du groupe de sécurité réseau et des disques de données.
   1. Arrêtez la machine virtuelle concernée.
   1. Supprimez la machine virtuelle concernée.
   1. Attachez le disque de la machine virtuelle concernée en tant que disque de données sur la machine virtuelle de récupération (la disponibilité du disque peut prendre quelques minutes).

1. Connectez-vous à la machine virtuelle de récupération et exécutez la commande suivante :

   ```
   sudo su –
   mkdir /tempmount
   fdisk -l
   mount /dev/sdc2 /tempmount /*adjust /dev/sdc2 as necessary*/
   chroot /tempmount/
   passwd root /*substitute root with the user whose password you want to reset*/
   rm -f /.autorelabel /*Remove the .autorelabel file to prevent a time consuming SELinux relabel of the disk*/
   exit /*to exit the chroot environment*/
   umount /tempmount
   ```

1. Connectez-vous au portail utilisateur :

   1. Détachez le disque de la machine virtuelle de récupération.
   1. Recréez la machine virtuelle à partir du disque.
   1. Veillez à transférer l’adresse IP publique de la machine virtuelle précédente, à attacher les disques de données, etc.


Vous pouvez également prendre un instantané du disque d’origine et créer un disque à partir de celui-ci plutôt que d’effectuer les modifications directement sur le disque d’origine. Pour plus d’informations, consultez les rubriques suivantes :

- [Réinitialiser le mot de passe](/azure/virtual-machines/troubleshooting/reset-password)
- [Créer un disque à partir de l’instantané](/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-portal-linux#create-a-disk-from-the-snapshot)
- [Modification et réinitialisation du mot de passe racine](https://access.redhat.com/documentation/red_hat_enterprise_linux/7/html/system_administrators_guide/sec-terminal_menu_editing_during_boot#sec-Changing_and_Resetting_the_Root_Password)


### <a name="license-activation-fails-for-windows-server-2012-r2-during-provisioning"></a>Échec de l’activation de la licence pour Windows Server 2012 R2 durant le provisionnement

Dans ce cas, l’activation de Windows est un échec et un filigrane s’affiche dans le coin inférieur droit de l’écran. Le journal WaSetup.xml situé sous C:\Windows\Panther contient l’événement suivant :

```xml
<Event time="2019-05-16T21:32:58.660Z" category="ERROR" source="Unattend">
    <UnhandledError>
        <Message>InstrumentProcedure: Failed to execute 'Call ConfigureLicensing()'. Will raise error to caller</Message>
        <Number>-2147221500</Number>
        <Description>Could not find the VOLUME_KMSCLIENT product</Description>
        <Source>Licensing.wsf</Source>
    </UnhandledError>
</Event>
```


Pour activer la licence, copiez la clé AVMA (activation automatique de machine virtuelle) correspondant à la référence SKU à activer.

|Édition|Clé AVMA|
|-|-|
|Centre de données|Y4TGP-NPTV9-HTC2H-7MGQ3-DV4TW|
|Standard|DBGBW-NPF86-BJVTX-K3WKJ-MTB6V|
|Essentials|K2XGM-NMBT3-2R6Q8-WF2FK-P36R2|

Sur la machine virtuelle, exécutez la commande suivante :

```powershell
slmgr /ipk <AVMA_key>
```

Pour obtenir des détails complets, consultez [Activation automatique de machine virtuelle](/windows-server/get-started-19/vm-activation-19).

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

Si vous utilisez un système intégré dans un scénario déconnecté, il est recommandé d’utiliser une autorité de certification d’entreprise. Exportez le certificat racine au format base 64, puis importez-le dans l’Explorateur Stockage Azure. Veillez à supprimer la barre oblique finale (`/`) du point de terminaison Resource Manager. Pour plus d’informations, consultez [Se préparer à la connexion à Azure Stack Hub](../user/azure-stack-storage-connect-se.md).

## <a name="troubleshoot-app-service"></a>Résoudre les problèmes liés à App Service

### <a name="create-aadidentityappps1-script-fails"></a>Le script Create-AADIdentityApp.ps1 échoue

Si le script Create-AADIdentityApp.ps1 nécessaire pour App Service échoue, veillez à inclure le paramètre obligatoire `-AzureStackAdminCredential` lors de l’exécution du script. Pour plus d’informations, consultez [Conditions préalables au déploiement d’App Service sur Azure Stack Hub](azure-stack-app-service-before-you-get-started.md#create-an-azure-ad-app).

## <a name="troubleshoot-azure-stack-hub-updates"></a>Résoudre les problèmes liés à Azure Stack Hub

Le processus relatif aux mises à jour et aux correctifs logiciels Azure Stack Hub est conçu pour permettre aux opérateurs d’appliquer les mises à jour de manière cohérente et rationalisée. Bien que cela soit rare, certains problèmes peuvent se produire pendant l’application des correctifs logiciels et des mises à jour. Les étapes suivantes sont recommandées si vous rencontrez un problème pendant le processus d’application des correctifs logiciels et des mises à jour :

0. **Prérequis** : Veillez à suivre la [check-list des activités de mise à jour](release-notes-checklist.md) et à [activer la collecte proactive des journaux](./diagnostic-log-collection.md#send-logs-proactively).

1. Suivez les étapes de correction indiquées dans l’alerte d’échec créée au moment de l’échec de votre mise à jour.

2. Si vous n’êtes pas parvenu à résoudre votre problème, créez un [ticket de support Azure Stack Hub](./azure-stack-help-and-support-overview.md). Veillez à disposer des [journaux collectés](./diagnostic-log-collection.md#send-logs-now) pour la période pendant laquelle le problème s’est produit. En cas d’échec d’une mise à jour, avec une alerte critique ou un avertissement, il est important de passer l’échec en revue et de contacter les services de support technique Microsoft comme indiqué dans l’alerte afin que votre unité d'échelle ne reste pas à l’état d’échec pendant trop de temps. Le fait de laisser une unité d'échelle dans un état d’échec de mise à jour pendant une période prolongée peut entraîner des problèmes plus difficiles à résoudre ultérieurement.

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>Problèmes usuels liés aux correctifs logiciels et mises à jour Azure Stack Hub

*S’applique à : Systèmes intégrés Azure Stack Hub*

### <a name="preparationfailed"></a>PreparationFailed

**Champ d’application** : Ce problème s’applique à toutes les versions prises en charge.

**Cause** : L’installation de la mise à jour d’Azure Stack Hub peut échouer, et son état peut passer à `PreparationFailed`. Pour les systèmes connectés à Internet, cela indique généralement que le package de mise à jour ne peut pas être téléchargé correctement en raison d’une connexion Internet faible. 

**Correction** : Vous pouvez contourner ce problème en cliquant sur **Installer maintenant**. Si le problème persiste, nous vous conseillons de charger manuellement le package de mise à jour comme cela est décrit dans la [section Installer des mises à jour](azure-stack-apply-updates.md?#install-updates-and-monitor-progress).

**Occurrence** : Courant

### <a name="warnings-and-errors-reported-while-update-is-in-progress"></a>Avertissements et erreurs signalés pendant la mise à jour

**Champ d’application** : Ce problème s’applique à toutes les versions prises en charge.

**Cause** : Lorsqu’une mise à jour Azure Stack Hub est à l’état **En cours**, des avertissements et des erreurs peuvent être signalés dans le portail. Les délais d’attente de certains composants peuvent être dépassés lors de l’attente d’autres composants pendant la mise à niveau, provoquant une erreur. Azure Stack Hub dispose d’un mécanisme de nouvelle tentative ou de correction de certaines des tâches en cas d’erreurs intermittentes.

**Correction** : Lorsqu’une mise à jour Azure Stack Hub est à l’état **En cours**, les avertissements et erreurs signalés dans le portail peuvent être ignorés.

**Occurrence** : Courant

::: moniker range="azs-2002"
### <a name="2002-update-failed"></a>Échec de la mise à jour 2002

**Champ d’application** : Ce problème s’applique uniquement à la version 2002.

**Cause** : Lors de la tentative de mise à jour 2002, cette dernière peut échouer et afficher le message suivant : `The private network parameter is missing from cloud parameters. Please use set-azsprivatenetwork cmdlet to set private networkTrace`.

**Correction** : [Configurez un réseau interne privé](./azure-stack-network.md?view=azs-2002&preserve-view=true#private-network).
::: moniker-end