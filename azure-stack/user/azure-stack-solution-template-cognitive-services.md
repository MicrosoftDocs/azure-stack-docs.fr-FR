---
title: Déployer Azure Cognitive Services sur Azure Stack Hub
description: Apprenez à déployer Azure Cognitive Services sur Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 10/09/2020
ms.author: mabrigg
ms.reviewer: guanghu
ms.lastreviewed: 10/09/2020
ms.openlocfilehash: 4dc6ad30d54b61e05f54be4a4f1301472794db60
ms.sourcegitcommit: e13f27291bab236aac5d8b05401056961e9cc1e9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697680"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack-hub"></a>Déployer Azure Cognitive Services sur Azure Stack Hub

Vous pouvez utiliser Azure Cognitive Services avec prise en charge des conteneurs sur Azure Stack Hub. La prise en charge des conteneurs dans Azure Cognitive Services vous permet d’utiliser les mêmes API puissantes que celles disponibles dans Azure. L’utilisation des conteneurs vous offre une flexibilité quant à l’emplacement où vous déployez et hébergez les services délivrés dans des [conteneurs Docker](https://www.docker.com/what-container). 

La mise en conteneur, ou conteneurisation, est une méthode de distribution de logiciels dans laquelle une application ou un service, y compris ses dépendances et sa configuration, est empaqueté(e) dans une image conteneur. Avec peu ou aucune modification, vous pouvez déployer une image sur un hôte de conteneur. Chaque conteneur est isolé des autres conteneurs et du système d’exploitation sous-jacent. Le système proprement dit a uniquement les composants nécessaires pour exécuter votre image. Un hôte de conteneur a un encombrement inférieur à celui d’une machine virtuelle. Vous pouvez également créer des conteneurs à partir d’images pour les tâches à court terme et les supprimer quand vous n’en avez plus besoin.

La prise en charge des conteneurs est actuellement disponible pour une partie des services Azure Cognitive Services :

- **Speech**
    - Reconnaissance vocale (standard)
    - Reconnaissance vocale (personnalisée)
    - Synthèse vocale (standard)
    - Synthèse vocale (neuronale)
- **Langage**
    - Compréhension de la langue
    - Analyse de texte (analyse des sentiments)
- **Décision**
    - Détecteur d’anomalies


> [!IMPORTANT]
> Une partie des services Azure Cognitive Services pour Azure Stack Hub est actuellement en préversion publique.
> La version de révision est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail en production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La prise en charge des conteneurs est actuellement en préversion publique pour une partie des services Azure Cognitive Services :

- **Vision par ordinateur**
    - Read 3.0, Read 3.1
    - Analyse spatiale (nouveau)
- **Langage**
    - Extraction d’expressions clés
    - Détection de la langue
    - Analyse de texte pour l’intégrité
- **Speech**
    - Détection de langue vocale
    - Synthèse vocale neuronale
    - Synthèse vocale (personnalisée)

## <a name="use-containers-with-cognitive-services-on-azure-stack-hub"></a>Utiliser des conteneurs avec Cognitive Services sur Azure Stack Hub

- **Contrôle des données**  
  Autorisez les utilisateurs de votre application à contrôler leurs données lors de l’utilisation de Cognitive Services. Vous pouvez distribuer Cognitive Services aux utilisateurs de l’application qui ne peuvent pas envoyer de données vers le cloud public ou Azure mondial.

- **Contrôle des mises à jour des modèles**  
  Fournissez aux utilisateurs de votre application la version et les mises à jour des modèles déployés dans leur solution.

- **Architecture portable**  
  Facilitez la création d'une architecture d'application portable afin de pouvoir déployer votre solution sur le cloud public, sur un cloud privé local ou à la périphérie. Vous pouvez déployer votre conteneur sur Azure Kubernetes Service, Azure Container Instances ou un cluster Kubernetes dans Azure Stack Hub. Pour plus d'informations, consultez [Déployer Kubernetes sur Azure Stack Hub](azure-stack-solution-template-kubernetes-deploy.md).

- **Haut débit et faible latence**  
   Offrez aux utilisateurs de votre application la possibilité de mettre à l’échelle avec des pics de trafic pour un débit élevé et une faible latence. Activez Cognitive Services pour une exécution dans Azure Kubernetes Service physiquement proche de leur logique d’application et de leurs données.

Avec Azure Stack Hub, déployez des conteneurs Cognitive Services dans un cluster Kubernetes avec vos conteneurs d'applications pour bénéficier d'une haute disponibilité et d'une mise à l'échelle élastique. Vous pouvez développer votre application en combinant Cognitive Services avec des composants reposant sur App Services, Functions, Stockage Blob, SQL ou des bases de données MySQL.

Pour plus d’informations sur les conteneurs Cognitive Services, consultez [Prise en charge des conteneurs dans Azure Cognitive Services](/azure/cognitive-services/cognitive-services-container-support).

## <a name="deploy-the-azure-face-api"></a>Déployer l’API Visage Azure

Cet article explique comment déployer l'API Visage Azure sur un cluster Kubernetes dans Azure Stack Hub. Vous pouvez adopter la même approche pour déployer d'autres conteneurs Cognitive Services sur des clusters Kubernetes d'Azure Stack Hub.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez :

1.  Demander l’accès au registre de conteneurs pour extraire des images conteneurs Visage à partir d’Azure Cognitive Services Container Registry. Pour plus d’informations, consultez [Demander l’accès au registre de conteneurs privé](/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry).

2.  Préparer un cluster Kubernetes sur Azure Stack Hub. Vous pouvez suivre les instructions fournies dans l'article [Déployer Kubernetes sur Azure Stack Hub](azure-stack-solution-template-kubernetes-deploy.md).

## <a name="create-azure-resources"></a>Créer des ressources Azure

Créez une ressource Cognitive Services sur Azure pour afficher un aperçu des conteneurs Visage, LUIS (Language Understanding) ou Reconnaître le texte. Vous devez utiliser la clé d’abonnement et l’URL de point de terminaison de la ressource pour instancier les conteneurs Cognitive Services.

1. Créez une ressource Azure dans le portail Azure. Si vous souhaitez afficher un aperçu des conteneurs Visage, vous devez d’abord créer une ressource Visage correspondante dans le portail Azure. Pour plus d’informations, consultez [Démarrage rapide : Créer un compte Cognitive Services dans le portail Azure](/azure/cognitive-services/cognitive-services-apis-create-account).

   > [!NOTE]
   >  La ressource Visage ou Vision par ordinateur doit utiliser le niveau tarifaire F0.

2. Obtenez l’URL de point de terminaison et la clé d’abonnement pour la ressource Azure. Après avoir créé la ressource Azure, utilisez la clé d’abonnement et l’URL de point de terminaison de cette ressource pour instancier le conteneur Visage, LUIS ou Reconnaître le texte correspondant pour l’aperçu.

## <a name="create-a-kubernetes-secret"></a>Créer un secret Kubernetes 

Utilisez la commande de création de secret Kubectl pour accéder au registre de conteneurs privé. Remplacez `<username>` par le nom de l’utilisateur et `<password>` par le mot de passe fourni dans les informations d’identification envoyées par l’équipe Azure Cognitive Services.

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>Préparer un fichier de configuration YAML

Utilisez le fichier de configuration YAML pour simplifier le déploiement du service cognitif sur le cluster Kubernetes.

Voici un exemple de fichier de configuration YAML pour déployer le service Visage sur Azure Stack Hub :

```Yaml  
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: <deploymentName>
spec:
  replicas: <replicaNumber>
  template:
    metadata:
      labels:
        app: <appName>
    spec:
      containers:
      - name: <containersName>
        image: <ImageLocation>
        env: 
        - name: EULA
          value: accept 
        - name: Billing
          value: <billingURL> 
        - name: apikey
          value: <apiKey>
        tty: true
        stdin: true
        ports:
        - containerPort: 5000 
      imagePullSecrets:
        - name: <secretName>
---
apiVersion: v1
kind: Service
metadata:
  name: <LBName>
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort : 5000
    name: <PortsName>
  selector:
    app: <appName>
```

Dans ce fichier de configuration YAML, utilisez le secret que vous avez utilisé pour obtenir les images conteneurs du service cognitif à partir d’Azure Container Registry. Le fichier de secret s’utilise pour déployer un réplica spécifique du conteneur. Vous pouvez aussi créer un équilibreur de charge pour vous assurer que les utilisateurs peuvent accéder à ce service en externe.

Détails concernant les principaux champs :

| Champ | Notes |
| --- | --- |
| replicaNumber | Définit les réplicas d’instances initiaux à créer. Vous pourrez effectuer une mise à l’échelle après le déploiement. |
| ImageLocation | Indique l’emplacement de l’image conteneur de service cognitif spécifique dans ACR. Par exemple, le service Visage : `aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |URL de point de terminaison notée à l’étape [Créer une ressource Azure](#create-azure-resources) |
| ApiKey | Clé d’abonnement notée à l’étape [Créer une ressource Azure](#create-azure-resources) |
| SecretName | Le nom du secret que vous avez créé à l’étape [Créer un secret Kubernetes](#create-a-kubernetes-secret) |

## <a name="deploy-the-cognitive-service"></a>Déployer le service cognitif

Utilisez la commande suivante pour déployer les conteneurs Cognitive Services :

```bash  
    Kubectl apply -f <yamlFileName>
```
Utilisez la commande suivante pour superviser le déploiement : 
```bash  
    Kubectl get pod - watch
```

## <a name="configure-http-proxy-settings"></a>Configurer les paramètres du proxy HTTP

Les nœuds worker nécessitent un proxy et SSL. Pour configurer un proxy HTTP afin d’effectuer des demandes sortantes, utilisez les deux arguments suivants :

- **HTTP_PROXY** – le proxy à utiliser, par exemple `https://proxy:8888`
- **HTTP_PROXY_CREDS** – toutes les informations d’identification nécessaires pour s’authentifier auprès du proxy, par exemple `username:password`.

### <a name="set-up-the-proxy"></a>Configurer le proxy

1. Ajoutez un fichier `http-proxy.conf` aux deux emplacements :
    - `/etc/system/systemd/docker.service.d/`
    - `/cat/etc/environment/`

2. Vérifiez que vous pouvez vous connecter au conteneur à l’aide des informations d’identification fournies par l’équipe Cognitive Services et exécutez une commande `docker pull` dans le conteneur suivant : 

    `docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest`

    Exécutez :

    `docker run hello-world pull`

### <a name="ssl-interception-setup"></a>Configuration de l’interception SSL

1. Ajoutez le certificat **https interception** à `/usr/local/share/ca-certificates` et mettez à jour le magasin avec `update-ca-certificates`. 

## <a name="test-the-cognitive-service"></a>Tester le service cognitif

Accédez à la [spécification OpenAPI](https://swagger.io/docs/specification/about/) à partir de l’URI relatif **/swagger** de ce conteneur. Cette spécification, anciennement appelée spécification Swagger, décrit les opérations prises en charge par un conteneur instancié. Par exemple, l’URI suivant permet d’accéder à la spécification OpenAPI pour le conteneur Analyse des sentiments instancié dans l’exemple précédent :

```HTTP  
http:<External IP>:5000/swagger
```

Vous pouvez obtenir l’adresse IP externe à l’aide de la commande suivante : 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>Essayer les services avec Python

Vous pouvez essayer de valider les services cognitifs sur votre système Azure Stack Hub en exécutant quelques scripts Python simples. Pour référence, il existe des exemples de guides de démarrage rapide Python officiels pour les services [Vision par ordinateur](/azure/cognitive-services/computer-vision/home), [Visage](/azure/cognitive-services/face/overview), [Analyse de texte](/azure/cognitive-services/text-analytics/overview) et [Language Understanding](/azure/cognitive-services/luis/luis-container-howto) (LUIS).

Il y a deux points à garder à l’esprit quand vous utilisez des applications Python pour valider les services exécutés sur des conteneurs : 
1. Les services cognitifs dans les conteneurs n’utilisent pas de sous-clés pour l’authentification, mais ils ont quand même besoin d’une chaîne comme espace réservé pour satisfaire au SDK. 
2. Remplacez base_URL par l’adresse IP réelle du point de terminaison de service.

Voici un exemple de script Python qui utilise le SDK Python des services Visage pour détecter et encadrer des visages dans une image :

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack Hub
BASE_URL = 'http://<svc IP Address>:5000/face/v1.0/'  
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)

```

## <a name="next-steps"></a>Étapes suivantes

[Guide pratique pour installer et exécuter des conteneurs d’API Vision par ordinateur](/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[Guide pratique pour installer et exécuter des conteneurs d’API Visage](/azure/cognitive-services/face/face-how-to-install-containers)

[Guide pratique pour installer et exécuter des conteneurs d’API Analyse de texte](/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[Guide pratique pour installer et exécuter des conteneurs LUIS (Language Understanding)](/azure/cognitive-services/luis/luis-container-howto)
