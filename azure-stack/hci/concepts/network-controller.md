---
title: Planifier le déploiement du contrôleur de réseau
description: Cette rubrique explique comment planifier le déploiement du contrôleur de réseau par le biais de Windows Admin Center sur un groupe de machines virtuelles exécutant le système d’exploitation Azure Stack HCI.
author: AnirbanPaul
ms.author: anpaul
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 58ae46591e2119bbfc9269440f36e9c37fd0e7b9
ms.sourcegitcommit: 69c859a89941ee554d438d5472308eece6766bdf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89643935"
---
# <a name="plan-to-deploy-the-network-controller"></a>Planifier le déploiement du contrôleur de réseau

>S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019 

Planifier le déploiement du contrôleur de réseau via Windows Admin Center requiert un groupe de machines virtuelles exécutant le système d’exploitation Azure Stack HCI. Le contrôleur de réseau est un rôle serveur hautement disponible et scalable qui requiert un minimum de trois machines virtuelles pour fournir une haute disponibilité sur votre réseau.

   >[!NOTE]
   > Nous vous recommandons de déployer le contrôleur de réseau sur ses propres machines virtuelles dédiées.

## <a name="network-controller-requirements-for-azure-stack-hci"></a>Configuration requise du contrôleur de réseau pour Azure Stack HCI
Les éléments suivants sont requis pour déployer le contrôleur de réseau :
- Un disque dur virtuel pour le système d’exploitation Azure Stack HCI afin de créer les machines virtuelles du contrôleur de réseau.
- Un nom de domaine et des informations d’identification pour joindre les machines virtuelles du contrôleur de réseau à un domaine.
- Une configuration de réseau physique qui correspond à l’une des deux options de topologie de cette section.

    Windows Admin Center crée la configuration au sein de l’hôte Hyper-V. Toutefois, le réseau de gestion doit se connecter aux cartes physiques de l’ordinateur hôte selon l’une des deux options suivantes :

    **Option 1** : Un commutateur physique unique connecte le réseau de gestion à une carte de gestion physique sur l’ordinateur hôte et une jonction sur les cartes physiques utilisées par le commutateur virtuel :

    :::image type="content" source="./media/network-controller/topology-option-1.png" alt-text="Option 1 pour créer un réseau physique pour le contrôleur de réseau." lightbox="./media/network-controller/topology-option-1.png":::

    **Option 2** : Si le réseau de gestion est physiquement séparé des réseaux de charges de travail, deux commutateurs virtuels sont nécessaires :

    :::image type="content" source="./media/network-controller/topology-option-2.png" alt-text="Option 2 pour créer un réseau physique pour le contrôleur de réseau." lightbox="./media/network-controller/topology-option-1.png":::

- Informations sur le réseau de gestion que le contrôleur de réseau utilise pour communiquer avec Windows Admin Center et les hôtes Hyper-V.
- Adressage basé sur un réseau DHCP ou statique pour les machines virtuelles du contrôleur de réseau.
- Nom de domaine complet (FQDN) Representational State Transfer (REST) du contrôleur de réseau utilisé par les clients de gestion pour communiquer avec le contrôleur de réseau.

   >[!NOTE]
   > Actuellement, Windows Admin Center ne prend pas en charge l’authentification du contrôleur de réseau pour la communication avec les clients REST ni pour la communication entre les machines virtuelles du contrôleur de réseau. Vous pouvez utiliser l’authentification Kerberos si vous utilisez PowerShell pour la déployer et la gérer.

## <a name="configuration-requirements"></a>Exigences de configuration
Vous pouvez déployer les nœuds de cluster du contrôleur de réseau sur le même sous-réseau ou sur des sous-réseaux différents. Si vous envisagez de déployer les nœuds de cluster du contrôleur de réseau sur des sous-réseaux différents, vous devez fournir le nom DNS REST du contrôleur de réseau pendant le processus de déploiement.

Pour plus d’informations, consultez [Configurer l’inscription DNS dynamique pour le contrôleur de réseau](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).


## <a name="next-steps"></a>Étapes suivantes
Vous êtes maintenant prêt à déployer le contrôleur de réseau sur des machines virtuelles exécutant le système d’exploitation Azure Stack HCI.

Pour plus d'informations, consultez les rubriques suivantes :
- [Créer un cluster Azure Stack HCI](../deploy/create-cluster.md)

## <a name="see-also"></a>Voir aussi
- [Contrôleur de réseau](/windows-server/networking/sdn/technologies/network-controller/network-controller)
- [Haute disponibilité du contrôleur de réseau](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability)