---
title: Notes de publication du Kit de développement Microsoft Azure Stack | Microsoft Docs
description: Améliorations, correctifs et problèmes connus pour le Kit de développement Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 05/01/2019
ms.openlocfilehash: 935f144ebbb40da66ac43fc8e9d5dfc7c3e3d0b6
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64983598"
---
# <a name="asdk-release-notes"></a>Notes de publication relatives à l’ASDK

Cet article fournit des informations sur des modifications, des correctifs et des problèmes connus en lien avec le Kit de développement Azure Stack (ASDK). Si vous n’êtes pas sûr de la version que vous exécutez, consultez le [portail pour vérifier](../operator/azure-stack-updates.md#determine-the-current-version).

Tenez-vous informé des nouveautés concernant le kit ASDK en vous abonnant au [flux RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11904036"></a>Build 1.1904.0.36

<!-- ### Changes -->

### <a name="new-features"></a>Nouvelles fonctionnalités

- Pour obtenir la liste des nouvelles fonctionnalités dans cette version, consultez [cette section](../operator/azure-stack-release-notes-1904.md#whats-in-this-update) des notes de publication Azure Stack.

### <a name="fixed-and-known-issues"></a>Problèmes connus et résolus

- Résolution du problème de connexion VPN identifié [ici, dans la version 1902](#known-issues).

- Pour obtenir la liste des autres problèmes Azure Stack corrigés dans cette version, consultez [cette section](../operator/azure-stack-release-notes-1904.md#fixes) des notes de publication Azure Stack.
- Pour obtenir la liste des problèmes connus, consultez [cet article](../operator/azure-stack-release-notes-known-issues-1904.md).
- Notez que les [correctifs logiciels d’Azure Stack](../operator/azure-stack-release-notes-1904.md#hotfixes) ne sont pas applicables au kit ASDK Azure Stack.

## <a name="build-1903"></a>Build 1903

La charge utile 1903 n’inclut aucune version ASDK.

## <a name="build-11902069"></a>Build 1.1902.0.69

### <a name="new-features"></a>Nouvelles fonctionnalités

- La build 1902 introduit une nouvelle interface utilisateur sur le portail Administrateur Azure Stack pour la création de plans, d’offres, de quotas et de plans complémentaires. Pour plus d’informations, y compris des captures d’écran, consultez [Créer des plans, des offres et des quotas](../operator/azure-stack-create-plan.md).

- Pour obtenir la liste des autres changements et améliorations dans cette version, consultez [cette section](../operator/azure-stack-update-1902.md#improvements) des notes de publication Azure Stack.

<!-- ### New features

- For a list of new features in this release, see [this section](../operator/azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../operator/azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../operator/azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../operator/azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

### <a name="known-issues"></a>Problèmes connus

- Il existe un problème pour établir une connexion VPN depuis un autre hôte vers le kit ASDK, avec la procédure décrite dans [cet article](asdk-connect.md). Lorsque vous tentez de vous connecter, de votre client VPN à l’environnement ASDK, vous voyez une erreur indiquant que **le nom d’utilisateur ou le mot de passe est incorrect**, même si vous êtes sûr d’avoir utilisé le bon compte et d’avoir correctement tapé le mot de passe. Le problème ne vient pas de vos informations d’identification, mais plutôt d’une modification apportée au protocole d’authentification utilisé pour la connexion VPN sur le kit ASDK. Pour contourner ce problème, effectuez les étapes suivantes :

   Tout d’abord, modifiez le protocole d’authentification utilisé côté serveur ASDK :

   1. Protocole RDP (Remote Desktop Protocol) vers l’hôte ASDK.
   2. Ouvrez une session PowerShell avec élévation de privilèges, connectez-vous en tant que AzureStack\AzureStackAdmin, avec le mot de passe que vous avez fourni au moment du déploiement.
   3. Exécutez les commandes suivantes :

      ```powershell
      netsh nps set np name = "Connections to Microsoft Routing and Remote Access server" profileid = "0x100a" profiledata = "1A000000000000000000000000000000" profileid = "0x1009" profiledata = "0x5"
      restart-service remoteaccess -force
      ```

   Ensuite, modifiez le script de connexion côté client. Pour ce faire, le plus simple consiste à apporter des modifications directement au module de script C:\AzureStack-Tools-master\connect\azurestack.connect.psm1 :

   1. Modifiez l’applet de commande **Add-AzsVpnConnection** pour remplacer la valeur `MsChapv2` du paramètre `AuthenticationMethod` par `EAP` :

      ```powershell
      $connection = Add-VpnConnection -Name $ConnectionName -ServerAddress $ServerAddress -TunnelType L2tp -EncryptionLevel Required -AuthenticationMethod Eap -L2tpPsk $PlainPassword -Force -RememberCredential -PassThru -SplitTunneling
      ```

   2. Modifiez l’applet de commande **Connect-AzsVpn** en remplaçant l’utilisation de `rasdial @ConnectionName $User $PlainPassword` par celle de `rasphone`, car EAP nécessite une connexion interactive :

      ```powershell
      rasphone $ConnectionName
      ```

   3. Enregistrez vos modifications, puis réimportez le module **azurestack.connect.psm1**.
   4. Suivez les instructions de [cet article](asdk-connect.md#set-up-vpn-connectivity).
   5. Lorsque vous vous connectez au kit ASDK via une connexion VPN, faites-le en passant par Paramètres Windows **Réseau et Internet**, puis **VPN**, plutôt qu’à partir de la barre des tâches, pour garantir la demande des informations d’identification.

- Un problème a été identifié dans lequel les paquets supérieure à 1 450 octets vers un équilibreur de charge interne (ILB) sont ignorés. Le problème est dû au fait que le paramètre MTU sur l’hôte est trop faible pour prendre en charge des paquets VXLAN encapsulés qui passent par le rôle, ce qui, à compter de la version 1901, a été déplacé vers l’hôte. Il existe au moins deux scénarios où ce problème peut se manifester :

  - Requêtes SQL pour SQL Always On qui est derrière un équilibreur de charge interne (ILB) et faisant plus 660 octets.
  - Les déploiements Kubernetes échouent si vous essayez d’activer plusieurs maîtres.  

  Le problème se produit lorsque vous avez une communication entre une machine virtuelle et un équilibreur de charge interne sur le même réseau virtuel, mais sur des sous-réseaux différents. Vous pouvez contourner ce problème en exécutant les commandes suivantes dans une invite de commandes avec élévation de privilèges sur l’ordinateur hôte ASDK :

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```

## <a name="build-11901095"></a>Version 1.1901.0.95

Consultez les [informations importantes sur la build dans les notes de publication Azure Stack](../operator/azure-stack-update-1901.md#build-reference).

### <a name="changes"></a>Changements

Cette version inclut les améliorations suivantes pour Azure Stack :

- Les composants BGP et NAT sont maintenant déployés sur l'hôte physique. Il n'est donc plus nécessaire d'avoir deux adresses IP publiques ou d'entreprise pour pouvoir déployer le kit ASDK, ce qui simplifie également le déploiement.
- Les sauvegardes des systèmes intégrés Azure Stack peuvent maintenant [être validées](asdk-validate-backup.md) à l’aide du script PowerShell **asdk-installer.ps1**.

### <a name="new-features"></a>Nouvelles fonctionnalités

- Pour obtenir la liste des nouvelles fonctionnalités dans cette version, consultez [cette section](../operator/azure-stack-update-1901.md#new-features) des notes de publication Azure Stack.

### <a name="fixed-and-known-issues"></a>Problèmes connus et résolus

- Pour obtenir la liste des problèmes corrigés dans cette version, consultez [cette section](../operator/azure-stack-update-1901.md#fixed-issues) des notes de publication Azure Stack. Pour obtenir la liste des problèmes connus, consultez [cette section](../operator/azure-stack-update-1901.md#known-issues-post-installation).
- Notez que les [correctifs logiciels d’Azure Stack](../operator/azure-stack-update-1901.md#azure-stack-hotfixes) ne sont pas applicables au kit ASDK Azure Stack.
