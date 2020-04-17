---
title: Modèle permettant d'effectuer l'apprentissage d'un modèle Machine Learning (ML) en périphérie à l'aide d'Azure et d'Azure Stack Hub.
description: Apprenez à utiliser les services Azure et Azure Stack Hub pour effectuer l'apprentissage d'un modèle ML en périphérie.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 4df466a3b1b1e89be704302153e9e835f504445e
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77688863"
---
# <a name="train-machine-learning-ml-model-at-the-edge-pattern"></a>Effectuer l'apprentissage d'un modèle Machine Learning (ML) en périphérie

Générez des modèles ML portables à partir de données uniquement disponibles localement.

## <a name="context-and-problem"></a>Contexte et problème

De nombreuses organisations aimeraient pouvoir extraire des insights de leurs données locales ou héritées à l'aide d'outils que leurs équipes de scientifiques des données comprennent. [Azure Machine Learning](/azure/machine-learning/) fournit des outils cloud natifs pour entraîner, régler et déployer des modèles ML et Deep Learning.  

Cependant, certaines données sont trop volumineuses pour être envoyées dans le cloud, ou ne peuvent l'être pour des raisons réglementaires. Grâce à ce modèle, les scientifiques des données peuvent utiliser Azure Machine Learning pour effectuer l'apprentissage de modèles à l'aide de données et de calculs locaux. 

## <a name="solution"></a>Solution

Le modèle d'apprentissage en périphérie utilise une machine virtuelle exécutée sur Azure Stack Hub. La machine virtuelle est enregistrée en tant que cible de calcul dans Azure ML, ce qui lui permet d'accéder aux données uniquement disponibles localement. Dans ce cas, les données sont stockées dans le stockage blob d'Azure Stack Hub. 

Au terme de son apprentissage, le modèle est enregistré auprès d'Azure ML, conteneurisé et ajouté à une instance d'Azure Container Registry à des fins de déploiement. Pour cette itération du modèle, la machine virtuelle d'apprentissage Azure Stack Hub doit être accessible sur l'Internet public. 

[![architecture d'apprentissage d'un modèle ml en périphérie](media/pattern-train-ml-model-at-edge/solution-architecture.png)](media/pattern-train-ml-model-at-edge/solution-architecture.png)

Le fonctionnement de la solution est le suivant : 

1. La machine virtuelle Azure Stack Hub est déployée et enregistrée en tant que cible de calcul auprès d'Azure ML.
2. Une expérience est créée dans Azure ML, avec la machine virtuelle Azure Stack Hub comme cible de calcul.
3. Au terme de son apprentissage, le modèle est enregistré et conteneurisé.
4. Le modèle peut maintenant être déployé localement ou dans le cloud.

## <a name="components"></a>Components

Cette solution utilise les composants suivants :

| Couche | Composant | Description |
|----------|-----------|-------------|
| Azure | Azure Machine Learning | [Azure Machine Learning](/azure/machine-learning/) orchestre l'apprentissage du modèle ML. |
| | Azure Container Registry | Azure ML empaquette le modèle dans un conteneur et le stocke dans une instance d'[Azure Container Registry](/azure/container-registry/) à des fins de déploiement.|
| Azure Stack Hub | App Service | [Azure Stack Hub avec App Service](/azure-stack/operator/azure-stack-app-service-overview) fournit une base aux composants situés en périphérie. |
| | Calcul | Une machine virtuelle Azure Stack Hub exécutant Ubuntu avec Docker est utilisée pour effectuer l'apprentissage du modèle ML. |
| | Stockage | Les données privées peuvent être hébergées dans le stockage blob d'Azure Stack Hub. |

## <a name="issues-and-considerations"></a>Problèmes et considérations

Prenez en compte des points suivants lors du choix de l'implémentation de cette solution :

### <a name="scalability"></a>Extensibilité 

Pour la mise à l'échelle de cette solution en vue de l'apprentissage, vous devez créer une machine virtuelle de taille appropriée sur Azure Stack Hub.

### <a name="availability"></a>Disponibilité

Assurez-vous que les scripts d'apprentissage et les machines virtuelles Azure Stack Hub ont accès aux données locales utilisées pour l'apprentissage.

### <a name="manageability"></a>Simplicité de gestion

Assurez-vous que les modèles et les expériences sont correctement enregistrés, versionnés et étiquetés afin d'éviter toute confusion lors du déploiement du modèle. 

### <a name="security"></a>Sécurité

Ce modèle permet à Azure ML d'accéder localement à d'éventuelles données sensibles. Assurez-vous que le compte utilisé pour la connexion SSH à la machine virtuelle Azure Stack Hub dispose d'un mot de passe fort, et que les scripts d'apprentissage ne conservent pas de données et n'en chargent pas dans le cloud. 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les sujets abordés dans cet article :
- Consultez la [documentation Azure Machine Learning](/azure/machine-learning) pour bénéficier d'une vue d'ensemble sur le Machine Learning et les sujets connexes.
- Consultez [Azure Container Registry](/azure/container-registry/) pour apprendre à créer, stocker et gérer des images pour les déploiements de conteneurs.
- Consultez [App Service sur Azure Stack Hub](/azure-stack/operator/azure-stack-app-service-overview) pour en savoir plus sur le fournisseur de ressources et son déploiement.
- Consultez [Considérations relatives à la conception des applications hybrides](overview-app-design-considerations.md) pour en savoir plus sur les meilleures pratiques et obtenir des réponses à d'autres questions.
- Consultez [Famille de produits et de solutions Azure Stack](/azure-stack) pour en savoir plus sur l'ensemble du portefeuille de produits et de solutions.

Lorsque vous êtes prêt à tester l'exemple de solution, poursuivez avec le [Guide de déploiement consacré à l'apprentissage d'un modèle ML en périphérie](https://aka.ms/edgetrainingdeploy). Ce guide de déploiement fournit des instructions pas à pas sur le déploiement et sur le test de ses composants.