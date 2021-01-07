---
title: Configurer les contrôles de sécurité dans Azure Stack Hub
description: Découvrez comment configurer les contrôles de sécurité dans Azure Stack Hub.
author: PatAltimore
ms.topic: how-to
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 06/17/2019
ms.openlocfilehash: 2ba13915c572c4b71cd5261ddb08cb876dd137a5
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869251"
---
# <a name="configure-azure-stack-hub-security-controls"></a>Configurer les contrôles de sécurité d’Azure Stack Hub

Cet article décrit les contrôles de sécurité qui peuvent être modifiés dans Azure Stack Hub et met en évidence les compromis éventuels.

L’architecture d’Azure Stack Hub repose sur deux principes de sécurité de base : violations présumées et renforcement par défaut. Pour plus d’informations sur la sécurité d’Azure Stack Hub, consultez [Situation de sécurité de l’infrastructure Azure Stack Hub](azure-stack-security-foundations.md). Si la situation de sécurité par défaut d’Azure Stack Hub est « prêt pour la production », certains scénarios de déploiement nécessitent un renforcement supplémentaire.

## <a name="tls-version-policy"></a>Stratégie de version du protocole TLS

Le protocole TLS est un protocole de chiffrement largement utilisé pour établir des communications chiffrées sur le réseau. Le protocole TLS a évolué au fil du temps et plusieurs versions ont été publiées. L’infrastructure Azure Stack Hub utilise exclusivement le protocole TLS 1.2 pour toutes les communications. Pour les interfaces externes, Azure Stack Hub utilise actuellement par défaut le protocole TLS 1.2. Toutefois, pour assurer la compatibilité descendante, il prend également en charge la négociation via les protocoles TLS 1.1 et 1.0. Lorsqu'un client TLS demande à communiquer via le protocole TLS 1.1 ou TLS 1.0, Azure Stack Hub satisfait la demande en négociant avec une version antérieure du protocole TLS. Si le client demande à utiliser le protocole TLS 1.2, Azure Stack Hub établit une connexion TLS à l’aide du protocole TLS 1.2.

Les protocoles TLS 1.0 et 1.1 étant progressivement abandonnés ou interdits par des organisations et des normes de conformité, à compter de la mise à jour 1906, vous pouvez configurer la stratégie de protocole TLS dans Azure Stack Hub. Vous pouvez appliquer une stratégie de protocole exclusivement TLS 1.2, où toute tentative d’établissement d’une session TLS avec une version antérieure à la version 1.2 n’est pas autorisée et est rejetée.

> [!IMPORTANT]
> Microsoft recommande d’utiliser une stratégie de protocole exclusivement TLS 1.2 pour les environnements de production d'Azure Stack Hub.

## <a name="get-tls-policy"></a>Obtenir la stratégie de protocole TLS

Utilisez le [point de terminaison privilégié (PEP)](azure-stack-privileged-endpoint.md) pour afficher la stratégie de protocole TLS pour tous les points de terminaison Azure Stack Hub :

```powershell
Get-TLSPolicy
```

Exemple de sortie :

```powershell
TLS_1.2
```

## <a name="set-tls-policy"></a>Définir une stratégie de protocole TLS

Utilisez le [point de terminaison privilégié (PEP)](azure-stack-privileged-endpoint.md) pour définir la stratégie de protocole TLS pour tous les points de terminaison Azure Stack Hub :

```powershell
Set-TLSPolicy -Version <String>
```

Paramètres pour la cmdlet *Set-TLSPolicy* :

| Paramètre | Description | Type | Obligatoire |
|-----|-----|-----|-----|
| *Version* | Version(s) autorisée(s) du protocole TLS dans Azure Stack Hub | String | Oui |

Utilisez l’une des valeurs suivantes pour configurer les versions de protocole TLS autorisées pour tous les points de terminaison Azure Stack Hub :

| Valeur de la version | Description |
|-------|-------|
| *TLS_All* | Les points de terminaison de protocole TLS Azure Stack Hub prennent en charge le protocole TLS 1.2, mais la négociation via les protocoles TLS 1.1 et TLS 1.0 est autorisée. |
| *TLS_1.2* | Les points de terminaison de protocole TLS Azure Stack Hub prennent uniquement en charge le protocole TLS 1.2. |

La mise à jour de la stratégie de protocole TLS prend quelques minutes.

### <a name="enforce-tls-12-configuration-example"></a>Exemple de configuration Appliquer le protocole TLS 1.2

Cet exemple définit votre stratégie de protocole TLS de façon à appliquer uniquement le protocole TLS 1.2.

```powershell
Set-TLSPolicy -Version TLS_1.2
```

Exemple de sortie :

```powershell
VERBOSE: Successfully setting enforce TLS 1.2 to True
VERBOSE: Invoking action plan to update GPOs
VERBOSE: Create Client for execution of action plan
VERBOSE: Start action plan
<...>
VERBOSE: Verifying TLS policy
VERBOSE: Get GPO TLS protocols registry 'enabled' values
VERBOSE: GPO TLS applied with the following preferences:
VERBOSE:     TLS protocol SSL 2.0 enabled value: 0
VERBOSE:     TLS protocol SSL 3.0 enabled value: 0
VERBOSE:     TLS protocol TLS 1.0 enabled value: 0
VERBOSE:     TLS protocol TLS 1.1 enabled value: 0
VERBOSE:     TLS protocol TLS 1.2 enabled value: 1
VERBOSE: TLS 1.2 is enforced
```

### <a name="allow-all-versions-of-tls-12-11-and-10-configuration-example"></a>Exemple de configuration Autoriser toutes les versions du protocole TLS (1.2, 1.1 et 1.0)

Cet exemple définit votre stratégie de protocole TLS pour autoriser toutes les versions du protocole TLS (1.2, 1.1 et 1.0).

```powershell
Set-TLSPolicy -Version TLS_All
```

Exemple de sortie :

```powershell
VERBOSE: Successfully setting enforce TLS 1.2 to False
VERBOSE: Invoking action plan to update GPOs
VERBOSE: Create Client for execution of action plan
VERBOSE: Start action plan
<...>
VERBOSE: Verifying TLS policy
VERBOSE: Get GPO TLS protocols registry 'enabled' values
VERBOSE: GPO TLS applied with the following preferences:
VERBOSE:     TLS protocol SSL 2.0 enabled value: 0
VERBOSE:     TLS protocol SSL 3.0 enabled value: 0
VERBOSE:     TLS protocol TLS 1.0 enabled value: 1
VERBOSE:     TLS protocol TLS 1.1 enabled value: 1
VERBOSE:     TLS protocol TLS 1.2 enabled value: 1
VERBOSE: TLS 1.2 is not enforced
```

## <a name="next-steps"></a>Étapes suivantes

- [Découvrez les conditions de sécurité de l’infrastructure Azure Stack Hub](azure-stack-security-foundations.md).
- [Sachez comment procéder à la rotation de vos secrets dans Azure Stack Hub](azure-stack-rotate-secrets.md).
- [Mettez à jour l’antivirus Windows Defender sur Azure Stack Hub](azure-stack-security-av.md).
