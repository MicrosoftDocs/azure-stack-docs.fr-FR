---
title: Mettre à l’échelle des actions de nœud d’unité dans Azure Stack Hub
description: Découvrez les actions de nœud d'unité d’échelle (mise sous tension, mise hors tension, désactivation et reprise) et la façon d’afficher l’état d’un nœud dans des systèmes intégrés Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 04/30/2020
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 17ecab0f42c89d6c25daba98652d8dc9d1a9e3b0
ms.sourcegitcommit: 21cdab346fc242b8848a04a124bc16c382ebc6f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777744"
---
# <a name="scale-unit-node-actions-in-azure-stack-hub"></a>Mettre à l’échelle des actions de nœud d’unité dans Azure Stack Hub

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

   - Liste des nœuds individuels.
   - État opérationnel (voir la liste ci-dessous).
   - État d’alimentation (en cours d’exécution ou arrêté).
   - Modèle de serveur.
   - Adresse IP du contrôleur de gestion de la carte de base (BMC).
   - Nombre total de cœurs.
   - Quantité totale de mémoire.

![état d’une unité d’échelle](media/azure-stack-node-actions/multinodeactions.png)

### <a name="node-operational-states"></a>États opérationnels d’un nœud

| Statut | Description |
|----------------------|-------------------------------------------------------------------|
| Exécution en cours | Le nœud participe de façon active à l’unité d’échelle. |
| Arrêté | Le nœud est indisponible. |
| Ajout | Le nœud est ajouté de façon active à l’unité d’échelle. |
| Réparation | Le nœud est réparé de façon active. |
| Maintenance  | Le nœud est suspendu et aucune charge de travail utilisateur active n’est en cours d’exécution. |
| Correction nécessaire | Une erreur nécessitant la réparation du nœud a été détectée. |

### <a name="azure-stack-hub-shows-adding-status-after-an-operation"></a>Azure Stack Hub affiche l'état Ajout après une opération

Azure Stack Hub peut afficher l'état opérationnel **Ajout** après l'exécution d'une des opérations suivantes : vidage, reprise, réparation, arrêt ou démarrage.
Cela peut se produire lorsque le cache du rôle Fournisseur de ressources d'infrastructure ne s'est pas actualisé après une opération. 

Avant de passer aux étapes suivantes, vérifiez qu'aucune opération n'est en cours. Mettez à jour le point de terminaison pour qu'il corresponde à votre environnement.

1. Ouvrez PowerShell et ajoutez votre environnement Azure Stack Hub. Pour cela, [Azure Stack Hub PowerShell doit être installé](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install) sur votre ordinateur.

   ```powershell
   Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint https://adminmanagement.local.azurestack.external
   Add-AzureRmAccount -Environment AzureStack
   ```

2. Exécutez la commande suivante pour redémarrer le rôle Fournisseur de ressources d'infrastructure.

   ```powershell
   Restart-AzsInfrastructureRole -Name FabricResourceProvider
   ```

3. Vérifiez que l'état opérationnel du nœud de l'unité d'échelle impactée a été remplacé par **Exécution en cours**. Vous pouvez utiliser le portail d'administration ou la commande PowerShell suivante :

   ```powershell
   Get-AzsScaleUnitNode |ft name,scaleunitnodestatus,powerstate
   ```

4. Si l'état opérationnel du nœud est toujours **Ajout**, ouvrez un incident de support.


## <a name="scale-unit-node-actions"></a>Actions de nœud d’unité d’échelle

Lorsque vous affichez des informations relatives à un nœud d’unité d’échelle, vous pouvez également effectuer des actions de nœud telles que :

 - Démarrer et arrêter (en fonction de l’état d’alimentation actuel).
 - Désactiver et reprendre (en fonction de l’état opérationnel).
 - Réparer.
 - Arrêter.

L’état de fonctionnement du nœud détermine les options disponibles.

Vous devez installer les modules Azure Stack Hub PowerShell. Ces applets de commande se trouvent dans le module **Azs.Fabric.Admin**. Pour installer ou vérifier votre installation de PowerShell pour Azure Stack Hub, consultez [Installer PowerShell pour Azure Stack Hub](azure-stack-powershell-install.md).

## <a name="stop"></a>Arrêter

L’action **Arrêter** désactive le nœud. Cela revient à appuyer sur le bouton d’alimentation. Aucun signal d’arrêt n’est envoyé au système d’exploitation. Pour les opérations d’arrêt planifiées, commencez toujours par l’opération d’arrêt.

Cette action est généralement utilisée lorsqu’un nœud est dans un état suspendu et ne répond plus aux demandes.

Pour exécuter l’action d’arrêt, ouvrez une invite de commandes PowerShell avec élévation de privilèges et exécutez l’applet de commande suivante :

```powershell  
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Dans le cas peu probable où l’action d’arrêt ne fonctionnerait pas, réessayez l’opération et, si elle échoue une deuxième fois, utilisez l’interface web BMC à la place.

Pour plus d’informations, consultez [Stop-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/stop-azsscaleunitnode).

## <a name="start"></a>Démarrer

L’action de **démarrage** active le nœud. Cela revient à appuyer sur le bouton d’alimentation.

Pour exécuter l’action de démarrage, ouvrez une invite de commandes PowerShell avec élévation de privilèges et exécutez l’applet de commande suivante :

```powershell  
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Dans le cas peu probable où l’action de démarrage ne fonctionnerait pas, répétez l’opération. Si elle échoue une seconde fois, utilisez l’interface web du BMC.

Pour plus d’informations, consultez [Start-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/start-azsscaleunitnode).

## <a name="drain"></a>Vidage

L’action de **vidage** déplace toutes les charges de travail actives sur les nœuds restants dans cette unité d’échelle spécifique.

Cette action est généralement utilisée au cours du remplacement de champs de parties, telles que le remplacement d’un nœud complet.

> [!Important]
> Veillez à effectuer une opération de vidage sur un nœud durant une période de maintenance planifiée, quand les utilisateurs ont été notifiés. Sous certaines conditions, les charges de travail actives peuvent subir des interruptions.

Pour exécuter l’action de vidage, ouvrez une invite de commandes PowerShell avec élévation de privilèges et exécutez l’applet de commande suivante :

```powershell  
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Pour plus d’informations, consultez [Disable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/disable-azsscaleunitnode).

## <a name="resume"></a>Reprendre

L’action de **reprise** reprend un nœud désactivé et le marque comme actif pour le positionnement de la charge de travail. Les charges de travail antérieures en cours d’exécution sur le nœud ne se restaurent pas automatiquement. (Si vous utilisez une opération de vidage sur un nœud, veillez à le mettre hors tension. Quand vous remettez le nœud sous tension, il n’est pas marqué comme actif pour le positionnement de la charge de travail. Lorsque vous êtes prêt, vous devez utiliser l’action de reprise pour marquer le nœud comme étant actif.

Pour exécuter l’action de reprise, ouvrez une invite de commandes PowerShell avec élévation de privilèges et exécutez l’applet de commande suivante :

```powershell  
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```

Pour plus d’informations, consultez [Enable-AzsScaleUnitNode](https://docs.microsoft.com/powershell/module/azs.fabric.admin/enable-azsscaleunitnode).

## <a name="repair"></a>Réparation

> [!CAUTION]  
> Le nivellement du microprogramme est essentiel pour la réussite de l’opération décrite dans cet article. Ignorer cette étape peut entraîner une instabilité du système, une baisse des performances, des menaces sur la sécurité ou l’échec de l’opération de déploiement du système d’exploitation par Azure Stack Hub Automation. Lors du remplacement du matériel, consultez toujours la documentation de votre partenaire de matériel pour vous assurer que le microprogramme appliqué correspond à la version OEM affichée dans le [portail administrateur Azure Stack Hub](azure-stack-updates.md).<br><br>
Pour en savoir plus et obtenir des liens vers la documentation du partenaire, consultez [Remplacer un composant matériel](azure-stack-replace-component.md).

| Partenaire matériel | Région | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Tous | [Guide des opérations Cisco Integrated System pour Microsoft Azure Stack Hub](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[Notes de publication de Cisco Integrated System pour Microsoft Azure Stack Hub](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | Tous | [Cloud pour Microsoft Azure Stack Hub 14G (compte et connexion requis)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud pour Microsoft Azure Stack Hub 13G (compte et connexion requis)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPON | [Support technique de service géré Fujitsu (compte et connexion requis)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA | [Support des produits et systèmes informatiques Fujitsu](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  |  | [MySupport Fujitsu (compte et connexion requis)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | Tous | [HPE ProLiant pour Microsoft Azure Stack Hub](http://www.hpe.com/info/MASupdates) |
| Lenovo | Tous | [Meilleures recettes ThinkAgile SXM](https://datacentersupport.lenovo.com/us/en/solutions/ht505122) |

L’action de **réparation** répare un nœud. À utiliser uniquement pour un des scénarios suivants :

- Remplacement de nœud complet (avec ou sans nouveaux disques de données).
- Après la défaillance et le remplacement d’un composant matériel (si cela est conseillé dans la documentation sur les unités remplaçables sur site).

> [!Important]  
> Consultez la documentation sur les unités remplaçables sur site de votre fabricant de matériel OEM pour connaître les étapes exactes relatives au remplacement d’un nœuds ou de composants matériels individuels. La documentation sur les unités remplaçables sur site indique s’il faut exécuter l’action de réparation après le remplacement d’un composant matériel.

Lorsque vous exécutez l’action de réparation, vous devez spécifier l’adresse IP du BMC.

Pour exécuter l’action de réparation, ouvrez une invite de commandes PowerShell avec élévation de privilèges et exécutez l’applet de commande suivante :

  ```powershell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPv4Address <BMCIPv4Address>
  ```

## <a name="shutdown"></a>Shutdown

L’action d’**arrêt** déplace d’abord toutes les charges de travail actives vers les nœuds restants de la même unité d’échelle. L’action arrête ensuite de manière appropriée le nœud d’unité d’échelle.

Après avoir démarré un nœud arrêté, vous devez exécuter l’action de [reprise](#resume). Les charges de travail antérieures en cours d’exécution sur le nœud ne se restaurent pas automatiquement.

En cas d’échec de l’opération d’arrêt, essayez l’opération de [drainage](#drain) suivie de l’opération d’arrêt.

Pour exécuter l’action d’arrêt, ouvrez une invite PowerShell avec élévation de privilèges, puis exécutez l’applet de commande suivante :

  ```powershell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -Shutdown
  ```

## <a name="next-steps"></a>Étapes suivantes

- [Installer Azure Stack PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install)
- [Découvrir le module opérateur Azure Stack Hub Fabric](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.6.0)
- [Surveiller les opérations d'ajout de nœud](https://docs.microsoft.com/azure-stack/operator/azure-stack-add-scale-node#monitor-add-node-operations)