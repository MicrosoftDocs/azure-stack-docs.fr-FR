---
title: New-AksHciNetworkSetting
author: jessicaguan
description: La commande PowerShell New-AksHciNetworkSetting crée un objet pour un nouveau réseau virtuel.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 5babbe99254b23d642696b7ce7c5b865105bd906
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874373"
---
# <a name="new-akshcinetworksetting"></a>New-AksHciNetworkSetting

## <a name="synopsis"></a>Synopsis
Créer un objet pour un nouveau réseau virtuel.

## <a name="syntax"></a>Syntaxe
```powershell
New-AksHciNetworkSetting -vnetName <String>
                         -gateway <String>
                         -dnsServers <String[]>
                         -ipAddressPrefix <String>
                         -vipPoolStart <IP address>
                         -vipPoolEnd <IP address>
                         -k8sNodeIpPoolStart <IP address>
                         -k8sNodeIpPoolEnd <IP address>
                        [-vlanID <int>]
                    
```

## <a name="description"></a>Description
Créer un réseau virtuel pour définir l’adresse IP statique ou DHCP du plan de contrôle, de l’équilibreur de charge, des points de terminaison d’agent, et une plage d’adresses IP statiques pour les nœuds de tous les clusters Kubernetes. Cette applet de commande retourne un objet VirtualNetwork qui peut être utilisé ultérieurement dans les étapes de configuration.

## <a name="examples"></a>Exemples

### <a name="deploy-with-a-static-ip-environment"></a>Déployer avec un environnement IP statique

```powershell
PS C:\> $vnet = New-AksHciNetworkSetting -vnetName "External" -k8sNodeIpPoolStart "172.16.10.0" -k8sNodeIpPoolEnd "172.16.10.255" -vipPoolStart "172.16.255.0" -vipPoolEnd "172.16.255.254" -ipAddressPrefix "172.16.0.0/16" -gateway "172.16.0.1" -dnsServers "172.16.0.1" 
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vnet $vnet -enableDiagnosticData -cloudservicecidr "172.16.10.10/16"
```

> [!NOTE]
> Les valeurs fournies dans cet exemple de commande doivent être personnalisées pour votre environnement.

### <a name="deploy-with-a-dhcp-environment"></a>Déployer avec un environnement DHCP

```powershell
PS C:\> $vnet = New-AksHciNetworkSetting -vnetName "External" -vipPoolStart "172.16.255.0" -vipPoolEnd "172.16.255.254" 
```

```powershell
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vnet $vnet -enableDiagnosticData"
```

## <a name="parameters"></a>Paramètres

### <a name="-vnetname"></a>-vnetName
Nom de votre commutateur externe. Pour obtenir la liste des noms de vos commutateurs disponibles, exécutez la commande `Get-VMSwitch`.

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

### <a name="-gateway"></a>-gateway
Adresse IP de la passerelle par défaut du sous-réseau.

```yaml
Type: System.String
Parameter Sets: (StaticIP)
Aliases:

Required: False (This is required when creating a network with a static IP.)
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-dnsservers"></a>-dnsServers
Obligatoire lors de la création d’un réseau avec une IP statique. Tableau d’adresses IP pointant vers les serveurs DNS à utiliser pour le sous-réseau. Vous devez spécifier entre 1 et 3 serveurs. soit "8.8.8.8","192.168.1.1".

```yaml
Type: System.String[]
Parameter Sets: (StaticIP)
Aliases:

Required: False (This is required when creating a network with a static IP.)
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-ipaddressprefix"></a>-ipAddressPrefix
Préfixe d’adresse à utiliser pour l’affectation d’IP statiques.

```yaml
Type: System.String
Parameter Sets: (StaticIP)
Aliases:

Required: True
Position: Named
Default value: external
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vippoolstart"></a>-vipPoolStart
Adresse IP de début du pool d’adresses IP virtuelles. L’adresse doit se trouver dans la plage traitée par le serveur DHCP, ou dans celle qui est indiquée dans le CIDR du sous-réseau. Les adresses IP du pool d’adresses IP virtuelles seront utilisées pour le serveur d’API et pour les services Kubernetes. Si vous utilisez DHCP, assurez-vous que vos adresses IP virtuelles font partie de la réserve d’adresses IP DHCP. Si vous utilisez des IP statiques, vérifiez que vos IP virtuelles proviennent bien du même sous-réseau.

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

### <a name="-vippoolend"></a>-vipPoolEnd
Adresse IP de fin du pool d’adresses IP virtuelles. L’adresse doit se trouver dans la plage traitée par le serveur DHCP, ou dans celle qui est indiquée dans le CIDR du sous-réseau. Les adresses IP du pool d’adresses IP virtuelles seront utilisées pour le serveur d’API et pour les services Kubernetes. Si vous utilisez DHCP, assurez-vous que vos adresses IP virtuelles font partie de la réserve d’adresses IP DHCP. Si vous utilisez des IP statiques, vérifiez que vos IP virtuelles proviennent bien du même sous-réseau.

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

### <a name="-k8snodeippoolstart"></a>-k8sNodeIpPoolStart
Adresse IP de début d’un pool de machines virtuelles. L’adresse doit être comprise dans la plage du sous-réseau. Cette condition est obligatoire pour les déploiements d’IP statiques.

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

### <a name="-k8snodeippoolend"></a>-k8sNodeIpPoolEnd
Adresse IP de fin d’un pool de machines virtuelles. L’adresse doit être comprise dans la plage du sous-réseau. Cette condition est obligatoire pour les déploiements d’IP statiques.

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

### <a name="-vlanid"></a>-vlanID
ID VLAN pour le réseau spécifié. Si vous l’omettez, le réseau ne sera pas étiqueté.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

<!--- ### -macPoolName
The name of the MAC address pool that you wish to use for the Azure Kubernetes Service host VM. The pool will be created with the New-AksHciMacPoolSetting command.

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
--->
