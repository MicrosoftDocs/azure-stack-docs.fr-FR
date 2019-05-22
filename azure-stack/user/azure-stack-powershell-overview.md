---
title: PowerShell sur Azure Stack | Microsoft Docs
description: PowerShell sur Azure Stack dispose d’un nombre de modules et de contextes.
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
ms.openlocfilehash: b28a90ceca59e5cd072018b217d81d101c1b0853
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490038"
---
# <a name="get-started-with-powershell-on-azure-stack"></a>Bien démarrer avec PowerShell sur Azure Stack

PowerShell est conçu pour gérer et administrer des ressources en ligne de commande. Vous pouvez utiliser PowerShell lorsque vous voulez créer des outils automatisés qui utilisent le modèle Azure Resource Manager. Un module PowerShell peut être défini comme un ensemble de fonctions PowerShell regroupées, afin de gérer tous les aspects d’une zone particulière. Pour travailler avec Azure Stack, vous devez jongler avec différents ensembles d’applets de commande PowerShell.

Cet article vous aide à vous repérer dans les différents modules PowerShell utilisés sur Azure Stack. Il existe quatre ensembles d’API avec lesquels vous pouvez interagir lorsque vous utilisez PowerShell sur Azure Stack.

| API | Référence sur PowerShell | Référence REST |
| --- | --- | --- |
| 1. Global Azure Resource Manager | [Modules Azure PowerShell](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [Navigateur d’API REST](https://docs.microsoft.com/rest/api/) |
| 2. Azure Stack Resource Manager | [Gérer les profils de version des API dans Azure Stack](azure-stack-version-profiles.md) | [Gérer les profils de version des API dans Azure Stack](azure-stack-version-profiles.md) |
| 3. Points de terminaison Azure Stack Administrator | [Module Administrateur Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [Navigateur d’API REST - Azure Stack](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| 4.  Point de terminaison privilégié Azure Stack | [Utilisation du point de terminaison privilégié dans Azure Stack](../operator/azure-stack-privileged-endpoint.md) | |

Chaque interface contacte les fournisseurs de ressources dans Azure global ou Azure Stack. Les fournisseurs de ressources activent les fonctionnalités Azure. Par exemple, le fournisseur de ressources Azure Compute vous donne un accès programmatique à la création et à la gestion de machines virtuelles et de leurs ressources prises en charge.

Les fournisseurs de ressources offrent des fonctionnalités, mais aussi des contrôles pour la gestion et la configuration de la ressource. Vous pouvez accéder par programmation aux fournisseurs de ressources avec Azure Resource Manager et, en retour, l’interface fournit une surface pour PowerShell, Azure CLI et vos propres clients REST.

## <a name="where-to-find-azure-stack-powershell"></a>Où trouver Azure Stack PowerShell

Le diagramme de blocs suivant illustre la relation entre chaque ensemble de modules PowerShell. À partir de votre machine, vous pouvez charger les modules PowerShell et gérer Azure global et Azure Stack.

![Azure Stack PowerShell](media/azure-stack-powershell-overview/Azure-Stack-PowerShell.png)

### <a name="global-azure"></a>Azure global

Azure PowerShell fournit un ensemble d’applets de commande qui utilisent la version actuelle du modèle Azure Resource Manager pour la gestion de vos ressources Azure. Azure PowerShell utilise .NET Standard, permettant ainsi une disponibilité pour Windows, macOS et Linux. Azure PowerShell est également disponible sur Azure Cloud Shell. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="azure-stack-resource-manager"></a>Azure Stack Resource Manager

Azure Stack PowerShell fournit un ensemble d’applets de commande qui utilisent des versions antérieures d’Azure Resource Manager, compatibles avec les fournisseurs de ressources dans Azure Stack. Chaque fournisseur de ressources dans Azure Stack utilise une version antérieure du fournisseur trouvé dans Azure global. Pour vous aider à coordonner la version de chaque fournisseur pris en charge par Azure Stack, vous pouvez utiliser des profils d’API. Azure Stack PowerShell utilise PowerShell 5.1 et est uniquement disponible sur Windows. Pour plus d’informations, consultez [Gérer les profils de version des API dans Azure Stack](azure-stack-version-profiles.md).

### <a name="azure-stack-administrator"></a>Azure Stack Administrator

Azure Stack expose un ensemble de fournisseurs de ressources à l’opérateur cloud, afin d’installer et de gérer Azure Stack. Dans Azure global, cette interaction est tirée de l’utilisateur et gérée en arrière-plan comme faisant partie d’Azure. Azure Stack, toutefois, permet aux entreprises de prendre en charge un cloud privé. Pour effectuer ces tâches, l’opérateur interagit avec les API Administrateur Azure Stack. Pour plus d’informations, consultez [Installer PowerShell pour Azure Stack](../operator/azure-stack-powershell-install.md).

### <a name="azure-stack-privileged-endpoint"></a>Point de terminaison privilégié Azure Stack

Pour les activités d’opérateur sur Azure Stack, telles que tester l’installation et accéder aux journaux, les opérateurs peuvent interagir avec le point de terminaison privilégié. Ce point de terminaison est une console PowerShell distante, préconfigurée, qui fournit aux opérateurs suffisamment d’accès pour vous aider à effectuer des tâches particulières. Le point de terminaison s’appuie sur PowerShell JEA (Just Enough Administration) pour exposer un ensemble limité d’applets de commande. Pour plus d’informations, consultez [Utilisation du point de terminaison privilégié dans Azure Stack](../operator/azure-stack-privileged-endpoint.md).

### <a name="azurestack-tools"></a>Outils AzureStack

Par ailleurs, Azure Stack fournit des scripts ainsi que d’autres applets de commande disponibles dans un dépôt GitHub : les outils Azure Stack. Les outils AzureStack héberge des modules PowerShell permettant de gérer et de déployer des ressources sur Azure Stack. Si vous prévoyez d’établir la connectivité VPN, vous pouvez télécharger ces modules PowerShell avec le Kit de développement Azure Stack ou sur un client externe Windows. Pour plus d’informations, consultez [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools).

## <a name="working-with-powershell-on-azure-stack"></a>Utilisation de PowerShell sur Azure Stack

PowerShell fournit un moyen programmatique d’interagir avec Azure Resource Manager. Vous pouvez utiliser une invite de commandes interactive, ou écrire des scripts si vous automatisez des tâches.

Si vous utilisez beaucoup Azure Stack PowerShell, vous vous surprendrez à installer et réinstaller les modules. Ce sont des tâches qui peuvent s’avérer compliquées si vous travaillez avec Azure global en même temps, car vous devez désinstaller et réinstaller vos modules en fonction de votre cible. Vous pouvez utiliser des conteneurs Docker pour isoler chaque version de PowerShell sur votre machine locale. [Utiliser Docker pour exécuter PowerShell](azure-stack-powershell-user-docker.md) revient à utiliser des conteneurs Docker, pour que vous puissiez passer d’un ensemble de modules PowerShell à un autre.


## <a name="next-steps"></a>Étapes suivantes

- Découvrez les [Profils d’API pour PowerShell](azure-stack-version-profiles.md) sur Azure Stack.
- [Installer Azure Stack PowerShell](../operator/azure-stack-powershell-install.md)
- Découvrez comment créer des [Modèles Azure Resource Manager](azure-stack-develop-templates.md) pour la cohérence du cloud.