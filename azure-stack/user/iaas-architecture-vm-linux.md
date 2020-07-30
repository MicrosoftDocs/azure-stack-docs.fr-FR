---
title: Exécuter une machine virtuelle Linux dans Azure Stack Hub
description: Découvrez comment exécuter une machine virtuelle Linux sur Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 970a128933e8bb65320e8d3d5a0657fac4bc208b
ms.sourcegitcommit: ad6bbb611ac671b295568d3f00a193b783470c68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87397530"
---
# <a name="run-a-linux-virtual-machine-on-azure-stack-hub"></a>Exécuter une machine virtuelle Linux dans Azure Stack Hub

Le provisionnement d’une machine virtuelle dans Azure Stack Hub, comme Azure, nécessite des composants supplémentaires en plus de la machine virtuelle elle-même, notamment des ressources réseau et de stockage. Cet article décrit les bonnes pratiques pour l’exécution d’une machine virtuelle Linux sur Azure Stack Hub.

![Architecture pour une machine virtuelle Linux sur Azure Stack Hub](./media/iaas-architecture-vm-linux/image1.png)

## <a name="resource-group"></a>Resource group

Un [groupe de ressources](/azure/azure-resource-manager/resource-group-overview) est un conteneur logique hébergeant des ressources Azure Stack Hub associées. En règle générale, regroupez les ressources en fonction de leur durée de vie et de qui va les gérer.

Placez les ressources étroitement associées qui partagent le même cycle de vie dans un même [groupe de ressources](/azure/azure-resource-manager/resource-group-overview). Les groupes de ressources vous permettent de déployer et de surveiller les ressources en tant que groupe et de suivre les coûts de facturation par groupe de ressources. Vous pouvez également supprimer des ressources comme un tout, ce qui est pratique pour les déploiements de test. Affectez des noms de ressource explicites pour simplifier la recherche d’une ressource spécifique et comprendre son rôle. Pour plus d’informations, consultez [Conventions d’affectation de noms recommandées pour les ressources Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="virtual-machine"></a>Machine virtuelle

Vous pouvez approvisionner une machine virtuelle à partir d’une liste d’images publiées, d’une image managée personnalisée ou d’un fichier de disque dur virtuel (VHD) chargés dans le stockage d’objets blob Azure Stack Hub. Azure Stack Hub prend en charge l’exécution de plusieurs distributions Linux populaires, notamment CentOS, Debian, Red Hat Enterprise, Ubuntu et SUSE. Pour plus d’informations, consultez [Linux sur Azure Stack Hub](../operator/azure-stack-linux.md). Vous pouvez également choisir de syndiquer l’une des images Linux publiées disponibles sur la Place de marché Azure Stack Hub.

Azure Stack Hub propose différentes tailles de machines virtuelles Azure. Pour plus d’informations, voir [Tailles des machines virtuelles dans Azure Stack Hub](./azure-stack-vm-sizes.md). Si vous déplacez une charge de travail vers Azure Stack Hub, commencez par choisir la taille de machine virtuelle correspondant le mieux à vos serveurs locaux/Azure. Mesurez ensuite les performances de votre charge de travail réelle en termes de processeur, de mémoire et d’opérations d’entrée/sortie par seconde du disque, puis ajustez la taille selon vos besoins.

## <a name="disks"></a>Disques

Le coût est basé sur la capacité du disque approvisionné. Le nombre d’E/S par seconde et le débit (c’est-à-dire le taux de transfert des données) dépendent de la taille de la machine virtuelle. Lorsque vous provisionnez un disque, vous devez donc tenir compte des trois facteurs : capacité, E/S par seconde et débit.

Sur Azure Stack Hub, les IOPS (opérations d’entrée/sortie par seconde) du disque dépendent de la [taille de la machine virtuelle](./azure-stack-vm-sizes.md) plutôt que du type de disque. Cela signifie que, pour une machine virtuelle Standard_Fs, quel que soit le type de disque choisi (SSD ou HDD), la limite d'IOPS est de 2 300 par disque de données supplémentaire. La limite d’IOPS imposée est un plafond (maximum possible) pour éviter les voisins bruyants. Elle ne garantit pas les IOPS obtenues avec une taille de machine virtuelle spécifique.

Nous vous recommandons également d’utiliser la fonctionnalité [Disques managés](./azure-stack-managed-disk-considerations.md). Les disques managés simplifient la gestion des disques en gérant le stockage pour vous. Les disques managés ne nécessitent pas de compte de stockage. Il vous suffit de spécifier leur taille et leur type, puis de les déployer en tant que ressource hautement disponible.

Le disque de système d’exploitation est un disque dur virtuel stocké dans [Stockage Azure Stack Hub](./azure-stack-storage-overview.md), donc il persiste même lorsque l’ordinateur hôte est arrêté. Pour les machines virtuelles Linux, le disque du système d’exploitation est /dev/sda1. Nous vous recommandons également de créer un ou plusieurs [disques de données](./azure-stack-manage-vm-disks.md), qui sont des disques durs virtuels persistants utilisés pour les données d’application.

Lorsque vous créez un disque dur virtuel, il n’est pas formaté. Connectez-vous à la machine virtuelle pour formater le disque. Dans l’interpréteur de commandes Linux, les disques de données sont affichés en tant que /dev/sdc, /dev/sdd et ainsi de suite. Vous pouvez exécuter lsblk pour répertorier les appareils de bloc, notamment les disques. Pour utiliser un disque de données, créez une partition et un système de fichiers, puis montez le disque. Par exemple :

```bash
# Create a partition.
sudo fdisk /dev/sdc \# Enter 'n' to partition, 'w' to write the change.

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

Lorsque vous ajoutez un disque de données, un numéro d’unité logique (LUN) lui est attribué. Vous pouvez également spécifier l’ID LUN, par exemple si vous remplacez un disque et souhaitez conserver le même ID LUN, ou si votre application nécessite un ID LUN spécifique. Toutefois, n’oubliez pas que les ID LUN doivent être uniques pour chaque disque.

La machine virtuelle est créée avec un disque temporaire. Ce disque est stocké sur un volume temporaire de l’infrastructure de stockage Azure Stack Hub. Il peut être supprimé lors des redémarrages ou d’autres événements de cycle de vie de la machine virtuelle. N’utilisez ce disque que pour des données temporaires, telles que des fichiers de pagination ou d’échange. Pour les machines virtuelles Linux, le disque temporaire est /dev/sdb1 et il est monté sur /mnt/resource ou /mnt.

## <a name="network"></a>Réseau

Les composants réseau incluent les ressources suivantes :

-   **Réseau virtuel**. Chaque machine virtuelle est déployée dans un réseau virtuel qui peut être segmenté en plusieurs sous-réseaux.

-   **Interfaces réseau (NIC)** . La carte d’interface réseau permet à la machine virtuelle de communiquer avec le réseau virtuel. Si vous avez besoin de plusieurs cartes réseau (NIC) pour votre machine virtuelle, notez que le nombre maximal de cartes réseau est défini pour chaque [taille de machine virtuelle](./azure-stack-vm-sizes.md).

-   **Adresse IP publique/Adresse IP virtuelle**. Une adresse IP publique est nécessaire pour communiquer avec la machine virtuelle, par exemple via le protocole RDP. Cette adresse IP publique peut être dynamique ou statique. Par défaut, elle est dynamique. Si vous avez besoin de plusieurs cartes réseau (NIC) pour votre machine virtuelle, notez que le nombre maximal de cartes réseau est défini pour chaque [taille de machine virtuelle](./azure-stack-vm-sizes.md).

-   Vous pouvez également créer un nom de domaine complet (FQDN) pour l’adresse IP. Vous pouvez inscrire un [enregistrement CNAME](https://en.wikipedia.org/wiki/CNAME_record) dans le DNS qui pointe vers le nom de domaine complet (FQDN). Pour en savoir plus, consultez [Créer un nom de domaine complet dans le Portail Azure](/azure/virtual-machines/linux/portal-create-fqdn).

-   **Groupe de sécurité réseau.** Les groupes de sécurité réseau sont utilisés pour autoriser ou refuser le trafic réseau vers des machines virtuelles. Les groupes de sécurité réseau peuvent être associés à des sous-réseaux ou à des instances de machine virtuelle individuelles.

Tous les groupes de sécurité réseau contiennent un ensemble de [règles par défaut](/azure/virtual-network/security-overview#default-security-rules), notamment une règle qui bloque tout le trafic Internet entrant. Les règles par défaut ne peuvent pas être supprimées, mais d’autres règles peuvent les remplacer. Pour autoriser le trafic Internet, créez des règles qui autorisent le trafic entrant vers des ports spécifiques, par exemple le port 80 pour HTTP. Pour activer le protocole SSH, ajoutez une règle de groupe de sécurité réseau qui autorise le trafic entrant sur le port TCP 22.

## <a name="operations"></a>Opérations

**SSH**. Avant de créer une machine virtuelle Linux, générez une paire de clés publique-privée RSA 2048 bits. Utilisez le fichier de clé publique lorsque vous créez la machine virtuelle. Pour plus d’informations, consultez la rubrique [Utilisation de SSH avec Linux dans Azure](/azure/virtual-machines/linux/mac-create-ssh-keys).

**Diagnostics**. Permet la supervision et le diagnostic, avec notamment des indicateurs d’intégrité de base, des journaux d’activité d’infrastructure de diagnostic et des [diagnostics de démarrage](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Les diagnostics de démarrage peuvent vous aider à identifier le problème de démarrage si votre machine virtuelle refuse de démarrer. Créez un compte de stockage Azure pour stocker les journaux d’activité. Un compte de stockage localement redondant (LRS) standard suffit pour les journaux de diagnostic. Pour en savoir plus, consultez [Activation de la surveillance et des diagnostics](./azure-stack-metrics-azure-data.md).

**Disponibilité**. Votre machine virtuelle peut faire l’objet d’un redémarrage en raison d’une maintenance planifiée par l’opérateur Azure Stack Hub. Pour bénéficier d’une disponibilité plus élevée, déployez plusieurs machines virtuelles dans un [groupe à haute disponibilité](../operator/azure-stack-app-service-deploy.md?view=azs-2002).

**Sauvegardes** Pour obtenir des recommandations sur la protection de vos machines virtuelles IaaS Azure Stack Hub, consultez [cet](./azure-stack-manage-vm-protect.md) article.

**Arrêt d’une machine virtuelle**. Azure établit une distinction entre les états « arrêté » et « désalloué ». Vous payez lorsque l’état de la machine virtuelle est « arrêté », mais pas lorsque la machine virtuelle est désallouée. Sur le portail Azure Stack Hub, le bouton **Arrêter** désalloue la machine virtuelle. Si vous arrêtez la machine virtuelle par le biais du système d’exploitation pendant que vous êtes connecté, la machine virtuelle est arrêtée mais **non** désallouée. Vous serez donc toujours facturé.

**Suppression d’une machine virtuelle**. La suppression d’une machine virtuelle n’entraîne pas celle des disques associés. Vous pouvez donc supprimer la machine virtuelle, sans risque de perdre des données. Toutefois, vous serez toujours facturé pour le stockage. Pour supprimer le disque de machine virtuelle, supprimez l’objet disque managé. Pour éviter toute suppression accidentelle, utilisez un [verrou de ressource](/azure/resource-group-lock-resources) pour verrouiller tout le groupe de ressources ou des ressources individuelles, par exemple une machine virtuelle.

## <a name="security-considerations"></a>Considérations relatives à la sécurité

Intégrez vos machines virtuelles dans [Azure Security Center](/azure/security-center/quick-onboard-azure-stack) pour avoir un aperçu global de l’état de toutes vos ressources Azure en termes de sécurité. Il surveille les problèmes potentiels de sécurité et fournit une image complète de la sécurité de votre déploiement. Le Centre de sécurité est configuré pour chaque abonnement Azure. Activez la collecte de données de sécurité comme décrit dans [Intégrer un abonnement Azure à Security Center Standard](/azure/security-center/security-center-get-started). Une fois la collecte de données activée, le Centre de sécurité analyse automatiquement les machines virtuelles créées dans le cadre de cet abonnement.

**Gestion des correctifs**. Pour configurer la gestion des correctifs sur votre machine virtuelle, reportez-vous à [cet article](./vm-update-management.md). Si cette option est activée, Security Center vérifie si des mises à jour critiques et de sécurité sont manquantes. Utilisez les [paramètres de stratégie de groupe](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates) sur la machine virtuelle pour activer les mises à jour automatiques du système.

**Logiciel anti-programme malveillant**. Si cette option est activée, le Centre de sécurité vérifie si un logiciel anti-programme malveillant est installé. Vous pouvez également utiliser le Centre de sécurité pour installer des logiciels anti-programme malveillant dans le portail Azure.

**Contrôle d’accès**. Utilisez le [contrôle d’accès en fonction du rôle (RBAC)](/azure/active-directory/role-based-access-control-what-is) pour contrôler l’accès aux ressources Azure. Le contrôle RBAC vous permet d’affecter des rôles d’autorisation aux membres de votre équipe DevOps. Par exemple, le rôle Lecteur permet d’afficher des ressources Azure mais pas de les créer, gérer ou supprimer. Certaines autorisations sont spécifiques à un type de ressource Azure. Par exemple, le rôle Contributeur de machine virtuelle peut redémarrer ou désallouer une machine virtuelle, réinitialiser le mot de passe administrateur, créer une machine virtuelle, et ainsi de suite. D’autres [rôles RBAC intégrés](/azure/active-directory/role-based-access-built-in-roles) peuvent être utiles dans cette architecture, notamment [Utilisateur DevTest Lab](/azure/active-directory/role-based-access-built-in-roles#devtest-labs-user) et [Collaborateur de réseau](/azure/active-directory/role-based-access-built-in-roles#network-contributor).

> [!Note]  
> Le contrôle RBAC ne limite pas les actions qu’un utilisateur connecté peut effectuer sur une machine virtuelle. Ces autorisations dépendent du type de compte installé sur le système d’exploitation invité.

**Journaux d’audit**. Utilisez les [journaux d’activité](./azure-stack-metrics-azure-data.md?#activity-log) pour voir les actions de provisionnement et d’autres événements concernant la machine virtuelle.

**Chiffrement des données**. Azure Stack Hub protège les données des utilisateurs et de l’infrastructure au niveau du sous-système de stockage à l’aide du chiffrement au repos. Le sous-système de stockage Azure Stack Hub est chiffré à l’aide de BitLocker avec le chiffrement AES 128 bits. Pour plus d’informations, consultez [cet](../operator/azure-stack-security-bitlocker.md) article.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les machines virtuelles Azure Stack Hub, voir [Fonctionnalités des machines virtuelles Azure Stack Hub](azure-stack-vm-considerations.md).  
- Pour plus d’informations sur les modèles Azure Cloud, consultez [Modèles de conception cloud](/azure/architecture/patterns).
