---
title: Accéder au tableau de bord Kubernetes dans Azure Stack | Microsoft Docs
description: Découvrez comment accéder au tableau de bord Kubernetes dans Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 2c1a762f002e5058e11857117b4210ad0b59e564
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277551"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack"></a>Accéder au tableau de bord Kubernetes dans Azure Stack 

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack* 
> [!Note]   
> Kubernetes sur Azure Stack est en préversion. Un scénario Azure Stack déconnecté n'est actuellement pas pris en charge par la préversion. Utiliser uniquement l’élément de la place de marché pour les scénarios de développement et de test.

Kubernetes comprend un tableau de bord web qui peut être utilisé pour les opérations de gestion de base. Ce tableau de bord vous permet d’afficher l’état de santé de base et les métriques concernant vos applications, ainsi que de créer et déployer des services et de modifier les applications existantes. Cet article vous montre comment configurer le tableau de bord Kubernetes sur Azure Stack.

## <a name="prerequisites-for-kubernetes-dashboard"></a>Prérequis pour le tableau de bord Kubernetes

* Cluster Kubernetes Azure Stack

    Vous devez avoir déployé un cluster Kubernetes sur Azure Stack. Pour plus d’informations, consultez [Déployer Kubernetes](azure-stack-solution-template-kubernetes-deploy.md).

* Client SSH

    Vous avez besoin d’un client SSH pour vous connecter en toute sécurité à votre nœud maître dans le cluster. Si vous utilisez Windows, vous pouvez employer [PuTTY](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm). Vous avez besoin de la clé privée utilisée quand vous avez déployé votre cluster Kubernetes.

* FTP (PSCP)

    Vous pouvez également avoir besoin d’un client FTP qui prend en charge SSH et le protocole SFTP (SSH File Transfer Protocol) pour transférer les certificats à partir du nœud maître sur votre ordinateur de gestion Azure Stack. Vous pouvez utiliser [FileZilla](https://filezilla-project.org/download.php?type=client). Vous avez besoin de la clé privée utilisée quand vous avez déployé votre cluster Kubernetes.

## <a name="overview-of-steps-to-enable-dashboard"></a>Vue d’ensemble des étapes pour activer le tableau de bord

1.  Exporter les certificats Kubernetes à partir du nœud maître du cluster. 
2.  Importer les certificats sur votre ordinateur de gestion Azure Stack.
2.  Ouvrir le tableau de bord web Kubernetes. 

## <a name="export-certificate-from-the-master"></a>Exporter un certificat à partir du nœud maître 

Vous pouvez récupérer l’URL du tableau de bord à partir du nœud maître dans votre cluster.

1. Obtenez l’adresse IP publique et le nom d’utilisateur pour votre maître de cluster à partir du tableau de bord Azure Stack. Pour obtenir ces informations :

    - Connectez-vous au [portail Azure Stack](https://portal.local.azurestack.external/)
    - Sélectionnez **Tous les services** > **Toutes les ressources**. Recherchez le maître dans votre groupe de ressources de cluster. Le maître se nomme `k8s-master-<sequence-of-numbers>`. 

2. Ouvrez le nœud maître dans le portail. Copiez l’**adresse IP publique**. Cliquez sur **Se connecter** pour obtenir votre nom d’utilisateur dans la zone **Connexion avec un compte local de machine virtuelle**. Il s’agit du même nom d’utilisateur que vous avez défini lors de la création de votre cluster. Utilisez l’adresse IP publique plutôt que l’adresse IP privée listée dans le panneau de connexion.

3.  Ouvrez un client SSH pour vous connecter au maître. Si vous travaillez sur Windows, vous pouvez utiliser [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) pour créer la connexion. Vous utilisez l’adresse IP publique pour le nœud maître, le nom d’utilisateur, puis ajoutez la clé privée utilisée lors de la création du cluster.

4.  Quand le terminal se connecte, tapez `kubectl` pour ouvrir le client de ligne de commande Kubernetes.

5. Exécutez la commande suivante :

    ```Bash   
    kubectl cluster-info 
    ``` 
    Recherchez l’URL du tableau de bord. Par exemple : `https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  Extrayez le certificat auto-signé et convertissez-le au format PFX. Exécutez la commande suivante :

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  Obtenez la liste des secrets dans l’espace de noms **kube-system**. Exécutez la commande suivante :

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    Notez la valeur kubernetes-dashboard-token-\<XXXXX>. 

8.  Obtenez le jeton et enregistrez-le. Mettez à jour `kubernetes-dashboard-token-<####>` avec la valeur du secret de l’étape précédente.

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>Importer le certificat

1. Ouvrez FileZilla et connectez-vous au nœud maître. Vous avez besoin des éléments suivants :

    - adresse IP publique du nœud maître
    - nom d’utilisateur
    - secret privé
    - **protocole SFTP (SSH File Transfer Protocol)**

2. Copiez `/etc/kubernetes/certs/client.pfx` et `/etc/kubernetes/certs/ca.crt` sur votre ordinateur de gestion Azure Stack.

3. Notez l’emplacement des fichiers. Mettez à jour le script avec les emplacements, puis ouvrez PowerShell à l’aide d’une invite de commandes avec élévation de privilèges. Exécutez le script mis à jour :  

    ```powershell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>Ouvrir le tableau de bord Kubernetes 

1. Désactivez le bloqueur de fenêtres publicitaires de votre navigateur web.

2. Pointez votre navigateur sur l’URL indiquée quand vous avez exécuté la commande `kubectl cluster-info`. Par exemple : https:\//azurestackdomainnamefork8sdashboard/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy 
3. Sélectionnez le certificat client.
4. Entrez le jeton. 
5. Reconnectez-vous à la ligne de commande Bash sur le nœud maître et accordez des autorisations à `kubernetes-dashboard`. Exécutez la commande suivante :

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    Le script donne à `kubernetes-dashboard` des privilèges d’administrateur de cloud. Pour plus d’informations, consultez [Pour les clusters où RBAC est activé](https://docs.microsoft.com/azure/aks/kubernetes-dashboard).

Vous pouvez utiliser le tableau de bord. Pour plus d’informations sur le tableau de bord Kubernetes, consultez [Tableau de bord de l’interface utilisateur web de Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 

![Tableau de bord Kubernetes Azure Stack](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="next-steps"></a>Étapes suivantes 

[Déployer Kubernetes sur Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)  

[Ajouter un cluster Kubernetes à la Place de marché (pour l’opérateur Azure Stack)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)  

[Kubernetes sur Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
