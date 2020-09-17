---
title: Applet de commande de point de terminaison privilégié Start-SecretRotation pour Azure Stack Hub
description: Informations de référence sur le point de terminaison privilégié Azure Stack Hub PowerShell - Start-SecretRotation
author: mattbriggs
ms.topic: reference
ms.date: 07/29/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 8a3dee9ad26c37626c0f3383b874f149be4fac01
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742606"
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
Appelle le processus de rotation des secrets pour les secrets d’infrastructure d’un système Azure Stack Hub. Par défaut, elle effectue uniquement la rotation des certificats des points de terminaison d’infrastructure réseau externes. Pour plus de détails, consultez [Effectuer la rotation des secrets dans Azure Stack Hub](../../operator/azure-stack-rotate-secrets.md).

## <a name="parameters"></a>Paramètres

### <a name="-internal"></a>-Internal
Effectuez une rotation des secrets pour les points de terminaison d’infrastructure réseau internes.

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

```console
    # Rotates external certificates only
    Start-SecretRotation -PfxFilesPath \<String\> -PathAccessCredential \<PSCredential\> -CertificatePassword \<SecureString\>

    # Rotates internal secrets only
    Start-SecretRotation -Internal  

    # Reruns internal secrets only
    Start-SecretRotation -Internal -ReRun 

    # Reruns external certificates only
    Start-SecretRotation -ReRun
```

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
