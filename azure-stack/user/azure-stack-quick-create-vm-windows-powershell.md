---
title: Créer une machine virtuelle Windows Server avec PowerShell dans Azure Stack | Microsoft Docs
description: Créez une machine virtuelle Windows Server avec PowerShell dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: mabrigg
ms.custom: mvc
ms.reviewer: kivenkat
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 04b2f2a5e4e9caa8e8eacc47b44c60a6884a6837
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986044"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-powershell-in-azure-stack"></a>Démarrage rapide : Créer une machine virtuelle Windows Server avec PowerShell dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Il est possible de créer une machine virtuelle Windows Server 2016 avec Azure Stack PowerShell. Suivez les étapes décrites dans cet article pour créer et utiliser une machine virtuelle. Cet article vous présente également les étapes pour :

* Se connecter à la machine virtuelle avec un client distant.
* Installer le serveur web IIS et afficher la page d’accueil par défaut.
* Nettoyer vos ressources.

> [!NOTE]
>  Vous pouvez exécuter les étapes décrites dans cet article à partir du Kit de développement Azure Stack ou à partir d’un client externe Windows si vous êtes connecté par le biais d’un VPN.

## <a name="prerequisites-for-windows-server-vm"></a>Prérequis des machines virtuelles Windows Server

* Vérifiez que votre opérateur Azure Stack a ajouté l’image **Windows Server 2016** à la Place de Marché Azure Stack.

* Azure Stack nécessite une version spécifique d’Azure PowerShell pour créer et gérer les ressources. Si PowerShell n’est pas configuré pour Azure Stack, suivez les étapes permettant [d’installer](../operator/azure-stack-powershell-install.md) PowerShell.

* Une fois Azure Stack PowerShell configuré, vous devrez vous connecter à votre environnement Azure Stack. Pour plus d’informations, consultez [Se connecter en tant qu’utilisateur à Azure Stack à l’aide de PowerShell](azure-stack-powershell-configure-user.md).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel les ressources Azure Stack sont déployées et gérées. À partir de votre kit de développement ou du système intégré Azure Stack, exécutez le bloc de code suivant pour créer un groupe de ressources. Des valeurs sont attribuées à toutes les variables dans ce document. Vous pouvez utiliser ces valeurs ou en attribuer de nouvelles.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Créer des ressources de stockage

Créez un compte de stockage, ainsi qu’un conteneur de stockage pour stocker l’image Windows Server 2016.

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

```

## <a name="create-networking-resources"></a>Création de ressources de mise en réseau

Créez un réseau virtuel, un sous-réseau et une adresse IP publique. Ces ressources sont utilisées pour fournir une connectivité réseau à la machine virtuelle.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Créez un groupe de sécurité réseau et une règle de groupe de sécurité réseau

Le groupe de sécurité réseau sécurise la machine virtuelle à l’aide de règles de trafic entrantes et sortantes. Créons une règle de trafic entrant pour le port 3389 pour autoriser les connexions Bureau à distance entrantes, et une règle de trafic entrant pour le port 80 pour autoriser le trafic web entrant.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleRDP `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleWWW `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Créer une carte réseau pour la machine virtuelle

La carte réseau connecte la machine virtuelle à un sous-réseau, un groupe de sécurité réseau et une adresse IP publique.

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Créez une configuration de machine virtuelle. Cette configuration inclut les paramètres utilisés lors du déploiement de la machine virtuelle. Par exemple : informations d’identification, taille et image de la machine virtuelle.

```powershell
# Define a credential object to store the username and password for the virtual machine
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
  -Version "latest"

# Sets the operating system disk properties on a virtual machine.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -CreateOption FromImage | `
  Set-AzureRmVMBootDiagnostics -ResourceGroupName $ResourceGroupName `
  -StorageAccountName $StorageAccountName -Enable |`
  Add-AzureRmVMNetworkInterface -Id $nic.Id


# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>Connectez-vous à la machine virtuelle.

Pour accéder à distance à la machine virtuelle créée à l’étape précédente, vous avez besoin de son adresse IP publique. Exécutez la commande suivante pour obtenir l’adresse IP publique de la machine virtuelle :

```powershell
Get-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```

Utilisez la commande suivante pour créer une session Bureau à distance avec la machine virtuelle. Remplacez l’adresse IP par l’adresse publicIPAddress de votre machine virtuelle. Quand vous y êtes invité, entrez le nom d’utilisateur et le mot de passe que vous avez utilisés lors de la création de la machine virtuelle.

```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Installer IIS à l’aide de PowerShell

Maintenant que vous êtes connecté à la machine virtuelle Azure, vous pouvez utiliser une seule ligne de PowerShell pour installer IIS et activer la règle de pare-feu local pour autoriser le trafic web. Ouvrez une invite PowerShell et exécutez la commande suivante :

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Afficher la page d’accueil IIS

Une fois IIS installé et le port 80 ouvert sur votre machine virtuelle, vous pouvez utiliser un navigateur web pour afficher la page d’accueil IIS par défaut. Utilisez l’élément *publicIpAddress* indiqué dans la section précédente pour vous rendre sur la page par défaut.

![Site IIS par défaut](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png)

## <a name="delete-the-virtual-machine"></a>Supprimer la machine virtuelle

Quand vous n’en avez plus besoin, utilisez la commande suivante pour supprimer le groupe de ressources qui contient la machine virtuelle et les ressources qui lui sont associées :

```powershell
Remove-AzureRmResourceGroup `
  -Name $ResourceGroupName
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une machine virtuelle Windows simple. Pour en savoir plus sur les machines virtuelles Azure Stack, continuez avec [Considérations relatives aux machines virtuelles dans Azure Stack](azure-stack-vm-considerations.md).
