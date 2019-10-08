---
title: Résolution des problèmes de Microsoft Azure Stack | Microsoft Docs
description: Résolution des problèmes d’Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 0fb46cd1b92c1b811ba1c72a91188201a7d2af96
ms.sourcegitcommit: 79ead51be63c372b23b7fca6ffeaf95fd44de786
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687967"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Résolution des problèmes de Microsoft Azure Stack

Ce document fournit des informations de dépannage pour les environnements intégrés Azure Stack. Pour obtenir de l’aide sur le kit de développement Azure Stack, consultez [Dépannage d’ASDK](../asdk/asdk-troubleshooting.md) ou contactez les experts du [forum MSDN Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

Ces sections contiennent des liens vers des documents qui abordent les questions les plus fréquemment posées aux services de support technique Microsoft.

### <a name="purchase-considerations"></a>Considérations relatives à l’achat

* [Comment acheter](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Vue d’ensemble d’Azure Stack](azure-stack-overview.md)

### <a name="updates-and-diagnostics"></a>Mises à jour et diagnostics

* [Comment utiliser les outils de diagnostic dans Azure Stack](azure-stack-diagnostics.md)
* [Comment valider l’état du système Azure Stack](azure-stack-diagnostic-test.md)
* [Cadence de publication des packages de mises à jour](azure-stack-servicing-policy.md#update-package-release-cadence)

### <a name="supported-operating-systems-and-sizes-for-guest-vms"></a>Systèmes d’exploitation et tailles pris en charge pour les machines virtuelles invitées

* [Systèmes d’exploitation invités pris en charge par Azure Stack](azure-stack-supported-os.md)
* [Tailles de machine virtuelle prises en charge dans Azure Stack](../user/azure-stack-vm-sizes.md)

### <a name="azure-marketplace"></a>Place de marché Azure

* [Éléments de Place de Marché Microsoft Azure disponibles pour Azure Stack](azure-stack-marketplace-azure-items.md)

### <a name="manage-capacity"></a>Gérer la capacité

#### <a name="memory"></a>Mémoire

Pour augmenter la capacité de mémoire totale disponible pour Azure Stack, vous pouvez ajouter plus de mémoire. Dans Azure Stack, votre serveur physique est également appelé nœud d'unité d'échelle. Tous les nœuds d'unité d'échelle qui sont membres d'une même unité d'échelle doivent avoir [la même quantité de mémoire](azure-stack-manage-storage-physical-memory-capacity.md).

#### <a name="retention-period"></a>Période de rétention

Le paramètre de période de conservation permet à un opérateur cloud de spécifier une période de temps en jours (entre 0 et 9 999 jours) pendant laquelle un compte supprimé peut être récupéré. La période de conservation par défaut est définie sur **0** jour. Pour ce paramètre, la valeur **0** signifie qu’un compte supprimé n’est plus conservé et qu’il est marqué comme devant faire l’objet d’un nettoyage périodique de la mémoire.

* [Définir la période de rétention](azure-stack-manage-storage-accounts.md#set-the-retention-period)

### <a name="security-compliance-and-identity"></a>Sécurité, conformité et identité  

#### <a name="manage-rbac"></a>Gérer RBAC

Un utilisateur Azure Stack peut être un lecteur, un propriétaire ou un collaborateur pour chaque instance d’un abonnement, d’un groupe de ressources ou d’un service.

* [Azure Stack - Gérer RBAC](azure-stack-manage-permissions.md)

Si les rôles intégrés des ressources Azure ne répondent pas aux besoins spécifiques de votre organisation, vous pouvez créer vos propres rôles personnalisés. Pour ce tutoriel, vous allez créer un rôle personnalisé nommé Reader Support Tickets à l’aide d’Azure PowerShell.

* [Tutoriel : Créer un rôle personnalisé pour les ressources Azure à l'aide d'Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>Gérer l’utilisation et la facturation en tant que fournisseur de services Cloud

* [Gérer l’utilisation et la facturation en tant que fournisseur de services cloud](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)
* [Créer un abonnement CSP ou APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

Choisissez le type de compte de services partagés que vous utilisez pour Azure Stack. Les types d’abonnements pouvant être utilisés pour l’inscription d’un système Azure Stack multilocataire sont les suivants :

* Fournisseur de services cloud
* Abonnement Partner Shared Services


## <a name="troubleshoot-deployment"></a>Résoudre les problèmes de déploiement 
### <a name="general-deployment-failure"></a>Échec général du déploiement
Si vous rencontrez un problème lors de l’installation, vous pouvez relancer le déploiement à partir de l’étape qui n’a pas abouti en utilisant l’option de réexécution du script de déploiement.  

### <a name="template-validation-error-parameter-osprofile-is-not-allowed"></a>Le paramètre d’erreur de validation de modèle osProfile n’est pas autorisé

Si vous recevez un message d’erreur pendant la validation du modèle et que le paramètre « osProfile » n’est pas autorisé, veillez à utiliser les versions appropriées des API pour ces composants :

- [Calcul](https://docs.microsoft.com/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions#microsoftcompute)
- [Réseau](https://docs.microsoft.com/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions#microsoftnetwork)

Pour copier un disque dur virtuel d’Azure vers Azure Stack, utilisez [AzCopy 7.3.0](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#download-and-install-azcopy). Collaborez avec votre fournisseur pour résoudre les problèmes liés à l’image elle-même. Pour plus d’informations sur les exigences de WALinuxAgent pour Azure Stack, consultez [Agent Linux Azure](azure-stack-linux.md#azure-linux-agent).

### <a name="deployment-fails-due-to-lack-of-external-access"></a>Le déploiement échoue en raison d’un manque d’accès externe
Lorsque le déploiement échoue à des étapes où l’accès externe est nécessaire, une exception telle que l’exemple suivant est retournée :

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
Si cette erreur se produit, vérifiez que toutes les exigences réseau minimales ont été satisfaites en consultant la [documentation sur le trafic réseau lié au déploiement](deployment-networking.md). Un outil de vérification du réseau est également disponible pour les partenaires dans le cadre du Partner Toolkit.

Les autres échecs de déploiement sont généralement dus à des problèmes de connexion aux ressources sur Internet.

Pour vérifier la connectivité aux ressources sur Internet, vous pouvez effectuer les étapes suivantes :

1. Ouvrez PowerShell.
2. Entrez -PSSession sur la machine virtuelle WAS01 ou sur une des machines virtuelles ERC.
3. Exécutez l’applet de commande suivante : 
   ```powershell
   Test-NetConnection login.windows.net -port 443
   ```

Si cette commande échoue, vérifiez que le commutateur TOR et tout autre périphérique réseau sont configurés pour [autoriser le trafic réseau](azure-stack-network.md).

## <a name="troubleshoot-virtual-machines"></a>Résoudre les problèmes liés aux machines virtuelles
### <a name="default-image-and-gallery-item"></a>Élément de la galerie et image par défaut
Vous devez ajouter un élément de la galerie et une image Windows Server avant de pouvoir déployer des machines virtuelles dans Azure Stack.


### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk"></a>J’ai supprimé des machines virtuelles, mais je vois toujours les fichiers VHD sur le disque.
Ce comportement est normal :

* La suppression d’une machine virtuelle n’entraîne pas celle des VHD. Les disques sont des ressources distinctes dans le groupe de ressources.
* Lorsqu’un compte de stockage est supprimé, la suppression est visible immédiatement sur Azure Resource Manager, mais les disques qu’il contient éventuellement restent conservés dans le stockage jusqu’à l’exécution du nettoyage de la mémoire.

Si vous voyez des VHD « orphelins », il est important de savoir s’ils font partie du dossier d’un compte de stockage supprimé. Si le compte de stockage n’a pas été supprimé, il est normal qu’ils soient toujours présents.

Pour en savoir plus sur la configuration du seuil de rétention et de la récupération à la demande, consultez la page [Gérer les comptes de stockage](azure-stack-manage-storage-accounts.md).

## <a name="troubleshoot-storage"></a>Résoudre les problèmes de stockage
### <a name="storage-reclamation"></a>Récupération du stockage
Il peut s’écouler jusqu’à 14 heures avant que la capacité récupérée ne s’affiche dans le portail. La récupération d’espace dépend de différents facteurs, notamment le pourcentage d’utilisation des fichiers conteneurs internes dans le magasin d’objets blob de blocs. Par conséquent, selon la quantité de données supprimées, il n’y a pas de garantie quant à la quantité d’espace récupérable lors de l’exécution du récupérateur de mémoire.

## <a name="troubleshooting-app-service"></a>Résolution des problèmes d’App Service
### <a name="create-aadidentityappps1-script-fails"></a>Le script Create-AADIdentityApp.ps1 échoue

Si le script Create-AADIdentityApp.ps1 nécessaire pour App Service échoue, veillez à inclure le paramètre -AzureStackAdminCredential obligatoire lors de l’exécution du script. Pour plus d’informations, consultez [Prérequis pour le déploiement d’App Service sur Azure Stack](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-app).

