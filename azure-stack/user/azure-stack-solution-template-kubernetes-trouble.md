---
title: Résoudre les problèmes de déploiement Kubernetes sur Azure Stack | Microsoft Docs
description: Découvrez comment résoudre les problèmes de déploiement Kubernetes sur Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.author: mabrigg
ms.date: 10/10/2019
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 14a32696a3e46782b8990ba57f9510976200f7d3
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277568"
---
# <a name="troubleshoot-kubernetes-deployment-to-azure-stack"></a>Résoudre les problèmes de déploiement Kubernetes sur Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

> [!Note]  
> Kubernetes sur Azure Stack est en préversion. Un scénario Azure Stack déconnecté n'est actuellement pas pris en charge par la préversion. Utiliser uniquement l’élément de place de marché pour les scénarios de développement et de test.

Cet article examine comment résoudre les problèmes sur votre cluster Kubernetes. Pour commencer, passez en revue les éléments requis pour le déploiement. Vous devrez peut-être collecter les journaux d’activité de déploiement à partir du système Azure Stack ou des machines virtuelles Linux qui hébergent Kubernetes. Pour récupérer les journaux d’activité à partir d’un point de terminaison d’administration, contactez votre administrateur Azure Stack.

## <a name="overview-of-kubernetes-deployment"></a>Vue d’ensemble du déploiement Kubernetes

Avant de commencer à résoudre les problèmes de votre cluster, passez en revue le processus de déploiement d’un cluster Kubernetes Azure Stack. Ce déploiement utilise un modèle de solution Azure Resource Manager pour créer les machines virtuelles, puis installe le moteur AKS de votre cluster.

### <a name="kubernetes-deployment-workflow"></a>Workflow du déploiement Kubernetes

Le schéma suivant illustre le processus général de déploiement du cluster.

![Processus de déploiement Kubernetes](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>Étapes du déploiement

1. Collecter des paramètres d’entrée à partir de l’élément Place de marché.

    Entrer les valeurs dont vous avez besoin pour configurer le cluster Kubernetes, y compris :
    -  **Nom d’utilisateur :** nom d’utilisateur pour les machines virtuelles Linux qui font partie du cluster Kubernetes et de DVM.
    -  **Clé publique SSH** : clé utilisée pour l’autorisation sur toutes les machines Linux créées dans le cadre du cluster Kubernetes et de DVM.
    -  **Principal du service** : ID utilisé par le fournisseur cloud d’Azure Kubernetes. Il s’agit de l’ID client identifié en tant qu’ID d’application lorsque vous avez créé votre principal de service. 
    -  **Clé secrète client** : clé que vous avez créée au moment de la création de votre principal du service.

2. Créer la machine virtuelle de déploiement et l’extension de script personnalisé.
    -  Créer les machines virtuelles Linux de déploiement au moyen de l’image Linux de la Place de marché, **Ubuntu Server 16. 04-LTS**.
    -  Télécharger et exécuter l'extension du script personnalisé à partir de la Place de marché. Le script est **Script personnalisé pour Linux 2.0**.
    -  Exécuter le script personnalisé DVM. Le script effectue les tâches suivantes :
        1. Obtient le point de terminaison de la galerie à partir du point de terminaison de métadonnées Azure Resource Manager.
        2. Obtient l’ID de ressource Active Directory à partir du point de terminaison des métadonnées Azure Resource Manager.
        3. Charge le modèle d’API pour le moteur AKS.
        4. Déploie le moteur AKS sur le cluster Kubernetes et enregistre le profil cloud Azure Stack sur `/etc/kubernetes/azurestackcloud.json`.
3. Créer les machines virtuelles principales.

4. Télécharger et exécuter des extensions de scripts personnalisés.

5. Exécuter le script principal.

    Le script effectue les tâches suivantes :
    - Installe les ressources etcd, Docker et Kubernetes, telles que kubelet. etcd est un magasin de valeurs de clés distribuées qui offre un moyen de stocker des données sur un cluster de machines. Docker prend en charge des virtualisations extrêmement simples au niveau du système d’exploitation, et connues sous l’appellation de conteneurs. Kubelet est l’agent de nœud qui s’exécute sur chaque nœud Kubernetes.
    - Configure le service **etcd**.
    - Configure le service **kubelet**.
    - Démarre Kubelet. Cette tâche implique les étapes suivantes :
        1. Démarrer le service de l’API.
        2. Démarrer le service du contrôleur.
        3. Démarrer le service du planificateur.
6. Créer des machines virtuelles d’agents.

7. Télécharger et exécuter les extensions de scripts personnalisés.

7. Exécuter le script de l’agent. Le script personnalisé de l’agent effectue les tâches suivantes :
    - Installe **etcd**.
    - Configure le service **kubelet**.
    - Rejoint le cluster Kubernetes

## <a name="steps-to-troubleshoot-kubernetes"></a>Étapes pour résoudre les problèmes de Kubernetes

Vous pouvez collecter et examiner des journaux d’activité liés au déploiement sur les machines virtuelles qui prennent en charge votre cluster Kubernetes. Contactez votre administrateur Azure Stack pour vérifier la version d’Azure Stack à utiliser et obtenir à partir d’Azure Stack les journaux d’activité liés à votre déploiement.

1. Examinez l’[état du déploiement](#review-deployment-status) et récupérez les journaux à partir du nœud principal de votre cluster Kubernetes.
2. Assurez-vous que vous utilisez la version la plus récente d’Azure Stack. Si vous ne savez pas quelle version vous utilisez, contactez votre administrateur Azure Stack.
3.  Passez en revue vos fichiers de création des machines virtuelles. Vous avez peut-être rencontré les problèmes suivants :  
    - La clé publique peut être non valide. Examinez la clé que vous avez créée.  
    - La création de machines virtuelles peut avoir déclenché une erreur interne ou une erreur de création. Différents facteurs peuvent entraîner des erreurs, y compris les limites de capacité de votre abonnement Azure Stack.
    - Assurez-vous que le nom de domaine complet (FQDN) de la machine virtuelle commence par un préfixe dupliqué.
4.  Si la machine virtuelle est **OK**, passez à l’évaluation du DVM. Si le DVM affiche un message d’erreur :

    - La clé publique peut être non valide. Examinez la clé que vous avez créée.  
    - Contactez votre administrateur Azure Stack pour récupérer les journaux d’activité Azure Stack en utilisant les points de terminaison privilégiés. Pour plus d’informations, voir [Outils de diagnostics Azure Stack](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep-to-collect-diagnostic-logs).
5. Si vous avez une question concernant votre déploiement, vous pouvez la poster ou regarder si quelqu’un y a déjà répondu sur le [Forum Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="review-deployment-status"></a>Examiner l’état du déploiement

Lorsque vous déployez votre cluster Kubernetes, vous pouvez consulter l’état du déploiement pour détecter d’éventuels problèmes.

1. Ouvrez le [portail Azure Stack](https://portal.local.azurestack.external).
2. Sélectionnez **Groupe de ressources**, puis sélectionnez le nom du groupe de ressources utilisé lors du déploiement du cluster Kubernetes.
3. Sélectionnez **Déploiements**, puis le **Nom du déploiement**.

    ![Résoudre les problèmes de Kubernetes : sélectionner le déploiement](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Consultez la fenêtre de résolution des problèmes. Chaque ressource déployée fournit les informations suivantes :
    
    | Propriété | Description |
    | ----     | ----        |
    | Ressource | Nom de la ressource. |
    | Type | Fournisseur de la ressource et type de la ressource. |
    | Statut | État de l’élément. |
    | TimeStamp | Horodatage UTC du temps. |
    | Détails de l’opération | Informations détaillées sur l’opération, par exemple le fournisseur de ressources concerné par l’opération, le point de terminaison de la ressource et le nom de la ressource. |

    Chaque élément affiche une icône d’état verte ou rouge.

## <a name="review-deployment-logs"></a>Examiner les journaux de déploiement

Si le portail Azure Stack ne fournit pas suffisamment d’informations pour vous permettre de résoudre les problèmes ou de surmonter un échec de déploiement, l’étape suivante consiste à analyser en profondeur les journaux de cluster. Pour récupérer manuellement les journaux de déploiement, vous devez généralement vous connecter à l’une des machines virtuelles maîtres du cluster. Une autre approche plus simple consiste à télécharger et exécuter le [script Bash](https://aka.ms/AzsK8sLogCollectorScript) suivant fourni par l’équipe Azure Stack. Ce script se connecte à la machine virtuelle de déploiement et aux machines virtuelles du cluster, collecte les journaux système et de cluster appropriés, puis les télécharge sur votre station de travail.

### <a name="prerequisites"></a>Prérequis

Vous avez besoin d’une invite Bash sur la machine que vous utilisez pour gérer Azure Stack. Sur un ordinateur Windows, vous pouvez obtenir une invite Bash en installant [Git pour Windows](https://git-scm.com/downloads). Après l’installation, recherchez _Git Bash_ dans votre menu Démarrer.

### <a name="retrieving-the-logs"></a>Récupération des journaux

Suivez ces étapes pour collecter et télécharger les journaux de cluster :

1. Ouvrez une invite Bash. À partir d’un ordinateur Windows, ouvrez _Git Bash_ ou exécutez : `C:\Program Files\Git\git-bash.exe`.

2. Téléchargez le script de collecteur de journaux en exécutant les commandes suivantes dans votre invite Bash :

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    chmod 744 getkuberneteslogs.sh
    ```

3. Recherchez les informations requises par le script et exécutez-le :

    | Paramètre           | Description                                                                                                      | Exemples                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -d, --vmd-host      | Adresse IP publique ou nom de domaine complet (FQDN) de la machine virtuelle de déploiement. Le nom de la machine virtuelle commence par `vmd-`. | Adresse IP : 192.168.102.38<br>DNS : vmd-myk8s.local.cloudapp.azurestack.external |
    | -h, --help  | Utilisation de la commande d’impression. | |
    | -i, --identity-file | Chemin du fichier de clé privée RSA passé à l’élément de la Place de marché lors de la création du cluster Kubernetes. Nécessaire pour la connexion à distance aux nœuds Kubernetes. | C:\data\id_rsa.pem (PuTTY)<br>~/.ssh/id_rsa (SSH)
    | -m, --master-host   | Adresse IP publique ou nom de domaine complet (FQDN) d’un nœud maître Kubernetes. Le nom de la machine virtuelle commence par `k8s-master-`. | Adresse IP : 192.168.102.37<br>Nom de domaine complet : k8s-12345.local.cloudapp.azurestack.external      |
    | -u, --user          | Nom d’utilisateur passé à l’élément de la Place de marché lors de la création du cluster Kubernetes. Nécessaire pour la connexion à distance aux nœuds Kubernetes. | azureuser (valeur par défaut) |


   Quand vous ajoutez vos valeurs de paramètres, votre commande peut ressembler à l’exemple suivant :

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmd-host 192.168.102.37
     ```

4. Après quelques minutes, le script envoie les journaux collectés dans un répertoire nommé `KubernetesLogs_{{time-stamp}}`. Vous y trouverez un répertoire pour chaque machine virtuelle qui appartient au cluster.

    Le script de collecteur de journaux recherche également des erreurs dans les fichiers journaux et présente une procédure de résolution s’il rencontre un problème connu. Veillez à exécuter la dernière version du script afin d’augmenter les chances de détecter les problèmes connus.

> [!Note]  
> Consultez ce [dépôt](https://github.com/msazurestackworkloads/azurestack-gallery/tree/master/diagnosis) GitHub pour en savoir plus sur le script de collecteur de journaux.

## <a name="next-steps"></a>Étapes suivantes

[Déployer Kubernetes sur Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)

[Ajouter un cluster Kubernetes à la Place de marché (pour l’opérateur Azure Stack)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes sur Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
