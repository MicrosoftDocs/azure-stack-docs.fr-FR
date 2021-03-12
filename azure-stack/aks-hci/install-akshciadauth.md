---
title: Install-AksHciAdAuth
author: jessicaguan
description: La commande PowerShell Install-AksHciAdAuth installe l’authentification Active Directory.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: d89861da54cf7fef44d8da57077aba9f372022c3
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874394"
---
# <a name="install-akshciadauth"></a>Install-AksHciAdAuth

## <a name="synopsis"></a>Synopsis
Installe l’authentification Active Directory.

## <a name="syntax"></a>Syntaxe

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminUser <String>
                     [-TTL]    
                    
```

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminGroup <String>    
                     [-TTL]    
                    
```

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminUserSID <String>
                     [-TTL]    
                    
```

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminGroupSID <String>    
                     [-TTL]    
                    
```

## <a name="description"></a>Description
Installe l’authentification Active Directory.

## <a name="examples"></a>EXEMPLES

### <a name="example"></a>Exemple
```powershell
PS C:\> Install-AksHciAdAuth -name mynewcluster1 -keytab <.\current.keytab> -previousKeytab <.\previous.keytab> -SPN <service/principal@CONTOSO.COM> -adminUser CONTOSO\Bob
```

## <a name="parameters"></a>Paramètres

### <a name="-name"></a>-name
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

### <a name="-keytab"></a>-keytab
Chemin de fichier où se trouve le fichier keytab. Vérifiez que le fichier se nomme `current.keytab`.

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

### <a name="-previouskeytab"></a>-previousKeytab
Ancien fichier keytab utilisé avant la mise à jour du mot de passe du compte Active Directory. Celui-ci est obligatoire pour la modification des mots de passe Active Directory.

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

### <a name="-spn"></a>-SPN
Nom du principal de service associé au compte AD du serveur d’API.

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

### <a name="-adminuser"></a>-adminUser
Nom d’utilisateur de l’administrateur auquel accorder des autorisations d’administrateur de cluster.

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

### <a name="-admingroup"></a>-adminGroup
Nom du groupe auquel accorder des autorisations d’administrateur de cluster.

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

### <a name="-adminusersid"></a>-adminUserSID
Nom du SID utilisateur auquel accorder des autorisations d’administrateur de cluster.

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

### <a name="-admingroupsid"></a>-adminGroupSID
Nom du SID utilisateur auquel accorder des autorisations d’administrateur de cluster.

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

### <a name="-ttl"></a>-TTL
Durée de vie (en heures) pour `-previousKeytab`, si fournie. La durée par défaut est de 10 heures.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 10
Accept pipeline input: False
Accept wildcard characters: False
```
