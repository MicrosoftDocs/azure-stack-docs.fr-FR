---
title: Modèle d'implémentation de la détection des ruptures de stock en périphérie à l'aide d'Azure et d'Azure Stack Edge.
description: Découvrez comment utiliser les services Azure et Azure Stack Edge pour implémenter la détection des ruptures de stock.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: fb4eb410eefb8a34bd869543a191a1b044a42f72
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73640094"
---
# <a name="out-of-stock-detection-at-the-edge-pattern"></a>Détection des ruptures de stock en périphérie

Ce modèle explique comment déterminer les articles en rupture de stock à l'aide d'un appareil ou de caméras réseau Azure Stack Edge ou Azure IoT Edge.

## <a name="context-and-problem"></a>Contexte et problème

Les magasins de détail physiques perdent des ventes lorsque leurs clients recherchent des articles non présents en rayon. Il arrive cependant que ces articles soient disponibles en réserve, mais n'ont tout simplement pas été remis en rayon. Les magasins entendent utiliser leur personnel plus efficacement et être systématiquement avertis lorsque des articles doivent être remis en rayon.

## <a name="solution"></a>Solution

L’exemple de solution utilise un appareil de périmètre, tel qu’Azure Stack Edge dans chaque magasin, pour traiter efficacement les données issues des caméras des magasins. Cette conception optimisée permet aux magasins d’envoyer uniquement les événements et images pertinents dans le cloud. Une telle conception économise la bande passante, l’espace de stockage et garantit la confidentialité des clients. Lorsqu'une image est lue à partir de chaque caméra, un modèle ML la traite et renvoie les zones en rupture de stock. L’image et les zones en rupture de stock s’affichent sur une application web locale. Ces données peuvent être envoyées vers un environnement Time Series Insight pour afficher des insights dans Power BI.

![rupture de stock au niveau de l'architecture de solution en périphérie](media/pattern-out-of-stock-at-edge/solution-architecture.png)

La solution fonctionne comme suit :
1. Les images sont capturées à partir d’une caméra réseau via HTTP ou RTSP.
2. Les images sont redimensionnées et envoyées au pilote d’inférence, qui communique avec le modèle ML pour déterminer s’il existe des images de rupture de stock.
3. Le modèle ML renvoie toutes les zones de rupture de stock.
4. Le pilote d’inférence charge l’image brute dans un objet blob (si spécifié) et envoie les résultats du modèle vers Azure IoT Hub et un processeur de cadre englobant sur l’appareil.
5. Le processeur de cadre englobant ajoute des cadres englobants aux images et met en cache le chemin d’accès de ces images dans une base de données en mémoire.
6. L’application web interroge les images et les affiche dans l’ordre reçu.
7. Les messages d'IoT Hub sont agrégés dans Time Series Insights.
8. Power BI affiche un rapport interactif sur les éléments en rupture de stock avec les données issues de Time Series Insights.


## <a name="components"></a>Composants

Cette solution utilise les composants suivants :

| Couche | Composant | Description |
|----------|-----------|-------------|
| Matériel local | Caméra réseau | Une caméra réseau avec flux HTTP ou RTSP est requise afin de fournir les images pour l’inférence. |
| Azure | Azure IoT Hub | [Azure IoT Hub](/azure/iot-hub/) gère le provisionnement des appareils et la messagerie pour les appareils de périmètre. |
|  | Azure Time Series Insights | [Azure Time Series Insights](/azure/time-series-insights/) stocke les messages issus d'IoT Hub à des fins de visualisation. |
|  | Power BI | [Microsoft Power BI](https://powerbi.microsoft.com/) fournit des rapports professionnels sur les événements de rupture de stock. Power BI fournit une interface de tableau de bord conviviale pour visualiser les résultats issus d’Azure Stream Analytics. |
| Appareil Azure Stack Edge ou<br>Azure IoT Edge | Azure IoT Edge | [Azure IoT Edge](/azure/iot-edge/) orchestre le runtime des conteneurs locaux et gère les appareils, ainsi que les mises à jour.|
| | Project Brainwave Azure | Sur un appareil Azure Stack Edge, [Project Brainwave](https://blogs.microsoft.com/ai/build-2018-project-brainwave/) utilise des FPGA (Field Programmable Gate Arrays) pour accélérer l’inférence ML.|

## <a name="issues-and-considerations"></a>Problèmes et considérations

Prenez en compte des points suivants lors du choix de l'implémentation de cette solution :

### <a name="scalability"></a>Extensibilité 

La plupart des modèles de Machine Learning s'exécutent uniquement à un certain nombre d’images par seconde, en fonction du matériel fourni. Déterminez le taux d’échantillonnage optimal à partir de vos caméras pour veiller à ce que le pipeline ML n'effectue pas de sauvegarde. Différents types de matériel gèrent différents nombres de caméras et fréquences d’images.

### <a name="availability"></a>Disponibilité

Il est important d'anticiper ce qui peut se produire si l’appareil de périmètre perd toute connectivité. Réfléchissez aux données susceptibles d'être perdues depuis le tableau de bord Time Series Insights et Power BI. L’exemple de solution fourni n'a pas vocation à être hautement disponible.

### <a name="manageability"></a>Facilité de gestion

Cette solution peut couvrir de nombreux appareils et emplacements, et donc devenir difficile à gérer. Les services IoT d'Azure peuvent être utilisés pour mettre automatiquement en ligne de nouveaux emplacements et appareils et les maintenir à jour. Des procédures de gouvernance des données appropriées doivent également être suivies.

### <a name="security"></a>Sécurité

Ce modèle gère les données potentiellement sensibles. Assurez-vous que les clés font régulièrement l'objet d'une rotation, et que les autorisations relatives au compte de stockage Azure et aux partages locaux sont correctement définies. 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les sujets abordés dans cet article :
- Plusieurs services IoT connexes sont utilisés dans ce modèle, notamment [Azure IoT Edge](/azure/iot-edge/), [Azure IoT Hub](/azure/iot-hub/) et [Azure Time Series Insights](/azure/time-series-insights/).
- Pour en savoir plus sur Microsoft Project Brainwave, consultez [l’annonce du blog](https://blogs.microsoft.com/ai/build-2018-project-brainwave/) ainsi que la [vidéo sur le Machine Learning accéléré par Azure avec Project Brainwave](https://www.youtube.com/watch?v=DJfMobMjCX0).
- Consultez [Considérations relatives à la conception des applications hybrides](overview-app-design-considerations.md) pour en savoir plus sur les meilleures pratiques et obtenir des réponses à d'autres questions.
- Consultez [Famille de produits et de solutions Azure Stack](/azure-stack) pour en savoir plus sur l'ensemble du portefeuille de produits et de solutions.

Lorsque vous êtes prêt à tester l’exemple de solution, poursuivez avec le [Guide de déploiement d’une solution de données hiérarchisées pour analytique](https://aka.ms/edgeinferencingdeploy). Ce guide de déploiement fournit des instructions pas à pas sur le déploiement et sur le test de ses composants.