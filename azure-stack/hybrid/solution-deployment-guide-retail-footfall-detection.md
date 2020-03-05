---
title: Déployer une solution de détection des pas basé sur l’intelligence artificielle à l'aide d'Azure et Azure Stack Hub
description: Découvrez comment déployer une solution de détection des pas basé sur l’intelligence artificielle à l'aide d'Azure et Azure Stack Hub. Cette solution permet d'analyser la fréquentation de magasins de détail.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 3820d5483a0a7051ea51cc4ce7489d2cfe9f2d42
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77687493"
---
# <a name="deploy-an-ai-based-footfall-detection-solution-using-azure-and-azure-stack-hub"></a>Déployer une solution de détection des pas basé sur l’intelligence artificielle à l'aide d'Azure et Azure Stack Hub

Cet article explique comment déployer une solution fournissant des insights à partir d'actions du monde réel, en utilisant Azure, Azure Stack Hub, et le kit de développement d’intelligence Custom Vision.

Dans cette solution, vous allez apprendre à :

> [!div class="checklist"]
> - Déployer des offres CNAB (Cloud Native Application Bundles) en périphérie. 
> - Déployer une application qui s’étend aux limites du cloud.
> - Utiliser le kit de développement IA Custom Vision pour l’inférence en périphérie.

> [!Tip]  
> ![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack Hub est une extension d’Azure. Azure Stack Hub offre à votre environnement local l’agilité et l’innovation du cloud computing grâce au seul cloud hybride qui vous permette de créer et de déployer des applications hybrides en tout lieu.  
> 
> L’article [Design Considerations for Hybrid Applications](overview-app-design-considerations.md) se penche sur les fondements de la qualité logicielle (sélection élective, scalabilité, disponibilité, résilience, facilité de gestion et sécurité) en matière de conception, de déploiement et d’exploitation des applications hybrides. Les considérations de conception vous aident à optimiser la conception d’application hybride, en réduisant les risques dans les environnements de production.

## <a name="prerequisites"></a>Prérequis 

Avant de commencer à utiliser ce guide de déploiement, vous devez :

- Consulter la [vue d'ensemble sur la solution de détection des pas](pattern-retail-footfall-detection.md) 
- Obtenir un accès utilisateur à un Kit de développement Azure Stack (ASDK) ou à une instance de système intégré Azure Stack Hub avec :
  - [Azure App Service sur le fournisseur de ressources Azure Stack Hub](../operator/azure-stack-app-service-overview.md) installé. Vous devez disposer d'un accès opérateur à votre instance Azure Stack Hub ou vous rapprocher de votre administrateur pour qu'il l'installe.
  - Abonnement à une offre fournissant App Service et un quota de stockage. Vous devez disposer d'un accès opérateur pour créer une offre.
- Obtenir un accès à un abonnement Azure
  - Si vous n’avez pas d’abonnement Azure, créez un [compte d'évaluation gratuit](https://azure.microsoft.com/free/) avant de commencer.
- Créez deux principaux de service dans votre répertoire :
  - Un principal de service configuré pour utiliser les ressources Azure, avec accès à l’étendue de l’abonnement Azure. 
  - Un principal de service configuré pour utiliser les ressources Azure Stack Hub, avec accès à l’étendue de l’abonnement Azure Stack Hub. 
  - Pour en savoir plus sur la création de principaux de service et l’autorisation d’accès, consultez [Utiliser une identité d’application pour accéder aux ressources](../operator/azure-stack-create-service-principals.md). Si vous préférez utiliser Azure CLI, consultez [Créer un principal du service avec Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).
- Déployez Azure Cognitive Services dans Azure ou Azure Stack Hub.
  - Commencez par vous [renseigner sur Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).
  - Consultez ensuite [Déployer Azure Cognitive Services sur Azure Stack Hub](../user/azure-stack-solution-template-cognitive-services.md) pour déployer Cognitive Services sur Azure Stack Hub. Vous devez d’abord vous inscrire pour accéder à la préversion.
- Clonez ou téléchargez un kit de développement IA Azure Custom Vision non configuré. Pour plus d’informations, consultez [Kit de développement IA Custom Vision](https://azure.github.io/Vision-AI-DevKit-Pages/).
- Créez un compte Power BI.
- Clé d’abonnement et URL de point de terminaison API Visage Azure Cognitive Services. Vous pouvez bénéficier d'une évaluation gratuite [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api), ou suivre les instructions contenues dans [Créer un compte Cognitive Services](/azure/cognitive-services/cognitive-services-apis-create-account).
- Installez les ressources de développement suivantes :
  - [Azure CLI 2.0](../user/azure-stack-version-profiles-azurecli2.md)
  - [Docker CE](https://hub.docker.com/search/?type=edition&offering=community)
  - [Porter](https://porter.sh/). Vous utilisez Porter pour déployer des applications cloud à l’aide des manifestes d'offre CNAB qui vous sont fournis.
  - [Visual Studio Code](https://code.visualstudio.com/)
  - [Azure IoT Tools pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  - [Extension Python pour Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - [Python](https://www.python.org/)

## <a name="deploy-the-hybrid-cloud-application"></a>Déployer l’application cloud hybride

Tout d’abord, vous utilisez l’interface CLI Porter pour générer un jeu d’informations d’identification, puis vous déployez l’application cloud.  

1. Clonez ou téléchargez l’exemple de code de solution depuis https://github.com/azure-samples/azure-intelligent-edge-patterns. 

1. Porter génère un jeu d'informations d’identification qui automatise le déploiement de l’application. Avant d’exécuter la commande de génération des informations d’identification, assurez-vous que les éléments suivants sont disponibles :

    - Principal de service pour accéder aux ressources Azure, ID du principal du service, clé et DNS du locataire notamment.
    - ID d’abonnement de votre abonnement Azure.
    - Principal de service pour accéder aux ressources Azure Stack Hub, ID du principal du service, clé et DNS du locataire notamment.
    - ID d’abonnement de votre compte Azure Stack Hub.
    - Clé et URL de point de terminaison des ressources API Visage Azure Cognitive Services.

1. Exécutez le processus de génération d'informations d’identification Porter et suivez les invites :

   ```porter
   porter creds generate --tag intelligentedge/footfall-cloud-deployment:0.1.0
   ```

1. Porter requiert également l’exécution d’un ensemble de paramètres. Créez un fichier texte de paramètres et entrez les paires nom/valeur suivantes. Rapprochez-vous de votre administrateur Azure Stack Hub si vous avez besoin d’aide concernant les valeurs requises.

   > [!NOTE] 
   > La valeur `resource suffix` est utilisée pour veiller à ce que les ressources de votre déploiement possèdent des noms uniques dans Azure. Il doit s’agir d’une chaîne unique de lettres et de chiffres, ne dépassant pas 8 caractères.

    ```
    azure_stack_tenant_arm="Your Azure Stack Hub tenant endpoint"
    azure_stack_storage_suffix="Your Azure Stack Hub storage suffix"
    azure_stack_keyvault_suffix="Your Azure Stack Hub keyVault suffix"
    resource_suffix="A unique string to identify your deployment"
    azure_location="A valid Azure region"
    azure_stack_location="Your Azure Stack Hub location identifier"
    powerbi_display_name="Your first and last name"
    powerbi_principal_name="Your Power BI account email address"
    ```
   Enregistrez le fichier texte et notez son chemin d’accès.

1. Vous êtes maintenant prêt à déployer l’application cloud hybride à l’aide de Porter. Exécutez la commande d’installation et observez le déploiement des ressources sur Azure et Azure Stack Hub :

    ```porter
    porter install footfall-cloud –tag intelligentedge/footfall-cloud-deployment:0.1.0 –creds footfall-cloud-deployment –param-file "path-to-cloud-parameters-file.txt"
    ```

1. Une fois le déploiement terminé, notez les valeurs suivantes :
    - Chaîne de connexion de la caméra.
    - Chaîne de connexion de compte de stockage d'image.
    - Noms des groupes de ressources.

## <a name="prepare-the-custom-vision-ai-dev-kit"></a>Préparer le kit de développement IA Custom Vision

Configurez ensuite le kit de développement IA Custom Vision comme expliqué dans le [démarrage rapide du kit de développement IA Custom Vision](https://azure.github.io/Vision-AI-DevKit-Pages/docs/quick_start/). Vous pouvez également configurer et tester votre caméra à l’aide de la chaîne de connexion fournie à l’étape précédente.

## <a name="deploy-the-camera-application"></a>Déployer l'application de caméra

Utilisez l’interface CLI Porter pour générer un jeu d’informations d’identification, puis déployez l’application de caméra.

1. Porter génère un jeu d'informations d’identification qui automatise le déploiement de l’application. Avant d’exécuter la commande de génération des informations d’identification, assurez-vous que les éléments suivants sont disponibles :
    
    - Principal de service pour accéder aux ressources Azure, ID du principal du service, clé et DNS du locataire notamment.
    - ID d’abonnement de votre abonnement Azure.
    - Chaîne de connexion au compte de stockage d’image fournie lors du déploiement de l’application cloud.

1. Exécutez le processus de génération d'informations d’identification Porter et suivez les invites :

    ```porter
    porter creds generate --tag intelligentedge/footfall-camera-deployment:0.1.0
    ```

1. Porter requiert également l’exécution d’un ensemble de paramètres. Créez un fichier texte de paramètres et entrez le texte suivant. Rapprochez-vous de votre administrateur Azure Stack Hub si vous ignorez certaines des valeurs requises.

    > [!NOTE]
    > La valeur `deployment suffix` est utilisée pour veiller à ce que les ressources de votre déploiement possèdent des noms uniques dans Azure. Il doit s’agir d’une chaîne unique de lettres et de chiffres, ne dépassant pas 8 caractères.

    ```
    iot_hub_name="Name of the IoT Hub deployed"
    deployment_suffix="Unique string here"
    ```

    Enregistrez le fichier texte et notez son chemin d’accès.

4. Vous êtes maintenant prêt à déployer l’application de caméra à l’aide de Porter. Exécutez la commande d’installation et observez le déploiement IoT Edge.

    ```porter
    porter install footfall-camera –tag intelligentedge/footfall-camera-deployment:0.1.0 –creds footfall-camera-deployment –param-file "path-to-camera-parameters-file.txt"
    ```

5. Pour vérifier que le déploiement de la caméra est terminé, consultez le flux de caméra sur `https://<camera-ip>:3000/`, où `<camara-ip>` correspond à l’adresse IP de la caméra. Cette étape peut durer jusqu’à 10 minutes.

## <a name="configure-azure-stream-analytics"></a>Configuration d’Azure Stream Analytics

Maintenant que les données sont transmises de la caméra à Azure Stream Analytics, vous devez l'autoriser manuellement à communiquer avec Power BI.

1.  À partir du portail Azure, ouvrez **Toutes les ressources** et le travail *process-footfall\[\]yoursuffix*.

2.  Dans la section **Topologie de la tâche** du volet du travail Stream Analytics, sélectionnez l’option **Sorties**.

3.  Sélectionnez le récepteur de sortie **traffic-output**.

4.  Sélectionnez **Renouveler une autorisation** et connectez-vous à votre compte Power BI.
    
    ![invite de renouvellement d'autorisation](./media/solution-deployment-guide-retail-footfall-detection/image2.png)

5.  Enregistrez les paramètres de sortie.

6.  Accédez au volet **Vue d’ensemble** et sélectionnez **Démarrer** pour commencer à envoyer des données à Power BI.

7.  Sélectionnez **Maintenant** pour l’heure de début de sortie du travail, puis sélectionnez **Démarrer**. Vous pouvez voir l’état du travail dans la barre de notification.

## <a name="create-a-power-bi-dashboard"></a>Créer un tableau de bord Power BI

1.  Lorsque le travail est terminé, accédez à [Power BI](https://powerbi.com/), puis connectez-vous avec votre compte professionnel ou scolaire. Si la requête du travail Stream Analytics génère des résultats, le jeu de données *footfall-dataset* que vous avez créé doit s’afficher dans l’onglet **Jeux de données**.

2.  À partir de votre espace de travail Power BI, sélectionnez **+ Créer** pour créer un nouveau tableau de bord intitulé *Analyse des pas*.

3.  En haut de la fenêtre, sélectionnez **Ajouter une vignette**. Ensuite, sélectionnez **Données de streaming personnalisées**, puis **Suivant**. Choisissez **football-dataset** dans **Vos jeux de données**. Sélectionnez **Carte** dans la liste déroulante **Type de visualisation**, puis ajoutez **âge** à **Champs**. Sélectionnez **Suivant** afin de saisir un nom pour la vignette, puis **Appliquer** pour créer la vignette.

4.  Vous pouvez ajouter des champs et cartes supplémentaires, selon vos besoins.

## <a name="test-your-solution"></a>Tester votre solution

Observez la manière dont les données des cartes que vous avez créées dans Power BI évoluent en fonction des personnes se déplaçant devant la caméra. Une fois enregistrées, les inférences peuvent mettre jusqu’à 20 secondes à s'afficher.

## <a name="remove-your-solution"></a>Supprimer votre solution

Si vous souhaitez supprimer votre solution, exécutez les commandes suivantes à l’aide de Porter, en utilisant les mêmes fichiers de paramètres que ceux créés pour le déploiement : 

```porter
porter uninstall footfall-cloud –tag intelligentedge/footfall-cloud-deployment:0.1.0 –creds footfall-cloud-deployment –param-file "path-to-cloud-parameters-file.txt"

porter uninstall footfall-camera –tag intelligentedge/footfall-camera-deployment:0.1.0 –creds footfall-camera-deployment –param-file "path-to-camera-parameters-file.txt"
```
## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [considérations relatives à la conception d’applications cloud hybrides](overview-app-design-considerations.md)
- Passez en revue et proposez des améliorations pour [le code de cet exemple sur GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/tree/master/footfall-analysis).
