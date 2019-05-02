---
title: Augmenter la taille des instances des rôles de travail dans App Services - Azure Stack  | Microsoft Docs
description: Instructions détaillées pour la mise à l’échelle d’App Services Azure Stack
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 02/22/2010
ms.date: 03/04/2019
ms.author: v-jay
ms.reviewer: anwestg
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: 839fa7fe8374f1f85b019178d4c3fe53f7137372
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308735"
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>App Service sur Azure Stack : Ajouter des rôles d'infrastructure ou de travail

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*  

Ce document fournit des instructions sur la mise à l’échelle d’App Service sur des rôles d’infrastructure et de travail Azure Stack. Il contient les étapes de création de rôles de travail supplémentaires pour prendre en charge des applications de n’importe quelle taille.

> [!NOTE]
> Si votre environnement Azure Stack n’a pas plus de 96 Go de RAM, l’ajout de capacité supplémentaire peut se révéler difficile.

App Service sur Azure Stack, par défaut, prend en charge les niveaux Worker gratuits et partagés. Pour ajouter d’autres niveaux Worker, vous devez ajouter davantage de rôles de travail.

Si vous n’êtes pas sûr de ce qui a été déployé avec l’installation App Service sur Azure Stack par défaut, vous pouvez consulter des informations supplémentaires dans la [vue d’ensemble d’App Service sur Azure Stack](azure-stack-app-service-overview.md).

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

3. Surveillez l’état des nouvelles instances de rôle dans l’administration App Service. Pour vérifier l’état d’une instance de rôle individuel, cliquez sur le type de rôle dans la liste.

## <a name="add-additional-workers-using-the-administration-portal"></a>Ajouter des workers supplémentaires à l’aide du portail d’administration

1. Connectez-vous au portail d’administration Azure Stack en tant qu’administrateur du service.

2. Parcourir **App Services**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Cliquez sur **Rôles**. La répartition de tous les rôles App Service déployés s’affiche ici.

4. Cliquez avec le bouton droit sur la ligne du type que vous voulez mettre à l’échelle, puis cliquez sur **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Cliquez sur **Mise à l’échelle**, sélectionnez le nombre d’instances vers lequel vous voulez faire la mise à l’échelle, puis cliquez sur **Enregistrer**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. App Service sur Azure Stack ajoute ensuite les machines virtuelles supplémentaires, les configure, installe tous les logiciels requis et les marque comme Prêt lorsque ce processus est terminé. Ce processus peut prendre environ 80 minutes.

7. Vous pouvez surveiller la progression de la préparation des nouveaux rôles en affichant les Workers dans le panneau **Rôles**.

## <a name="result"></a>Résultat

Une fois qu’ils sont entièrement déployés et prêts, les utilisateurs peuvent déployer leur charge de travail sur les Workers. Voici un exemple des différents niveaux tarifaires disponibles par défaut. S’il n’existe aucun Worker disponible pour un niveau Worker spécifique, l’option permettant de choisir le niveau tarifaire correspondant est indisponible.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Pour monter en puissance les rôles de gestion, de serveur frontal ou de serveur de publication, vous devez monter en puissance le type de rôle correspondant. 
>
>

Pour la montée en charge des rôles Gestion, Frontal ou Serveur de publication, suivez les mêmes étapes en sélectionnant le type de rôle approprié. Les contrôleurs ne sont pas déployés comme groupes de machines virtuelles identiques : par conséquent, vous devez déployer deux contrôleurs au moment de l’installation pour tous les déploiements de production.

### <a name="next-steps"></a>Étapes suivantes

[Configurer des sources de déploiement](azure-stack-app-service-configure-deployment-sources.md)

<!-- Update_Description: wording update -->