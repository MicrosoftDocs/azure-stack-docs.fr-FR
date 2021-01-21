---
title: Configurer des pare-feu pour Azure Stack HCI
description: Cette rubrique fournit des conseils sur la façon de configurer des pare-feu pour le système d’exploitation Azure Stack HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/06/2020
ms.openlocfilehash: a67881f2dd4be5e4dce5fb967c88484c27025624
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255229"
---
# <a name="configure-firewalls-for-azure-stack-hci"></a>Configurer des pare-feu pour Azure Stack HCI

>S’applique à : Azure Stack HCI, version 20H2

Cette rubrique fournit des conseils sur la façon de configurer des pare-feu pour le système d’exploitation Azure Stack HCI. Elle traite notamment des exigences de connectivité, et explique comment les étiquettes de service regroupent les adresses IP dans Azure auxquelles le système d’exploitation doit accéder. Elle présente également les étapes de mise à jour du pare-feu Microsoft Defender.

## <a name="connectivity-requirements"></a>Connectivité requise
Azure Stack HCI doit se connecter régulièrement à Azure. L’accès est limité aux éléments suivants :
- Adresses IP Azure connues
- Direction sortante
- Port 443 (HTTPS)

Pour plus d’informations, consultez la section « Connectivité Azure Stack HCI » du [FAQ Azure Stack HCI](../faq.md).

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
Pendant le processus d’inscription Azure, lorsque vous exécutez `Register-AzStackHCI` ou le Centre d’administration Windows, l’applet de commande tente de contacter PowerShell Gallery pour vérifier que vous disposez de la dernière version des modules PowerShell nécessaires, comme Az et AzureAD. Même si le référentiel PowerShell Gallery est hébergé sur Azure, il n’existe actuellement pas d’étiquette de service pour lui. Si vous ne pouvez pas exécuter l’applet de commande `Register-AzStackHCI` à partir d’un nœud de serveur car il n’a pas accès à Internet, nous vous recommandons de télécharger les modules sur votre ordinateur de gestion, puis de les transférer manuellement vers le nœud de serveur sur lequel vous souhaitez exécuter l’applet de commande.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations, consultez également :
- La section Connectivité du [FAQ sur Azure Stack HCI](../faq.md)