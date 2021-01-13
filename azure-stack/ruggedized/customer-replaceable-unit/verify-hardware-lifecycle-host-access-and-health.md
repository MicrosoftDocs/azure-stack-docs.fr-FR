---
title: Vérifier l’intégrité et l’accès de l’hôte de cycle de vie du matériel
description: Découvrez comment vérifier l’intégrité et l’accès de l’hôte de cycle de vie du matériel.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: d4dd073e3ec9ec47110df916517ef0f5e3273a3a
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910667"
---
# <a name="verifying-hardware-lifecycle-host-access-and-health"></a>Vérification de l’intégrité et l’accès de l’hôte de cycle de vie du matériel

Connectez-vous à l’iDRAC et au système d’exploitation de l’hôte de cycle de vie du matériel (HLH, Hardware Lifecycle Host) et vérifiez l’intégrité du système.

1.  Connectez-vous à l’iDRAC.

    1.  À l’aide d’un navigateur web, accédez à l’interface web de l’iDRAC et connectez-vous à l’aide des informations d’identification fournies par le client.

        ![Capture d’écran montrant la page de connexion iDRAC.](media/image-3.png) 
    
    1.  Dans le menu de navigation supérieur, sélectionnez **System**.

        ![Capture d’écran montrant le tableau de bord avec le menu « Système » mis en surbrillance.](media/image-4.png)
        
    1.  Sous l’onglet **Overview**, vérifiez que le système est entièrement sain ou affiche le problème attendu qui devrait être corrigé pendant ce remplacement de matériel.
    
        ![Capture d’écran montrant la page « Système » avec les vignettes de présentation mises en surbrillance.](media/image-5.png)
    
2.  Connectez-vous au système d’exploitation du HLH à l’aide de la console virtuelle iDRAC.

    > [!NOTE]
    > Vous pouvez ignorer cette étape si vous vous connectez à l’aide d’un panier d’incident avec des connexions VGA et USB.
    
    1.  Dans l’interface web de l’iDRAC, sélectionnez **Dashboard**.

        ![Capture d’écran montrant le bouton « Tableau de bord » sélectionné.](media/image-6.png)
    
    1.  Dans le volet **Virtual Console**, sélectionnez **Settings**.
    
        ![Capture d’écran montrant le bouton « Paramètres » sélectionné.](media/image-7.png)
        
    1.  Vérifiez que **Plug-in Type** a la valeur **HTML 5**. Si ce n’est pas le cas, modifiez-le, sélectionnez **Apply**, puis **OK** quand vous y êtes invité.
    
        ![Capture d’écran montrant la page « Configuration » avec « Type de plug-in - HTML5 » et le bouton « Appliquer » sélectionné.](media/image-8.png)
        
    1.  Sélectionnez **Launch Virtual Console**.

        ![Capture d’écran montrant la page « Configuration » avec l’option « Lancer la console virtuelle » mise en surbrillance.](media/image-9.png)
    
    1.  Si un avertissement contextuel s’affiche, modifiez les paramètres du navigateur pour toujours autoriser le lancement de la console virtuelle. Par exemple, dans Internet Explorer, sélectionnez **Options pour ce site** puis **Toujours autoriser**. Si nécessaire, après avoir modifié le paramètre du navigateur, répétez l’étape précédente pour lancer la console virtuelle.
    
        ![Capture d’écran montrant l’affichage de la fenêtre contextuelle « Avertissement ».](media/image-10.png)
        
    1.  La console virtuelle doit maintenant être présente. Pour vous connecter au système d’exploitation, dans le menu supérieur, sélectionnez **Console Controls**.
    
        ![Capture d’écran montrant la console virtuelle avec le bouton « Contrôles de console » sélectionné.](media/image-11.png)
        
    1.  Sélectionnez la **macro clavier** **Ctrl+Alt+Suppr**, sélectionnez **Apply**, puis **Close**.
    
        ![Capture d’écran montrant l’écran « Contrôles de console » avec « Macros du clavier » et le bouton « Fermer » sélectionné.](media/image-12.png)
        
    1.  Sélectionnez l’**utilisateur** en fonction des informations d’identification fournies par le client, entrez le mot de passe et sélectionnez la **flèche** pour vous connecter.
    
        ![Capture d’écran montrant les informations d’identification de l’utilisateur entrées.](media/image-13.png)
        
        Vous êtes maintenant connecté au HLH.
        
3.  Vérifiez l’intégrité.

    1.  Lancez le **Gestionnaire de serveur**.

        ![Capture d’écran montrant le « Gestionnaire de serveur » sélectionné.](media/image-14.png)
        
    1.  Sélectionnez **Oui** à l’invite **Contrôle de compte d’utilisateur**.
    
        ![Capture d’écran montrant l’invite « Contrôle de compte d’utilisateur » avec le bouton « Oui » sélectionné.](media/image-15.png)
        
    1.  Dans le menu **Outils**, sélectionnez **Gestionnaire Hyper-V**.
    
        ![Capture d’écran montrant le menu « Outils » ouvert et le « Gestionnaire Hyper-V » sélectionné.](media/image-16.png)
        
    1.  Dans le **Gestionnaire Hyper-V**, sélectionnez le nœud supérieur dans le menu de gauche, puis vérifiez que les machines virtuelles, telles que la **station de travail à accès privilégié**, le cas échéant, sont à l’état **En cours d’exécution**.