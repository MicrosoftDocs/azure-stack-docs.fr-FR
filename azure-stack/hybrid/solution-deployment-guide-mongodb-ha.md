---
title: Déployer une solution MongoDB hautement disponible sur Azure et Azure Stack Hub
description: Découvrez comment déployer une solution MongoDB hautement disponible sur Azure et Azure Stack Hub
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 627e6a6092a93161b2d6b216178b84ce056e36e4
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73640406"
---
# <a name="deploy-a-highly-available-mongodb-solution-to-azure-and-azure-stack-hub"></a>Déployer une solution MongoDB hautement disponible sur Azure et Azure Stack Hub

*S’applique à : Systèmes intégrés Azure Stack Hub*

Cet article vous guide dans le processus de déploiement automatisé d’un cluster MongoDB hautement disponible de base avec un site de récupération d’urgence dans deux environnements Azure Stack Hub. Pour en savoir plus sur MongoDB et la haute disponibilité, voir [Membres d’un jeu de réplicas](https://docs.mongodb.com/manual/core/replica-set-members/).

Dans cette solution, vous allez créer un exemple d’environnement pour :

> [!div class="checklist"]
> - Orchestrer un déploiement dans deux environnements Azure Stack Hub
> - Utiliser Docker pour minimiser les problèmes de dépendance avec des profils d’API Azure
> - Déployer un cluster MongoDB hautement disponible de base avec un site de récupération d’urgence


> [!Tip]  
> ![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack Hub est une extension d’Azure. Azure Stack Hub offre à votre environnement local l’agilité et l’innovation du cloud computing grâce au seul cloud hybride qui vous permette de créer et de déployer des applications hybrides en tout lieu.  
> 
> L’article [Design Considerations for Hybrid Applications](overview-app-design-considerations.md) se penche sur les fondements de la qualité logicielle (sélection élective, scalabilité, disponibilité, résilience, facilité de gestion et sécurité) en matière de conception, de déploiement et d’exploitation des applications hybrides. Les considérations de conception vous aident à optimiser la conception d’application hybride, en réduisant les risques dans les environnements de production.



## <a name="architecture-for-mongodb-with-azure-stack-hub"></a>Architecture pour MongoDB avec Azure Stack Hub

![MongoDB hautement disponible dans Azure Stack Hub](media/solution-deployment-guide-mongodb-ha/image1.png)

## <a name="prerequisites-for-mongodb-with-azure-stack-hub"></a>Conditions préalables pour MongoDB avec Azure Stack Hub

  - Deux systèmes intégrés Azure Stack Hub (Azure Stack Hub) connectés ; ce déploiement ne fonctionne pas sur les Kits de développement Azure Stack Hub (ASDK). Pour en savoir plus sur Azure Stack Hub, consultez [Qu’est-ce qu’Azure Stack Hub ?](https://azure.microsoft.com/overview/azure-stack/).
      - Un abonnement de locataire sur chaque infrastructure Azure Stack Hub.    
      - **Prenez note de chaque ID d’abonnement et du point de terminaison Azure Resource Manager de chaque infrastructure Azure Stack Hub.**
  - Un principal de service Azure Active Directory (Azure AD) disposant d’autorisations pour l’abonnement du locataire sur chaque infrastructure Azure Stack Hub. Vous devrez peut-être créer deux principaux de service si les infrastructures Azure Stack Hub sont déployées auprès de différents locataires Azure AD. Pour en savoir plus sur la création d’un principal de service pour Azure Stack Hub, consultez [Créer des principaux de service pour permettre à des applications d’accéder à des ressources Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).    
      - **Prenez note de l’ID d’application, de la clé secrète client et du nom de locataire de chaque principal de service (xxxxx.onmicrosoft.com).**
  - Ubuntu 16.04 syndiqué sur la Place de marché de chaque Azure Stack Hub. Pour en savoir plus sur la syndication de place de marché, consultez [Télécharger des éléments de la Place de marché vers Azure Stack Hub à partir d’Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
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

3.  Exécutez le déploiement. Fournissez les informations d’identification et les noms de ressources si nécessaire. HA fait référence à l’infrastructure Azure Stack Hub où le cluster hautement disponible sera déployé, et DR, à l’infrastructure Azure Stack Hub où le cluster de récupération d’urgence sera déployé.

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

5.  Les ressources hautement disponibles sont déployées en premier. Supervisez le déploiement et attendez qu’il se termine. Lorsque s’affiche le message indiquant que le déploiement haute disponibilité est terminé, vous pouvez consulter le portail de l’infrastructure Azure Stack Hub hautement disponible pour voir les ressources déployées. 

6.  Poursuivez avec le déploiement des ressources de récupération d’urgence, et décidez si vous souhaitez activer une machine virtuelle jumpbox sur l’infrastructure Azure Stack Hub de récupération d’urgence pour interagir avec le cluster.

7.  Attendez la fin du déploiement de la ressource de récupération d’urgence.

8.  Une fois le déploiement de la ressource de récupération d’urgence terminé, quittez le conteneur.

  ```powershell
  exit
  ```

## <a name="next-steps"></a>Étapes suivantes

  - Si vous avez activé la machine virtuelle jumpbox sur l’infrastructure Azure Stack Hub de récupération d’urgence, vous pouvez vous connecter via le protocole SSH et interagir avec le cluster MongoDB en installant l’interface de ligne de commande mongo. Pour en savoir plus sur l’interaction avec MongoDB, voir [Interpréteur de commandes mongo](https://docs.mongodb.com/manual/mongo/).

  - Pour en savoir plus sur les applications de cloud hybride, voir [Solutions de cloud hybride](https://aka.ms/azsdevtutorials).

  - Modifiez le code pour cet exemple sur [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
