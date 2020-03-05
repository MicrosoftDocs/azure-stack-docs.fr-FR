---
title: Se connecter au stockage iSCSI avec Azure Stack Hub
description: Découvrez comment vous connecter au stockage iSCSI avec Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 7451338194742723d6b669f94dc11b4449570eda
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703840"
---
# <a name="connect-to-iscsi-storage-with-azure-stack-hub"></a>Se connecter au stockage iSCSI avec Azure Stack Hub

Vous pouvez utiliser le modèle présenté dans cet article pour connecter une machine virtuelle Azure Stack Hub à une cible iSCSI locale, configurer la machine virtuelle pour qu’elle utilise le stockage hébergé en dehors de l’instance Azure Stack Hub et ailleurs dans votre centre de données. Cet article présente l’utilisation d’un ordinateur Windows en tant que cible iSCSI.

Le modèle est disponible dans la branche **lucidqdreams** du dépôt [Azure Intelligent Edge Patterns GitHub](https://github.com/lucidqdreams/azure-intelligent-edge-patterns). Le modèle se trouve dans le dossier **storage-iSCSI**. Le modèle est conçu pour configurer l’infrastructure nécessaire côté Azure Stack Hub afin de se connecter à une cible iSCSI. Il s’agit d’une machine virtuelle qui agit en tant qu’initiateur iSCSI avec le réseau virtuel, le groupe de sécurité réseau, l’adresse IP privée et le stockage associés. Une fois que le modèle a été déployé, deux scripts PowerShell doivent être exécutés pour terminer la configuration. Un script est exécuté sur la machine virtuelle locale (cible) et l’autre sur la machine virtuelle Azure Stack Hub (initiateur). Une fois ces scripts exécutés, un stockage local est ajouté à votre machine virtuelle Azure Stack Hub. 

## <a name="overview"></a>Vue d’ensemble

Le schéma montre une machine virtuelle hébergée sur Azure Stack Hub avec un disque iSCSI monté à partir d’un ordinateur (physique ou virtuel) Windows local, ce qui permet le montage du stockage externe à Azure Stack Hub à l’intérieur de votre machine virtuelle hébergée par Azure Stack Hub via le protocole iSCSI.

![texte de remplacement](./media/azure-stack-network-howto-iscsi-storage/overview.png)

### <a name="requirements"></a>Spécifications

- Un ordinateur local (physique ou virtuel) exécutant Windows Server 2016 Datacenter ou Windows Server 2019 Datacenter.
- Éléments requis de la Place de marché Azure Stack Hub :
    -  Windows Server 2016 Datacenter ou Windows Server 2019 Datacenter (dernière version recommandée).
    -  Extension PowerShell DSC.
    -  Extension de script personnalisé.
    -  Ordinateur virtuel ou physique existant. Dans l’idéal, cet ordinateur a deux cartes réseau. Il peut également s’agir d’une autre cible iSCSI, telle qu’un réseau SAN par exemple.

### <a name="things-to-consider"></a>Points importants à prendre en compte

- Un groupe de sécurité réseau est appliqué au sous-réseau du modèle. Passez ceci en revue et effectuez des octrois supplémentaires en fonction des besoins.
- Une règle de refus RDP est appliquée au groupe de sécurité réseau de tunnel et doit être définie sur Autoriser si vous souhaitez accéder aux machines virtuelles par le biais de l’adresse IP publique.
- Cette solution ne prend pas en compte la résolution DNS.
- Vous devez modifier votre association Chapusername et Chappassword. Le Chappassword doit compter entre 12 à 16 caractères.
- Ce modèle utilise une adresse IP statique pour la machine virtuelle, car la connexion iSCSI utilise l’adresse locale de la configuration.
- Ce modèle utilise une licence BYOL Windows.
- Vous pouvez également connecter des systèmes basés sur Linux aux cibles iSCSI. Des instructions sont disponibles dans l’article sur l’[initiateur iSCSI](https://help.ubuntu.com/lts/serverguide/iscsi-initiator.html) dans la documentation Ubuntu.

### <a name="options"></a>Options

- Vous pouvez utiliser votre propre compte de stockage blob et votre propre jeton SAS à l’aide des paramètres **_artifactsLocation** et **_artifactsLocationSasToken**.
- Ce modèle fournit des valeurs par défaut pour l’affectation de nom au réseau virtuel et l’adressage IP.
- Cette configuration n’a qu’une seule carte réseau iSCSI provenant du client iSCSI. Nous avions testé un certain nombre de configurations pour utiliser des sous-réseaux et des cartes réseau distincts. Toutefois, les problèmes liés à plusieurs passerelles ont été résolus et la tentative de création d’un sous-réseau de stockage distinct pour isoler le trafic est réellement redondant. 
- Veillez à conserver ces valeurs dans les limites de plages d’adresses et de sous-réseaux légales, car le déploiement peut échouer. 
- L’objectif principal des packages PowerShell DSC consiste à rechercher les redémarrages en attente. Ce DSC peut être personnalisé davantage si nécessaire. Pour plus d’informations, consultez [ComputerManagementDsc](https://github.com/PowerShell/ComputerManagementDsc/).

### <a name="resource-group-template-iscsi-client"></a>Modèle de groupe de ressources (client iSCSI)

Le schéma montre les ressources déployées à partir du modèle pour créer le client iSCSI que vous pouvez utiliser pour vous connecter à la cible iSCSI. Ce modèle déploie la machine virtuelle et d’autres ressources. De plus, il exécute prepare-iSCSIClient.ps1 et redémarre la machine virtuelle.

![texte de remplacement](./media/azure-stack-network-howto-iscsi-storage/iscsi-file-server.png)

### <a name="the-deployment-process"></a>Processus de déploiement

Le modèle de groupe de ressources génère une sortie, qui est censée être l’entrée de l’étape suivante. Elle se concentre principalement sur le nom du serveur et l’adresse IP publique Azure Stack Hub d’où provient le trafic iSCSI. Pour cet exemple :

1. Déployez le modèle d’infrastructure.
2. Déployez une machine virtuelle Azure Stack Hub sur une machine virtuelle hébergée ailleurs dans votre centre de données. 
3. Exécutez `Create-iSCSITarget.ps1` à l’aide des sorties d’adresse IP et de nom de serveur du modèle en tant que paramètres d’extraction du script sur la cible iSCSI, qui peut être une machine virtuelle ou un serveur physique.
4. Utilisez l’adresse IP externe ou les adresses du serveur cible iSCSI comme entrées pour exécuter le script `Connect-toiSCSITarget.ps1`. 

![texte de remplacement](./media/azure-stack-network-howto-iscsi-storage/process.png)

### <a name="inputs-for-azuredeployjson"></a>Entrées pour azuredeploy.json

|**Paramètres**|**default**|**description**|
|------------------|---------------|------------------------------|
|WindowsImageSKU         |2019-Datacenter   |Sélectionnez l’image de machine virtuelle Windows de base.
|VMSize                  |Standard_D2_v2    |Entrez la taille de la machine virtuelle.
|VMName                  |FileServer        |nom de la machine virtuelle
|adminUsername           |storageadmin      |Nom de l’administrateur de la nouvelle machine virtuelle.
|adminPassword           |                  |Mot de passe du compte d’administrateur des nouvelles machines virtuelles. La valeur par défaut est l’ID d’abonnement.
|VNetName                |Stockage           |Nom du réseau virtuel. Il sert à étiqueter les ressources.
|VNetAddressSpace        |10.10.0.0/23      |Espace d’adressage pour le réseau virtuel.
|VNetInternalSubnetName  |Interne          |Nom de sous-réseau interne de réseau virtuel.
|VNetInternalSubnetRange |10.10.1.0/24      |Plage d’adresses pour le sous-réseau interne de réseau virtuel.
|InternalVNetIP          |10.10.1.4         |Adresse statique de l’adresse IP interne du serveur de fichiers.
|_artifactsLocation      ||
|_artifactsLocationSasToken||

### <a name="deployment-steps"></a>Étapes du déploiement

1. Déployer une infrastructure cliente iSCSI à l’aide de `azuredeploy.json`
2. Exécutez `Create-iSCSITarget.ps1` sur la cible iSCSI du serveur local. Une fois le modèle terminé, vous devez exécuter Create-iSCSITarget.ps1 sur la cible iSCSI du serveur local avec les sorties de la première étape.
3. Exécutez `Connect-toiSCSITarget.ps1` sur le client iSCSI. Exécutez Connect-toiSCSITarget.ps1 sur le client iSCSI avec les détails de la cible iSCSI.

## <a name="adding-iscsi-storage-to-existing-vms"></a>Ajout d’un stockage iSCSI à des machines virtuelles existantes

Vous pouvez également exécuter les scripts sur une machine virtuelle existante pour vous connecter à partir du client iSCSI à une cible iSCSI. Ce flux est adapté si vous créez vous-même la cible iSCSI. Ce schéma illustre le déroulement de l’exécution des scripts PowerShell. Ces scripts se trouvent dans le répertoire Script :

![texte de remplacement](./media/azure-stack-network-howto-iscsi-storage/script-flow.png)

### <a name="prepare-iscsiclientps1"></a>Prepare-iSCSIClient.ps1

Le script `Prepare-iSCSIClient.ps1` installe les composants requis sur le client iSCSI, notamment :
- installation des services Multipath-IO
- configuration du démarrage du service initiateur iSCSI sur Automatique
- activation de la prise en charge de MPIO (Multipath MPIO) vers iSCSI
- activer la revendication automatique de tous les volumes iSCSI
- définir le délai d’expiration du disque sur 60 secondes

Il est important de redémarrer le système après l’installation de ces composants requis. La stratégie d’équilibrage de charge MPIO nécessite un redémarrage pour pouvoir être définie.

### <a name="create-iscsitargetps1"></a>Create-iSCSITarget.ps1

Le script `Create-iSCSITarget.ps1 ` doit être exécuté sur le système, qui sert le stockage. Vous pouvez créer plusieurs disques et cibles restreints par les initiateurs. Vous pouvez exécuter ce script plusieurs fois pour créer plusieurs disques virtuels que vous pouvez attacher à différentes cibles. Vous pouvez connecter plusieurs disques à une même cible. 

|**Input**|**default**|**description**|
|------------------|---------------|------------------------------|
|RemoteServer         |FileServer               |Nom du serveur qui se connecte à la cible iSCSI.
|RemoteServerIPs      |1.1.1.1                  |Adresse IP d’où vient le trafic iSCSI.
|DiskFolder           |C:\iSCSIVirtualDisks     |Dossier et lecteur où sont stockés les disques virtuels.
|DiskName             |DiskName                 |Nom du fichier VHDX du disque.
|DiskSize             |5 Go                      |Taille du disque VHDX
|TargetName           |RemoteTarget01           |Nom de cible utilisé pour définir la configuration cible pour le client iSCSI. 
|ChapUsername         |username                 |Nom d’utilisateur pour l’authentification Chap
|ChapPassword         |userP@ssw0rd!            |Mot de passe pour l’authentification Chap. Sa longueur doit être comprise entre 12 et 16 caractères.

### <a name="connect-toiscsitargetps1"></a>Connect-toiSCSITarget.ps1

`Connect-toiSCSITarget.ps1` est le dernier script, qui est exécuté sur le client iSCSI et monte le disque présenté par la cible iSCSI sur le client iSCSI.

|**Input**|**default**|**description**|
|------------------|---------------|------------------------------|
|TargetiSCSIAddresses   |"2.2.2.2","2.2.2.3"    |Adresses IP de la cible iSCSI.
|LocalIPAddresses       |"10.10.1.4"            |Adresse IP interne d’où vient le trafic iSCSI.
|LoadBalancePolicy      |C:\iSCSIVirtualDisks   |Adresse IP d’où vient le trafic iSCSI.
|ChapUsername           |username               |Nom d’utilisateur pour l’authentification Chap
|ChapPassword           |userP@ssw0rd!          |Mot de passe pour l’authentification Chap. Sa longueur doit être comprise entre 12 et 16 caractères.

## <a name="next-steps"></a>Étapes suivantes

[Différences et considérations relatives aux réseaux Azure Stack Hub](azure-stack-network-differences.md)  