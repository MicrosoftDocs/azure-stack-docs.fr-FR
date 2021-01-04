---
title: Codes et voyants EMC PowerEdge Tactical
description: Apprenez-en davantage sur les codes et voyants EMC PowerEdge Tactical.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 5b4920fb2ab174867092c44ca51a5193412a9a9a
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392836"
---
# <a name="dell-emc-poweredge-tactical-indicators-and-codes"></a>Codes et voyants Dell EMC PowerEdge Tactical

Les fonctionnalités décrites dans cette section sont les mêmes pour les serveurs de nœuds Unité d’échelle et Hôte de cycle de vie du matériel.

## <a name="system-information-label"></a>Étiquette d’informations système

L’illustration suivante montre l’étiquette d’informations système, qui se trouve sur le panneau avant.


Figure 10. Comportement des voyants

![](media/image-75.png)

## <a name="idrac-direct-led-indicator-codes"></a>Codes du voyant iDRAC Direct

Le voyant iDRAC Direct s’allume pour indiquer que le port est connecté et utilisé dans le cadre du sous-système iDRAC.

Vous pouvez configurer iDRAC Direct à l’aide d’un câble USB vers micro-USB (type AB), que vous pouvez connecter à votre ordinateur portable ou tablette. Le tableau suivant décrit l’activité iDRAC Direct lorsque le port iDRAC Direct est actif, et explique la signification des codes du voyant iDRAC Direct.

Tableau 4. Codes du voyant

| **Codes du voyant**                                         | **Condition**                                                |
|-------------------------------------------------------------|--------------------------------------------------------------|
| Vert fixe pendant deux secondes                                 | Indique que l’ordinateur portable ou la tablette est connecté.            |
| Vert clignotant (allumé pendant deux secondes et éteint pendant deux secondes) | Indique que l’ordinateur portable ou la tablette connecté est reconnu. |
| Éteint                                             | Indique que l’ordinateur portable ou la tablette est débranché.            |

## <a name="left-control-panel"></a>Panneau de configuration gauche

L’illustration suivante montre les voyants d’état et d’intégrité du panneau de commande de gauche.



Figure 11. Panneau de configuration gauche

![](media/image-76.png)

Le tableau suivant décrit les caractéristiques du panneau de gauche.

Tableau 5. Voyants d’état et d’intégrité du panneau de commande de gauche PowerEdge

|    <br>Élément       |    <br>Voyant ou bouton                       |    <br>Icône                                |    <br>Description                                                                                                  |
|-------------------|--------------------------------------------------|--------------------------------------------|---------------------------------------------------------------------------------------------------------------------|
|    <br>1          |    <br>Voyants d’état                     |    <br>N/A                                 |    <br>Indique l'état du système. Pour plus d’informations, consultez Voyants d’état PowerEdge page 61.    |
|    <br>2 et 3    |    <br>Voyants d’intégrité du système et d’ID système    | ![](media/image-77.png) |    <br>Indique l’intégrité du système.                                                                                 |

Le tableau suivant liste les voyants d’état PowerEdge.

> [!NOTE]
> Les voyants d’état sont toujours éteints, et ne s’allument en orange fixe que si une erreur se produit.

Tableau 6. Voyants d’état PowerEdge

| <br>Icône | <br>Description           | <br>Condition                                                                                                                                                                       | <br>Action corrective                                                                                                                                                                                                                                                                                                    |
|----------|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|          | <br>Voyant de lecteur       | <br>Le voyant devient orange fixe en cas d’erreur de lecteur.                                                                                                                      | 1. Consultez le journal des événements système pour savoir si le lecteur présente une erreur.<br>2. Exécutez le test de diagnostic en ligne approprié.<br>3. Redémarrez le système et exécutez les diagnostics embarqués (ePSA).<br>4. Si les disques sont configurés dans un contrôleur RAID, redémarrez le système et accédez à l'utilitaire de configuration de l’adaptateur hôte. |
|          | <br>Voyant de température | <br>Le voyant devient orange fixe si le système subit une erreur thermique (par exemple si la température ambiante est hors limites ou s’il y a une défaillance du ventilateur).                 | Vérifiez que :<br>* Aucun ventilateur de refroidissement n’a été retiré ou n’a subi une défaillance. <br>* Le capot du système, la prise d’air, le module de mémoire vide ou la plaque de recouvrement arrière n’a pas été retiré(e).<br>* La température ambiante n’est pas trop élevée.<br>* Le ventilation externe n’est pas obstrué.                                                 |
|          | <br>Voyant électrique  | <br>Le voyant devient orange fixe si le système subit une erreur électrique (par exemple si la tension est hors limites ou si un bloc d’alimentation ou un régulateur de tension a échoué). | 1. Vérifiez le journal des événements système ou les messages système pour en savoir plus sur le problème.<br>2. Si le journal indique un problème au niveau du bloc d’alimentation, vérifiez le voyant du bloc d’alimentation.<br>3. Réinsérez le bloc d’alimentation.                                                                                                                                              |
|          | <br>Voyant de mémoire      | <br>Le voyant devient orange fixe si une erreur de mémoire se produit.                                                                                                                       | 1. Vérifiez le journal des événements système ou les messages système pour localiser la défaillance de mémoire.<br>2. Réinsérez le module de mémoire.                                                                                                                                                                                                  |
|          | <br>Voyant PCIe      | <br>Le voyant devient orange fixe si une carte PCIe rencontre une erreur.                                                                                                          | 1. Redémarrez le système.<br>2. Mettez à jour tous les pilotes requis pour la carte PCIe.<br>3. Réinstallez la carte.                                                                                                                                                                                                                    |

## <a name="power-supply-unit-indicator-codes"></a>Codes du voyant de bloc d’alimentation

Les blocs d’alimentation secteur AC ont une poignée translucide éclairée qui sert de voyant. Les blocs d’alimentation secteur DC ont une LED qui sert de voyant. Le voyant indique si l’alimentation est présente ou si une panne de courant s’est produite.

La figure suivante montre le bloc d’alimentation secteur AC.

Figure 12 : BLOC D’ALIMENTATION SECTEUR AC

![](media/image-83.png)

Le tableau suivant fournit la signification des codes du voyant.

Tableau 7. Codes du voyant de bloc d’alimentation AC

| Codes du voyant d’alimentation         | Condition                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|-------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Vert                         | Une source d’alimentation valide est connectée au bloc d’alimentation et celui-ci est opérationnel.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Orange clignotant                | Indique un problème au niveau du bloc d’alimentation.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Éteint               | L’alimentation n’est pas connectée au bloc d’alimentation.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| Vert clignotant                | Lorsque le microprogramme du bloc d’alimentation est mis à jour, la poignée du bloc d’alimentation clignote en vert. <br>Ne déconnectez pas le cordon d’alimentation et ne débranchez pas le bloc d’alimentation lors de la mise à jour du microprogramme. Si la mise à jour du microprogramme est interrompue, le bloc d’alimentation ne fonctionne pas.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Clignote en vert et s’éteint  | Lors de la connexion à chaud d’un bloc d’alimentation, sa poignée clignote en vert cinq fois à une fréquence de 4 Hz, puis s’éteint. Cela indique une incompatibilité de bloc d’alimentation en termes d’efficacité, de jeu de caractéristiques, d’état d’intégrité ou de tension prise en charge. <br><br>Si deux blocs d’alimentation sont installés, ils doivent tous les deux avoir le même type d’étiquette, par exemple une étiquette EPP (performances d’alimentation étendues). La combinaison de blocs d’alimentation de générations précédentes de serveurs PowerEdge n’est pas prise en charge, même si les blocs d’alimentation ont la même puissance. Cela entraîne une condition d’incompatibilité de blocs d’alimentation ou un échec d’activation du système. <br>Lorsque vous corrigez une incompatibilité de blocs d’alimentation, remplacez uniquement celui dont le voyant clignote. Le fait de permuter le bloc d’alimentation pour créer une paire correspondante peut entraîner une condition d’erreur et <br>un arrêt inattendu du système. Pour passer d’une configuration de sortie élevée à une configuration de sortie basse, ou vice versa, vous devez arrêter le système. <br>Les blocs d’alimentation AC prennent en charge des tensions d’entrée de 240 V et 120 V, à l’exception des blocs d’alimentation Titanium, qui ne prennent en charge que 240 V. Lorsque deux blocs d’alimentation identiques reçoivent des tensions d’entrée différentes, ils sont susceptibles de générer des puissances différentes et de déclencher une incompatibilité. <br>Si deux blocs d’alimentation sont utilisés, ils doivent être du même type et avoir la même puissance de sortie maximale. <br>La combinaison de blocs d’alimentation AC et DC n’est pas prise en charge, et déclenche une incompatibilité. |
