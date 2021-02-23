---
title: Utiliser l’action de déploiement d’applications web Azure avec Azure Stack Hub
description: Utiliser l’action de déploiement d’applications web Azure pour créer un workflow d’intégration et de déploiement continus (CI/CD) sur Azure Stack Hub
author: mattbriggs
ms.topic: how-to
ms.date: 2/16/2021
ms.author: mabrigg
ms.reviewer: gara
ms.lastreviewed: 2/16/2021
ms.openlocfilehash: fec9acb7a3e156a8646aef88c53e681eed9e53da
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100655273"
---
# <a name="use-the-azure-web-app-deploy-action-with-azure-stack-hub"></a>Utiliser l’action de déploiement d’application web Azure avec Azure Stack Hub

Vous pouvez configurer la fonctionnalité GitHub Actions pour déployer une application web sur votre instance Azure Stack Hub. Cela vous permet de configurer l’intégration et le déploiement continus pour votre application. Cet article vous aide à devenir opérationnel avec le déploiement automatisé à l’aide de la fonctionnalité GitHub Actions et d’Azure Stack Hub. Vous allez créer une application web et utiliser le profil de publication pour créer l’application web destinée à héberger votre application.

La fonctionnalité GitHub Actions correspond à des workflows composés d’actions qui permettent une automatisation directe dans votre dépôt de code. Vous pouvez déclencher les workflows avec des événements dans votre processus de développement GitHub. Vous pouvez définir des tâches d’automatisation DevOps courantes, comme le test, le déploiement et l’intégration continue.

Cet exemple de workflow comprend :
- Instructions sur la création et la validation de votre principal de service (SPN).
- Instructions sur la création de votre profil de publication d’application web
- Ajout d’un flux de travail spécifique du runtime
- Ajout d’un flux de travail correspondant avec un déploiement d’application web
## <a name="get-service-principal"></a>Obtenir un principal du service

Un principal du service (SPN) fournit des informations d’identification basées sur les rôles afin que les processus hors Azure puissent se connecter aux ressources et interagir avec elles. Vous allez avoir besoin d’un SPN doté d’un accès contributeur et des attributs spécifiés dans ces instructions à utiliser avec votre fonctionnalité GitHub Actions.

En tant qu’utilisateur d’Azure Stack Hub, vous n’êtes pas autorisé à créer le SPN. Vous allez devoir demander ce principe à votre opérateur cloud. Les instructions sont fournies ici pour vous permettre de créer le SPN si vous êtes un opérateur cloud. Ou bien, si vous êtes un développeur, vous pouvez valider le SPN fourni par un opérateur cloud.

L’opérateur cloud doit créer le SPN avec Azure CLI.

Les extraits de code suivants sont écrits pour une machine Windows en utilisant l’invite PowerShell avec Azure CLI. Si vous utilisez l’interface de ligne de commande sur une machine Linux et bash, supprimez la rallonge de ligne ou remplacez-la par le signe `\`.

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

    Si vous n’avez pas de privilège d’opérateur cloud, vous pouvez également vous connecter avec le SPN fourni par votre opérateur cloud. Vous aurez besoin de l’ID client, du secret et de votre ID de locataire. Avec ces valeurs, vous pouvez utiliser les commandes Azure CLI suivantes pour créer l’objet JSON que vous pouvez ajouter à votre dépôt GitHub en tant que secret.

    ```azurecli  
    az login --service-principal -u "<client-id>" -p "<secret>" --tenant "<tenant-ID>" --allow-no-subscriptions
    az account show --sdk-auth
    ```

6. Vérifiez l’objet JSON obtenu. Vous allez utiliser l’objet JSON pour créer votre secret dans votre dépôt GitHub contenant votre action. L’objet JSON doit avoir les attributs suivants :

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

## <a name="create-the-web-app-publish-profile"></a>Créer le profil de publication d’application web

### <a name="open-the-create-web-app-blade"></a>Ouvrir le panneau de création d’application web

1. Connectez-vous au portail Azure Stack Hub.
1. Sélectionnez **Créer une ressource** > **Web + Mobile** > **Application web**.
    ![Créer une application web dans Azure Stack Hub](\media\ci-cd-github-action-webapp\create-web-app.png)
### <a name="to-create-your-web-app"></a>Pour créer votre application web

1. Sélectionnez votre **abonnement**.
1. Créez ou sélectionnez un **Groupe de ressources**.
1. Tapez le **Nom** de votre application. Le nom de l’application s’affiche dans l’URL de votre application, par exemple, `yourappname.appservice.<region>.<FQDN>`.
1. Sélectionnez la **pile d’exécution** pour votre application. Le runtime doit correspondre au flux de travail que vous utilisez pour cibler votre profil de publication.
1. Sélectionnez le **Système d’exploitation** (SE) qui hébergera votre runtime et votre application.
1. Sélectionnez ou tapez la **Région** de votre instance Azure Stack Hub.
1. Sélectionnez le plan en fonction de votre instance Azure Stack Hub, de la région et du système d’exploitation de l’application.
1. Sélectionnez **Vérifier + créer**.
1. Examinez votre application web. Sélectionnez **Create** (Créer).
    ![Examiner une application web dans Azure Stack Hub](\media\ci-cd-github-action-webapp\review-azure-stack-hub-web-app.png)
1. Sélectionnez **Accéder à la ressource**.
    ![Obtenir un profil de publication dans Azure Stack Hub](\media\ci-cd-github-action-webapp\get-azure-stack-hub-web-app-publish-profile.png)
1. Sélectionnez **Obtenir le profil de publication**. Votre profil de publication est téléchargé et nommé `<yourappname>.PublishSettings` . Le fichier contient un code XML avec les valeurs cibles de votre application web.
1. Stockez votre profil de publication afin de pouvoir y accéder lorsque vous créez les secrets pour votre dépôt.

## <a name="add-your-secrets-to-the-repository"></a>Ajouter vos secrets au dépôt

Vous pouvez utiliser des secrets GitHub pour chiffrer les informations sensibles à utiliser dans vos actions. Vous allez créer un secret pour contenir votre SPN, et un autre pour contenir votre profil de publication d’application web, afin que l’action puisse se connecter à votre instance Azure Stack Hub et générer votre application sur la cible d’application web.

1. Ouvrez ou créez un dépôt GitHub. Si vous avez besoin d’aide pour créer un dépôt dans GitHub, vous trouverez des [instructions dans la documentation de GitHub](https://docs.github.com/en/free-pro-team@latest/github/getting-started-with-github/create-a-repo).
1. Sélectionnez **Paramètres**.
1. Sélectionnez **Secrets**.
1. Sélectionnez **New repository secret** (Nouveau secret de dépôt).
    ![Ajoutez votre secret GitHub Actions.](.\media\ci-cd-github-action-login-cli\github-action-secret.png)
1. Nommez votre secret `AZURE_CREDENTIALS`.
1. Collez l’objet JSON qui représente votre SPN.
1. Sélectionnez **Ajouter un secret**.
1. Sélectionnez **New repository secret** (Nouveau secret de dépôt).
1. Nommez votre secret `AZURE_WEBAPP_PUBLISH_PROFILE`.
1. Ouvrez votre `<yourappname>.PublishSettings` dans un éditeur de texte, puis copiez et collez le code XML dans le secret du dépôt.
1. Sélectionnez **Ajouter un secret**.

## <a name="add-a-runtime-workflow"></a>Ajouter un flux de travail de runtime

1. Choisissez un modèle dans la table pour le runtime de votre application web.

    | Runtime | Modèle |
    | --- | --- |
    | DotNet | [dotnet.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/asp.net-core-webapp-on-azure.yml) |
    | NodeJS | [node.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/node.js-webapp-on-azure.yml) |
    | Java | [java_jar.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/java-jar-webapp-on-azure.yml) |
    | Java | [java_war.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/java-war-webapp-on-azure.yml) |
    | Python | [python.yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/python-webapp-on-azure.yml) |
    | PHP | [php.yml](https://github.com/Azure/actions-workflow-samples/blob/master/AppService/php-webapp-on-azure.yml) |
    | Docker | [docker.yml](https://github.com/Azure/actions-workflow-samples/blob/master/AppService/docker-webapp-container-on-azure.yml) |

2. Placez le modèle de répertoire de flux de travail d’action GitHub dans le dépôt de votre projet : `.github/workflows/<runtime.yml>` Votre répertoire de flux de travail contiendra deux flux de travail.

## <a name="add-the-web-app-deploy-action"></a>Ajouter l’action de déploiement d’application web

Créez un deuxième flux de travail en utilisant le code YAML figurant dans cette section. Dans cet exemple, vous déployez une application web Python. Vous devez sélectionner une action de configuration en fonction de votre flux de travail. Vous pouvez trouver des références pour configurer des actions pour différents runtimes dans le tableau [Actions d’installation pour différents runtimes](#setup-actions-for-different-runtimes) après les étapes utilisées pour créer l’action.

### <a name="example-github-action-workflow"></a>Exemple de flux de travail d’action GitHub

1. Ouvrez votre dépôt GitHub. Si vous n’avez pas encore ajouté les ressources de votre application web, ajoutez-les maintenant. J’utilise ici l’exemple [Python Flask Hello World](https://github.com/Azure-Samples/python-docs-hello-world) et j’ai ajouté les fichiers Python `.gitignore`, `app.py` et `requirements.txt`.

    ![Exemple de dépôt utilisant Python Flask avec Azure Stack Hub](\media\ci-cd-github-action-webapp\example-of-repo.png)
1. Sélectionnez **Actions**.
1. Sélectionnez **Nouveau workflow**.
    - S’il s’agit de votre premier workflow, sélectionnez **Set up a workflow yourself** (Configurer un workflow vous-même) sous **Choose a workflow template** (Choisir un modèle de workflow).
    - Si vous avez des workflows existants, sélectionnez **New workflow** > **Set up a workflow yourself** (Nouveau workflow > Configurer un workflow vous-même).

4. Dans le chemin, nommez le fichier `workflow.yml`.
5. Copiez et collez le ficher yml du workflow.
    ```yaml  
    # .github/workflows/worfklow.yml
    on: push

    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout Github Action' 
          uses: actions/checkout@master
    
        - name: Setup Python 3.6
          uses: actions/setup-node@v1
          with:
            python-version: '3.6'
        - name: 'create a virtual environment and install dependencies'
          run: |
            python3 -m venv .venv
            source .venv/bin/activate
            pip install -r requirements.txt
    
        - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v2
          with:
            app-name: <YOURAPPNAME>
            publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
    ```

6. Dans le code YAML du flux de travail, remplacez `<YOURAPPNAME>` par le nom de votre application.
7. Sélectionnez **Démarrer la validation**.
8. Ajoutez le titre du commit et les détails facultatifs, puis sélectionnez **Commit new file** (Commiter un nouveau fichier).


### <a name="setup-actions-for-different-runtimes"></a>Actions d’installation pour différents runtimes

Pour générer du code d’application dans un environnement basé sur un langage spécifique, utilisez des actions d’installation :

|  Runtime | Actions d’installation |
|------------|---------|
| DotNet     | [Setup DotNet](https://github.com/actions/setup-dotnet) |
| NodeJS     | [Setup Node](https://github.com/actions/setup-node) |
| Java     | [Setup Java  ](https://github.com/actions/setup-java) |
| Python     | [Setup Python ](https://github.com/actions/setup-python) |
| Docker | [docker-login ](https://github.com/Azure/docker-login) |

Une fois l’action de connexion effectuée, l’ensemble suivant d’actions dans le flux de travail peut effectuer des tâches telles que la génération, le balisage et le push de conteneurs. Pour plus d’informations, consultez la documentation relative à l'[action d’application web Azure](https://github.com/marketplace/actions/azure-webapp).
## <a name="trigger-your-deployment"></a>Déclencher votre déploiement

Quand l’action s’exécute, vérifiez qu’elle a bien abouti.

1. Ouvrez votre dépôt GitHub. Vous pouvez déclencher le workflow par un envoi (push) au dépôt.
1. Sélectionnez **Actions**.
1. Sélectionnez le nom du commit sous **All workflows** (Tous les workflows). Les deux flux de travail ont journalisé leur état.
    ![Examiner l’état de votre action GitHub](\media\ci-cd-github-action-webapp\workflow-success.png)
1. Sélectionnez le nom du travail pour le déploiement, `.github/workflows/workflow.yml`.
1. Développez les sections afin d’examiner les valeurs renvoyées pour vos actions de flux de travail. Recherchez l’URL de votre application web déployée.
    ![Rechercher l’URL de votre application web Azure Stack Hub](\media\ci-cd-github-action-webapp\review-cli-log-and-get-url.png)
1. Ouvrez un navigateur web et chargez l’URL.
## <a name="next-steps"></a>Étapes suivantes

- Recherchez d’autres actions dans la [marketplace de GitHub](https://github.com/marketplace).
- Découvrez les [déploiements courants pour Azure Stack Hub](azure-stack-dev-start-deploy-app.md).  
- Découvrez comment [utiliser les modèles Azure Resource Manager dans Azure Stack Hub](azure-stack-arm-templates.md).  
- Passez en revue le [modèle cloud hybride DevOps](https://docs.microsoft.com/hybrid/app-solutions/pattern-cicd-pipeline).
