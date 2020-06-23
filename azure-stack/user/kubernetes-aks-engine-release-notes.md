---
title: Notes de publication du moteur AKS (Azure Kubernetes Service) sur Azure Stack Hub
description: Découvrez les étapes à suivre avec la mise à jour du moteur AKS sur Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 4/23/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 4/23/2020
ms.openlocfilehash: 9fd9671d4dbdad1dbf43b151c481fdbd9ca2c0ab
ms.sourcegitcommit: f0ee2a3af78dd6d6e2806710681d52b763948967
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84533686"
---
# <a name="release-notes-for-the-aks-engine-on-azure-stack-hub"></a>Notes de publication du moteur AKS sur Azure Stack Hub

Cet article décrit le contenu du moteur Azure Kubernetes Service (AKS) sur la mise à jour d’Azure Stack Hub. La mise à jour inclut des améliorations et des correctifs pour la dernière version du moteur AKS ciblé sur la plateforme Azure Stack Hub. Notez que cette rubrique n’est pas destinée à documenter les informations de version relatives au moteur AKS pour Azure global.

## <a name="update-planning"></a>Planification des mises à jour

La commande de mise à niveau du moteur AKS automatise entièrement le processus de mise à niveau de votre cluster. Elle se charge des tâches liées aux machines virtuelles, à la mise en réseau, au stockage, à Kubernetes et à l’orchestration. Avant d’appliquer la mise à jour, veillez à consulter les informations contenues dans cet article.

### <a name="upgrade-considerations"></a>Points importants relatifs à la mise à niveau

-   Utilisez-vous l’élément de la Place de marché appropriée pour votre version du moteur AKS, à savoir la distribution d’images de base AKS Ubuntu 16.04-LTS ? Les versions sont disponibles dans la section [Télécharger les versions de la nouvelle l’image de base Ubuntu AKS et du moteur AKS](#download-new-image-and-aks-engine).

-   Utilisez-vous la spécification de cluster (apimodel.json) et le groupe de ressources adaptés au cluster cible ? Quand vous avez initialement déployé le cluster, ce fichier a été généré dans votre répertoire de sortie. Consultez les paramètres de la commande « deploy » dans [Déployer un cluster Kubernetes](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-cluster#deploy-a-kubernetes-cluster).

-   Utilisez-vous une machine fiable pour exécuter le moteur AKS et à partir de laquelle vous effectuez les opérations de mise à niveau ?

-   Si vous mettez à jour un cluster opérationnel avec des charges de travail actives, vous pouvez appliquer la mise à niveau sans les affecter, en supposant que le cluster se trouve dans des conditions de charge normale. Toutefois, vous devez disposer d’un cluster de sauvegarde s’il s’avère nécessaire de rediriger les utilisateurs vers celui-ci.

-   Si possible, exécutez la commande à partir d’une machine virtuelle au sein de l’environnement Azure Stack Hub pour réduire les tronçons réseau et les échecs de connectivité potentiels.

-   Vérifiez que votre abonnement dispose d’un quota suffisant pour l’ensemble du processus. De nouvelles machines virtuelles sont allouées pendant le processus. Le nombre de machines virtuelles qui en résulte est identique à celui d’origine, mais prévoyez la création de quelques machines virtuelles de plus au cours du processus.

-   Aucune mise à jour système ni tâche planifiée ne sont prévues.

-   Configurez une mise à niveau intermédiaire sur un cluster configuré avec les mêmes valeurs que le cluster de production et testez-y la mise à niveau avant de l’appliquer à votre cluster de production.

### <a name="use-the-upgrade-command"></a>Utiliser la commande de mise à niveau

Vous devez utiliser la commande « upgrade » du moteur AKS comme décrit dans l’article suivant : [Mettre à niveau un cluster Kubernetes sur Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-upgrade).

### <a name="upgrade-interruptions"></a>Interruptions de la mise à niveau

Parfois, des facteurs inattendus interrompent la mise à niveau du cluster. Une interruption peut se produire quand le moteur AKS signale une erreur ou quand il arrive quelque chose au processus d’exécution du moteur AKS. Examinez la cause de l’interruption, résolvez-la, puis soumettez à nouveau la même commande de mise à niveau pour poursuivre le processus de mise à niveau. La commande **upgrade** est idempotente et doit reprendre la mise à niveau du cluster une fois la commande à nouveau soumise. Normalement, les interruptions augmentent la durée d’exécution de la mise à jour, mais elles ne doivent pas en affecter l’exécution.

### <a name="estimated-upgrade-time"></a>Durée de mise à niveau estimée

La durée estimée est comprise entre 12 et 15 minutes par machine virtuelle présente dans le cluster. Par exemple, la mise à niveau d’un cluster de 20 nœuds peut prendre jusqu’à cinq (5) heures environ.

## <a name="download-new-image-and-aks-engine"></a>Télécharger une nouvelle image et le moteur AKS

Téléchargez les nouvelles versions de l’image de base Ubuntu AKS et du moteur AKS.

Comme expliqué dans la documentation du moteur AKS pour Azure Stack Hub, le déploiement d’un cluster Kubernetes exige deux composants principaux : 
- Binaire aks-engine
- Distribution d’images de base AKS Ubuntu 16.04-LTS

Les nouvelles versions de ces composants sont disponibles avec cette mise à jour :

-   L’opérateur Azure Stack Hub doit télécharger une nouvelle image de base Ubuntu AKS :

    -   Nom : `AKS Base Ubuntu 16.04-LTS Image Distro, March 2020`
    -   Version : `2020.03.19`
    -   Suivez les instructions de l’article [Ajouter les prérequis du moteur Azure Kubernetes Services (AKS) à la Place de marché Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-aks-engine)

-   L’administrateur de cluster Kubernetes doit télécharger la nouvelle version 0.48.0 d’aks-engine. Consultez les instructions de l’article [Installer le moteur AKS sur Linux dans Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-linux). Vous pouvez suivre le même processus que celui que vous utilisez pour installer le cluster pour la première fois. La mise à jour remplace le binaire précédent. Par exemple, si vous avez utilisé le script `get-akse.sh`, effectuez les mêmes étapes que celles décrites dans l’article [Installer dans un environnement connecté](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-linux#install-in-a-connected-environment). Le même processus s’applique si vous effectuez l’installation sur un système Windows. Consultez l’article [Installer le moteur AKS sur Windows dans Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-windows).

## <a name="kubernetes-version-upgrade-path"></a>Chemin de mise à niveau de la version de Kubernetes

La version actuelle et la version de mise à niveau sont indiquées dans le tableau suivant pour Azure Stack Hub. Ne suivez pas la commande aks-engine `get-versions`, car la commande inclut également les versions prises en charge dans Azure global. Le tableau de versions et de mises à niveau suivant s’applique au cluster du moteur AKS dans Azure Stack Hub.

| **Version actuelle** | **Mise à niveau disponible** |
| --------------------| ----------------------|
| 1.14.7 | 1.15.10 |
| 1.14.8 | 1.15.10 |
| 1.15.4 | 1.15.10 |
| 1.15.5 | 1.15.10 |

## <a name="whats-new"></a>Nouveautés 

-   Prise en charge de Kubernetes version 1.15.10 ([\#2834](https://github.com/Azure/aks-engine/issues/2834)). Lors du déploiement d’un nouveau cluster, veillez à spécifier dans votre fichier JSON de modèle d’API (fichier de définition de cluster a.k.s.), le numéro de version release et le numéro de version mineure. Un exemple est disponible : [kubernetes-azurestack.json](https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json) :

    - `"orchestratorRelease": "1.15`

    - `"orchestratorVersion": "1.15.10"`

    > [!Note]  
    > Si la version de Kubernetes n’est pas explicitement fournie dans le fichier JSON de modèle d’API, la version `1.15` est utilisée ([\#2932](https://github.com/Azure/aks-engine/issues/2932)) et orchestratorVersion a par défaut la valeur` 1.15.11`, ce qui génère une erreur lors du déploiement du cluster.

-   Avec aks-engine v0.43.1, les paramètres de fréquence par défaut du fournisseur de cloud pour exécuter sa boucle de contrôle et d’autres tâches ne fonctionnent pas correctement avec les limites de seuil Azure Stack Hub Resource Manager pour les demandes entrantes. Cette mise à jour change les valeurs par défaut d’Azure Stack Hub pour réduire le chargement de nouvelles tentatives vers Azure Stack Hub Resource Manager ([\#2861](https://github.com/Azure/aks-engine/issues/2861)).

-   Une nouvelle étape de vérification dans aks-engine entraîne l’arrêt de l’exécution ou l’affichage d’avertissements si le fichier JSON de modèle d’API contient des propriétés non prises en charge par Azure Stack Hub ([\#2717](https://github.com/Azure/aks-engine/issues/2717)).

-   Avec un nouvel archivage des vérifications, aks-engine valide la disponibilité de la version de l’image de base AKS nécessaire pour la version d’aks-engine en cours d’exécution ([\#2342](https://github.com/Azure/aks-engine/issues/2342)). Cela se produit après l’analyse du fichier de modèle d’API et avant l’appel d’Azure Stack Hub Resource Manager.

-   La nouvelle option aks-engine « --control-plane-only » de la commande « upgrade » permet à l’utilisateur de mettre à niveau des opérations afin de cibler uniquement les machines virtuelles maîtres ([\#2635](https://github.com/Azure/aks-engine/issues/2635)).

-   Mises à jour vers Linux Kernel version 4.15.0-1071-azure pour Ubuntu 16.04-LTS. Pour plus d’informations, consultez « [Package : linux-image-4.15.0-1071-azure (4.15.0-1071.76) \[sécurité\]](https://packages.ubuntu.com/xenial/linux-image-4.15.0-1071-azure) ».

-   Nouvelles mises à jour d’hypercubes pour prendre en charge les versions 1.14.8 et 1.15.10 de Kubernetes.

-   Mettez à jour kubectl pour qu’il corresponde à la version de Kubernetes pour le cluster. Ce composant est disponible dans les nœuds principaux du cluster Kubernetes. Vous pouvez l’exécuter par le biais de SSH dans un maître.

-   Mises à jour du module complémentaire Azure Container Monitor avec la dernière [version de février 2020](https://github.com/microsoft/Docker-Provider/blob/ci_feature_prod/README.md) ([\#2850](https://github.com/Azure/aks-engine/issues/2850)).

-   Mise à niveau de `coredns` vers la version v1.6.6 ([\#2555](https://github.com/Azure/aks-engine/issues/2555)).

-   Mise à niveau de `etcd` vers la version 3.3.18 ([\#2462](https://github.com/Azure/aks-engine/issues/2462)).

-   Mise à niveau de `moby` vers la version 3.0.11 ([\#2887](https://github.com/Azure/aks-engine/issues/2887)).

-   Avec cette version, le moteur AKS réduit les dépendances de `k8s.gcr.io` afin d’utiliser maintenant le `Kubernetes MCR registry @ mcr.microsoft.com` officiel lors de la création de ses images ([\#2722](https://github.com/Azure/aks-engine/issues/2722)).

## <a name="known-issues"></a>Problèmes connus

-  Le déploiement de plusieurs services Kubernetes en parallèle à l’intérieur d’un même cluster peut entraîner une erreur dans la configuration de l’équilibreur de charge de base. Déployez un service à la fois si possible.

-  L’exécution de la commande aks-engine get-versions produit des informations applicables à Azure et à Azure Stack Hub. Toutefois, il n’existe pas de méthode explicite pour identifier ce qui correspond à Azure Stack Hub. N’utilisez pas cette commande pour déterminer quelles versions sont disponibles pour la mise à niveau. Utilisez le tableau de référence des mises à niveau décrit ci-dessus.

-  Étant donné que l’outil aks-engine est un dépôt de code source de partage sur Azure et Azure Stack Hub. L’examen de nombreuses notes de publication et demandes de tirage (pull request) vous incitera à croire que l’outil prend en charge d’autres versions de Kubernetes et de la plateforme de système d’exploitation en plus de celles listées ci-dessus. Veuillez les ignorer et utiliser le tableau de versions ci-dessus comme guide officiel pour cette mise à jour.

## <a name="reference"></a>Informations de référence

Voici une liste de certains bogues résolus, ainsi que l’ensemble complet des notes de publication de la version 0.44.0 à la version 0.48.0. Notez que la dernière liste inclura les éléments Azure et Azure Stack Hub

### <a name="bug-fixes"></a>Résolution des bogues

-   Guillemets manquants autour de `userAssignedIdentityId` dans Windows `azure.json` ([\#2327](https://github.com/Azure/aks-engine/issues/2327))

-   Le module complémentaire `update config` est en version de mise à niveau uniquement ([\#2282](https://github.com/Azure/aks-engine/issues/2282))

-   Augmentation du délai d’attente pour l’obtention de l’adresse IP de gestion sur les nœuds Windows ([\#2284](https://github.com/Azure/aks-engine/issues/2284))

-   Ajout du fichier zip Azure CNI 1.0.28 au disque dur virtuel Windows ([\#2268](https://github.com/Azure/aks-engine/issues/2268))

-   Correction de l’ordre des valeurs par défaut pour définir IPAddressCount ([\#2358](https://github.com/Azure/aks-engine/issues/2358))

-   Mise à jour pour utiliser un seul fichier yaml de l’agent OMS pour toutes les versions de K8s afin d’éviter les erreurs manuelles et faciliter la maintenance ([\#2692](https://github.com/Azure/aks-engine/issues/2692))

### <a name="release-notes"></a>Notes de publication

Voici l’ensemble complet des notes de publication combinées d’Azure et d’Azure Stack Hub

-   https://github.com/Azure/aks-engine/releases/tag/v0.44.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.44.1
-   https://github.com/Azure/aks-engine/releases/tag/v0.44.2
-   https://github.com/Azure/aks-engine/releases/tag/v0.45.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.1
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.2
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.3
-   https://github.com/Azure/aks-engine/releases/tag/v0.47.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.48.0

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [le moteur AKS sur Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md)
