---
title: Compréhension d’iDNS dans Azure Stack | Microsoft Docs
description: Compréhension des fonctionnalités iDNS dans Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 36a80e0ce9a53b3ea2bdd7ddef990123af9e2669
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985935"
---
# <a name="introducing-idns-for-azure-stack"></a>Présentation d’iDNS pour Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

iDNS est une fonctionnalité de mise en réseau Azure Stack qui vous permet de résoudre les noms DNS externes (par exemple, https:\//www.bing.com.) Elle vous permet également d’enregistrer des noms de réseau virtuel interne. De cette façon, vous pouvez résoudre des machines virtuelles sur le même réseau virtuel par nom plutôt que par adresse IP. Cette approche évite la nécessité de fournir des entrées de serveur DNS personnalisées. Pour plus d’informations sur DNS, consultez l’article [Vue d’ensemble d’Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>Que fait iDNS ?

Avec iDNS dans Azure Stack, vous obtenez les fonctionnalités suivantes, sans avoir à spécifier d’entrées de serveur DNS personnalisées.

- Services de résolution de noms DNS partagés pour les charges de travail de locataire.
- Service DNS faisant autorité pour la résolution de noms et l’enregistrement DNS dans le réseau virtuel du locataire.
- Service DNS récursif pour la résolution de noms Internet à partir de machines virtuelles de locataire. Les locataires n’ont plus besoin de spécifier des entrées DNS personnalisées pour résoudre les noms Internet (par exemple, www.bing.com).

Vous pouvez toujours configurer votre propre DNS et utiliser des serveurs DNS personnalisés. Toutefois, en utilisant iDNS, vous pouvez résoudre les noms DNS Internet et vous connecter à d’autres machines virtuelles du même réseau virtuel sans avoir à créer d’entrées DNS personnalisées.

## <a name="what-doesnt-idns-do"></a>Quelles sont les opérations non prises en charge par iDNS ?

iDNS ne vous permet pas de créer un enregistrement DNS pour un nom qui peut être résolu en dehors du réseau virtuel.

Dans Azure, vous avez la possibilité de spécifier une étiquette de nom DNS qui est associée à une adresse IP publique. Vous pouvez choisir l’étiquette (préfixe), mais Azure choisit le suffixe, qui est basé sur la région dans laquelle vous créez l’adresse IP publique.

![Exemple d’étiquette de nom DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Dans l’image ci-dessus, Azure crée un enregistrement « A » dans DNS pour l’étiquette de nom DNS spécifiée dans la zone **westus.cloudapp.azure.com**. Le préfixe et le suffixe combinés représentent un [nom de domaine complet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) pouvant être résolu n’importe où sur le réseau Internet public.

Azure Stack prend en charge iDNS uniquement pour l’enregistrement de noms internes. Dans ce sens, il ne peut pas effectuer les opérations suivantes :

- Créer un enregistrement DNS dans une zone DNS hébergée existante (par exemple, local.azurestack.external).
- Créer une zone DNS (par exemple, Contoso.com).
- Créer un enregistrement dans votre propre zone DNS personnalisée.
- Prendre en charge l’achat de noms de domaine.

## <a name="next-steps"></a>Étapes suivantes

[DNS dans Azure Stack](azure-stack-dns.md)
