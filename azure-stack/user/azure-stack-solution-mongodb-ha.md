---
title: Déployer une solution MongoDB hautement disponible sur Azure et Azure Stack | Microsoft Docs
description: Découvrez comment déployer une solution MongoDB hautement disponible sur Azure et Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 25be3914f58abad44b64870f9da94610a7498d52
ms.sourcegitcommit: 35b13ea6dc0221a15cd0840be796f4af5370ddaf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68603042"
---
# <a name="deploy-a-highly-available-mongodb-solution-to-azure-and-azure-stack"></a>Déployer une solution MongoDB hautement disponible sur Azure et Azure Stack

Cet article vous guide dans le processus de déploiement automatisé d’un cluster MongoDB hautement disponible de base avec un site de récupération d’urgence dans deux environnements Azure Stack. Pour en savoir plus sur MongoDB et la haute disponibilité, voir [Membres d’un jeu de réplicas](https://docs.mongodb.com/manual/core/replica-set-members/).

Dans cette solution, vous allez créer un exemple d’environnement pour :

> [!div class="checklist"]
> - Orchestrer un déploiement dans deux environnements Azure Stack
> - Utiliser Docker pour minimiser les problèmes de dépendance avec des profils d’API Azure
> - Déployer un cluster MongoDB hautement disponible de base avec un site de récupération d’urgence


> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack est une extension d’Azure. Azure Stack apporte l’agilité et l’innovation du cloud computing à votre environnement local, en activant le seul cloud hybride qui vous permet de créer et de déployer des applications hybrides en tout lieu.  
> 
> L’article [Design Considerations for Hybrid Applications](azure-stack-edge-pattern-overview.md) se penche sur les fondements de la qualité logicielle (sélection élective, scalabilité, disponibilité, résilience, facilité de gestion et sécurité) en matière de conception, de déploiement et d’exploitation des applications hybrides. Les considérations de conception vous aident à optimiser la conception d’application hybride, en réduisant les risques dans les environnements de production.



## <a name="architecture-for-mongodb-with-azure-stack"></a>Architecture pour MongoDB avec Azure Stack

![MongoDB hautement disponible dans Azure Stack](media/azure-stack-solution-mongdb-ha/image1.png)

## <a name="prerequisites-for-mongodb-with-azure-stack"></a>Conditions préalables pour MongoDB avec Azure Stack

  - Deux systèmes intégrés Azure Stack (Azure Stack) connectés ; ce déploiement ne fonctionne pas sur des Kits de développement Azure Stack (ASDK). Pour en savoir plus sur Azure Stack, voir [Qu’est-ce qu’Azure Stack ?](https://azure.microsoft.com/overview/azure-stack/).
      - Un abonnement de locataire sur chaque infrastructure Azure Stack.    
      - **Prenez note de chaque ID d’abonnement et du point de terminaison Azure Resource Manager pour chaque infrastructure Azure Stack.**
  - Un principal de service Azure Active Directory (Azure AD) disposant d’autorisations pour l’abonnement du locataire sur chaque infrastructure Azure Stack. Il se peut que vous deviez créer deux principaux de service si les infrastructures Azure Stack sont déployées auprès de différents locataires Azure AD. Pour découvrir comment créer un principal de service pour Azure Stack, voir [Créer des principaux de service pour permettre à des applications d’accéder à des ressources Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).    
      - **Prenez note de l’ID d’application, de la clé secrète client et du nom de locataire de chaque principal de service (xxxxx.onmicrosoft.com).**
  - Ubuntu 16.04 syndiqué sur la Place de marché de chaque Azure Stack. Pour en savoir plus sur la syndication de place de marché, voir [Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
  - [Docker pour Windows](https://docs.docker.com/docker-for-windows/) installé sur votre ordinateur local.

## <a name="get-the-docker-image"></a>Obtenir l’image Docker

Des images Docker pour chaque déploiement éliminent les problèmes de dépendance entre les différentes versions d’Azure PowerShell.
1.  Assurez-vous que Docker pour Windows utilise des conteneurs Windows.
2.  Exécutez la commande suivante dans une invite de commandes avec élévation de privilèges pour obtenir le conteneur Docker avec les scripts de déploiement.
```powershell  
docker pull intelligentedge/mongodb-hadr:1.0.0
```

## <a name="deploy-the-clusters"></a>Déployer les clusters

1.  Une fois l’image conteneur correctement extraite, démarrez l’image.

    ```powershell  
    docker run -it intelligentedge/mongodb-hadr:1.0.0 powershell
    ```

2.  Une fois le conteneur démarré, vous recevez un terminal PowerShell avec élévation de privilèges dans le conteneur. Modifiez les répertoires pour obtenir le script de déploiement.

    ```powershell  
    cd .\MongoHADRDemo\
    ```

3.  Exécutez le déploiement. Fournissez les informations d’identification et les noms de ressources si nécessaire. HA fait référence à l’infrastructure Azure Stack où sera déployé le cluster hautement disponible, et DR à l’infrastructure Azure Stack où le cluster de récupération d’urgence sera déployé.

    ```powershell
    .\Deploy-AzureResourceGroup.ps1 `
    -AzureStackApplicationId_HA "applicationIDforHAServicePrincipal" `
    -AzureStackApplicationSercet_HA "clientSecretforHAServicePrincipal" `
    -AADTenantName_HA "hatenantname.onmicrosoft.com" `
    -AzureStackResourceGroup_HA "haresourcegroupname" `
    -AzureStackArmEndpoint_HA "https://management.haazurestack.com" `
    -AzureStackSubscriptionId_HA "haSubscriptionId" `
    -AzureStackApplicationId_DR "applicationIDforDRServicePrincipal" `
    -AzureStackApplicationSercet_DR "ClientSecretforDRServicePrincipal" `
    -AADTenantName_DR "drtenantname.onmicrosoft.com" `
    -AzureStackResourceGroup_DR "drresourcegroupname" `
    -AzureStackArmEndpoint_DR "https://management.drazurestack.com" `
    -AzureStackSubscriptionId_DR "drSubscriptionId"
    ```

4.  Tapez `Y` pour autoriser l’installation du fournisseur NuGet qui déclenche l’installation des modules « 2018-03-01-hybrid » du profil d’API.

5.  Les ressources hautement disponibles sont déployées en premier. Supervisez le déploiement et attendez qu’il se termine. Lorsque s’affiche le message indiquant que le déploiement haute disponibilité est terminé, vous pouvez consulter le portail de l’infrastructure Azure Stack hautement disponible pour voir les ressources déployées. 

6.  Poursuivez avec le déploiement des ressources de récupération d’urgence, et décidez si vous souhaitez activer une machine virtuelle jumpbox sur l’infrastructure Azure Stack de récupération d’urgence pour interagir avec le cluster.

7.  Attendez la fin du déploiement de la ressource de récupération d’urgence.

8.  Une fois le déploiement de la ressource de récupération d’urgence terminé, quittez le conteneur.

  ```powershell
  exit
  ```

## <a name="next-steps"></a>Étapes suivantes

  - Si vous avez activé la machine virtuelle jumpbox sur l’infrastructure Azure Stack de récupération d’urgence, vous pouvez vous connecter via le protocole SSH et interagir avec le cluster MongoDB en installant l’interface de ligne de commande mongo. Pour en savoir plus sur l’interaction avec MongoDB, voir [Interpréteur de commandes mongo](https://docs.mongodb.com/manual/mongo/).

  - Pour en savoir plus sur les applications de cloud hybride, voir [Solutions de cloud hybride](https://aka.ms/azsdevtutorials).

  - Modifiez le code pour cet exemple sur [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
