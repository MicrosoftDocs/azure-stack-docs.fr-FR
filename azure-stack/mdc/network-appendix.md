---
title: Annexe de mise en réseau MDC (Modular Data Center)
description: Annexes pour la mise en réseau MDC.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 12/30/2019
ms.lastreviewed: 12/30/2019
ms.openlocfilehash: 10de658f7e5f8621d44baaa16f2181f58f47811e
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183391"
---
# <a name="mdc-network-appendix"></a>Annexe réseau MDC

L’annexe fournit des informations sur les paramètres et les identités des appareils pour le matériel MDC.

## <a name="technical-device-parameters"></a>Paramètres de l’appareil technique

### <a name="cisco-93360yc-fx2--cisco-9348gc-fxp"></a>Cisco 93360YC-FX2 et Cisco 9348GC-FXP

| **Fonctionnalité** | **Cisco Nexus 93360YC-FX2** | **Cisco Nexus 9348GC-FXP** |
|---|---|---|
| Ports | 96 ports x 1/10/25 Gbit/s et 12 ports 40/100 Gbit/s QSFP28 | 48 ports x 1-GBASE-T, 4 ports x 1/10/25-Gbit/s SFP28 et 2 ports x 40/100 QSFP28 |
| Vitesses prises en charge | 1/10/25-Gbits/s sur liaisons descendantes, 40/100-Gbits/s sur liaisons montantes, ports d’évasion pris en charge, 97-108 : 4x10/25G | vitesses de 100 Mbits/s et 1 Gbits/s |
| UC | Quatre cœurs | Quatre cœurs |
| Mémoire système | Jusqu’à 24 Go | 24 Go |
| Lecteur SSD | 128 Go | 128 Go |
| Mémoire tampon système | 40 Mo | 40 Mo |
| Ports de gestion | Deux ports : Un RJ-45 et un SFP+ | Deux ports : Un RJ-45 et un SFP+ |
| Ports USB | Une | Une |
| Ports série RS-232 | Une | Une |
| Alimentations (jusqu’à deux) | Courant alternatif 1 200 w (CA), 1 200 W HVAC/HVDC | 350 W CA, 440 W CC |
| Alimentation classique (CA) | 404 W | 178 W |
| Alimentation maximale (CA) | 900 W | 287 W |
| Tension en entrée (CA) | de 100 V à 240 V | de 100 V à 240 V |
| Tension en entrée (CA haute tension [HVAC]) | de 100 V à 277 V | de 90 V à 305 V |
| Tension en entrée (CC) | de -40 V à 72 V | de \-36 V à -72 V |
| Tension en entrée (CC haute tension [HVDC]) | de - 240 V à -380 V | de -192 V à 400 V |
| Fréquence (CA) | 50 Hz à 60 Hz | 50 Hz à 60 Hz |
| Ventilateurs | Trois plateaux de ventilateur | Trois |
| Ventilation | Admission et échappement côté port | Admission et échappement côté port |
| Dimensions physiques | 3,38 x 17,41 x 24,14 pouce (8,59 x 44,23 x 61,31 cm) | 1,72 x 17,3 x 19,7 pouce. |
| (H x L x P) | | (4,4 x 43,9 x 49,9 cm) |
| Acoustique | 76,7 dBA à 40 % de la vitesse du ventilateur, 88,7 dBA à 70 % de la vitesse du ventilateur et 97,4 dBA à 100 % de la vitesse du ventilateur | 67,5 dBA à 50 % de la vitesse du ventilateur, 73,2 dBA à 70 % de la vitesse du ventilateur et 81,6 dBA à 100 % de la vitesse du ventilateur |
| Conformité RoHS | Oui | Oui |
| MTBF | 320 040 heures | 257 860 heures |
| Image ACI minimale | ACI-N9KDK9-14.1.2 | ACI-N9KDK9-13.0 |
| Image NX-OS minimale | NXOS-9.3(1) | NXOS-703I7.1 |

### <a name="juniper-mx204"></a>Juniper MX204

|  |  |  |
|--|--|--|
| Layout | Capacité du système | 3 Tbits/s |
|        | Orientation d’emplacement | N/D |
|        | Montage | Avant ou centre |
| Spécification physique | Dimensions (L x H x P) | 17,45" x 8,71" x 24,5" (44,3 x 22,1 x 62,2 cm) |
|                        | Poids complètement chargé | 130 lb (59 kg) |
|                        | Poids déchargé | 52 lb (23,6 kg) |
| Moteur de routage | Mémoire par défaut | Stockage flash NOR 2x16 Mo ; 64 Go de RAM DDR4 ; SSD 2x50 Go |
|                | Nombre de mémoires à tores magnétiques | 6 cœurs |
| Redondance | Composants | Alimentation, RES, ventilateurs |
| Environnement | Écoulement de l’air | Côte à côte |
|               | Température de fonctionnement | de 0° à 46° C au niveau de la mer |
|               | Humidité de fonctionnement | de 5 % à 90 % |
|               | Altitude de fonctionnement | 10 000 ft (3 048 m) |
| Certifications | NEBS | • CEM GR-1089-Core et sécurité électrique |
|                | | • Réseau de liaison commun (CBN) |
|                | | • National Electrical Code (code national d'électricité NEC) |
|                | | • Protection physique GR-63-Core |

### <a name="dell-emc-s4148f-on"></a>Dell EMC S4148F-ON

Fonctionnalités :

- 48 ports 10 Gigabit Ethernet SFP+
- Six ports 40 Gigabit Ethernet QSFP+
- Une console/port de gestion RJ45 avec signalisation RS232 
- Un port de console RJ45 micro-USB-B 
- Un port Ethernet de gestion RJ45 10/100/1000Base-T 

Spécifications :

- Taille : 
  - 1 RU, 1,75 "(h) x 17" (l) x 18 "(p) (4,4 cm (h) x 43,1 cm (l) x 45,7 cm (p)) 
  - S4112 : 1,7"(h) x 8,28"(l) x 18"(p) (4,125 cm (h) x 20,9 cm (l) x 45 cm (p) 
- Alimentation : 100 - 50 V CA, 240 Hz
- Alimentation (CC), applicable à S4412 : nominal-40 VCC à-72 VDC
- Nombre maximal de tirages actuels par système : 6A/5A à 100/120 v CA ; 3A/2.5 A à 200/240 v CA
- S4112 : 2A/1,7 A à 100/120 v CA.; 1A/0,8 A à 200/240 v CA. 
- S4112 (CC) :-40V/5A,-48V/4,2 A,-72V/2,8 A 

Nombre maximal de spécifications de fonctionnement :
- Température de fonctionnement : de 5° à 40° C
- Humidité de fonctionnement : De 5 % à 85 % sans condensation 

Nombre maximal de spécifications de non-fonctionnement :
- Température de stockage : de 40 °C à 65 °C 
- Humidité du stockage : De 5 % à 95 % sans condensation


## <a name="identity"></a>Identité

Tous les périphériques réseau sont conçus pour fonctionner avec le système TACACS (Terminal Access Controller Access-Control System). TACACS fournit une expérience d’identité centralisée. Le serveur TACACS doit être fourni dans le cadre de la configuration initiale.

Notez que lorsque le serveur TACACS n’est pas disponible, les appareils reviennent aux identités locales répertoriées ci-dessous. Pendant un fonctionnement normal, ces comptes sont désactivés.


Les comptes d’utilisateur suivants se ferment par appareil :

| Appareil | Nom d’utilisateur | Mot de passe par défaut |
|---------|--------------|----------------------|
| Edge 1 | root | Attribué par le client |
| Edge 2 | Root | Attribué par le client |
| Tor 1 | Root | Attribué par le client |
| Tor 2 | Root | Attribué par le client |
| BMC | Root | Attribué par le client |
| PDU 1 | root | Attribué par le client |
| PDU 2 | root | Attribué par le client |
| Série | Root | Attribué par le client |
| Avocent | Racine et administrateur | Attribué par le client |
