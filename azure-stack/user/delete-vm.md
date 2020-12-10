---
title: Supprimer une machine virtuelle avec des dépendances sur Azure Stack Hub
description: Comment supprimer une machine virtuelle avec des dépendances sur Azure Stack Hub
author: mattbriggs
ms.topic: how-to
ms.date: 12/7/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 12/7/2020
ms.openlocfilehash: aaedac367ab9bd12a75e09917acfa6b02dc4930e
ms.sourcegitcommit: 62eb5964a824adf7faee58c1636b17fedf4347e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778187"
---
# <a name="how-to-delete-a-vm-virtual-machine-with-dependencies-on-azure-stack-hub"></a>Comment supprimer une machine virtuelle avec des dépendances sur Azure Stack Hub

Dans cet article, vous trouverez la procédure de suppression d’une machine virtuelle et de ses dépendances dans Azure Stack Hub.

Si vous supprimez une machine virtuelle d’Azure Stack Hub, les dépendances de composants que sont les disques de données, les interfaces de réseau virtuel et les conteneurs de diagnostic, demeurent dans le groupe de ressources. Ces éléments ne sont pas supprimés automatiquement, en même temps que le disque du système d’exploitation.

## <a name="delete-a-vm-overview"></a>Vue d’ensemble de la suppression d’une machine virtuelle

Lorsque vous créez une machine virtuelle, vous créez généralement un nouveau groupe de ressources et placez toutes les dépendances dans ce groupe de ressources. Lorsque vous souhaitez supprimer la machine virtuelle et toutes ses dépendances, vous pouvez supprimer le groupe de ressources. Azure Resource Manager gérera les dépendances pour les supprimer correctement. Il arrive parfois que vous ne puissiez pas supprimer le groupe de ressources pour supprimer la machine virtuelle. Par exemple, la machine virtuelle peut contenir des ressources qui ne sont pas des dépendances de cette machine virtuelle que vous souhaiteriez supprimer.

## <a name="delete-a-vm-with-dependencies"></a>Supprimer une machine virtuelle avec des dépendances

### <a name="with-the-portal"></a>[À l’aide du portail](#tab/portal)

Si vous ne pouvez pas supprimer le groupe de ressources, soit parce que les dépendances ne se trouvent pas dans le même groupe de ressources, soit parce qu’il existe d’autres ressources, suivez les étapes ci-dessous :

1. Ouvrez le portail utilisateur Azure Stack.

2. Sélectionnez **Machines virtuelles**. Recherchez votre machine virtuelle, puis sélectionnez-la pour ouvrir le panneau qui lui correspond.  
![Supprimer la machine virtuelle avec des dépendances](./media/delete-vm/azure-stack-hub-delete-vm-portal.png)  

3. Notez le groupe de ressources qui contient la machine virtuelle et les dépendances associées.

4. Sélectionnez **Réseau** et prenez note de l’interface réseau.

5. Sélectionnez **Disques** et notez le disque du système d’exploitation et les disques de données.

6. Revenez au panneau **Machine virtuelle** et sélectionnez **Supprimer**.

7. Tapez `yes` pour confirmer la suppression, puis sélectionnez **Supprimer**.

7. Sélectionnez **Groupes de ressources**, puis sélectionnez le groupe de ressources.

8. Supprimez les dépendances en les sélectionnant manuellement, puis en sélectionnant **Supprimer**.
    1. Tapez `yes` pour confirmer la suppression, puis sélectionnez **Supprimer**.
    2. Attendez que la ressource soit complètement supprimée.
    3. Vous pouvez ensuite supprimer la dépendance suivante.

### <a name="with-powershell"></a>À l’aide de [PowerShell](#tab/ps-az)

Si vous ne pouvez pas supprimer le groupe de ressources, soit parce que les dépendances ne se trouvent pas dans le même groupe de ressources, soit parce qu’il existe d’autres ressources, suivez ces étapes.

Connectez-vous à votre environnement Azure Stack Hub, puis mettez à jour les variables suivantes avec le nom de votre machine virtuelle et votre groupe de ressources. Pour obtenir des instructions sur la connexion à votre session PowerShell sur Azure Stack Hub, consultez [Se connecter à Azure Stack Hub en tant qu’utilisateur avec PowerShell](azure-stack-powershell-configure-user.md).

```powershell
$machineName = 'VM_TO_DELETE'
$resGroupName = 'RESOURCE_GROUP'
$machine = Get-AzVM -Name $machineName -ResourceGroupName $resGroupName
```

Récupérez les informations de machine virtuelle et le nom des dépendances. Dans la même session, exécutez les applets de commande suivantes :

```powershell
 $azResParams = @{
 'ResourceName' = $machineName
 'ResourceType' = 'Microsoft.Compute/virtualMachines'
     'ResourceGroupName' = $resGroupName
 }
 $vmRes = Get-AzResource @azResParams
 $vmId = $vmRes.Properties.VmId
```

Supprimez le conteneur de stockage des diagnostics de démarrage. Si la longueur du nom de votre machine est inférieure à 9 caractères, vous devez modifier l’index en spécifiant la longueur de la chaîne dans la sous-chaîne au moment de la création de la variable `$diagContainer`. 

Dans la même session, exécutez les applets de commande suivantes :

```powershell
$container = [regex]::match($machine.DiagnosticsProfile.bootDiagnostics.storageUri, '^http[s]?://(.+?)\.').groups[1].value
$diagContainer = ('bootdiagnostics-{0}-{1}' -f $machine.Name.ToLower().Substring(0, 9), $vmId)
$containerRg = (Get-AzStorageAccount | where { $_.StorageAccountName -eq $container }).ResourceGroupName
$storeParams = @{
    'ResourceGroupName' = $containerRg
    'Name' = $container }
Get-AzStorageAccount @storeParams | Get-AzureStorageContainer | where { $_.Name-eq $diagContainer } | Remove-AzureStorageContainer -Force
```

Supprimez l’interface de réseau virtuel.

```powershell
$machine | Remove-AzNetworkInterface -Force
```

Supprimez le disque du système d’exploitation.

```powershell
$osVhdUri = $machine.StorageProfile.OSDisk.Vhd.Uri
$osDiskConName = $osVhdUri.Split('/')[-2]
$osDiskStorageAcct = Get-AzStorageAccount | where { $_.StorageAccountName -eq $osVhdUri.Split('/')[2].Split('.')[0] }
$osDiskStorageAcct | Remove-AzureStorageBlob -Container $osDiskConName -Blob $osVhdUri.Split('/')[-1]
```

Supprimez les disques de données attachés à votre machine virtuelle.

```powershell
if ($machine.DataDiskNames.Count -gt 0)
 {
    Write-Verbose -Message 'Deleting disks...'
        foreach ($uri in $machine.StorageProfile.DataDisks.Vhd.Uri )
        {
            $dataDiskStorageAcct = Get-AzStorageAccount -Name $uri.Split('/')[2].Split('.')[0]
             $dataDiskStorageAcct | Remove-AzureStorageBlob -Container $uri.Split('/')[-2] -Blob $uri.Split('/')[-1] -ea Ignore
        }
 }
```

Pour terminer, supprimez la machine virtuelle. L’applet de commande prend un certain temps pour s’exécuter. Vous pouvez auditer les composants attachés à la machine virtuelle en examinant l’objet de machine virtuelle dans PowerShell. Pour examiner l’objet, il vous suffit de vous référer à la variable qui contient l’objet de machine virtuelle. Tapez `$machine`.

Pour supprimer la machine virtuelle, dans la même session, exécutez les applets de commande suivantes :

```powershell
$machine | Remove-AzVM -Force
```
### <a name="with-powershell"></a>À l’aide de [PowerShell](#tab/ps-azureRM)

Si vous ne pouvez pas supprimer le groupe de ressources, soit parce que les dépendances ne se trouvent pas dans le même groupe de ressources, soit parce qu’il existe d’autres ressources, suivez ces étapes.

Connectez-vous à votre environnement Azure Stack Hub, puis mettez à jour les variables suivantes avec le nom de votre machine virtuelle et votre groupe de ressources. Pour obtenir des instructions sur la connexion à votre session PowerShell sur Azure Stack Hub, consultez [Se connecter à Azure Stack Hub en tant qu’utilisateur avec PowerShell](azure-stack-powershell-configure-user.md).

```powershell
$machineName = 'VM_TO_DELETE'
$resGroupName = 'RESOURCE_GROUP'
$machine = Get-AzureRmVM -Name $machineName -ResourceGroupName $resGroupName
```

Récupérez les informations de machine virtuelle et le nom des dépendances. Dans la même session, exécutez les applets de commande suivantes :

```powershell
 $azResParams = @{
 'ResourceName' = $machineName
 'ResourceType' = 'Microsoft.Compute/virtualMachines'
     'ResourceGroupName' = $resGroupName
 }
 $vmRes = Get-AzureRmResource @azResParams
 $vmId = $vmRes.Properties.VmId
```

Supprimez le conteneur de stockage des diagnostics de démarrage. Si la longueur du nom de votre machine est inférieure à 9 caractères, vous devez modifier l’index en spécifiant la longueur de la chaîne dans la sous-chaîne au moment de la création de la variable `$diagContainer`. 

Dans la même session, exécutez les applets de commande suivantes :

```powershell
$container = [regex]::match($machine.DiagnosticsProfile.bootDiagnostics.storageUri, '^http[s]?://(.+?)\.').groups[1].value
$diagContainer = ('bootdiagnostics-{0}-{1}' -f $machine.Name.ToLower().Substring(0, 9), $vmId)
$containerRg = (Get-AzureRmStorageAccount | where { $_.StorageAccountName -eq $container }).ResourceGroupName
$storeParams = @{
    'ResourceGroupName' = $containerRg
    'Name' = $container }
Get-AzureRmStorageAccount @storeParams | Get-AzureStorageContainer | where { $_.Name-eq $diagContainer } | Remove-AzureStorageContainer -Force
```

Supprimez l’interface de réseau virtuel.

```powershell
$machine | Remove-AzureRmNetworkInterface -Force
```

Supprimez le disque du système d’exploitation.

```powershell
$osVhdUri = $machine.StorageProfile.OSDisk.Vhd.Uri
$osDiskConName = $osVhdUri.Split('/')[-2]
$osDiskStorageAcct = Get-AzureRmStorageAccount | where { $_.StorageAccountName -eq $osVhdUri.Split('/')[2].Split('.')[0] }
$osDiskStorageAcct | Remove-AzureStorageBlob -Container $osDiskConName -Blob $osVhdUri.Split('/')[-1]
```

Supprimez les disques de données attachés à votre machine virtuelle.

```powershell
if ($machine.DataDiskNames.Count -gt 0)
 {
    Write-Verbose -Message 'Deleting disks...'
        foreach ($uri in $machine.StorageProfile.DataDisks.Vhd.Uri )
        {
            $dataDiskStorageAcct = Get-AzureRmStorageAccount -Name $uri.Split('/')[2].Split('.')[0]
             $dataDiskStorageAcct | Remove-AzureStorageBlob -Container $uri.Split('/')[-2] -Blob $uri.Split('/')[-1] -ea Ignore
        }
 }
```

Pour terminer, supprimez la machine virtuelle. L’applet de commande prend un certain temps pour s’exécuter. Vous pouvez auditer les composants attachés à la machine virtuelle en examinant l’objet de machine virtuelle dans PowerShell. Pour examiner l’objet, il vous suffit de vous référer à la variable qui contient l’objet de machine virtuelle. Tapez `$machine`.

Pour supprimer la machine virtuelle, dans la même session, exécutez les applets de commande suivantes :

```powershell
$machine | Remove-AzureRmVM -Force
```
---
## <a name="next-steps"></a>Étapes suivantes

[Fonctionnalités des machines virtuelles Azure Stack Hub](azure-stack-vm-considerations.md)