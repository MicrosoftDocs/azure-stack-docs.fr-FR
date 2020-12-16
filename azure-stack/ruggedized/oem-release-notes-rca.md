---
title: Notes de publication OEM pour Azure Stack Hub renforcé
description: Notes de publication OEM pour Azure Stack Hub renforcé
author: sethmanheim
ms.topic: article
ms.date: 12/08/2020
ms.author: sethm
ms.reviewer: danlewi
ms.lastreviewed: 12/08/2020
ms.openlocfilehash: fac088f56dc7de04d8f78bfef1ab63c87b83f336
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96940970"
---
# <a name="azure-stack-hub-ruggedized-2008-oem-release-notes"></a>Notes de publication OEM pour Azure Stack Hub renforcé 2008

Cet article contient des informations de version et de version release pour Azure Stack Hub renforcé.

## <a name="overview"></a>Vue d’ensemble

Ce document décrit le contenu des mises à jour internes renforcées d’Azure Stack Hub renforcé pour le microprogramme et les pilotes. Cette mise à jour inclut les améliorations et les correctifs de la dernière version release d’Azure Stack Hub. Vous trouverez ci-dessous les liens de téléchargement :

* https://aka.ms/azshwpackage
* https://aka.ms/azshwmanifest

## <a name="baseline-and-document-history"></a>Base de référence et historique des documents

| Libérer | Date       | Description des changements         |
|---------|------------|--------------------------------|
| 2008    | 13/10/2020 | Mises à jour du package OEM 2.2.2010.5 |

## <a name="firmware"></a>Microprogramme

### <a name="bios"></a>Bios

| Version commerciale | Version du microprogramme | Modifications                                                                                                                                                                                                                                                                                                                                                                                                       |   |   |
|-----------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|
| 2008            | 2.8.2            | Correction d’un problème connu dans les versions du BIOS 2.6.4 à 2.8.1, où le système peut se réinitialiser durant la mise sous tension au moment où « Configuring Memory » (Configuration de la mémoire) s’affiche sur l’écran de démarrage. Le problème s’applique aux configurations de mémoire DDR4 et NVDIMM-N.<br><br>Améliorations apportées pour résoudre les failles de sécurité CVE (Common Vulnerabilities and Exposures), notamment les CVE-2020-0545, CVE-2020-0548 et CVE-2020-0549. |   |   |
| 2005            | 2.7.7            |                                                                                                                                                                                                                                                                                                                                                                                                               |   |   |
|                 |                  |                                                                                                                                                                                                                                                                                                                                                                                                               |   |   |

### <a name="idrac"></a>IDRAC

| Version commerciale | Version du microprogramme | Modifications                                                                                                                                                                                                                                                                          |   |   |
|-----------------|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|
| 2008            | 4.22.0.0         | 167411 : Correction d’un problème qui entraînait le redémarrage forcé du système au moment de la mise à jour du microprogramme via l’API Redfish.<br><br>155376 : Correction d’un problème qui entraînait le redémarrage d’iDRAC au moment de la collecte des journaux SupportAssist.<br><br>162778 : Correction d’un problème d’insuffisance de mémoire sur iDRAC en lien avec le service de console virtuelle. |   |   |
| 2005            | 4.10.10.10       |                                                                                                                                                                                                                                                                                  |   |   |
|                 |                  |                                                                                                                                                                                                                                                                                  |   |   |

### <a name="nic-azure-stack-nodes"></a>Cartes d’interface réseau - Nœuds Azure Stack

| Version commerciale    | Version du microprogramme    | Modifications                                                                                                          |
|--------------------|---------------------|------------------------------------------------------------------------------------------------------------------|
|     2008           |     14.27.60.08     | Correction d’une assertion de microprogramme irrécupérable indiquant un plantage IRISC, car init_hca est en attente de la libération du verrou de flux des minuteurs. |
|     2005           |     14.26.60.00     |                                                                                                                  |

### <a name="nic-hlh"></a>Cartes d’interface réseau - HLH

| Version commerciale    | Version du microprogramme    | Modifications                                                                                                                                                                   |
|--------------------|---------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     2008           |     19.5.12         | Résolution d’un problème sur les cartes réseau Intel(R) Ethernet X710 10 Go, où la LED du périphérique reste allumée après avoir clignoté alors qu’elle est définie en mode clignotant dans les paramètres de périphérique (F2). |
|     2005           |     19.0.12         |                                                                                                                                                                           |

### <a name="hba-azure-stack-node-capacity-drives"></a>HBA - Lecteurs de capacité de nœud Azure Stack

| Version commerciale    | Version du microprogramme    | Modifications    |
|--------------------|---------------------|------------|
|     2008           |     16.17.01.00     |            |
|     2005           |     16.17.00.05     |            |

### <a name="hba-hlh-capacity-drives"></a>HBA - Lecteurs de capacité HLH

|     Version commerciale |     Version du microprogramme |     Modifications                                                                                           |
|---------------------|----------------------|-------------------------------------------------------------------------------------------------------|
| 2005, 2008          | 25.5.7.0005          | Correction d’un problème où le contrôleur peut planter au démarrage en raison d’une configuration incomplète de la mémoire non volatile. |

### <a name="hba---boot-drives"></a>HBA - Lecteurs de démarrage

| Version commerciale | Version du microprogramme | Modifications                                                                                   |
|-----------------|------------------|-------------------------------------------------------------------------------------------|
| 2005, 2008      | 2.5.13.3024      | Correction d’une divergence de PPID pour les lecteurs M.2 du contrôleur BOSS dans la page d’inventaire matériel d’iDRAC |

### <a name="cpld"></a>CPLD

| Version commerciale | Version du microprogramme | Modifications                                                                                                                                                                                                |
|-----------------|------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     2008        |     9.0.6        | Corrige un éventuel problème de plantage d’IDRAC durant la mise à jour du microprogramme.<br> Ajout d’un filtrage de bruit du signal pour éviter le signalement de fausses erreurs.<br> Modification du mappage de la mémoire de l’hôte pour empêcher une éventuelle désactivation du port USB avant. |
|     2005        |     1.0.6        |                                                                                                                                                                                                        |

### <a name="drive-fw"></a>Microprogramme de lecteur

| Version commerciale | Version du microprogramme | Modifications                                |
|-----------------|------------------|----------------------------------------|
| 2008            | S402             | Permet les futures mises à jour de microprogramme en ligne |
| 2005            | S401             |                                        |

## <a name="drivers"></a>Pilotes

### <a name="nic"></a>Carte d’interface réseau

| Version commerciale | Version du microprogramme | Modifications                                |
|-----------------|------------------|----------------------------------------|
| 2008            | 2.40.22511.0    |  |
| 2005            | 2.30.21713.0 |                                        |

### <a name="hba---capacity-drives"></a>HBA - Lecteurs de capacité

| Version commerciale | Version du microprogramme | Modifications |
|-----------------|------------------|---------|
|  2005, 2008   |  2.51.25.02  |         |

### <a name="hba---boot-drives"></a>HBA - Lecteurs de démarrage

| Version commerciale | Version du microprogramme | Modifications |
|-----------------|------------------|---------|
|  2005, 2008   |  1.2.0.1051 |         |

### <a name="intel-chipset"></a>Chipset Intel

| Version commerciale | Version du microprogramme | Modifications |
|-----------------|------------------|---------|
|  2005, 2008   | 10.1.18243.8188 |         |
