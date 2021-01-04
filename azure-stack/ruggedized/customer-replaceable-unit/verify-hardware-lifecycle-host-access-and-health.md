---
title: Vérifier l’intégrité et l’accès de l’hôte de cycle de vie du matériel
description: Découvrez comment vérifier l’intégrité et l’accès de l’hôte de cycle de vie du matériel.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: b14bff53b8c8d5545a00424a543656a190c06bf3
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392680"
---
# <a name="verifying-hardware-lifecycle-host-access-and-health"></a>Vérification de l’intégrité et l’accès de l’hôte de cycle de vie du matériel

Connectez-vous à l’iDRAC et au système d’exploitation de l’hôte de cycle de vie du matériel (HLH, Hardware Lifecycle Host) et vérifiez l’intégrité du système.

1.  Connectez-vous à l’iDRAC.

    1.  À l’aide d’un navigateur web, accédez à l’interface web de l’iDRAC et connectez-vous à l’aide des informations d’identification fournies par le client.

        ![](media/image-3.png) 
    
    1.  Dans le menu de navigation supérieur, sélectionnez **System**.

        ![](media/image-4.png)
        
    1.  Sous l’onglet **Overview**, vérifiez que le système est entièrement sain ou affiche le problème attendu qui devrait être corrigé pendant ce remplacement de matériel.
    
        ![](media/image-5.png)
    
2.  Connectez-vous au système d’exploitation du HLH à l’aide de la console virtuelle iDRAC.

    > [!NOTE]
    > Vous pouvez ignorer cette étape si vous vous connectez à l’aide d’un panier d’incident avec des connexions VGA et USB.
    
    1.  Dans l’interface web de l’iDRAC, sélectionnez **Dashboard**.

        ![](media/image-6.png)
    
    1.  Dans le volet **Virtual Console**, sélectionnez **Settings**.
    
        ![](media/image-7.png)
        
    1.  Vérifiez que **Plug-in Type** a la valeur **HTML 5**. Si ce n’est pas le cas, modifiez-le, sélectionnez **Apply**, puis **OK** quand vous y êtes invité.
    
        ![](media/image-8.png)
        
    1.  Sélectionnez **Launch Virtual Console**.

        ![](media/image-9.png)
    
    1.  Si un avertissement contextuel s’affiche, modifiez les paramètres du navigateur pour toujours autoriser le lancement de la console virtuelle. Par exemple, dans Internet Explorer, sélectionnez **Options pour ce site** puis **Toujours autoriser**. Si nécessaire, après avoir modifié le paramètre du navigateur, répétez l’étape précédente pour lancer la console virtuelle.
    
        ![](media/image-10.png)
        
    1.  La console virtuelle doit maintenant être présente. Pour vous connecter au système d’exploitation, dans le menu supérieur, sélectionnez **Console Controls**.
    
        ![](media/image-11.png)
        
    1.  Sélectionnez la **macro clavier** **Ctrl+Alt+Suppr**, sélectionnez **Apply**, puis **Close**.
    
        ![](media/image-12.png)
        
    1.  Sélectionnez l’**utilisateur** en fonction des informations d’identification fournies par le client, entrez le mot de passe et sélectionnez la **flèche** pour vous connecter.
    
        ![](media/image-13.png)
        
        Vous êtes maintenant connecté au HLH.
        
3.  Vérifiez l’intégrité.

    1.  Lancez le **Gestionnaire de serveur**.

        ![](media/image-14.png)
        
    1.  Sélectionnez **Oui** à l’invite **Contrôle de compte d’utilisateur**.
    
        ![](media/image-15.png)
        
    1.  Dans le menu **Outils**, sélectionnez **Gestionnaire Hyper-V**.
    
        ![](media/image-16.png)
        
    1.  Dans le **Gestionnaire Hyper-V**, sélectionnez le nœud supérieur dans le menu de gauche, puis vérifiez que les machines virtuelles, telles que la **station de travail à accès privilégié**, le cas échéant, sont à l’état **En cours d’exécution**.