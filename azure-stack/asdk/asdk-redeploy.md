---
title: Redéployer le Kit de développement Azure Stack (ASDK) | Microsoft Docs
description: Cet article vous montre comment réinstaller l’ASDK.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: f61fff0d29b1e0bf847ffc1761ff53c90b703991
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66267780"
---
# <a name="redeploy-the-asdk"></a>Redéployer l’ASDK
Dans cet article, vous allez découvrir comment redéployer le Kit de développement Azure Stack (ASDK) dans un environnement hors production. La mise à niveau de l’ASDK n’étant pas prise en charge, vous devez le redéployer complètement pour passer à une version plus récente. Vous pouvez également redéployer l’ASDK lorsque vous souhaitez simplement tout reprendre à zéro.

> [!IMPORTANT]
> La mise à niveau de l’ASDK vers une nouvelle version n’est pas prise en charge. Vous devez redéployer l’ASDK sur l’ordinateur hôte du Kit de développement chaque fois que vous souhaitez évaluer une version plus récente d’Azure Stack.

## <a name="remove-azure-registration"></a>Supprimer l’inscription auprès d’Azure 
Si vous avez précédemment inscrit votre installation ASDK auprès d’Azure, vous devez supprimer la ressource d’inscription avant de redéployer l’ASDK. Réinscrivez l’ASDK pour activer la syndication des éléments de marketplace lorsque vous redéployez l’ASDK. Si vous n’avez pas précédemment inscrit l’ASDK auprès de votre abonnement Azure, vous pouvez ignorer cette section.

Pour supprimer la ressource d’inscription, utilisez la cmdlet **Remove-AzsRegistration** pour annuler l’inscription d’Azure Stack. Ensuite, utilisez la cmdlet **Remove-AzureRMResourceGroup** pour supprimer le groupe de ressources Azure Stack de votre abonnement Azure :

1. Ouvrez une console PowerShell en tant qu’administrateur sur un ordinateur qui a accès au point de terminaison privilégié. Pour l’ASDK, il s’agit de l’ordinateur hôte du Kit de développement.

2. Exécutez les commandes PowerShell suivantes pour annuler l’inscription de votre installation ASDK et supprimer le groupe de ressources **azurestack** de votre abonnement Azure :

   ```powershell    
   #Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Provide Azure subscription admin credentials
   Add-AzureRmAccount

   # Provide ASDK admin credentials
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

   # Unregister Azure Stack
   Remove-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01

   # Remove the Azure Stack resource group
   Remove-AzureRmResourceGroup -Name azurestack -Force
   ```

3. Vous êtes invité à vous connecter à votre abonnement Azure et l’installation ASDK locale lors de l’exécution du script.
4. Une fois le script exécuté, vous devez voir des messages similaires aux exemples suivants :

    `De-Activating Azure Stack (this may take up to 10 minutes to complete).``Your environment is now unable to syndicate items and is no longer reporting usage data.`
    `Remove registration resource from Azure...`
    `"Deleting the resource..." on target "/subscriptions/<subscription information>"`
    `********** End Log: Remove-AzsRegistration *********`



Azure Stack doit maintenant avoir été correctement désinscrit de votre abonnement Azure. En outre, le groupe de ressources azurestack créé lorsque vous avez inscrit l’ASDK auprès d’Azure doit également avoir été supprimé.

## <a name="deploy-the-asdk"></a>Déployer l’ASDK
Pour redéployer Azure Stack, vous devez recommencer à partir de zéro comme décrit ci-dessous. Les étapes sont diffèrent selon que vous avez utilisé ou non le script du programme d’installation d’Azure Stack (asdk-installer.ps1) pour installer l’ASDK.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Redéployer le Kit de développement Azure Stack à l’aide du script du programme d’installation
1. Sur l’ordinateur ASDK, ouvrez une console PowerShell avec élévation de privilèges et accédez au script asdk-installer.ps1 dans le répertoire **AzureStack_Installer** situé sur un lecteur autre que le lecteur système. Exécutez le script, puis cliquez sur **Redémarrer**.

   ![Exécuter le script asdk-installer.ps1](media/asdk-redeploy/1.png)

2. Sélectionnez le système d’exploitation de base (et non pas **Azure Stack**), puis cliquez sur **Suivant**.

   ![Redémarrer sur le système d’exploitation hôte](media/asdk-redeploy/2.png)

3. Après le redémarrage du Kit de développement dans le système d’exploitation de base, connectez-vous en tant qu’administrateur local. Localisez et supprimez le fichier **C:\CloudBuilder.vhdx** qui a été utilisé lors du déploiement précédent. 

4. Répétez les mêmes étapes que celles que vous avez suivies pour [déployer l’ASDK](asdk-install.md) la première fois.

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Redéployer le Kit de développement Azure Stack à l’aide du programme d’installation
Si vous n’avez pas utilisé le script asdk-installer.ps1 pour installer l’ASDK, vous devez reconfigurer manuellement l’ordinateur hôte du Kit de développement avant de redéployer l’ASDK.

1. Démarrez l’utilitaire de configuration du système en exécutant **msconfig.exe** sur l’ordinateur ASDK. Dans l’onglet **Démarrage**, sélectionnez le système d’exploitation de l’ordinateur hôte (pas Azure Stack), cliquez sur **Définir par défaut**, puis cliquez sur **OK**. Cliquez sur **Redémarrer** lorsque vous y êtes invité.

      ![Définir la configuration de démarrage](media/asdk-redeploy/4.png)

2. Après le redémarrage du Kit de développement dans le système d’exploitation de base, connectez-vous en tant qu’administrateur local de l’ordinateur hôte du Kit de développement. Localisez et supprimez le fichier **C:\CloudBuilder.vhdx** qui a été utilisé lors du déploiement précédent. 

3. Répétez les mêmes étapes que celles que vous avez suivies pour [déployer l’ASDK avec PowerShell](asdk-deploy-powershell.md) la première fois.


## <a name="next-steps"></a>Étapes suivantes
[Tâches post-déploiement du Kit de développement Azure Stack](asdk-post-deploy.md)




