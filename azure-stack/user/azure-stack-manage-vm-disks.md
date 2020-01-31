---
title: Créer un stockage sur disque de machine virtuelle dans Azure Stack Hub
description: Créez des disques pour machines virtuelles dans Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: a6f728e69eacab25941ef0640a35243d390fa4c7
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883478"
---
# <a name="create-vm-disk-storage-in-azure-stack-hub"></a>Créer un stockage sur disque de machine virtuelle dans Azure Stack Hub

Cet article explique comment créer du stockage sur disque de machine virtuelle à l’aide du portail Azure Stack Hub ou de PowerShell.

## <a name="overview"></a>Vue d’ensemble

À compter de la version 1808, Azure Stack Hub prend en charge l’utilisation de disques managés et non managés dans des machines virtuelles, tant comme disque de système d’exploitation que comme disque de données. Dans les versions antérieures à la version 1808, seuls les disques non managés sont pris en charge.

Les [disques managés](/azure/virtual-machines/windows/managed-disks-overview) simplifient la gestion des disques des machines virtuelles Azure IaaS, en gérant les comptes de stockage associés aux disques de machines virtuelles. Vous devez simplement spécifier la taille de disque dont vous avez besoin pour qu’Azure Stack Hub crée et gère le disque pour vous.

Les disques non managés nécessitent la création d’un compte de stockage pour stocker les disques. Les disques que vous créez sont appelés « disques de machine virtuelle » et sont stockés dans des conteneurs, dans le compte de stockage.

## <a name="best-practice-guidelines"></a>Conseils de bonnes pratiques

Nous vous recommandons d’utiliser Disques managés pour les machines virtuelles afin de faciliter la gestion et l’équilibrage de la capacité. Il n’est pas nécessaire de préparer un compte de stockage et des conteneurs avant d’utiliser Disques managés. Lors de la création de plusieurs disques managés, ceux-ci sont répartis sur plusieurs volumes, ce qui permet d’équilibrer la capacité des volumes.  

Pour améliorer les performances et réduire les coûts globaux liés aux disques non managés, nous vous recommandons de placer chaque disque non managé dans un conteneur distinct. Même si vous pouvez placer les disques de système d’exploitation et les disques de données dans un même conteneur, il est préférable de les placer chacun dans leur conteneur.

Si vous ajoutez un ou plusieurs disques de données à une machine virtuelle, utilisez des conteneurs supplémentaires comme emplacement de stockage de ces disques. Le disque du système d’exploitation pour des machines virtuelles supplémentaires doit également se trouver dans son propre conteneur distinct.

Quand vous créez des machines virtuelles, vous pouvez réutiliser le même compte de stockage pour chaque nouvelle machine virtuelle. Seuls les conteneurs que vous créez doivent être uniques.

## <a name="adding-new-disks"></a>Ajout de nouveaux disques

Le tableau suivant est un récapitulatif de la façon d’ajouter des disques avec le portail et avec PowerShell :

| Méthode | Options
|-|-|
|Portail de l’utilisateur|- Ajouter de nouveaux disques de données à une machine virtuelle existante. Les nouveaux disques sont créés par Azure Stack Hub. </br> </br> - Ajouter un fichier disque (.vhd) existant à une machine virtuelle déjà créée. Pour cela, vous devez d’abord préparer le .vhd, puis charger le fichier sur Azure Stack Hub. |
|[PowerShell](#use-powershell-to-add-multiple-disks-to-a-vm) | - Créez une machine virtuelle en utilisant un disque de système d’exploitation, et ajoutez parallèlement un ou plusieurs disques de données à cette machine virtuelle. |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>Utiliser le portail pour ajouter des disques à une machine virtuelle

Par défaut, quand vous utilisez le portail pour créer une machine virtuelle pour la plupart des éléments de la Place de marché, seul le disque du système d’exploitation est créé.

Après avoir créé une machine virtuelle, vous pouvez utiliser le portail pour :

* Créer un disque de données et l’attacher à la machine virtuelle.
* Charger un disque de données existant et l’attacher à la machine virtuelle.

Chaque disque non managé que vous ajoutez doit être placé dans un conteneur distinct.

>[!NOTE]  
>Les disques créés et managés par Azure sont appelés [disques managés](/azure/virtual-machines/windows/managed-disks-overview).

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>Utiliser le portail pour créer et attacher un disque de données

1. Dans le portail, sélectionnez **Tous les services**, puis **Machines virtuelles**.
   ![Exemple : tableau de bord de machine virtuelle](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2. Sélectionnez une machine virtuelle qui a déjà été créée.
   ![Exemple : sélectionner une machine virtuelle dans le tableau de bord](media/azure-stack-manage-vm-disks/select-a-vm.png)

3. Pour la machine virtuelle, sélectionnez **Disques**, puis **Ajouter un disque de données**.
   ![Exemple : attacher un nouveau disque à la machine virtuelle](media/azure-stack-manage-vm-disks/Attach-disks.png)

4. Pour le disque de données :
   * Entrez le **LUN** (numéro d’unité logique). Le numéro d’unité logique doit être un nombre valide.
   * Sélectionnez **Créer un disque**.
   ![Exemple : attacher un nouveau disque à la machine virtuelle](media/azure-stack-manage-vm-disks/add-a-data-disk-create-disk.png)

5. Dans le panneau **Créer un disque géré** :
   * Entrez le **Nom** du disque.
   * Sélectionnez un **groupe de ressources** existant ou créez-en un.
   * Sélectionnez un **Emplacement**. Par défaut, l’emplacement est défini sur le conteneur qui renferme le disque du système d’exploitation.
   * Sélectionnez le **Type de compte**.
      ![Exemple : attacher un nouveau disque à la machine virtuelle](media/azure-stack-manage-vm-disks/create-manage-disk.png)

      **SSD Premium**  
      Les disques Premium (SSD) sont associés à des disques SSD afin d’offrir des performances constantes et une faible latence. Ils proposent le meilleur rapport prix/performances et conviennent parfaitement aux charges de travail de production et aux applications nécessitant beaucoup d’E/S.

      **HDD Standard**  
      Les disques standard (HDD) sont associés à des lecteurs magnétiques et conviennent davantage aux applications dont les données sont rarement utilisées. Les disques redondants interzones sont associés à un stockage redondant interzone (ZRS) qui réplique vos données sur plusieurs zones, garantissant ainsi leur disponibilité, même si une zone n’est pas disponible.

   * Sélectionnez le **Type de source**.

     Créez un disque à partir d’un instantané d’un autre disque ou d’un objet blob d’un compte de stockage, ou créez un disque vide.

      **Capture instantanée** : Sélectionnez un instantané, si disponible. L’instantané doit être disponible dans l’abonnement et l’emplacement de la machine virtuelle.

      **Blob de stockage** :
     * Ajoutez l’URI de l’objet blob de stockage qui contient l’image disque.  
     * Sélectionnez **Parcourir** pour ouvrir le panneau Comptes de stockage. Pour obtenir des instructions, consultez [Ajouter un disque de données à partir d’un compte de stockage](#add-a-data-disk-from-a-storage-account).
     * Sélectionnez le type de système d’exploitation de l’image : **Windows**, **Linux** ou **Aucun(e) (disque de données)** .

   * Sélectionnez la **Taille (Gio)** .

     Le coût d’un disque standard augmente avec sa taille. Le coût et les performances d’un disque Premium augmentent avec sa taille. Pour plus d’informations, consultez [Tarification de la fonctionnalité Disques managés](https://go.microsoft.com/fwlink/?linkid=843142).

   * Sélectionnez **Create** (Créer). Azure Stack Hub crée et valide le disque managé.

6. Après qu’Azure Stack Hub a créé le disque et l’a attaché à la machine virtuelle, le nouveau disque apparaît dans les paramètres de disque de machine virtuelle sous **DISQUES DE DONNÉES**.

   ![Exemple : Afficher le disque](media/azure-stack-manage-vm-disks/view-data-disk.png)

### <a name="add-a-data-disk-from-a-storage-account"></a>Ajouter un disque de données à partir d’un compte de stockage

Pour plus d’informations sur l’utilisation des comptes de stockage dans Azure Stack Hub, voir [Présentation du stockage Azure Stack Hub](azure-stack-storage-overview.md).

1. Sélectionnez le **compte de stockage** à utiliser.
2. Sélectionnez le **Conteneur** dans lequel vous souhaitez placer le disque de données. Si vous le souhaitez, vous pouvez créer un conteneur dans le panneau **Conteneurs**. Vous pouvez ensuite modifier l’emplacement du nouveau disque et le définir sur son propre conteneur. Si vous utilisez un conteneur distinct pour chaque disque, vous répartissez le positionnement du disque de données, ce qui peut améliorer les performances.
3. Pour enregistrer la sélection, choisissez **Sélectionner**.

    ![Exemple : Sélectionner un conteneur](media/azure-stack-manage-vm-disks/select-container.png)

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Ajouter un disque de données existant à une machine virtuelle

1. [Préparez un fichier .vhd](/azure/virtual-machines/windows/classic/createupload-vhd) à utiliser comme disque de données pour une machine virtuelle. Chargez ce fichier .vhd sur un compte de stockage que vous utilisez avec la machine virtuelle à laquelle vous souhaitez attacher le fichier .vhd.

    - Pour stocker le fichier .vhd, pensez à utiliser un conteneur autre que le conteneur du disque du système d’exploitation.  
    - Avant de télécharger un disque dur virtuel dans Azure, vous devez apprendre à [préparer un disque dur virtuel Windows à charger sur Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
    - Consultez [Planification de la migration vers la fonctionnalité Disques managés](https://docs.microsoft.com/azure/virtual-machines/windows/on-prem-to-azure#plan-for-the-migration-to-managed-disks) avant de démarrer la migration vers la [fonctionnalité Disques managés](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).
    
    ![Exemple : Télécharger un fichier VHD](media/azure-stack-manage-vm-disks/upload-vhd.png)



2. Une fois que le fichier .vhd est chargé, vous êtes prêt à attacher le disque dur virtuel (VHD) à une machine virtuelle. Dans le menu de gauche, sélectionnez **Machines virtuelles**.  
 ![Exemple : sélectionner une machine virtuelle dans le tableau de bord](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3. Choisissez la machine virtuelle dans la liste.

    ![Exemple : sélectionner une machine virtuelle dans le tableau de bord](media/azure-stack-manage-vm-disks/select-a-vm.png)

4. Sur la page correspondant à la machine virtuelle, sélectionnez **Disques**, puis **Attacher un disque existant**.

    ![Exemple : Association d'un disque existant](media/azure-stack-manage-vm-disks/attach-disks2.png)

5. Dans la page **Attacher un disque existant**, sélectionnez **Fichier VHD**. La page **Comptes de stockage** s’ouvre.

    ![Exemple : Sélectionner un fichier VHD](media/azure-stack-manage-vm-disks/select-vhd.png)

6. Sous **Comptes de stockage**, sélectionnez le compte à utiliser, puis choisissez un conteneur qui contient le fichier .vhd que vous avez précédemment chargé. Sélectionnez le fichier .vhd, puis choisissez **Sélectionner** pour enregistrer la sélection.

    ![Exemple : Sélectionner un conteneur](media/azure-stack-manage-vm-disks/select-container2.png)

7. Sous **Attacher un disque existant**, le fichier que vous avez sélectionné est répertorié sous **Fichier VHD**. Mettez à jour le paramètre **Mise en cache de l’hôte** du disque, puis sélectionnez **OK** pour enregistrer la configuration du nouveau disque de la machine virtuelle.

    ![Exemple : Attacher le fichier VHD](media/azure-stack-manage-vm-disks/attach-vhd.png)

8. Après qu’Azure Stack Hub a créé le disque et l’a attaché à la machine virtuelle, le nouveau disque apparaît dans les paramètres de disque de la machine virtuelle sous **Disques de données**.

    ![Exemple : Procéder à l’attachement du disque](media/azure-stack-manage-vm-disks/complete-disk-attach.png)

## <a name="use-powershell-to-add-multiple-disks-to-a-vm"></a>Utiliser PowerShell pour ajouter plusieurs disques à une machine virtuelle

Vous pouvez utiliser PowerShell pour provisionner une machine virtuelle et ajouter de nouveaux disques de données, ou pour attacher un disque managé préexistant ou un fichier .vhd en tant que disque de données.

La cmdlet **Add-AzureRmVMDataDisk** ajoute un disque de données à une machine virtuelle. Vous pouvez ajouter un disque de données lorsque vous créez une machine virtuelle. Vous pouvez également ajouter un disque de données à une machine virtuelle existante. Pour un disque non managé, spécifiez le paramètre **VhdUri** pour distribuer les disques sur différents conteneurs.

### <a name="add-data-disks-to-a-new-vm"></a>Ajouter des disques de données à une **nouvelle** machine virtuelle

Les exemples suivants utilisent des commandes PowerShell pour créer une machine virtuelle avec trois disques de données. Les commandes sont fournies avec plusieurs parties en raison des différences mineures liées à l’utilisation de disques managés ou de disques non managés. 

#### <a name="create-virtual-machine-configuration-and-network-resources"></a>Créer une configuration de machine virtuelle et des ressources réseau

La première commande crée un objet de machine virtuelle, puis le stocke dans la variable `$VirtualMachine`. Les commandes affectent un nom et une taille à la machine virtuelle, puis créent les ressources réseau (réseau virtuel, sous-réseau, carte réseau virtuelle, groupe de sécurité réseau et adresse IP publique) pour la machine virtuelle :

```powershell
# Create new virtual machine configuration
$VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"

# Set variables
$rgName = "myResourceGroup"
$location = "local"

# Create a subnet configuration
$subnetName = "mySubNet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

# Create a vnet configuration
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
                                  -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

# Create a public IP
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
                                  -AllocationMethod Dynamic

# Create a network security group configuration
$nsgName = "myNsg"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
                                                -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
                                                -SourceAddressPrefix Internet -SourcePortRange * `
                                                -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
                                       -Name $nsgName -SecurityRules $rdpRule

# Create a NIC configuration
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
                                   -Location $location -SubnetId $vnet.Subnets[0].Id `
                                   -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

```

#### <a name="add-managed-disk"></a>Ajouter un disque managé
>[!NOTE]  
>Cette section concerne seulement l’ajout de disques managés. 

Les trois commandes suivantes ajoutent des disques de données managés à la machine virtuelle stockée dans `$VirtualMachine`. Chaque commande spécifie le nom et d’autres propriétés du disque :

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                                        -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                                        -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                                        -CreateOption Empty
```

La commande suivante ajoute un disque de système d’exploitation en tant que disque managé à la machine virtuelle stockée dans `$VirtualMachine`.

```powershell
# Set OS Disk
$osDiskName = "osDisk"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName  `
                                      -CreateOption FromImage -Windows
```

#### <a name="add-unmanaged-disk"></a>Ajouter un disque non managé

>[!NOTE]  
>Cette section concerne seulement l’ajout de disques non managés. 

Les trois commandes suivantes affectent les chemins des trois disques de données non managés aux variables `$DataDiskVhdUri01`, `$DataDiskVhdUri02` et `$DataDiskVhdUri03`. Définissez un nom de chemin d’accès différent dans l’URL pour répartir les disques dans différents conteneurs :

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

Les trois commandes suivantes ajoutent des disques de données à la machine virtuelle stockée dans `$VirtualMachine`. Chaque commande spécifie le nom et d’autres propriétés du disque. L’URI de chaque disque est stocké dans `$DataDiskVhdUri01`, `$DataDiskVhdUri02` et `$DataDiskVhdUri03` :

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                                        -VhdUri $DataDiskVhdUri01 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                                        -VhdUri $DataDiskVhdUri02 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                                        -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                                        -VhdUri $DataDiskVhdUri03 -CreateOption Empty
```

Les commandes suivantes ajoutent des disques de système d’exploitation non managés à la machine virtuelle stockée dans `$VirtualMachine`.

```powershell
# Set OS Disk
$osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
$osDiskName = "osDisk"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
                                      -CreateOption FromImage -Windows
```


#### <a name="create-new-virtual-machine"></a>Créer une machine virtuelle
Utilisez les commandes PowerShell suivantes pour définir une image de système d’exploitation, pour ajouter la configuration du réseau à la machine virtuelle, puis pour démarrer la nouvelle machine virtuelle.

```powershell
#Create the new VM
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine -ProvisionVMAgent | `
                  Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
                  -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
```


### <a name="add-data-disks-to-an-existing-vm"></a>Ajouter des disques de données à une machine virtuelle **existante**
Les exemples suivants utilisent des commandes PowerShell pour ajouter trois disques de données à une machine virtuelle existante.

#### <a name="get-virtual-machine"></a>Obtenir une machine virtuelle

 La première commande récupère la machine virtuelle nommée **VirtualMachine** à l’aide de la cmdlet **Get-AzureRmVM**. La commande stocke la machine virtuelle dans la variable `$VirtualMachine` :

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                -Name "VirtualMachine"
```

#### <a name="add-managed-disk"></a>Ajouter un disque managé

>[!NOTE]  
>Cette section concerne seulement l’ajout de disques managés.

Les trois commandes suivantes ajoutent les disques de données managés à la machine virtuelle stockée dans la variable `$VirtualMachine`. Chaque commande spécifie le nom et d’autres propriétés du disque :

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk1" -Lun 0 `
                      -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk2" -Lun 1 `
                      -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3" -Lun 2 `
                      -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
```

#### <a name="add-unmanaged-disk"></a>Ajouter un disque non managé

>[!NOTE]  
>Cette section concerne seulement l’ajout de disques non managés. 

Les trois commandes suivantes affectent des chemins pour trois disques de données aux variables `$DataDiskVhdUri01`, `$DataDiskVhdUri02` et `$DataDiskVhdUri03`. Les différents noms de chemin dans les URI VHD indiquent différents conteneurs pour le placement des disques :

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

Les trois commandes suivantes ajoutent les disques de données à la machine virtuelle stockée dans la variable `$VirtualMachine`. Chaque commande spécifie le nom, l’emplacement et des propriétés supplémentaires du disque. L’URI de chaque disque est stocké dans `$DataDiskVhdUri01`, `$DataDiskVhdUri02` et `$DataDiskVhdUri03` :

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk1" `
                      -VhdUri $DataDiskVhdUri01 -LUN 0 `
                      -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk2" `
                      -VhdUri $DataDiskVhdUri02 -LUN 1 `
                      -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3" `
                      -VhdUri $DataDiskVhdUri03 -LUN 2 `
                      -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
```

#### <a name="update-virtual-machine-state"></a>Mettre à jour l’état d’une machine virtuelle

Cette commande met à jour l’état de la machine virtuelle stockée dans `$VirtualMachine` dans `-ResourceGroupName` :

```powershell
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les machines virtuelles Azure Stack Hub, voir [Considérations relatives aux machines virtuelles dans Azure Stack Hub](azure-stack-vm-considerations.md).
