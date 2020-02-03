---
title: Utiliser le point de terminaison privilégié dans Azure Stack Hub
description: Découvrez comment utiliser le point de terminaison privilégié dans Azure Stack Hub en tant qu’opérateur.
author: mattbriggs
ms.topic: article
ms.date: 1/8/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 1/8/2020
ms.openlocfilehash: 74d21915b7df5161817c0cf48ec7db6f06cbd904
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76881523"
---
# <a name="use-the-privileged-endpoint-in-azure-stack-hub"></a>Utiliser le point de terminaison privilégié dans Azure Stack Hub

En tant qu’opérateur Azure Stack Hub, vous devez utiliser le portail administrateur, PowerShell ou les API Azure Resource Manager pour la plupart des tâches d’administration quotidiennes. Toutefois, pour certaines opérations moins courantes, vous devez utiliser le *point de terminaison privilégié* (PEP). Ce point de terminaison est une console PowerShell distante préconfigurée qui vous fournit suffisamment de fonctionnalités pour vous aider à effectuer une tâche nécessaire. Le point de terminaison s’appuie sur [PowerShell JEA (Just Enough Administration)](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview) pour exposer uniquement un ensemble limité d’applets de commande. Pour accéder au point de terminaison privilégié et appeler l’ensemble limité d’applets de commande, un compte à faibles privilèges est utilisé. Aucun compte d’administrateur n’est nécessaire. Pour plus de sécurité, les scripts ne sont pas autorisés.

Vous pouvez utiliser le points de terminaison privilégié pour effectuer les tâches suivantes :

- Des tâches de bas niveau, comme [collecter les journaux de diagnostic](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs).
- De nombreuses tâches post-déploiement d’intégration au centre de données pour les systèmes intégrés, comme l’ajout de redirecteurs DNS (Domain Name System), la configuration de l’intégration de Microsoft Graph, l’intégration des services de fédération Active Directory (AD FS), la permutation des certificats, etc.
- Collaborer avec l’équipe de support afin d’obtenir un accès global temporaire pour un dépannage approfondi d’un système intégré.

Le point de terminaison privilégié journalise chaque action (et sa sortie correspondante) que vous effectuez pendant la session PowerShell. Transparence totale et audit complet des opérations sont ainsi assurés. Vous pouvez conserver ces fichiers journaux en vue d’opérations d’audit futures.

> [!NOTE]
> Dans le Kit de développement Azure Stack (ASDK), vous pouvez exécuter certaines commandes disponibles dans le point de terminaison privilégié directement à partir d’une session PowerShell sur l’hôte du Kit de développement. Toutefois, vous pouvez être amené à tester certaines opérations à l’aide du point de terminaison privilégié, telles que la collecte de journaux, car c’est la seule méthode disponible pour effectuer certaines opérations dans un environnement de systèmes intégrés.

## <a name="access-the-privileged-endpoint"></a>Accéder au point de terminaison privilégié

Vous accédez au point de terminaison privilégié via une session PowerShell distante sur la machine virtuelle qui héberge le point de terminaison privilégié. Dans ASDK, cette machine virtuelle est nommée **AzS-ERCS01**. Si vous utilisez un système intégré, il existe trois instances du point de terminaison privilégié, chacune s’exécutant à l’intérieur d’une machine virtuelle (*Préfixe*-ERCS01, *Préfixe*-ERCS02 ou *Préfixe*-ERCS03) sur des hôtes différents à des fins de résilience.

Avant de commencer cette procédure pour un système intégré, vérifiez que vous pouvez accéder à un point de terminaison privilégié via une adresse IP ou via DNS. Après le déploiement initial d’Azure Stack Hub, vous pouvez accéder au point de terminaison privilégié seulement via une adresse IP, car l’intégration de DNS n’est pas encore configurée. Votre fournisseur de matériel OEM vous fournira un fichier JSON nommé **AzureStackStampDeploymentInfo** qui contient les adresses IP de point de terminaison privilégié.

Vous pouvez également trouver l’adresse IP dans le portail administrateur Azure Stack Hub. Ouvrez le portail, par exemple, `https://adminportal.local.azurestack.external`. Sélectionnez **Gestion des régions** > **Propriétés**.

Vous devez affecter la valeur `en-US` à votre paramètre de culture actuel au moment de l’exécution du point de terminaison privilégié. Sinon, les applets de commande telles que Test-AzureStack ou Get-AzureStackLog ne vont pas fonctionner comme prévu.

> [!NOTE]
> Pour des raisons de sécurité, vous devez vous connecter au point de terminaison privilégié uniquement à partir d’une machine virtuelle renforcée s’exécutant par-dessus l’hôte de cycle de vie du matériel, ou d’un ordinateur dédié et sécurisé, comme une [station de travail à accès privilégié](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations). La configuration d’origine de l’hôte de cycle de vie du matériel ne doit pas être modifiée (même lors de l’installation de nouveaux logiciels), ni utilisée pour se connecter au point de terminaison privilégié.

1. Établir une relation de confiance.

      - Sur un système intégré, exécutez la commande suivante à partir d’une session Windows PowerShell avec élévation de privilèges pour ajouter le point de terminaison privilégié en tant qu’hôte approuvé sur la machine virtuelle renforcée, qui s’exécute sur l’hôte de cycle de vie du matériel ou sur la station de travail à accès privilégié.

      ```powershell  
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```

      - Si vous exécutez le kit ASDK, connectez-vous à l’hôte du kit de développement.

2. Sur la machine virtuelle renforcée s’exécutant sur l’hôte de cycle de vie du matériel ou sur la station de travail à accès privilégié, ouvrez une session Windows PowerShell. Exécutez les commandes suivantes pour établir une session distante sur la machine virtuelle qui héberge le point de terminaison privilégié :
 
  - Sur un système intégré :

    ```powershell  
    $cred = Get-Credential

    $pep = New-PSSession -ComputerName <IP_address_of_ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
    Enter-PSSession $pep
    ```
    
    Le paramètre `ComputerName` peut être l’adresse IP ou le nom DNS de l’une des machines virtuelles qui héberge le point de terminaison privilégié.

    > [!NOTE]  
    >Azure Stack Hub n’effectue pas d’appel à distance lors de la validation des informations d’identification du point de terminaison privilégié. Pour les valider, il s’appuie sur une clé publique RSA stockée localement.

   - Si vous exécutez le kit ADSK :

     ```powershell  
      $cred = Get-Credential
    
      $pep = New-PSSession -ComputerName azs-ercs01 -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
      Enter-PSSession $pep
     ```
    
   - Quand vous y êtes invité, utilisez les informations d’identification suivantes :
   
       - **Nom d’utilisateur** : spécifiez le compte CloudAdmin, au format **&lt;*domaine Azure Stack Hub*&gt;\cloudadmin**. (Pour le Kit ASDK, le nom d’utilisateur est **azurestack\cloudadmin**.)
  
        - **Mot de passe** : entrez le mot de passe du compte d'administrateur de domaine AzureStackAdmin tel qu'il vous a été fourni pendant l'installation.

      > [!NOTE]
      > Si vous ne parvenez pas à vous connecter au point de terminaison ERCS, réessayez les étapes 1 et 2 avec l’adresse IP d’une autre machine virtuelle ERCS.

3. Une fois connecté, l'invite devient **[*adresse IP ou nom de la machine virtuelle ERCS*]: PS>** ou à **[azs-ercs01]: PS>** , en fonction de l'environnement. Depuis cette invite, exécutez `Get-Command` pour afficher la liste des applets de commande disponibles.

   Un grand nombre de ces applets de commande sont uniquement destinées aux environnements de système intégré (par exemple, les applets de commande associées à l’intégration au centre de données). Dans le Kit ASDK, les applets de commande suivantes ont été validées :

   - Clear-Host
   - Close-PrivilegedEndpoint
   - Exit-PSSession
   - Get-AzureStackLog
   - Get-AzureStackStampInformation
   - Get-Command
   - Get-FormatData
   - Get-Help
   - Get-ThirdPartyNotices
   - Measure-Object
   - New-CloudAdminUser
   - Out-Default
   - Remove-CloudAdminUser
   - Select-Object
   - Set-CloudAdminUserPassword
   - Test-AzureStack
   - Stop-AzureStack
   - Get-ClusterLog

## <a name="tips-for-using-the-privileged-endpoint"></a>Conseils d’utilisation du point de terminaison privilégié 

Comme mentionné ci-dessus, le point de terminaison privilégié est un point de terminaison [PowerShell JEA](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview). Tout en procurant une couche de sécurité renforcée, un point de terminaison JEA réduit certaines des fonctionnalités de base de PowerShell, comme l’écriture de scripts ou la saisie semi-automatique via la touche Tab. Toute tentative d’opération de script est vouée à l’échec et se solde par l’erreur **ScriptsNotAllowed**. Cette erreur est normale.

Par exemple, pour obtenir la liste des paramètres d’une applet de commande donnée, exécutez la commande suivante :

```powershell
    Get-Command <cmdlet_name> -Syntax
```

Sinon, vous pouvez utiliser l’applet de commande [Import-PSSession](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) pour importer l’ensemble des applets de commande de point de terminaison privilégié dans la session actuelle sur votre ordinateur local. En procédant ainsi, l’ensemble des applets de commande et des fonctions du point de terminaison sont désormais disponibles sur votre ordinateur local, avec la saisie semi-automatique via la touche Tab et, plus généralement, l’écriture de scripts.

Pour importer la session du point de terminaison privilégié sur votre ordinateur local, procédez ainsi :

1. Établir une relation de confiance.

    - Sur un système intégré, exécutez la commande suivante à partir d’une session Windows PowerShell avec élévation de privilèges pour ajouter le point de terminaison privilégié en tant qu’hôte approuvé sur la machine virtuelle renforcée, qui s’exécute sur l’hôte de cycle de vie du matériel ou sur la station de travail à accès privilégié.

    ```powershell
    winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
    ```

    - Si vous exécutez le kit ASDK, connectez-vous à l’hôte du kit de développement.

2. Sur la machine virtuelle renforcée s’exécutant sur l’hôte de cycle de vie du matériel ou sur la station de travail à accès privilégié, ouvrez une session Windows PowerShell. Exécutez les commandes suivantes pour établir une session distante sur la machine virtuelle qui héberge le point de terminaison privilégié :

    - Sur un système intégré :
    
      ```powershell  
        $cred = Get-Credential
      
        $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```
    
      Le paramètre `ComputerName` peut être l’adresse IP ou le nom DNS de l’une des machines virtuelles qui héberge le point de terminaison privilégié.

    - Si vous exécutez le kit ADSK :
     
        ```powershell  
          $cred = Get-Credential
    
          $session = New-PSSession -ComputerName azs-ercs01 `
             -ConfigurationName PrivilegedEndpoint -Credential $cred
        ```

     Quand vous y êtes invité, utilisez les informations d’identification suivantes :

     - **Nom d’utilisateur** : spécifiez le compte CloudAdmin, au format **&lt;*domaine Azure Stack Hub*&gt;\cloudadmin**. (Pour le Kit ASDK, le nom d’utilisateur est **azurestack\cloudadmin**.)
     - **Mot de passe** : entrez le mot de passe du compte d'administrateur de domaine AzureStackAdmin tel qu'il vous a été fourni pendant l'installation.

3. Importer la session du point de terminaison privilégié dans votre machine locale :

    ```powershell 
      Import-PSSession $session
    ```

4. Vous pouvez désormais utiliser la saisie semi-automatique via la touche Tab pour écrire des scripts comme de coutume sur votre session locale PowerShell à l’aide de l’ensemble des fonctions et des applets de commande du point de terminaison privilégié, sans réduire la sécurité d’Azure Stack Hub. Vous n’avez plus qu’à l’utiliser !

## <a name="close-the-privileged-endpoint-session"></a>Fermer la session du point de terminaison privilégié

 Comme indiqué plus haut, le point de terminaison privilégié journalise chaque action (et sa sortie correspondante) que vous effectuez dans la session PowerShell. Vous devez fermer la session à l’aide de la cmdlet `Close-PrivilegedEndpoint`. Cette applet de commande ferme correctement le point de terminaison et transfère les fichiers journaux vers un partage de fichiers externe à des fins de rétention.

Pour fermer la session du point de terminaison :

1. Créez un partage de fichiers externe qui est accessible au point de terminaison privilégié. Dans un environnement avec Kit de développement, vous pouvez simplement créer un partage de fichiers sur l’hôte du Kit de développement.
2. Exécutez l’applet de commande suivante :

  ```powershell  
     Close-PrivilegedEndpoint -TranscriptsPathDestination "\\fileshareIP\SharedFolder" -Credential Get-Credential
  ```

   L’applet de commande utilise les paramètres du tableau suivant :

   | Paramètre | Description | Type | Obligatoire |
   |---------|---------|---------|---------|
   | *TranscriptsPathDestination* | Chemin du partage de fichiers externe défini comme ceci : « adresse_IP_partage_fichiers\nom_dossier_partage » | String | Oui|
   | *Informations d'identification* | Informations d’identification pour accéder au partage de fichiers | SecureString |   Oui |


Une fois les fichiers journaux de transcription correctement transférés vers le partage de fichiers, ils sont automatiquement supprimés du point de terminaison privilégié. 

> [!NOTE]
> Si vous fermez la session du point de terminaison privilégié à l’aide des applets de commande `Exit-PSSession` ou `Exit`, ou que vous fermez simplement la console PowerShell, les journaux d’activité de transcription ne sont pas transférés vers un partage de fichiers. Ils demeurent dans le point de terminaison privilégié. La prochaine fois que vous exécutez `Close-PrivilegedEndpoint` et que vous incluez un partage de fichiers, les journaux d’activité de transcription issus de la (des) session(s) précédente(s) sont également transférés. N’utilisez pas `Exit-PSSession` ou `Exit` pour fermer la session du point de terminaison privilégié ; utilisez `Close-PrivilegedEndpoint`.


## <a name="next-steps"></a>Étapes suivantes

[Outils de diagnostic d’Azure Stack Hub](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)
