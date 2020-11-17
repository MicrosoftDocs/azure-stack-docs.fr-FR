---
title: PowerShell dans Azure Stack Hub
description: PowerShell dans Azure Stack Hub présente un certain nombre de modules et de contextes.
author: mattbriggs
ms.topic: article
ms.date: 10/16/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/16/2020
ms.openlocfilehash: 7103a2a8268ffb4c8cbbc84f35e6e1a6a90992c3
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546580"
---
# <a name="get-started-with-powershell-in-azure-stack-hub"></a>Bien démarrer avec PowerShell dans Azure Stack Hub

PowerShell est conçu pour gérer et administrer des ressources en ligne de commande. Vous pouvez utiliser PowerShell lorsque vous voulez créer des outils automatisés qui utilisent le modèle Azure Resource Manager. Un module PowerShell peut être défini comme un ensemble de fonctions PowerShell regroupées afin de gérer tous les aspects d’une zone particulière. Pour utiliser Azure Stack Hub, vous devez jongler avec différents ensembles de cmdlets PowerShell.

Cet article vous aide à vous repérer dans les différents modules PowerShell utilisés dans Azure Stack Hub. Lorsque vous utilisez PowerShell dans Azure Stack Hub, vous pouvez interagir avec les quatre ensembles d’API, comme indiqué dans le tableau suivant :

| API | Référence sur PowerShell | Référence REST |
| --- | --- | --- |
| Global Azure Resource Manager | [Modules Azure PowerShell](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [Navigateur d’API REST](/rest/api/) |
| Azure Stack Hub Resource Manager | [Gérer les profils de version des API dans Azure Stack Hub](azure-stack-version-profiles.md) | [Gérer les profils de version des API dans Azure Stack Hub](azure-stack-version-profiles.md) |
| Points de terminaison administrateur Azure Stack Hub | [Module administrateur Azure Stack Hub](/powershell/azure/azure-stack/overview) | [Navigateur d’API REST - Azure Stack Hub](/rest/api/?term=Azure Azure Stack Admin) |
| Point de terminaison privilégié Azure Stack Hub | [Utiliser le point de terminaison privilégié dans Azure Stack Hub](../operator/azure-stack-privileged-endpoint.md) | |

Chaque interface contacte les fournisseurs de ressources dans Azure global ou Azure Stack Hub. Les fournisseurs de ressources activent les fonctionnalités Azure. Par exemple, le fournisseur de ressources Azure Compute vous donne un accès programmatique à la création et à la gestion de machines virtuelles et de leurs ressources prises en charge.

Les fournisseurs de ressources offrent à la fois des fonctionnalités et des contrôles pour la gestion et la configuration de la ressource. Vous pouvez accéder programmatiquement aux fournisseurs de ressources avec Azure Resource Manager. En retour, l’interface fournit une surface pour PowerShell, Azure CLI et vos propres clients REST.

## <a name="where-to-find-azure-stack-hub-powershell"></a>Où trouver Azure Stack Hub PowerShell

Le diagramme de blocs suivant illustre la relation entre les ensembles de modules PowerShell. À partir de votre machine, vous pouvez charger les modules PowerShell et gérer Azure global et Azure Stack Hub.

![Azure Stack Hub PowerShell](media/azure-stack-powershell-overview/azure-stack-powershell.svg)

### <a name="global-azure"></a>Azure global

Azure PowerShell contient un ensemble d’applets de commande qui utilisent la version actuelle d’Azure Resource Manager pour l’utilisation de vos ressources Azure. Azure PowerShell utilise la version .NET Standard, ce qui signifie que vous pouvez utiliser des versions de PowerShell avec Windows, macOS et Linux. Azure PowerShell est également disponible sur Azure Cloud Shell. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="azure-stack-hub-resource-manager"></a>Azure Stack Hub Resource Manager

Azure Stack Hub PowerShell fournit un ensemble de cmdlets qui utilisent des versions précédentes d’Azure Resource Manager. Ces cmdlets sont compatibles avec les fournisseurs de ressources dans Azure Stack Hub. Chaque fournisseur de ressources dans Azure Stack Hub utilise une version antérieure du fournisseur trouvé dans Azure global. Pour vous aider à coordonner la version de chaque fournisseur pris en charge par Azure Stack Hub, vous pouvez utiliser des profils d’API. Pour plus d’informations, consultez [Gérer les profils de version des API dans Azure Stack Hub](azure-stack-version-profiles.md).

### <a name="azure-stack-hub-administrator"></a>Administrateur Azure Stack Hub

Azure Stack Hub expose un ensemble de fournisseurs de ressources à l’opérateur cloud pour que celui-ci puisse installer et gérer Azure Stack Hub. Dans Azure global, cette interaction est tirée de l’utilisateur et gérée en arrière-plan comme faisant partie d’Azure. Cela étant, Azure Stack Hub permet aux grandes entreprises de prendre en charge un cloud privé. Pour effectuer ces tâches, l’opérateur interagit avec les API d’administration Azure Stack Hub. Pour plus d’informations, consultez [Installer PowerShell pour Azure Stack Hub](../operator/powershell-install-az-module.md).

### <a name="azure-stack-hub-privileged-endpoint"></a>Point de terminaison privilégié Azure Stack Hub

Pour les activités d’opérateur dans Azure Stack Hub, telles que tester l’installation et accéder aux journaux, les opérateurs peuvent interagir avec le point de terminaison privilégié. Ce point de terminaison est une console PowerShell distante, préconfigurée, qui fournit aux opérateurs suffisamment d’accès pour effectuer des tâches particulières. Le point de terminaison s’appuie sur PowerShell JEA (Just Enough Administration) pour exposer un ensemble limité d’applets de commande. Pour plus d’informations, consultez [Utiliser le point de terminaison privilégié dans Azure Stack Hub](../operator/azure-stack-privileged-endpoint.md).

### <a name="azure-stack-hub-tools"></a>Outils Azure Stack Hub

Azure Stack Hub met à disposition des scripts ainsi que d’autres applets de commande dans un référentiel GitHub, *AzureStack-Tools*. Les outils AzureStack héberge des modules PowerShell permettant de gérer et de déployer des ressources sur Azure Stack Hub. Si vous prévoyez d’établir la connectivité VPN, vous pouvez télécharger ces modules PowerShell avec le kit de développement Azure Stack ou sur un client externe Windows. Pour plus d’informations, accédez à la page [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools).

## <a name="work-with-powershell-in-azure-stack-hub"></a>Utiliser PowerShell dans Azure Stack Hub

PowerShell fournit un moyen programmatique d’interagir avec Azure Resource Manager. Vous pouvez utiliser une invite de commandes interactive ou, si vous automatisez des tâches, écrire des scripts.

Si vous utilisez beaucoup Azure Stack Hub PowerShell, vous vous surprendrez à installer et réinstaller les modules. Si vous travaillez avec Azure global en même temps, cette routine peut s’avérer compliquée, car vous devez désinstaller et réinstaller vos modules en fonction de votre cible.

Vous pouvez utiliser des conteneurs Docker pour isoler chaque version de PowerShell sur votre machine locale. Pour utiliser des conteneurs Docker afin de pouvoir passer d’un ensemble de modules PowerShell à un autre, consultez [Utiliser Docker pour exécuter PowerShell](azure-stack-powershell-user-docker.md).


## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [Profils d’API pour PowerShell](azure-stack-version-profiles.md) dans Azure Stack Hub.
- Installez [Azure Stack Hub PowerShell](../operator/powershell-install-az-module.md).
- Découvrez comment créer des [Modèles Azure Resource Manager](azure-stack-develop-templates.md) pour la cohérence du cloud.
