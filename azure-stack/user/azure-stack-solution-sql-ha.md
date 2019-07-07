---
title: Déployer un groupe de disponibilité SQL Server 2016 sur Azure et Azure Stack | Microsoft Docs
description: Découvrez comment déployer un groupe de disponibilité SQL Server 2016 sur Azure et Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 39b0078345c16b7931f41cd2394476f8258d92dd
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308836"
---
# <a name="tutorial-deploy-a-sql-server-2016-availability-group-to-azure-and-azure-stack"></a>Didacticiel : Déployer un groupe de disponibilité SQL Server 2016 sur Azure et Azure Stack

Cet article vous guide dans le processus de déploiement automatisé d’un cluster SQL Server 2016 Entreprise hautement disponible de base avec un site de récupération d’urgence asynchrone dans deux environnements Azure Stack. Pour en savoir plus sur SQL Server 2016 et la haute disponibilité, voir [Groupes de disponibilité Always On : une solution de haute disponibilité et de récupération d’urgence](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2016).

Dans ce tutoriel, vous créez un exemple d’environnement pour :

> [!div class="checklist"]
> - Orchestrer un déploiement dans deux environnements Azure Stack
> - Utiliser Docker pour minimiser les problèmes de dépendance avec des profils d’API Azure
> - Déployer un cluster SQL Server 2016 Entreprise hautement disponible de base avec un site de récupération d’urgence

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack est une extension d’Azure. Azure Stack apporte l’agilité et l’innovation du cloud computing à votre environnement local, et constitue le seul cloud hybride qui vous permet de créer et déployer des applications hybrides en tout lieu.  
> 
> Le livre blanc [Design Considerations for Hybrid Applications](https://aka.ms/hybrid-cloud-applications-pillars) (Étude des conceptions pour les applications hybrides) se penche sur les fondements de la qualité logicielle (sélection élective, extensibilité, disponibilité, résilience, facilité de gestion et sécurité) en matière de conception, de déploiement et d’exploitation des applications hybrides. Les considérations de conception vous aident à optimiser la conception des applications hybrides, en réduisant les risques dans les environnements de production.

## <a name="architecture-for-sql-server-2016"></a>Architecture pour SQL Server 2016

![Azure Stack haute disponibilité pour SQL Server 2016](media/azure-stack-solution-sql-ha/image1.png)

## <a name="prerequisites-for-sql-server-2016"></a>Conditions préalables pour SQL Server 2016

  - Deux systèmes intégrés Azure Stack (Azure Stack) connectés ; ce déploiement ne fonctionne pas sur des Kits de développement Azure Stack (ASDK). Pour en savoir plus sur Azure Stack, voir [Qu’est-ce qu’Azure Stack ?](https://azure.microsoft.com/overview/azure-stack/).
  - Un abonnement de locataire sur chaque infrastructure Azure Stack.    
      - **Prenez note de chaque ID d’abonnement et du point de terminaison Azure Resource Manager pour chaque infrastructure Azure Stack.**
  - Un principal de service Azure Active Directory (Azure AD) disposant d’autorisations pour l’abonnement du locataire sur chaque infrastructure Azure Stack. Il se peut que vous deviez créer deux principaux de service si les infrastructures Azure Stack sont déployées auprès de différents locataires Azure AD. Pour découvrir comment créer un principal de service pour Azure Stack, voir [Créer des principaux de service pour permettre à des applications d’accéder à des ressources Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).
      - **Prenez note de l’ID d’application, de la clé secrète client et du nom de locataire de chaque principal de service (xxxxx.onmicrosoft.com).**
  - SQL Server 2016 Entreprise syndiqué sur la Place de marché de chaque Azure Stack. Pour en savoir plus sur la syndication de place de marché, voir [Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
    **Assurez-vous que votre organisation dispose des licences SQL appropriées.**
  - [Docker pour Windows](https://docs.docker.com/docker-for-windows/) installé sur votre ordinateur local.

## <a name="get-the-docker-image"></a>Obtenir l’image Docker

Des images Docker pour chaque déploiement éliminent les problèmes de dépendance entre les différentes versions d’Azure PowerShell.

1.  Assurez-vous que Docker pour Windows utilise des conteneurs Windows.
2.  Exécutez la commande suivante dans une invite de commandes avec élévation de privilèges pour obtenir le conteneur Docker avec les scripts de déploiement.

```powershell  
 docker pull intelligentedge/sqlserver2016-hadr:1.0.0
```

## <a name="deploy-the-availability-group"></a>Déployer le groupe de disponibilité

1.  Une fois l’image conteneur correctement extraite, démarrez l’image.

      ```powershell  
      docker run -it intelligentedge/sqlserver2016-hadr:1.0.0 powershell
      ```

2.  Une fois le conteneur démarré, vous recevez un terminal PowerShell avec élévation de privilèges dans le conteneur. Modifiez les répertoires pour obtenir le script de déploiement.

      ```powershell  
      cd .\SQLHADRDemo\
      ```

3.  Exécutez le déploiement. Fournissez les informations d’identification et les noms de ressources si nécessaire. HA fait référence à l’infrastructure Azure Stack où sera déployé le cluster hautement disponible, et DR à l’infrastructure Azure Stack où le cluster de récupération d’urgence sera déployé.

      ```powershell
      > .\Deploy-AzureResourceGroup.ps1 `
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

5.  Attendez la fin du déploiement de la ressource.

6.  Une fois le déploiement de la ressource de récupération d’urgence terminé, quittez le conteneur.

      ```powershell
      exit
      ```

7.  Inspectez le déploiement en examinant les ressources dans le portail de chaque infrastructure Azure Stack. Connectez-vous à l’une des instances SQL dans l’environnement hautement disponible et inspectez le groupe de disponibilité via SQL Server Management Studio (SSMS).

![haute disponibilité SQL dans SQL Server 2016](media/azure-stack-solution-sql-ha/image2.png)

## <a name="next-steps"></a>Étapes suivantes

  - Pour utiliser SQL Server Management Studio afin de basculer manuellement le cluster, voir [Effectuer un basculement manuel forcé d’un groupe de disponibilité AlwaysOn (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server?view=sql-server-2017)
  - Pour en savoir plus sur les applications de cloud hybride, voir [Solutions de cloud hybride](https://aka.ms/azsdevtutorials).
  - Utilisez vos propres données ou modifiez le code pour cet exemple sur [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
