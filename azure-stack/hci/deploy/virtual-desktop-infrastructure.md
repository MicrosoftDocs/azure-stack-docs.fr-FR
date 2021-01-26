---
title: Déployer Virtual Desktop Infrastructure (VDI) sur Azure Stack HCI
description: Cette rubrique fournit des conseils sur la planification, la configuration et le déploiement de Virtual Desktop Infrastructure (VDI) sur le système d’exploitation Azure Stack HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: dfe22eb768b5bf661760c31c913d8c93caf590a4
ms.sourcegitcommit: 0983c1f90734b7ea5e23ae614eeaed38f9cb3c9a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571588"
---
# <a name="deploy-virtual-desktop-infrastructure-vdi-on-azure-stack-hci"></a>Déployer Virtual Desktop Infrastructure (VDI) sur Azure Stack HCI

>S’applique à : Azure Stack HCI, version 20H2

Cette rubrique fournit des conseils sur la planification, la configuration et le déploiement de Virtual Desktop Infrastructure (VDI) sur le système d’exploitation Azure Stack HCI. Tirez parti de votre investissement Azure Stack HCI pour proposer une gestion centralisée, hautement disponible, simplifiée et sécurisée aux utilisateurs de votre organisation. Utilisez ces conseils pour mettre en place des scénarios comme BYOD (Bring Your Own Device) pour vos utilisateurs, tout en leur proposant une expérience cohérente et fiable pour les applications critiques pour l’entreprise sans sacrifier la sécurité.

## <a name="overview"></a>Vue d’ensemble
VDI utilise du matériel serveur pour exécuter des systèmes d’exploitation de bureau et des programmes logiciels sur une machine virtuelle. Ainsi, VDI vous permet d’exécuter des charges de travail de bureau traditionnelles sur des serveurs centralisés. Dans un contexte d’entreprise, VDI offre les avantages de conserver les applications et données sensibles de l’entreprise dans un centre de données sécurisé et d’adopter une stratégie BYOD sans se soucier de la combinaison des données personnelles avec les ressources de l’entreprise. VDI est également devenu la norme pour prendre en charge les télétravailleurs et les employés en présentiel, ainsi que pour fournir un accès aux sous-traitants et partenaires.

Azure Stack HCI offre la plateforme optimale pour VDI. Une solution Azure Stack HCI validée et combinée aux [Services Bureau à distance Microsoft](/windows-server/remote/remote-desktop-services/welcome-to-rds) vous permet d’obtenir une architecture hautement disponible et hautement scalable.

De plus, VDI sur Azure Stack HCI fournit les fonctionnalités cloud uniques suivantes pour protéger les charges de travail et clients VDI :
- Gestion centralisée des mises à jour avec Azure Update Management
- Gestion unifiée de la sécurité et protection avancée contre les menaces pour les clients VDI

## <a name="deploy-vdi"></a>Déployer une infrastructure VDI
Cette section décrit succinctement la manière d’acquérir du matériel pour déployer VDI sur Azure Stack HCI et utiliser Windows Admin Center pour la gestion. Elle aborde aussi le déploiement des services Bureau à distance pour prendre en charge VDI.

### <a name="step-1-acquire-hardware-for-vdi-on-azure-stack-hci"></a>Étape 1 : Acquérir du matériel pour VDI sur Azure Stack HCI
Tout d’abord, vous devez vous procurer du matériel. Le moyen le plus simple est de localiser votre partenaire de matériel Microsoft préféré dans le [catalogue Azure Stack HCI](https://hcicatalog.azurewebsites.net) et d’acheter un système intégré sur lequel le système d’exploitation Azure Stack HCI est préinstallé. Dans le catalogue, vous pouvez appliquer un filtre de façon à afficher le matériel fournisseur qui est optimisé pour ce type de charge de travail. Veillez à consulter votre partenaire matériel pour vérifier que le matériel peut prendre en charge le nombre de bureaux virtuels que vous voulez héberger sur votre cluster.

Dans le cas contraire, vous devez déployer le système d’exploitation Azure Stack HCI sur votre propre matériel. Pour plus d’informations sur les options de déploiement Azure Stack HCI et sur l’installation de Windows Admin Center, consultez [Déployer le système d’exploitation Azure Stack HCI](./operating-system.md).

Ensuite, utilisez Windows Admin Center pour [créer un cluster Azure Stack HCI](./create-cluster.md).

### <a name="step-2-set-up-azure-update-management-in-windows-admin-center"></a>Étape 2 : Configurer Azure Update Management dans Windows Admin Center
Dans Windows Admin Center, configurez [Azure Update Management](/windows-server/manage/windows-admin-center/azure/azure-update-management) pour évaluer rapidement l’état des mises à jour disponibles, planifier les mises à jour nécessaires et passer en revue les résultats de déploiement pour vérifier les mises à jour appliquées.

Pour commencer à utiliser Azure Update Management, vous devez avoir un abonnement à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free).

Vous pouvez aussi utiliser Windows Admin Center pour configurer d’autres services hybrides Azure, comme Sauvegarde, File Sync, Site Recovery, VPN de point à site et Azure Security Center.

### <a name="step-3-deploy-remote-desktop-services-rds-for-vdi-support"></a>Étape 3 : Déployer les services Bureau à distance pour la prise en charge de VDI
Une fois que votre déploiement d’Azure Stack HCI et votre inscription auprès d’Azure pour utiliser Update Management sont terminés, vous êtes prêt à utiliser les instructions données dans cette section pour générer et déployer les services Bureau à distance afin de prendre en charge VDI.

Pour générer et déployer les services Bureau à distance :
1. [Déployer un environnement des services Bureau à distance](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)
1. [Créer une collection de sessions des services Bureau à distance](/windows-server/remote/remote-desktop-services/rds-create-collection) pour partager des applications et ressources
1. [Licence de votre déploiement de services Bureau à distance](/windows-server/remote/remote-desktop-services/rds-client-access-license)
1. Demander à vos utilisateurs d’installer un  [client Bureau à distance](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) pour accéder aux applications et ressources
1. Ajouter des services Broker pour les connexions et des hôtes de session pour permettre la haute disponibilité :
    - [Monter en charge une collection existante de services Bureau à distance avec une batterie de serveurs Hôte de session Bureau à distance](/windows-server/remote/remote-desktop-services/rds-scale-rdsh-farm)
    - [Ajouter la haute disponibilité à l'infrastructure du service Broker pour les connexions Bureau à distance](/windows-server/remote/remote-desktop-services/rds-connection-broker-cluster)
    - [Ajouter la haute disponibilité du site Web des services Bureau à distance et de la passerelle des services Bureau à distance web frontale](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)
    - [Déployer un système de fichiers d’espaces de stockage direct à deux nœuds pour le stockage UPD](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur VDI, consultez [Configurations prises en charge pour les services Bureau à distance](/windows-server/remote/remote-desktop-services/rds-supported-config).
