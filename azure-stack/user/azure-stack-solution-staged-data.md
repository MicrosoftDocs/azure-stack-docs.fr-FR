---
title: Déployer une solution d’analytique données mises en lots sur Azure Stack | Microsoft Docs
description: Découvrez comment déployer une solution d’analytique données mises en lots sur Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.topic: conceptual
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 187fe88ad304804462cc4461b24eb10a7af7a299
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277737"
---
# <a name="deploy-a-staged-data-analytics-solution-to-azure-stack"></a>Déployer une solution d’analytique données mises en lots sur Azure Stack

Cet article indique la procédure de déploiement d’une solution pour la collecte des données nécessitant une analyse au point de collecte pour permettre une prise de décisions rapide. Cette collecte de données se produit souvent sans accès à Internet. Lorsque la connexion est établie, vous devrez peut-être effectuer une analyse des données gourmande en ressources pour obtenir des informations supplémentaires.

Dans cette solution, vous allez créer un exemple d’environnement pour :

> [!div class="checklist"]
> - Créer l’objet blob de stockage des données brutes.
> - Créer une nouvelle fonction Azure Stack pour déplacer les données propres d’Azure Stack vers Azure.
> - Créer une fonction déclenchée par le stockage Blob.
> - Créer un compte de stockage Azure Stack contenant un objet blob et une file d’attente.
> - Créer une fonction déclenchée par une file d’attente.
> - Tester la fonction déclenchée par une file d’attente.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack est une extension d’Azure. Azure Stack apporte l’agilité et l’innovation du cloud computing à votre environnement local, en activant le seul cloud hybride qui vous permet de créer et de déployer des applications hybrides en tout lieu.  
> 
> L’article [Design Considerations for Hybrid Applications](azure-stack-edge-pattern-overview.md) se penche sur les fondements de la qualité logicielle (sélection élective, scalabilité, disponibilité, résilience, facilité de gestion et sécurité) en matière de conception, de déploiement et d’exploitation des applications hybrides. Les considérations de conception vous aident à optimiser la conception d’application hybride, en réduisant les risques dans les environnements de production.

## <a name="architecture-for-staged-data-analytics"></a>Architecture pour l’analytique données mises en lots

![analytique données mises en lots](media/azure-stack-solution-staged-data/image1.png)

## <a name="prerequisites-for-staged-data-analytics"></a>Conditions préalables à l’analytique données mises en lots

  - Un abonnement Azure.
  - Un principal de service Azure Active Directory (Azure AD) disposant d’autorisations pour l’abonnement du locataire sur Azure et Azure Stack. Il se peut que vous deviez créer deux principaux de service si Azure Stack utilise un autre locataire AAD que votre abonnement Azure. Pour découvrir comment créer un principal de service pour Azure Stack, voir [Créer des principaux de service pour permettre à des applications d’accéder à des ressources Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).
      - **Prenez note de l’ID d’application, du secret client, de l’ID de locataire Azure AD et du nom de locataire de chaque principal de service (xxxxx.onmicrosoft.com).**
  - Vous devez fournir une collection de données pour l’analyse des données. Des exemples de données sont fournis.
  - [Docker pour Windows](https://docs.docker.com/docker-for-windows/) installé sur votre ordinateur local.

## <a name="get-the-docker-image"></a>Obtenir l’image Docker

Des images Docker pour chaque déploiement éliminent les problèmes de dépendance entre les différentes versions d’Azure PowerShell.
1.  Assurez-vous que Docker pour Windows utilise des conteneurs Windows.
2.  Exécutez la commande suivante dans une invite de commandes avec élévation de privilèges pour obtenir le conteneur Docker avec les scripts de déploiement.

```
 docker pull intelligentedge/stageddatasolution:1.0.0
```

## <a name="deploy-the-solution"></a>Déployer la solution

1.  Une fois l’image conteneur correctement extraite, démarrez l’image.

      ```powershell  
      docker run -it intelligentedge/stageddatasolution:1.0.0 powershell
      ```

2.  Une fois le conteneur démarré, vous recevez un terminal PowerShell avec élévation de privilèges dans le conteneur. Modifiez les répertoires pour obtenir le script de déploiement.

      ```powershell  
      cd .\SDDemo\
      ```

3.  Exécutez le déploiement. Fournissez les informations d’identification et les noms de ressources si nécessaire. HA fait référence à l’infrastructure Azure Stack où le cluster hautement disponible sera déployé, et DR, à l’infrastructure Azure Stack où le cluster de récupération d’urgence sera déployé.

      ```powershell
      .\DeploySolution-Azure-AzureStack.ps1 `
      -AzureApplicationId "applicationIDforAzureServicePrincipal" `
      -AzureApplicationSercet "clientSecretforServicePrincipal" `
      -AzureTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -AzureStackAADTenantName "azurestacktenant.onmicrosoft.com" `
      -AzureStackTenantARMEndpoint "https://management.haazurestack.com" `
      -AzureStackApplicationId "applicationIDforStackServicePrincipal" `
      -AzureStackApplicationSercet "ClientSecretforStackServicePrincipal" `
      -AzureStackTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -ResourcePrefix "aPrefixForResources"
      ```

1.  Si vous y êtes invité, entrez une région pour le déploiement d’Azure et Application Insights.

2.  Tapez « Y » pour autoriser l’installation du fournisseur NuGet qui déclenche l’installation des modules « 2018-03-01-hybrid » du profil d’API afin d’autoriser le déploiement sur Azure et Azure Stack.

3.  Une fois les ressources déployées, vérifiez que les données sont générées pour Azure Stack et Azure.

    ```powershell  
      .\TDAGenerator.exe
    ```

4.  Pour voir les données qui sont en cours de traitement, accédez aux applications web déployées sur Azure ou Azure Stack.

### <a name="azure-web-app"></a>Application web Azure
 
![solution d’analytique données mises en lots](media/azure-stack-solution-staged-data/image2.png)
 
### <a name="azure-stack-web-app"></a>Application web Azure Stack
 
![solution d’analytique données mises en lots pour Azure Stack](media/azure-stack-solution-staged-data/image3.png)

## <a name="next-steps"></a>Étapes suivantes

  - Pour en savoir plus sur les applications de cloud hybride, voir [Solutions de cloud hybride](https://aka.ms/azsdevtutorials).

  - Utilisez vos propres données ou modifiez le code pour cet exemple sur [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
