---
title: Déployer la filiale et la périphérie sur Azure Stack HCI
description: Cette rubrique fournit de l’aide relative aux procédures de planification, de configuration et de déploiement de scénarios de filiale et de périphérie sur le système d’exploitation Azure Stack HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 672a97a9804de324edde7c3802849a32ea44c0c4
ms.sourcegitcommit: dd34ae1c6207aafb5218c31658123e913f51bf7c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98691062"
---
# <a name="deploy-branch-office-and-edge-on-azure-stack-hci"></a>Déployer la filiale et la périphérie sur Azure Stack HCI

>S’applique à : Azure Stack HCI, version 20H2

Cette rubrique fournit de l’aide relative aux procédures de planification, de configuration et de déploiement de scénarios de filiale et de périphérie sur le système d’exploitation Azure Stack HCI. Cette aide permet à votre organisation d’exécuter des charges de travail complexes et hautement disponibles sur les machines virtuelles et dans les conteneurs des filiales distantes et des déploiements en périphérie. L’informatique en périphérie transfère le traitement de la plupart des données d’un système centralisé vers la périphérie du réseau, plus proche d’un appareil ou d’un système qui nécessite des données rapidement.

Utilisez Azure Stack HCI pour exécuter des applications virtualisées et des charges de travail avec une haute disponibilité sur le matériel recommandé. Le matériel prend en charge les clusters constitués de deux serveurs configurés avec une résilience imbriquée pour le stockage, un témoin de cluster de lecteurs USB simple, à faible coût et l’administration via Windows Admin Center basé sur un navigateur. Pour plus de détails sur la création d’un témoin de cluster d’appareils USB, consultez [Déployer un témoin de partage de fichiers](https://docs.microsoft.com/windows-server/failover-clustering/file-share-witness).

Azure IoT Edge déplace l’analytique et la logique métier personnalisée du cloud vers les appareils afin que votre organisation puisse se concentrer sur les perspectives commerciales plutôt que sur la gestion des données. Azure IoT Edge combine l’intelligence artificielle, le cloud et le computing en périphérie dans des charges de travail cloud en conteneur, telles que Azure Cognitive Services, Machine Learning, Stream Analytics et Functions. Les charges de travail peuvent s’exécuter sur des appareils allant d’un Raspberry Pi à un serveur de périphérie convergé. [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) permet de gérer vos applications et appareils en périphérie.

L’ajout d’Azure IoT Edge à votre filiale Azure Stack HCI et à vos déploiements en périphérie modernise votre environnement pour prendre en charge l’infrastructure de déploiement d’applications du [pipeline d’intégration continue et de livraison continue](https://docs.microsoft.com/azure/iot-edge/how-to-continuous-integration-continuous-deployment). Le personnel DevOps de votre organisation peut déployer et itérer des applications en conteneur générées et prises en charge par le service informatique via les outils et processus de gestion de machine virtuelle traditionnels.

Principales fonctionnalités d’Azure IoT Edge :
- Logiciel open source de Microsoft
- S’exécute sur Windows ou Linux
- S’exécute « en périphérie » pour fournir des réponses quasiment en temps réel
- Mécanismes logiciels et matériels sécurisés
- Disponible dans [AI Toolkit pour Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge)
- Prise en charge de la programmabilité ouverte pour : Java, .NET Core 2.0, Node.js, C et Python
- Prise en charge de la connectivité hors connexion et intermittente
- Gestion native à partir d’Azure IoT Hub

Pour en savoir plus, consultez [Présentation d’Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/about-iot-edge).

## <a name="deploy-branch-office-and-edge"></a>Déployer la filiale et la périphérie
Cette section décrit succinctement la manière d’acquérir du matériel pour les déploiements de filiales et en périphérie sur Azure Stack HCI et l’utilisation de Windows Admin Center pour la gestion. Elle aborde également le déploiement d’Azure IoT Edge pour gérer les conteneurs dans le cloud.

### <a name="step-1-acquire-hardware-from-the-azure-stack-hci-catalog"></a>Étape 1 : Acquérir du matériel à partir du catalogue Azure Stack HCI
Tout d’abord, vous devez vous procurer du matériel. Le moyen le plus simple est de localiser votre partenaire de matériel Microsoft préféré dans le [catalogue Azure Stack HCI](https://hcicatalog.azurewebsites.net) et d’acheter un système intégré sur lequel le système d’exploitation Azure Stack HCI est préinstallé. Dans le catalogue, vous pouvez appliquer un filtre de façon à afficher le matériel fournisseur qui est optimisé pour ce type de charge de travail.

Dans le cas contraire, vous devez déployer le système d’exploitation Azure Stack HCI sur votre propre matériel. Pour plus d’informations sur les options de déploiement Azure Stack HCI et sur l’installation de Windows Admin Center, consultez [Déployer le système d’exploitation Azure Stack HCI](./operating-system.md).

Ensuite, utilisez Windows Admin Center pour [créer un cluster Azure Stack HCI](./create-cluster.md).

### <a name="step-2-set-up-azure-monitor-in-windows-admin-center"></a>Étape 2 : Configurer Azure Monitor dans Windows Admin Center
Dans Windows Admin Center, configurez Azure Monitor pour obtenir un aperçu de l’application, du réseau et de l’intégrité de votre déploiement de filiales et en périphérie d’Azure Stack HCI.

Pour en savoir plus, consultez [Superviser Azure Stack HCI avec Azure Monitor](../manage/azure-monitor.md).

Vous pouvez également utiliser Windows Admin Center pour configurer d’autres services hybrides Azure, tels que Sauvegarde, File Sync, Site Recovery, VPN de point à site, Update Management et Security Center.

### <a name="step-3-use-container-based-apps-and-iot-data-processing"></a>Étape 3 : Utiliser des applications basées sur des conteneurs et le traitement des données IoT
Vous êtes maintenant prêt à utiliser le développement moderne d’applications basées sur des conteneurs et le traitement des données IoT. Utilisez Windows Admin Center pour les étapes de cette section afin de déployer une machine virtuelle exécutant Azure IoT Edge.

Pour en savoir plus, consultez [Présentation d’Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/about-iot-edge).

Pour déployer Azure IoT Edge sur Azure Stack HCI :
1. Utilisez Windows Admin Center pour [créer une machine virtuelle dans Azure Stack HCI](https://docs.microsoft.com/windows-server/manage/windows-admin-center/use/manage-virtual-machines#create-a-new-virtual-machine).

    Pour plus d’informations sur les versions de système d’exploitation, les types de machines virtuelles, les architectures de processeur et la configuration système pris en charge, consultez [Systèmes Azure IoT Edge pris en charge](https://docs.microsoft.com/azure/iot-edge/support).

1. Si vous n’avez pas encore de compte Azure, créez un [compte gratuit](https://azure.microsoft.com/free).
1. Dans le portail Azure, [créez un hub Azure IoT](https://docs.microsoft.com/azure/iot-edge/quickstart#create-an-iot-hub).
1. Dans le portail Azure, [inscrivez un appareil IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart#register-an-iot-edge-device).

    >[!NOTE]
    > L’appareil IoT Edge se trouve sur une machine virtuelle exécutant Windows ou Linux sur Azure Stack HCI.

1. Sur la machine virtuelle que vous avez créée à l’étape 1, [installez et démarrez le runtime IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart#install-and-start-the-iot-edge-runtime).

   >[!IMPORTANT]
   > Vous avez besoin de la chaîne d’appareil que vous avez créée à l’étape 4 pour connecter le runtime à Azure IoT Hub.

1. [Déployez un module](https://docs.microsoft.com/azure/iot-edge/quickstart#deploy-a-module) sur Azure IoT Edge.

    Vous pouvez approvisionner et déployer des modules prédéfinis à partir de la section [Modules IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) de la Place de marché Azure.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les filiales et les périphéries, ainsi que sur Azure IoT Edge, consultez :
- [Démarrage rapide : Déployer votre premier module IoT Edge sur un appareil virtuel Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux?view=iotedge-2018-06&preserve-view=true)
- [Démarrage rapide : Déployer votre premier module IoT Edge sur un appareil Windows](https://docs.microsoft.com/azure/iot-edge/quickstart?view=iotedge-2018-06&preserve-view=true)
