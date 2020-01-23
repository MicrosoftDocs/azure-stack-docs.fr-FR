---
title: Modèles de connexion des systèmes intégrés d’Azure Stack Hub | Microsoft Docs
description: Décidez des modèles de connexion à utiliser et d’autres aspect de la planification du déploiement pour les systèmes intégrés d’Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 53057c5235422f7d5ec016fcd8f9f3286c41dc58
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76534071"
---
# <a name="azure-stack-hub-integrated-systems-connection-models"></a>Modèles de connexion des systèmes intégrés d’Azure Stack Hub
Si vous envisagez d’acheter un système intégré Azure Stack Hub, vous devez prendre en considération [plusieurs aspects relatifs à l’intégration à un centre de données](azure-stack-datacenter-integration.md) pour le déploiement d’Azure Stack Hub, afin de déterminer de quelle façon le système sera intégré dans votre centre de données. Vous devez également définir comment vous allez intégrer Azure Stack Hub dans votre environnement cloud hybride. Cet article fournit une vue d’ensemble de ces décisions, y compris celles relatives aux modèles de connexion Azure, aux options du magasin d’identités et aux options du modèle de facturation.

Si vous décidez d’acheter un système intégré, votre fabricant OEM vous guidera tout au long du processus de planification. Le fabricant OEM effectue également le déploiement.

## <a name="choose-an-azure-stack-hub-deployment-connection-model"></a>Choisir un modèle de connexion pour le déploiement d’Azure Stack Hub
Vous pouvez choisir de déployer Azure Stack Hub connecté à Internet (et à Azure) ou déconnecté d’Internet. Pour tirer le meilleur parti d’Azure Stack Hub, y compris de scénarios hybrides entre Azure Stack Hub et Azure, effectuez un déploiement connecté à Azure. Ce choix détermine les options disponibles pour votre magasin d’identités (Azure Active Directory ou services de fédération Active Directory (AD FS)) et le modèle de facturation (facturation à l’utilisation ou selon la capacité), comme indiqué dans le schéma et le tableau suivants :

![Scénarios de facturation et de déploiement d’Azure Stack Hub](media/azure-stack-connection-models/azure-stack-scenarios.png)
  
> [!IMPORTANT]
> Ce choix est déterminant. Vous devez sélectionner les services de fédération Active Directory (AD FS) ou Azure Active Directory (Azure AD) au moment du déploiement. Vous ne pourrez pas revenir en arrière sans avoir à redéployer l’intégralité du système.  


|Options|Déploiement connecté à Azure|Déploiement déconnecté d’Azure|
|-----|:-----:|:-----:|
|Azure AD|![Prise en charge](media/azure-stack-connection-models/check.png)| |
|AD FS|![Prise en charge](media/azure-stack-connection-models/check.png)|![Prise en charge](media/azure-stack-connection-models/check.png)|
|Facturation à l’utilisation|![Prise en charge](media/azure-stack-connection-models/check.png)| |
|Facturation selon la capacité|![Prise en charge](media/azure-stack-connection-models/check.png)|![Prise en charge](media/azure-stack-connection-models/check.png)|
|Licence| Accord Entreprise ou Fournisseur de solutions cloud | Contrat Entreprise |
|Correctifs et mises à jour|Le package de mise à jour peut être téléchargé directement à partir d’Internet sur Azure Stack Hub |  Obligatoire<br><br>Nécessite aussi un support amovible<br> et un appareil connecté distinct |
| Inscription | Automatisé | Obligatoire<br><br>Nécessite aussi un support amovible<br> et un appareil connecté distinct |

Une fois que vous avez choisi le modèle de connexion Azure à utiliser pour le déploiement d’Azure Stack Hub, vous devrez prendre d’autres décisions liées à la connexion pour le magasin d’identités et le mode de facturation.

## <a name="next-steps"></a>Étapes suivantes

[Décisions de déploiement d’Azure Stack Hub connecté à Azure](azure-stack-connected-deployment.md)

[Décisions de déploiement d’Azure Stack Hub déconnecté d’Azure](azure-stack-disconnected-deployment.md)
