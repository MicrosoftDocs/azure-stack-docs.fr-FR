---
title: Utiliser des modèles Resource Manager pour les disques managés dans Azure Stack Hub
description: Apprenez-en davantage sur les différences entre les disques managés et non managés lors de l’utilisation de modèles Azure Resource Manager.
author: sethmanheim
ms.author: sethm
ms.date: 8/25/2020
ms.topic: conceptual
ms.reviewer: wellsluo
ms.lastreviewed: 8/25/2020
ms.openlocfilehash: 2d2f274fffd1a07857b79255587b659003a3a1ce
ms.sourcegitcommit: 65a115d1499b5fe16b6fe1c31cce43be21d05ef8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88823269"
---
# <a name="use-vm-managed-disks-templates"></a>Modèles de disques managés de machine virtuelle

Cet article décrit les différences entre les disques managés et non managés lorsque vous utilisez des modèles Azure Resource Manager pour approvisionner des machines virtuelles dans Azure Stack Hub. Ces exemples vous aident à convertir des modèles utilisant des disques non managés en modèles utilisant des disques managés.

## <a name="unmanaged-disks-template-formatting"></a>Mise en forme de modèle de disques non managés

Pour commencer, nous nous intéressons à la façon dont les disques non managés sont déployés. Lorsque vous créez des disques non managés, vous avez besoin d’un compte de stockage pour héberger les fichiers de disque dur virtuel. Vous pouvez créer un compte de stockage ou en utiliser un existant. Créez une ressource de compte de stockage dans le bloc de ressources du modèle comme suit :

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2017-10-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage"
}
```

Dans l’objet de machine virtuelle, ajoutez une dépendance sur le compte de stockage afin de garantir qu’il est créé avant la machine virtuelle. Dans la section `storageProfile`, indiquez l’URI complet de l’emplacement du disque dur virtuel, qui fait référence au compte de stockage et qui est nécessaire pour le disque du système d’exploitation et les disques de données. L’exemple suivant crée un disque de système d’exploitation à partir d’une image et un disque de données vide avec une taille de 1023 Go :

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2017-12-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Mise en forme de modèle de disques managés

Les disques managés Azure deviennent des ressources de niveau supérieur qui ne nécessitent plus que l’utilisateur crée un compte de stockage. Les disques managés ont été introduits pour la première fois dans la version `2017-03-30` de l’API. Les sections suivantes abordent les paramètres par défaut, et décrivent comment personnaliser davantage vos disques.

### <a name="default-managed-disk-settings"></a>Paramètres de disque géré par défaut

Pour créer une machine virtuelle avec des disques managés, vous n’avez plus besoin créer la ressource de compte de stockage. Dans l’exemple de modèle ci-dessous, il faut noter quelques différences par rapport aux exemples de disques non managés précédents :

- La propriété `apiVersion` est une version pour un type de ressource « virtualMachines » qui prend en charge les disques managés.
- `osDisk` et `dataDisks` ne font plus référence à un URI spécifique pour le disque dur virtuel.
- Lors d’un déploiement sans spécification de propriétés supplémentaires, le disque utilise un type de stockage dont le choix dépend de la taille de la machine virtuelle. Par exemple, si vous utilisez une taille de machine virtuelle qui prend en charge le stockage Premium (taille dont le nom contient la lettre « s », par exemple Standard_DS2_v2), les disques Premium sont configurés par défaut. Vous pouvez modifier cela à l’aide du paramètre SKU du disque pour spécifier un type de stockage.
- Si aucun nom n’est spécifié pour le disque, il prend le format `<VMName>_OsDisk_1_<randomstring>` pour le disque du système d’exploitation et `<VMName>_disk<#>_<randomstring>` pour chaque disque de données.
  - Si une machine virtuelle est créée à partir d’une image personnalisée, les paramètres par défaut pour le type de compte de stockage et le nom de disque sont récupérés dans les propriétés de disque définies dans la ressource d’image personnalisée. Elles peuvent être remplacées en spécifiant des valeurs pour celles-ci dans le modèle.
- Par défaut, la mise en cache du disque est **Lecture/Écriture** pour le disque du système d’exploitation et **Aucune** pour les disques de données.
- Dans l’exemple suivant, il existe toujours une dépendance de compte de stockage, bien que celle-ci concerne uniquement le stockage des diagnostics et ne soit pas nécessaire pour le stockage sur disque :

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2017-12-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="use-a-top-level-managed-disk-resource"></a>Utiliser une ressource de disque managé de niveau supérieur

Au lieu de spécifier la configuration du disque dans l’objet de machine virtuelle, vous pouvez créer une ressource de disque de niveau supérieur et l’attacher dans le cadre de la création de la machine virtuelle. Veillez à utiliser `2017-03-30` comme version de l’API de ressource `disks`. Par exemple, vous pouvez créer une ressource de disque comme suit pour l’utiliser comme disque de données. Dans cet exemple, `vmName` fait partie du nom du disque :

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2017-03-30",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

Dans l’objet de machine virtuelle, faites référence à l’objet de disque devant être attaché. Spécifier l’ID de ressource du disque managé créé dans la propriété `managedDisk` permet d’attacher le disque lors de la création de la machine virtuelle. La propriété `apiVersion` de la ressource de la machine virtuelle est définie sur `2017-12-01`. Une dépendance sur la ressource de disque est ajoutée pour s’assurer que celle-ci est correctement créée avant la création de la machine virtuelle :

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2017-12-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Créer des groupes à haute disponibilité managés avec des machines virtuelles à l’aide de disques managés

Pour créer des groupes à haute disponibilité managés avec des machines virtuelles à l’aide de disques managés, ajoutez l’objet `sku` à la ressource de groupe à haute disponibilité et définissez la propriété `name` sur `Aligned`. Cette propriété garantit que les disques de chaque machine virtuelle sont suffisamment isolés les uns des autres pour éviter les points uniques de défaillance. Notez également que la propriété `apiVersion` pour la ressource de groupe à haute disponibilité est définie sur `2017-12-01` :

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2017-12-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 1,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

<!--
* For full templates that use managed disks visit the following Azure Quickstart Repo links.
    * [Windows VM with managed disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Linux VM with managed disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
-->

- Pour en savoir plus sur les disques managés, consultez [Disques managés Azure Stack Hub](azure-stack-managed-disk-considerations.md).
- Consultez la documentation de référence sur le modèle pour les ressources de machine virtuelle en consultant le document de [référence sur le modèle Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/2017-12-01/virtualmachines).
- Consultez la documentation de référence sur le modèle pour les ressources de disque en consultant le document [de référence sur le modèle Microsoft.Compute/disks](/azure/templates/microsoft.compute/2017-03-30/disks).
