---
title: Ajouter des workers et une infrastructure dans App Service sur Azure Stack | Microsoft Docs
description: Instructions détaillées pour la mise à l’échelle d’App Services Azure Stack
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: b01199bfe96c39fe79aac65eca219a065f39375c
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70975004"
---
# <a name="add-workers-and-infrastructure-in-app-service-on-azure-stack"></a>Ajouter des workers et une infrastructure dans App Service sur Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*  

Ce document fournit des instructions sur la mise à l’échelle des rôles de worker et de l’infrastructure dans App Service sur Azure Stack. Il explique toutes les étapes à effectuer pour créer des rôles de worker supplémentaires en vue de la prise en charge de toutes les applications, quelle que soit leur taille.

> [!NOTE]
> Si votre environnement Azure Stack n’a pas plus de 96 Go de RAM, l’ajout de capacité supplémentaire peut se révéler difficile.

App Service sur Azure Stack prend en charge les niveaux de worker gratuits et partagés par défaut. Pour ajouter d’autres niveaux Worker, vous devez ajouter davantage de rôles de travail.

Si vous ne savez pas précisément ce qui a été déployé avec l’installation App Service sur Azure Stack par défaut, consultez [Vue d’ensemble d’App Service sur Azure Stack](azure-stack-app-service-overview.md) pour avoir plus d’informations.

Azure App Service sur Azure Stack déploie tous les rôles à l’aide de groupes de machines virtuelles identiques et en tant que tel tire parti des fonctionnalités de mise à l’échelle de cette charge de travail. Par conséquent, toute la mise à l’échelle des niveaux Worker s’effectue via l’administrateur App Service.

## <a name="add-additional-workers-with-powershell"></a>Ajouter des Workers supplémentaires avec PowerShell

1. [Configurer l’environnement d’administration Azure Stack dans PowerShell](azure-stack-powershell-configure-admin.md)

2. Utilisez cet exemple pour monter en puissance le groupe identique de machines virtuelles :
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRmAccount -EnvironmentName AzureStackAdmin
                                                 
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"

    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"

    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
   ```    

   > [!NOTE]
   > L’exécution de cette étape peut prendre plusieurs heures en fonction du type de rôle et du nombre d’instances.
   >
   >

3. Surveillez l’état des nouvelles instances de rôle dans le portail d’administration App Service. Pour vérifier l’état d’une instance de rôle, cliquez sur le type de rôle dans la liste.

## <a name="add-additional-workers-using-the-administrator-portal"></a>Ajouter des rôles de travail supplémentaires à l’aide du portail administrateur

1. Connectez-vous au portail administrateur Azure Stack en tant qu’administrateur du service.

2. Parcourir **App Services**.

    ![App Service dans le portail administrateur Azure Stack](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Cliquez sur **Rôles**. La répartition de tous les rôles App Service déployés s’affiche ici.

4. Cliquez avec le bouton droit sur la ligne du type que vous voulez mettre à l’échelle, puis cliquez sur **ScaleSet**.

    ![Rôles App Service ScaleSet dans le portail administrateur Azure Stack](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Cliquez sur **Mise à l’échelle**, sélectionnez le nombre d’instances vers lequel vous voulez faire la mise à l’échelle, puis cliquez sur **Enregistrer**.

    ![Définir les instances vers lesquelles faire la mise à l’échelle dans les rôles App Service dans le portail administrateur Azure Stack](media/azure-stack-app-service-add-worker-roles/image03.png)

6. App Service sur Azure Stack ajoute ensuite les machines virtuelles supplémentaires, les configure, installe tous les logiciels requis et les marque comme Prêt lorsque ce processus est terminé. Ce processus peut prendre environ 80 minutes.

7. Vous pouvez surveiller la progression de la préparation des nouveaux rôles en affichant les Workers dans le panneau **Rôles**.

## <a name="result"></a>Résultat

Une fois que les workers sont entièrement déployés et prêts, les utilisateurs peuvent y déployer leur charge de travail. La capture d’écran suivante montre un exemple des différents niveaux tarifaires disponibles par défaut. S’il n’existe aucun Worker disponible pour un niveau Worker spécifique, l’option permettant de choisir le niveau tarifaire correspondant est indisponible.

![Niveaux tarifaires pour le nouveau plan App Service dans le portail administrateur Azure Stack](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Pour la montée en charge des rôles Gestion, Frontal ou Serveur de publication, suivez les mêmes étapes en sélectionnant le type de rôle approprié. Les contrôleurs ne sont pas déployés en tant que groupes identiques. Vous devez donc déployer deux contrôleurs au moment de l’installation pour tous les déploiements de production.

### <a name="next-steps"></a>Étapes suivantes

[Configurer des sources de déploiement](azure-stack-app-service-configure-deployment-sources.md)
