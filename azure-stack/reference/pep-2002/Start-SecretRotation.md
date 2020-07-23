---
title: Point de terminaison privilégié Close-PrivilegedEndpoint pour Azure Stack Hub
description: Informations de référence sur le point de terminaison privilégié Azure Stack PowerShell - Close-PrivilegedEndpoint
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: e06d51a735daae8c82e1203f7f5e98bfbea47851
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486136"
---
# <a name="start-secretrotation"></a>Start-SecretRotation

## <a name="synopsis"></a>Synopsis
Déclenche la rotation des secrets sur un tampon.

## <a name="syntax"></a>Syntaxe

```
Start-SecretRotation [-PathAccessCredential <Object>] [-ReRun] [-CertificatePassword <Object>] [-Internal]
 [-PfxFilesPath <Object>] [-AsJob]
```

## <a name="description"></a>Description
Appelle le processus de rotation des secrets.

## <a name="parameters"></a>Paramètres

### <a name="-internal"></a>-Internal
 

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

### <a name="-pfxfilespath"></a>-PfxFilesPath
Chemin des nouveaux fichiers pfx partagés pour la rotation des certificats externes.
Ce paramètre est obligatoire pour activer la rotation des certificats externes.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-pathaccesscredential"></a>-PathAccessCredential
Informations d’identification pour accéder à PfxFilesPath.
Ce paramètre est obligatoire pour activer la rotation des certificats externes.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-certificatepassword"></a>-CertificatePassword
Mot de passe pour les nouveaux fichiers pfx fournis.
Ce paramètre est obligatoire pour activer la rotation des certificats externes.
Le mot de passe peut être différent du premier mot de passe pfx fourni au moment du déploiement initial.
Nous allons regénérer les fichiers pfx avec un mot de passe d’autorité de certification correct.

Utilisation ::
    # Rotates external certificates only
    Start-SecretRotation -PfxFilesPath \<String\> -PathAccessCredential \<PSCredential\> -CertificatePassword \<SecureString\>

    # Rotates internal secrets only
    Start-SecretRotation -Internal  

    # Reruns internal secrets only
    Start-SecretRotation -Internal -ReRun 

    # Reruns external certificates only
    Start-SecretRotation -ReRun

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-rerun"></a>-ReRun
 

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

Pour plus d’informations sur l’accès au point de terminaison privilégié et sur son utilisation, consultez [Utiliser le point de terminaison privilégié dans Azure Stack Hub](../../operator/azure-stack-privileged-endpoint.md).
