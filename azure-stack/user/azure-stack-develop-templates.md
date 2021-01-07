---
title: Développer des modèles pour Azure Stack Hub
description: Apprenez à développer des modèles Azure Resource Manager pour la portabilité des applications entre Azure et Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 2d449adc59be9051188c1d942ae54d6a0761d28e
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874215"
---
# <a name="develop-templates-for-azure-stack-hub-with-azure-resource-manager"></a>Développer des modèles pour Azure Stack Hub avec Azure Resource Manager

Lorsque vous développez votre application, il est important de garantir la portabilité des modèles entre Azure et Azure Stack Hub. Cet article fournit des informations sur le développement des [modèles Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf). Avec ces modèles, vous pouvez créer un prototype de votre d'application et en tester le déploiement dans Azure sans environnement Azure Stack Hub.

## <a name="resource-provider-availability"></a>Disponibilité du fournisseur de ressources

Le modèle que vous envisagez de déployer doit seulement utiliser les services Microsoft Azure déjà disponibles, ou en préversion, dans Azure Stack Hub.

## <a name="public-namespaces"></a>Espaces de noms publics

Comme Azure Stack Hub est hébergé dans votre centre de données, il dispose d'espaces de noms de point de terminaison de service différents de ceux du cloud public Azure. Par conséquent, les points de terminaison publics codés en dur dans les modèles Azure Resource Manager échouent lorsque vous essayez de les déployer sur Azure Stack Hub. Vous pouvez générer dynamiquement des points de terminaison de service à l’aide des fonctions de `reference` et `concatenate` pour récupérer des valeurs à partir du fournisseur de ressources lors du déploiement. Par exemple, au lieu de coder en dur `blob.core.windows.net` dans votre modèle, récupérez l’objet [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-vm-windows-create/azuredeploy.json#L175) pour définir dynamiquement le point de terminaison *osDisk.URI* :

```json
"osDisk": {"name": "osdisk","vhd": {"uri":
"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
 '/',variables('OSDiskName'),'.vhd')]"}}
```

## <a name="api-versioning"></a>Contrôle de version d’API

Les versions du service Azure peuvent différer entre Azure et Azure Stack Hub. Chaque ressource requiert l’attribut **apiVersion**, qui définit les fonctionnalités proposées. Pour assurer la compatibilité des versions d'API dans Azure Stack Hub, les versions suivantes sont valides pour chaque fournisseur de ressources :

| Fournisseur de ressources | apiVersion |
| --- | --- |
| Calcul |**2015-06-15** |
| Réseau |**2015-06-15**, **2015-05-01-preview** |
| Stockage |**2016-01-01**, **2015-06-15**, **2015-05-01-preview** |
| KeyVault | **2015-06-01** |
| App Service |**2015-08-01** |

## <a name="template-functions"></a>Fonctions des modèles de gestionnaire des ressources Azure

Les [fonctions](/azure/azure-resource-manager/resource-group-template-functions) Azure Resource Manager offrent les fonctionnalités nécessaires pour créer des modèles dynamiques. Par exemple, vous pouvez utiliser des fonctions pour des tâches telles que :

* La concaténation ou la troncation de chaînes.
* Le référencement de valeurs d’autres ressources.
* L’itération sur les ressources pour déployer plusieurs instances.

Ces fonctions ne sont pas disponibles dans Azure Stack Hub :

* Ignorer
* Take

## <a name="resource-location"></a>Emplacement de la ressource

Les modèles Azure Resource Manager utilisent un attribut `location` pour placer les ressources pendant le déploiement. Dans Azure, les emplacements font référence à une région, par exemple USA Ouest ou Amérique du Sud. Dans Azure Stack Hub, les emplacements sont différents, car Azure Stack Hub se trouve dans votre centre de données. Pour que les modèles soient transférables entre Azure et Azure Stack Hub, vous devez référencer l'emplacement du groupe de ressources lorsque vous déployez des ressources individuelles. Vous pouvez pour cela utiliser `[resourceGroup().Location]` afin de veiller à ce que toutes les ressources héritent de l’emplacement du groupe de ressources. Le code suivant est un exemple d’utilisation de cette fonction lors du déploiement d’un compte de stockage :

```json
"resources": [
{
  "name": "[variables('storageAccountName')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "[variables('apiVersionStorage')]",
  "location": "[resourceGroup().location]",
  "comments": "This storage account is used to store the VM disks",
  "properties": {
  "accountType": "Standard_GRS"
  }
}
]
```

## <a name="next-steps"></a>Étapes suivantes

* [Déployer des modèles avec PowerShell](azure-stack-deploy-template-powershell.md)
* [Déployer des modèles avec l’interface de ligne de commande Azure](azure-stack-deploy-template-command-line.md)
* [Déployer des modèles avec Visual Studio](azure-stack-deploy-template-visual-studio.md)
