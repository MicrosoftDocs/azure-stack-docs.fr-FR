---
title: Utiliser l’action de connexion Azure avec Azure CLI et PowerShell sur Azure Stack Hub
description: Utilisez l’action de connexion Azure avec Azure CLI et PowerShell pour créer un workflow d’intégration continue et déploiement continu (CI/CD) sur Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 1/11/2021
ms.author: mabrigg
ms.reviewer: gara
ms.lastreviewed: 1/11/2021
ms.openlocfilehash: 1421917f870d09d61f665a2cee6eb9b617ae75f3
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102187348"
---
# <a name="use-the-azure-login-action-with-azure-cli-and-powershell-on-azure-stack-hub"></a>Utiliser l’action de connexion Azure avec Azure CLI et PowerShell sur Azure Stack Hub

Vous pouvez configurer GitHub Actions pour vous connecter à votre instance Azure Stack Hub, exécuter PowerShell, puis exécuter un script Azure CLI. Vous pouvez utiliser ces actions comme la base d’un workflow d’intégration continue et déploiement continu (CI/CD) pour votre solution avec Azure Stack Hub. Avec ce workflow, vous pouvez automatiser la génération, le test et le déploiement de votre solution de sorte à vous concentrer sur l’écriture du code. Par exemple, en ajoutant d’autres actions, vous pouvez utiliser ce workflow de concert avec un modèle Azure Resource Manager pour provisionner une machine virtuelle, valider un dépôt d’application, puis déployer une application sur cette machine virtuelle à chaque fusion avec une branche spécifique dans GitHub. Pour le moment, cet article vise à vous familiariser avec GitHub Actions et Azure Stack Hub.

La fonctionnalité GitHub Actions correspond à des workflows composés d’actions qui permettent une automatisation directe dans votre dépôt de code. Vous pouvez déclencher les workflows avec des événements dans votre processus de développement GitHub. Vous pouvez effectuer des tâches d’automatisation DevOps courantes, comme le test, le déploiement et l’intégration continue.

Pour utiliser GitHub Actions avec Azure Stack Hub, vous devez utiliser un principal du service (SPN) avec des conditions spécifiques. Dans cet article, vous allez créer un *exécuteur auto-hébergé*. GitHub vous permet d’utiliser n’importe quel machine accessible par GitHub dans vos GitHub Actions. Vous pouvez créer une machine virtuelle en tant qu’exécuteur dans Azure, dans Azure Stack Hub ou ailleurs.

Cet exemple de workflow comprend :
- Des instructions sur la création et la validation de votre SPN.
- La configuration d’une machine Windows 2016 Server Core en tant qu’exécuteur auto-hébergé GitHub Actions pour utiliser Azure Stack Hub.
- Un workflow qui utilise :
    - L’action de connexion Azure
    - L’action du script PowerShell

### <a name="azure-stack-hub-github-actions"></a>GitHub Actions avec Azure Stack Hub

Le diagramme suivant illustre les différents environnements et leurs relations.

![GitHub Actions avec Azure Stack Hub](.\media\ci-cd-github-action-login-cli\ash-github-actions-v1d1.svg) Composants de l’utilisation de l’exécuteur auto-hébergé :

- Fonctionnalité GitHub Actions hébergée sur GitHub
- Exécuteur auto-hébergé hébergé sur Azure
- Azure Stack Hub

Une limite à l’utilisation de GitHub Actions avec Azure Stack Hub est que le processus exige l’utilisation d’un système Azure Stack Hub connecté au web. Le workflow est déclenché dans un dépôt GitHub. Vous pouvez utiliser à la fois Azure Active Directory (Azure AD) ou les services de fédération Active Directory (AD FS).

Bien que cela sorte du cadre de cet article, votre exécuteur auto-hébergé peut aussi utiliser un réseau privé virtuel pour se connecter à votre service Azure Stack Hub derrière un pare-feu.

## <a name="get-service-principal"></a>Obtenir un principal du service

Un principal du service (SPN) fournit des informations d’identification basées sur les rôles afin que les processus hors Azure puissent se connecter aux ressources et interagir avec elles. Vous avez besoin d’un SPN doté d’un accès contributeur et des attributs spécifiés dans ces instructions à utiliser avec votre fonctionnalité GitHub Actions.

En tant qu’utilisateur d’Azure Stack Hub, vous n’êtes pas autorisé à créer le SPN. Vous avez besoin de demander ce principe à votre opérateur cloud. Les instructions sont fournies ici pour vous permettre de créer le SPN si vous êtes opérateur cloud, ou bien vous pouvez valider le SPN si vous êtes développeur en utilisant un SPN inclus dans votre workflow fourni par un opérateur cloud.

L’opérateur cloud doit créer le SPN avec Azure CLI.

Les extraits de code suivants sont écrits pour une machine Windows en utilisant l’invite PowerShell avec Azure CLI. Si vous utilisez l’interface CLI sur une machine Linux et Bash, supprimez l’extension de ligne ou remplacez-la par `\`.

1. Préparez les valeurs des paramètres suivants utilisés pour créer le SPN :

    | Paramètre | Exemple | Description |
    | --- | --- | --- |
    endpoint-resource-manager | "https://management.orlando.azurestack.corp.microsoft.com"  | Point de terminaison de gestion des ressources. |
    suffix-storage-endpoint | « orlando.azurestack.corp.microsoft.com »  | Suffixe de point de terminaison pour les comptes de stockage. |
    suffix-keyvault-dns | « .vault.orlando.azurestack.corp.microsoft.com »  | Suffixe DNS du service Key Vault. |
    endpoint-active-directory-graph-resource-id | "https://graph.windows.net/"  | L’ID de ressource Active Directory. |
    endpoint-sql-management | https://notsupported  | Point de terminaison de gestion SQL Server. À définir avec la valeur `https://notsupported` |
    profile | 2019-03-01-hybrid | Profil à utiliser pour ce cloud. |

2. Ouvrez votre outil en ligne de commande, comme Windows PowerShell ou Bash et connectez-vous. Utilisez la commande suivante :

    ```azurecli  
    az login
    ```

3. Utilisez la commande `register` pour un nouvel environnement ou la commande `update` si vous utilisez un environnement existant. Utilisez la commande suivante.

    ```azurecli  
    az cloud register `
        -n "AzureStackUser" `
        --endpoint-resource-manager "https://management.<local>.<FQDN>" `
        --suffix-storage-endpoint ".<local>.<FQDN>" `
        --suffix-keyvault-dns ".vault.<local>.<FQDN>" `
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" `
        --endpoint-sql-management https://notsupported  `
        --profile 2019-03-01-hybrid
    ```

4. Obtenez votre ID d’abonnement et le groupe de ressources que vous voulez utiliser pour le SPN.

5. Créez le SPN avec la commande suivante à l’aide de l’ID d’abonnement et du groupe de ressources :

    ```azurecli  
    az ad sp create-for-rbac --name "myApp" --role contributor `
        --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} `
        --sdk-auth
    ```

6. Vérifiez l’objet JSON obtenu. Vous allez utiliser cet objet JSON pour créer votre secret dans votre dépôt GitHub qui contient votre action. L’objet JSON doit avoir les attributs suivants :

    ```json
    {
      "clientId": <Application ID for the SPN>,
      "clientSecret": <Client secret for the SPN>,
      "subscriptionId": <Subscription ID for the SPN>,
      "tenantId": <Tenant ID for the SPN>,
      "activeDirectoryEndpointUrl": "https://login.microsoftonline.com/",
      "resourceManagerEndpointUrl": "https://management.<FQDN>",
      "activeDirectoryGraphResourceId": "https://graph.windows.net/",
      "sqlManagementEndpointUrl": "https://notsupported",
      "galleryEndpointUrl": "https://providers.<FQDN>:30016/",
      "managementEndpointUrl": "https://management.<FQDN>"
    }
    ```

## <a name="add-service-principal-to-repository"></a>Ajouter un principal du service au dépôt

Vous pouvez utiliser des secrets GitHub pour chiffrer les informations sensibles à utiliser dans vos actions. Vous allez créer un secret qui va contenir votre SPN afin que l’action puisse se connecter à votre instance Azure Stack Hub.

> [!WARNING]  
> GitHub recommande de ne pas utiliser des exécuteurs auto-hébergés avec des dépôts publics. Les duplications (forks) de votre dépôt public risquent d’exécuter du code dangereux sur votre machine qui sert d’exécuteur auto-hébergé en créant une demande de tirage (pull request) qui exécute le code dans un workflow. Pour plus d’informations, consultez « [About self-hosted runners](https://docs.github.com/en/free-pro-team@latest/github/automating-your-workflow-with-github-actions/about-self-hosted-runners#self-hosted-runner-security-with-public-repositories) ».

1. Ouvrez ou créez un dépôt GitHub. Si vous avez besoin d’aide pour créer un dépôt dans GitHub, vous trouverez des [instructions dans la documentation de GitHub](https://docs.github.com/en/free-pro-team@latest/github/getting-started-with-github/create-a-repo).
1. Définissez votre dépôt comme étant privé.
    1. Sélectionnez **Settings** > **Change repository visibility** (Paramètres > Changer la visibilité du dépôt).
    1. Sélectionnez **Make private** (Rendre privé).
    1. Tapez le nom de votre dépôt.
    1. Sélectionnez **I understand, change the repository visibility** (Je comprends, changer la visibilité du dépôt).
1. Sélectionnez **Paramètres**.
1. Sélectionnez **Secrets**.
1. Sélectionnez **New repository secret** (Nouveau secret de dépôt).
    ![Ajoutez votre secret GitHub Actions.](.\media\ci-cd-github-action-login-cli\github-action-secret.png)
1. Nommez votre secret `AZURE_CREDENTIALS`.
1. Collez l’objet JSON qui représente votre SPN.
1. Sélectionnez **Ajouter un secret**.

## <a name="create-your-vm-and-install-prerequisites"></a>Créer votre machine virtuelle et installer les prérequis

1. Créez votre exécuteur auto-hébergé. 

    Ces instructions créent un exécuteur sous forme de machine virtuelle Windows dans Azure. Si vous avez besoin de vous connecter à votre service Azure Stack Hub hébergé dans un centre de données, vous pouvez avoir besoin d’une connexion VPN. Vous trouverez des instructions sur l’activation de cette connexion dans la section [Installer les outils Azure Stack Hub sur votre exécuteur auto-hébergé](#optional-install-azure-stack-hub-tools-on-your-self-hosted-runner) qui a éventuellement besoin d’une connexion VPN.
    - Pour obtenir des conseils sur la création d’une machine virtuelle Windows dans Azure, consultez [Démarrage rapide : Création d’une machine virtuelle Windows dans le portail Azure](/azure/virtual-machines/windows/quick-create-portal). Quand vous suivez ces instructions, installez Windows Server 2016 Core.
    - Pour obtenir des conseils sur la création d’une machine virtuelle Windows dans Azure Stack Hub, consultez [Démarrage rapide : Créez une machine virtuelle Windows Server avec le portail Azure Stack Hub](./azure-stack-quick-windows-portal.md). Quand vous suivez ces instructions, installez Windows Server 2016 Core.
1. Utilisez une connexion à distance pour vous connecter à votre serveur Windows 2016 à l’aide de l’adresse IP du serveur, du nom d’utilisateur et du mot de passe que vous avez définis lors de la création de la machine.
1. Installez Chocolatey. Chocolatey est un gestionnaire de package pour Windows que vous pouvez utiliser pour installer et gérer des dépendances à partir de la ligne de commande. À partir d’une invite PowerShell avec des privilèges élevés :
    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
    ```
1. Installez PowerShell Core. À partir d’une invite PowerShell avec des privilèges élevés :
    ```powershell  
    choco install powershell-core
    ```
1. Installez Azure CLI. À partir d’une invite PowerShell avec des privilèges élevés :
    ```powershell  
    choco install azure-cli
    ```
1. Installez Azure Stack Hub PowerShell. À partir d’une invite PowerShell avec des privilèges élevés :
    ```powershell  
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

    Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
    Install-AzProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease
    ```
    Pour plus d’informations sur l’utilisation des modules Az pour Azure Stack Hub, consultez [Installer le module PowerShell Az pour Azure Stack Hub](../operator/powershell-install-az-module.md).
7. Redémarrez votre machine. À partir d’une invite PowerShell avec des privilèges élevés :
    ```powershell  
    shutdown /r
    ```
8. Ajoutez la machine en tant qu’exécuteur auto-hébergé à votre dépôt GitHub. Vous trouverez des instructions sur l’ajout d’un exécuteur auto-hébergé dans la documentation de GitHub. Pour plus d’informations, consultez [Ajout d’exécuteurs auto-hébergés](https://docs.github.com/en/free-pro-team@latest/actions/hosting-your-own-runners/adding-self-hosted-runners).

    ![L’exécuteur est en train d’écouter.](.\media\ci-cd-github-action-login-cli\github-action-runner-listen.png)

9. Quand vous avez terminé, vérifiez que le service est en cours d’exécution et que l’exécuteur l’écoute. Revérifiez en exécutant `/run.cmd` à partir du répertoire de l’exécuteur.

### <a name="optional-install-azure-stack-hub-tools-on-your-self-hosted-runner"></a>Facultatif : Installer les outils Azure Stack Hub sur exécuteur auto-hébergé

Les instructions données dans cet article ne nécessitent pas d’accès aux [outils Azure Stack Hub](../operator/azure-stack-powershell-download.md?tabs=az), mais quand vous allez développer votre propre workflow, vous risquez d’avoir besoin de les utiliser. Les instructions qui suivent peuvent vous aider à installer les outils sur votre exécuteur auto-hébergé Windows. Pour plus d’informations sur les outils Azure Stack Hub, consultez [Télécharger les outils Azure Stack Hub à partir de GitHub](../operator/azure-stack-powershell-download.md?tabs=az). Ces instructions partent du principe que vous avez installé le gestionnaire de package Chocolatey.

1. Installez Git.
    ```powershell  
    choco install git
    ```

2. À partir d’une invite PowerShell avec des privilèges élevés :
    ```powershell
    # Change directory to the root directory.
    cd \
    
    # Download the tools archive.
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
    invoke-webrequest `
      https://github.com/Azure/AzureStack-Tools/archive/az.zip `
      -OutFile az.zip
    
    # Expand the downloaded files.
    expand-archive az.zip `
      -DestinationPath . `
      -Force
    
    # Change to the tools directory.
    cd AzureStack-Tools-az
    ```

3. Si vous avez besoin que votre exécuteur se connecte à votre instance Azure Stack Hub, vous pouvez utiliser PowerShell. Vous trouverez les instructions correspondantes dans l’article [Se connecter à Azure Stack Hub avec PowerShell](../operator/azure-stack-powershell-configure-admin.md?tabs=az1%2Caz2%2Caz3).

## <a name="create-a-self-hosted-runner"></a>Créer un exécuteur auto-hébergé

Vous pouvez configurer un exécuteur auto-hébergé dans la documentation de GitHub. Un exécuteur auto-hébergé peut s’exécuter sur une machine capable de se connecter à GitHub. Vous pouvez choisir d’utiliser un exécuteur auto-hébergé si vous avez une tâche d’automatisation dans votre workflow qui nécessite beaucoup de dépendances, des conditions de licence spécifiques, comme un dongle USB pour une licence logicielle, voire d’autres conditions propre à la machine ou au logiciel. Votre machine peut être un ordinateur physique, une machine virtuelle ou un conteneur. Vous pouvez placer l’exécuteur dans votre centre de données ou dans le cloud.

Dans cet article, vous allez utiliser une machine virtuelle Windows hébergée dans Azure et configurée selon des exigences PowerShell propres à Azure Stack Hub.

Pour obtenir des instructions sur l’installation, la configuration et la connexion de votre exécuteur auto-hébergé sur votre dépôt, consultez « [About self-hosted runners](https://docs.github.com/en/free-pro-team@latest/actions/hosting-your-own-runners/about-self-hosted-runners) » dans la documentation de GitHub.

![Exécuteur auto-hébergé connecté](.\media\ci-cd-github-action-login-cli\github-actions-self-hosted-runner.png)

Notez le nom et les étiquettes de votre exécuteur auto-hébergé. Le workflow de cet article l’appelle en utilisant l’étiquette `self-hosted`.

## <a name="add-the-workflow-to-your-repository"></a>Ajouter le workflow à votre dépôt

Créez un workflow en utilisant le code YALM indiqué dans cette section pour créer votre workflow.

1. Ouvrez votre dépôt GitHub.
2. Sélectionnez **Actions**.
3. Créez un workflow.
    - S’il s’agit de votre premier workflow, sélectionnez **Set up a workflow yourself** (Configurer un workflow vous-même) sous **Choose a workflow template** (Choisir un modèle de workflow).
    - Si vous avez des workflows existants, sélectionnez **New workflow** > **Set up a workflow yourself** (Nouveau workflow > Configurer un workflow vous-même).

4. Dans le chemin, nommez le fichier `workflow.yml`.
5. Copiez et collez le ficher yml du workflow.
    ```yaml  
    on: [push]
    
    env:
      ACTIONS_ALLOW_UNSECURE_COMMANDS: 'true'
     
    jobs: 
      azurestack-test:
        runs-on: self-hosted
        steps:
    
          - name: Login to AzureStack with Az Powershell
            uses: azure/login@releases/v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
              environment: 'AzureStack'
              enable-AzPSSession: true
          
          - name: Run Az PowerShell Script Against AzureStack
            uses: azure/powershell@v1
            with:
              azPSVersion: '3.1.0'
              inlineScript: |
                hostname
                Get-AzContext
                Get-AzResourceGroup
          
          - name: Login to AzureStack with CLI
            uses: azure/login@releases/v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
              environment: 'AzureStack'
              enable-AzPSSession: false
          
          - name: Run Azure CLI Script Against AzureStack
            run: |
              hostname
              az group list --output table
    ```
6. Sélectionnez **Démarrer la validation**.
7. Ajoutez le titre du commit et les détails facultatifs, puis sélectionnez **Commit new file** (Commiter un nouveau fichier).

Quand l’action s’exécute, vérifiez qu’elle a bien abouti.

1. Ouvrez votre dépôt GitHub. Vous pouvez déclencher le workflow par un envoi (push) au dépôt.
1. Sélectionnez **Actions**.
1. Sélectionnez le nom du commit sous **All workflows** (Tous les workflows).

    ![Passer en revue le résumé du commit](.\media\ci-cd-github-action-login-cli\github-actions-review-log-summary.png)
1. Sélectionnez le nom du travail, **azurestack-test**.

    ![Passer en revue les détails du commit](.\media\ci-cd-github-action-login-cli\github-action-success-screen.png)
1. Développez les sections pour passer en revue les valeurs renvoyées de vos commandes PowerShell et CLI.

Remarques sur le fichier et l’action du workflow :

- Le workflow contient un seul travail nommé `azurestack-test`.
- Un événement d’envoi (push) déclenche le workflow.
- L’action utilise un exécuteur auto-hébergé qui a été configuré dans le dépôt et elle est appelée par l’étiquette de l’exécuteur dans le workflow avec la ligne : `runs on: self-hosted`.
- Le workflow contient trois actions.
- La première action appelle l’action de connexion Azure pour se connecter à PowerShell avec GitHub Actions pour Azure. Vous pouvez créer des workflows que vous pouvez configurer dans votre dépôt pour générer, tester, empaqueter, publier et déployer sur Azure. Cette action utilise les informations d’identification de votre SPN Azure Stack pour se connecter et ouvrir une session dans votre environnement Azure Stack Hub. Pour obtenir plus d’informations sur l’utilisation de l’action dans GitHub, consultez [Azure Login Action](https://github.com/marketplace/actions/azure-login).
- La deuxième action utilise Azure PowerShell. L’action utilise les modules PowerShell Az et fonctionne avec les clouds Government et Azure Stack Hub. Une fois que vous avez exécuté ce workflow, passez en revue le travail pour vérifier que le script a collecté les groupes de ressources dans votre environnement Azure Stack Hub. Pour plus d’informations, consultez [Azure PowerShell Action](https://github.com/marketplace/actions/azure-powershell-action).
- La troisième action utilise Azure CLI pour se connecter à votre service Azure Stack Hub afin de collecter les groupes de ressources. Pour plus d’informations, consultez [Azure CLI Action](https://github.com/marketplace/actions/azure-cli-action).
- Pour plus d’informations sur l’utilisation de GitHub Actions et d’un exécuteur auto-hébergé, consultez la documentation de [GitHub Actions](https://github.com/features/actions).

## <a name="next-steps"></a>Étapes suivantes

- Recherchez d’autres actions dans la [marketplace de GitHub](https://github.com/marketplace).
- Découvrez les [déploiements courants pour Azure Stack Hub](azure-stack-dev-start-deploy-app.md).  
- Découvrez comment [utiliser les modèles Azure Resource Manager dans Azure Stack Hub](azure-stack-arm-templates.md).  
- Passez en revue le [modèle cloud hybride DevOps](/hybrid/app-solutions/pattern-cicd-pipeline).
