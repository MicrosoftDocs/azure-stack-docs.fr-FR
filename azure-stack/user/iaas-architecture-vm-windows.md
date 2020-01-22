---
title: Exécuter une machine virtuelle Windows sur Azure Stack Hub | Microsoft Docs
description: Découvrez comment exécuter une machine virtuelle Windows sur Azure Stack Hub.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 20e855655edfdbf2c67c130b88cfa2af10338986
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75818449"
---
# <a name="run-a-windows-virtual-machine-on-azure-stack-hub"></a>Exécuter une machine virtuelle Windows sur Azure Stack Hub

L’approvisionnement d’une machine virtuelle dans Azure Stack Hub nécessite des composants en plus de la machine virtuelle proprement dite, dont des ressources réseau et de stockage. Cet article décrit les bonnes pratiques pour l’exécution d’une machine virtuelle Windows sur Azure.

![Architecture pour machine virtuelle Windows sur Azure Stack Hub](./media/iaas-architecture-vm-windows/image1.png)

## <a name="resource-group"></a>Resource group

Un [groupe de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) est un conteneur logique hébergeant des ressources Azure Stack Hub associées. En règle générale, regroupez les ressources en fonction de leur durée de vie et de qui va les gérer.

Placez les ressources étroitement associées qui partagent le même cycle de vie dans un même [groupe de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Les groupes de ressources vous permettent de déployer et de surveiller les ressources en tant que groupe et de suivre les coûts de facturation par groupe de ressources. Vous pouvez également supprimer des ressources comme un tout, ce qui est pratique pour les déploiements de test. Affectez des noms de ressource explicites pour simplifier la recherche d’une ressource spécifique et comprendre son rôle. Pour plus d’informations, consultez [Conventions d’affectation de noms recommandées pour les ressources Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="virtual-machine"></a>Machine virtuelle

Vous pouvez approvisionner une machine virtuelle à partir d’une liste d’images publiées, d’une image managée personnalisée ou d’un fichier de disque dur virtuel (VHD) chargés dans le stockage d’objets blob Azure Stack Hub.

Azure Stack Hub propose différentes tailles de machines virtuelles Azure. Pour plus d’informations, voir [Tailles des machines virtuelles dans Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes). Si vous déplacez une charge de travail vers Azure Stack Hub, commencez par choisir la taille de machine virtuelle correspondant le mieux à vos serveurs locaux/Azure. Mesurez ensuite les performances de votre charge de travail réelle en termes de processeur, de mémoire et d’opérations d’entrée/sortie par seconde du disque, puis ajustez la taille selon vos besoins.

## <a name="disks"></a>Disques

Le coût est basé sur la capacité du disque approvisionné. Le nombre d’E/S par seconde et le débit (c’est-à-dire le taux de transfert des données) dépendent de la taille de la machine virtuelle. Lorsque vous provisionnez un disque, vous devez donc tenir compte des trois facteurs : capacité, E/S par seconde et débit.

Sur Azure Stack Hub, les IOPS (opérations d’entrée/sortie par seconde) du disque dépendent de la [taille de la machine virtuelle](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes) plutôt que du type de disque. Cela signifie que, pour une machine virtuelle Standard_Fs, quel que soit le type de disque choisi (SSD ou HDD), la limite d'IOPS est de 2 300 par disque de données supplémentaire. La limite d’IOPS imposée est un plafond (maximum possible) pour éviter les voisins bruyants. Elle ne garantit pas les IOPS obtenues avec une taille de machine virtuelle spécifique.

Nous vous recommandons également d’utiliser la fonctionnalité [Disques managés](https://docs.microsoft.com/azure-stack/user/azure-stack-managed-disk-considerations). Les disques managés simplifient la gestion des disques en gérant le stockage pour vous. Les disques managés ne nécessitent pas de compte de stockage. Il vous suffit de spécifier leur taille et leur type, puis de les déployer en tant que ressource hautement disponible.

Le disque du système d’exploitation étant un disque dur virtuel stocké dans le stockage d’objets blob Azure Stack Hub, il persiste même lorsque l’ordinateur hôte est arrêté. Nous vous recommandons également de créer un ou plusieurs [disques de données](https://docs.microsoft.com/azure-stack/user/azure-stack-manage-vm-disks), qui sont des disques durs virtuels persistants utilisés pour les données d’application. Lorsque cela est possible, installez les applications sur un disque de données plutôt que sur le disque du système d’exploitation. Certaines applications héritées requièrent peut-être l’installation de composants sur le lecteur C: ; dans ce cas, vous pouvez [redimensionner le disque du système d’exploitation](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-expand-os-disk) à l’aide de PowerShell.

La machine virtuelle est créée avec un disque temporaire (le lecteur D: sur Windows). Ce disque est stocké sur un volume temporaire dans l’infrastructure de stockage Azure Stack Hub. Il peut être supprimé lors des redémarrages ou d’autres événements de cycle de vie de la machine virtuelle. N’utilisez ce disque que pour des données temporaires, telles que des fichiers de pagination ou d’échange.

## <a name="network"></a>Réseau

Les composants réseau incluent les ressources suivantes :

-   **Réseau virtuel**. Chaque machine virtuelle est déployée dans un réseau virtuel qui peut être segmenté en plusieurs sous-réseaux.

-   **Interfaces réseau (NIC)** . La carte d’interface réseau permet à la machine virtuelle de communiquer avec le réseau virtuel. Si vous avez besoin de plusieurs cartes réseau (NIC) pour votre machine virtuelle, notez que le nombre maximal de cartes réseau est défini pour chaque [taille de machine virtuelle](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes).

-   **Adresse IP publique/Adresse IP virtuelle**. Une adresse IP publique est nécessaire pour communiquer avec la machine virtuelle, par exemple via le protocole RDP. Cette adresse IP publique peut être dynamique ou statique. Par défaut, elle est dynamique.

-   Utilisez une [adresse IP statique](https://docs.microsoft.com/azure/virtual-network/virtual-networks-reserved-public-ip) si vous avez besoin d’une adresse IP non modifiable, par exemple si vous devez créer un enregistrement « A » dans le DNS ou ajouter l’adresse IP dans une liste sécurisée.

-   Vous pouvez également créer un nom de domaine complet (FQDN) pour l’adresse IP. Vous pouvez inscrire un [enregistrement CNAME](https://en.wikipedia.org/wiki/CNAME_record) dans le DNS qui pointe vers le nom de domaine complet (FQDN). Pour en savoir plus, consultez [Créer un nom de domaine complet dans le Portail Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-portal-create-fqdn).

-   **Groupe de sécurité réseau**. Les groupes de sécurité réseau sont utilisés pour autoriser ou refuser le trafic réseau vers une machine virtuelle. Les groupes de sécurité réseau peuvent être associés à des sous-réseaux ou à des instances de machine virtuelle individuelles.

Tous les groupes de sécurité réseau contiennent un ensemble de [règles par défaut](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules), notamment une règle qui bloque tout le trafic Internet entrant. Les règles par défaut ne peuvent pas être supprimées, mais d’autres règles peuvent les remplacer. Pour autoriser le trafic Internet, créez des règles qui autorisent le trafic entrant vers des ports spécifiques, par exemple le port 80 pour HTTP. Pour activer le protocole RDP, ajoutez une règle de groupe de sécurité réseau qui autorise le trafic entrant sur le port TCP 3389.

## <a name="operations"></a>Opérations

**Diagnostics**. Permet la supervision et le diagnostic, avec notamment des indicateurs d’intégrité de base, des journaux d’infrastructure de diagnostic et des [diagnostics de démarrage](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Les diagnostics de démarrage peuvent vous aider à identifier le problème de démarrage si votre machine virtuelle refuse de démarrer. Créez un compte de stockage Azure pour stocker les journaux d’activité. Un compte de stockage localement redondant (LRS) standard suffit pour les journaux de diagnostic. Pour en savoir plus, consultez [Activation de la surveillance et des diagnostics](https://docs.microsoft.com/azure-stack/user/azure-stack-metrics-azure-data).

**Disponibilité**. Votre machine virtuelle peut faire l’objet d’un redémarrage en raison d’une maintenance planifiée par l’opérateur Azure Stack Hub. Pour garantir la haute disponibilité d’un système de production à plusieurs machines virtuelles dans Azure, ces machines virtuelles sont placées dans un [groupe à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) qui les répartit entre plusieurs domaines d’erreur et de mise à jour. À la plus petite échelle d’Azure Stack Hub, un domaine d’erreur au sein d’un groupe à haute disponibilité est défini comme un nœud unique dans l’unité d’échelle.  

Même si l’infrastructure d’Azure Stack Hub est déjà résiliente aux défaillances, la technologie sous-jacente (clustering de basculement) implique toujours un temps d’arrêt pour les machines virtuelles qui se trouvent sur un serveur physique impacté en cas de défaillance matérielle. Azure Stack Hub prend ne charge les groupes à haute disponibilité avec un maximum de trois domaines d’erreur, pour rester cohérent avec Azure.

|                   |             |
|-------------------|-------------|
| **Domaines d'erreur** | Les machines virtuelles placées dans un groupe à haute disponibilité sont physiquement isolées les unes des autres grâce à une répartition aussi équilibrée que possible sur plusieurs domaines d’erreur (nœuds Azure Stack Hub). En cas de défaillance matérielle, les machines virtuelles du domaine défaillant sont redémarrées dans d’autres domaines d’erreur. Elles seront conservées dans des domaines d’erreur distincts des autres machines virtuelles, mais si possible dans le même groupe à haute disponibilité. Une fois le matériel rétabli, les machines virtuelles seront rééquilibrées de façon à maintenir une haute disponibilité. |
| **Domaines de mise à jour**| Les domaines de mise à jour sont utilisés par Azure pour fournir la haute disponibilité aux groupes à haute disponibilité. Un domaine de mise à jour est un groupe logique de matériel sous-jacent pouvant faire l’objet simultanément d’une opération de maintenance. Les machines virtuelles qui se trouvent dans le même domaine de mise à jour sont redémarrées ensemble lors de la maintenance planifiée. Lorsqu’un client crée des machines virtuelles au sein d’un groupe à haute disponibilité, la plateforme Azure les distribue automatiquement dans ces différents domaines de mise à jour. <br>Dans Azure Stack Hub, les machines virtuelles sont migrées en direct sur les autres hôtes en ligne du cluster avant que leur hôte sous-jacent soit mis à jour. Comme il ne se produit aucun temps d’arrêt du côté du locataire pendant la mise à jour d’un hôte, la fonctionnalité de domaine de mise à jour d’Azure Stack Hub n’existe que pour des raisons de compatibilité des modèles avec Azure. Les machines virtuelles d’un groupe à haute disponibilité affichent 0 comme numéro de domaine de mise à jour sur le portail. |

**Sauvegardes** Pour obtenir des recommandations sur la protection de vos machines virtuelles IaaS Azure Stack Hub, voir cet article.

**Arrêt d’une machine virtuelle**. Azure établit une distinction entre les états « arrêté » et « désalloué ». Vous payez lorsque l’état de la machine virtuelle est « arrêté », mais pas lorsque la machine virtuelle est désallouée. Sur le portail Azure Stack Hub, le bouton **Arrêter** désalloue la machine virtuelle. Si vous arrêtez la machine virtuelle par le biais du système d’exploitation pendant que vous êtes connecté, la machine virtuelle est arrêtée mais **non** désallouée. Vous serez donc toujours facturé.

**Suppression d’une machine virtuelle**. La suppression d’une machine virtuelle n’entraîne pas celle des disques associés. Vous pouvez donc supprimer la machine virtuelle, sans risque de perdre des données. Toutefois, vous serez toujours facturé pour le stockage. Pour supprimer le disque de machine virtuelle, supprimez l’objet disque managé. Pour éviter toute suppression accidentelle, utilisez un *verrou de ressource* pour verrouiller tout le groupe de ressources ou des ressources individuelles, par exemple une machine virtuelle.

## <a name="security-considerations"></a>Considérations relatives à la sécurité

Intégrez vos machines virtuelles dans [Azure Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack) pour avoir un aperçu global de l’état de toutes vos ressources Azure en termes de sécurité. Il surveille les problèmes potentiels de sécurité et fournit une image complète de la sécurité de votre déploiement. Le Centre de sécurité est configuré pour chaque abonnement Azure. Activez la collecte de données de sécurité comme décrit dans [Intégrer un abonnement Azure à Security Center Standard](https://docs.microsoft.com/azure/security-center/security-center-get-started). Une fois la collecte de données activée, le Centre de sécurité analyse automatiquement les machines virtuelles créées dans le cadre de cet abonnement.

**Gestion des correctifs**. Pour configurer la gestion des correctifs sur votre machine virtuelle, reportez-vous à [cet article](https://docs.microsoft.com/azure-stack/user/vm-update-management). Si cette option est activée, Security Center vérifie si des mises à jour critiques et de sécurité sont manquantes. Utilisez les [paramètres de stratégie de groupe](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates) sur la machine virtuelle pour activer les mises à jour automatiques du système.

**Logiciel anti-programme malveillant**. Si cette option est activée, le Centre de sécurité vérifie si un logiciel anti-programme malveillant est installé. Vous pouvez également utiliser le Centre de sécurité pour installer des logiciels anti-programme malveillant dans le portail Azure.

**Contrôle d’accès**. Utilisez le [contrôle d’accès en fonction du rôle (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) pour contrôler l’accès aux ressources Azure. Le contrôle RBAC vous permet d’affecter des rôles d’autorisation aux membres de votre équipe DevOps. Par exemple, le rôle Lecteur permet d’afficher des ressources Azure mais pas de les créer, gérer ou supprimer. Certaines autorisations sont spécifiques à un type de ressource Azure. Par exemple, le rôle Contributeur de machine virtuelle peut redémarrer ou désallouer une machine virtuelle, réinitialiser le mot de passe administrateur, créer une machine virtuelle, et ainsi de suite. D’autres [rôles RBAC intégrés](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) peuvent être utiles dans cette architecture, notamment [Utilisateur DevTest Lab](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#devtest-labs-user) et [Collaborateur de réseau](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#network-contributor).

> [!Note]  
> Le contrôle RBAC ne limite pas les actions qu’un utilisateur connecté peut effectuer sur une machine virtuelle. Ces autorisations dépendent du type de compte installé sur le système d’exploitation invité.

**Journaux d’audit**. Utilisez les [journaux d’activité](https://docs.microsoft.com/azure-stack/user/azure-stack-metrics-azure-data?#activity-log) pour voir les actions de provisionnement et d’autres événements concernant la machine virtuelle.

**Chiffrement des données**. Azure Stack Hub utilise le chiffrement AES 128 bits BitLocker pour protéger les données de l’utilisateur et de l’infrastructure au repos dans le sous-système de stockage. Pour plus d’informations, voir [Chiffrement des données au repos dans Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-security-bitlocker).


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les machines virtuelles Azure Stack Hub, voir [Fonctionnalités des machines virtuelles Azure Stack Hub](azure-stack-vm-considerations.md).  
- Pour plus d’informations sur les modèles Azure Cloud, consultez [Modèles de conception cloud](https://docs.microsoft.com/azure/architecture/patterns).
