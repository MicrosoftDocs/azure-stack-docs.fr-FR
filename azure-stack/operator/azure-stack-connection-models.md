---
title: Modèles de connexion des systèmes intégrés Azure Stack | Microsoft Docs
description: Décidez des modèles de connexion à utiliser et prenez d’autres décisions relatives à la planification du déploiement pour les systèmes intégrés Azure Stack.
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 8646e9d1936c865482368d176194c095c9dd0483
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909414"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Modèles de connexion des systèmes intégrés Azure Stack
Si vous envisagez d’acheter un système intégré Azure Stack, vous devez prendre en considération [plusieurs points relatifs à l’intégration à un centre de données](azure-stack-datacenter-integration.md) pour le déploiement Azure Stack, afin de déterminer de quelle façon le système sera intégré à votre centre de données. En outre, vous devez définir comment vous allez intégrer Azure Stack à votre environnement cloud hybride. Cet article fournit une vue d’ensemble de ces décisions, y compris celles relatives aux modèles de connexion Azure, aux options du magasin d’identités et aux options du modèle de facturation.

Si vous décidez d’acheter un système intégré, votre fabricant OEM vous guidera tout au long du processus de planification. Le fabricant OEM effectue également le déploiement.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Choisir un modèle de connexion pour le déploiement Azure Stack
Vous pouvez choisir de déployer Azure Stack en le connectant ou non à internet (et à Azure). Pour tirer le meilleur parti d’Azure Stack, y compris les scénarios hybrides entre Azure Stack et Azure, effectuez un déploiement connecté à Azure. Ce choix détermine les options disponibles pour votre magasin d’identités (Azure Active Directory ou services de fédération Active Directory (AD FS)) et le modèle de facturation (facturation à l’utilisation ou selon la capacité), comme indiqué dans le schéma et le tableau suivants :

![Scénarios de facturation et de déploiement Azure Stack](media/azure-stack-connection-models/azure-stack-scenarios.png)
  
> [!IMPORTANT]
> Ce choix est déterminant. Vous devez sélectionner les services de fédération Active Directory (AD FS) ou Azure Active Directory (Azure AD) au moment du déploiement. Vous ne pourrez pas revenir en arrière sans avoir à redéployer l’intégralité du système.  


|Options|Déploiement connecté à Azure|Déploiement déconnecté d’Azure|
|-----|:-----:|:-----:|
|Azure AD|![Pris en charge](media/azure-stack-connection-models/check.png)| |
|AD FS|![Pris en charge](media/azure-stack-connection-models/check.png)|![Pris en charge](media/azure-stack-connection-models/check.png)|
|Facturation à l’utilisation|![Pris en charge](media/azure-stack-connection-models/check.png)| |
|Facturation selon la capacité|![Pris en charge](media/azure-stack-connection-models/check.png)|![Pris en charge](media/azure-stack-connection-models/check.png)|
|Gestion des licences| Accord Entreprise ou Fournisseur de solutions cloud | Contrat Entreprise |
|Correctifs et mises à jour|Le package de mise à jour peut être téléchargé directement à partir d’Internet sur Azure Stack |  Obligatoire<br><br>Nécessite aussi un support amovible<br> et un appareil connecté distinct |
| Inscription | Automatisé | Obligatoire<br><br>Nécessite aussi un support amovible<br> et un appareil connecté distinct |

Une fois que vous avez choisi le modèle de connexion Azure à utiliser pour le déploiement Azure Stack, vous devrez prendre d’autres décisions liées au modèle de connexion concernant le magasin d’identités et le mode de facturation.

## <a name="next-steps"></a>Étapes suivantes

[Décisions de déploiement Azure Stack connecté à Azure](azure-stack-connected-deployment.md)

[Décisions de déploiement Azure Stack non connecté à Azure](azure-stack-disconnected-deployment.md)
