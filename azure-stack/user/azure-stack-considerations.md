---
title: Différences entre Azure Stack Hub et Azure quand vous utilisez des services et créez des applications
description: Comprendre les différences entre Azure et Azure Stack Hub quand vous utilisez des services et créez des applications.
author: sethmanheim
ms.topic: overview
ms.date: 06/11/2020
ms.author: sethm
ms.lastreviewed: 12/27/2019
ms.openlocfilehash: fb5f30d97d1c12a94b5366a358dc21450965fd93
ms.sourcegitcommit: 5f4f0ee043ff994efaad44129ce49be43c64d5dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2020
ms.locfileid: "84819528"
---
# <a name="differences-between-azure-stack-hub-and-azure-when-using-services-and-building-apps"></a>Différences entre Azure Stack Hub et Azure quand vous utilisez des services et créez des applications

Avant d’utiliser des services ou de créer des applications pour Azure Stack Hub, il est important de comprendre les différences existant entre Azure Stack Hub et Azure. Cet article présente les différentes fonctionnalités et les points importants à prendre en compte quand vous utilisez Azure Stack Hub comme environnement de développement cloud hybride.

## <a name="overview"></a>Vue d’ensemble

Azure Stack Hub est une plateforme cloud hybride qui vous permet d’utiliser les services Azure à partir du centre de données de votre entreprise ou de votre fournisseur de services. Vous pouvez créer une application dans Azure Stack Hub et la déployer ensuite dans Azure Stack Hub, Azure ou votre cloud hybride Azure.

Votre opérateur Azure Stack Hub vous indique les services disponibles et comment accéder au support. Il propose ces services par le biais de ses offres et plans personnalisés.

Le [contenu de la documentation technique Azure](/azure) part du principe que les applications sont développées pour un service Azure et non pour Azure Stack Hub. Lorsque vous générez et déployez des applications sur Azure Stack Hub, vous devez comprendre les principales différences, telles que les suivantes :

* Azure Stack Hub fournit une partie des services et fonctionnalités qui sont disponibles dans Azure.
* Votre entreprise ou fournisseur de services peut choisir les services à proposer. Les options disponibles peuvent inclure des applications ou des services personnalisés. Il peut offrir sa propre documentation personnalisée.
* Utilisez les points de terminaison corrects spécifiques à Azure Stack Hub (par exemple, les URL de l’adresse du portail et le point de terminaison Azure Resource Manager).
* Vous devez utiliser des versions de PowerShell et d’API qui sont prises en charge par Azure Stack Hub. Utiliser des versions prises en charge permet de s’assurer que vos applications fonctionnent dans Azure Stack Hub et Azure.

## <a name="cheat-sheet-high-level-differences"></a>Aide-mémoire : principales différences

Le tableau suivant décrit les principales différences entre Azure Stack Hub et Azure. Gardez ces différences à l’esprit quand vous développez pour Azure Stack Hub ou utilisez des services Azure Stack Hub :

| Domaine | Azure (global) | Azure Stack Hub |
| -------- | ------------- | ----------|
| Qui est en charge de son fonctionnement ? | Microsoft | Votre organisation ou fournisseur de services.|
| Qui contactez-vous pour obtenir un support ? | Microsoft | Pour un système intégré, contactez votre opérateur Azure Stack Hub (auprès de votre organisation ou fournisseur de services) pour obtenir un support.<br><br>Pour plus d’informations sur le support lié au Kit de développement Azure Stack (ASDK), visitez les [forums Microsoft](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack). Le Kit de développement étant un environnement d’évaluation, le support Microsoft n’offre aucun support officiel.
| Services disponibles | Consultez la liste des [produits Azure](https://azure.microsoft.com/services/?b=17.04b). Les services disponibles varient selon la région Azure. | Azure Stack Hub prend en charge une partie des services Azure. Les services réels varient en fonction de ce que votre organisation ou fournisseur de services choisit d’offrir.
| Point de terminaison Azure Resource Manager* | `https://management.azure.com` | Pour un système intégré Azure Stack Hub, utilisez le point de terminaison fourni par votre opérateur Azure Stack Hub.<br><br>Pour le kit de développement, utilisez : `https://management.local.azurestack.external`.
| URL du portail* | [https://portal.azure.com](https://portal.azure.com) | Pour un système intégré Azure Stack Hub, utilisez l’URL fournie par votre opérateur Azure Stack Hub.<br><br>Pour le kit de développement, utilisez : `https://portal.local.azurestack.external`.
| Région | Vous pouvez sélectionner la région où effectuer le déploiement. | Pour un système intégré Azure Stack Hub, utilisez la région disponible sur votre système.<br><br>Pour le Kit de développement Azure Stack (ASDK), la région est toujours définie sur **local**.
| Groupes de ressources | Un groupe de ressources peut s’étendre sur plusieurs régions. | Pour les systèmes intégrés et le kit de développement, il n’existe qu’une seule région.
|Espaces de noms, types de ressources et versions d’API pris en charge | La dernière version (ou les versions antérieures qui ne sont pas encore dépréciées). | Azure Stack Hub prend en charge des versions spécifiques. Consultez la section [Configuration requise pour la version](#version-requirements) de cet article.
| | |

*Si vous êtes opérateur Azure Stack Hub, consultez [Utilisation du portail administrateur](../operator/azure-stack-manage-portals.md) et [Principes de bases de l’administration](../operator/azure-stack-manage-basics.md) pour plus d’informations.

## <a name="helpful-tools-and-best-practices"></a>Outils utiles et meilleures pratiques

Microsoft fournit des outils et des conseils qui vous aident dans votre développement pour Azure Stack Hub.

| Recommandation | References |
| -------- | ------------- |
| Installez les outils appropriés sur votre station de travail du développeur. | - [Installer PowerShell](../operator/azure-stack-powershell-install.md)<br>- [Télécharger des outils](../operator/azure-stack-powershell-download.md)<br>- [Configurer PowerShell](azure-stack-powershell-configure-user.md)<br>- [Installer Visual Studio](azure-stack-install-visual-studio.md)
| Passez en revue les informations relatives aux éléments suivants :<br>- Considérations sur les modèles Azure Resource Manager.<br>- Comment trouver les modèles de démarrage rapide.<br>- Se servir d’un module de stratégie afin d’utiliser Azure et développer pour Azure Stack Hub. | [Développer pour Azure Stack Hub](azure-stack-developer.md) |
| Passez en revue et appliquez les meilleures pratiques relatives aux modèles. | [Modèles de démarrage rapide Resource Manager](https://aka.ms/aa6yz42)
| | |

## <a name="version-requirements"></a>Configuration requise pour la version

Azure Stack Hub prend en charge des versions spécifiques d’Azure PowerShell et des API de service Azure. Utilisez les versions prises en charge pour vous assurer que votre application peut être déployée dans Azure Stack Hub et Azure.

Pour vous assurer que vous utilisez une version appropriée d’Azure PowerShell, utilisez les [profils de version d’API](azure-stack-version-profiles.md). Pour connaître le profil de version d’API le plus récent que vous pouvez adopter, déterminez quelle build d’Azure Stack Hub vous utilisez. Contactez votre administrateur Azure Stack Hub pour obtenir ces informations.

> [!NOTE]
> Si vous utilisez le Kit de développement Azure Stack, et que vous disposez d’un accès administratif, consultez la section [Déterminer la version actuelle](../operator/azure-stack-updates.md) pour connaître la build Azure Stack Hub.

Pour les autres API, exécutez la commande PowerShell suivante pour afficher les espaces de noms, les types de ressources et les versions d’API qui sont pris en charge dans votre abonnement Azure Stack Hub (des différences peuvent subsister au niveau des propriétés). Pour que cette commande fonctionne, vous devez avoir déjà [installé](../operator/azure-stack-powershell-install.md) et [configuré](azure-stack-powershell-configure-user.md) PowerShell pour un environnement Azure Stack Hub. Vous devez également disposer d’un abonnement à une offre Azure Stack Hub.

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Exemple de sortie (tronquée) : ![Exemple de sortie de la commande Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les différences au niveau du service, consultez :

* [Considérations relatives aux machines virtuelles dans Azure Stack Hub](azure-stack-vm-considerations.md)
* [Considérations relatives au stockage dans Azure Stack Hub](azure-stack-acs-differences.md)
* [Considérations relatives aux réseaux Azure Stack Hub](azure-stack-network-differences.md)
