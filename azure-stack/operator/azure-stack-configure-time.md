---
title: Configurer le serveur de temps pour Azure Stack | Microsoft Docs
description: Découvrez comment configurer le serveur de temps pour Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/10/2019
ms.openlocfilehash: cc432538715c1c990a9efe6473b33303deb78734
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72280552"
---
# <a name="configure-the-time-server-for-azure-stack"></a>Configurer le serveur de temps pour Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*  

Vous pouvez utiliser le point de terminaison privilégié pour mettre à jour le serveur de temps dans Azure Stack. Utilisez un nom d’hôte qui se résout en deux adresses IP ou plus de serveur NTP.

Azure Stack utilise le protocole NTP (Network Time Protocol) pour se connecter aux serveurs de temps sur Internet. Les serveurs NTP fournissent une heure système précise. L’heure est utilisée sur les commutateurs réseau physiques d’Azure Stack, sur l’hôte de cycle de vie du matériel, sur le service d’infrastructure et sur les machines virtuelles. Si l’horloge n’est pas synchronisée, Azure Stack risque de rencontrer des problèmes graves avec le réseau et l’authentification. Des fichiers journaux, des documents et d’autres fichiers peuvent être créés avec des horodatages incorrects.

Au moins un serveur de temps (NTP) est nécessaire pour qu’Azure Stack synchronise l’heure. Quand vous déployez Azure Stack, vous fournissez l’adresse d’un serveur NTP. L’heure est un service d’infrastructure de centre de données critique. Si le service change, vous devez mettre à jour l’heure.

## <a name="configure-time"></a>Configurer l’heure

1. [Connectez-vous au point de terminaison privilégié](azure-stack-privileged-endpoint.md). 
    > [!Note]  
    > Il n’est pas nécessaire de déverrouiller le point de terminaison privilégié en ouvrant un ticket de support.

2. Exécutez la commande suivante pour examiner le serveur NTP configuré actuel :

    ```PowerShell
    Get-AzsTimeSource
    ```

3. Exécutez la commande suivante pour mettre à jour Azure Stack afin d’utiliser le nouveau serveur NTP et de synchroniser l’heure immédiatement.

    > [!Note]  
    > Cette procédure ne met pas à jour le serveur de temps sur les commutateurs physiques

    ```PowerShell
    Set-AzsTimeSource -TimeServer NEWTIMESERVERIP -resync
    ```

4. Recherchez les erreurs éventuelles dans la sortie de la commande.


## <a name="next-steps"></a>Étapes suivantes

[Afficher le rapport de préparation](azure-stack-validation-report.md)  
[Considérations générales relatives à l’intégration de Microsoft Azure Stack](azure-stack-datacenter-integration.md)  
