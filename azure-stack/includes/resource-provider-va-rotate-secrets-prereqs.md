---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 10/10/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 294f0f5b1f6ac45d53f5d3eebc222b3edba23776
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "93050267"
---
> [!NOTE]
> Pour les fournisseurs de ressources à valeur ajoutée, la rotation des secrets n'est actuellement prise en charge que via PowerShell. 

À l'instar de l'infrastructure Azure Stack Hub, les fournisseurs de ressources à valeur ajoutée utilisent à la fois des secrets internes et externes. Les secrets peuvent se présenter sous différentes formes, comme des mots de passe et des clés de chiffrement gérées par des certificats X509. En tant qu'opérateur, vous devez :

- fournir des secrets externes à jour, tels qu'un nouveau certificat TLS pour sécuriser les points de terminaison des fournisseurs de ressources ;
- gérer la rotation des secrets des fournisseurs de ressources sur une base régulière.

Pour préparer le processus de rotation :

1. Consultez [Exigences en matière de certificat pour infrastructure à clé publique (PKI) Azure Stack Hub](../operator/azure-stack-pki-certs.md#certificate-requirements) afin d'obtenir des informations préalables importantes avant d'acquérir/de renouveler votre certificat X509, avec des détails sur le format PFX requis. Consultez également la section [Certificats PaaS facultatifs](../operator/azure-stack-pki-certs.md#optional-paas-certificates) pour connaître les exigences relatives à votre fournisseur de ressources à valeur ajoutée spécifique.

2. Si ce n'est déjà fait, veillez à [Installer le module PowerShell Az pour Azure Stack Hub](../operator/powershell-install-az-module.md) avant de continuer. La version 2.0.2-preview (ou ultérieure) est requise pour la rotation des secrets Azure Stack Hub. Pour plus d'informations, consultez [Effectuer une migration d'AzureRM vers Azure PowerShell Az dans Azure Stack Hub](../operator/migrate-azurerm-az.md).
