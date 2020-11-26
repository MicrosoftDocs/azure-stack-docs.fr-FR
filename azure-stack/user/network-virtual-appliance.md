---
title: Résoudre les problèmes d’appliance virtuelle réseau sur Azure Stack Hub
description: Résolvez les problèmes de connectivité de machines virtuelles ou de réseau privé virtuel (VPN) lors de l’utilisation d’une appliance virtuelle réseau dans Microsoft Azure Stack Hub.
author: sethmanheim
ms.author: sethm
ms.date: 11/22/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: 271587baa3890a7dbb02d7ac935ceb51e2e405b7
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517146"
---
# <a name="troubleshoot-network-virtual-appliance-problems"></a>Résoudre les problèmes d’appliance virtuelle réseau

Vous pouvez rencontrer des problèmes de connectivité avec les machines virtuelles ou les réseaux privés virtuels qui utilisent une appliance virtuelle réseau dans Azure Stack Hub.

Cet article explique comment valider les exigences de plateforme de base d’Azure Stack Hub pour des configurations d’appliance virtuelle réseau (NVA).

Le fournisseur d’une appliance virtuelle réseau assure le support technique pour celle-ci ainsi que son intégration à la plateforme Azure Stack Hub.

> [!NOTE]
> Si vous rencontrez un problème de connectivité ou de routage qui implique une appliance virtuelle réseau, vous devez [contacter directement le fournisseur de l’appliance virtuelle réseau](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Si cet article ne traite pas votre problème d’appliance virtuelle réseau avec Azure Stack Hub, créez un [ticket de support Azure Stack Hub](../operator/azure-stack-manage-basics.md#where-to-get-support).

## <a name="checklist-for-troubleshooting-with-an-nva-vendor"></a>Check-list pour résoudre les problèmes avec un fournisseur d’appliance virtuelle réseau

- Mises à jour des logiciels des machines virtuelles d’appliance virtuelle réseau.
- Fonctionnalités et configuration des comptes de service.
- Routes définies par l’utilisateur sur des sous-réseaux de réseau virtuel qui dirigent le trafic vers l’appliance virtuelle réseau.
- Routes définies par l’utilisateur sur des sous-réseaux de réseau virtuel qui dirigent le trafic à partir de l’appliance virtuelle réseau.
- Tables et règles de routage au sein de l’appliance virtuelle réseau (par exemple, de NIC1 à NIC2).
- Suivi sur les cartes réseau d’appliance virtuelle réseau pour vérifier la réception et l’envoi de trafic réseau.

## <a name="basic-troubleshooting-steps"></a>Étapes de dépannage de base

1. Vérification de la configuration de base.
2. Vérification des performances de l’appliance virtuelle réseau.
3. Résolution des problèmes réseau avancée.

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Vérifiez la configuration minimale requise pour les appliances virtuelles réseau sur Azure

Chaque appliance virtuelle réseau doit respecter les configurations de base requises pour fonctionner correctement sur Azure Stack Hub. Cette section présente les étapes de vérification de ces configurations de base. Pour plus d’informations, [contactez le fournisseur de l’appliance virtuelle réseau](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

> [!IMPORTANT]
> Quand ils utilisent un tunnel S2S, les paquets sont davantage encapsulés avec des en-têtes supplémentaires. Cette encapsulation augmente la taille globale de chaque paquet.
>
> Dans ce scénario, vous devez fixer la taille maximale de segment du protocole TCP à 1 350 octets. Dans le cas où vos appareils VPN ne prendraient pas en charge la limitation de la taille maximale de segment, vous pouvez à la place définir l’unité de transmission maximale dans l’interface de tunnel sur 1 400 octets.
>
> Pour plus d’informations, consultez [Réglage des performances TCP/IP des réseaux virtuels](/azure/virtual-network/virtual-network-tcpip-performance-tuning).

### <a name="check-whether-ip-forwarding-is-enabled-on-the-nva"></a>Vérifier si le transfert IP est activé sur l’appliance virtuelle réseau

### <a name="portal"></a>[Portail](#tab/portal)

1. Localisez la ressource d’appliance virtuelle réseau sur le portail Azure Stack Hub, sélectionnez **Mise en réseau**, puis sélectionnez l’interface réseau.
2. Dans la page **Interface réseau**, sélectionnez **Configuration IP**.
3. Assurez-vous que le transfert IP est activé.

### <a name="powershell-az"></a>[PowerShell Az](#tab/az)

1. Exécutez la commande suivante : Remplacez les valeurs figurant entre crochets pointus par vos informations.

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   ```

2. Vérifiez la propriété **EnableIPForwarding**.

3. Si le transfert IP n’est pas activé, exécutez les commandes suivantes pour l’activer :

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

### <a name="powershell-azurerm"></a>[PowerShell AzureRM](#tab/azurerm)

1. Exécutez la commande suivante : Remplacez les valeurs figurant entre crochets pointus par vos informations.

   ```powershell
   Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   ```

2. Vérifiez la propriété **EnableIPForwarding**.

3. Si le transfert IP n’est pas activé, exécutez les commandes suivantes pour l’activer :

   ```powershell
   $nic2 = Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   $nic2.EnableIPForwarding = 1
   Set-AzureRMNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

---

### <a name="check-whether-traffic-can-be-routed-to-the-nva"></a>Vérifier si le trafic peut être routé vers l’appliance virtuelle réseau

1. Localisez une machine virtuelle qui est configurée pour rediriger le trafic vers l’appliance virtuelle réseau.
1. Pour vérifier que l’appliance virtuelle réseau est le tronçon suivant, exécutez `Tracert <Private IP of NVA>` pour Windows ou `Traceroute <Private IP of NVA>`.
1. Si l’appliance virtuelle réseau n’est pas listée comme tronçon suivant, vérifiez et mettez à jour les tables de routage Azure Stack Hub.

Certains systèmes d’exploitation de niveau invité peuvent mettre en place des stratégies de pare-feu pour bloquer le trafic ICMP. Mettez à jour ces règles de pare-feu pour que les commandes précédentes fonctionnent.

### <a name="check-whether-traffic-can-reach-the-nva"></a>Vérifier si le trafic peut atteindre l’appliance virtuelle réseau

1. Localisez une machine virtuelle qui devrait être connectée à l’appliance virtuelle réseau.
1. Vérifiez si des groupes de sécurité réseau bloquent le trafic. Pour Windows, exécutez `ping` (ICMP) ou `Test-NetConnection <Private IP of NVA>` (TCP). Pour Linux, exécutez `Tcpping <Private IP of NVA>`.
1. Si vos groupes de sécurité réseau bloquent le trafic, modifiez-les pour autoriser le trafic.

### <a name="check-whether-the-nva-and-vms-are-listening-for-expected-traffic"></a>Vérifier si l’appliance virtuelle réseau et les machines virtuelles écoutent le trafic attendu

1. Connectez-vous à l’appliance virtuelle réseau en utilisant le protocole RDP ou SSH, puis exécutez la commande suivante :

   **Windows**

   ```shell
   netstat -an
   ```

   **Linux**

   ```shell
   netstat -an | grep -i listen
   ```

1. Recherchez les ports TCP utilisés par le logiciel de l’appliance virtuelle réseau qui est listé dans les résultats. Si vous ne les voyez pas, configurez l’application sur l’appliance virtuelle réseau et la machine virtuelle pour écouter le trafic qui atteint ces ports et y répondre. [Contactez le fournisseur de l’appliance virtuelle réseau pour obtenir de l’aide](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Vérifier les performances de l’appliance virtuelle réseau

### <a name="validate-vm-cpu-usage"></a>Valider l’utilisation du processeur de machine virtuelle

Si l’utilisation du processeur approche 100 pour cent, vous risquez de rencontrer des problèmes affectant les rejets de paquets réseau.

Lors d’un pic d’utilisation du processeur, recherchez sur la machine virtuelle invitée quel processus est à l’origine de l’utilisation élevée du processeur. Limitez ensuite l’utilisation autant que possible.

Vous devrez peut-être aussi redimensionner la machine virtuelle en définissant une taille de référence SKU supérieure ou, pour un groupe identique de machines virtuelles, augmenter le nombre d’instances.

Si vous avez besoin d’aide, [contactez le fournisseur de l’appliance virtuelle réseau](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="validate-vm-network-statistics"></a>Valider les statistiques du réseau de machines virtuelles

Si l’utilisation du réseau de machines virtuelles présente des pics ou des périodes d’utilisation intense, envisagez d’augmenter la taille de référence SKU de la machine virtuelle pour obtenir un débit plus élevé.

## <a name="advanced-network-administrator-troubleshooting"></a>Dépannage d’administrateur réseau avancé

### <a name="capture-a-network-trace"></a>Capturer une trace réseau

Pendant que vous exécutez [`PsPing`](/sysinternals/downloads/psping) ou `Nmap`, capturez une trace réseau simultanée sur les machines virtuelles source et de destination ainsi que sur l’appliance virtuelle réseau. Arrêtez ensuite la trace.

1. Pour capturer une trace réseau simultanée, exécutez la commande suivante :

   **Windows**

   ```shell
   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection
   ```

   **Linux**

   ```shell
   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap
   ```

2. Utilisez `PsPing` ou `Nmap` à partir de la machine virtuelle source vers la machine virtuelle de destination. `PsPing 10.0.0.4:80` ou `Nmap -p 80 10.0.0.4` en sont des exemples.

3. Ouvrez le suivi réseau à partir de la machine virtuelle de destination en utilisant **tcpdump** ou un analyseur de paquets de votre choix. Appliquez un filtre d’affichage pour l’adresse IP de la machine virtuelle source à partir de laquelle vous avez exécuté `PsPing` ou `Nmap`. Un exemple Windows **netmon** est `IPv4.address==10.0.0.4`. `tcpdump -nn -r vmtrace.cap src` et `dst host 10.0.0.4` dont des exemples Linux.

### <a name="analyze-traces"></a>Analyser les traces

Si vous ne voyez pas les paquets entrants dans la trace de la machine virtuelle back-end, il est probable qu’un groupe de sécurité réseau ou une route définie par l’utilisateur interfère, ou que les tables de routage de l’appliance virtuelle réseau soient incorrectes.

Si vous voyez les paquets entrants, mais qu’ils sont sans réponse, vous devrez peut-être résoudre un problème lié au pare-feu ou à l’application de la machine virtuelle.

Si vous avez besoin d’aide, [contactez le fournisseur de l’appliance virtuelle réseau](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="create-a-support-ticket"></a>Création d’un ticket de support

Si les étapes précédentes ne permettent pas de résoudre votre problème, créez un [ticket de support](../operator/azure-stack-manage-basics.md#where-to-get-support) et utilisez l’[outil de collecte de journaux à la demande](../operator/azure-stack-diagnostic-log-collection-overview.md) pour fournir des journaux.
