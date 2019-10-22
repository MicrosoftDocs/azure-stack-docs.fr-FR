---
title: Fonctionnalités des machines virtuelles Azure Stack | Microsoft Docs
description: Découvrez les différentes fonctionnalités et les éléments à prendre en compte lors de l’utilisation des machines virtuelles dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 10/09/2019
ms.openlocfilehash: e3601f4489a0a80881cccd2ba64f98b61e14683a
ms.sourcegitcommit: 70147e858956443bc66b3541ec278c102bb45c07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72381435"
---
# <a name="azure-stack-vm-features"></a>Fonctionnalités des machines virtuelles Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Les machines virtuelles Azure Stack fournissent des ressources de calcul scalables et à la demande. Avant de déployer des machines virtuelles, il est important de connaître les différences entre les fonctionnalités de machine virtuelle disponibles dans Azure Stack et Microsoft Azure. Cet article explique ces différences et identifie les points clés à prendre en compte lors de la planification des déploiements de machines virtuelles. Pour en savoir plus sur les principales différences entre Azure Stack et Azure, consultez l’article [Principales considérations](azure-stack-considerations.md).

## <a name="vm-differences"></a>Différences entre les machines virtuelles

| Fonctionnalité | Azure (global) | Azure Stack |
| --- | --- | --- |
| Images de machine virtuelle | La Place de marché Azure contient des images que vous pouvez utiliser pour créer une machine virtuelle. Consultez la page [Place de marché Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) pour obtenir la liste des images disponibles sur la Place de marché Azure. | Par défaut, il n’y a pas d’images disponibles sur la Place de marché Azure Stack. L’administrateur du cloud Azure Stack doit publier ou télécharger des images sur la Place de marché Azure Stack pour les mettre à la disposition des utilisateurs. |
| Tailles de machines virtuelles | Azure prend en charge de nombreuses tailles de machine virtuelle. Pour connaître les options et les tailles disponibles, consultez les rubriques [Tailles des machines virtuelles Windows](/azure/virtual-machines/virtual-machines-windows-sizes) et [Tailles des machines virtuelles Linux](/azure/virtual-machines/linux/sizes). | Azure Stack prend en charge certaines des tailles de machine virtuelle qui sont disponibles dans Azure. Pour obtenir la liste des tailles prises en charge, consultez la section [Tailles de machine virtuelle](#vm-sizes) dans cet article. |
| Quotas de machine virtuelle | Les [limites de quota](/azure/azure-subscription-service-limits#service-specific-limits) sont définies par Microsoft. | L’administrateur du cloud Azure Stack doit assigner des quotas avant de mettre des machines virtuelles à la disposition des utilisateurs. |
| Extensions de machine virtuelle |Azure prend en charge de nombreuses extensions de machine virtuelle. Pour connaître les extensions disponibles, consultez l’article [Extensions et fonctionnalités de machine virtuelle](/azure/virtual-machines/windows/extensions-features).| Azure Stack prend en charge certaines des extensions disponibles dans Azure. Notez que chaque extension a des versions spécifiques. L’administrateur du cloud Azure Stack peut choisir les extensions qu’il veut mettre à la disposition des utilisateurs. Pour obtenir la liste des extensions prises en charge, consultez la section [Extensions de machine virtuelle](#vm-extensions) dans cet article. |
| Réseau de machines virtuelles | Les adresses IP publiques assignées à une machine virtuelle locataire sont accessibles via Internet.<br><br><br>Les machines virtuelles Azure ont un nom DNS fixe. | Les adresses IP publiques assignées à une machine virtuelle locataire sont accessibles uniquement au sein de l’environnement du Kit de développement Azure Stack. Un utilisateur doit pouvoir accéder au Kit de développement Azure Stack par le biais du [RDP](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) ou du [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) pour se connecter à une machine virtuelle créée dans Azure Stack.<br><br>Les machines virtuelles créées dans une instance Azure Stack spécifique ont un nom DNS basé sur la valeur définie par l’administrateur du cloud. |
| Stockage de machine virtuelle | Prend en charge les [disques managés](/azure/virtual-machines/windows/managed-disks-overview). | Les disques managés sont pris en charge dans Azure Stack avec la version 1808 et les versions ultérieures. |
| Performances des disques de machines virtuelles | Dépendent de la taille et du type de disque. | Dépendent de la taille de la machine virtuelle à laquelle les disques sont attachés. Pour plus d’informations, consultez l’article [Tailles de machine virtuelle prises en charge dans Azure Stack](azure-stack-vm-sizes.md).
| Versions d’API | Azure utilise toujours les dernières versions d’API pour toutes les fonctionnalités de machine virtuelle. | Azure Stack prend en charge certains services Azure et des versions d’API spécifiques pour ces services. Pour obtenir la liste des versions d’API prises en charge, consultez la section [Versions d’API](#api-versions) dans cet article. |
| Service de métadonnées d’instance Azure | Le service Azure Instance Metadata Service fournit des informations sur l’exécution d’instances de machine virtuelle qui peuvent être utilisées pour gérer et configurer votre machine virtuelle.  | Le service Azure Instance Metadata Service n’est pas pris en charge sur Azure Stack. |
| Groupes à haute disponibilité de machines virtuelles|Plusieurs domaines d’erreur (2 ou 3 par région).<br>Plusieurs domaines de mise à jour.|Plusieurs domaines d’erreur (2 ou 3 par région).<br>Un seul domaine de mise à jour, avec migration en direct pour protéger les charges de travail pendant la mise à jour. 20 domaines de mise à jour pris en charge pour la compatibilité des modèles.<br>La machine virtuelle et le groupe à haute disponibilité doivent se trouver dans le même emplacement et le même groupe de ressources.|
| Groupes identiques de machines virtuelles|La mise à l’échelle automatique est prise en charge.|La mise à l’échelle automatique n’est pas prise en charge.<br><br>Pour ajouter d’autres instances à un groupe identique, utilisez le portail, les modèles Resource Manager ou PowerShell. |
| Témoin cloud | Sélectionnez les points de terminaison dans les propriétés du compte de stockage disponibles dans Azure Stack. | Le [témoin de cloud](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) est un type de témoin de quorum de cluster de basculement qui utilise Microsoft Azure pour fournir un vote sur le quorum du cluster.<br>Voici à quoi peuvent ressembler les points de terminaison dans Azure global et ceux dans Azure Stack :<br>Azure global :<br>`https://mywitness.blob.core.windows.net/`<br>Azure Stack :<br>`https://mywitness.blob.<region>.<FQDN>/`|
| Diagnostics de machine virtuelle | Les diagnostics de machine virtuelle Linux sont pris en charge. | Les diagnostics de machine virtuelle Linux ne sont pas pris en charge dans Azure Stack. Lorsque vous déployez une machine virtuelle Linux en activant les diagnostics de machine virtuelle, le déploiement échoue. Le déploiement échoue également si vous activez les mesures de base de la machine virtuelle Linux dans les paramètres de diagnostic. |

## <a name="vm-sizes"></a>Tailles de machine virtuelle

Azure Stack impose des limites de ressources pour éviter la consommation excessive des ressources (au niveau du service ou du serveur local). Ces limites améliorent les performances du locataire en réduisant l’impact de la consommation des ressources par d’autres locataires.

- Pour la sortie réseau de la machine virtuelle, des limites de bande passante sont en place. Les limites dans Azure Stack sont les mêmes que celles appliquées dans Azure.
- Pour les ressources de stockage, Azure Stack implémente des limites d’opérations d’entrée/sortie par seconde (IOPS, Input/Output Operations Per Second) pour éviter une consommation excessive de base des ressources par les locataires pour l’utilisation du stockage.
- Pour les disques de machine virtuelle, les opérations IOPS du disque sur Azure Stack dépendent de la taille de la machine virtuelle et non du type de disque. Cela signifie que, pour une machine virtuelle Standard_Fs, quel que soit le type de disque choisi (SSD ou HDD), la limite d’IOPS est de 2 300 pour un deuxième disque de données.

Le tableau suivant répertorie les machines virtuelles prises en charge sur Azure Stack, ainsi que leur configuration :

| Type            | Size          | Plage de tailles prises en charge |
| ----------------| ------------- | ------------------------ |
|Usage général  |De base A        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|Usage général  |Standard A     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|Usage général  |Série Av2     |[A1_v2 - A8m_v2](azure-stack-vm-sizes.md#av2-series)     |
|Usage général  |Série D       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|Usage général  |Série Dv2     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Usage général  |Série DS      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Usage général  |Séries DSv2    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Mémoire optimisée |Série D       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|Mémoire optimisée |Série DS      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|Mémoire optimisée |Série Dv2     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Mémoire optimisée |Séries DSv2    |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |
|Optimisé pour le calcul|Série F       |[F1 - F16](azure-stack-vm-sizes.md#f-series)    |
|Optimisé pour le calcul|Série Fs      |[F1s - F16s](azure-stack-vm-sizes.md#fs-series)    |
|Optimisé pour le calcul|Série Fsv2    |[F2s_v2 - F64s_v2](azure-stack-vm-sizes.md#fsv2-series)    |

Les tailles de machine virtuelle et les quantités de ressources associées sont cohérentes entre Azure Stack et Azure. Cette cohérence inclut la quantité de mémoire, le nombre de cœurs et le nombre ou la taille des disques de données qui peuvent être créés. Toutefois, les performances des machines virtuelles de même taille dépendent des caractéristiques sous-jacentes de chaque environnement Azure Stack.

## <a name="vm-extensions"></a>Extensions de machine virtuelle

Azure Stack inclut un petit ensemble d’extensions. Des mises à jour et des extensions supplémentaires sont disponibles par le biais de la syndication de Place de marché.

Utilisez le script PowerShell suivant pour obtenir la liste des extensions de machine virtuelle qui sont disponibles dans votre environnement Azure Stack :

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, laissez expirer le délai d’attente de l’approvisionnement au lieu d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.

## <a name="api-versions"></a>Versions d’API

Les fonctionnalités de machine virtuelle dans Azure Stack prennent en charge les versions d’API suivantes :

« 2017-12-01 », « 2017-03-30 », « 2016-03-30 », « 2015-06-15 »

Utilisez le script PowerShell suivant pour obtenir la liste des versions d’API des fonctionnalités de machine virtuelle qui sont disponibles dans votre environnement Azure Stack :

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```

La liste des types de ressources et des versions d’API pris en charge peut varier si l’opérateur du cloud met à jour votre environnement Azure Stack avec une version plus récente.

## <a name="windows-activation"></a>Activation de Windows

Les produits Windows doivent être utilisés conformément aux droits d’utilisation des produits et aux termes des contrats de licence Microsoft. Azure Stack active les machines virtuelles Windows Server à l’aide de la fonctionnalité [Activation automatique des machines virtuelles](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA).

- L’hôte Azure Stack active Windows avec les clés AVMA pour Windows Server 2016. Toutes les machines virtuelles exécutant Windows Server 2012 R2 ou une version ultérieure sont automatiquement activées.
- Celles qui exécutent Windows Server 2012 ou une version antérieure doivent être activées manuellement à l’aide de l’[activation MAK](https://technet.microsoft.com/library/ff793438.aspx). Pour utiliser l’activation MAK, vous devez fournir votre propre clé de produit (Product Key).

Microsoft Azure utilise l’activation KMS pour activer les machines virtuelles Windows. Si vous déplacez une machine virtuelle d’Azure Stack vers Azure et rencontrez des problèmes d’activation, consultez [Résoudre les problèmes d’activation de Windows sur les machines virtuelles Azure](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems). Vous trouverez des informations supplémentaires dans le billet de blog [Troubleshooting Windows activation failures on Azure VMs](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) publié par l’équipe du support technique Azure.

## <a name="next-steps"></a>Étapes suivantes

[Créer une machine virtuelle Windows à l’aide de PowerShell dans Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)
