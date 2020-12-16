---
title: Annexe de la gestion de réseau Azure Stack Hub renforcé
description: Annexes de la gestion de réseau Azure Stack Hub renforcé.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/14/2020
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 2a5acdf50a310fe2bda89e4472c38af2c75f9487
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96940973"
---
# <a name="azure-stack-hub-ruggedized-network-appendix"></a>Annexe du réseau Azure Stack Hub renforcé

L’annexe fournit des informations relatives aux paramètres et aux identités des appareils pour du matériel Azure Stack Hub renforcé.

## <a name="technical-device-parameters"></a>Paramètres de l’appareil technique

### <a name="dell-s5048f-on"></a>Dell S5048F-ON

Ports : 

- 48 ports SFP28 25 Gigabit Ethernet. 
- Six ports QSFP28 100 Gigabit Ethernet. 
- Un port de console/gestion RJ45 avec signalisation RS232.
- Un port de console optionnel Micro USB type B. 
- Un port Ethernet 10/100/1000 Base-T utilisé en tant que port de gestion. 
- Un port USB type A pour le stockage de masse externe. 

Spécifications :

- Taille : 1 RU, 1,72" (h) x 17,1" (l) x 18" (p) - (4,4 (h) x 43,4 (l) x 45,7 (p) cm). 
- Poids : 22 lb (9,98 kg) 
- Niveau de pression acoustique pondéré ISO 7779 A : 59,6 dBA à 23 °C (73,4 °F).  
- Alimentation : 100-240 V CA 50/60 Hz. 
- Puissance thermique maximale : 1 956 BTU/h. 
- Nombre maximal de tirages actuels par système : 
  - 5,73 A/4,8 A à 100/120 V en courant alternatif (CA). 
  - 2,87 A/2,4 A à 200/240 V AC. 
- Consommation d’énergie maximale : 573 watts (CA). 
- Consommation énergétique normale : 288 watts (CA) avec tous les modules optiques chargés.  
- Nombre maximal de spécifications de fonctionnement : 
  - Température de fonctionnement : de 32 °F à 113 °F (de 0 °C à 45 °C). 
  - Humidité de fonctionnement : de 10 % à 90 % d’humidité relative, sans condensation. 
  - Conformité avec la fraîcheur de l’air jusqu’à 45 °C. 
- Nombre maximal de spécifications de non-fonctionnement : 
  - Température de stockage : de -40 °F à 158 °F (de –40 °C à 70 °C). 
  - Humidité du stockage : de 5 % à 95 % d’humidité relative, sans condensation.

### <a name="dell-3048-on"></a>Dell 3048-ON

Ports :

- 48 ports 1000BASE-T.  
- Quatre ports SFP+ 10 GbE. 
- Un port de console/gestion RJ45 avec signalisation RS232.  

Spécifications :

- Taille : Un RU, 1,71" (h) x 17,09" (l) x 12,6" (p) - (4,4 (h) x 43,4 (l) x 32 (p) cm).  
- Poids : 12,8 lb (5,84 kg) avec un bloc d’alimentation, 14,8 lb (6,74 kg) avec deux blocs d’alimentation. 
- Niveau de pression acoustique pondéré ISO 7779 A : \<36 dBA à 78,8 °F (26 °C). 
- Alimentation : 90-264 V CA 50/60 Hz. 
  1) Flux d’air vers l’avant CA.  
  2) Flux d’air inversé CA. 
- Puissance thermique maximale : 290 BTU/h. 
- Nombre maximal de tirages actuels par système :  
  - \<1 A à 100/120 V CA. 
  - \<0,5 A à 200/240 V AC.  
- Consommation d’énergie maximale : 87 W. 
- Consommation énergétique normale : 65 watts. 
- Nombre maximal de spécifications de fonctionnement :  
  - Température de fonctionnement : de 32 °F à 113 °F (de 0 °C à 45 °C).  
  - Humidité de fonctionnement : de 5 % à 85 % d’humidité relative, sans condensation.    
  - Altitude de fonctionnement : de 0 à 10 000 pieds (3 048 m) au-dessus du niveau de la mer.  
- Nombre maximal de spécifications de non-fonctionnement : 
  - Température de stockage : de -40 °F à 158 °F (de –40 °C à 70 °C). 
  - Humidité du stockage : de 5 % à 95 % d’humidité relative, sans condensation.   

## <a name="identity"></a>Identité

Tous les périphériques réseau sont conçus pour fonctionner avec le système TACACS (Terminal Access Controller Access-Control System). TACACS fournit une expérience d’identité centralisée. Le serveur TACACS doit être fourni dans le cadre de la configuration initiale.

Notez que lorsque le serveur TACACS n’est pas disponible, les appareils reviennent aux identités locales répertoriées ci-dessous. Pendant un fonctionnement normal, ces comptes sont désactivés.

Les comptes d’utilisateur suivants se ferment par appareil :

| Appareil | Nom d’utilisateur | Mot de passe par défaut     |
|--------|----------|----------------------|
| Tor 1  | Root     | Attribué par le client |
| Tor 2  | Root     | Attribué par le client |
| BMC    | Root     | Attribué par le client |
