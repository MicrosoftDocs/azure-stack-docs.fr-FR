---
title: Configurer des pare-feu pour Azure Stack HCI
description: Cette rubrique fournit des conseils sur la façon de configurer des pare-feu pour le système d’exploitation Azure Stack HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 28fd04d9fb84f612dca6b241b8935b8f9cbfe049
ms.sourcegitcommit: 7ee28fad5b8ba628b1a7dc3d82cabfc36aa62f0d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2021
ms.locfileid: "102250318"
---
# <a name="configure-firewalls-for-azure-stack-hci"></a>Configurer des pare-feu pour Azure Stack HCI

>S’applique à : Azure Stack HCI, version 20H2

Cette rubrique fournit des conseils sur la façon de configurer des pare-feu pour le système d’exploitation Azure Stack HCI. Elle traite notamment des exigences de connectivité, et explique comment les étiquettes de service regroupent les adresses IP dans Azure auxquelles le système d’exploitation doit accéder. Elle présente également les étapes de mise à jour du pare-feu Microsoft Defender.

## <a name="connectivity-requirements"></a>Connectivité requise
Azure Stack HCI doit se connecter régulièrement à Azure. L’accès est limité aux éléments suivants :
- Adresses IP Azure connues
- Direction sortante
- Port 443 (HTTPS)

Pour plus d’informations, consultez la section « Connectivité Azure Stack HCI » du [FAQ Azure Stack HCI](../faq.yml).

Cette rubrique explique comment utiliser une configuration de pare-feu très verrouillée afin, si vous le souhaitez, de bloquer tout le trafic vers toutes les destinations, sauf celles incluses dans votre liste verte.

   >[!IMPORTANT]
   > Si la connectivité sortante est limitée par votre pare-feu d’entreprise externe ou votre serveur proxy, vérifiez que les URL listées dans le tableau ci-dessous ne sont pas bloquées. Pour obtenir des informations connexes, consultez la section « Configuration de la mise en réseau » de [Présentation de l’agent des serveurs activés par Azure Arc](/azure/azure-arc/servers/agent-overview#networking-configuration).


Comme indiqué ci-dessous, Azure Stack HCI accède à Azure en utilisant potentiellement plusieurs pare-feu.

:::image type="content" source="./media/configure-firewalls/firewalls-diagram.png" alt-text="Le diagramme montre Azure Stack HCI accédant aux points de terminaison d’étiquettes de service par le biais du port 443 (HTTPS) des pare-feu." lightbox="./media/configure-firewalls/firewalls-diagram.png":::

## <a name="working-with-service-tags"></a>Utilisation d’étiquettes de service
Une *étiquette de service* représente un groupe d’adresses IP d’un service Azure donné. Microsoft gère les adresses IP incluses dans l’étiquette de service, et met automatiquement à jour l’étiquette de service à mesure que les adresses IP changent, afin de limiter les mises à jour au minimum. Pour plus d’informations, consultez [Étiquette de service de réseau virtuel](/azure/virtual-network/service-tags-overview).

## <a name="required-endpoint-daily-access-after-azure-registration"></a>Accès quotidien aux points de terminaison requis (après inscription auprès d’Azure)
Azure gère les adresses IP connues pour les services Azure qui sont organisés à l’aide d’étiquettes de service. Azure publie un fichier JSON hebdomadaire de toutes les adresses IP pour chaque service. Les adresses IP ne changent pas souvent, mais elles changent tout de même quelques fois par an. Le tableau suivant présente les points de terminaison d’étiquettes de service auxquels le système d’exploitation doit accéder.

| Description                   | Étiquette de service pour plage d’adresses IP  | URL                                                                                 |
| :-----------------------------| :-----------------------  | :---------------------------------------------------------------------------------- |
| Azure Active Directory        | AzureActiveDirectory      | `https://login.microsoftonline.com`<br> `https://graph.microsoft.com`               |
| Azure Resource Manager        | AzureResourceManager      | `https://management.azure.com`                        |
| Service cloud Azure Stack HCI | AzureFrontDoor.Frontend   | `https://azurestackhci.azurefd.net` |
| Azure Arc                     | AzureArcInfrastructure<br> AzureTrafficManager | Dépend de la fonctionnalité que vous souhaitez utiliser :<br> Service d’identité hybride : `*.his.arc.azure.com`<br> Guest Configuration : `*.guestconfiguration.azure.com`<br> **Remarque :** D’autres URL seront ajoutées à mesure que nous activerons davantage de fonctionnalités. |

## <a name="update-microsoft-defender-firewall"></a>Mettre à jour le pare-feu Microsoft Defender
Cette section montre comment configurer le pare-feu Microsoft Defender pour permettre aux adresses IP associées à une étiquette de service de se connecter au système d’exploitation :

1. Téléchargez le fichier JSON à partir de la ressource suivante sur l’ordinateur cible exécutant le système d’exploitation : [Plages d’adresses IP et étiquettes de services - Cloud public](https://www.microsoft.com/download/details.aspx?id=56519).

1. Utilisez la commande PowerShell suivante pour ouvrir le fichier JSON :

    ```powershell
    $json = Get-Content -Path .\ServiceTags_Public_20201012.json | ConvertFrom-Json
    ```

1. Obtenez la liste des plages d’adresses IP pour une étiquette de service donnée, telle que « AzureResourceManager » :

    ```powershell
    $IpList = ($json.values | where Name -Eq "AzureResourceManager").properties.addressPrefixes
    ```

1. Importez la liste des adresses IP sur votre pare-feu d’entreprise externe, si vous utilisez une liste verte.

1. Créez une règle de pare-feu pour chaque serveur du cluster afin d’autoriser le trafic sortant 443 (HTTPS) vers la liste des plages d’adresses IP :

    ```powershell
    New-NetFirewallRule -DisplayName "Allow Azure Resource Manager" -RemoteAddress $IpList -Direction Outbound -LocalPort 443 -Protocol TCP -Action Allow -Profile Any -Enabled True
    ```

## <a name="additional-endpoint-for-one-time-azure-registration"></a>Point de terminaison supplémentaire pour l’inscription Azure ponctuelle
Pendant le processus d’inscription Azure, lorsque vous exécutez `Register-AzStackHCI` ou le Centre d’administration Windows, l’applet de commande tente de contacter PowerShell Gallery pour vérifier que vous disposez de la dernière version des modules PowerShell nécessaires, comme Az et AzureAD.

Même si le référentiel PowerShell Gallery est hébergé sur Azure, il n’existe actuellement pas d’étiquette de service pour lui. Si vous ne pouvez pas exécuter l’applet de commande `Register-AzStackHCI` à partir d’un nœud de serveur car il n’a pas accès à Internet, nous vous recommandons de télécharger les modules sur votre ordinateur de gestion, puis de les transférer manuellement vers le nœud de serveur sur lequel vous souhaitez exécuter l’applet de commande.

## <a name="set-up-a-proxy-server"></a>Configurer un serveur proxy
Pour configurer un serveur proxy pour Azure Stack HCI, exécutez la commande PowerShell suivante en tant qu’administrateur :

```powershell
Set-WinInetProxy -ProxySettingsPerUser 0 -ProxyServer webproxy1.com:9090
```

Utilisez l’indicateur `ProxySettingsPerUser 0` pour étendre la configuration du proxy à l’ensemble du serveur au lieu d’effectuer une configuration par utilisateur, telle que prévue par défaut. 

Téléchargez le script WinInetProxy.psm1 à partir de : [PowerShell Gallery | WinInetProxy.psm1 0.1.0](https://www.powershellgallery.com/packages/WinInetProxy/0.1.0/Content/WinInetProxy.psm1).

## <a name="network-port-requirements"></a>Configuration requise des ports réseau
Vérifiez que les ports réseau appropriés sont ouverts entre tous les nœuds serveur au sein d’un site et entre les sites (pour les clusters étendus). Vous avez besoin de règles de pare-feu et de routeur appropriées pour autoriser les protocoles ICMP, SMB (port 445, plus port 5445 pour SMB Direct) et WS-MAN (port 5985) à trafic bidirectionnel entre tous les serveurs du cluster.

Lorsque vous utilisez l’Assistant Création d’un cluster dans Windows Admin Center pour créer le cluster, l’Assistant ouvre automatiquement les ports de pare-feu appropriés sur chaque serveur du cluster pour le clustering de basculement, Hyper-V et le réplica de stockage. Si vous utilisez un pare-feu différent sur chaque serveur, ouvrez les ports suivants :

### <a name="failover-clustering-ports"></a>Ports de clustering de basculement
- ICMPv4 et ICMPv6
- Port TCP 445
- Ports dynamiques RPC
- Port TCP 135
- Port TCP 137
- Port TCP 3343
- Port UDP 3343

### <a name="hyper-v-ports"></a>Ports Hyper-V
- Port TCP 135
- Port TCP 80 (connectivité HTTP)
- Port TCP 443 (connectivité HTTPS)
- Port TCP 6600
- Port TCP 2179
- Ports dynamiques RPC
- Mappeur de point de terminaison RPC
- Port TCP 445

### <a name="storage-replica-ports-stretched-cluster"></a>Ports de réplica de stockage (cluster étendu)
- Port TCP 445
- TCP 5445 (si vous utilisez iWarp RDMA)
- Port TCP 5985
- ICMPv4 et ICMPv6 (si vous utilisez l’applet de commande PowerShell `Test-SRTopology`)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations, consultez également :
- La section Connectivité du [FAQ sur Azure Stack HCI](../faq.yml)