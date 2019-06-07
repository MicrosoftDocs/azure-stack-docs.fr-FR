---
title: PowerShell dans Azure Stack | Microsoft Docs
description: PowerShell dans Azure Stack présente un certain nombre de modules et de contextes.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 8f5d97969c96e8f3546d37ffed28b6332d80dc05
ms.sourcegitcommit: 2ee75ded704e8cfb900d9ac302d269c54a5dd9a3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394425"
---
# <a name="get-started-with-powershell-in-azure-stack"></a>Bien démarrer avec PowerShell dans Azure Stack

PowerShell est conçu pour gérer et administrer des ressources en ligne de commande. Vous pouvez utiliser PowerShell lorsque vous voulez créer des outils automatisés qui utilisent le modèle Azure Resource Manager. Un module PowerShell peut être défini comme un ensemble de fonctions PowerShell regroupées afin de gérer tous les aspects d’une zone particulière. Pour utiliser Azure Stack, vous devez jongler avec différents ensembles d’applets de commande PowerShell.

Cet article vous aide à vous repérer dans les différents modules PowerShell utilisés dans Azure Stack. Quand vous utilisez PowerShell dans Azure Stack, vous pouvez interagir avec les quatre ensembles d’API, comme indiqué dans le tableau suivant :

| API | Référence sur PowerShell | Référence REST |
| --- | --- | --- |
| Global Azure Resource Manager | [Modules Azure PowerShell](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [Navigateur d’API REST](https://docs.microsoft.com/rest/api/) |
| Azure Stack Resource Manager | [Gérer les profils de version des API dans Azure Stack](azure-stack-version-profiles.md) | [Gérer les profils de version des API dans Azure Stack](azure-stack-version-profiles.md) |
| Points de terminaison Azure Stack Administrator | [Module Administrateur Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [Navigateur d’API REST - Azure Stack](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| Point de terminaison privilégié Azure Stack | [Utiliser le point de terminaison privilégié dans Azure Stack](../operator/azure-stack-privileged-endpoint.md) | |

Chaque interface contacte les fournisseurs de ressources dans Azure global ou Azure Stack. Les fournisseurs de ressources activent les fonctionnalités Azure. Par exemple, le fournisseur de ressources Azure Compute vous donne un accès programmatique à la création et à la gestion de machines virtuelles et de leurs ressources prises en charge.

Les fournisseurs de ressources offrent à la fois des fonctionnalités et des contrôles pour la gestion et la configuration de la ressource. Vous pouvez accéder programmatiquement aux fournisseurs de ressources avec Azure Resource Manager. En retour, l’interface fournit une surface pour PowerShell, Azure CLI et vos propres clients REST.

## <a name="where-to-find-azure-stack-powershell"></a>Où trouver Azure Stack PowerShell

Le diagramme de blocs suivant illustre la relation entre les ensembles de modules PowerShell. À partir de votre machine, vous pouvez charger les modules PowerShell et gérer Azure global et Azure Stack.

![Azure Stack PowerShell](media/azure-stack-powershell-overview/Azure-Stack-PowerShell.png)

### <a name="global-azure"></a>Azure global

Azure PowerShell contient un ensemble d’applets de commande qui utilisent la version actuelle d’Azure Resource Manager pour l’utilisation de vos ressources Azure. Azure PowerShell utilise la version .NET Standard, ce qui signifie que vous pouvez utiliser des versions de PowerShell avec Windows, macOS et Linux. Azure PowerShell est également disponible sur Azure Cloud Shell. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="azure-stack-resource-manager"></a>Azure Stack Resource Manager

Azure Stack PowerShell fournit un ensemble d’applets de commande qui utilisent des versions précédentes d’Azure Resource Manager. Ces applets de commande sont compatibles avec les fournisseurs de ressources dans Azure Stack. Chaque fournisseur de ressources dans Azure Stack utilise une version antérieure du fournisseur trouvé dans Azure global. Pour vous aider à coordonner la version de chaque fournisseur pris en charge par Azure Stack, vous pouvez utiliser des profils d’API. Azure Stack PowerShell utilise PowerShell 5.1 et est disponible uniquement sur Windows. Pour plus d’informations, consultez [Gérer les profils de version des API dans Azure Stack](azure-stack-version-profiles.md).

### <a name="azure-stack-administrator"></a>Azure Stack Administrator

Azure Stack expose un ensemble de fournisseurs de ressources à l’opérateur cloud pour que celui-ci puisse installer et gérer Azure Stack. Dans Azure global, cette interaction est tirée de l’utilisateur et gérée en arrière-plan comme faisant partie d’Azure. Azure Stack, toutefois, permet aux entreprises de prendre en charge un cloud privé. Pour effectuer ces tâches, l’opérateur interagit avec les API d’administration Azure Stack. Pour plus d’informations, consultez [Installer PowerShell pour Azure Stack](../operator/azure-stack-powershell-install.md).

### <a name="azure-stack-privileged-endpoint"></a>Point de terminaison privilégié Azure Stack

Pour les activités d’opérateur dans Azure Stack, telles que tester l’installation et accéder aux journaux, les opérateurs peuvent interagir avec le point de terminaison privilégié. Ce point de terminaison est une console PowerShell distante, préconfigurée, qui fournit aux opérateurs suffisamment d’accès pour effectuer des tâches particulières. Le point de terminaison s’appuie sur PowerShell JEA (Just Enough Administration) pour exposer un ensemble limité d’applets de commande. Pour plus d’informations, consultez [Utiliser le point de terminaison privilégié dans Azure Stack](../operator/azure-stack-privileged-endpoint.md).

### <a name="azure-stack-tools"></a>Outils Azure Stack

Azure Stack met à disposition des scripts ainsi que d’autres applets de commande dans un dépôt GitHub, *AzureStack-Tools*. Les outils AzureStack héberge des modules PowerShell permettant de gérer et de déployer des ressources sur Azure Stack. Si vous prévoyez d’établir la connectivité VPN, vous pouvez télécharger ces modules PowerShell avec le kit de développement Azure Stack ou sur un client externe Windows. Pour plus d’informations, accédez à la page [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools).

## <a name="work-with-powershell-in-azure-stack"></a>Utiliser PowerShell dans Azure Stack

PowerShell fournit un moyen programmatique d’interagir avec Azure Resource Manager. Vous pouvez utiliser une invite de commandes interactive ou, si vous automatisez des tâches, écrire des scripts.

Si vous utilisez beaucoup Azure Stack PowerShell, vous vous surprendrez à installer et réinstaller les modules. Si vous travaillez avec Azure global en même temps, cette routine peut s’avérer compliquée, car vous devez désinstaller et réinstaller vos modules en fonction de votre cible. 

Vous pouvez utiliser des conteneurs Docker pour isoler chaque version de PowerShell sur votre machine locale. Pour utiliser des conteneurs Docker afin de pouvoir passer d’un ensemble de modules PowerShell à un autre, consultez [Utiliser Docker pour exécuter PowerShell](azure-stack-powershell-user-docker.md).


## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [Profils d’API pour PowerShell](azure-stack-version-profiles.md) dans Azure Stack.
- Installez [Azure Stack PowerShell](../operator/azure-stack-powershell-install.md).
- Découvrez comment créer des [Modèles Azure Resource Manager](azure-stack-develop-templates.md) pour la cohérence du cloud.
