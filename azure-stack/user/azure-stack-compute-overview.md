---
title: Présentation des machines virtuelles Azure Stack Hub
description: Apprenez-en davantage sur les machines virtuelles Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 01/05/2020
ms.openlocfilehash: 576580732440cabd8ae1c140d13130b81b212d16
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79295574"
---
# <a name="introduction-to-azure-stack-hub-vms"></a>Présentation des machines virtuelles Azure Stack Hub

Azure Stack Hub propose des machines virtuelles en tant que type de ressource informatique évolutive à la demande. Une machine virtuelle est mieux adaptée à vos besoins si vous devez surtout améliorer le contrôle de votre environnement informatique. Cet article fournit des informations préalables à la création de votre première machine virtuelle.

Une machine virtuelle Azure Stack Hub vous offre la souplesse de la virtualisation, sans devoir gérer des clusters ou des machines individuelles. Vous devez cependant toujours assurer la maintenance de la machine virtuelle en effectuant des tâches comme la configuration, la mise à jour corrective/mise à jour et l’installation des logiciels qui s’exécutent sur celle-ci.

Vous pouvez utiliser des machines virtuelles Azure Stack Hub de différentes manières. Par exemple :

- **Développement et tests** : Les machines virtuelles Azure Stack Hub vous permettent de créer un ordinateur doté d'une configuration spécifique pour coder et tester une application.

- **Applications dans le cloud** : La demande de votre application étant susceptible de fluctuer, il peut être économique de l’exécuter sur une machine virtuelle dans Azure Stack Hub. Vous payez pour des machines virtuelles supplémentaires lorsque vous en avez besoin et vous les arrêtez le reste du temps.

- **Centre de données étendu** : Au sein d’un réseau virtuel Azure Stack Hub, les machines virtuelles peuvent se connecter au réseau de votre organisation ou à Azure.

Les machines virtuelles utilisées par votre application peuvent effectuer un scale-up ou un scale-out pour répondre à vos besoins.

## <a name="before-creating-a-vm"></a>Avant de créer une machine virtuelle

Différentes considérations liées à la conception sont à prendre en compte lorsque vous créez une infrastructure d’application dans Azure Stack Hub. Avant de commencer à créer votre infrastructure, il est important de réfléchir aux aspects suivants concernant les machines virtuelles :

- Les noms des ressources de votre application.
- La taille de la machine virtuelle.
- Le nombre maximal de machines virtuelles qui peuvent être créées.
- Le système d’exploitation exécuté par la machine virtuelle.
- La configuration de la machine virtuelle après son démarrage.
- Les ressources liées nécessaires à la machine virtuelle.

### <a name="naming"></a>Dénomination

Une machine virtuelle a un nom qui lui est assigné et a un nom d’ordinateur configuré dans le cadre du système d’exploitation. Le nom d’une machine virtuelle peut comprendre jusqu’à 15 caractères.

Si vous utilisez Azure Stack Hub pour créer le disque du système d’exploitation, le nom de l’ordinateur et celui de la machine virtuelle sont identiques. Si vous chargez et utilisez votre propre image qui contient un système d’exploitation précédemment configuré et que vous l’utilisez pour créer une machine virtuelle, les noms peuvent être différents. Nous vous recommandons de faire en sorte que le nom d’ordinateur dans le système d’exploitation corresponde à celui de la machine virtuelle quand vous chargez votre propre fichier image.

### <a name="vm-size"></a>Taille de la machine virtuelle

La taille de la machine virtuelle que vous utilisez est déterminée par la charge de travail que vous souhaitez exécuter. La taille que vous choisissez détermine ensuite des facteurs comme la puissance de traitement, la mémoire et la capacité de stockage. Azure Stack Hub propose différentes tailles vous permettant de prendre en charge de nombreux types d’utilisation.

### <a name="vm-limits"></a>Limites des machines virtuelles

Votre abonnement comporte des limites de quota par défaut qui peuvent avoir un impact négatif sur le déploiement des machines virtuelles pour votre projet. La limite est de 20 machines virtuelles par région et par abonnement.

### <a name="operating-system-disks-and-images"></a>Images et disques du système d’exploitation

Dans Azure Stack Hub, les machines virtuelles sont limitées au format de disque dur virtuel (VHD/VHDX) de 1ère génération. Les disques durs virtuels peuvent être utilisés pour stocker le système d’exploitation et les données de l’ordinateur. Les disques durs virtuels sont également utilisés pour les images à partir desquelles vous choisissez d’installer un système d’exploitation. Azure Stack Hub fournit une place de marché à utiliser avec différents types et versions de systèmes d’exploitation. Les images de la Place de marché sont identifiées par l’éditeur de l’image, l’offre, la référence SKU et la version (la version la plus récente est généralement spécifiée comme **dernière**).

Le tableau suivant montre comment trouver ces informations pour une image :

|Méthode|Description|
|---------|---------|
|Portail Azure Stack Hub|Les valeurs sont spécifiées automatiquement pour vous lorsque vous sélectionnez une image à utiliser.|
|Azure Stack Hub PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|API REST     |[Lister les éditeurs d’images](/rest/api/compute/platformimages/platformimages-list-publishers)<br>[Lister les offres d’images](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Lister les références d’images](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

Vous pouvez choisir de télécharger et d’utiliser votre propre image. Dans ce cas, le nom de l’éditeur, l’offre et la référence SKU ne sont pas utilisés.

### <a name="extensions"></a>Extensions

Les extensions de machines virtuelles étendent les fonctionnalités de votre machine virtuelle par le biais de la configuration post-déploiement et de tâches automatisées.
Ces tâches courantes peuvent être accomplies à l’aide des extensions :

- **Scripts personnalisés** : L’extension de script personnalisé vous permet de configurer des charges de travail sur la machine virtuelle en exécutant votre script pendant le provisionnement de la machine virtuelle.

- **Déploiement et gestion des configurations** : L’extension de configuration d’état souhaité (DSC) PowerShell vous permet de configurer DSC sur une machine virtuelle pour gérer les environnements et les configurations.

- **Collecte des données de diagnostic** : L’extension Diagnostics Azure vous permet de configurer la machine virtuelle de sorte qu’elle collecte des données de diagnostics utilisées pour superviser l’intégrité de votre application.

### <a name="related-resources"></a>Ressources associées

Les ressources figurant dans le tableau suivant sont utilisées par la machine virtuelle et doivent exister ou être créées lors de sa création :

|Ressource|Obligatoire|Description|
|---------|---------|---------|
|Resource group|Oui|La machine virtuelle doit être contenue dans un groupe de ressources.|
|Compte de stockage|Non|La machine virtuelle ne nécessite pas que le compte de stockage stocke ses disques durs virtuels en cas d’utilisation de disques managés. <br>La machine virtuelle exige que le compte de stockage stocke ses disques durs virtuels en cas d’utilisation de disques non managés.|
|Réseau virtuel|Oui|La machine virtuelle doit faire partie d’un réseau virtuel.|
|Adresse IP publique|Non|La machine virtuelle peut avoir une adresse IP publique pour être accessible à distance.|
|interface réseau|Oui|La machine virtuelle a besoin de l’interface réseau pour communiquer sur le réseau.|
|Disques de données|Non|La machine virtuelle peut comprendre des disques de données pour développer ses capacités de stockage.|

## <a name="create-your-first-vm"></a>Créer votre première machine virtuelle

Vous avez plusieurs possibilités pour créer une machine virtuelle. Votre choix dépend de votre environnement. Le tableau suivant fournit des informations pour vous aider à créer votre machine virtuelle :

|Méthode|Article|
|---------|---------|
|Portail Azure Stack Hub|Créer une machine virtuelle Windows avec le portail Azure Stack Hub<br>[Créer une machine virtuelle Linux à l’aide du portail Azure Stack Hub](azure-stack-quick-linux-portal.md)|
|Modèles|Les modèles de démarrage rapide Azure Stack Hub sont disponibles à l’adresse suivante :<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://aka.ms/aa6z60s)|
|PowerShell|[Créer une machine virtuelle Windows à l’aide de PowerShell dans Azure Stack Hub](azure-stack-quick-create-vm-windows-powershell.md)<br>[Créer une machine virtuelle Linux à l’aide de PowerShell dans Azure Stack Hub](azure-stack-quick-create-vm-linux-powershell.md)|
|Interface de ligne de commande|[Créer une machine virtuelle Windows à l’aide de CLI dans Azure Stack Hub](azure-stack-quick-create-vm-windows-cli.md)<br>[Créer une machine virtuelle Linux à l’aide de CLI dans Azure Stack Hub](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>Gérer votre machine virtuelle

Vous pouvez gérer les machines virtuelles via un portail basé sur un navigateur, via des outils en ligne de commande avec prise en charge des scripts ou directement via des API. Voici quelques tâches de gestion classiques que vous pouvez effectuer :

- Obtenir des informations sur une machine virtuelle
- Se connecter à une machine virtuelle
- Gérer la disponibilité
- Faire des sauvegardes

### <a name="get-information-about-your-vm"></a>Obtenir des informations sur votre machine virtuelle

Le tableau suivant indique différents moyens d’obtenir des informations sur une machine virtuelle.

|Méthode|Description|
|---------|---------|
|Portail Azure Stack Hub|Dans le menu Hub, cliquez sur **Machines virtuelles**, puis sélectionnez la machine virtuelle dans la liste. Sur la page de la machine virtuelle, vous pouvez consulter les informations de vue d’ensemble, fixer des valeurs et surveiller les métriques.|
|Azure PowerShell|La gestion des machines virtuelles est similaire dans Azure et dans Azure Stack Hub. Pour plus d’informations sur l’utilisation de PowerShell, consultez l’article suivant relatif à Azure :<br>[Créer et gérer des machines virtuelles Windows avec le module Azure PowerShell](/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|Kits de développement logiciel (SDK) client|L’utilisation de C# pour gérer les machines virtuelles est similaire dans Azure et dans Azure Stack Hub. Pour plus d’informations, consultez l’article suivant relatif à Azure :<br>[Créer et gérer des machines virtuelles Windows dans Azure à l’aide de C#](/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-your-vm"></a>Connexion à votre machine virtuelle

Vous pouvez utiliser le bouton **Se connecter** du portail Azure Stack Hub pour vous connecter à votre machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

- [Considérations relatives aux machines virtuelles dans Azure Stack Hub](azure-stack-vm-considerations.md)
