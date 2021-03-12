---
title: Install-AksHciArcOnboarding
author: jessicaguan
description: La commande PowerShell Install-AksHciArcOnboarding connecte un cluster de charge de travail AKS sur Azure Stack HCI à Azure Arc pour Kubernetes.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: a366e553ac76882a7f45a9f25424cabbb4402319
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874392"
---
# <a name="install-akshciarconboarding"></a>Install-AksHciArcOnboarding

## <a name="synopsis"></a>Synopsis
Connecte un cluster de charge de travail AKS sur Azure Stack HCI à Azure Arc pour Kubernetes.

## <a name="syntax"></a>Syntaxe

```powershell
Install-AksHciArcOnboarding -Name <String> 
                            -tenantId <String>
                            -subscriptionId <String> 
                            -resourceGroup <String>
                            -clientId <String>
                            -clientSecret <String>
                            [-location <String>]
```

## <a name="description"></a>Description
Connecte un cluster de charge de travail AKS sur Azure Stack HCI à Azure Arc pour Kubernetes.

## <a name="examples"></a>Exemples

### <a name="connect-an-aks-on-azure-stack-hci-cluster-to-azure-arc-for-kubernetes-with-required-parameters"></a>Connecter un cluster AKS sur Azure Stack HCI à Azure Arc pour Kubernetes avec les paramètres exigés.

```PowerShell
Install-AksHciArcOnboarding -name "myCluster" -resourcegroup "myResourceGroup" -subscriptionid "57ac26cf-a9f0-4908-b300-9a4e9a0fb205"  -clientid "22cc2695-54b9-49c1-9a73-2269592103d8" -clientsecret "09d3a928-b223-4dfe-80e8-fed13baa3b3d" -tenantid "72f988bf-86f1-41af-91ab-2d7cd011db47"
```

### <a name="connect-an-aks-on-azure-stack-hci-cluster-to-azure-arc-for-kubernetes-with-all-parameters"></a>Connecter un cluster AKS sur Azure Stack HCI à Azure Arc pour Kubernetes avec tous les paramètres

```PowerShell
Install-AksHciArcOnboarding -name "myCluster" -resourcegroup "myResourceGroup" -location "eastus" -subscriptionid "57ac26cf-a9f0-4908-b300-9a4e9a0fb205"  -clientid "22cc2695-54b9-49c1-9a73-2269592103d8" -clientsecret "09d3a928-b223-4dfe-80e8-fed13baa3b3d" -tenantid "72f988bf-86f1-41af-91ab-2d7cd011db47"
```

## <a name="parameters"></a>Paramètres

### <a name="-name"></a>-Name
Nom alphanumérique de votre cluster Kubernetes.

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

### <a name="-tenantid"></a>-tenantId
ID de locataire de votre principal de service Azure.

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

### <a name="-subscriptionid"></a>-subscriptionId
ID d’abonnement de votre compte Azure.

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

### <a name="-resourcegroup"></a>-resourceGroup
Nom du groupe de ressources Azure.

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

### <a name="-clientid"></a>-clientId
ID client ou ID d’application de votre principal de service Azure

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

### <a name="-clientsecret"></a>-clientSecret
Mot de passe ou secret client de votre principal de service Azure.

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

### <a name="-location"></a>-location
Emplacement ou région Azure de votre ressource Azure. La valeur par défaut est l’emplacement de votre groupe de ressources Azure. L’emplacement peut être uniquement eastus ou westeurope pour Azure Arc pour Kubernetes.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Azure resource group's location
Accept pipeline input: False
Accept wildcard characters: False
```
