---
title: Déployer un groupe de disponibilité SQL Server 2016 sur Azure et Azure Stack Hub
description: Apprenez à déployer un groupe de disponibilité SQL Server 2016 sur Azure et Azure Stack Hub
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: b1de7de0c81af80c30620b85bd19b4806877190a
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76876709"
---
# <a name="deploy-a-sql-server-2016-availability-group-to-azure-and-azure-stack-hub"></a>Déployer un groupe de disponibilité SQL Server 2016 sur Azure et Azure Stack Hub

Cet article vous guidera tout au long du processus de déploiement automatisé d'un cluster de base SQL Server 2016 Entreprise haute disponibilité (HA) avec un site de récupération d'urgence asynchrone dans deux environnements Azure Stack Hub. Pour en savoir plus sur SQL Server 2016 et la haute disponibilité, voir [Groupes de disponibilité Always On : une solution de haute disponibilité et de récupération d’urgence](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2016).

Dans cette solution, vous allez générer un exemple d’environnement pour :

> [!div class="checklist"]
> - Orchestrer un déploiement dans deux environnements Azure Stack Hub
> - Utiliser Docker pour minimiser les problèmes de dépendance avec des profils d’API Azure
> - Déployer un cluster SQL Server 2016 Entreprise hautement disponible de base avec un site de récupération d’urgence

> [!Tip]  
> ![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack Hub est une extension d’Azure. Azure Stack Hub offre à votre environnement local l’agilité et l’innovation du cloud computing grâce au seul cloud hybride qui vous permette de créer et de déployer des applications hybrides en tout lieu.  
> 
> L’article [Design Considerations for Hybrid Applications](overview-app-design-considerations.md) se penche sur les fondements de la qualité logicielle (sélection élective, scalabilité, disponibilité, résilience, facilité de gestion et sécurité) en matière de conception, de déploiement et d’exploitation des applications hybrides. Les considérations de conception vous aident à optimiser la conception d’application hybride, en réduisant les risques dans les environnements de production.

## <a name="architecture-for-sql-server-2016"></a>Architecture pour SQL Server 2016

![Azure Stack Hub haute disponibilité pour SQL Server 2016](media/solution-deployment-guide-sql-ha/image1.png)

## <a name="prerequisites-for-sql-server-2016"></a>Conditions préalables pour SQL Server 2016

  - Deux systèmes intégrés Azure Stack Hub (Azure Stack Hub) connectés ; ce déploiement ne fonctionne pas sur les Kits de développement Azure Stack Hub (ASDK). Pour en savoir plus sur Azure Stack Hub, consultez [Qu'est-ce qu'Azure Stack Hub ?](https://azure.microsoft.com/overview/azure-stack/).
  - Un abonnement de locataire sur chaque infrastructure Azure Stack Hub.    
      - **Prenez note de chaque ID d’abonnement et du point de terminaison Azure Resource Manager de chaque infrastructure Azure Stack Hub.**
  - Un principal de service Azure Active Directory (Azure AD) disposant d’autorisations pour l’abonnement du locataire sur chaque infrastructure Azure Stack Hub. Vous devrez peut-être créer deux principaux de service si les infrastructures Azure Stack Hub sont déployées auprès de différents locataires Azure AD. Pour en savoir plus sur la création d’un principal de service pour Azure Stack Hub, consultez [Créer des principaux de service pour permettre à des applications d’accéder à des ressources Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).
      - **Prenez note de l’ID d’application, de la clé secrète client et du nom de locataire de chaque principal de service (xxxxx.onmicrosoft.com).**
  - SQL Server 2016 Entreprise syndiqué sur la Place de marché de chaque infrastructure Azure Stack Hub. Pour en savoir plus sur la syndication de place de marché, consultez [Télécharger des éléments de la Place de marché vers Azure Stack Hub à partir d’Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
    **Assurez-vous que votre organisation dispose des licences SQL appropriées.**
  - [Docker pour Windows](https://docs.docker.com/docker-for-windows/) installé sur votre ordinateur local.

## <a name="get-the-docker-image"></a>Obtenir l’image Docker

Des images Docker pour chaque déploiement éliminent les problèmes de dépendance entre les différentes versions d’Azure PowerShell.

1.  Assurez-vous que Docker pour Windows utilise des conteneurs Windows.
2.  Exécutez le script suivant dans une invite de commandes avec élévation de privilèges pour obtenir le conteneur Docker avec les scripts de déploiement.

```powershell  
 docker pull intelligentedge/sqlserver2016-hadr:1.0.0
```

## <a name="deploy-the-availability-group"></a>Déployer le groupe de disponibilité

1.  Une fois l’image conteneur correctement extraite, démarrez l’image.

      ```powershell  
      docker run -it intelligentedge/sqlserver2016-hadr:1.0.0 powershell
      ```

2.  Une fois le conteneur démarré, vous recevrez un terminal PowerShell avec élévation de privilèges dans le conteneur. Modifiez les répertoires pour obtenir le script de déploiement.

      ```powershell  
      cd .\SQLHADRDemo\
      ```

3.  Exécutez le déploiement. Fournissez les informations d’identification et les noms de ressources si nécessaire. HA fait référence à l'infrastructure Azure Stack Hub où le cluster haute disponibilité sera déployé, et DR, à l'infrastructure Azure Stack Hub où le cluster de récupération d'urgence sera déployé.

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

7.  Inspectez le déploiement en examinant les ressources sur le portail de chaque infrastructure Azure Stack Hub. Connectez-vous à l’une des instances SQL dans l’environnement hautement disponible et inspectez le groupe de disponibilité via SQL Server Management Studio (SSMS).

![haute disponibilité SQL dans SQL Server 2016](media/solution-deployment-guide-sql-ha/image2.png)

## <a name="next-steps"></a>Étapes suivantes

  - Pour utiliser SQL Server Management Studio afin de basculer manuellement le cluster, voir [Effectuer un basculement manuel forcé d’un groupe de disponibilité AlwaysOn (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server?view=sql-server-2017)
  - Pour en savoir plus sur les applications de cloud hybride, voir [Solutions de cloud hybride](https://aka.ms/azsdevtutorials).
  - Utilisez vos propres données ou modifiez le code pour cet exemple sur [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
