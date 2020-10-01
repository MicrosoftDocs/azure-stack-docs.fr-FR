---
title: Problèmes connus liés à Azure Stack HCI
description: Cette rubrique décrit les problèmes connus rencontrés avec Azure Stack HCI.
author: myoungerman
ms.author: v-myoung
ms.topic: troubleshooting
ms.date: 07/21/2020
ms.openlocfilehash: 21e56fbb34c89446b0dd0e395046a9c851f391dc
ms.sourcegitcommit: f2d16c3148da50d679940e024267995b85ce6332
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91407750"
---
# <a name="known-issues-for-azure-stack-hci"></a>Problèmes connus liés à Azure Stack HCI

>S’applique à : Azure Stack HCI, version 20H2

Cette rubrique décrit les problèmes connus liés à Azure Stack HCI

- La connexion entre le cluster local et le service cloud Azure Stack HCI ne prend pas en charge [Azure Private Link](https://azure.microsoft.com/services/private-link) pour l’instant.
- À l’heure actuelle, le service cloud Azure Stack HCI est uniquement disponible dans certaines régions.
- La disponibilité d’Azure Stack HCI a été annoncée le 21 juillet 2020. Plusieurs jours peuvent être nécessaires pour que son extension d’interface utilisateur sur le portail Azure soit visible dans le monde entier. Durant cette période, votre cluster peut se trouver représenté sous forme de page de ressources par défaut dans le portail Azure. Nous vous remercions de votre patience.
- Lorsque vous vous connectez de manière interactive au système d’exploitation, l’écran d’accueil qui indique « Bienvenue dans Azure Stack HCI » n’est pas encore localisé en tchèque, en hongrois, en néerlandais,en polonais, en suédois et en turc (codes de paramètres régionaux cs-cz, hu-hu, nl-nl, pl-pl, pt-pt, sv-se, tr-tr). Par ailleurs, dans tous les langues à part l’anglais, les invites d’entrée, telles que « [O]ui/[N]on », n’acceptent respectivement que les valeurs « Y » et « N », même si l’invite elle-même semble insidieusement localisée, comme [O]ui/[N]on en français ou [J]a/[N]ein en allemand.
- Si vous évaluez Azure Stack HCI au moyen de la virtualisation imbriquée, vous pouvez rencontrer une erreur telle que « Impossible d’installer Hyper-V, car la prise en charge de la virtualisation n’est pas activée », du fait de la dépendance d’Azure Stack HCI à la sécurité basée sur la virtualisation. Il existe deux solutions de contournement possibles : (1) Utiliser à la place des machines virtuelles Hyper-V Génération 1 ou (2) Injecter la fonctionnalité Hyper-V dans le VHDX de la machine virtuelle en mode hors connexion. À partir de l’hôte, exécutez la commande PowerShell suivante sur chacune des machines virtuelles ; celles-ci feront office de nœuds Azure Stack HCI pendant qu’elles seront éteintes : **Install-WindowsFeature -Vhd \<path> -Name Hyper-V, RSAT-Hyper-V-Tools, Hyper-V-Powershell**.
