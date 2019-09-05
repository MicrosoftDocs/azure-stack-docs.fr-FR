---
title: Préparer l’ordinateur hôte du kit ASDK | Microsoft Docs
description: Découvrez comment préparer l’ordinateur hôte du Kit de développement Azure Stack (ASDK) en vue de son installation.
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
ms.date: 08/28/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 08/28/2019
ms.openlocfilehash: cf15aebac3ad4d099892270bb2e334d32f82f580
ms.sourcegitcommit: 5efa09034a56eb2f3dc0c9da238fe60cff0c67ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70144014"
---
# <a name="prepare-the-asdk-host-computer"></a>Préparer l’ordinateur hôte de l’ASDK
Avant de pouvoir installer le Kit de développement Azure Stack (ASDK) sur l’ordinateur hôte, vous devez préparer l’hôte ASDK. Une fois préparé, l’ordinateur hôte démarre à partir du disque dur de la machine virtuelle CloudBuilder.vhdx pour commencer le déploiement du kit ASDK.

## <a name="prepare-the-development-kit-host-computer"></a>Préparer l’ordinateur hôte du kit de développement
Avant de pouvoir installer le kit ASDK sur l’ordinateur hôte, vous devez préparer l’environnement de l’ordinateur. Pour préparer cet environnement, procédez comme suit :

1. Connectez-vous en tant qu’administrateur local à votre ordinateur hôte ASDK.
2. Vérifiez que le fichier CloudBuilder.vhdx a été déplacé à la racine du lecteur C:\ (`C:\CloudBuilder.vhdx`).
3. Exécutez le script suivant pour télécharger le fichier du programme d’installation ASDK (asdk-installer.ps1) du [répertoire des outils GitHub Azure Stack](https://github.com/Azure/AzureStack-Tools) au dossier **C:\AzureStack_Installer** de votre ordinateur hôte ASDK :

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

    ![Préparer l’environnement dans ASDK](media/asdk-prepare-host/1.PNG) 

5. Dans la page **Sélectionner Cloudbuilder.vhdx** du programme d’installation, sélectionnez le fichier **cloudbuilder.vhdx** que vous avez téléchargé et extrait dans les [étapes précédentes](asdk-download.md). Dans cette page, vous pouvez également cocher la case **Ajouter des pilotes** si vous avez besoin de pilotes supplémentaires sur l’ordinateur hôte du kit ASDK. Cliquez sur **Suivant**.  

    ![Sélectionnez cloudbuilder.vhdx et ajoutez des pilotes à ASDK](media/asdk-prepare-host/2.PNG)

6. Dans la page **Paramètres facultatifs**, entrez les informations d’identification du compte d’administrateur local de l’ordinateur hôte ASDK, puis cliquez sur **Suivant**.

    Si vous ne fournissez pas ces informations d’identification, vous aurez besoin d’un accès KVM ou direct à l’hôte après le redémarrage de l’ordinateur, dans le cadre de la procédure de configuration du ASDK.

   ![Paramètres facultatifs dans ASDK - Fournir des informations sur le compte d’administrateur local](media/asdk-prepare-host/3.PNG)

    Vous pouvez également fournir des valeurs pour les paramètres facultatifs suivants :
    - **Nom de l'ordinateur** : cette option définit le nom de l'hôte ASDK. Le nom doit respecter les spécifications des noms de domaine complets et ne pas dépasser 15 caractères. La valeur par défaut est un nom d’ordinateur aléatoire généré par Windows.

        - Sélectionner une carte réseau. Vérifiez que vous pouvez vous connecter à la carte avant de cliquer sur **Suivant**.

            ![Capture d’écran des paramètres de carte réseau](media/asdk-prepare-host/step-four-network-adapter.png)

        - Vérifiez que les valeurs affichées pour **Adresse IP**, **Passerelle** et **DNS** sont correctes, fournissez une adresse **IP de serveur de temps** valide, puis cliquez sur **Suivant**.

            >[!TIP]
            >Pour rechercher l'adresse IP d'un serveur de temps, visitez [ntppool.org](https://www.ntppool.org/) ou effectuez un test ping time.windows.com. 

            ![Capture d’écran des paramètres de configuration IP](media/asdk-prepare-host/step-five-host-ip-config.png)

7. Cliquez sur **Suivant** pour démarrer le processus de préparation.
8. Quand la préparation indique **Terminé**, cliquez sur **Suivant**.

    ![Préparation de l’environnement dans ASDK](media/asdk-prepare-host/4.PNG)

9. Cliquez sur **Redémarrer maintenant** pour démarrer l’ordinateur hôte ASDK dans cloudbuilder.vhdx, puis [continuer le processus de déploiement](asdk-install.md).

    ![Redémarrer le ASDK](media/asdk-prepare-host/5.PNG)


## <a name="next-steps"></a>Étapes suivantes
[Installer le kit ASDK](asdk-install.md)
