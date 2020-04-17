---
title: Différences et points à connaître sur les disques managés Azure Stack Hub
description: Découvrez les différences et considérations à prendre en compte lors de l’utilisation de disques et d’images managés dans Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 1abee3556ac45791831c6dcb9fc8dce05477e51e
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80479303"
---
# <a name="azure-stack-hub-managed-disks-differences-and-considerations"></a>Disques managés Azure Stack Hub : différences et considérations

Cet article récapitule les différences entre les [*disques managés* dans Azure Stack Hub](azure-stack-manage-vm-disks.md) et les [disques managés dans Azure](/azure/virtual-machines/windows/managed-disks-overview). Pour en savoir plus sur les principales différences entre Azure Stack Hub et Azure, consultez l'article [Principales considérations](azure-stack-considerations.md).

Les disques managés simplifient la gestion des disques des machines virtuelles IaaS, en gérant les [comptes de stockage](../operator/azure-stack-manage-storage-accounts.md) associés aux disques de machines virtuelles.

> [!NOTE]  
> Les disques managés sur Azure Stack Hub sont disponibles à compter de la mise à jour 1808. À compter de la mise à jour 1811, la fonctionnalité est activée par défaut lors de la création de machines virtuelles par le biais du portail Azure Stack Hub.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Aide-mémoire : différences entre les disques managés

| Fonctionnalité | Azure (global) | Azure Stack Hub |
| --- | --- | --- |
|Chiffrement des données au repos |Azure Storage Service Encryption (SSE), Azure Disk Encryption (ADE).     |Chiffrement AES 128 bits BitLocker      |
|Image          | Image personnalisée managée |Prise en charge|
|Options de sauvegarde | Service Azure Backup |Pas encore pris en charge |
|Options de récupération d’urgence | Azure Site Recovery |Pas encore pris en charge|
|Types de disque     |SSD Premium, SSD Standard et HDD Standard. |SSD Premium, HDD Standard |
|Disques Premium  |Entièrement pris en charge. |Peut être approvisionné, mais sans limite ni garantie de performances  |
|IOPS des disques Premium  |Varie selon la taille du disque.  |2 300 IOPS par disque |
|Débit des disques Premium |Varie selon la taille du disque. |145 Mo/seconde par disque |
|Taille du disque  |Disque Premium Azure : P4 (32 Gio) à P80 (32 Tio)<br>Disque SSD Standard Azure : E10 (128 Gio) à E80 (32 Tio)<br>Disque HDD Standard Azure : S4 (32 Gio) à S80 (32 Tio) |M4 : 32 Gio<br>M6 : 64 Gio<br>M10 : 128 Go<br>M15 : 256 Gio<br>M20 : 512 Go<br>M30 : 1 023 Gio |
|Copie d’un instantané de disques|Instantané de disques managés Azure attaché à une machine virtuelle en cours d’exécution pris en charge.|Pas encore pris en charge |
|Analyse des performances des disques |Agréger les métriques et les métriques par disque prises en charge. |Pas encore pris en charge |
|Migration      |Fournir l’outil pour migrer à partir de machines virtuelles Azure Resource Manager non managées existantes sans devoir recréer de machine virtuelle.  |Pas encore pris en charge |

> [!NOTE]  
> Les IOPS et le débit de disques managés dans Azure Stack Hub sont exprimés en nombre plafond plutôt qu’en nombre provisionné, qui peut être affecté par le matériel et les charges de travail en cours d’exécution dans Azure Stack Hub.

## <a name="metrics"></a>Mesures

Il existe également des différences sur le plan des métriques de stockage :

- Avec Azure Stack Hub, les données de transaction dans les métriques de stockage ne font pas la distinction entre les bandes passantes réseau interne et externe.
- Les données de transaction d’Azure Stack Hub dans les métriques de stockage n’incluent pas l’accès des machines virtuelles aux disques montés.

## <a name="api-versions"></a>Versions d’API

Les disques managés Azure Stack Hub prennent en charge les versions d’API suivantes :

- 2017-03-30 
- 2017-12-01 (images managées uniquement, pas de disques, pas d’instantanés)

## <a name="convert-to-managed-disks"></a>Convertir en disques managés

> [!NOTE]  
> La cmdlet Azure PowerShell **ConvertTo-AzureRmVMManagedDisk** ne peut pas être utilisée pour convertir un disque non managé en disque managé dans Azure Stack Hub. Azure Stack Hub ne prend pas en charge cette cmdlet pour l’instant.

Vous pouvez utiliser le script suivant pour convertir une machine virtuelle actuellement provisionnée de disques non managés en disques managés. Remplacez les espaces réservés par vos valeurs :

```powershell
$SubscriptionId = "SubId"

# The name of your resource group where your VM to be converted exists.
$ResourceGroupName ="MyResourceGroup"

# The name of the managed disk to be created.
$DiskName = "mngddisk"

# The size of the disks in GB. It should be greater than the VHD file size.
$DiskSize = "50"

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$VhdUri = "https://rgmgddisks347.blob.local.azurestack.external/vhds/unmngdvm20181109013817.vhd"

# The storage type for the managed disk: PremiumLRS or StandardLRS.
$AccountType = "StandardLRS"

# The Azure Stack Hub location where the managed disk will be located.
# The location should be the same as the location of the storage account in which VHD file is stored.
# Configure the new managed VM point to the old unmanaged VM configuration (network config, VM name, location).
$Location = "local"
$VirtualMachineName = "unmngdvm"
$VirtualMachineSize = "Standard_D1"
$PIpName = "unmngdvm-ip"
$VirtualNetworkName = "unmngdrg-vnet"
$NicName = "unmngdvm"

# Set the context to the subscription ID in which the managed disk will be created.
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

# Delete old VM, but keep the OS disk.
Remove-AzureRmVm -Name $VirtualMachineName -ResourceGroupName $ResourceGroupName

# Create the managed disk configuration.
$DiskConfig = New-AzureRmDiskConfig -AccountType $AccountType -Location $Location -DiskSizeGB $DiskSize -SourceUri $VhdUri -CreateOption Import

# Create managed disk.
New-AzureRmDisk -DiskName $DiskName -Disk $DiskConfig -ResourceGroupName $resourceGroupName
$Disk = Get-AzureRmDisk -DiskName $DiskName -ResourceGroupName $ResourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $VirtualMachineName -VMSize $VirtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to "-Windows" if the OS disk has the Windows OS.
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $Disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM.
$PublicIp = Get-AzureRmPublicIpAddress -Name $PIpName -ResourceGroupName $ResourceGroupName

# Get the virtual network where the virtual machine will be hosted.
$VNet = Get-AzureRmVirtualNetwork -Name $VirtualNetworkName -ResourceGroupName $ResourceGroupName

# Create NIC in the first subnet of the virtual network.
$Nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Nic.Id

# Create the virtual machine with managed disk.
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $ResourceGroupName -Location $Location
```

## <a name="managed-images"></a>Images managées

Azure Stack Hub prend en charge les *images managées* qui vous permettent de créer un objet d’image managée sur une machine virtuelle généralisée (à la fois managée et non managée) qui ne pourra ensuite créer que des machines virtuelles à disque managé. Les images managées activent les deux scénarios suivants :

- Vous avez généralisé les machines virtuelles non managées et souhaitez utiliser des disques managés à l’avenir.
- Vous possédez une machine virtuelle managée généralisée et souhaitez créer plusieurs machines virtuelles managées similaires.

### <a name="step-1-generalize-the-vm"></a>Étape 1 : Généraliser la machine virtuelle

Pour Windows, consultez la section [Généraliser la machine virtuelle de Windows à l’aide de Sysprep](/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep). Pour Linux, suivez l’étape 1 [ici](/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm).

> [!NOTE]
> Veillez à généraliser votre machine virtuelle. La création d’une machine virtuelle mal généralisée peut provoquer une erreur **VMProvisioningTimeout**.

### <a name="step-2-create-the-managed-image"></a>Étape 2 : Créer l'image managée

Vous pouvez utiliser le portail, PowerShell ou CLI pour créer l’image managée. Suivez les étapes de [Créer une image managée](/azure/virtual-machines/windows/capture-image-resource).

### <a name="step-3-choose-the-use-case"></a>Étape 3 : Choisir le cas d’usage

#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>Cas n° 1 : Migrer des machines virtuelles non managées vers des disques managés

Veillez à généraliser correctement votre machine virtuelle avant d’effectuer cette étape. Après la généralisation, vous ne pouvez plus utiliser cette machine virtuelle. La création d’une machine virtuelle mal généralisée entraîne une erreur **VMProvisioningTimeout**.

Suivez les instructions de la section [Créer une image à partir d’une machine virtuelle qui utilise un compte de stockage](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-vm-that-uses-a-storage-account) pour créer une image managée à partir d’un disque dur virtuel généralisé dans un compte de stockage. Vous pouvez utiliser cette image pour créer ultérieurement des machines virtuelles managées.

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>Cas n° 2 : Créer une machine virtuelle managée à partir d’une image managée à l’aide de Powershell

Après avoir créé une image à partir d’une machine virtuelle de disque managé avec le script dans [Créer une image à partir d’un disque managé à l’aide de PowerShell](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-managed-disk-using-powershell), utilisez l’exemple de script suivant pour créer une machine virtuelle Linux similaire à partir d’un objet image existant.

Module Azure Stack Hub PowerShell 1.7.0 ou ultérieur : suivez les instructions de la section [Créer une machine virtuelle à partir d’une image managée](/azure/virtual-machines/windows/create-vm-generalized-managed).

Module Azure Stack Hub PowerShell 1.6.0 ou antérieur :

```powershell
# Variables for common values
$ResourceGroupName = "MyResourceGroup"
$Location = "local"
$VirtualMachineName = "MyVM"
$ImageRG = "managedlinuxrg"
$ImageName = "simplelinuxvmm-image-2019122"

# Create credential object
$Cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a subnet configuration
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name "MySubnet" -AddressPrefix "192.168.1.0/24"

# Create a virtual network
$VNet = New-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "MyVNet" -AddressPrefix "192.168.0.0/16" -Subnet $SubnetConfig

# Create a public IP address and specify a DNS name
$PIp = New-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "MyNetworkSecurityGroupRuleRDP"  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "MyNetworkSecurityGroup" -SecurityRules $NsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$Nic = New-AzureRmNetworkInterface -Name "MyNic" -ResourceGroupName $ResourceGroupName -Location $Location `
  -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PIp.Id -NetworkSecurityGroupId $Nsg.Id

$Image = Get-AzureRmImage -ResourceGroupName $ImageRG -ImageName $ImageName

# Create a virtual machine configuration
$VmConfig = New-AzureRmVMConfig -VMName $VirtualMachineName -VMSize "Standard_D1" | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $VirtualMachineName -Credential $Cred | `
Set-AzureRmVMSourceImage -Id $Image.Id | `
Add-AzureRmVMNetworkInterface -Id $Nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VmConfig
```

Vous pouvez également utiliser le portail pour créer une machine virtuelle à partir d’une image managée. Pour plus d’informations, consultez les articles sur les images managées Azure, [Créer une image managée d’une machine virtuelle généralisée dans Azure](/azure/virtual-machines/windows/capture-image-resource) et [Créer une machine virtuelle à partir d’une image managée](/azure/virtual-machines/windows/create-vm-generalized-managed).

## <a name="configuration"></a>Configuration

Après avoir appliqué la mise à jour 1808 ou ultérieure, vous devez appliquer le changement de configuration suivant avant d’utiliser des disques managés :

- Si un abonnement a été créé avant la mise à jour 1808, procédez comme suit pour mettre à jour l’abonnement. Sinon, le déploiement de machines virtuelles dans cet abonnement peut échouer avec un message d’erreur « Erreur interne dans le gestionnaire de disque ».
   1. Dans le portail utilisateur Azure Stack Hub, accédez à **Abonnements** et recherchez l’abonnement. Cliquez sur **Fournisseurs de ressources**, sur **Microsoft.Compute**, puis sur **Réinscrire**.
   2. Sous le même abonnement, accédez à **Contrôle d’accès (IAM)** et vérifiez que l’élément **Azure Stack Hub – Disques managés** est répertorié.
- Si vous utilisez un environnement mutualisé, demandez à votre opérateur cloud (dans votre organisation ou chez votre fournisseur de services) de reconfigurer chacun de vos annuaires invités conformément aux étapes décrites dans [cet article](../operator/azure-stack-enable-multitenancy.md#registering-azure-stack-hub-with-the-guest-directory). Sinon, le déploiement de machines virtuelles dans un abonnement associé à cet annuaire invité peut échouer avec le message d’erreur « Erreur interne dans le gestionnaire de disque ».

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur les [machines virtuelles Azure Stack Hub](azure-stack-compute-overview.md).
