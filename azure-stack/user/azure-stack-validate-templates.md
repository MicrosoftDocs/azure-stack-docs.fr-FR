---
title: Utiliser l’outil de validation de modèles dans Azure Stack Hub
description: Vérifiez vos modèles pour le déploiement vers Azure Stack Hub avec l’outil de validation de modèles.
author: sethmanheim
ms.topic: article
ms.date: 12/2/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: b8e5b14542773372751fe76d7c6299961e0da86d
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525895"
---
# <a name="use-the-template-validation-tool-in-azure-stack-hub"></a>Utiliser l’outil de validation de modèles dans Azure Stack Hub

Utilisez l’outil de validation de modèles pour vérifier si vos [modèles](azure-stack-arm-templates.md) Azure Resource Manager sont prêts pour un déploiement vers Azure Stack Hub. L’outil de validation de modèles est disponible dans le dépôt GitHub des outils Azure Stack Hub. Pour téléchargez les outils Azure Stack Hub, suivez la procédure décrite dans [Télécharger des outils à partir de GitHub](../operator/azure-stack-powershell-download.md).

## <a name="overview"></a>Vue d’ensemble

Pour valider un modèle, vous devez d’abord générer un fichier des fonctionnalités du cloud, puis exécuter l’outil de validation. Utilisez les modules PowerShell suivants à partir des outils Azure Stack Hub :

- Dans le dossier **CloudCapabilities** : **Az.CloudCapabilities.psm1** crée un fichier JSON des fonctionnalités du cloud représentant les services et versions présentes dans un cloud Azure Stack Hub.
- Dans le dossier **TemplateValidator** : **Az.TemplateValidator.psm1** utilise un fichier JSON des fonctionnalités du cloud pour tester des modèles en vue d’un déploiement dans Azure Stack Hub.

## <a name="build-the-cloud-capabilities-file"></a>Générer le fichier des fonctionnalités du cloud

Avant d’utiliser le validateur de modèle, exécutez le module PowerShell **Az.CloudCapabilities** pour générer un fichier JSON.

> [!NOTE]
> Si vous mettez à jour votre système intégré ou que vous ajoutez de nouveaux services ou de nouvelles extensions virtuelles, vous devez exécuter à nouveau ce module.


### <a name="az-modules"></a>[Modules Az](#tab/az1)

1. Vérifiez que vous disposez d’une connectivité à Azure Stack Hub. Cette procédure peut être effectuée à partir de l’hôte du Kit de développement Azure Stack, ou vous pouvez utiliser un [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) pour vous connecter à partir de votre station de travail.
2. Importez le module PowerShell **Az.CloudCapabilities** :

    ```powershell
    Import-Module .\CloudCapabilities\Az.CloudCapabilities.psm1
    ```

3. Utilisez l’applet de commande **Get-CloudCapabilities** pour récupérer des versions de service et créer un fichier JSON de fonctionnalités cloud. Si vous ne spécifiez pas `-OutputPath`, le fichier **AzureCloudCapabilities.json** est créé dans le répertoire actif. Utilisez votre emplacement Azure réel :

```powershell
Get-AzCloudCapability -Location <your location> -Verbose
```

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm1)

1. Vérifiez que vous disposez d’une connectivité à Azure Stack Hub. Cette procédure peut être effectuée à partir de l’hôte du Kit de développement Azure Stack, ou vous pouvez utiliser un [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) pour vous connecter à partir de votre station de travail.
2. Importez le module PowerShell **Az.CloudCapabilities** :

    ```powershell
    Import-Module .\CloudCapabilities\Az.CloudCapabilities.psm1
    ```

3. Utilisez l’applet de commande **Get-CloudCapabilities** pour récupérer des versions de service et créer un fichier JSON de fonctionnalités cloud. Si vous ne spécifiez pas `-OutputPath`, le fichier **AzureCloudCapabilities.json** est créé dans le répertoire actif. Utilisez votre emplacement Azure réel :

```powershell
Get-AzureRMCloudCapability -Location <your location> -Verbose
```

---

## <a name="validate-templates"></a>Valider des modèles

Utilisez cette procédure pour valider des modèles à l’aide du module PowerShell **Az.TemplateValidator**. Vous pouvez utiliser vos propres modèles ou les [modèles de démarrage rapide Azure Stack Hub](https://github.com/Azure/AzureStack-QuickStart-Templates).

### <a name="az-modules"></a>[Modules Az](#tab/az2)

1. Importez le module PowerShell **Az.TemplateValidator.psm1** :

    ```powershell
    cd "c:\AzureStack-Tools-az\TemplateValidator"
    Import-Module .\Az.TemplateValidator.psm1
    ```

2. Exécutez le validateur de modèle :

```powershell
Test-AzTemplate -TemplatePath <path to template.json or template folder> `
-CapabilitiesPath <path to cloudcapabilities.json> `
-Verbose
```

### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm2)

1. Importez le module PowerShell **Az.TemplateValidator.psm1** :

    ```powershell
    cd "c:\AzureStack-Tools-az\TemplateValidator"
    Import-Module .\Az.TemplateValidator.psm1
    ```

2. Exécutez le validateur de modèle :

```powershell
Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
-CapabilitiesPath <path to cloudcapabilities.json> `
-Verbose
```

---

Les avertissements ou erreurs de validation de modèle sont affichés dans la console PowerShell et consignés dans un fichier HTML situé dans le répertoire source. La capture d’écran suivante montre un exemple de rapport de validation :

![Rapport de validation de modèle](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Paramètres

La cmdlet du validateur de modèle prend en charge les paramètres suivants.

| Paramètre | Description | Obligatoire |
| ----- | -----| ----- |
| `TemplatePath` | Spécifie le chemin pour rechercher des modèles Azure Resource Manager de manière récursive. | Oui |
| `TemplatePattern` | Spécifie le nom des fichiers de modèle à faire correspondre | Non |
| `CapabilitiesPath` | Spécifie le chemin d’accès du fichier JSON de fonctionnalités cloud. | Oui |
| `IncludeComputeCapabilities` | Inclut l’évaluation de ressources IaaS telles que des tailles de machine virtuelle et des extensions de machine virtuelle. | Non |
| `IncludeStorageCapabilities` | Inclut l’évaluation de ressources de stockage, par exemple des types de références (SKU). | Non |
| `Report` | Spécifie le nom du rapport HTML généré. | Non |
| `Verbose` | Journalise les erreurs et les avertissements dans la console. | Non|

### <a name="examples"></a>Exemples

Cet exemple valide tous les [modèles de démarrage rapide Azure Stack Hub](https://github.com/Azure/AzureStack-QuickStart-Templates) téléchargés dans le stockage local. L’exemple valide également les tailles et extensions de machines virtuelles par rapport aux fonctionnalités de l’ASDK.

### <a name="az-modules"></a>[Modules Az](#tab/az3)

```powershell
test-AzTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```
### <a name="azurerm-modules"></a>[Modules AzureRM](#tab/azurerm3)

```powershell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```
---

## <a name="next-steps"></a>Étapes suivantes

- [Déployer des modèles sur Azure Stack Hub](azure-stack-arm-templates.md)
- [Développer des modèles pour Azure Stack Hub](azure-stack-develop-templates.md)
