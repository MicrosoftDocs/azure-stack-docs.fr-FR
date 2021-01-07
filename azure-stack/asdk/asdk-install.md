---
title: Installer l’ASDK
description: En savoir plus sur l’installation du Kit de développement Azure Stack (ASDK).
author: PatAltimore
ms.topic: article
ms.date: 05/06/2019
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: fe485a3eebee3e44e19173fdf68440057de7d783
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873416"
---
# <a name="install-the-asdk"></a>Installer l’ASDK
Après la [préparation de l’ordinateur hôte ASDK](asdk-prepare-host.md), le Kit de développement Azure Stack (ASDK) peut être déployé dans l’image CloudBuilder.vhdx en suivant les étapes de cet article.

## <a name="install-the-asdk"></a>Installer l’ASDK
Les étapes de cet article vous montrent comment déployer le kit ASDK à l’aide d’une interface graphique utilisateur (GUI) que vous pouvez obtenir en téléchargeant et en exécutant le script PowerShell **asdk-installer.ps1**.

> [!NOTE]
> L’interface utilisateur du programme d’installation du kit ASDK est un script open source basé sur WCF et PowerShell.


1. Une fois que l’ordinateur hôte a correctement démarré dans l’image CloudBuilder.vhdx, connectez-vous avec les informations d’identification d’administration spécifiées lorsque vous avez [préparé l’ordinateur hôte du kit ASDK](asdk-prepare-host.md) en vue de l’installation du kit ASDK. Il doit s’agit des mêmes informations d’identification que celles de l’administrateur local de l’hôte ASDK.
2. Ouvrez une console PowerShell avec élévation de privilèges et exécutez le script PowerShell **&lt;lettre de lecteur>\AzureStack_Installer\asdk-installer.ps1**. Notez que le script peut maintenant se trouver sur un lecteur autre que C:\ dans l’image CloudBuilder.vhdx. Cliquez sur **Installer**.

    ![Installer ASDK](media/asdk-install/1.PNG) 

3. Dans la zone déroulante **Type** du fournisseur d’identité, sélectionnez **Cloud Azure Chine**, **Cloud Azure US Government**, **AD FS** ou **Cloud Azure**. Sous **Mot de passe de l’administrateur local**, dans la zone **Mot de passe**, tapez le mot de passe de l’administrateur local (qui doit correspondre au mot de passe de l’administrateur local actuellement configuré), puis cliquez sur **Suivant**.

    ![Liste déroulante du type de fournisseur d’identité dans ASDK](media/asdk-install/2.PNG) 
  
    Si vous choisissez un fournisseur d'identité d’abonnement Azure, vous avez besoin d’une connexion Internet, du nom complet d’un locataire d’annuaire Azure AD au format *nomdomaine*.onmicrosoft.com ou d’un nom de domaine personnalisé vérifié Azure AD. Vous avez également besoin des informations d’identification de l’administrateur global pour le répertoire spécifié.

    Après le déploiement, l’autorisation d’administrateur général Azure Active Directory (Azure AD) n’est pas nécessaire. Cependant, certaines opérations peuvent nécessiter les informations d'identification d’administrateur global. Par exemple, un script d’installation d’un fournisseur de ressources ou une nouvelle fonctionnalité peut avoir besoin d’une autorisation spécifique. Vous pouvez temporairement réactiver les autorisations d’administrateur général du compte ou utiliser un compte d’administrateur général distinct qui est propriétaire de *l’abonnement de fournisseur par défaut*.

    Lors de l’utilisation d’AD FS en tant que fournisseur d’identité, le service d’annuaire de marquage par défaut est sélectionné. Le compte par défaut avec lequel se connecter est azurestackadmin@azurestack.local et le mot de passe à utiliser est celui que vous avez fourni dans le cadre de la configuration.

   > [!NOTE]
   > Pour un résultat optimal, même si vous voulez utiliser un environnement Azure Stack déconnecté et AD FS comme fournisseur d’identité, il est préférable d’installer le kit ASDK en étant connecté à Internet. De cette façon, la version d’évaluation de Windows Server 2016 incluse avec l’installation du kit ASDK peut être activée au moment du déploiement.

4. Sélectionnez une carte réseau à utiliser pour kit ASDK, puis cliquez sur **Suivant**.

    ![Sélectionner une carte réseau pour ASDK](media/asdk-install/3.PNG)

5. Sur la page **Configuration réseau**, indiquez une **adresse IP de serveur de temps** valide. Ce champ obligatoire définit le serveur de temps qui doit être utilisé par le kit ASDK. Ce paramètre doit être fourni sous la forme d’une adresse IP de serveur temps valide. Les noms des serveurs ne sont pas pris en charge.

      > [!TIP]
      > Pour rechercher l'adresse IP d'un serveur de temps, visitez [ntppool.org](https://www.ntppool.org/) ou effectuez un test ping time.windows.com. 

    **Le cas échant**, vous pouvez indiquer une adresse IP de **redirecteur DNS**. Un serveur DNS est créé dans le cadre du déploiement Azure Stack. Pour permettre aux ordinateurs de la solution de résoudre les noms en dehors du marquage, spécifiez le serveur DNS de votre infrastructure existante. Le serveur DNS couvert par le marquage transfère les demandes de résolution de noms inconnus à ce serveur.

    ![Redirecteur DNS et configuration réseau dans ASDK](media/asdk-install/4.PNG)

6. Dans la page **Vérification des propriétés de la carte d’interface réseau**, vous voyez une barre de progression. Une fois la vérification terminée, cliquez sur **Suivant**.

    ![Vérification des propriétés de la carte d’interface réseau dans ASDK](media/asdk-install/5.PNG)

7. Dans la page **Résumé**, cliquez sur **Déployer** pour démarrer l’installation du kit ASDK sur l’ordinateur hôte du kit ASDK.

    ![Résumé du script avant le déploiement dans ASDK](media/asdk-install/6.PNG)

    > [!TIP]
    > Ici, vous pouvez également copier les commandes de configuration PowerShell qui seront utilisées pour installer le kit ASDK. C’est utile si vous avez besoin de [redéployer le kit ASDK sur l’ordinateur hôte à l’aide de PowerShell](asdk-deploy-powershell.md).

8. Si vous utilisez un déploiement Azure AD, vous serez invité à entrer vos informations d’identification du compte administrateur général quelques minutes après le lancement de la configuration.

9. Le processus de déploiement peut prendre quelques heures, au cours desquelles l’ordinateur hôte ne redémarre automatiquement qu’une seule fois. Si vous voulez surveiller la progression du déploiement, connectez-vous en tant que azurestack\AzureStackAdmin après le redémarrage de l’hôte ASDK. Quand le déploiement est terminé, la console PowerShell affiche le message suivant : **TERMINÉ : Action « Déploiement »** . 
    > [!IMPORTANT]
    > Si vous vous connectez en tant qu’administrateur local une fois que la machine est jointe au domaine azurestack, vous ne voyez pas la progression du déploiement. Ne réexécutez pas le déploiement : au lieu de cela, connectez-vous en tant que azurestack\AzureStackAdmin pour vérifier qu’il est en cours d’exécution.

    ![Réussite du déploiement ASDK](media/asdk-install/7.PNG)

Félicitations, vous avez installé le kit ASDK !

Si le déploiement échoue pour la même raison, vous pouvez effectuer un [redéploiement](asdk-redeploy.md) depuis le début ou utiliser les commandes PowerShell suivantes pour redémarrer le déploiement à partir de la dernière étape réussie. Les commandes peuvent être utilisées à partir de la même fenêtre PowerShell avec élévation de privilèges :

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>Étapes suivantes
[Configuration post-déploiement](asdk-post-deploy.md)
