---
title: Configurer le serveur de temps pour Azure Stack Hub
description: Découvrez comment configurer le serveur de temps pour Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 2/19/2020
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 10/10/2019
ms.openlocfilehash: 56d0bfdef5329d5dd20c3b9add4d30e7942801e1
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77703534"
---
# <a name="configure-the-time-server-for-azure-stack-hub"></a>Configurer le serveur de temps pour Azure Stack Hub

Vous pouvez utiliser le point de terminaison privilégié pour mettre à jour le serveur de temps dans Azure Stack Hub. Utilisez un nom d’hôte qui se résout en deux adresses IP ou plus de serveur NTP.

Azure Stack Hub utilise le protocole NTP (Network Time Protocol) pour se connecter aux serveurs de temps sur Internet. Les serveurs NTP fournissent une heure système précise. L’heure est utilisée sur les commutateurs réseau physiques d’Azure Stack Hub, sur l’hôte de cycle de vie du matériel, sur le service d’infrastructure et sur les machines virtuelles. Si l’horloge n’est pas synchronisée, Azure Stack Hub risque de rencontrer des problèmes graves avec le réseau et l’authentification. Des fichiers journaux, des documents et d’autres fichiers peuvent être créés avec des horodatages incorrects.

Au moins un serveur de temps (NTP) est requis pour qu’Azure Stack Hub synchronise l’heure. Quand vous déployez Azure Stack Hub, vous fournissez l’adresse d’un serveur NTP. L’heure est un service d’infrastructure de centre de données critique. Si le service change, vous devez mettre à jour l’heure.

> [!NOTE]
> Azure Stack Hub prend en charge la synchronisation de l’heure avec un seul serveur de temps (NTP). Vous ne pouvez pas fournir plusieurs serveurs de temps avec lesquels Azure Stack Hub synchronise l’heure.

## <a name="configure-time"></a>Configurer l’heure

1. [Connectez-vous au point de terminaison privilégié](azure-stack-privileged-endpoint.md). 
    > [!Note]  
    > Il n’est pas nécessaire de déverrouiller le point de terminaison privilégié en ouvrant un ticket de support.

2. Exécutez la commande suivante pour examiner le serveur NTP configuré actuel :

    ```PowerShell
    Get-AzsTimeSource
    ```

3. Exécutez la commande suivante pour mettre à jour Azure Stack Hub afin d’utiliser le nouveau serveur NTP et de synchroniser l’heure immédiatement.

    > [!Note]  
    > Cette procédure ne met pas à jour le serveur de temps sur les commutateurs physiques. Si le serveur de temps n’est pas un serveur NTP basé sur Windows, vous devez ajouter l’indicateur `0x8`.

    ```PowerShell
    Set-AzsTimeSource -TimeServer NEWTIMESERVERIP -resync
    ```

    Pour les serveurs autres que les serveurs de temps basés sur Windows :

    ```PowerShell
    Set-AzsTimeSource -TimeServer "NEWTIMESERVERIP,0x8" -resync
    ```

4. Recherchez les erreurs éventuelles dans la sortie de la commande.


## <a name="next-steps"></a>Étapes suivantes

[Afficher le rapport de préparation](azure-stack-validation-report.md)  
[Considérations générales relatives à l’intégration d’Azure Stack Hub](azure-stack-datacenter-integration.md)  
