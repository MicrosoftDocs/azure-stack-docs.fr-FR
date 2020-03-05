---
title: Déployer un cluster Kubernetes avec le moteur AKS sur Azure Stack Hub
description: Découvrez comment déployer un cluster Kubernetes sur Azure Stack Hub à partir d’une machine virtuelle cliente exécutant le moteur AKS.
author: mattbriggs
ms.topic: article
ms.date: 01/10/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: b8826fc929c571e39d36139bf724861ae9cc7fbd
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77702701"
---
# <a name="deploy-a-kubernetes-cluster-with-the-aks-engine-on-azure-stack-hub"></a>Déployer un cluster Kubernetes avec le moteur AKS sur Azure Stack Hub

Vous pouvez déployer un cluster Kubernetes sur Azure Stack Hub à partir d’une machine virtuelle cliente exécutant le moteur AKS. Dans cet article, nous nous intéressons à l’écriture d’une spécification de cluster, au déploiement d’un cluster avec le fichier `apimodel.json` et à la vérification du cluster en déployant MySQL avec Helm.

## <a name="define-a-cluster-specification"></a>Définir une spécification de cluster

Vous pouvez préciser une spécification de cluster dans un fichier de document au format JSON appelé [modèle d’API](https://github.com/Azure/aks-engine/blob/master/docs/topics/architecture.md#architecture-diagram). Le moteur AKS utilise une spécification de cluster dans le modèle d’API pour créer votre cluster. 

### <a name="update-the-api-model"></a>Mettre à jour le modèle d’API

Cette section présente la création d’un modèle d’API pour votre cluster.

1.  Commencez par utiliser un [exemple](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack) de fichier de modèle d’API Azure Stack Hub et en faire une copie locale pour votre déploiement. À partir de la machine sur laquelle vous avez installé le moteur AKS, exécutez :

    ```bash
    curl -o kubernetes-azurestack.json https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json
    ```

    > [!Note]  
    > Si vous êtes déconnecté, vous pouvez télécharger le fichier et le copier manuellement sur la machine déconnectée sur laquelle vous envisagez de le modifier. Vous pouvez copier le fichier sur votre machine Linux à l’aide d’outils comme [PuTTY ou WinSCP.](https://www.suse.com/documentation/opensuse103/opensuse103_startup/data/sec_filetrans_winssh.html)

2.  Pour ouvrir le fichier dans un éditeur, vous pouvez utiliser nano :

    ```bash
    nano ./kubernetes-azurestack.json
    ```

    > [!Note]  
    > Si vous n’avez pas installé nano, vous pouvez le faire sur Ubuntu : `sudo apt-get install nano`.

3.  Dans le fichier kubernetes-azurestack.JSON, recherchez `orchestratorRelease`. Sélectionnez l’une des versions de Kubernetes prises en charge. Exemples : 1.14, 1.15. Les versions sont souvent des mises à jour. Spécifiez la version sous la forme x.xx plutôt que x.xx.x. Pour obtenir la liste des versions actuelles, consultez [Versions de Kubernetes prises en charge](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). Vous pouvez déterminer la version prise en charge en exécutant la commande de moteur AKS suivante :

    ```bash
    aks-engine get-versions
    ```

4.  Recherchez `customCloudProfile` et fournissez l’URL du portail du locataire. Par exemple : `https://portal.local.azurestack.external`. 

5. Ajoutez `"identitySystem":"adfs"` si vous utilisez AD FS. Par exemple,

    ```JSON  
        "customCloudProfile": {
            "portalURL": "https://portal.local.azurestack.external",
            "identitySystem": "adfs"
        },
    ```

    > [!Note]  
    > Si vous utilisez Azure AD pour votre système d’identité, vous n’avez pas besoin d’ajouter le champ **identitySystem**.

6. Recherchez `portalURL` et fournissez l’URL du portail du locataire. Par exemple : `https://portal.local.azurestack.external`.

7.  Dans le tableau `masterProfile`, définissez les champs suivants :

    | Champ | Description |
    | --- | --- |
    | dnsPrefix | Entrez une chaîne unique qui servira à identifier le nom d’hôte des machines virtuelles. Par exemple, un nom basé sur le nom du groupe de ressources. |
    | count |  Entrez le nombre de maîtres que vous souhaitez pour votre déploiement. Le minimum pour un déploiement HA s’élève à 3, mais 1 seul maître est autorisé pour les déploiements non-HA. |
    | vmSize |  Entrez [une taille prise en charge par Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes), par exemple `Standard_D2_v2`. |
    | distro | Entrez `aks-ubuntu-16.04`. |

8.  Dans le tableau `agentPoolProfiles`, mettez à jour :

    | Champ | Description |
    | --- | --- |
    | count | Entrez le nombre d’agents que vous souhaitez pour votre déploiement. |
    | vmSize | Entrez [une taille prise en charge par Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes), par exemple `Standard_D2_v2`. |
    | distro | Entrez `aks-ubuntu-16.04`. |

9.  Dans le tableau `linuxProfile`, mettez à jour :

    | Champ | Description |
    | --- | --- |
    | adminUsername | Entrez le nom d’utilisateur administrateur de la machine virtuelle. |
    | ssh | Entrez la clé publique qui sera utilisée pour l’authentification SSH auprès des machines virtuelles. Si vous utilisez PuTTY, ouvrez le générateur de clé PuTTY pour charger la clé privée PuTTY et la clé publique qui commence par ssh-rsa, comme dans l’exemple suivant. Vous pouvez utiliser la clé générée lors de la création du client Linux mais **vous devez copier la clé publique pour qu’il s’agisse d’un texte d’une ligne, comme indiqué dans l’exemple**.|

    ![Générateur de clé PuTTY](media/azure-stack-kubernetes-aks-engine-deploy-cluster/putty-key-generator.png)

### <a name="more-information-about-the-api-model"></a>Plus d’informations sur le modèle d’API

- Pour obtenir des informations de référence complètes sur toutes les options disponibles dans le modèle d’API, reportez-vous aux [définitions de cluster](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).  
- Pour obtenir des informations clés sur des options spécifiques pour Azure Stack Hub , voir [Spécificités des définitions de cluster Azure Stack Hub](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cluster-definition-aka-api-model).  

## <a name="deploy-a-kubernetes-cluster"></a>Déployer un cluster Kubernetes

Une fois que vous avez collecté toutes les valeurs requises dans votre modèle d’API, vous pouvez créer votre cluster. À ce stade, vous devez :

Demander à votre opérateur Azure Stack Hub d’effectuer les opérations suivantes :

- Vérifier l’intégrité du système, en lui suggérant d’exécuter `Test-AzureStack` et l’outil de supervision de matériel de votre fournisseur OEM.
- Vérifiez la capacité système, notamment les ressources comme la mémoire, le stockage et les adresses IP publiques.
- Fournir les détails du quota associé à votre abonnement afin de vous permettre de vérifier que l’espace est toujours suffisant pour le nombre de machines virtuelles que vous envisagez d’utiliser.

Procéder au déploiement d’un cluster :

1.  Passez en revue les paramètres disponibles pour les [indicateurs CLI](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cli-flags) du moteur AKS sur Azure Stack Hub.

    | Paramètre | Exemple | Description |
    | --- | --- | --- |
    | azure-env | AzureStackCloud | Pour indiquer au moteur AKS que votre plateforme cible est Azure Stack Hub, utilisez `AzureStackCloud`. |
    | identity-system | adfs | facultatif. Spécifiez votre solution de gestion des identités si vous utilisez Active Directory Federated Services (AD FS). |
    | location | local | Nom de la région de votre Azure Stack Hub. Pour le Kit de développement Azure Stack (ASDK), la région a la valeur `local`. |
    | resource-group | kube-rg | Entrez le nom d’un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant. Le nom de la ressource doit être alphanumérique et en minuscules. |
    | api-model | ./kubernetes-azurestack.json | Chemin du fichier de configuration du cluster ou modèle d’API. |
    | output-directory | kube-rg | Entrez le nom du répertoire contenant le fichier de sortie `apimodel.json`, ainsi que d’autres fichiers générés. |
    | client-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Entrez le GUID du principal du service. ID de client identifié comme ID d’application lorsque votre administrateur Azure Stack Hub a créé le principal du service. |
    | client-secret | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Entrez le secret du principal du service. Il s’agit de la clé secrète client que vous définissez lors de la création de votre service. |
    | subscription-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Entrez votre ID d’abonnement. Pour plus d’informations, consultez [S’abonner à une offre](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer). |

    Voici un exemple :

    ```bash  
    aks-engine deploy \
    --azure-env AzureStackCloud \
    --location <for asdk is local> \
    --resource-group kube-rg \
    --api-model ./kubernetes-azurestack.json \
    --output-directory kube-rg \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --identity-system adfs # required if using AD FS
    ```

2.  Si, pour une raison quelconque, l’exécution échoue après la création du répertoire de sortie, vous pouvez corriger le problème et réexécuter la commande. Si vous réexécutez le déploiement et si vous avez déjà utilisé le même répertoire de sortie, le moteur AKS renvoie une erreur indiquant que le répertoire existe déjà. Vous pouvez remplacer le répertoire existant à l’aide de l’indicateur : `--force-overwrite`.

3.  Enregistrez la configuration de cluster du moteur AKS à un emplacement sécurisé et chiffré.

    Localisez le fichier `apimodel.json`. Enregistrez-le à un emplacement sécurisé. Ce fichier sera utilisé comme entrée dans toutes vos autres opérations du moteur AKS.

    Le fichier `apimodel.json` généré contient le principal du service, le secret et la clé publique SSH que vous utilisez dans le modèle d’API d’entrée. Il contient aussi toutes les autres métadonnées dont le moteur AKS a besoin pour effectuer toutes les autres opérations. Si vous le perdez, le moteur AKS ne peut pas configurer le cluster.

    Les secrets sont **non chiffrés**. Conservez le fichier à un emplacement sécurisé et chiffré. 

## <a name="verify-your-cluster"></a>Vérifier votre cluster

Vérifiez votre cluster en déployant MySql avec Helm pour contrôler votre cluster.

1. Obtenez l’adresse IP publique de l’un de vos nœuds principaux à l’aide du portail Azure Stack Hub.

2. À partir d’une machine ayant accès à votre instance Azure Stack Hub, connectez-vous en utilisant le protocole SSH au nouveau nœud principal à l’aide d’un client tel que PuTTY ou MobaXterm. 

3. Pour le nom d’utilisateur SSH, vous utilisez « azureuser » et le fichier de clé privée de la paire de clés que vous avez fournie pour le déploiement du cluster.

4. Exécutez les commandes suivantes pour créer un exemple de déploiement d’un maître Redis (pour les tampons connectés uniquement) :

   ```bash
   kubectl apply -f https://k8s.io/examples/application/guestbook/redis-master-deployment.yaml
   ```

    1. Interroger la liste des pods :

       ```bash
       kubectl get pods
       ```

    2. La réponse doit être semblable à ce qui suit :

       ```shell
       NAME                            READY     STATUS    RESTARTS   AGE
       redis-master-1068406935-3lswp   1/1       Running   0          28s
       ```

    3. Afficher les journaux de déploiement :

       ```shell
       kubectl logs -f <pod name>
       ```

    S’il s’agit d’un déploiement complet d’un exemple d’application PHP qui comprend le maître Redis, suivez [les instructions fournies ici](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/).

5. Pour un tampon déconnecté, il suffit généralement d’utiliser les commandes suivantes :

    1. Vérifiez d’abord que les points de terminaison du cluster sont en cours d’exécution :

       ```bash
       kubectl cluster-info
       ```

       Le résultat doit être semblable à ce qui suit :

       ```shell
       Kubernetes master is running at https://democluster01.location.domain.com
       CoreDNS is running at https://democluster01.location.domain.com/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
       kubernetes-dashboard is running at https://democluster01.location.domain.com/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy
       Metrics-server is running at https://democluster01.location.domain.com/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy
       ```

    2. Ensuite, vérifiez les états des nœuds :

       ```bash
       kubectl get nodes
       ```

       La sortie doit ressembler à ce qui suit :

       ```shell
       k8s-linuxpool-29969128-0   Ready      agent    9d    v1.15.5
       k8s-linuxpool-29969128-1   Ready      agent    9d    v1.15.5
       k8s-linuxpool-29969128-2   Ready      agent    9d    v1.15.5
       k8s-master-29969128-0      Ready      master   9d    v1.15.5
       k8s-master-29969128-1      Ready      master   9d    v1.15.5
       k8s-master-29969128-2      Ready      master   9d    v1.15.5
       ```

6. Pour nettoyer le déploiement POD Redis effectué à l’étape précédente, exécutez la commande suivante :

    ```bash
    kubectl delete deployment -l app=redis
    ```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Résoudre des problèmes du moteur AKS sur Azure Stack Hub](azure-stack-kubernetes-aks-engine-troubleshoot.md)
