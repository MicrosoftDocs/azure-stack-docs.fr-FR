---
title: Gérer IoT Hub sur Azure Stack Hub
description: Expérience de gestion IoT Hub et alertes
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: 3fe1459d28c399269ccdb1d4cffed024bd741000
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255739"
---
# <a name="how-to-manage-iot-hub-on-azure-stack-hub"></a>Gérer IoT Hub sur Azure Stack Hub

L’expérience de gestion IoT Hub vous permet de visualiser et de gérer l’état global, les alertes et la capacité.

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

## <a name="iot-hub-management-dashboard"></a>Tableau de bord de gestion IoT Hub

Pour accéder au tableau de bord de gestion IoT Hub :

1. Connectez-vous au portail d’administration Azure Stack Hub, sélectionnez la vue **Tableau de bord** sur la gauche, puis le fournisseur de ressources **IoT Hub** :

   [![tableau de bord de l’opérateur](media\iot-hub-rp-manage\dashboard.png)](media\iot-hub-rp-manage-capacity\dashboard.png#lightbox)

2. Le tableau de bord IoT Hub fournit une vue récapitulative présentant les alertes actuelles, les quotas créés sur le tampon et le nombre total de clusters IoT Hub de votre abonnement : 

   [![tableau de bord iot hub - vue d’ensemble](media\iot-hub-rp-manage\dashboard-rp-iot-hub-overview.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-overview.png#lightbox)

## <a name="alerts"></a>Alertes

Le fournisseur de ressources IoT Hub prend en charge les alertes suivantes :

|Category|Alerte|Type|Description|
|-|-|-|-|
|Performances|L’utilisation du processeur IoT Hub nécessite votre attention.|Avertissement|La moyenne du pourcentage d’utilisation du processeur du fournisseur de ressources IoT Hub au cours des 6 dernières heures est supérieure à 75 %.|
|Performances|L’utilisation de la mémoire IoT Hub nécessite votre attention.|Avertissement|L’utilisation de la mémoire restante du fournisseur de ressources IoT Hub au cours des 6 dernières heures est inférieure à 1 024 Mo.|
|Performances|Espace disque insuffisant pour le fournisseur de ressources IoT Hub.|Avertissement|L’espace disque restant est inférieur à 10 %.|
|Ressource|Échec de création ou de mise à jour de la ressource IoT Hub.|Avertissement|Le nombre d’échecs de création ou de mise à jour du fournisseur de ressources IoT Hub est inférieur ou égal à 1 en 15 minutes.|
|Service|Les erreurs de journalisation du fournisseur de ressources IoT Hub nécessitent une attention.|Avertissement|Le nombre d’échecs de journalisation du fournisseur de ressources IoT Hub par instance de rôle est supérieur à 3 en 15 minutes.|
|Service|IoTHub-SU-InternalError|Avertissement|Le nombre d’échecs et d’expirations internes de l’unité de stockage IoT Hub n’est pas inférieur à 5 au cours des 30 dernières minutes.|
|Service|Un échec de sauvegarde du plan de données IoT Hub s’est produit.|Avertissement|Le plan de données IoT Hub a subi des échecs de sauvegarde au cours des 15 dernières minutes. Les données de l’appareil ne seront pas protégées.|
|Service|Un nouvel échec de sauvegarde du plan de données IoT Hub s’est produit.|Avertissement|Le plan de données IoT Hub a subi de nouveaux échecs de sauvegarde au cours des 15 dernières minutes. Les données de l’appareil ne seront pas protégées.|
|Service|Un échec de sauvegarde sans succès du plan de données IoT Hub s’est produit.|Avertissement|Aucune sauvegarde IoT hub n’a abouti au cours des 3 dernières heures. Les données de l’appareil peuvent ne pas être protégées.|
|Service|Échec de restauration du plan de données IoT Hub.|Avertissement|La restauration des informations de l’appareil IoT Hub a échoué. IotHubPerformanceMetrics a dépassé le seuil au cours des 15 dernières minutes.|
|Service|Un échec de passerelle IoT Hub s’est produit.|Avertissement|Un échec de passerelle IoT Hub s’est produit. La fonctionnalité de télémétrie de l’appareil peut être affectée.|
|Service|Un échec de conteneur IoT Hub s’est produit.|Avertissement|Un échec de conteneur IoT Hub s’est produit. L’authentification de l’appareil peut échouer. |
|Service|Un échec de conteneur de gestion des appareils IoT Hub s’est produit.|Avertissement|Un échec de conteneur de gestion des appareils IoT Hub s’est produit. Les fonctionnalités de jumeaux d’appareil et de méthode directe peuvent être dégradées.|

Pour surveiller les alertes et les quotas :

1. Sélectionnez **Alertes** pour afficher l’historique des alertes : 
 
   [![tableau de bord iot hub - alertes](media\iot-hub-rp-manage\dashboard-rp-iot-hub-alerts.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-alerts.png#lightbox)  

2. Sélectionnez **Quotas** pour afficher la liste des quotas en vigueur :  

   > [!NOTE]
   > Dans le cadre de la préversion, la fonctionnalité **Créer** est désactivée et un quota illimité par défaut est fourni. **Créer** sera activé pour la version en disponibilité générale.

   [![tableau de bord iot hub - quotas](media\iot-hub-rp-manage\dashboard-rp-iot-hub-quotas.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-quotas.png#lightbox) 

## <a name="capacity-management"></a>Gestion des capacités

En tant qu’opérateur, vous gérez et exploitez votre instance Azure Stack Hub de la même manière que n’importe quel opérateur cloud. Vous vous assurez que les logiciels sont correctement installés, configurés et sécurisés, et qu’ils fonctionnent pour la haute disponibilité (HA), de manière cohérente et efficace. Vous devez appliquer les principes de gestion de la capacité à l’instance Azure Stack Hub et au fournisseur de ressources IoT Hub.

### <a name="azure-stack-hub-capacity-management"></a>Gestion de la capacité Azure Stack Hub

Pour déterminer la capacité requise pour IoT Hub, vous devez estimer la charge de travail, notamment le nombre d’appareils et le débit des messages. Pour faciliter la planification, nous avons effectué les tests suivants sur un environnement Azure Stack Hub à 4 nœuds :

| Scénario | Machines virtuelles | Vcores | Appareils/Hub | [Édition Hub](https://azure.microsoft.com/pricing/details/iot-hub) | Hubs | Unités/Hub | Nombre total d’appareils | Total des unités Hub | Millions de messages/jour |
|----------|---------------|------------------|-----------------------|-------------------|-|-|-|-|-|
|Default|5|20|300 000|S2|4|200|1 200 000|800|4 800|
|12 machines virtuelles|12|48|500 000|S2|4|200|2 000 000|800|4 800|
|18 machines virtuelles|18|72|400 000|S3|4|10|1 600 000|40|12,000|

Pour plus d’informations, consultez [Azure Stack Hub Capacity Planner](azure-stack-capacity-planner.md).

### <a name="iot-hub-capacity-management"></a>Gestion de la capacité IoT Hub

Azure Stack Hub étant déployé dans un centre de données local avec ressources limitées, tous les services s’exécutant sur Azure Stack Hub partagent et se disputent le même pool de ressources. Les opérateurs doivent planifier et gérer la capacité en fonction des besoins de l’entreprise. Le fournisseur de ressources IoT Hub offre aux opérateurs la possibilité de gérer les besoins en capacité pour le service.

IoT Hub dispose d’un type de machine virtuelle unique. Dans le cadre du déploiement de IoT Hub, le système configure un ensemble de ces machines virtuelles sur Azure Stack Hub. Les machines virtuelles peuvent prendre en charge un certain nombre d’appareils et un certain débit de messages. Le paramètre par défaut doit répondre à la plupart des exigences. Toutefois, s’il vous faut un nombre d’appareils ou un débit de messages supérieur, vous pouvez augmenter la capacité à l’aide du portail d’administration, de l’interface CLI ou de PowerShell. 

Pour surveiller et modifier les paramètres de capacité :

1. Sélectionnez la vue **Capacité** à gauche. Vous verrez l’état de la capacité, y compris le nombre de machines virtuelles approvisionnées dans un cluster IoT Hub, ainsi que leur utilisation des ressources. Le nombre de nœuds affichés correspond au nombre de nœuds actuellement alloués à IoT Hub. 

2. Pour augmenter la capacité, sélectionnez le nom du cluster IoT Hub, modifiez le nombre de nœuds, puis sélectionnez **Mettre à jour l'échelle**. Vous pouvez augmenter la capacité en ajoutant autant de machines virtuelles que les ressources disponibles le permettent.

   > [!IMPORTANT]
   > Seule l’opération de scale-out de cluster IoT Hub (de la plus petite vers la plus grande taille) est prise en charge dans le cadre de la préversion. L’opération de scale-in (de la plus grande vers la plus petite taille) sera prise en charge dans la version en disponibilité générale d’IoT Hub.

   [![tableau de bord iot hub - capacité](media\iot-hub-rp-manage\dashboard-rp-iot-hub-capacity.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-capacity.png#lightbox)


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur :

la fonctionnalité de supervision Azure Stack Hub, notamment la génération d’alertes, consultez [Surveiller l’intégrité et les alertes](azure-stack-monitor-health.md).

la collecte des journaux Azure Stack Hub, consultez [Vue d’ensemble de la collecte automatique des journaux de diagnostic Azure Stack](./diagnostic-log-collection.md).