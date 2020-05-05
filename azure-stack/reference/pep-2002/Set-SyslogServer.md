---
title: Point de terminaison privilégié Close-PrivilegedEndpoint pour Azure Stack Hub
description: Informations de référence sur le point de terminaison privilégié Azure Stack PowerShell - Close-PrivilegedEndpoint
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 1b0df5892149d8cd351e3a222b11cf63c9d37759
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563625"
---
# <a name="set-syslogserver"></a>Set-SyslogServer

## <a name="synopsis"></a>Synopsis
Définit le point de terminaison du serveur Syslog.

## <a name="syntax"></a>Syntaxe

```
Set-SyslogServer [-Remove] [[-ServerName] <Object>] [-NoEncryption] [-SkipCertificateCheck] [-UseUDP]
 [-SkipCNCheck] [[-ServerPort] <Object>] [-AsJob]
```


## <a name="examples"></a>Exemples

### <a name="example-1"></a>Exemple 1

```
Set-SyslogServer -ServerName <FQDN or IP address of Syslog server>
```

### <a name="example-2"></a>Exemple 2
```
-NoEncryption
```

### <a name="example-3"></a>Exemple 3
```
-SkipCertificateCheck
```

### <a name="example-4"></a>Exemple 4
```
-SkipCNCheck
```

### <a name="example-5"></a>Exemple 5
```
-UseUDP
```

### <a name="example-6"></a>Exemple 6
```
Set-SyslogServer -Remove
```

## <a name="parameters"></a>Paramètres

### <a name="-servername"></a>-ServerName
Nom de domaine complet (FQDN) ou adresse IPv4 du serveur Syslog.
Sans autre paramètre, le protocole par défaut est TCP avec le chiffrement, la validation du certificat et la vérification du nom du certificat activés.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-serverport"></a>-ServerPort
Port du serveur Syslog.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: 0
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-noencryption"></a>-NoEncryption
Spécifiez NoEncryption si vous souhaitez désactiver le chiffrement pour le trafic TCP.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-skipcncheck"></a>-SkipCNCheck
Spécifiez SkipCNCheck si vous souhaitez ignorer la vérification du nom du certificat.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-skipcertificatecheck"></a>-SkipCertificateCheck
Spécifiez SkipCertificateCheck si vous souhaitez ignorer la vérification du certificat du serveur Syslog.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-useudp"></a>-UseUDP
Spécifiez UseUDP si le serveur Syslog utilise le protocole UDP.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-remove"></a>-Remove
 

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-asjob"></a>-AsJob


```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’accès au point de terminaison privilégié et sur son utilisation, consultez [Utiliser le point de terminaison privilégié dans Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).