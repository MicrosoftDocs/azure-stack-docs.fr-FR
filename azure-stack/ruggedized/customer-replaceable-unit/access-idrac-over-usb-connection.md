---
title: Accéder à l’iDRAC par le biais d’une connexion USB
description: Découvrez comment accéder à l’iDRAC par le biais d’une connexion USB.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 5c270de88d78bb8cb7ba1f7b9216c4a160a4aae8
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910055"
---
# <a name="accessing-the-idrac-interface-over-a-direct-usb-connection"></a>Accès à l’interface iDRAC par le biais d’une connexion USB directe.

La fonctionnalité iDRAC Direct vous permet de connecter directement votre ordinateur portable au port USB iDRAC. Cette fonctionnalité vous permet d’interagir directement avec les interfaces iDRAC telles que l’interface web, RACADM et WSMan pour la gestion et la maintenance avancées des serveurs.



Pour accéder à l’interface iDRAC par le biais du port USB, effectuez les étapes suivantes à partir de votre ordinateur portable.

**Étapes**

1.  À partir de votre ordinateur portable, désactivez tous les réseaux sans fil et déconnectez-vous de tous les autres réseaux câblés.

2.  Connectez un câble micro USB de votre ordinateur portable au port iDRAC Direct, situé à l’avant du serveur.
    Reportez-vous à l’élément 4 dans le diagramme.

    ![Schéma qui montre un bouton d’alimentation, ainsi que des ports USB et micro-USB.](media/image-67.png)

3.  Attendez que l’ordinateur portable obtienne l’adresse IP 169.254.0.4.

    L’acquisition des adresses IP peut prendre plusieurs secondes. L’IDRAC acquiert l’adresse IP 169.254.0.3.

4.  Connectez-vous à l’interface web de l’iDRAC.

    Pour accéder à l’interface web de l’iDRAC, ouvrez un navigateur et accédez à 169.254.0.3.

5.  Effectuez les activités requises.

    

    > [!NOTE]
    > Lorsque l’iDRAC utilise le port USB, qui est l’élément 3 dans le diagramme ci-dessus, le voyant clignote quatre fois par seconde pour signaler l’activité.
    
6.  Déconnectez le câble micro USB.

    Une fois que vous avez effectué les actions souhaitées, déconnectez le câble USB du système. Le voyant s’éteint.
    
