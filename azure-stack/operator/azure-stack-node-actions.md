---
title: Mettre à l’échelle des actions de nœud d’unité dans Azure Stack | Microsoft Docs
description: Découvrez comment afficher l’état d’un nœud et utiliser les actions de nœud (mise sous tension, mise hors tension, désactivation et reprise) sur un système intégré Azure Stack.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
origin.date: 01/22/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.reviewer: ppacent
ms.lastreviewed: 01/22/2019
ms.openlocfilehash: cd7e66961a0b9a80150a3d3e132efd29485cdb66
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307865"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Mettre à l’échelle des actions de nœud d’unité dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit comment afficher l’état d’une unité d’échelle. Vous pouvez afficher les nœuds de l’unité. Vous pouvez exécuter des actions de nœud comme la mise sous tension, la mise hors tension, l’arrêt, le vidage, la reprise et la réparation. En règle générale, vous utilisez ces actions de nœud durant le remplacement de pièces sur le terrain ou dans le but de récupérer un nœud.

> [!Important]  
> Toutes les actions de nœud décrites dans cet article ne doivent cibler qu’un seul nœud à la fois.

## <a name="view-the-node-status"></a>Visualiser l’état des nœuds

Dans le portail d’administration, vous pouvez afficher l’état d’une unité d’échelle et de ses nœuds associés.

Pour afficher l’état d’une unité d’échelle :

1. Dans la vignette **Gestion des régions**, sélectionnez la région.
2. Sur la gauche, sous **Ressources d’infrastructure**, sélectionnez **Unités d’échelle**.
3. Dans les résultats, sélectionnez l’unité d’échelle.
4. Sur la gauche, sous **Général**, sélectionnez **Nœuds**.

   Examinez les informations suivantes :

   - Liste des nœuds individuels
   - État opérationnel (voir la liste ci-dessous)
   - État d’alimentation (en cours d’exécution ou arrêté)
   - Modèle de serveur
   - Adresse IP du contrôleur de gestion de la carte de base (BMC)
   - Nombre total de cœurs
   - Quantité totale de mémoire

![état d’une unité d’échelle](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>États opérationnels d’un nœud

| Statut | Description |
|----------------------|-------------------------------------------------------------------|
| Exécution | Le nœud participe de façon active à l’unité d’échelle. |
| Arrêté | Le nœud est indisponible. |
| Ajout | Le nœud est ajouté de façon active à l’unité d’échelle. |
| Réparation | Le nœud est réparé de façon active. |
| Maintenance  | Le nœud est suspendu et aucune charge de travail utilisateur active n’est en cours d’exécution. |
| Correction nécessaire | Une erreur nécessitant la réparation du nœud a été détectée. |

## <a name="scale-unit-node-actions"></a>Actions de nœud d’unité d’échelle

Lorsque vous affichez des informations relatives à un nœud d’unité d’échelle, vous pouvez également effectuer des actions de nœud telles que :
 - Démarrer et arrêter (en fonction de l’état d’alimentation actuel)
 - Désactiver et reprendre (en fonction de l’état opérationnel)
 - Réparation
 - Shutdown

L’état de fonctionnement du nœud détermine les options disponibles.

Vous devez installer les modules Azure Stack PowerShell. Ces applets de commande se trouvent dans le module **Azs.Fabric.Admin**. Pour installer ou vérifier votre installation de PowerShell pour Azure Stack, consultez [Installer PowerShell pour Azure Stack](azure-stack-powershell-install.md).

## <a name="stop"></a>Arrêter

L’action **Arrêter** désactive le nœud. Cela revient à appuyer sur le bouton d’alimentation. Aucun signal d’arrêt n’est envoyé au système d’exploitation. Pour les opérations d’arrêt planifiées, commencez toujours par l’opération d’arrêt. 

Cette action est généralement utilisée lorsqu’un nœud est dans un état suspendu et ne répond plus aux demandes.

Pour exécuter l’action d’arrêt, ouvrez une invite de commandes PowerShell avec élévation de privilèges et exécutez l’applet de commande suivante :

```PowerShell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Dans le cas peu probable où l’action d’arrêt ne fonctionnerait pas, réessayez l’opération et, si elle échoue une deuxième fois, utilisez l’interface web BMC à la place.

Pour plus d’informations, consultez [Stop-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/stop-azsscaleunitnode).

## <a name="start"></a>Démarrer

L’action de **démarrage** active le nœud. Cela revient à appuyer sur le bouton d’alimentation. 
 
Pour exécuter l’action de démarrage, ouvrez une invite de commandes PowerShell avec élévation de privilèges et exécutez l’applet de commande suivante :

```PowerShell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Dans le cas peu probable où l’action de démarrage ne fonctionnerait pas, réessayez l’opération et, si elle échoue une deuxième fois, utilisez l’interface web BMC à la place.

Pour plus d’informations, consultez [Start-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/start-azsscaleunitnode).

## <a name="drain"></a>Vidage

L’action de **vidage** déplace toutes les charges de travail actives sur les nœuds restants dans cette unité d’échelle spécifique.

Cette action est généralement utilisée au cours du remplacement de champs de parties, telles que le remplacement d’un nœud complet.

> [!Important]
> Veillez à effectuer une opération de vidage sur un nœud durant une période de maintenance planifiée, quand les utilisateurs ont été notifiés. Sous certaines conditions, les charges de travail actives peuvent subir des interruptions.

Pour exécuter l’action de vidage, ouvrez une invite de commandes PowerShell avec élévation de privilèges et exécutez l’applet de commande suivante :

```PowerShell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Pour plus d’informations, consultez [Disable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/disable-azsscaleunitnode).

## <a name="resume"></a>Reprendre

L’action de **reprise** reprend un nœud désactivé et le marque comme actif pour le positionnement de la charge de travail. Les charges de travail antérieures en cours d’exécution sur le nœud ne se restaurent pas automatiquement. (Si vous utilisez une opération de vidage sur un nœud, veillez à le mettre hors tension. Quand vous remettez le nœud sous tension, il n’est pas marqué comme actif pour le positionnement de la charge de travail. Lorsque vous êtes prêt, vous devez utiliser l’action de reprise pour marquer le nœud comme étant actif.

Pour exécuter l’action de reprise, ouvrez une invite de commandes PowerShell avec élévation de privilèges et exécutez l’applet de commande suivante :

```PowerShell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Pour plus d’informations, consultez [Enable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## <a name="repair"></a>Réparation

L’action de **réparation** répare un nœud. À utiliser uniquement pour un des scénarios suivants :
 - Remplacement de nœud complet (avec ou sans nouveaux disques de données)
 - Après la défaillance et le remplacement d’un composant matériel (Si cela est conseillé dans la documentation sur les unités remplaçables sur site.

> [!Important]  
> Consultez la documentation sur les unités remplaçables sur site de votre fabricant de matériel OEM pour connaître les étapes exactes relatives au remplacement d’un nœuds ou de composants matériels individuels. La documentation sur les unités remplaçables sur site indique s’il faut exécuter l’action de réparation après le remplacement d’un composant matériel. 

Lorsque vous exécutez l’action de réparation, vous devez spécifier l’adresse IP du BMC. 

Pour exécuter l’action de réparation, ouvrez une invite de commandes PowerShell avec élévation de privilèges et exécutez l’applet de commande suivante :

  ```PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ```

## <a name="shutdown"></a>Shutdown

L’action d’**arrêt** déplace toutes les charges de travail actives vers les nœuds restants de la même unité d’échelle. L’action arrête ensuite de manière appropriée le nœud d’unité d’échelle.

Après avoir démarré un nœud arrêté, vous devez exécuter l’action de [reprise](#resume). Les charges de travail antérieures en cours d’exécution sur le nœud ne se restaurent pas automatiquement.

En cas d’échec de l’opération d’arrêt, essayez l’opération de [drainage](#drain) suivie de l’opération d’arrêt.

Pour exécuter l’action d’arrêt, ouvrez une invite PowerShell avec élévation de privilèges, puis exécutez l’applet de commande suivante :

  ```PowerShell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -Shutdown
  ```



## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le module administrateur Azure Stack Fabric, consultez [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0).
