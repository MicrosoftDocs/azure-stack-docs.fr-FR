---
title: Résoudre les problèmes du moteur AKS sur Azure Stack | Microsoft Docs
description: Cette rubrique contient les étapes de résolution des problèmes pour le moteur AKS sur Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 09/14/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/14/2019
ms.openlocfilehash: eb8a46c5b226d1be40d922a78c6ecdcdda5e45ad
ms.sourcegitcommit: 09d14eb77a43fd585e7e6be93c32fa427770adb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019383"
---
# <a name="troubleshoot-the-aks-engine-on-azure-stack"></a>Résoudre les problème du moteur AKS sur Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez rencontrer un problème lors du déploiement ou de l’utilisation du moteur AKS sur Azure Stack. Cet article décrit les étapes à suivre pour résoudre les problèmes liés à votre déploiement du moteur AKS, collecter des informations sur votre moteur AKS, collecter des journaux Kubernetes, passer en revue les codes d’erreur d’extension de script personnalisé et obtenir des instructions sur l’ouverture d’un problème GitHub pour le moteur AKS.

> [!IMPORTANT]
> Le moteur AKS est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="troubleshoot-the-aks-engine-install"></a>Résoudre les problèmes d’installation du moteur AKS

### <a name="try-gofish"></a>Essayer GoFish

Si la procédure d’installation échoue, tentez une installation à l’aide du gestionnaire de package GoFish. [GoFish](https://gofi.sh) se présente comme un Homebrew multiplateforme.

#### <a name="install-the-aks-engine-with-gofish-on-linux"></a>Installer le moteur AKS avec GoFish sur Linux

Installez GoFish à partir de la page [Installation](https://gofi.sh/#install).

1. À partir d’une invite Bash, exécutez la commande suivante :

    ```bash
    curl -fsSL https://raw.githubusercontent.com/fishworks/gofish/master/scripts/install.sh | bash
    ```

2.  Exécutez la commande suivante pour installer le moteur AKS avec GoFish :

    ```bash
    Run "gofish install aks-engine"
    ```

#### <a name="install-the-aks-engine-with-gofish-on-windows"></a>Installer le moteur AKS avec GoFish sur Windows

Installez GoFish à partir de la page [Installation](https://gofi.sh/#install).

1. À partir d’une invite PowerShell avec élévation de privilèges, exécutez la commande suivante :

    ```PowerShell
    Set-ExecutionPolicy Bypass -Scope Process -Force
iex ((New-Object System.Net.WebClient).DownloadString('https://raw.githubusercontent.com/fishworks/gofish/master/scripts/install.ps1'))
    ```

2.  Exécutez la commande suivante dans la même session pour installer le moteur AKS avec GoFish :

    ```PowerShell
    gofish install aks-engine
    ```

### <a name="checklist-for-common-deployment-issues"></a>Liste de contrôle des problèmes de déploiement courants

Quand vous rencontrez des erreurs lors du déploiement d’un cluster Kubernetes à l’aide du moteur AKS, vous pouvez vérifier les points suivants :

1.  Utilisez-vous les bonnes informations d’identification du principal du service (SPN) ?
2.  Le SPN joue-t-il un rôle de contributeur pour l’abonnement Azure Stack ?
3. Disposez-vous d’un quota suffisamment élevé dans votre plan Azure Stack ?
4.  Un correctif ou une mise à niveau sont-ils appliqués à l’instance Azure Stack ?

Pour plus d’informations, consultez l’article sur la [résolution des problèmes](https://github.com/Azure/aks-engine/blob/master/docs/howto/troubleshooting.md) dans le dépôt GitHub **Azure/aks-engine**.

## <a name="collect-aks-engine-logs"></a>Collecter les journaux du moteur AKS

Vous pouvez accéder aux informations de révision créées par le moteur AKS. Le moteur AKS signale l’état et les erreurs pendant l’exécution de l’application. Vous pouvez soit diriger la sortie vers un fichier texte, soit la copier directement à partir de la console de ligne de commande.

1.  Collectez la sortie et l’erreur standard à partir des informations présentées dans l’outil en ligne de commande du moteur AKS.

2. Obtenez les journaux à partir d’un fichier local. Vous pouvez définir le répertoire de sortie avec le paramètre **--output-directory**.

    Pour définir le chemin local des journaux :

    ```bash  
    aks-engine --output-directory <path to the directory>
    ```

## <a name="collect-kubernetes-logs"></a>Collecter des journaux Kubernetes

Outre les journaux du moteur AKS, les composants Kubernetes génèrent des messages d’état et d’erreur. Vous pouvez collecter ces journaux à l’aide du script Bash [getkuberneteslogs.sh](https://github.com/msazurestackworkloads/azurestack-gallery/releases/download/diagnosis-v0.1.0/diagnosis.zip).

Ce script automatise le processus de collecte des journaux suivants : 

 - Journaux de l’agent Linux Microsoft Azure (waagent)
 - Journaux d’extension de script personnalisé
 - Métadonnées de conteneur kube-system en cours d’exécution
 - Journaux de conteneur kube-system en cours d’exécution
 - État et journal du service Kubelet
 - État et journal du service Etcd
 - Journaux DVM de l’élément de la galerie
 - Instantané kube-system

Sans ce script, vous devez vous connecter à chaque nœud du cluster pour rechercher et télécharger les journaux manuellement. De plus, le script peut éventuellement charger les journaux collectés dans un compte de stockage que vous pouvez utiliser pour partager les journaux avec d’autres utilisateurs.

Requirements:

 - Une machine virtuelle Linux, Git Bash ou Bash sur Windows.
 - [Azure CLI](azure-stack-version-profiles-azurecli2.md) installé sur la machine à partir de laquelle le script sera exécuté.
 - Identité du principal du service connectée dans une session Azure CLI à Azure Stack. Étant donné que le script a la possibilité de découvrir et de créer des ressources ARM pour effectuer son travail, il nécessite Azure CLI et une identité de principal du service.
 - Compte d’utilisateur (abonnement) dans lequel le cluster Kubernetes est déjà sélectionné dans l’environnement. 
1. Téléchargez la dernière version du fichier tar du script sur votre machine virtuelle cliente, une machine qui a accès à votre cluster Kubernetes ou à la même machine que celle que vous avez utilisée pour déployer votre cluster avec le moteur AKS.

    Exécutez les commandes suivantes :

    ```bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    wget https://github.com/msazurestackworkloads/azurestack-gallery/releases/download/diagnosis-v0.1.0/diagnosis.tar.gz
    tar xvzf diagnosis.tar.gz -C ./
    ```

2. Recherchez les paramètres exigés par le script `getkuberneteslogs.sh`. Le script utilise les paramètres suivants :

    | Paramètre | Description | Obligatoire | Exemples |
    | --- | --- | --- | --- |
    | -h, --help | Utilisation de la commande d’impression. | no | 
    -u,--user | Nom de l’administrateur des machines virtuelles du cluster. | Oui | azureuser<br>(valeur par défaut) |
    | -i, --identity-file | Clé privée RSA liée à la clé publique utilisée pour créer le cluster Kubernetes (parfois nommée « id_rsa »).  | Oui | `./rsa.pem` (Putty)<br>`~/.ssh/id_rsa` (SSH) |
    |   -g, --resource-group    | Groupe de ressources de cluster Kubernetes. | Oui | k8sresourcegroup |
    |   -n, --user-namespace               | Collectez les journaux des conteneurs dans les espaces de noms spécifiés (journaux kube-system toujours collectés). | no |   monitoring |
    |       --api-model                    | Rend le fichier apimodel.json persistant dans un compte de stockage Azure Stack. Le chargement du fichier apimodel.json dans le compte de stockage se produit quand le paramètre --upload-logs est également fourni. | no | `./apimodel.json` |
    | --all-namespaces               | Collectez les journaux des conteneurs dans tous les espaces de noms. Ce paramètre remplace --user-namespace. | no | |
    | --upload-logs                  | Conserve les journaux récupérés dans un compte de stockage Azure Stack. Les journaux se trouvent dans le groupe de ressources KubernetesLogs. | no | |
    --disable-host-key-checking    | Définit l’option StrictHostKeyChecking SSH sur « no » pendant l’exécution du script. Utilisez ce paramètre uniquement dans un environnement sécurisé. | no | |

3. Exécutez l’un des exemples de commandes suivants avec vos informations :

    ```bash
    ./getkuberneteslogs.sh -u azureuser -i private.key.1.pem -g k8s-rg
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --disable-host-key-checking
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg -n default -n monitoring
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --upload-logs --api-model clusterDefinition.json
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --upload-logs
    ```

## <a name="review-custom-script-extension-error-codes"></a>Examiner les codes d’erreur d’extension de script personnalisé

Vous pouvez consulter la liste des codes d’erreur créés par l’extension de script personnalisé pendant l’exécution de votre cluster. L’erreur d’extension de script personnalisé peut s’avérer utile pour diagnostiquer la cause racine du problème. L’extension de script personnalisé correspondant au serveur Ubuntu utilisé dans votre cluster Kubernetes prend en charge un grand nombre d’opérations du moteur AKS. Pour plus d’informations sur les codes de sortie de l’extension de script personnalisé, consultez [cse_helpers.sh](https://github.com/Azure/aks-engine/blob/master/parts/k8s/cloud-init/artifacts/cse_helpers.sh).

## <a name="open-github-issues"></a>Ouvrir des problèmes GitHub

Si vous ne parvenez pas à corriger votre erreur de déploiement, vous pouvez ouvrir un problème GitHub. 

1. Ouvrez un [problème GitHub](https://github.com/Azure/aks-engine/issues/new) dans le dépôt du moteur AKS.
2. Ajoutez un titre au format suivant : C`SE error: exit code <INSERT_YOUR_EXIT_CODE>`.
3. Incluez les informations suivantes dans le problème :

    - Fichier de configuration de cluster, `apimodel json`, utilisé pour déployer le cluster. Supprimez tous les secrets et toutes les clés avant de poster le problème sur GitHub.  
     - Sortie de la commande **kubectl** suivante `get nodes`.  
     - Contenu de `/var/log/azure/cluster-provision.log` et `/var/log/cloud-init-output.log`.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [moteur AKS sur Azure Stack](azure-stack-kubernetes-aks-engine-overview.md)
