---
title: Déployer une infrastructure SDN avec SDN Express
description: Découvrez comment déployer une infrastructure SDN à l’aide de SDN Express
author: v-dasis
ms.topic: how-to
ms.date: 02/17/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e367602252207a673316caf3482d7805bff02ba8
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100647808"
---
# <a name="deploy-an-sdn-infrastructure-using-sdn-express"></a>Déployer une infrastructure SDN avec SDN Express

> S’applique à Azure Stack HCI, version 20H2

Dans cette rubrique, vous déployez une infrastructure SDN (Software Defined Network) de bout en bout à l’aide de scripts PowerShell pour SDN Express. L’infrastructure peut comprendre trois composants à haut niveau de disponibilité, à savoir : un contrôleur de réseau, un équilibreur de charge logiciel et une passerelle.  Les scripts prennent en charge un déploiement par phases dans le cadre duquel vous pouvez déployer uniquement le composant contrôleur de réseau afin d’obtenir un ensemble principal de fonctionnalités avec une configuration réseau minimale.

Vous pouvez aussi déployer une infrastructure SDN à l’aide de VMM (System Center Virtual Machine Manager). Pour plus d’informations, consultez [Gérer les ressources SDN dans la structure VMM](/system-center/vmm/network-sdn).

## <a name="before-you-begin"></a>Avant de commencer

Avant de commencer un déploiement SDN, planifiez et configurez votre infrastructure réseau physique et hôte. Consultez les articles suivants :

- [Configuration requise des réseaux virtuels](../concepts/physical-network-requirements.md)
- [Configuration requise des réseaux hôtes](../concepts/host-network-requirements.md)
- [Créer un cluster à l’aide de Windows Admin Center](../deploy/create-cluster.md)
- [Créer un cluster à l’aide de Windows PowerShell](../deploy/create-cluster-powershell.md)
- [Planifier une infrastructure de réseau défini par logiciel](../concepts/plan-software-defined-networking-infrastructure.md)

Vous n’avez pas besoin de déployer tous les composants SDN. Consultez la section [Déploiement échelonné](../concepts/plan-software-defined-networking-infrastructure.md#phased-deployment) dans [Planifier une infrastructure de réseau défini par logiciel](../concepts/plan-software-defined-networking-infrastructure.md) pour déterminer les composants d’infrastructure dont vous avez besoin, puis exécutez les scripts appropriés.

Assurez-vous que le système d’exploitation Azure Stack HCI est installé sur tous les serveurs hôtes. Pour cela, consultez [Déployer le système d’exploitation Azure Stack HCI](../deploy/operating-system.md).

## <a name="requirements"></a>Configuration requise

Pour un déploiement SDN, les conditions suivantes doivent être remplies :

- Hyper-V doit être activé sur tous les serveurs hôtes
- Tous les serveurs hôtes doivent être joints à Active Directory
- Un commutateur virtuel doit être créé
- Le réseau physique doit être configuré pour les sous-réseaux et les réseaux VLAN qui sont définis dans le fichier de configuration.
- Le fichier VHDX spécifié dans le fichier de configuration doit être accessible à partir de l’ordinateur de déploiement sur lequel est exécuté le script SDN Express.

## <a name="create-the-vdx-file"></a>Créer le fichier VDX

SDN utilise un fichier VHDX contenant le système d’exploitation Azure Stack HCI, en tant que source pour la création des machines virtuelles SDN. La version du système d’exploitation dans votre fichier VHDX doit correspondre à celle qu’utilisent les hôtes Hyper-V Azure Stack HCI. Ce fichier VHDX est utilisé par tous les composants de l’infrastructure SDN.

Si vous avez téléchargé et installé le système d’exploitation Azure Stack HCI à partir d’une image ISO, vous pouvez créer le fichier VHDX à l’aide de l’utilitaire [Convert-WindowsImage ](https://www.powershellgallery.com/packages/Convert-WindowsImage/10.0).

L’exemple suivant utilise `Convert-WindowsImage` :

 ```powershell
$wimpath = "d:\sources\install.wim"
$vhdpath = "c:\temp\WindowsServerDatacenter.vhdx"
$Edition = 4   # 4 = Full Desktop, 3 = Server Core

import-module ".\convert-windowsimage.ps1"

Convert-WindowsImage -SourcePath $wimpath -Edition $Edition -VHDPath $vhdpath -SizeBytes 500GB -DiskLayout UEFI
```

## <a name="download-the-github-repository"></a>Télécharger le dépôt GitHub

Les fichiers de script SDN Express se trouvent dans GitHub. La première étape consiste à obtenir les fichiers et dossiers nécessaires sur votre ordinateur de déploiement.

1. Accédez au dépôt [GitHub SDN Microsoft](https://github.com/microsoft/SDN).

1. Dans le dépôt, développez la liste déroulante **Code**, puis choisissez **Cloner** ou **Télécharger le fichier ZIP** pour télécharger les fichiers SDN sur votre ordinateur de déploiement désigné.

    > [!NOTE]
    > L’ordinateur de déploiement désigné doit exécuter Windows Server 2016 ou une version ultérieure.

1. Extrayez le fichier ZIP et copiez le dossier `SDNExpress` dans le dossier `C:\` sur l’ordinateur de déploiement.

## <a name="edit-the-configuration-file"></a>Modifier le fichier de configuration

Le fichier de données de configuration `MultiNodeSampleConfig.psd1` de PowerShell contient tous les paramètres dont a besoin le script SDN Express comme entrée pour les divers paramètres de configuration. Ce fichier fournit des informations spécifiques sur les valeurs à renseigner selon que vous déployez uniquement le composant contrôleur de réseau, ou également les composants équilibreur de charge logiciel et passerelle. Pour plus d’informations, consultez [Planifier une infrastructure de réseau défini par logiciel](../concepts/plan-software-defined-networking-infrastructure.md).

Accédez au dossier `C:\SDNExpress\scripts`, puis ouvrez le fichier `MultiNodeSampleConfig.psd1` dans votre éditeur de texte de prédilection. Modifiez certaines valeurs de paramètres pour les adapter à votre infrastructure et à votre déploiement :

### <a name="general-settings-and-parameters"></a>Réglages et paramètres généraux

Les réglages et paramètres sont utilisés par SDN en général pour tous les déploiements :

- **VHDPath** : chemin d’accès de fichier VHD qu’utilisent toutes les machines virtuelles de l’infrastructure SDN (contrôleur de réseau, équilibreur de charge logiciel, passerelle).
- **VHDFile** : nom de fichier VHD qu’utilisent toutes les machines virtuelles de l’infrastructure SDN.
- **VMLocation** : chemin d’accès aux machines virtuelles de l’infrastructure SDN.
- **JoinDomain** : domaine auquel sont jointes les machines virtuelles de l’infrastructure SDN.
- **SDNMacPoolStart** : adresse de début du pool d’adresses MAC pour les machines virtuelles de charge de travail cliente.
- **SDNMacPoolEnd** : adresse de fin du pool d’adresses MAC pour les machines virtuelles de charge de travail cliente.
- **ManagementSubnet** : sous-réseau de réseau de gestion que le contrôleur de réseau utilise pour gérer les hôtes Hyper-V, ainsi que les composants équilibreur de charge logiciel et passerelle.
- **ManagementGateway** : adresse de passerelle pour le réseau de gestion.
- **ManagementDNS** : serveur DNS pour le réseau de gestion.
- **ManagementVLANID** : ID de VLAN pour le réseau de gestion.
- **DomainJoinUsername** : nom d’utilisateur de l’administrateur.
- **LocalAdminDomainUser** : mot de passe de l’administrateur.
- **RestName** : nom DNS que les clients de gestion (par exemple Windows Admin Center) utilisent pour communiquer avec le contrôleur de réseau.
- **HyperVHosts** : serveurs hôtes que doit gérer le contrôleur de réseau.
- **NCUsername** : nom d’utilisateur du compte du contrôleur de réseau.
- **ProductKey** : clé de produit pour les machines virtuelles de l’infrastructure SDN.
- **SwitchName** : nom requis uniquement si plusieurs commutateurs virtuels existent sur les hôtes Hyper-V.
- **VMMemory** : mémoire (en Go) attribuée aux machines virtuelles de l’infrastructure. Par défaut, 4 Go.
- **VMProcessorCount** : nombre de processeurs attribués aux machines virtuelles de l’infrastructure. Par défaut, 8.
- **Locale** : paramètres régionaux. S’ils ne sont pas spécifiés, ceux de l’ordinateur de déploiement sont utilisés.
- **TimeZone** : fuseau horaire. S’il n’est pas spécifié, le fuseau horaire local de l’ordinateur de déploiement est utilisé.

Vous pouvez éventuellement inclure des mots de passe si ceux-ci sont stockés chiffrés en tant que chaînes sécurisées encodées en texte.  Les mots de passe sont utilisés uniquement si les scripts SDN Express sont exécutés sur l’ordinateur sur lequel les mots de passe ont été chiffrés. Autrement, vous êtes invité à les entrer :

- **DomainJoinSecurePassword** : pour un compte de domaine.
- **LocalAdminSecurePassword** : pour un compte d’administrateur local.
- **NCSecurePassword** : pour un compte de contrôleur de réseau.

> [!NOTE]
> Le script SDN Express ne prend pas en charge l’adressage DHCP. Vérifiez que les adresses MAC de toutes les machines virtuelles de l’infrastructure SDN se trouvent en dehors de la plage de paramètres `SDNMACPool`.

### <a name="network-controller-vm-section"></a>Section machine virtuelle contrôleur de réseau

La section `NCs = @()` est utilisée pour les machines virtuelles contrôleurs de réseau. Assurez-vous que l’adresse MAC de chaque machine virtuelle contrôleur de réseau est en dehors de la plage `SDNMACPool` répertoriée dans les paramètres généraux :

- **ComputerName** : nom de machine virtuelle contrôleur de réseau.
- **Hostname** : nom d’hôte du serveur sur lequel se trouve la machine virtuelle contrôleur de réseau.
- **ManagementIP** : adresse IP du réseau de gestion pour la machine virtuelle contrôleur de réseau.
- **MacAddress** : adresse MAC pour la machine virtuelle contrôleur de réseau.

### <a name="software-load-balancer-vm-section"></a>Section machine virtuelle équilibreur de charge logiciel

La section `Muxes = @()` est utilisée pour les machines virtuelles équilibreurs de charge logiciel. Assurez-vous que l’adresse MAC de chaque machine virtuelle équilibreur de charge logiciel est en dehors de la plage `SDNMACPool` répertoriée dans les paramètres généraux : Laissez cette section vide (`Muxes = @()`) si vous ne déployez pas le composant équilibreur de charge logiciel :

- **ComputerName** : nom de la machine virtuelle équilibreur de charge logiciel.
- **Hostname** : nom d’hôte du serveur sur lequel se trouve la machine virtuelle équilibreur de charge logiciel.
- **ManagementIP** : adresse IP du réseau de gestion pour la machine virtuelle équilibreur de charge logiciel.
- **MacAddress** : adresse MAC pour la machine virtuelle équilibreur de charge logiciel.
- **PAIPAddress** : adresse IP réseau du fournisseur (PA) pour la machine virtuelle équilibreur de charge logiciel.
- **PAMACAddress** : adresse IP réseau du fournisseur (PA) pour la machine virtuelle équilibreur de charge logiciel.

### <a name="gateway-vm-section"></a>Section machine virtuelle passerelle

La section `Gateways = @()` est utilisée pour les machines virtuelles passerelles. Assurez-vous que l’adresse MAC de chaque machine virtuelle passerelle est en dehors de la plage `SDNMACPool` répertoriée dans les paramètres généraux : Laissez cette section vide (`Gateways = @()`) si vous ne déployez pas le composant passerelle :

- **ComputerName** : nom de la machine virtuelle passerelle.
- **Hostname** : nom d’hôte du serveur sur lequel se trouve la machine virtuelle passerelle.
- **ManagementIP** : adresse IP du réseau de gestion pour la machine virtuelle passerelle.
- **MacAddress** : adresse MAC pour la machine virtuelle passerelle.
- **FrontEndIp** : adresse IP frontale du réseau de fournisseur pour la machine virtuelle passerelle.
- **FrontEndMac** : adresse MAC frontale du réseau de fournisseur pour la machine virtuelle passerelle.
- **BackEndMac** : adresse MAC dorsale du réseau de fournisseur pour la machine virtuelle passerelle.

### <a name="additional-settings-for-slb-and-gateway"></a>Paramètres supplémentaires pour l’équilibreur de charge logiciel et la passerelle

Les paramètres supplémentaires suivants sont utilisés par les machines virtuelles équilibreur de charge logiciel et passerelle. Laissez ces valeurs vides si vous ne déployez pas de machines virtuelles équilibreur de charge logiciel ou passerelle :

- **SDNASN** : numéro de système autonome (ASN) que SDN utilise pour appairer avec des commutateurs réseau.
- **RouterASN** : numéro de système autonome du routeur de passerelle.
- **RouterIPAddress** : adresse IP du routeur de passerelle.
- **PrivateVIPSubnet** : adresse IP virtuelle (VIP) pour le sous-réseau privé.
- **PublicVIPSubnet** : adresse IP virtuelle pour le sous-réseau public.

Les paramètres supplémentaires suivants sont utilisés uniquement par les machines virtuelles passerelles. Laissez ces valeurs vides si vous ne déployez pas de machines virtuelles passerelles :

- **Poolname** : nom du pool qu’utilisent toutes les machines virtuelles passerelles.
- **GRESubnet** : sous-réseau d’adresse IP virtuelle pour GRE (en cas d’utilisation de connexions GRE).
- **Capacity** : capacité en Kbits/s pour chaque machine virtuelle passerelle dans le pool.

### <a name="settings-for-tenant-overlay-networks"></a>Paramètres pour les réseaux de superposition de locataire.

Les paramètres suivants sont utilisés si vous déployez et gérez des réseaux virtualisés de superposition pour les locataires. Si vous utilisez un contrôleur de réseau pour gérer des réseaux VLAN traditionnels, vous pouvez laisser ces valeurs vides.

- **PASubnet** : sous-réseau pour le réseau d’adresse de fournisseur (PA).
- **PAVLANID** : ID de VLAN pour le réseau PA.
- **PAGateway** : adresse IP pour la passerelle réseau PA.
- **PAPoolStart** : adresse IP de début pour le pool réseau PA.
- **PAPoolEnd** : adresse IP de fin pour le pool réseau PA.

## <a name="run-the-deployment-script"></a>Exécuter le script de déploiement

Le script SDN Express déploie votre infrastructure SDN spécifiée. Une fois l’exécution du script terminée, votre infrastructure SDN est prête à être utilisée pour des déploiements de charge de travail de machine virtuelle.

1. Pour obtenir des informations de dernière minute sur la façon d’exécuter le script de déploiement, consultez le fichier `README.md`.  

1. Exécutez la commande suivante à partir d’un compte d’utilisateur avec des informations d’identification d’administration pour les serveurs hôtes du cluster :

    ```powershell
    SDNExpress\scripts\SDNExpress.ps1 -ConfigurationDataFile MultiNodeSampleConfig.psd1 -Verbose
    ```

1. Une fois les machines virtuelles contrôleurs de réseau créées, configurez les mises à jour DNS dynamiques pour le nom du cluster du contrôleur de réseau sur le serveur DNS. Pour plus d’informations, consultez [Comment configurer les mises à jour dynamiques DNS](https://docs.microsoft.com/troubleshoot/windows-server/networking/configure-dns-dynamic-updates-windows-server-2003).

## <a name="next-steps"></a>Étapes suivantes

Gérez vos machines virtuelles. Pour plus d’informations, consultez [Gérer des machines virtuelles](../manage/vm.md).
