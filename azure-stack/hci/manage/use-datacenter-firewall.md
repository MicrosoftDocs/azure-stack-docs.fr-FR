---
title: Utiliser le pare-feu Datacenter pour le SDN dans Azure Stack HCI et Windows Server
description: Utilisez cette rubrique afin de vous familiariser avec le pare-feu Datacenter pour le SDN dans Azure Stack HCI, Windows Server 2019 et Windows Server 2016.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 02/02/2021
ms.openlocfilehash: 8c150de090bd1f863a29109ddae9d6e4bb104dfc
ms.sourcegitcommit: 0e58c5cefaa81541d9280c0e8a87034989358647
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99510701"
---
# <a name="use-datacenter-firewall-for-software-defined-networking-in-azure-stack-hci-and-windows-server"></a>Utiliser le pare-feu Datacenter pour le SDN dans Azure Stack HCI et Windows Server

> S’applique à : Azure Stack HCI version 20H2, Windows Server 2019, Windows Server 2016

Cette rubrique explique comment configurer des listes de contrôle d'accès afin de gérer le flux de trafic de données à l'aide du [Pare-feu Datacenter](../concepts/datacenter-firewall-overview.md) pour la mise en réseau SDN (Software-Defined Networking) dans Azure Stack HCI via Windows PowerShell. Pour activer et configurer le Pare-feu Datacenter, vous devez créer des listes de contrôle d'accès qui sont appliquées à un sous-réseau ou à une interface réseau. Les exemples de scripts de cette rubrique utilisent des commandes Windows PowerShell exportées à partir du module **NetworkController**. Vous pouvez également utiliser Windows Admin Center pour configurer et gérer les listes de contrôle d'accès.

## <a name="configure-datacenter-firewall-to-allow-all-traffic"></a>Configurer le Pare-feu Datacenter de manière à autoriser tout le trafic

Une fois que vous avez déployé la mise en réseau SDN, vous devez tester la connectivité réseau de base dans votre nouvel environnement. Pour ce faire, créez une règle pour le Pare-feu Datacenter qui autorise tout le trafic réseau, sans restriction.

Utilisez les entrées du tableau suivant pour créer un ensemble de règles qui autorisent tout le trafic réseau entrant et sortant.

| IP Source | IP de destination | Protocol | Port source | Port de destination | Sens | Action | Priority |
|:---------:|:--------------:|:--------:|:-----------:|:----------------:|:---------:|:------:|:--------:|
|    \*     |       \*       |   Tous    |     \*      |        \*        |  Trafic entrant  | Allow  |   100    |
|    \*     |       \*       |   Tous    |     \*      |        \*        | Règle de trafic sortant  | Allow  |   110    |

---

Dans cet exemple, vous allez créer une liste de contrôle d'accès comportant deux règles :

1. **AllowAll_Inbound** : permet à tout le trafic réseau de passer dans l'interface réseau où cette liste de contrôle d'accès est configurée.
2. **AllowAllOutbound** : permet à tout le trafic de sortir de l'interface réseau. Cette liste de contrôle d'accès, identifiée par l'ID de ressource « AllowAll-1 », est maintenant prête à être utilisée dans les sous-réseaux virtuels et dans les interfaces réseau.

Tout d'abord, connectez-vous à l'un des nœuds du cluster en ouvrant une session PowerShell :

```PowerShell
Enter-PSSession <server-name>
```

Puis exécutez le script suivant afin de créer la liste de contrôle d'accès :

```PowerShell
$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "100"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"
$aclrule1 = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule1.Properties = $ruleproperties
$aclrule1.ResourceId = "AllowAll_Inbound"
$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "110"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"
$aclrule2 = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule2.Properties = $ruleproperties
$aclrule2.ResourceId = "AllowAll_Outbound"
$acllistproperties = new-object Microsoft.Windows.NetworkController.AccessControlListProperties
$acllistproperties.AclRules = @($aclrule1, $aclrule2)
New-NetworkControllerAccessControlList -ResourceId "AllowAll" -Properties $acllistproperties -ConnectionUri <NC REST FQDN>
```

>[!NOTE]
>Les informations de référence sur les commandes Windows PowerShell qui s'appliquent au Contrôleur de réseau se trouvent dans la rubrique [Cmdlets du Contrôleur de réseau](/powershell/module/networkcontroller/).

## <a name="use-acls-to-limit-traffic-on-a-subnet"></a>Utiliser des listes de contrôle d'accès pour limiter le trafic sur un sous-réseau
Dans cet exemple, vous allez créer une liste de contrôle d'accès qui empêchera les machines virtuelles du sous-réseau 192.168.0.0/24 de communiquer entre elles. Ce type de liste de contrôle d'accès permet de limiter la capacité d'un programme malveillant à se propager latéralement dans le sous-réseau, tout en permettant aux machines virtuelles de recevoir des requêtes provenant de l'extérieur du sous-réseau, ainsi que de communiquer avec d'autres services sur d'autres sous-réseaux.

|   IP Source    | IP de destination | Protocol | Port source | Port de destination | Sens | Action | Priority |
|:--------------:|:--------------:|:--------:|:-----------:|:----------------:|:---------:|:------:|:--------:|
|  192.168.0.1   |       \*       |   Tous    |     \*      |        \*        |  Trafic entrant  | Allow  |   100    |
|       \*       |  192.168.0.1   |   Tous    |     \*      |        \*        | Règle de trafic sortant  | Allow  |   101    |
| 192.168.0.0/24 |       \*       |   Tous    |     \*      |        \*        |  Trafic entrant  | Block  |   102    |
|       \*       | 192.168.0.0/24 |   Tous    |     \*      |        \*        | Règle de trafic sortant  | Block  |   103    |
|       \*       |       \*       |   Tous    |     \*      |        \*        |  Trafic entrant  | Allow  |   104    |
|       \*       |       \*       |   Tous    |     \*      |        \*        | Règle de trafic sortant  | Allow  |   105    |

---

La liste de contrôle d'accès créée par l'exemple de script ci-dessous, identifiée par l'ID de ressource **Subnet-192-168-0-0**, peut désormais être appliquée à un sous-réseau du réseau virtuel qui utilise l'adresse de sous-réseau « 192.168.0.0/24 ». Les règles de la liste de contrôle d'accès ci-dessus sont appliquées automatiquement à toute interface réseau connectée à ce sous-réseau du réseau virtuel.

Voici un exemple de script permettant de créer cette liste de contrôle d'accès à l'aide de l'API REST Contrôleur de réseau :

```PowerShell
import-module networkcontroller
$ncURI = "https://mync.contoso.local"
$aclrules = @()

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "192.168.0.1"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "100"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowRouter_Inbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "192.168.0.1"
$ruleproperties.Priority = "101"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowRouter_Outbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Deny"
$ruleproperties.SourceAddressPrefix = "192.168.0.0/24"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "102"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "DenySubnet_Inbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Deny"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "192.168.0.0/24"
$ruleproperties.Priority = "103"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "DenySubnet_Outbound"

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "104"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowAll_Inbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "105"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowAll_Outbound"
$aclrules += $aclrule

$acllistproperties = new-object Microsoft.Windows.NetworkController.AccessControlListProperties
$acllistproperties.AclRules = $aclrules

New-NetworkControllerAccessControlList -ResourceId "Subnet-192-168-0-0" -Properties $acllistproperties -ConnectionUri $ncURI
```

## <a name="add-an-acl-to-a-network-interface"></a>Ajouter une liste de contrôle d'accès à une interface réseau

Une fois que vous avez créé une liste de contrôle d'accès et que vous l'avez attribuée à un sous-réseau virtuel, vous pouvez remplacer la liste de contrôle d'accès par défaut du sous-réseau virtuel par une liste de contrôle d'accès spécifique pour une interface réseau individuelle. Depuis Windows Server 2019 Datacenter, vous pouvez appliquer des listes de contrôle d’accès directement aux interfaces réseau qui sont attachées aux réseaux logiques SDN, en plus des réseaux virtuels SDN. Si des listes de contrôle d’accès sont définies sur le sous-réseau virtuel connecté à l’interface réseau, toutes les listes de contrôle d’accès sont appliquées, et celles de l’interface réseau sont prioritaires sur celles du sous-réseau virtuel.

Dans cet exemple, nous allons vous montrer comment ajouter une liste de contrôle d'accès à un réseau virtuel.

>[!TIP]
>Il est également possible d'ajouter une liste de contrôle d'accès au moment de la création de l'interface réseau.

1. Récupérez ou créez l'interface réseau à laquelle vous allez ajouter la liste de contrôle d'accès.

   ```PowerShell
   $nic = get-networkcontrollernetworkinterface -ConnectionUri $uri -ResourceId "MyVM_Ethernet1"
   ```

2. Récupérez ou créez la liste de contrôle d'accès que vous allez ajouter à l'interface réseau.

   ```PowerShell
   $acl = get-networkcontrolleraccesscontrollist -ConnectionUri $uri -ResourceId "AllowAllACL"
   ```

3. Attribuez la liste de contrôle d'accès à la propriété AccessControlList de l'interface réseau.

   ```PowerShell
    $nic.properties.ipconfigurations[0].properties.AccessControlList = $acl
   ```

4. Ajoutez l'interface réseau dans le Contrôleur de réseau.

   ```PowerShell
   new-networkcontrollernetworkinterface -ConnectionUri $uri -Properties $nic.properties -ResourceId $nic.resourceid
   ```

## <a name="remove-an-acl-from-a-network-interface"></a>Supprimer une liste de contrôle d'accès d'une interface réseau

Dans cet exemple, nous allons vous montrer comment supprimer une liste de contrôle d'accès d'une interface réseau. La suppression d'une liste de contrôle d'accès applique l'ensemble de règles par défaut à l'interface réseau. L'ensemble de règles par défaut autorise tout le trafic sortant, mais bloque tout le trafic entrant. Si vous souhaitez autoriser tout le trafic entrant, vous devez suivre l'[exemple](#add-an-acl-to-a-network-interface) précédent afin d'ajouter une liste de contrôle d'accès qui autorise tout le trafic entrant et sortant.

1. Récupérez l'interface réseau à partir de laquelle vous allez supprimer la liste de contrôle d'accès.
   ```PowerShell
   $nic = get-networkcontrollernetworkinterface -ConnectionUri $uri -ResourceId "MyVM_Ethernet1"
   ```

2. Attribuez $null à la propriété AccessControlList d'ipConfiguration.
   ```PowerShell
   $nic.properties.ipconfigurations[0].properties.AccessControlList = $null
   ```

3. Ajoutez l'objet d'interface réseau dans le Contrôleur de réseau.
   ```PowerShell
   new-networkcontrollernetworkinterface -ConnectionUri $uri -Properties $nic.properties -ResourceId $nic.resourceid
   ```

## <a name="firewall-auditing"></a>Audit du pare-feu

Dans Windows Server 2019, une nouvelle fonctionnalité appelée « audit du pare-feu » a été ajoutée au pare-feu Datacenter. Celle-ci enregistre tous les flux qui sont traités par les règles de pare-feu SDN. Toutes les listes de contrôle d'accès pour lesquelles la journalisation est activée sont enregistrées. La syntaxe des fichiers journaux doit être cohérente avec celle des [journaux de flux Azure Network Watcher](/azure/network-watcher/network-watcher-nsg-flow-logging-overview). Ces journaux peuvent être utilisés à des fins de diagnostic ou archivés en vue d'une analyse ultérieure.

Voici un exemple de script permettant d'activer l'audit du pare-feu sur les serveurs hôtes. Commencez par mettre les variables à jour, puis exécutez le script sur un cluster Azure Stack HCI sur lequel le [Contrôleur de réseau](../concepts/network-controller-overview.md) est déployé :

```PowerShell
$logpath = "C:\test\log1"
$servers = @("sa18n22-2", "sa18n22-3", "sa18n22-4")
$uri = "https://sa18n22sdn.sa18.nttest.microsoft.com"

# Create log directories on the hosts
invoke-command -Computername $servers  {
    param(
        $Path
    )
    mkdir $path    -force
} -argumentlist $LogPath

# Set firewall auditing settings on Network Controller
$AuditProperties = new-object Microsoft.Windows.NetworkController.AuditingSettingsProperties
$AuditProperties.OutputDirectory = $logpath
set-networkcontrollerauditingsettingsconfiguration -connectionuri $uri -properties $AuditProperties -force  | out-null

# Enable logging on each server
$servers = get-networkcontrollerserver -connectionuri $uri
foreach ($s in $servers) {
    $s.properties.AuditingEnabled = @("Firewall")
    new-networkcontrollerserver -connectionuri $uri -resourceid $s.resourceid -properties $s.properties -force | out-null
}
```

Une fois activé, un nouveau fichier apparaît dans le répertoire spécifié de chaque hôte environ une fois par heure.  Vous devez régulièrement traiter ces fichiers et les supprimer des hôtes.  La longueur du fichier actuel est nulle et il est verrouillé jusqu'à ce qu'il soit vidé à l'heure suivante :

```syntax
PS C:\test\log1> dir

    Directory: C:\test\log1

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        7/19/2018   6:28 AM          17055 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL122803093.json
-a----        7/19/2018   7:28 AM           7880 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL132803173.json
-a----        7/19/2018   8:28 AM           7867 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL142803264.json
-a----        7/19/2018   9:28 AM          10949 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL152803360.json
-a----        7/19/2018   9:28 AM              0 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL162803464.json
```

Ces fichiers contiennent une séquence d'événements de flux, par exemple :

```syntax
{
    "records": [
        {
            "properties":{
                "Version":"1.0",
                "flows":[
                    {
                        "flows":[
                            {
                                "flowTuples":["1531963580,192.122.0.22,192.122.255.255,138,138,U,I,A"],
                                "portId":"9",
                                "portName":"7290436D-0422-498A-8EB8-C6CF5115DACE"
                            }
                        ],
                        "rule":"Allow_Inbound"
                    }
                ]
            },
            "operationName":"NetworkSecurityGroupFlowEvents",
            "resourceId":"394f647d-2ed0-4c31-87c5-389b8c0c8132",
            "time":"20180719:L012620622",
            "category":"NetworkSecurityGroupFlowEvent",
            "systemId":"d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a"
            },
```

Notez que la journalisation s'applique uniquement aux règles pour lesquelles le paramètre **Journalisation** est défini sur **Activée**, par exemple :

```syntax
{
    "Tags":  null,
    "ResourceRef":  "/accessControlLists/AllowAll",
    "InstanceId":  "4a63e1a5-3264-4986-9a59-4e77a8b107fa",
    "Etag":  "W/\"1535a780-0fc8-4bba-a15a-093ecac9b88b\"",
    "ResourceMetadata":  null,
    "ResourceId":  "AllowAll",
    "Properties":  {
                       "ConfigurationState":  null,
                       "ProvisioningState":  "Succeeded",
                       "AclRules":  [
                                        {
                                            "ResourceMetadata":  null,
                                            "ResourceRef":  "/accessControlLists/AllowAll/aclRules/AllowAll_Inbound",
                                            "InstanceId":  "ba8710a8-0f01-422b-9038-d1f2390645d7",
                                            "Etag":  "W/\"1535a780-0fc8-4bba-a15a-093ecac9b88b\"",
                                            "ResourceId":  "AllowAll_Inbound",
                                            "Properties":  {
                                                               "Protocol":  "All",
                                                               "SourcePortRange":  "0-65535",
                                                               "DestinationPortRange":  "0-65535",
                                                               "Action":  "Allow",
                                                               "SourceAddressPrefix":  "*",
                                                               "DestinationAddressPrefix":  "*",
                                                               "Priority":  "101",
                                                               "Description":  null,
                                                               "Type":  "Inbound",
                                                               "Logging":  "Enabled",
                                                               "ProvisioningState":  "Succeeded"
                                                           }
                                        },
                                        {
                                            "ResourceMetadata":  null,
                                            "ResourceRef":  "/accessControlLists/AllowAll/aclRules/AllowAll_Outbound",
                                            "InstanceId":  "068264c6-2186-4dbc-bbe7-f504c6f47fa8",
                                            "Etag":  "W/\"1535a780-0fc8-4bba-a15a-093ecac9b88b\"",
                                            "ResourceId":  "AllowAll_Outbound",
                                            "Properties":  {
                                                               "Protocol":  "All",
                                                               "SourcePortRange":  "0-65535",
                                                               "DestinationPortRange":  "0-65535",
                                                               "Action":  "Allow",
                                                               "SourceAddressPrefix":  "*",
                                                               "DestinationAddressPrefix":  "*",
                                                               "Priority":  "110",
                                                               "Description":  null,
                                                               "Type":  "Outbound",
                                                               "Logging":  "Enabled",
                                                               "ProvisioningState":  "Succeeded"
                                                           }
                                        }
                                    ],
                       "IpConfigurations":  [

                                            ],
                       "Subnets":  [
                                       {
                                           "ResourceMetadata":  null,
                                           "ResourceRef":  "/virtualNetworks/10_0_1_0/subnets/Subnet1",
                                           "InstanceId":  "00000000-0000-0000-0000-000000000000",
                                           "Etag":  null,
                                           "ResourceId":  null,
                                           "Properties":  null
                                       }
                                   ]
                   }
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des informations connexes, reportez-vous également à :

- [Vue d'ensemble du Pare-feu Datacenter](../concepts/datacenter-firewall-overview.md)
- [Vue d’ensemble du contrôleur de réseau](../concepts/network-controller-overview.md)
- [SDN dans Azure Stack HCI et Windows Server](../concepts/software-defined-networking.md)