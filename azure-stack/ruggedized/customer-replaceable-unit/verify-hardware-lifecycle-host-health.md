---
title: Vérifier l’intégrité de l’hôte de cycle de vie du matériel
description: Découvrez comment vérifier l’intégrité de l’hôte de cycle de vie du matériel.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 47d3d5198418042ad0bc24e35414bd309a492bc2
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97909852"
---
# <a name="verifying-hardware-lifecycle-host-health"></a>Vérification de l’intégrité de l’hôte de cycle de vie du matériel



Une fois le remplacement du matériel terminé, et AVANT de quitter le site, utilisez l’iDRAC pour vérifier l’intégrité de l’hôte de cycle de vie du matériel.


1.  Vérifiez l’intégrité du système dans l’interface web de l’iDRAC, et vérifiez que le problème sur lequel vous avez travaillé a disparu, s’il était présent dans l’interface web avant le processus FRU.

    ![Capture d’écran montrant la page « Système » avec les actions « Présentation » mises en surbrillance.](media/image-5.png)
    
2.  Vérifiez que les machines virtuelles Hyper-V sont à l’état **En cours d’exécution**.

    ![Capture d’écran montrant la page « Gestionnaire Hyper-V » avec la section « Machines virtuelles » mise en surbrillance.](media/image-55.png) 

