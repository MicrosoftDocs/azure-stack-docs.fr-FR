---
title: Installer le Kit de développement Azure Stack (ASDK) | Microsoft Docs
description: Explique comment installer le Kit de développement Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: c14b55379bfad08cbb604b8f27b45a125741a241
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617409"
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Installer le Kit de développement Azure Stack (ASDK)
Après la [préparation de l’ordinateur hôte ASDK](asdk-prepare-host.md), le kit ASDK peut être déployé dans l’image CloudBuilder.vhdx en suivant les étapes de cet article.

## <a name="install-the-asdk"></a>Installer l’ASDK
Les étapes de cet article vous montrent comment déployer le kit ASDK à l’aide d’une interface graphique utilisateur (GUI) que vous pouvez obtenir en téléchargeant et en exécutant le script PowerShell **asdk-installer.ps1**.

> [!NOTE]
> L’interface utilisateur du programme d’installation du Kit de développement Azure Stack est un script open source basé sur WCF et PowerShell.


1. Une fois que l’ordinateur hôte a correctement démarré dans l’image CloudBuilder.vhdx, connectez-vous avec les informations d’identification d’administration spécifiées lorsque vous avez [préparé l’ordinateur hôte du kit de développement](asdk-prepare-host.md) en vue de l’installation du kit ASDK. Il doit s’agir des mêmes informations d’identification d’administrateur local que celles de l’hôte du Kit de développement.
2. Ouvrez une console PowerShell avec élévation de privilèges et exécutez le script PowerShell **&lt;lettre de lecteur>\AzureStack_Installer\asdk-installer.ps1**. Notez que le script peut maintenant se trouver sur un lecteur autre que C:\ dans l’image CloudBuilder.vhdx. Cliquez sur **Installer**.

    ![](media/asdk-install/1.PNG) 

3. Dans la zone déroulante **Type** du fournisseur d’identité, sélectionnez **Cloud Azure Chine**, **Cloud Azure US Government**, **AD FS** ou **Cloud Azure**. Sous **Mot de passe de l’administrateur local**, dans la zone **Mot de passe**, tapez le mot de passe de l’administrateur local (qui doit correspondre au mot de passe de l’administrateur local actuellement configuré), puis cliquez sur **Suivant**.

    ![](media/asdk-install/2.PNG) 
  
   Si vous choisissez un fournisseur d'identité d’abonnement Azure, vous avez besoin d’une connexion Internet, du nom complet d’un locataire d’annuaire Azure AD au format *nomdomaine*.onmicrosoft.com ou d’un nom de domaine personnalisé vérifié Azure AD et des informations d’identification d’administrateur général de l’annuaire spécifié.<br><br>Après le déploiement, l’autorisation d’administrateur général Azure Active Directory n’est pas nécessaire. Toutefois, certaines opérations peuvent demander des informations d’identification d’administrateur général. Par exemple, un script d’installation d’un fournisseur de ressources ou une nouvelle fonctionnalité peut avoir besoin d’une autorisation spécifique. Vous pouvez temporairement réactiver les autorisations d’administrateur général du compte ou utiliser un compte d’administrateur général distinct qui est propriétaire de *l’abonnement de fournisseur par défaut*.<br><br>Lors de l’utilisation d’AD FS en tant que fournisseur d’identité, le service d’annuaire de marquage par défaut est sélectionné. Le compte par défaut avec lequel se connecter est azurestackadmin@azurestack.local et le mot de passe à utiliser est celui que vous avez fourni dans le cadre de la configuration.

   > [!NOTE]
   > Pour un résultat optimal, même si vous voulez utiliser un environnement Azure Stack déconnecté et AD FS comme fournisseur d’identité, il est préférable d’installer le kit ASDK en étant connecté à Internet. De cette façon, la version d’évaluation de Windows Server 2016 incluse avec l’installation du kit de développement peut être activée au moment du déploiement.

4. Sélectionnez une carte réseau à utiliser pour le Kit de développement, puis cliquez sur **Suivant**.

    ![](media/asdk-install/3.PNG)

5. Sur la page **Configuration réseau**, indiquez une **adresse IP de serveur de temps** valide. Ce champ obligatoire définit le serveur de temps qui doit être utilisé par le Kit de développement. Ce paramètre doit être fourni sous la forme d’une adresse IP de serveur temps valide. Les noms de serveur ne sont pas pris en charge.

      > [!TIP]
      > Pour rechercher l'adresse IP d'un serveur de temps, visitez [ntppool.org](https://www.ntppool.org/) ou effectuez un test ping time.windows.com. 

    **Le cas échant**, vous pouvez indiquer une adresse IP de **redirecteur DNS**. Un serveur DNS est créé dans le cadre du déploiement Azure Stack. Pour permettre aux ordinateurs de la solution de résoudre les noms en dehors du marquage, spécifiez le serveur DNS de votre infrastructure existante. Le serveur DNS couvert par le marquage transfère les demandes de résolution de noms inconnus à ce serveur.

    ![](media/asdk-install/4.PNG)

6. Dans la page **Vérification des propriétés de la carte d’interface réseau**, vous voyez une barre de progression. Une fois la vérification terminée, cliquez sur **Suivant**.

    ![](media/asdk-install/5.PNG)

7. Dans la page **Résumé**, cliquez sur **Déployer** pour démarrer l’installation du kit ASDK sur l’ordinateur hôte du kit de développement.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > Ici, vous pouvez également copier les commandes de configuration PowerShell qui seront utilisées pour installer le kit de développement. C’est utile si vous avez besoin de [redéployer le kit ASDK sur l’ordinateur hôte à l’aide de PowerShell](asdk-deploy-powershell.md).

8. Si vous effectuez un déploiement Azure AD, vous serez invité à entrer les informations d’identification du compte administrateur général quelques minutes après le début de l’installation.

9. Le processus de déploiement peut prendre quelques heures, au cours desquelles l’ordinateur hôte ne redémarre automatiquement qu’une seule fois. Si vous voulez surveiller la progression du déploiement, connectez-vous en tant que azurestack\AzureStackAdmin après le redémarrage de l’hôte du kit de développement. Une fois le déploiement terminé, la console PowerShell affiche le message suivant : **TERMINÉ : Action « Déploiement »**. 
    > [!IMPORTANT]
    > Si vous vous connectez en tant qu’administrateur local une fois que la machine est jointe au domaine azurestack, vous ne voyez pas la progression du déploiement. Ne réexécutez pas le déploiement : au lieu de cela, connectez-vous en tant que azurestack\AzureStackAdmin pour vérifier qu’il est en cours d’exécution.

    ![](media/asdk-install/7.PNG)

Félicitations, vous avez installé le kit ASDK !

Si le déploiement échoue pour la même raison, vous pouvez effectuer un [redéploiement](asdk-redeploy.md) depuis le début ou utiliser les commandes PowerShell suivantes, dans la même fenêtre PowerShell avec des privilèges élevés, pour redémarrer le déploiement à partir de la dernière étape réussie :

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>Étapes suivantes
[Configuration post-déploiement](asdk-post-deploy.md)
