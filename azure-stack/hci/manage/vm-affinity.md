---
title: Configurer des règles d’affinité de machine virtuelle à l’aide de Windows PowerShell
description: Découvrez comment configurer des règles d’affinité de machine virtuelle à l’aide de Windows PowerShell
author: v-dasis
ms.topic: how-to
ms.date: 07/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: b53ed05d8ce255b4fd68d40faf37b44f11bee059
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947178"
---
# <a name="create-server-and-site-affinity-rules-for-vms"></a>Créer des règles d’affinité de serveur et de site pour les machines virtuelles

> S’applique à Azure Stack HCI, version 20H2

À l’aide de Windows PowerShell, vous pouvez facilement créer des règles d’affinité et d’anti-affinité pour vos machines virtuelles dans un cluster.

L’affinité est une règle qui établit une relation entre au moins deux groupes de ressources ou rôles, tels que des machines virtuelles, pour les conserver ensemble dans un cluster Azure Stack HCI. L’anti-affinité est l’inverse dans le sens où cette règle est utilisée pour garder les groupes de ressources spécifiés indépendants les uns des autres.

Les règles d’affinité et d’anti-affinité sont utilisées de la même façon qu’Azure utilise les zones de disponibilité. Dans Azure, vous pouvez configurer des zones de disponibilité pour conserver les machines virtuelles dans des zones distinctes et les séparer les unes des autres ou pour les conserver dans la même zone les unes avec les autres.  

À l’aide des règles d’affinité et d’anti-affinité, les machines virtuelles de cluster restent dans le même nœud de cluster ou ne peuvent pas résider ensemble dans le même nœud de cluster.  De cette façon, le seul moyen de faire sortir une machine virtuelle d’un nœud de cluster est de le faire manuellement.  Vous pouvez également conserver les machines virtuelles ensemble avec le volume partagé de cluster (CSV) sur lequel se trouve le VHDX.

En associant les règles d’affinité et d’anti-affinité, vous pouvez également configurer un cluster étendu sur deux sites (domaines d’erreur) et conserver vos machines virtuelles dans le site dans lequel elles doivent se trouver.

En règle générale, vous configurez vos règles à partir d’un ordinateur distant plutôt que sur un serveur hôte dans un cluster. Cet ordinateur distant est appelé ordinateur de gestion.

> [!NOTE]
> Quand vous exécutez des commandes PowerShell à partir d’un ordinateur de gestion, incluez le paramètre -Name ou -Cluster dans le nom du cluster que vous gérez. Si applicable, vous allez également avoir besoin de spécifier le nom de domaine complet (FQDN) quand vous utilisez le paramètre -ComputerName pour un nœud de serveur.

## <a name="new-powershell-cmdlets"></a>Nouvelles applets de commande PowerShell

Pour créer des règles d’affinité pour les clusters Azure Stack HCI, utilisez les applets de commande PowerShell suivantes.

### <a name="new-clusteraffinityrule"></a>New-ClusterAffinityRule

L’applet de commande **`New-ClusterAffinityRule`** permet de créer des règles.  Avec cette commande, vous devez spécifier le nom de la règle, ainsi que le type de règle, où :

**`-Name`** est le nom de la règle.

Les valeurs **`-RuleType`** sont les suivantes : `SameFaultDomain` | `SameNode` | `DifferentFaultDomain` | `DifferentNode`

Exemple :

```powershell
New-ClusterAffinityRule -Name -RuleType SameFaultDomain -Cluster Cluster1
```

### <a name="set-clusteraffinityrule"></a>Set-ClusterAffinityRule

L’applet de commande **`Set-ClusterAffinityRule`** permet d’activer ou de désactiver une règle, où :

**`-Name`** est le nom de la règle à activer ou désactiver.

**`-Enabled`**  |  **`Disabled`** active ou désactive la règle.

Exemple :

```powershell
Set-ClusterAffinityRule -Name -Enabled -Cluster Cluster1
```

### <a name="get-clusteraffinityrule"></a>Get-ClusterAffinityRule

L’applet de commande **`Get-ClusterAffinityRule`** permet d’afficher la règle spécifiée et son type.  Si **`-Name`** n’est pas spécifié, toutes les règles sont répertoriées.

Exemple :

```powershell
Get-ClusterAffinityRule -Name -Cluster Cluster1
```

### <a name="add-clustergrouptoaffinityrule"></a>Add-ClusterGroupToAffinityRule

L’applet de commande **`Add-ClusterGroupToAffinityRule`** permet d’ajouter un nom de groupe ou de rôle de machine virtuelle à une règle d’affinité spécifique, où :

**`-Groups`** est le nom du groupe ou du rôle à ajouter à la règle.

**`-Name`** est le nom de la règle pour l’ajout.

Exemple :

```powershell
Add-ClusterGroupToAffinityRule -Groups -Name -Cluster Cluster1
```

### <a name="add-clustersharedvolumetoaffinityrule"></a>Add-ClusterSharedVolumeToAffinityRule

**`Add-ClusterSharedVolumeToAffinityRule`** permet à vos machines virtuelles de rester ensemble avec le Volume partagé de cluster sur lequel réside le VHDX, où :

**`-ClusterSharedVolumes`** correspond aux disques CSV que vous souhaitez ajouter à la règle.

**`-Name`** est le nom de la règle pour l’ajout.

Exemple :

```powershell
Add-ClusterSharedVolumeToAffinityRule  -ClusterSharedVolumes -Name -Cluster Cluster1
```

### <a name="remove-clusteraffinityrule"></a>Remove-ClusterAffinityRule

**`Remove-ClusterAffinityRule`** supprime la règle spécifiée, où **`-Name`** est le nom de la règle.

Exemple :

```powershell
Remove-ClusterAffinityRule -Name -Cluster Cluster1
```

### <a name="remove-clustergroupfromaffinityrule"></a>Remove-ClusterGroupFromAffinityRule

**`Remove-ClusterGroupFromAffinityRule`** supprime un rôle ou un groupe de machines virtuelles d’une règle spécifique, mais ne désactive pas ou ne supprime pas la règle, où :

**`-Name`** est le nom de la règle.

**`-Groups`** correspond aux groupes ou rôles que vous souhaitez supprimer de la règle.

Exemple :

```powershell
Remove-ClusterGroupFromAffinityRule -Name -Groups -Cluster Cluster1
```

### <a name="remove-clustersharedvolumefromaffinityrule"></a>Remove-ClusterSharedVolumeFromAffinityRule

L’applet de commande **`Remove-ClusterSharedVolumeFromAffinityRule`** permet de supprimer les volumes partagés de cluster d’une règle spécifique, mais ne désactive pas ou ne supprime pas la règle, où :

**`-ClusterSharedVolumes`** correspond aux disques CSV que vous souhaitez supprimer de la règle.

**`-Name`** est le nom de la règle pour l’ajout.

Exemple :

```powershell
Remove-ClusterSharedVolumeFromAffinityRule -ClusterSharedVolumes -Name -Cluster Cluster1
```

## <a name="existing-powershell-cmdlets"></a>applets de commande PowerShell existantes

Avec l’arrivée de nouvelles applets de commande, nous avons également ajouté des commutateurs supplémentaires à quelques applets de commande existantes.

### <a name="move-clustergroup"></a>Move-ClusterGroup

Le nouveau commutateur `-IgnoreAffinityRule` ignore la règle et déplace le groupe de ressources de cluster vers un autre nœud de cluster. Pour plus d’informations sur cette applet de commande, consultez [Move-ClusterGroup](/powershell/module/failoverclusters/move-clustergroup?view=win10-ps).

Exemple :

```powershell
Move-ClusterGroup -IgnoreAffinityRule -Cluster Cluster1
```

> [!NOTE]
> Si une règle de déplacement est valide (prise en charge), tous les groupes et rôles concernés sont également déplacés.  Si un déplacement de machine virtuelle viole sciemment une règle mais qu’il est provisoirement nécessaire, utilisez le commutateur `-IgnoreAffinityRule` pour autoriser le déplacement. Dans ce cas, un avertissement de violation s’affiche pour la machine virtuelle. Vous pouvez ensuite réactiver la règle en fonction des besoins.

### <a name="start-clustergroup"></a>Start-ClusterGroup

Le nouveau commutateur `-IgnoreAffinityRule` ignore la règle et met en ligne le groupe de ressources de cluster à son emplacement actuel. Pour plus d’informations sur cette applet de commande, consultez [Start-ClusterGroup](/powershell/module/failoverclusters/start-clustergroup?view=win10-ps).

Exemple :

```powershell
Start-ClusterGroup -IgnoreAffinityRule -Cluster Cluster1
```

## <a name="anti-affinity-rules"></a>Règles d’anti-affinité

### <a name="scenario-1"></a>Scénario 1
Deux machines virtuelles exécutent SQL Server sur le même cluster multi-sites Azure Stack HCI.  Chaque machine virtuelle utilise beaucoup de ressources de mémoire, de processeur et de stockage.  Si les deux résident sur le même nœud, cela peut entraîner des problèmes de performances avec l’une d’elles ou les deux, car elles sont en concurrence pour les cycles de mémoire, de processeur et de stockage.  En utilisant une règle d’anti-affinité avec `DifferentNode` comme type de règle, ces machines virtuelles restent toujours sur des nœuds de cluster différents.  

Dans ce cas de figure, voici des exemples de commandes :

```powershell
New-ClusterAffinityRule -Name SQL -Ruletype DifferentNode -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,SQL2 –Name SQL -Cluster Cluster1

Set-ClusterAffinityRule -Name SQL -Enabled 1 -Cluster Cluster1
```

Pour afficher la règle et la façon dont elle est configurée, utilisez l’applet de commande **`Get-ClusterAffinityRule`** pour afficher la sortie :

```powershell
Get-ClusterAffinityRule -Name SQL -Cluster Cluster1

Name    RuleType        Groups        Enabled
----    -----------     -------       -------
SQL     DifferentNode   {SQL1, SQL2}     1
```

### <a name="scenario-2"></a>Scénario 2

Supposons que vous disposez d’un cluster étendu Azure Stack HCI avec deux sites (domaines d’erreur). Vous avez deux contrôleurs de domaine que vous souhaitez conserver dans des sites distincts. À l’aide d’une règle d’anti-affinité utilisant `DifferentFaultDomain` comme type de règle, ces contrôleurs de domaine restent toujours dans des sites différents.  Dans ce cas de figure, voici des exemples de commandes :

```powershell
New-ClusterAffinityRule -Name DC -Ruletype DifferentFaultDomain -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups DC1,DC2 –Name DC -Cluster Cluster1

Set-ClusterAffinityRule -Name DC -Enabled 1 -Cluster Cluster1
```

Pour afficher cette règle et la façon dont elle est configurée, utilisez l’applet de commande **`Get-ClusterAffinityRule`** pour afficher la sortie :

```powershell
Get-ClusterAffinityRule -Name DC -Cluster Cluster1

Name    RuleType                Groups        Enabled
----    --------                -------       -------
DC      DifferentFaultDomain    {DC1, DC2}       1
```

## <a name="affinity-rules"></a>Règles d’affinité

Voici quelques scénarios courants pour la configuration de règles d’affinité.

### <a name="scenario-1"></a>Scénario 1

Supposons que vous disposez d’une machine virtuelle SQL Server et d’une machine virtuelle de serveur web. Ces deux machines virtuelles doivent toujours rester sur le même site, mais elles ne doivent pas nécessairement se trouver sur le même nœud de cluster dans le site.  À l’aide de `SameFaultDomain`, cela est possible, comme indiqué ci-dessous :

```powershell
New-ClusterAffinityRule -Name WebData -Ruletype SameFaultDomain -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,WEB1 –Name WebData -Cluster Cluster1

Set-ClusterAffinityRule -Name WebData -Enabled 1 -Cluster Cluster1
```

Pour afficher cette règle et la façon dont elle est configurée, utilisez l’applet de commande **`Get-ClusterAffinityRule`** pour afficher la sortie :

```powershell
Get-ClusterAffinityRule -Name WebData -Cluster Cluster1

Name        RuleType          Groups        Enabled
----        ---------         ------        -------
WebData     SameFaultDomain   {SQL1, WEB1}     1
```

### <a name="scenario-2"></a>Scénario 2
Utilisons le même scénario que ci-dessus, sauf que les machines virtuelles doivent résider sur le même nœud de cluster, mais pas nécessairement sur le même site. À l’aide de `SameNode`, vous pouvez définir ce qui suit :

```powershell
New-ClusterAffinityRule -Name WebData1 -Ruletype SameNode -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,WEB1 –Name WebData1 -Cluster Cluster1

Set-ClusterAffinityRule -Name WebData1 -Enabled 1 -Cluster Cluster1
```

Pour afficher la règle et la façon dont elle est configurée, utilisez l’applet de commande **`Get-ClusterAffinityRule`** pour afficher la sortie :

```powershell
Get-ClusterAffinityRule -Name WebData1 -Cluster Cluster1

Name    RuleType    Groups        Enabled
----    --------    ------        -------
DC      SameNode    {SQL1, WEB1}     1
```

## <a name="combined-rules"></a>Règles combinées

En associant les règles d’affinité et d’anti-affinité, vous pouvez facilement configurer différentes combinaisons de machines virtuelles sur un cluster à plusieurs sites.  Dans ce scénario, chaque site comporte trois machines virtuelles : SQL Server (SQL), serveur web (WEB) et contrôleur de domaine (DC).  Pour chacune des combinaisons, vous pouvez utiliser des règles d’affinité avec `SameFaultDomain` pour les conserver toutes dans le même site.  Vous pouvez également définir les contrôleurs de domaine de chaque site avec des règles d’anti-affinité et `DifferentFaultDomain` pour conserver les machines virtuelles du contrôleur de domaine dans des sites distincts, comme indiqué ci-dessous :

```powershell
New-ClusterAffinityRule -Name Site1Trio -Ruletype SameFaultDomain -Cluster Cluster1

New-ClusterAffinityRule -Name Site2Trio -Ruletype SameFaultDomain -Cluster Cluster1

New-ClusterAffinityRule -Name TrioApart -Ruletype DifferentFaultDomain -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,WEB1,DC1 –Name Site1Trio -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL2,WEB2,DC2 –Name Site2Trio -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups DC1,DC2 –Name TrioApart -Cluster Cluster1

Set-ClusterAffinityRule -Name Site1Trio -Enabled 1 -Cluster Cluster1

Set-ClusterAffinityRule -Name Site2Trio -Enabled 1 -Cluster Cluster1

Set-ClusterAffinityRule -Name TrioApart -Enabled 1 -Cluster Cluster1
```

Pour afficher les règles et la façon dont elles sont configurées, utilisez l’applet de commande **`Get-ClusterAffinityRule`** sans le commutateur `-Name` pour voir toutes les règles créées et leur sortie.

```powershell
Get-ClusterAffinityRule -Cluster Cluster1

Name        RuleType               Groups            Enabled
----        --------               ------            -------
Site1Trio   SameFaultDomain        {SQL1, WEB1, DC1}    1
Site2Trio   SameFaultDomain        {SQL2, WEB2, DC2}    1
TrioApart   DifferentFaultDomain   {DC1, DC2}           1
```

## <a name="storage-affinity-rules"></a>Règles d’affinité de stockage

Vous pouvez également conserver une machine virtuelle et son VHDX sur un volume partagé de cluster (CSV) sur le même nœud de cluster. Cela empêcherait ainsi la redirection CSV de se produire, ce qui peut ralentir le démarrage ou l’arrêt d’une machine virtuelle.  En tenant compte du scénario combiné d’affinité et d’anti-affinité précédent, vous pouvez conserver la machine virtuelle SQL et le volume partagé de cluster sur le même nœud de cluster.  Pour ce faire, utilisez les commandes suivantes :

```powershell
New-ClusterAffinityRule -Name SQL1CSV1 -Ruletype SameNode -Cluster Cluster1

New-ClusterAffinityRule -Name SQL2CSV2 -Ruletype SameNode -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1 –Name SQL1CSV1 -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL2 –Name SQL2CSV2 -Cluster Cluster1

Add-ClusterSharedVolumeToAffinityRule -ClusterSharedVolumes CSV1 -Name SQL1CSV1 -Cluster Cluster1

Add-ClusterSharedVolumeToAffinityRule -ClusterSharedVolumes CSV2 -Name SQL2CSV2 -Cluster Cluster1

Set-ClusterAffinityRule -Name SQL1CSV1 -Enabled 1 -Cluster Cluster1

Set-ClusterAffinityRule -Name SQL2CSV2 -Enabled 1 -Cluster Cluster1
```

Pour voir ces règles et comment elles sont configurées, utilisez l’applet de commande **`Get-ClusterAffinityRule`** sans le commutateur -Name et affichez la sortie.

```powershell
Get-ClusterAffinityRule -Cluster Cluster1

Name        RuleType               Groups            Enabled
----        --------               ------            -------
Site1Trio   SameFaultDomain        {SQL1, WEB1, DC1}    1
Site2Trio   SameFaultDomain        {SQL2, WEB2, DC2}    1
TrioApart   DifferentFaultDomain   {DC1, DC2}           1
SQL1CSV1    SameNode               {SQL1, <CSV1-GUID>}  1
SQL2CSV2    SameNode               {SQL2, <CSV2-GUID>}  1
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment gérer vos machines virtuelles. Consultez [Gérer des machines virtuelles sur Azure Stack HCI avec Windows Admin Center](../manage/vm.md).
