---
title: Tailles de machine virtuelle prises en charge dans Azure Stack Hub
description: Référence pour les tailles de machine virtuelle prises en charge dans Azure Stack Hub.
author: mattbriggs
ms.topic: reference
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 03/23/2020
ms.openlocfilehash: 01299f2ddd04e66145d781785097c74e5d129e48
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873756"
---
# <a name="vm-sizes-supported-in-azure-stack-hub"></a>Tailles de machine virtuelle prises en charge dans Azure Stack Hub

Cet article répertorie les tailles de machine virtuelle disponibles dans Azure Stack Hub. Cet article peut vous aider à choisir une machine virtuelle pour la prise en charge de votre solution Azure Stack Hub.

Sur Azure Stack Hub, les IOPS disque (opérations d’entrée/sortie par seconde) dépendent de la taille de la machine virtuelle et non du type de disque. Cela signifie que, pour une machine virtuelle Standard_Fs, quel que soit le type de disque choisi (SSD ou HDD), la limite d'IOPS est de 2 300 par disque de données supplémentaire. La limite d'IOPS imposée est un plafond (maximum possible) pour éviter les voisins bruyants. Elle ne garantit pas les IOPS obtenues avec une taille de machine virtuelle spécifique.

Les processeurs virtuels des machines virtuelles dépendent du nombre de cœurs présents dans chaque nœud. Par exemple, les systèmes dont le nombre de cœurs ou de processeurs logiques est inférieur à 64 ne pourront pas prendre en charge la taille de machine virtuelle Standard_F64s_v2.

## <a name="vm-general-purpose"></a>Usage général d’une machine virtuelle

Les tailles de machine virtuelle à usage général ont un ratio processeur/mémoire équilibré. Elles conviennent pour le test et le développement, les bases de données petites à moyennes et les serveurs web au trafic faible à moyen. Chaque disque de données est 2300 IOPS pour les tailles de machine virtuelle Premium, sauf la série De base A. Pour De base A, la taille de disque de données est de 500 IOPS.

### <a name="basic-a"></a>De base A

> [!NOTE]
> Les tailles de machine virtuelle *De base A* ne sont plus disponibles pour la [création de groupes identiques de machines virtuelles](../operator/azure-stack-compute-add-scalesets.md) dans le portail. Pour créer un groupe de machines virtuelles identiques avec cette taille, utilisez PowerShell ou un modèle.

|Taille - Taille\Nom |Processeurs virtuels     |Mémoire | Taille max. du disque temporaire | Débit de disque du système d’exploitation max. : (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Débit de disque de données max. (E/S par seconde) | Nombre max de cartes réseau |    
|-----------------|-----|---------|---------|-----|------|-----------|----|
|**0A0\Basic_A0**  |1    |768 Mo   | 20 Go   |300  | 300  |1 / 1x300  |2   |
|**A1\Basic_A1**  |1    |1,75 Go  | 40 Go   |300  | 300  |2 / 2x300  |2   |
|**A2\Basic_A2**  |2    |3,5 Go   | 60 Go   |300  | 300  |4 / 4x300  |2   |
|**A3\Basic_A3**  |4    |7 Go     | 120 Go  |300  | 300  |8 / 8x300  |2   |
|**A4\Basic_A4**  |8    |14 Go    | 240 Go  |300  | 300  |16 / 16X300 |2   |

### <a name="standard-a"></a>Standard A 
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max de cartes réseau |    
|----------------|--|------|----|----|----|-------|---------|
|**Standard_A0** |1 |0,768 |20  |500 |500 |1 x 500  |2 |
|**Standard_A1** |1 |1,75  |70  |500 |500 |2 x 500  |2 |
|**Standard_A2** |2 |3,5   |135 |500 |500 |4 x 500  |2 |
|**Standard_A3** |4 |7     |285 |500 |500 |8 x 500  |2 |
|**Standard_A4** |8 |14    |605 |500 |500 |16 x 500 |4 |
|**Standard_A5** |2 |14    |135 |500 |500 |4 x 500  |2 |
|**Standard_A6** |4 |28    |285 |500 |500 |8 x 500  |2 |
|**Standard_A7** |8 |56    |605 |500 |500 |16 x 500 |4 |

### <a name="av2-series"></a>Série Av2
*Nécessite Azure Stack Hub version 1804 ou ultérieure*

|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max de cartes réseau |
|-----------------|----|----|-----|-----|------|--------------|---------|
|**Standard_A1_v2**  |1   |2   |10   |500 |1 000  |2 / 2 x 500   |2 |
|**Standard_A2_v2**  |2   |4   |20   |500 |2000  |4 / 4 x 500   |2 |
|**Standard_A4_v2**  |4   |8   |40   |500 |4000  |8 / 8 x 500   |4 |
|**Standard_A8_v2**  |8   |16  |80   |500 |8000  |16 / 16 x 500 |8 |
|**Standard_A2m_v2** |2   |16  |20   |500 |2000  |4 / 4 x 500   |2 |
|**Standard_A4m_v2** |4   |32  |40   |500 |4000  |8 / 8 x 500   |4 |
|**Standard_A8m_v2** |8   |64  |80   |500 |8000  |16 / 16 x 500 |8 |

### <a name="d-series"></a>Série D
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max de cartes réseau |
|----------------|----|----|-----|----|------|------------|---------|
|**D1 standard** |1   |3,5 |50   |500 |3000  |4 / 4 x 500   |2 |
|**D2 standard** |2   |7   |100  |500 |6000  |8 / 8 x 500   |2 |
|**D3 standard** |4   |14  |200  |500 |12 000 |16 / 16 x 500 |4 |
|**D4 standard** |8   |28  |400  |500 |24 000 |32 / 32 x 500 |8 |


### <a name="ds-series"></a>Série DS
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max de cartes réseau |
|-----------------|----|----|-----|-----|------|-------------|---------|
|**Standard_DS1** |1   |3,5 |7    |1 000 |4000  |4 / 4x2 300   |2 |
|**Standard_DS2** |2   |7   |14   |1 000 |8000  |8 / 8x2 300   |2 |
|**Standard_DS3** |4   |14  |28   |1 000 |16000 |16 / 16x2 300 |4 |
|**Standard_DS4** |8   |28  |56   |1 000 |32000 |32 / 32x2 300 |8 |

### <a name="dv2-series"></a>Série Dv2
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max de cartes réseau |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3,5 |50   |500 |3000  |4 / 4 x 500   |2 |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8 / 8 x 500   |2 |
|**Standard_D3_v2** |4   |14  |200  |500 |12 000 |16 / 16 x 500 |4 |
|**Standard_D4_v2** |8   |28  |400  |500 |24 000 |32 / 32 x 500 |8 |
|**Standard_D5_v2** |16  |56  |800  |500 |48 000 |64 / 64 x 500 |8 |

### <a name="dsv2-series"></a>Séries DSv2
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max de cartes réseau |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3,5 |7   |1 000 |4000  |4 / 4x2 300   |2 |
|**Standard_DS2_v2** |2   |7   |14  |1 000 |8000  |8 / 8x2 300   |2 |
|**Standard_DS3_v2** |4   |14  |28  |1 000 |16000 |16 / 16x2 300 |4 |
|**Standard_DS4_v2** |8   |28  |56  |1 000 |32000 |32 / 32x2 300 |8 |
|**Standard_DS5_v2** |16  |56  |112 |1 000 |64 000 |64 / 64x2 300 |8 |


## <a name="compute-optimized"></a>Optimisé pour le calcul
### <a name="f-series"></a>Série F
*Nécessite Azure Stack Hub version 1804 ou ultérieure*

|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max de cartes réseau |
|-----------------|----|----|-----|----|------|------------|---------|
|**Standard_F1**  |1   |2   |16   |500 |3000  |4 / 4 x 500   |2 |
|**Standard_F2**  |2   |4   |32   |500 |6000  |8 / 8 x 500   |2 |
|**Standard_F4**  |4   |8   |64   |500 |12 000 |16 / 16 x 500 |4 |
|**Standard_F8**  |8   |16  |128  |500 |24 000 |32 / 32 x 500 |8 |
|**Standard_F16** |16  |32  |256  |500 |48 000 |64 / 64 x 500 |8 |


### <a name="fs-series"></a>Série Fs
*Nécessite Azure Stack Hub version 1804 ou ultérieure*  

|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max de cartes réseau |
|------------------|----|----|----|-----|------|-------------|---------|
|**Standard_F1s**  |1   |2   |4   |1 000 |4000  |4 / 4x2 300   |2 |
|**Standard_F2s**  |2   |4   |8   |1 000 |8000  |8 / 8x2 300   |2 |
|**Standard_F4s**  |4   |8   |16  |1 000 |16000 |16 / 16x2 300 |4 |
|**Standard_F8s**  |8   |16  |32  |1 000 |32000 |32 / 32x2 300 |8 |
|**Standard_F16s** |16  |32  |64  |1 000 |64 000 |64 / 64x2 300 |8 |


### <a name="fsv2-series"></a>Série Fsv2
*Nécessite Azure Stack Hub version 1804 ou ultérieure* 

|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max de cartes réseau |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_F2s_v2**  |2   |4   |16   |1 000 |4000   |4 / 4x2 300    |2 |
|**Standard_F4s_v2**  |4   |8   |32   |1 000 |8000   |8 / 8x2 300    |2 |
|**Standard_F8s_v2**  |8   |16  |64   |1 000 |16000  |16 / 16x2 300  |4 |
|**Standard_F16s_v2** |16  |32  |128  |1 000 |32000  |32 / 32x2 300  |4 |
|**Standard_F32s_v2** |32  |64  |256  |1 000 |64 000  |32 / 32x2 300  |8 |
|**Standard_F64s_v2** |64  |128 |512  |1 000 |128000 |32 / 32x2 300  |8 |


## <a name="memory-optimized"></a>Mémoire optimisée

Les tailles de machine virtuelle à mémoire optimisée offrent un ratio mémoire/processeur supérieur pour les serveurs de base de données relationnelle, les caches moyens à grands et l’analytique en mémoire.

### <a name="d-series"></a><a name="mo-d"></a>Série D
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max de cartes réseau |
|------------------|---|----|----|--------|------|------------|---------|
|**D11 standard**  |2  |14  |100 |500     |6000  |8 / 8 x 500   |2 |
|**D12 standard**  |4  |28  |200 |500     |12 000 |16 / 16 x 500 |4 |
|**D13 standard**  |8  |56  |400 |500     |24 000 |32 / 32 x 500 |8 |
|**D14 standard**  |16 |112 |800 |500     |48 000 |64 / 64 x 500 |8 |

### <a name="ds-series"></a><a name="mo-ds"></a>Série DS
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max de cartes réseau |
|-------------------|---|----|----|--------|------|-------------|---------|
|**Standard_DS11**  |2  |14  |28  |1 000    |8000  |8 / 8x2 300   |2 |
|**Standard_DS12**  |4  |28  |56  |1 000    |12 000 |16 / 16x2 300 |4 |
|**Standard_DS13**  |8  |56  |112 |1 000    |32000 |32 / 32x2 300 |8 |
|**Standard_DS14**  |16 |112 |224 |1 000    |64 000 |64 / 64x2 300 |8 |

### <a name="dv2-series"></a><a name="mo-dv2"></a>Série Dv2
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max de cartes réseau |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8 / 8 x 500    |2 |
|**Standard_D12_v2** |4   |28  |200  |500 |12 000  |16 / 16 x 500  |4 |
|**Standard_D13_v2** |8   |56  |400  |500 |24 000  |32 / 32 x 500  |8 |
|**Standard_D14_v2** |16  |112 |800  |500 |48 000  |64 / 64 x 500  |8 |


### <a name="dsv2-series"></a><a name="mo-dsv2"></a>Séries DSv2
|Taille     |Processeurs virtuels     |Mémoire (Gio) | Stockage temporaire (Gio)  | Débit de disque du système d’exploitation max. (E/S par seconde) | Débit de stockage temporaire max. (E/S par seconde) | Disques de données max. / débit (E/S par seconde) | Nombre max de cartes réseau |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1 000 |8000   |4 / 4x2 300    |2 |
|**Standard_DS12_v2** |4   |28  |56   |1 000 |16000  |8 / 8x2 300    |4 |
|**Standard_DS13_v2** |8   |56  |112  |1 000 |32000  |16 / 16x2 300  |8 |
|**Standard_DS14_v2** |16  |112 |224  |1 000 |64 000  |32 / 32x2 300  |8 |


## <a name="next-steps"></a>Étapes suivantes

[Fonctionnalités des machines virtuelles Azure Stack Hub](azure-stack-vm-considerations.md)
