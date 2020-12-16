---
title: Préparation du site Azure Stack Hub renforcé pour Azure Stack Hub | Microsoft Docs
description: Découvrez les spécifications relatives à la préparation d’un site pour une solution Azure Stack Hub renforcé.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: wamota
ms.reviewer: wamota
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 02e549d4cae9dc0c543d4a21c48e972231257ced
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941025"
---
# <a name="azure-stack-hub-ruggedized-site-readiness"></a>Préparation du site Azure Stack Hub renforcé

Cette rubrique décrit les critères d’environnement et les conditions d’alimentation des unités PDU (unités de distribution d’alimentation) pour Azure Stack Hub renforcé. 

>[!NOTE]
>Ces valeurs sont destinées uniquement à la planification des installations. Elles sont approximatives et basées sur une marge de prudence. Les conditions réelles peuvent varier.

## <a name="environmental-requirements"></a>Configuration requise de l’environnement

Le tableau ci-dessous liste les critères d’environnement à respecter pour une solution Azure Stack Hub renforcé dans la configuration suivante :

- Unité d’échelle de 14 To
- Tension d’entrée CA de 200 volts
- Température ambiante maximale de 35 °C

*Tableau 1. Configuration maximale/minimale et conditions environnementales*

| Object                         | Conditions à respecter pour Azure Stack Hub renforcé               |
|--------------------------------|--------------------------------|
|Température de fonctionnement           | Température de fonctionnement d’Azure Stack Hub renforcé (minimale et maximale) : de -32 °C (-25,6 °F) à 43 °C (109 °F).    |
|Humidité           | Stockage : de 5 % à 95 % d’humidité relative avec un point de rosée maximal de 33 °C (91 °F). L’atmosphère doit être en permanence sans condensation. <br> En fonctionnement : de 5 % à 85 % d’humidité relative avec un point de rosée maximal de 29 °C (84,2 °F).
|Connectivité physique           | Azure Stack Hub renforcé peut être connecté physiquement à l’aide des éléments suivants : <br>4 x 10 Go SR SFP+ <br>4 x 1000BASE-SX <br>4 x 1000BASE-T
|Alimentation électrique                     | Max : 4,981 kW. Moyenne : 4,391 kW<br> Connecteur d’entrée C13/C14<br> Entrée : 100-240 V 50/60 Hz

## <a name="pdu-power-drop-requirements"></a>Conditions d’alimentation nécessaires aux PDU

Le tableau suivant liste les sources d’alimentation nécessaires à Azure Stack Hub renforcé.

*Tableau 2. Nombre de sources d’alimentation nécessaires*

| Configuration  | Monophasée  | Delta triphasée |Étoile triphasée |
|----------------|---------------|-------------------|----------------|
|Max./min.        | 2             | 2                 | 2              |

Le système intégré Azure Stack vous permet d’utiliser différents types de connecteur PDU pour obtenir la meilleure intégration possible à votre centre de données. Le tableau ci-dessous liste les types de connecteur :

*Tableau 3. Options d’unités PDU et de connecteurs*

| Localisation     | Monophasée                                | Delta triphasée                                   | Étoile triphasée                                        |
|--------------|---------------------------------------------|-----------------------------------------------------|-----------------------------------------------------------|
|Amérique du Nord |- L630P<br>- L7-30P<br>- Russellstoll 3750DP |- Hubbell Pro CS8365L<br>- Russellstoll 9P54U2T/1100 |- Hubbell C530P6S<br>- ABL Sursum S52S0A<br>- Câbles volants |


