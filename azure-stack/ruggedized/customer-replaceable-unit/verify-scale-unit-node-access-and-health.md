---
title: Vérifier l’accès et l’intégrité des nœuds d’unité d’échelle
description: Découvrez comment vérifier l’accès et l’intégrité des nœuds d’unité d’échelle
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: cec29fed89fa02f6a5142f9eb179d8fc8437f37d
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392681"
---
# <a name="verifying-scale-unit-node-access-and-health"></a>Vérification de l’accès et de l’intégrité des nœuds d’unité d’échelle



Connectez-vous à la station de travail à accès privilégié, lancez le portail d’administration, vérifiez l’intégrité du système, obtenez les adresses IP des points de terminaison privilégiés et identifiez les nœuds qui devront être vidés ou repris.

1.  À l’aide du Bureau à distance, connectez-vous à la station de travail à accès privilégié.

2.  Ouvrez le portail administrateur Azure Stack Hub.

    Connectez-vous au portail administrateur Azure Stack Hub avec les informations d’identification fournies par le client.
        
3.  Obtenez les adresses IP des points de terminaison privilégiés.


    Sélectionnez la vignette **Region Management**, puis **Properties**. Faites défiler vers le bas du volet et recherchez les adresses IP dans le champ **Privileged endpoint IP addresses**. Prenez-en note, car elles seront peut-être nécessaires plus loin dans cette procédure, ou pourront être demandées par le support technique en cas de problème.

    [![](media/image-18-inline.png)](media/image-18-expanded.png)
    
4.  Passez en revue les alertes en cours.

    Dans **Region Management**, sélectionnez **Alerts** et passez en revue les alertes en cours. Si des alertes inattendues sont présentes, confirmez auprès du Support technique Dell qu’elles peuvent être effacées ou ignorées sans risque.
    
    [![](media/image-19-inline.png)](media/image-19-expanded.png)
    
5.  Identifiez les nœuds d’unité d’échelle.

    Si seule l’étiquette de service vous a été fournie, et que vous ne parvenez pas à identifier le nœud qui rencontre le problème à partir du portail administrateur Azure Stack Hub (c’est-à-dire que l’état d’alimentation du nœud est déjà Arrêté), effectuez les étapes suivantes pour mettre en corrélation le nœud d’unité d’échelle avec l’étiquette de service :
    
    1.  Dans **Region Management**, sélectionnez **Scale Units**, puis sélectionnez le cluster **s-cluster**. Sélectionner **Nœuds**.
    
    1.  Pour obtenir l’étiquette de service du nœud, sélectionnez le lien d’adresse IP **BMC**, qui ouvre l’interface web iDRAC du serveur dans un nouvel onglet ou une nouvelle fenêtre.

        [![](media/image-20-inline.png)](media/image-20-expanded.png) 
    
    1.  Connectez-vous à l’interface de l’iDRAC et vérifiez l’étiquette de service du nœud dans le volet **System Information**.
    
    1.  Répétez cette procédure pour chaque nœud, et mettez en corrélation ces étiquettes de service avec le remplacement matériel planifié afin de déterminer le ou les nœuds qui doivent faire l’objet d’une opération de maintenance.

        [![](media/image-21-inline.png)](media/image-21-expanded.png)
    
