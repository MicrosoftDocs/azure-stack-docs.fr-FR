---
title: Systèmes d’exploitation invités pris en charge sur Azure Stack Hub
titleSuffix: Azure Stack
description: Découvrez les systèmes d’exploitation invités pouvant être utilisés sur Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/06/2019
ms.openlocfilehash: a731c7c72f38fee0fb37fc8b82e55799d5b80d3b
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77696938"
---
# <a name="guest-operating-systems-supported-on-azure-stack-hub"></a>Systèmes d’exploitation invités pris en charge sur Azure Stack Hub

## <a name="windows"></a>Windows

Azure Stack Hub prend en charge les systèmes d’exploitation invités Windows listés dans le tableau suivant :

| Système d’exploitation | Description | Disponible sur la Place de marché Azure Stack Hub |
| --- | --- | --- |
| Windows Server, version 1709 | 64 bits | Core avec conteneurs |
| Windows Server 2019 | 64 bits |  Centre de données, centre de données principal, centre de données avec conteneurs |
| Windows Server 2016 | 64 bits |  Centre de données, centre de données principal, centre de données avec conteneurs |
| Windows Server 2012 R2 | 64 bits |  Centre de données |
| Windows Server 2012 | 64 bits |  Centre de données |
| Windows Server 2008 R2 SP1 | 64 bits |  Centre de données |
| Windows Server 2008 SP2 | 64 bits |  Apportez votre propre image |
| Windows 10 *(voir remarque 1)* | 64 bits, Professionnel et Entreprise | Apportez votre propre image |

> [!NOTE]
> Pour déployer des systèmes d’exploitation clients Windows 10 sur Azure Stack Hub, vous devez disposer de [licences Windows par utilisateur](https://www.microsoft.com/licensing/product-licensing/windows10.aspx) ou en acheter via le programme [QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx) (Qualified Multitenant Hoster).

Les images de la Place de marché sont disponibles avec un système de licence de paiement à l’utilisation ou BYOL (apportez votre propre licence) (EA/SPLA). L’utilisation des deux sur une instance unique d'Azure Stack Hub n’est pas prise en charge. Au cours du déploiement, Azure Stack Hub injecte la version appropriée de l’agent invité dans l’image.

Les éditions de centre de données sont disponibles au téléchargement sur la Place de marché Azure Stack Hub ; Les clients peuvent apporter leurs propres images de serveur, y compris d’autres éditions. Les images des clients Windows ne sont pas disponibles sur la Place de marché Azure Stack Hub.

## <a name="linux"></a>Linux

Les distributions Linux répertoriées comme disponibles sur la Place de marché Azure Stack Hub incluent l’agent Windows Azure Linux (WALA) nécessaire. Si vous ajoutez votre propre image à Azure Stack, suivez les instructions de l’article [Ajouter des images Linux à Azure Stack](azure-stack-linux.md).

> [!NOTE]
> Les images personnalisées doivent être générées avec la dernière version publique de WALA (sur la build 1903 ou ultérieure d’Azure Stack Hub, ou avec le correctif logiciel 1901/1902), ou la version 2.2.20. Les versions antérieures à la version 2.2.20 et celles comprises entre les versions 2.2.21 et 2.2.34 (incluses) risquent de ne pas fonctionner correctement sur Azure Stack Hub. Sur Azure Stack Hub 1910 et les versions ultérieures, toutes les versions de l’agent Azure WALA fonctionnent avec Azure Stack Hub.
>
> [cloud-init](https://cloud-init.io/) est pris en charge sur Azure Stack Hub 1910 et les versions ultérieures.

| Distribution | Description | Serveur de publication | Place de marché Azure Stack Hub |
| --- | --- | --- | --- |
| CentOS-based 6.9 | 64 bits | Rogue Wave | Oui |
| CentOS-based 7.5 | 64 bits | Rogue Wave | Oui |
| Basé sur CentOS 7.3 | 64 bits | Rogue Wave | Oui |
| ClearLinux | 64 bits | ClearLinux.org | Oui |
| CoreOS Linux (Stable) |  64 bits | CoreOS | Oui |
| Debian 8 Jessie | 64 bits | credativ |  Oui |
| Debian 9 "Stretch" | 64 bits | credativ | Oui |
| Oracle Linux | 64 bits | Oracle | Oui |
| Red Hat Enterprise Linux 7.1 (et ultérieur) | 64 bits | Red Hat | Apportez votre propre image |
| SLES 11SP4 | 64 bits | SUSE | Oui |
| SLES 12SP3 | 64 bits | SUSE | Oui |
| Ubuntu 14.04-LTS | 64 bits | Canonical | Oui |
| Ubuntu 16.04-LTS | 64 bits | Canonical | Oui |
| Ubuntu 18.04-LTS | 64 bits | Canonical | Oui |

Pour plus d’informations sur la prise en charge Red Hat Enterprise Linux, consultez [Red Hat and Azure Stack Hub : Forum aux questions](https://access.redhat.com/articles/3413531).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la Place de marché Azure Stack Hub, consultez les articles suivants :

- [Télécharger des articles du Marketplace](azure-stack-download-azure-marketplace-item.md)  
- [Créer et publier un élément de Place de Marché](azure-stack-create-and-publish-marketplace-item.md)
