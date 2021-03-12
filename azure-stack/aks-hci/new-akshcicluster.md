---
title: New-AksHciCluster
author: jessicaguan
description: La commande PowerShell New-AksHciCluster crée un cluster Kubernetes managé.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: d4b374ca093c60018d1dc68c3d5a39db68456298
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874390"
---
# <a name="new-akshcicluster"></a>New-AksHciCluster

## <a name="synopsis"></a>Synopsis
Créer un cluster Kubernetes managé.

## <a name="syntax"></a>Syntaxe

```powershell
New-AksHciCluster -name <String>
                 [-kubernetesVersion <String>]
                 [-controlPlaneNodeCount <int>]
                 [-linuxNodeCount <int>]
                 [-windowsNodeCount <int>]
                 [-controlPlaneVmSize <VmSize>]
                 [-loadBalancerVmSize <VmSize>]
                 [-linuxNodeVmSize <VmSize>]
                 [-windowsNodeVmSize <VmSize>]
                 [-vnet <Virtual Network>]
                 [-primaryNetworkPlugin <Network Plugin>]   
                 [-enableAdAuth]
                 [-enableSecretsEncryption]          
```

## <a name="description"></a>Description

Créer un cluster Azure Kubernetes Service sur Azure Stack HCI.

## <a name="examples"></a>Exemples

### <a name="new-aks-hci-cluster-with-required-params"></a>Nouveau cluster AKS-HCI avec les paramètres exigés.

```powershell
PS C:\> New-AksHciCluster -name myCluster
```

## <a name="parameters"></a>Paramètres

### <a name="-name"></a>-name
Nom du cluster Kubernetes.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-kubernetesversion"></a>-kubernetesVersion
Version de Kubernetes que vous souhaitez déployer. La version la plus récente est utilisée par défaut. Pour obtenir la liste des tailles de versions disponibles, exécutez `Get-AksHciKubernetesVersion`.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: v1.18.8 or later
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-controlplanenodecount"></a>-controlPlaneNodeCount
Nombre de nœuds dans votre plan de contrôle. 1 constitue la valeur par défaut.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-linuxnodecount"></a>-linuxNodeCount
Nombre de nœuds Linux dans votre cluster Kubernetes. 1 constitue la valeur par défaut.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases: 

Required: False
Position: Named
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-windowsnodecount"></a>-windowsNodeCount
Nombre de nœuds Windows dans votre cluster Kubernetes. La valeur par défaut est 0. Vous ne pouvez déployer des nœuds Windows que si vous exécutez Kubernetes 1.18.8 ou une version ultérieure.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 0
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-controlplanevmsize"></a>-controlPlaneVmSize
Taille de la machine virtuelle du plan de contrôle. La valeur par défaut est Standard_A4_V2. Pour obtenir la liste des tailles de machines virtuelles disponibles, exécutez `Get-AksHciVmSize`.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_A4_V2
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-loadbalancervmsize"></a>-loadBalancerVmSize
Taille de votre machine virtuelle équilibreur de charge. La valeur par défaut est Standard_A4_V2. Pour obtenir la liste des tailles de machines virtuelles disponibles, exécutez `Get-AksHciVmSize`.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_A4_V2
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-linuxnodevmsize"></a>-linuxNodeVmSize
Taille de votre machine virtuelle de nœud Linux. La valeur par défaut est Standard_K8S3_v1. Pour obtenir la liste des tailles de machines virtuelles disponibles, exécutez `Get-AksHciVmSize`.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_K8S3_v1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-windowsvmsize"></a>-windowsVmSize
Taille de votre machine virtuelle de nœud Windows. La valeur par défaut est Standard_K8S3_v1. Pour obtenir la liste des tailles de machines virtuelles disponibles, exécutez `Get-AksHciVmSize`.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_K8S3_v1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vnet"></a>-vnet
Nom de l’objet AksHciNetworkSetting créé avec la commande New-AksHciNetworkSetting.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-primarynetworkplugin"></a>-primaryNetworkPlugin
Plug-in réseau à utiliser pour votre déploiement. Ce paramètre accepte `flannel` ou `calico`. Calico est uniquement disponible pour les clusters Linux. Si vous choisissez Calico pour votre cluster, l’ajout de nœuds Windows n’est pas autorisé.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: flannel
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-enableadauth"></a>-enableADAuth
Utilisez cet indicateur pour activer Active Directory dans votre cluster Kubernetes.

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-enablesecretsencryption"></a>-enableSecretsEncryption
Utilisez cet indicateur pour activer le chiffrement dans vos secrets Kubernetes.

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
