---
title: Comprendre les principales différences entre Azure et Azure Stack lorsque vous utilisez des services et générer des applications | Microsoft Docs
description: Ce que vous devez savoir quand vous utilisez des services ou générez des applications pour Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/08/2019
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 3c3e00358efd582b5a6f1ddb0f1ae3634fe577c7
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64312878"
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>Principales considérations relatives à l’utilisation de services ou à la création d’applications pour Azure Stack

Avant d’utiliser des services ou de générer des applications pour Azure Stack, il est important de comprendre les différences entre Azure Stack et Azure. Cet article présente les points importants à prendre en compte lorsque vous utilisez Azure Stack comme environnement de développement cloud hybride.

## <a name="overview"></a>Vue d’ensemble

Azure Stack est une plateforme cloud hybride qui vous permet d’utiliser les services Azure à partir du centre de données de votre entreprise ou de votre fournisseur de services. Vous pouvez créer une application dans Azure Stack et la déployer ensuite dans Azure Stack, Azure ou votre cloud hybride Azure.

Votre opérateur Azure Stack vous indiquera les services que vous pouvez utiliser et comment obtenir un support technique. Il propose ces services par le biais de ses offres et plans personnalisés.

Le contenu technique Azure suppose que les applications sont développées pour un service Azure au lieu d’Azure Stack. Lorsque vous générez et déployez des applications sur Azure Stack, vous devez comprendre les principales différences, telles que les suivantes :

* Azure Stack fournit un sous-ensemble des services et fonctionnalités qui sont disponibles dans Azure.
* Votre entreprise ou fournisseur de services peut choisir les services à proposer. Les options disponibles peuvent inclure des applications ou des services personnalisés. Il peut offrir sa propre documentation personnalisée.
* Vous devez utiliser les points de terminaison corrects spécifiques à Azure Stack (par exemple, les URL de l’adresse du portail et le point de terminaison Azure Resource Manager).
* Vous devez utiliser des versions de PowerShell et d’API qui sont prises en charge par Azure Stack. Utiliser des versions prises en charge permet de s’assurer que vos applications fonctionnent dans Azure Stack et Azure.

## <a name="cheat-sheet-high-level-differences"></a>Aide-mémoire : principales différences

Le tableau suivant décrit les principales différences entre Azure Stack et Azure. Gardez ces différences à l’esprit lorsque vous développez pour Azure Stack ou utilisez des services Azure Stack.

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

| Domaine | Azure (global) | Azure Stack |
| -------- | ------------- | ----------|
| Qui est en charge de son fonctionnement ? | Microsoft | Votre organisation ou fournisseur de services.|
| Qui contactez-vous pour obtenir un support ? | Microsoft | Pour un système intégré, contactez votre opérateur Azure Stack (auprès de votre organisation ou fournisseur de services) pour obtenir un support.<br><br>Pour la prise en charge du Kit de développement Azure Stack, visitez les [forums Microsoft](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). Étant donné que le kit de développement est un environnement d’évaluation, il n’y a aucune prise en charge officielle de la part des services clients Microsoft.
| Services disponibles | Consultez la liste des [produits Azure](https://azure.microsoft.com/services/?b=17.04b). Les services disponibles varient selon la région Azure. | Azure Stack prend en charge une partie des services Azure. Les services réels varient en fonction de ce que votre organisation ou fournisseur de services choisit d’offrir.
| Point de terminaison Azure Resource Manager* | https://management.azure.com | Pour un système intégré Azure Stack, utilisez le point de terminaison fourni par votre opérateur Azure Stack.<br><br>Pour le kit de développement, utilisez : https://management.local.azurestack.external.
| URL du portail* | [https://portal.azure.com](https://portal.azure.com) | Pour un système intégré Azure Stack, utilisez l’URL fournie par votre opérateur Azure Stack.<br><br>Pour le kit de développement, utilisez : https://portal.local.azurestack.external.
| Région | Vous pouvez sélectionner la région où effectuer le déploiement. | Pour un système intégré Azure Stack, utilisez la région disponible sur votre système.<br><br>Pour le kit de développement, la région est toujours définie sur **local**.
| Groupes de ressources | Un groupe de ressources peut s’étendre sur plusieurs régions. | Pour les systèmes intégrés et le kit de développement, il n’existe qu’une seule région.
|Espaces de noms, types de ressources et versions d’API pris en charge | La dernière version (ou les versions antérieures qui ne sont pas encore obsolètes). | Azure Stack prend en charge des versions spécifiques. Consultez la section [Configuration requise pour la version](#version-requirements) de cet article.
| | |

*Si vous êtes un opérateur Azure Stack, consultez [Utilisation du portail administrateur](../operator/azure-stack-manage-portals.md) et [Principes de bases de l’administration](../operator/azure-stack-manage-basics.md) pour plus d’informations.

## <a name="helpful-tools-and-best-practices"></a>Outils utiles et meilleures pratiques

Microsoft fournit des outils et des conseils qui vous aident lors du développement pour Azure Stack.

| Recommandation | Références |
| -------- | ------------- |
| Installez les outils appropriés sur votre station de travail du développeur. | - [Installer PowerShell](../operator/azure-stack-powershell-install.md)<br>- [Télécharger des outils](../operator/azure-stack-powershell-download.md)<br>- [Configurer PowerShell](azure-stack-powershell-configure-user.md)<br>- [Installer Visual Studio](azure-stack-install-visual-studio.md) 
| Passez en revue les informations relatives aux éléments suivants :<br>- Considérations relatives au modèle Azure Resource Manager<br>- Comment trouver les modèles de démarrage rapide<br>- Utiliser un module de stratégie pour vous aider à utiliser Azure pour le développement pour Azure Stack | [Développer pour Azure Stack](azure-stack-developer.md) | 
| Passez en revue et appliquez les meilleures pratiques relatives aux modèles. | [Modèles de démarrage rapide Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Configuration requise pour la version

Azure Stack prend en charge des versions spécifiques d’Azure PowerShell et des API de service Azure. Utilisez les versions prises en charge pour vous assurer que votre application peut être déployée dans Azure Stack et Azure.

Pour vous assurer que vous utilisez une version appropriée d’Azure PowerShell, utilisez les [profils de version d’API](azure-stack-version-profiles.md). Pour connaître le profil de version d’API le plus récent que vous pouvez utiliser, vous devez savoir quelle version d’Azure Stack vous utilisez. Contactez votre administrateur Azure Stack pour obtenir ces informations.

> [!NOTE]
> Si vous utilisez le Kit de développement Azure Stack, et que vous disposez d’un accès administratif, consultez la section [Déterminer la version actuelle](../operator/azure-stack-updates.md#determine-the-current-version) pour connaître le build Azure Stack.

Pour les autres API, exécutez la commande PowerShell suivante pour afficher les espaces de noms, les types de ressources et les versions d’API qui sont pris en charge dans votre abonnement Azure Stack. Notez que des différences peuvent subsister au niveau de la propriété. Pour que cette commande fonctionne, vous devez avoir déjà [installé](../operator/azure-stack-powershell-install.md) et [configuré](azure-stack-powershell-configure-user.md) PowerShell pour un environnement Azure Stack. Vous devez également disposer d’un abonnement à une offre Azure Stack.

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Exemple de sortie (tronquée) : ![Exemple de sortie de la commande Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les différences au niveau du service, consultez :

* [Considérations relatives aux machines virtuelles dans Azure Stack](azure-stack-vm-considerations.md)
* [Considérations relatives au stockage dans Azure Stack](azure-stack-acs-differences.md)
* [Considérations relatives à la mise en réseau Azure Stack](azure-stack-network-differences.md)
