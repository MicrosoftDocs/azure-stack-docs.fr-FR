---
title: Configurer les contrôles de sécurité d’Azure Stack
description: Découvrez comment configurer les contrôles de sécurité dans Azure Stack
services: azure-stack
author: PatAltimore
ms.service: azure-stack
ms.topic: article
ms.date: 06/17/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 06/17/2019
ms.openlocfilehash: b36a6d826dc7249f10b4785b27511096e45923a9
ms.sourcegitcommit: 7348876a97e8bed504b5f5d90690ec8d1d9472b0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67557854"
---
# <a name="configure-azure-stack-security-controls"></a>Configurer les contrôles de sécurité d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit les contrôles de sécurité qui peuvent être modifiés dans Azure Stack et met en évidence les compromis éventuels.

L’architecture d’Azure Stack repose sur deux principes de sécurité de base : violations présumées et renforcement par défaut. Pour plus d’informations sur la sécurité d’Azure Stack, voir [Situation de sécurité de l’infrastructure Azure Stack](azure-stack-security-foundations.md). Si la situation de sécurité par défaut d’Azure Stack est « prêt pour la production », certains scénarios de déploiement nécessitent un renforcement supplémentaire.

## <a name="tls-version-policy"></a>Stratégie de version du protocole TLS

Le protocole TLS est un protocole de chiffrement largement utilisé pour établir une communication chiffrée via le réseau. Le protocole TLS a évolué au fil du temps et plusieurs versions ont été publiées. L’infrastructure Azure Stack utilise exclusivement le protocole TLS 1.2 pour toutes les communications. Pour les interfaces externes, Azure Stack utilise actuellement par défaut le protocole TLS 1.2. Toutefois, pour assurer la compatibilité descendante, il prend également en charge la négociation via les protocoles TLS 1.1 et 1.0. Quand un client TLS demande à communiquer via le protocole TLS 1.1 ou TLS 1.0, Azure Stack satisfait la demande en négociant avec une version antérieure du protocole TLS. Si le client demande à utiliser le protocole TLS 1.2, Azure Stack établit une connexion TLS à l’aide du protocole TLS 1.2.

Étant donné que les protocoles TLS 1.0 et 1.1 sont progressivement abandonnés ou interdits par des organisations et des normes de conformité, depuis la mise à jour 1906, vous pouvez configurer la stratégie de protocole TLS dans Azure Stack. Vous pouvez appliquer une stratégie de protocole exclusivement TLS 1.2 où toute tentative d’établissement d’une session TLS avec une version antérieure à la version 1.2 n’est pas autorisée et rejetée.

> [!IMPORTANT]
> Microsoft recommande d’utiliser une stratégie de protocole exclusivement TLS 1.2 pour les environnements de production de Azure Stack.

## <a name="get-tls-policy"></a>Obtenir la stratégie de protocole TLS

Utilisez le [point de terminaison privilégié (PEP)](azure-stack-privileged-endpoint.md) pour afficher la stratégie de protocole TLS pour tous les points de terminaison Azure Stack :

```powershell
Get-TLSPolicy
```

Sortie de l’exemple :

    TLS_1.2

## <a name="set-tls-policy"></a>Définir une stratégie de protocole TLS

Utilisez le [point de terminaison privilégié (PEP)](azure-stack-privileged-endpoint.md) pour définir la stratégie de protocole TLS pour tous les points de terminaison Azure Stack :

```powershell
Set-TLSPolicy -Version <String>
```

Paramètres pour la cmdlet *Set-TLSPolicy* :

| Paramètre | Description | Type | Obligatoire |
|---------|---------|---------|---------|
| *Version* | Version(s) autorisée(s) du protocole TLS dans Azure Stack | Chaîne | Oui|

Utilisez l’une des valeurs suivantes pour configurer les versions de protocole TLS autorisées pour tous les points de terminaison Azure Stack :

| Valeur de la version | Description |
|---------|---------|
| *TLS_All* | Les points de terminaison de protocole TLS Azure Stack prennent en charge le protocole TLS 1.2, mais la négociation via les protocoles TLS 1.1 et TLS 1.0 est autorisée. |
| *TLS_1.2* | Les points de terminaison de protocole TLS Azure Stack prennent en charge uniquement le protocole TLS 1.2. | 

La mise à jour de la stratégie de protocole TLS prend quelques minutes.

### <a name="enforce-tls-12-configuration-example"></a>Exemple de configuration Appliquer le protocole TLS 1.2

Cet exemple définit votre stratégie de protocole TLS de façon à appliquer uniquement le protocole TLS 1.2.

```powershell
Set-TLSPolicy -Version TLS_1.2
```

Sortie de l’exemple :

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

### <a name="allow-all-versions-of-tls-12-11-and-10-configuration-example"></a>Exemple de configuration Autoriser toutes les versions du protocole TLS (1.2, 1.1 et 1.0)

Cet exemple définit votre stratégie de protocole TLS pour autoriser toutes les versions du protocole TLS (1.2, 1.1 et 1.0).

```powershell
Set-TLSPolicy -Version TLS_All
```

Sortie de l’exemple :

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

## <a name="next-steps"></a>Étapes suivantes

- [Découvrir la situation de sécurité de l’infrastructure Azure Stack](azure-stack-security-foundations.md)
- [Apprendre à faire pivoter vos clés secrètes dans Azure Stack](azure-stack-rotate-secrets.md)
- [Mettre à jour l’antivirus Windows Defender sur Azure Stack](azure-stack-security-av.md)
