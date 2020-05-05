---
title: Point de terminaison privilégié Register-CustomAdfs pour Azure Stack Hub
description: Informations de référence sur le point de terminaison privilégié Azure Stack PowerShell - Register-CustomAdfs
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: fd40c3930705d450d7e48b373f209b6e2ce7d218
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563757"
---
# <a name="register-customadfs"></a>Register-CustomAdfs

## <a name="synopsis"></a>Synopsis
Script pour inscrire un service AD FS (Active Directory Federation Service) personnalisé en tant que fournisseur de revendications auprès d’Azure Stack AD FS

## <a name="syntax"></a>Syntaxe

```
Register-CustomAdfs [-CustomADFSFederationMetadataEndpointUri <Object>]
 [-CustomADFSFederationMetadataFileContent <Object>] [-TimeoutInSecs <Object>] [-CustomAdfsName <Object>]
 [-SigningCertificateRevocationCheck <Object>] [-EncryptionCertificateRevocationCheck <Object>] [-AsJob]
```

## <a name="description"></a>Description
Script pour inscrire un service AD FS (Active Directory Federation Service) personnalisé en tant que fournisseur de revendications auprès d’Azure Stack AD FS

## <a name="examples"></a>Exemples

### <a name="example-1"></a>Exemple 1
```
Register-CustomAdfs -CustomAdfsName "Contoso" -CustomADFSFederationMetadataEndpointUri "https://adfs.contoso.com/federationmetadata/2007-06/federationmetadata.xml"  -TimeoutInSecs 1000
```

### <a name="example-2"></a>Exemple 2
```
Register-CustomAdfs -CustomAdfsName "Contoso" -CustomADFSFederationMetadataFile "c:\temp\FederationMetadata.xml" -TimeoutInSecs 1000
```
## <a name="parameters"></a>Paramètres

### <a name="-customadfsname"></a>-CustomAdfsName
 

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

### <a name="-customadfsfederationmetadataendpointuri"></a>-CustomADFSFederationMetadataEndpointUri
 

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

### <a name="-customadfsfederationmetadatafilecontent"></a>-CustomADFSFederationMetadataFileContent
 

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

### <a name="-encryptioncertificaterevocationcheck"></a>-EncryptionCertificateRevocationCheck
 

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

### <a name="-signingcertificaterevocationcheck"></a>-SigningCertificateRevocationCheck
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: CheckChainExcludeRoot
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 1000
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