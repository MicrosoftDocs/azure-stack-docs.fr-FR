---
title: Résolution des problèmes connus des machines virtuelles sur Azure Stack Hub
description: Découvrez comment résoudre les problèmes connus liés aux machines virtuelles sur Azure Stack Hub
author: mattbriggs
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 07/09/2020
ms.openlocfilehash: d752b31ac5076255ae4368904145a50d5a3c09ff
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867160"
---
# <a name="known-issues-vms-on-azure-stack-hub"></a>Problèmes connus : Machines virtuelles sur Azure Stack Hub

Dans cet article, vous pouvez trouver des éléments de réponse pour résoudre les problèmes connus du fournisseur de ressources de calcul Azure Stack Hub quand vous utilisez des machines virtuelles et des groupes identiques.

## <a name="portal-doesnt-show-correct-vm-name"></a>Le portail n’affiche pas le nom de machine virtuelle approprié
- **Applicable**  
    Ce problème concerne toutes les versions.  
- **Cause**  
    Quand vous visualisez les détails d’une machine virtuelle dans le panneau de vue d’ensemble, le nom de l’ordinateur s’affiche sous la forme (non disponible). Cela est dû à la conception des machines virtuelles créées à partir de disques/captures instantanées de disques spécialisés.  
- **Correction**  
    Dans le portail, sélectionnez **Paramètres** > **Propriétés**.
- **Occurrence**  
    Courant  

## <a name="nvv4-vm-size-on-portal"></a>Taille de machine virtuelle NVv4 sur le portail
- **Applicable**  
    Ce problème s’applique à Azure Stack Hub version 2002 et versions ultérieures.  
- **Cause**  
    Lors de la création de la machine virtuelle, vous verrez la taille suivante : NV4as_v4. Les clients qui disposent du matériel nécessaire à la préversion du processeur Azure Stack Hub AMD Mi25 verront réussir le déploiement de leur machine virtuelle. Tous les autres clients verront le déploiement de leur machine virtuelle échouer avec cette taille.  
- **Correction**  
    Aucun.  
- **Occurrence**  
    Courant  

## <a name="vm-boot-diagnostics"></a>Diagnostics de démarrage de machine virtuelle
- **Applicable**  
    Ce problème s’applique à toutes les versions prises en charge.  
- **Cause**  
    Quand vous créez une machine virtuelle, l’erreur suivante peut s’afficher : Échec du démarrage de la machine virtuelle « nom-machine-virtuelle ». Erreur : Échec de la mise à jour des paramètres de sortie série pour la machine virtuelle « nom-machine-virtuelle ». Cette erreur se produit si vous activez les diagnostics de démarrage sur une machine virtuelle alors que vous avez supprimé votre compte de stockage des diagnostics de démarrage.  
- **Correction**  
    Recréez le compte de stockage en réutilisant le même nom.
- **Occurrence**  
    Courant  

## <a name="vm-diagnostics-storage-account-not-found"></a>Compte de stockage des diagnostics de machine virtuelle introuvable
- **Applicable**  
    Ce problème s’applique à toutes les versions prises en charge.  
- **Cause**  
    Quand vous essayez de démarrer une machine virtuelle arrêtée-libérée, l’erreur suivante peut s’afficher : Le compte de stockage des diagnostics de machine virtuelle « diagnosticstorageaccount » est introuvable. Vérifiez que le compte de stockage n’est pas supprimé. L’erreur se produit si vous tentez de démarrer une machine virtuelle avec les diagnostics de démarrage activés, mais que le compte de stockage des diagnostics de démarrage référencé est supprimé.  
- **Correction**  
    Recréez le compte de stockage en réutilisant le même nom.  
- **Occurrence** Courante  

## <a name="consumed-compute-quota"></a>Quota de calcul consommé
- **Applicable**  
    Ce problème s’applique à toutes les versions prises en charge.  
- **Cause**   
    Au moment de la création d’une machine virtuelle, vous pouvez recevoir une erreur telle que Cet abonnement a atteint sa capacité maximale pour Total des processeurs virtuels régionaux à cet emplacement. Il utilise l’ensemble des 50 Total des processeurs virtuels régionaux disponibles. Cela indique que le quota pour le nombre total de cœurs à votre disposition a été atteint.  
- **Correction**  
    Demandez à votre opérateur un plan additionnel avec un quota supplémentaire. Le changement de quota du plan actuel ne fonctionne pas ou ne reflète pas l’augmentation du quota.
- **Occurrence**  
    Rare  

## <a name="virtual-machine-scale-set"></a>Jeu de mise à l’échelle de machine virtuelle

-  **Applicable**  
    Ce problème s’applique à toutes les versions prises en charge.  
- **Cause**  
    Échecs de création durant l’application des correctifs et des mises à jour dans les environnements Azure Stack Hub à 4 nœuds. Échec de la création de machines virtuelles dans un groupe à haute disponibilité de 3 domaines d’erreur, et échec de la création d’une instance de groupe de machines virtuelles identiques avec l’erreur FabricVmPlacementErrorUnsupportedFaultDomainSize durant le processus de mise à jour sur un environnement Azure Stack Hub à 4 nœuds.  
- **Correction**  
    Vous pouvez réussir à créer des machines virtuelles uniques dans un groupe à haute disponibilité comprenant 2 domaines d’erreur. Toutefois, la création d’instances de groupes identiques n’est toujours pas disponible durant le processus de mise à jour sur un déploiement Azure Stack Hub à 4 nœuds.  
- **Occurrence**  
    Rare  

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [fonctionnalités des machines virtuelles Azure Stack Hub](azure-stack-vm-considerations.md).
