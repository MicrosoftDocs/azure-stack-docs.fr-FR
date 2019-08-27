---
title: Préparer l’ordinateur hôte du Kit de développement Azure Stack (ASDK) | Microsoft Docs
description: Explique comment préparer l’ordinateur hôte du Kit de développement Azure Stack (ASDK) en vue de son installation.
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
ms.date: 08/20/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: 291042e0a7af78ed2431c901901e7e44b1f05de1
ms.sourcegitcommit: fc7da38321736e952b2cc6d5d07f276d095dc8d1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887140"
---
# <a name="prepare-the-asdk-host-computer"></a>Préparer l’ordinateur hôte de l’ASDK
Avant de pouvoir installer le kit ASDK sur l’ordinateur hôte, vous devez préparer l’hôte ASDK. Une fois préparé, l’ordinateur hôte du kit de développement Azure Stack démarre à partir du disque dur de la machine virtuelle CloudBuilder.vhdx pour commencer le déploiement du kit.

## <a name="prepare-the-development-kit-host-computer"></a>Préparer l’ordinateur hôte du kit de développement
Avant de pouvoir installer le kit ASDK sur l’ordinateur hôte, vous devez préparer l’environnement de l’ordinateur.
1. Connectez-vous à l’ordinateur hôte du kit de développement en tant qu’administrateur local.
2. Vérifiez que le fichier CloudBuilder.vhdx a été déplacé à la racine du lecteur C:\ (C:\CloudBuilder.vhdx).
3. Exécutez le script suivant pour télécharger le fichier du programme d’installation du kit de développement (asdk-installer.ps1) du [répertoire des outils GitHub Azure Stack](https://github.com/Azure/AzureStack-Tools) au dossier **C:\AzureStack_Installer** de votre ordinateur hôte du kit de développement :

   > [!IMPORTANT]
   > Assurez-vous de télécharger le fichier asdk-installer.ps1 chaque fois que vous installez le kit ASDK. Des changements fréquents sont apportés à ce script et la version la plus récente doit être utilisée pour chaque déploiement ASDK. Les versions plus anciennes du script peuvent ne pas fonctionner avec la version la plus récente.

   ```powershell
   # Variables
   $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
   $LocalPath = 'C:\AzureStack_Installer'
   # Create folder
   New-Item $LocalPath -Type directory
   # Enforce usage of TLSv1.2 to download from GitHub
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   # Download file
   Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
   ```

4. Depuis une console PowerShell avec élévation de privilèges, lancez le script **C:\AzureStack_Installer\asdk-installer.ps1**, puis cliquez sur **Préparer l’environnement**.

    ![Capture d’écran de Préparer l’environnement](media/asdk-prepare-host/1.PNG) 

5. Dans la page **Sélectionner Cloudbuilder.vhdx** du programme d’installation, sélectionnez le fichier **cloudbuilder.vhdx** que vous avez téléchargé et extrait dans les [étapes précédentes](asdk-download.md). Dans cette page, vous pouvez également (si vous le souhaitez) cocher la case **Ajouter des pilotes** si vous avez besoin de pilotes supplémentaires sur l’ordinateur hôte du kit de développement. Cliquez sur **Suivant**.  

    ![Capture d’écran de Select Cloudbuilder.vhdx](media/asdk-prepare-host/2.PNG)

6. Dans la page **Paramètres facultatifs**, entrez les informations d’identification du compte d’administrateur local de l’ordinateur hôte du kit de développement, puis cliquez sur **Suivant**.<br><br>Si vous ne fournissez pas ces informations d’identification, vous aurez besoin d’un accès KVM ou direct à l’hôte après le redémarrage de l’ordinateur, dans le cadre de la procédure de configuration du kit de développement.

   ![Capture d’écran des paramètres facultatifs](media/asdk-prepare-host/3.PNG)

    Vous pouvez également fournir des valeurs pour les paramètres facultatifs suivants :
    - **Nom de l'ordinateur** : cette option définit le nom de l'hôte du kit de développement. Le nom doit respecter les spécifications des noms de domaine complets et ne pas dépasser 15 caractères. La valeur par défaut est un nom d’ordinateur aléatoire généré par Windows.
    - **Configuration IP statique** : indique que votre déploiement doit utiliser une adresse IP statique. Dans le cas contraire, quand le programme d’installation redémarre dans cloudbuilder.vhdx, les interfaces réseau sont configurées avec DHCP. Si vous choisissez d’utiliser une configuration IP statique, des options supplémentaires s’affichent, ce qui vous oblige à effectuer les étapes suivantes :
      - Sélectionner une carte réseau. Vérifiez que vous pouvez vous connecter à la carte avant de cliquer sur **Suivant**.

        ![Capture d’écran des paramètres de carte réseau](media/asdk-prepare-host/step-four-network-adapter.png)

      - Vérifiez que les valeurs affichées pour **Adresse IP**, **Passerelle** et **DNS** sont correctes, fournissez une adresse **IP de serveur de temps** valide, puis cliquez sur **Suivant**.

        >[!TIP]
        >Pour rechercher l'adresse IP d'un serveur de temps, visitez [ntppool.org](https://www.ntppool.org/) ou effectuez un test ping time.windows.com. 

        ![Capture d’écran des paramètres de configuration IP](media/asdk-prepare-host/step-five-host-ip-config.png)

7. Cliquez sur **Suivant** pour démarrer le processus de préparation.
8. Quand la préparation indique **Terminé**, cliquez sur **Suivant**.

    ![Capture d’écran de Terminé](media/asdk-prepare-host/4.PNG)

9. Cliquez sur **Redémarrer maintenant** pour démarrer l’ordinateur hôte du kit de développement dans cloudbuilder.vhdx, puis [continuer le processus de déploiement](asdk-install.md).

    ![Capture d’écran de Redémarrer maintenant](media/asdk-prepare-host/5.PNG)


## <a name="next-steps"></a>Étapes suivantes
[Installer le kit ASDK](asdk-install.md)
