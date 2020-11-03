---
title: Problèmes connus liés à Azure Stack HCI
description: Cette rubrique décrit les problèmes connus rencontrés avec Azure Stack HCI.
author: myoungerman
ms.author: v-myoung
ms.topic: troubleshooting
ms.date: 10/27/2020
ms.openlocfilehash: 05884ce83f6f35f8d75c632f67dabd38fa3c2814
ms.sourcegitcommit: 75603007badd566f65d01ac2eacfe48ea4392e58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92688324"
---
# <a name="known-issues-for-azure-stack-hci"></a>Problèmes connus liés à Azure Stack HCI

>S’applique à : Azure Stack HCI, version 20H2

Cette rubrique décrit les problèmes connus liés à Azure Stack HCI

- La mise à jour de la préversion du 20 octobre 2020 (KB4580388) peut entraîner l’échec d’une opération de Mise à jour adaptée aux clusters si l’une des machines virtuelles est supposée effectuer une Migration dynamique pendant cette opération. Pour éviter cela, modifiez temporairement le comportement par défaut de sorte que la Mise à jour adaptée aux clusters utilise la Migration rapide plutôt que la Migration dynamique. Pour plus d’informations, consultez les [notes de publication](release-notes.md#october-20-2020-preview-update-kb4580388).
- La connexion entre le cluster local et le service cloud Azure Stack HCI ne prend pas en charge [Azure Private Link](https://azure.microsoft.com/services/private-link) pour l’instant.
- À l’heure actuelle, le service cloud Azure Stack HCI est uniquement disponible dans certaines régions.
- Lorsque vous vous connectez de manière interactive au système d’exploitation, l’écran d’accueil qui indique « Bienvenue dans Azure Stack HCI » n’est pas encore localisé en tchèque, en hongrois, en néerlandais,en polonais, en suédois et en turc (codes de paramètres régionaux cs-cz, hu-hu, nl-nl, pl-pl, pt-pt, sv-se, tr-tr). Par ailleurs, dans tous les langues à part l’anglais, les invites d’entrée, telles que « [O]ui/[N]on », n’acceptent respectivement que les valeurs « Y » et « N », même si l’invite elle-même semble insidieusement localisée, comme [O]ui/[N]on en français ou [J]a/[N]ein en allemand.
- Si vous évaluez Azure Stack HCI au moyen de la virtualisation imbriquée, vous pouvez rencontrer une erreur telle que « Impossible d’installer Hyper-V, car la prise en charge de la virtualisation n’est pas activée », du fait de la dépendance d’Azure Stack HCI à la sécurité basée sur la virtualisation. Il existe deux solutions de contournement possibles : (1) Utiliser des machines virtuelles Hyper-V Génération 1 à la place ou (2) Injecter la fonctionnalité Hyper-V dans le disque VHDX de la machine virtuelle en mode hors connexion. À partir de l’hôte, exécutez la commande PowerShell suivante sur chacune des machines virtuelles ; celles-ci feront office de nœuds Azure Stack HCI pendant qu’elles seront éteintes : **Install-WindowsFeature -Vhd \<path> -Name Hyper-V, RSAT-Hyper-V-Tools, Hyper-V-Powershell**.
