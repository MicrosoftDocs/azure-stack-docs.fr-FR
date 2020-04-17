---
title: Mettre à jour le redirecteur DNS dans Azure Stack Hub
description: Découvrez comment mettre à jour le redirecteur DNS dans Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 6d5138868b07e2a3d552cd154976352266941760
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77704469"
---
# <a name="update-the-dns-forwarder-in-azure-stack-hub"></a>Mettre à jour le redirecteur DNS dans Azure Stack Hub

Au moins un redirecteur DNS accessible est nécessaire pour que l’infrastructure Azure Stack Hub résolve les noms externes. Un redirecteur DNS doit être fourni pour le déploiement d’Azure Stack Hub. Cette entrée est utilisée pour les serveurs DNS internes d’Azure Stack Hub en tant que redirecteur et permet de résoudre les noms externes pour des services tels que l’authentification, l’utilisation ou la gestion de la Place de marché.

DNS est un service d’infrastructure de centre de données critique qui peut changer, auquel cas Azure Stack Hub doit être mis à jour.

Cet article décrit l’utilisation du point de terminaison privilégié (PEP) pour mettre à jour le redirecteur DNS dans Azure Stack Hub. Il est recommandé d’utiliser deux adresses IP fiables de redirecteur DNS.

1. Connectez-vous au [point de terminaison privilégié](azure-stack-privileged-endpoint.md). Notez qu’il n’est pas nécessaire de déverrouiller le point de terminaison privilégié en ouvrant un ticket de support.

2. Exécutez la commande suivante pour passer en revue le redirecteur DNS configuré actuel. Une alternative consiste à utiliser les propriétés de région du portail d’administration :

   ```powershell
   Get-AzsDnsForwarder
   ```

3. Exécutez la commande suivante pour mettre à jour Azure Stack Hub afin d’utiliser le nouveau redirecteur DNS :

   ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1","IPAddress 2"
   ```

4. Recherchez les erreurs éventuelles dans la sortie de la commande.

## <a name="next-steps"></a>Étapes suivantes

[Intégration du pare-feu](azure-stack-firewall.md)
