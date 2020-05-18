---
title: Résoudre les problèmes d’appliance virtuelle réseau sur Azure Stack Hub
description: Résolvez des problèmes de connectivité de machines virtuelles ou de réseau privé virtuel (VPN), ainsi que des erreurs lors de l’utilisation d’une appliance virtuelle réseau (NVA) tierce dans Microsoft Azure Stack Hub.
author: sethmanheim
ms.author: sethm
ms.date: 05/12/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 05/12/2020
ms.openlocfilehash: 5e42f9e1b099df2ab1dc1063b3bf2710f59ad66f
ms.sourcegitcommit: 999c6cd0ab64cd2d695feb8405a9c720c9ae755b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83342944"
---
# <a name="troubleshoot-network-virtual-appliance-issues"></a>Résoudre les problèmes d’appliance virtuelle réseau

Vous pouvez rencontrer des problèmes de connectivité de machines virtuelles ou de réseau privé virtuel (VPN), ainsi que des erreurs lors de l’utilisation d’une appliance virtuelle réseau (NVA) tierce dans Microsoft Azure Stack Hub. Cet article décrit les étapes élémentaires permettant de valider les exigences de la plateforme Azure Stack Hub de base pour des configurations d’appliance virtuelle réseau.

Un support technique pour les appliances virtuelles réseau tierces et leur intégration à la plateforme Azure Stack Hub est assuré par le fournisseur de l’appliance virtuelle réseau.

> [!NOTE]
> Si vous rencontrez un problème de connectivité ou de routage qui implique une appliance virtuelle réseau, vous devez [contactez directement le fournisseur de l’appliance virtuelle réseau](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Si le problème d’appliance virtuelle réseau Azure Stack Hub n’est pas traité dans cet article, créez un [ticket de support Azure Stack Hub](../operator/azure-stack-manage-basics.md#where-to-get-support).

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Liste de contrôle pour le dépannage avec un fournisseur d’appliance virtuelle réseau

- Mises à jour logicielles des machines virtuelles d’appliance virtuelle réseau.
- Fonctionnalités et configuration des comptes de service.
- Routes définies par l’utilisateur sur des sous-réseaux de réseau virtuel qui dirigent le trafic vers l’appliance virtuelle réseau.
- Routes définies par l’utilisateur sur des sous-réseaux de réseau virtuel qui dirigent le trafic à partir de l’appliance virtuelle réseau.
- Tables et règles de routage au sein de l’appliance virtuelle réseau (par exemple, de NIC1 à NIC2).
- Suivi sur les cartes réseau d’appliance virtuelle réseau pour vérifier la réception et l’envoi de trafic réseau.

## <a name="basic-troubleshooting-steps"></a>Étapes de dépannage de base

- Vérification de la configuration de base.
- Vérification des performances de l’appliance virtuelle réseau.
- Dépannage de réseau avancé.

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Vérifiez la configuration minimale requise pour les appliances virtuelles réseau sur Azure

Chaque appliance virtuelle réseau comporte des exigences de configuration de base pour fonctionner correctement sur Azure Stack Hub. La section suivante décrit les étapes de vérification de ces configurations de base. Pour plus d’informations, [contactez le fournisseur de l’appliance virtuelle réseau](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

> [!IMPORTANT]
> Lors de l’utilisation d’un tunnel S2S, les paquets sont davantage encapsulés avec des en-têtes supplémentaires. Cette encapsulation augmente la taille globale du paquet. Dans ces scénarios, vous devez définir TCP **MSS** sur **1350**. Dans le cas où vos appareils VPN ne prendraient pas en charge la limitation de la taille maximale de segment, vous pouvez à la place définir l’unité de transmission maximale dans l’interface de tunnel sur **1400** octets. Pour plus d’informations, consultez [Réglage des performances TCPIP des réseaux virtuels](/azure/virtual-network/virtual-network-tcpip-performance-tuning).

### <a name="check-whether-ip-forwarding-is-enabled-on-nva"></a>Vérifier si le transfert IP est activé sur l’appliance virtuelle réseau

#### <a name="use-the-azure-stack-hub-portal"></a>Utiliser le portail Azure Stack Hub

- Localisez la ressource d’appliance virtuelle réseau sur le portail Azure Stack Hub, sélectionnez **Mise en réseau**, puis sélectionnez l’interface réseau.
- Dans la page **Interface réseau**, sélectionnez **Configuration IP**.
- Assurez-vous que le transfert IP est activé.

#### <a name="use-powershell"></a>Utiliser PowerShell

- Exécutez la commande suivante (en remplaçant les valeurs entre crochets par vos informations) :

   ```powershell
   Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   ```

- Vérifiez la propriété **EnableIPForwarding**.
- Si le transfert IP n’est pas activé, exécutez les commandes suivantes pour l’activer :

   ```powershell
   $nic2 = Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   $nic2.EnableIPForwarding = 1
   Set-AzureRMNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

### <a name="check-whether-the-traffic-can-be-routed-to-the-nva"></a>Vérifier si le trafic peut être routé vers l’appliance virtuelle réseau

- Localiser une machine virtuelle qui est configurée pour rediriger le trafic vers l’appliance virtuelle réseau
- Exécutez `Tracert <Private IP of NVA>` pour Windows, ou `Traceroute <Private IP of NVA>` pour rechercher une appliance virtuelle réseau comme tronçon suivant.
- Si l’appliance virtuelle réseau n’est pas listée comme tronçon suivant, vérifiez et mettez à jour les tables de routage Azure Stack Hub.

Certains systèmes d’exploitation de niveau invité peuvent mettre en place des stratégies de pare-feu pour bloquer le trafic ICMP. Ces règles de pare-feu doivent être mises à jour pour que les commandes précédentes fonctionnent.

### <a name="check-whether-the-traffic-can-reach-the-nva"></a>Vérifier si le trafic peut atteindre l’appliance virtuelle réseau

- Localisez une machine virtuelle qui devrait être connectée à l’appliance virtuelle réseau.
- Exécutez `ping (ICMP)` ou `Test-NetConnection (TCP) <Private IP of NVA>` pour Windows, et `Tcpping <Private IP of NVA>` pour Linux, pour vérifier si le trafic est bloqué par des groupes de sécurité réseau.
- Si le trafic est bloqué, modifiez vos groupes pour autoriser le trafic.

### <a name="check-whether-nva-and-vms-are-listening-for-expected-traffic"></a>Vérifier si l’appliance virtuelle réseau et les machines virtuelles écoutent le trafic attendu

- Connectez-vous à l’appliance virtuelle réseau en utilisant le protocole RDP ou SSH, puis exécutez la commande suivante :

   **Windows** :
   ```shell
   netstat -an
   ```

   **Linux** :
   ```shell
   netstat -an | grep -i listen
   ```

- Si vous ne voyez pas le port TCP qui est utilisé par le logiciel de l’appliance virtuelle réseau listé dans les résultats, vous devez configurer l’application sur l’appliance virtuelle réseau et la machine virtuelle pour écouter le trafic qui atteint ces ports et y répondre. [Contactez le fournisseur de l’appliance virtuelle réseau au besoin](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Vérifier les performances de l’appliance virtuelle réseau

### <a name="validate-vm-cpu"></a>Valider le processeur de machine virtuelle

Si l’utilisation du processeur approche 100 pour cent, vous risquez de rencontrer des problèmes affectant les rejets de paquet réseau. Lors d’un pic d’utilisation du processeur, recherchez sur la machine virtuelle invitée quel processus est à l’origine de l’utilisation élevée du processeur, et limitez-le autant que possible. Il se peut que vous deviez également redimensionner la machine virtuelle en définissant une taille de référence SKU supérieure ou, pour un groupe de machines virtuelles identiques, augmenter le nombre d’instances. Pour chacun de ces problèmes, [contactez au besoin le fournisseur de l’appliance virtuelle réseau](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="validate-vm-network-statistics"></a>Valider les statistiques du réseau de machines virtuelles

Si l’utilisation du réseau de machines virtuelles présente des pics ou des périodes d’utilisation intense, il se peut également que vous deviez augmenter la taille de la référence SKU de la machine virtuelle pour obtenir des capacités de débit supérieures.

## <a name="advanced-network-administrator-troubleshooting"></a>Dépannage d’administrateur réseau avancé

### <a name="capture-network-trace"></a>Capturer la trace réseau

Capturez une trace réseau simultanée sur la machine virtuelle source, l’appliance virtuelle réseau et la machine virtuelle de destination pendant que vous exécutez [**PsPing**](/sysinternals/downloads/psping) ou **Nmap**, puis arrêtez la capture de la trace.

- Pour capturer une trace réseau simultanée, exécutez la commande suivante :

   **Windows**

   ```shell
   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection
   ```

   **Linux**

   ```shell
   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap
   ```

- Utilisez **PsPing** ou **Nmap** à partir de la machine virtuelle source vers la machine virtuelle de destination (par exemple, `PsPing 10.0.0.4:80` ou `Nmap -p 80 10.0.0.4`).

- Ouvrez le suivi réseau à partir de la machine virtuelle de destination en utilisant **tcpdump** ou un analyseur de paquets de votre choix. Appliquez un filtre d’affichage pour l’adresse IP de la machine virtuelle source à partir de laquelle vous avez exécuté **PsPing** ou **Nmap**, par exemple `IPv4.address==10.0.0.4` (Windows netmon) ou `tcpdump -nn -r vmtrace.cap src` ou `dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Analyser les traces

Si vous ne voyez pas les paquets entrants dans la trace de la machine virtuelle back-end, il est probable qu’un groupe de sécurité réseau ou une route définie par l’utilisateur interfère, ou que les tables de routage de l’appliance virtuelle réseau soient incorrectes.

Si vous voyez les paquets entrants, mais qu’ils sont sans réponse, vous devrez peut-être résoudre un problème lié au pare-feu ou à l’application de la machine virtuelle. Pour chacun de ces problèmes, [contactez au besoin le fournisseur de l’appliance virtuelle réseau](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="create-a-support-ticket"></a>Création d’un ticket de support

Si aucune des étapes précédentes ne résout votre problème, créez un [ticket de support](../operator/azure-stack-manage-basics.md#where-to-get-support) et utilisez l’[outil de collecte de journaux à la demande](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md) pour fournir des journaux.
