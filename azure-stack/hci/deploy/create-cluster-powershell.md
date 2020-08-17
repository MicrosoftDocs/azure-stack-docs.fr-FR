---
title: Créer un cluster Azure Stack HCI en utilisant Windows PowerShell
description: Découvrez comment créer un cluster hyperconvergé pour Azure Stack HCI en utilisant Windows PowerShell
author: v-dasis
ms.topic: how-to
ms.date: 08/11/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e92aa28deadbf334e3cd545e5cd9bc6b0e12e6c4
ms.sourcegitcommit: 673d9b7cf723bc8ef6c04aee5017f539a815da51
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88110466"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-powershell"></a>Créer un cluster Azure Stack HCI en utilisant Windows PowerShell

> S’applique à : Azure Stack HCI, version v20H2

Dans cet article, vous allez découvrir comment utiliser Windows PowerShell pour créer un cluster hyperconvergé Azure Stack HCI qui utilise les espaces de stockage direct. Si vous utilisez plutôt l’Assistant Création de cluster dans Windows Admin Center pour créer le cluster, consultez [Créer le cluster avec Windows Admin Center](create-cluster.md).

Vous avez le choix entre deux types de cluster :

- Cluster standard avec au moins deux nœuds de serveur, tous se trouvant dans un même site.
- Cluster étendu avec au moins quatre nœuds de serveur qui s’étendent sur deux sites, avec deux nœuds par site.

Dans cet article, nous allons créer un exemple de cluster nommé Cluster1 composé de quatre nœuds de serveur nommés Server1, Server2, Server3 et Server4.

Pour le scénario de cluster étendu, nous allons utiliser ClusterS1 comme nom et utiliser les quatre mêmes nœuds de serveur étendus sur les sites Site1 et Site2.

Pour plus d’informations sur les clusters étendus, consultez [Vue d’ensemble des clusters étendus](../concepts/stretched-clusters.md).

Si vous êtes intéressé par les tests Azure Stack HCI, mais que vous disposez d’un matériel de rechange limité ou inexistant, consultez le [Guide d’évaluation Azure Stack HCI](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/README.md), dans lequel nous vous guidons tout au long de la rencontre avec Azure Stack HCI à l’aide de la virtualisation imbriquée, dans Azure ou sur un seul système physique local.

## <a name="before-you-begin"></a>Avant de commencer

Avant de commencer, vérifiez que :

- Vous avez lu la configuration matérielle requise et les autres exigences dans [Avant de déployer Azure Stack HCI](before-you-start.md).
- Vous avez installé le système d’exploitation Azure Stack HCI sur chaque serveur du cluster. Consultez [Déployer le système d’exploitation Azure Stack HCI](operating-system.md).
- Vous avez un compte qui est membre du groupe Administrateurs local sur chaque serveur.
- Vous disposez des droits dans Active Directory pour créer des objets.

## <a name="using-windows-powershell"></a>Utilisation de Windows PowerShell

Vous pouvez exécuter PowerShell localement dans une session RDP sur un serveur hôte ou vous pouvez l’exécuter à distance à partir d’un ordinateur de gestion. Cet article porte sur l’option à distance.

Quand vous exécutez PowerShell à partir d’un ordinateur de gestion, incluez le paramètre `-Name` ou `-Cluster` avec le nom du serveur ou du cluster que vous gérez. En outre, il peut être nécessaire de spécifier le nom de domaine complet (FQDN) quand vous utilisez le paramètre `-ComputerName` pour un nœud de serveur.

Vous aurez également besoin des applets de commande des outils d’administration de serveur distant (RSAT), et des modules PowerShell pour Hyper-V et le clustering de basculement. Si ceux-ci derniers ne sont pas déjà disponibles dans votre session PowerShell sur votre ordinateur de gestion, vous pouvez les ajouter en utilisant la commande suivante : `Add-WindowsFeature RSAT-Clustering-PowerShell`.

## <a name="step-1-provision-the-servers"></a>Étape 1 : Provisionner les serveurs

Nous allons d’abord nous connecter à chacun des serveurs, les joindre à un domaine (le même domaine que celui où se trouve l’ordinateur de gestion), et installer les rôles et fonctionnalités nécessaires.

### <a name="step-11-connect-to-the-servers"></a>Étape 1.1 : Se connecter aux serveurs

Pour vous connecter aux serveurs, vous devez d’abord disposer d’une connectivité réseau, être joint au même domaine ou à un domaine entièrement approuvé, et disposer des autorisations d’administration locale sur les serveurs.

Ouvrez PowerShell et utilisez le nom de domaine complet ou l’adresse IP du serveur auquel vous voulez vous connecter. Vous serez invité à entrer un mot de passe après avoir exécuté la commande suivante sur chaque serveur (Server1, Server2, Server3, Server4) :

   ```powershell
   Enter-PSSession -ComputerName "Server1" -Credential "Server1\Administrator"
   ```

Voici un autre exemple qui fait la même chose :

   ```powershell
   $myServer1 = "Server1"
   $user = "$myServer1\Administrator"

   Enter-PSSession -ComputerName $myServer1 -Credential $user
   ```

> [!TIP]
> Quand vous exécutez des commandes PowerShell à partir de votre PC de gestion, vous pouvez obtenir une erreur comme *WinRM ne peut pas traiter la demande.* Pour résoudre ce problème, utilisez PowerShell pour ajouter chaque serveur à la liste Hôtes approuvés sur votre ordinateur de gestion. Cette liste prend en charge les caractères génériques, comme par exemple `Server*`.
>
> `Set-Item WSMAN:\Localhost\Client\TrustedHosts -Value Server1 -Force`
>  
> Pour voir votre liste Hôtes approuvés, tapez `Get-Item WSMAN:\Localhost\Client\TrustedHosts`.  
>
> Pour vider la liste, tapez `Clear-Item WSMAN:\Localhost\Client\TrustedHost`.  

### <a name="step-12-join-the-domain-and-add-domain-accounts"></a>Étape 1.2 : Joindre le domaine et ajouter des comptes de domaine

Jusqu’à présent, vous vous êtes connecté à chaque nœud de serveur avec le compte d’administrateur local `<ServerName>\Administrator`.

Pour continuer, vous devez joindre les serveurs à un domaine et utiliser le compte de domaine qui se trouve dans le groupe Administrateurs local sur chaque serveur.

Utilisez l’applet de commande `Enter-PSSession` pour vous connecter à chaque serveur et exécutez l’applet de commande suivante, en remplaçant le nom du serveur, le nom de domaine et les informations d’identification de domaine :

```powershell  
Add-Computer -NewName "Server1" -DomainName "contoso.com" -Credential "Contoso\User" -Restart -Force  
```

Si votre compte d’administrateur n’est pas membre du groupe Administrateurs de domaine, ajoutez votre compte administrateur au groupe Administrateurs local sur chaque serveur ; ou encore mieux, ajoutez le groupe que vous utilisez pour les administrateurs. Pour cela, vous pouvez utiliser la commande suivante :

```powershell
Add-LocalGroupMember -Group "Administrators" -Member "king@contoso.local"
```

### <a name="step-13-install-roles-and-features"></a>Étape 1.3 : Installer les rôles et les fonctionnalités

L’étape suivante consiste à installer les rôles et fonctionnalités Windows nécessaires sur chaque serveur pour le cluster. Voici les rôles à installer :

- BitLocker
- Data Center Bridging (pour les cartes réseau RoCEv2)
- Clustering de basculement
- Serveur de fichiers
- Module FS-Data-Deduplication
- Hyper-V
- Module RSAT-AD-PowerShell
- Réplica de stockage (uniquement pour les clusters étendus)

Utilisez la commande suivante pour chaque serveur :

```powershell
Install-WindowsFeature -ComputerName "Server1" -Name "BitLocker", "Data-Center-Bridging", "Failover-Clustering", "FS-FileServer", "Hyper-V", "Hyper-V-PowerShell", "RSAT-Clustering-PowerShell", "Storage-Replica" -IncludeAllSubFeature -IncludeManagementTools
```

Pour exécuter la commande sur tous les serveurs du cluster en même temps, utilisez le script suivant, en modifiant la liste des variables au début pour les adapter à votre environnement.

```powershell
# Fill in these variables with your values
$ServerList = "Server1", "Server2", "Server3", "Server4"
$FeatureList = "BitLocker", "Data-Center-Bridging", "Failover-Clustering", "FS-FileServer", "Hyper-V", "Hyper-V-PowerShell", "RSAT-Clustering-PowerShell", "Storage-Replica"

# This part runs the Install-WindowsFeature cmdlet on all servers in $ServerList, passing the list of features in $FeatureList.
Invoke-Command ($ServerList) {
    Install-WindowsFeature -Name $Using:Featurelist -IncludeAllSubFeature -IncludeManagementTools
}
```
Ensuite, redémarrez tous les serveurs :

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
Restart-Computer -ComputerName $ServerList
```

## <a name="step-2-configure-networking"></a>Étape 2 : Configurer la mise en réseau

Cette étape configure différents éléments de mise en réseau dans votre environnement.

### <a name="disable-unused-networks"></a>Désactiver les réseaux inutilisés

Vous devez désactiver les réseaux déconnectés ou non utilisés pour le trafic de gestion, de stockage ou de charge de travail (comme les machines virtuelles). Voici comment identifier les réseaux inutilisés :

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
Get-NetAdapter -CimSession $Servers | Where-Object Status -eq Disconnected
```
Et voici comment les désactiver :

```powershell
Get-NetAdapter -CimSession $Servers | Where-Object Status -eq Disconnected | Disable-NetAdapter -Confirm:$False
```

### <a name="assign-virtual-network-adapters"></a>Affecter des cartes réseau virtuelles

Attribuez ensuite des cartes réseau virtuelles (vNICs) pour la gestion et le reste du trafic, comme dans l’exemple suivant. Vous devez configurer au moins une carte réseau pour la gestion des clusters.

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
$vSwitchName="vSwitch"
Rename-VMNetworkAdapter -ManagementOS -Name $vSwitchName -NewName Management -ComputerName $Servers
Add-VMNetworkAdapter -ManagementOS -Name SMB01 -SwitchName $vSwitchName -CimSession $Servers
Add-VMNetworkAdapter -ManagementOS -Name SMB02 -SwitchName $vSwitchName -Cimsession $Servers
```

Vous allez aussi vérifier qu’elles ont été correctement ajoutées et affectées :

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
Get-VMNetworkAdapter -CimSession $Servers -ManagementOS
```

### <a name="create-virtual-switches"></a>Créer des commutateurs virtuels

Un commutateur virtuel est nécessaire pour chaque nœud de serveur de votre cluster. Dans l’exemple suivant, un commutateur virtuel avec la fonctionnalité SR-IOV est créé en utilisant des cartes réseau connectées (l’état est en Opérationnel). La fonctionnalité SR-IOV activée peut également être utile, car elle est nécessaire pour les adaptateurs réseau virtuels pour machines virtuelles compatibles RDMA.

Toutes les cartes réseau doivent être identiques pour l’association de cartes réseau.

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
$vSwitchName="vSwitch"
```

Et pour créer le commutateur virtuel :

```powershell
Invoke-Command -ComputerName $Servers -ScriptBlock {New-VMSwitch -Name $using:vSwitchName -EnableEmbeddedTeaming $TRUE -EnableIov $true -NetAdapterName (Get-NetAdapter | Where-Object Status -eq Up ).InterfaceAlias}
```

Maintenant, vérifiez que le commutateur a été créé correctement :

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
Get-VMSwitch -CimSession $Servers | Select-Object Name, IOVEnabled, IOVS*
Get-VMSwitchTeam -CimSession $Servers
```

### <a name="configure-ip-addresses-and-vlans"></a>Configurer des adresses IP et des réseaux VLAN

Vous pouvez configurer un ou deux sous-réseaux. Deux sous-réseaux sont préférables si vous voulez empêcher la surcharge de l’interconnexion du commutateur. Par exemple, le trafic de stockage SMB reste sur un sous-réseau dédié à un commutateur physique.

### <a name="obtain-network-interface-information"></a>Obtenir des informations sur l’interface réseau

Avant de pouvoir définir des adresses IP pour les cartes d’interface réseau, vous devez d’abord disposer de certaines informations, comme l’index d’interface (`ifIndex`), `Interface Alias` et `Address Family`. Notez-les pour chaque nœud de serveur, car vous en aurez besoin plus tard.

Exécutez la commande suivante :

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
Get-NetIPInterface -ComputerName $ServerList
```

#### <a name="configure-one-subnet"></a>Configurer un sous-réseau

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
$StorNet="172.16.1."
$StorVLAN=1
$IP=1 #starting IP Address

#Configure IP Addresses
foreach ($Server in $Servers){
    New-NetIPAddress -IPAddress ($StorNet+$IP.ToString()) -InterfaceAlias "vEthernet (SMB01)" -CimSession $Server -PrefixLength 24
    $IP++
    New-NetIPAddress -IPAddress ($StorNet+$IP.ToString()) -InterfaceAlias "vEthernet (SMB02)" -CimSession $Server -PrefixLength 24
    $IP++
}

#Configure VLANs
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB01 -VlanId $StorVLAN -Access -ManagementOS -CimSession $Servers
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB02 -VlanId $StorVLAN -Access -ManagementOS -CimSession $Servers
#Restart each host vNIC adapter so that the Vlan is active.
Restart-NetAdapter "vEthernet (SMB01)" -CimSession $Servers
Restart-NetAdapter "vEthernet (SMB02)" -CimSession $Servers
```

#### <a name="configure-two-subnets"></a>Configurer deux sous-réseaux

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
$StorNet1="172.16.1."
$StorNet2="172.16.2."
$StorVLAN1=1
$StorVLAN2=2
$IP=1 #starting IP Address

#Configure IP Addresses
foreach ($Server in $Servers){
    New-NetIPAddress -IPAddress ($StorNet1+$IP.ToString()) -InterfaceAlias "vEthernet (SMB01)" -CimSession $Server -PrefixLength 24
    New-NetIPAddress -IPAddress ($StorNet2+$IP.ToString()) -InterfaceAlias "vEthernet (SMB02)" -CimSession $Server -PrefixLength 24
    $IP++
}

#Configure VLANs
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB01 -VlanId $StorVLAN1 -Access -ManagementOS -CimSession $Servers
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB02 -VlanId $StorVLAN2 -Access -ManagementOS -CimSession $Servers
#Restart each host vNIC adapter so that the Vlan is active.
Restart-NetAdapter "vEthernet (SMB01)" -CimSession $Servers
Restart-NetAdapter "vEthernet (SMB02)" -CimSession $Servers
```

#### <a name="verify-vlan-ids-and-subnets"></a>Vérifier les ID et les sous-réseaux du VLAN

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
#verify ip config
Get-NetIPAddress -CimSession $servers -InterfaceAlias vEthernet* -AddressFamily IPv4 | Sort-Object -Property PSComputername | ft pscomputername,interfacealias,ipaddress -AutoSize -GroupBy PSComputerName

#Verify that the VlanID is set
Get-VMNetworkAdapterVlan -ManagementOS -CimSession $servers | Sort-Object -Property Computername | Format-Table ComputerName,AccessVlanID,ParentAdapter -AutoSize -GroupBy ComputerName
```

## <a name="step-3-prep-for-cluster-setup"></a>Étape 3 : Préparer la configuration du cluster

Ensuite, vérifiez que vos serveurs sont prêts pour le clustering. En premier lieu, au titre de contrôle de l’intégrité, vous pouvez envisager d’exécuter les commandes suivantes pour vérifier que les serveurs n’appartiennent pas déjà à un cluster :

Utilisez `Get-ClusterNode` pour afficher tous les nœuds :

```powershell
Get-ClusterNode
```

Utilisez `Get-ClusterResource` pour afficher tous les nœuds du cluster :

```powershell
Get-ClusterResource
```

Utilisez `Get-ClusterNetwork` pour afficher tous les réseaux du cluster :

```powershell
Get-ClusterNetwork
```

### <a name="step-31-prepare-drives"></a>Étape 3.1 : Préparer les lecteurs

Avant d’activer les espaces de stockage direct, vérifiez que vos lecteurs sont vides. Exécutez le script suivant pour supprimer les anciennes partitions ou d’autres données.

> [!WARNING]
> Ce script supprime définitivement toutes les données sur tous les lecteurs autres que le lecteur de démarrage système Azure Stack HCI.

```powershell
# Fill in these variables with your values
$ServerList = "Server1", "Server2", "Server3", "Server4"

Invoke-Command ($ServerList) {
    Update-StorageProviderCache
    Get-StoragePool | ? IsPrimordial -eq $false | Set-StoragePool -IsReadOnly:$false -ErrorAction SilentlyContinue
    Get-StoragePool | ? IsPrimordial -eq $false | Get-VirtualDisk | Remove-VirtualDisk -Confirm:$false -ErrorAction SilentlyContinue
    Get-StoragePool | ? IsPrimordial -eq $false | Remove-StoragePool -Confirm:$false -ErrorAction SilentlyContinue
    Get-PhysicalDisk | Reset-PhysicalDisk -ErrorAction SilentlyContinue
    Get-Disk | ? Number -ne $null | ? IsBoot -ne $true | ? IsSystem -ne $true | ? PartitionStyle -ne RAW | % {
        $_ | Set-Disk -isoffline:$false
        $_ | Set-Disk -isreadonly:$false
        $_ | Clear-Disk -RemoveData -RemoveOEM -Confirm:$false
        $_ | Set-Disk -isreadonly:$true
        $_ | Set-Disk -isoffline:$true
    }
    Get-Disk | Where Number -Ne $Null | Where IsBoot -Ne $True | Where IsSystem -Ne $True | Where PartitionStyle -Eq RAW | Group -NoElement -Property FriendlyName
} | Sort -Property PsComputerName, Count
```

### <a name="step-32-test-cluster-configuration"></a>Étape 3.2 : Tester la configuration du cluster

Dans cette étape, vous allez vérifier que les nœuds de serveur sont configurés correctement pour créer un cluster. L’applet de commande `Test-Cluster` est utilisée pour exécuter des tests afin de vérifier que votre configuration est appropriée pour fonctionner en tant que cluster hyperconvergé. L’exemple ci-dessous utilise le paramètre `-Include`, avec les catégories spécifiques de tests spécifiées. Ceci garantit que les tests corrects sont inclus dans la validation.

```powershell
Test-Cluster -Cluster –Node "Server1", "Server2", "Server3", "Server4" –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
```

## <a name="step-4-create-the-cluster"></a>Étape 4 : Créer le cluster

Vous êtes maintenant prêt à créer un cluster avec les nœuds de serveur que vous avez validés dans les étapes précédentes.

Lors de la création du cluster, vous obtenez un avertissement indiquant `"There were issues while creating the clustered role that may prevent it from starting. For more information, view the report file below."` Vous pouvez ignorer cet avertissement sans problème. Il est dû au fait qu’aucun disque n’est disponible pour le témoin de cluster que vous allez créer ultérieurement. 

> [!NOTE]
> Si les serveurs utilisent des adresses IP statiques, modifiez la commande suivante de façon refléter l’adresse IP statique en ajoutant le paramètre suivant et en spécifiant l’adresse IP : `–StaticAddress <X.X.X.X>;`.

```powershell
 New-Cluster –Name "Cluster1" –Node "Server1", "Server2", "Server3", "Server4" –NoStorage
```

Félicitations, votre cluster a maintenant été créé.

Une fois le cluster créé, la réplication du nom du cluster sur votre domaine peut prendre du temps, en particulier si des serveurs de groupe de travail ont été ajoutés à Active Directory. Bien que le cluster puisse être affiché dans Windows Admin Center, il est possible qu’il ne soit pas encore disponible pour s’y connecter.

Si la résolution du cluster échoue au bout d’un certain temps, dans la plupart des cas, vous pouvez vous connecter en utilisant le nom d’un des serveurs en cluster plutôt que le nom du cluster.

## <a name="step-5-set-up-sites-stretched-cluster"></a>Étape 5 : Configurer des sites (cluster étendu)

Cette tâche s’applique seulement si vous créez un cluster étendu entre deux sites.

### <a name="step-51-create-sites"></a>Étape 5.1 : Créer des sites

Dans l’applet de commande ci-dessous, *FaultDomain* est simplement un autre nom pour un site. Cet exemple utilise « ClusterS1 » comme nom du cluster étendu.

```powershell
New-ClusterFaultDomain -CimSession "ClusterS1" -FaultDomainType Site -Name "Site1"
```

```powershell
New-ClusterFaultDomain -CimSession "ClusterS1" -FaultDomainType Site -Name "Site2"
```

Utilisez l’applet de commande `Get-ClusterFaultDomain` pour vérifier que les deux sites ont été créés pour le cluster.

```powershell
New-ClusterFaultDomain -CimSession "ClusterS1"
```

### <a name="step-52-assign-server-nodes"></a>Étape 5.2 : Affecter des nœuds de serveur

Nous allons ensuite affecter les quatre nœuds de serveur à leurs sites respectifs. Dans l’exemple ci-dessous, Server1 et Server2 sont affectés à Site1, tandis que Server3 et Server4 sont affectés à Site2.

```powershell
Set-ClusterFaultDomain -CimSession "ClusterS1" -Name "Server1", "Server2" -Parent "Site1"
```

```powershell
Set-ClusterFaultDomain -CimSession "ClusterS1" -Name "Server3", "Server4" -Parent "Site2"
```

Avec l’applet de commande `Get-ClusterFaultDomain`, vérifiez que les nœuds se trouvent dans les sites corrects.

```powershell
Get-ClusterFaultDomain -CimSession "ClusterS1"
```

### <a name="step-53-set-a-preferred-site"></a>Étape 5.3 : Définir un site préféré

Vous pouvez aussi définir un site *préféré* global, ce qui signifie que les ressources et les groupes spécifiés doivent s’exécuter sur le site préféré.  Ce paramètre peut être défini au niveau du site avec la commande suivante :  

```powershell
(Get-Cluster).PreferredSite = "Site1"
```

La spécification d’un site préféré pour les clusters étendus a les avantages suivants :

- **Démarrage à froid** : lors d’un démarrage à froid, les machines virtuelles sont placées dans le site préféré

- **Vote du quorum**
  - Avec un quorum dynamique, la pondération est d’abord réduite sur le site passif (répliqué) pour garantir que le site préféré survit si toutes les autres choses sont égales. En outre, les nœuds de serveur sont d’abord nettoyés du site passif lors du regroupement après des événements comme des défaillances de connectivité réseau asymétrique.

  - Lors d’une division de quorum de deux sites, si le témoin de cluster ne peut pas être contacté, le site préféré est automatiquement choisi pour l’emporter. Les nœuds de serveur du site passif abandonnent alors l’appartenance au cluster. Ceci permet au cluster de survivre à une perte simultanée de 50 % des votes.

Le site préféré peut également être configuré au niveau du rôle ou du groupe de clusters. Dans ce cas, un autre site préféré peut être configuré pour chaque groupe de machines virtuelles. Ceci permet à un site d’être actif et préféré pour des machines virtuelles spécifiques.

## <a name="step-6-enable-storage-spaces-direct"></a>Étape 6 : Activer les espaces de stockage direct

Après avoir créé le cluster, utilisez l’applet de commande `Enable-ClusterStorageSpacesDirect`, qui activera les espaces de stockage direct et effectuera automatiquement les actions suivantes :

- **Créer un pool de stockage :** Crée un pool de stockage pour le cluster avec un nom tel que « Pool de stockage Cluster1 ».

- **Créer un disque d’historique des performances du cluster :** Crée un disque virtuel d’historique des performances du cluster dans le pool de stockage.

- **Créer des volumes de données et de journaux :** Crée un volume de données et un volume de journaux dans le pool de stockage.

- **Configurer des caches pour les espaces de stockage direct :** Si plusieurs types de supports (lecteurs) sont disponibles pour les espaces de stockage direct, ceci active les plus rapides comme périphériques de cache (en lecture et en écriture dans la plupart des cas).

- **Créer des niveaux :** Crée deux niveaux comme niveaux par défaut. Un est appelé « Capacité » et l’autre « Performances ». L’applet de commande analyse les périphériques et configure chaque niveau avec la combinaison des types de périphériques et de la résilience.

Pour les clusters étendus, l’applet de commande `Enable-ClusterStorageSpacesDirect` effectue également les actions suivantes :

- Vérifier si des sites ont été configurés
- Déterminer quels nœuds sont dans quels sites
- Détermine le stockage disponible pour chaque nœud
- Vérifie si la fonctionnalité de réplica de stockage est installée sur chaque nœud
- Crée un pool de stockage pour chaque site et l’identifie au même site
- Crée des volumes de données et de journaux dans chaque pool de stockage, un par site

La commande suivante active les espaces de stockage direct. Vous pouvez aussi spécifier un nom convivial pour un pool de stockage, comme illustré ici :

```powershell
$session = New-CimSession -Cluster "Cluster1" | Enable-ClusterStorageSpacesDirect -PoolFriendlyName "Cluster1 Storage Pool"
```

Pour voir les pools de stockage, utilisez ceci :

```powershell
Get-StoragePool -CimSession $session
```

Félicitations, vous avez maintenant créé un cluster simple.

## <a name="after-you-create-the-cluster"></a>Après avoir créé le cluster

Une fois cela fait, il reste encore des tâches importantes à exécuter :

- Configurer un témoin de cluster. Consultez [Configurer un témoin de cluster](witness.md).
- Créer vos volumes. Consultez [Créer des volumes](../manage/create-volumes.md).
- Pour les clusters étendus, créez des volumes et configurez la réplication en utilisant le réplica de stockage. Consultez [Créer des volumes et configurer la réplication pour des clusters étendus](../manage/create-stretched-volumes.md).

## <a name="next-steps"></a>Étapes suivantes

- Inscrire votre cluster auprès d’Azure. Consultez [Gérer une inscription Azure](../manage/manage-azure-registration.md).
- Effectuer une validation finale du cluster. Consultez [Valider un cluster Azure Stack HCI](validate.md).
- Provisionner vos machines virtuelles. Consultez [Gérer des machines virtuelles sur Azure Stack HCI avec PowerShell](../manage/vm-powershell.md).
- Vous pouvez aussi créer un cluster avec Windows Admin Center. Consultez [Créer un cluster Azure Stack HCI en utilisant Windows Admin Center](create-cluster.md).
