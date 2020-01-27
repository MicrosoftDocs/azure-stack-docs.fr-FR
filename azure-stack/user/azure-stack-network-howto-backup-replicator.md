---
title: Répliquer des ressources sur plusieurs abonnements Azure Stack Hub | Microsoft Docs
description: Apprenez à répliquer des ressources à l’aide du jeu de scripts du réplicateur d’abonnements Azure Stack Hub.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/07/2019
ms.author: mabrigg
ms.reviewer: rtiberiu
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 411dafa86d0ec61ff431cfea217f3721862b69f4
ms.sourcegitcommit: 7dd685fddf2f5d7a0c0a20fb8830ca5a061ed031
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76259798"
---
# <a name="replicate-resources-using-the-azure-stack-hub-subscription-replicator"></a>Répliquer des ressources à l’aide du réplicateur d’abonnements Azure Stack Hub

Vous pouvez utiliser le script PowerShell du réplicateur d’abonnements Azure Stack Hub pour copier les ressources entre des abonnements Azure Stack Hub, entre des tampons Azure Stack Hub, ou entre Azure Stack Hub et Azure. Le script du réplicateur lit et regénère les ressources Azure Resource Manager de différents abonnements Azure et Azure Stack Hub. Cet article décrit le fonctionnement et l'utilisation du script, et fournit une référence pour les opérations qui s'y rapportent.

Vous trouverez les scripts utilisés dans cet article dans le dépôt GitHub [Azure Intelligent Edge Patterns](https://github.com/Azure-Samples/azure-intelligent-edge-patterns). Les scripts sont situés dans le dossier [subscription replicator](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/tree/master/subscription%20replicator).

## <a name="subscription-replicator-overview"></a>Présentation du réplicateur d’abonnements

Le réplicateur d'abonnements Azure a été conçu pour être modulaire. Cet outil utilise un processeur principal qui orchestre la réplication des ressources. En outre, l’outil prend en charge des processeurs personnalisables qui jouent le rôle de modèles pour la copie de différents types de ressources. 

Le processeur principal est constitué des trois scripts suivants :

- **resource_retriever.ps1**

    - Génère des dossiers pour stocker les fichiers de sortie.

    - Définit le contexte pour l’abonnement source.

    - Récupère les ressources et les transmet à **resource_processor.ps1**.

- **resource_processor.ps1**

    - Traite la ressource transmise par **resource_retriever.ps1**.

    - Détermine le processeur personnalisé à utiliser et transmet les ressources.

- **post_process.ps1**

    - Traite la sortie générée par le processeur personnalisé pour préparer son déploiement dans l'abonnement cible.

    - Génère du code de déploiement pour déployer les ressources dans l’abonnement cible.

Les trois scripts contrôlent le flux d'informations de manière à permettre plus de flexibilité. La prise en charge de ressources supplémentaires, par exemple, n'implique aucune modification de code dans le processeur principal.

Les processeurs personnalisés, mentionnés ci-dessus, correspondent à des fichiers `ps1` qui déterminent la façon dont un type de ressource doit être traité. Un processeur personnalisé est toujours nommé d'après les données de type d'une ressource. Par exemple, si `$vm` contient un objet de machine virtuelle, l'exécution de `$vm`.Type génère `Microsoft.Compute/virtualMachines`. Ainsi, un processeur de machine virtuelle est nommé `virtualMachines_processor.ps1`. Ce nom doit être identique au nom qui s'affiche dans les métadonnées de la ressource, car cela permet au processeur principal de déterminer le processeur personnalisé à utiliser.

Un processeur personnalisé dicte la manière dont une ressource doit être répliquée en déterminant les informations importantes et la manière dont ces informations doivent être extraites des métadonnées de ressources. Le processeur personnalisé utilise ensuite toutes les données extraites pour générer un fichier de paramètres qui sera utilisé conjointement avec un modèle Azure Resource Manager pour déployer la ressource dans l’abonnement cible. Une fois traité par post_process. ps1, ce fichier de paramètres est stocké dans **Parameter_Files**.

La structure de fichiers du réplicateur contient un dossier nommé **Standardized_ARM_Templates**. Selon l’environnement source, les déploiements utilisent un de ces modèles Azure Resource Manager normalisés, à défaut de quoi un modèle Azure Resource Manager personnalisé doit être généré. Dans ce cas, un processeur personnalisé doit appeler un générateur de modèles Azure Resource Manager. Dans l’exemple précédent, le nom d’un générateur de modèles Azure Resource Manager pour machines virtuelles serait **virtualMachines_ARM_Template_Generator. ps1**. Le générateur de modèles Azure Resource Manager est chargé de créer un modèle Azure Resource Manager personnalisé en fonction des informations contenues dans les métadonnées d’une ressource. Par exemple, si la ressource de machine virtuelle comporte des métadonnées spécifiant qu'elle appartient à un groupe à haute disponibilité, le générateur de modèles Azure Resource Manager crée un modèle Azure Resource Manager avec du code spécifiant l’ID du groupe à haute disponibilité dont fait partie la machine virtuelle. Ainsi, lors de son déploiement sur le nouvel abonnement, la machine virtuelle est automatiquement ajoutée au groupe à haute disponibilité. Ces modèles personnalisés Azure Resource Manager sont stockés dans le dossier **Custom_ARM_Templates** situé dans le dossier **Standardized_ARM_Templates**. Post_processor. ps1 doit déterminer si un déploiement est supposé utiliser un modèle Azure Resource Manager normalisé ou personnalisé, et générer le code de déploiement correspondant.

Le script **post-Process. ps1** doit nettoyer les fichiers de paramètres et créer les scripts dont l'utilisateur se servira pour déployer les nouvelles ressources. Lors de la phase de nettoyage, le script remplace toutes les références à l’ID d’abonnement source, à l’ID de locataire et à l’emplacement par les valeurs cibles correspondantes. Il génère ensuite le fichier de paramètres dans le dossier **Parameter_Files**. Puis, il détermine si la ressource en cours de traitement utilise un modèle Azure Resource Manager personnalisé et génère le code de déploiement correspondant, qui utilise cmdlet **New-AzureRmResourceGroupDeployment**. Le code de déploiement est ensuite ajouté au fichier nommé **DeployResources. ps1** stocké dans le dossier **Deployment_Files**. Enfin, le script détermine le groupe de ressources auquel appartient la ressource et vérifie le script **DeployResourceGroups. ps1** pour vérifier si le code permettant de déployer ce groupe de ressources existe déjà. Si ce n’est pas le cas, il ajoute le code à ce script pour déployer le groupe de ressources, sinon il ne fait rien.

### <a name="dynamic-api-retrieval"></a>Récupération d'API dynamique

L’outil est doté d'une fonctionnalité de récupération d’API dynamique afin de permettre l'utilisation de la dernière version de l’API du fournisseur de ressources disponible dans l’abonnement source pour déployer les ressources dans l’abonnement cible :

![Figure de récupération d'API](./media/azure-stack-network-howto-backup-replicator/image1.png)

Figure de récupération d'API dans **resource_processor.ps1**.

Il est toutefois possible que la version de l’API du fournisseur de ressources de l’abonnement cible soit antérieure à celle de l’abonnement source et ne prenne pas en charge la version fournie par l’abonnement source. Dans ce cas, une erreur est générée lors du déploiement. Pour y remédier, mettez à jour les fournisseurs de ressources dans l’abonnement cible pour qu’ils correspondent à ceux de l’abonnement source.

### <a name="parallel-deployments"></a>Déploiements parallèles

L’outil nécessite un paramètre nommé **parallel**. Ce paramètre utilise une valeur booléenne qui indique si les ressources récupérées doivent être ou non déployées en parallèle. Si cette valeur est définie sur **true**, chaque appel à **New-AzureRmResourceGroupDeployment** porte l'indicateur **-asJob** et des blocs de code visant à attendre la fin des travaux parallèles sont ajoutés entre les déploiements des ensembles de ressources en fonction du type de ces dernières. Ainsi, toutes les ressources d’un type donné sont déployées préalablement au déploiement du type de ressource suivant. Si la valeur du paramètre **parallel** est définie sur **false**, les ressources sont déployées en série.

## <a name="add-additional-resource-types"></a>Ajout de types de ressources supplémentaires

L’ajout de nouveaux types de ressources est simple. Le développeur doit créer un processeur personnalisé, ainsi qu'un modèle Azure Resource Manager ou un générateur de modèles Azure Resource Manager. Ensuite, le développeur doit ajouter le type de ressource à ValidateSet pour le paramètre **$resourceType** et le groupe **$resourceTypes** dans resource_retriever. ps1. L’ajout du type de ressource au groupe **$resourceTypes** doit se faire dans l'ordre qui convient. L’ordre du groupe détermine l’ordre dans lequel les ressources sont déployées et dès lors, il convient de prendre en compte les dépendances. Enfin, si le processeur personnalisé utilise un générateur de modèles Azure Resource Manager, ils doivent ajouter le nom du type de ressource au groupe **$customTypes** dans **post_process.ps1**.

## <a name="run-azure-subscription-replicator"></a>Exécution du réplicateur d'abonnements Azure

Pour exécuter le réplicateur d'abonnements Azure (v3), vous devez lancer resource_retriever. ps1, en fournissant tous les paramètres. Pour le paramètre **resourceType**, une option permet de choisir **Tous** plutôt qu’un seul type de ressource. Si **Tous** est sélectionné, resource_retriever. ps1 traite toutes les ressources dans un certain ordre afin de faire en sorte qu'une fois le déploiement exécuté, les ressources dépendantes soient déployées en premier. Par exemple, les réseaux virtuels sont déployés avant les machines virtuelles car le déploiement de ces dernières requiert un réseau virtuel.

Une fois l’exécution du script terminée, trois nouveaux dossiers sont créés : **Deployment_Files**, **Parameter_Files** et **Custom_ARM_Templates**.

 > [!Note]  
 > Avant d’exécuter l’un des scripts générés, vous devez définir l’environnement qui convient, vous connecter à l’abonnement cible (par exemple, dans la nouvelle instance Azure Stack Hub) et définir le répertoire de travail sur le dossier **Deployment_Files**.

Deployment_Files contient les deux fichiers **DeployResourceGroups.ps1** et **DeployResources.ps1**. L’exécution de DeployResourceGroups. ps1 déploie les groupes de ressources. L’exécution de DeployResources. ps1 déploie toutes les ressources traitées. Si l’outil a été exécuté avec **Tous** ou **Microsoft.Compute/virtualMachines** en tant que type de ressource, DeployResources.ps1 invite l’utilisateur à entrer un mot de passe d’administrateur de machine virtuelle qui sera utilisé pour créer toutes les machines virtuelles.

### <a name="example"></a>Exemple

1.  Exécutez le script.

    ![Exécuter le script](./media/azure-stack-network-howto-backup-replicator/image2.png)

    > [!Note]  
    > N'oubliez pas de configurer l'environnement source et le contexte de l'abonnement pour l'instance PS. 

2.  Examinez les dossiers nouvellement créés :

    ![Examinez les dossiers](./media/azure-stack-network-howto-backup-replicator/image4.png)

3.  Définissez le contexte sur l'abonnement cible, remplacez le dossier par **Deployment_Files**, déployez les groupes de ressources, puis lancez le déploiement des ressources.

    ![Configurez et démarrez le déploiement](./media/azure-stack-network-howto-backup-replicator/image6.png)

4.  Exécutez `Get-Job` pour vérifier l'état. Get-Job | Receive-Job renverront les résultats.

## <a name="clean-up"></a>Nettoyer

Le dossier replicatorV3 contient un fichier nommé **cleanup_generated_items.ps1** qui supprime les dossiers **Deployment_Files**, **Parameter_Files** et **Custom_ARM_Templates**, de même que leur contenu.

## <a name="subscription-replicator-operations"></a>Opérations du réplicateur d’abonnements

Le réplicateur d’abonnements Azure (v3) peut actuellement répliquer les types de ressources suivants :

- Microsoft.Compute/availabilitySets

- Microsoft.Compute/virtualMachines

- Microsoft.Network/loadBalancers

- Microsoft.Network/networkSecurityGroups

- Microsoft.Network/publicIPAddresses

- Microsoft.Network/routeTables

- Microsoft.Network/virtualNetworks

- Microsoft.Network/virtualNetworkGateways

- Microsoft.Storage/storageAccounts

Si l'outil est exécuté avec le type de ressource **Tous**, l’ordre ci-dessous sera suivi lors de la réplication et du déploiement (ci-dessous, la configuration de toutes les ressources est répliquée, à savoir référence SKU, offre, etc.) :

- Microsoft.Network/virtualNetworks

    - Réplique :       - Tous les espaces d'adressage       - Tous les sous-réseaux

- Microsoft.Network/virtualNetworkGateways

    - Réplique :       - Configuration des adresses IP publiques       - Configuration des sous-réseaux       - Type de VPN       - Type de passerelle

- Microsoft.Network/routeTables

- Microsoft.Network/networkSecurityGroups

    - Réplique :       - Toutes les règles de sécurité entrantes et sortantes

- Microsoft.Network/publicIPAddresses

- Microsoft.Network/loadBalancers

    - Réplique :       - Adresses IP privées       - Configuration des adresses IP publiques       - Configuration des sous-réseaux
    
- Microsoft.Compute/availabilitySets

    - Réplique :       - Nombre de domaines d'erreur       - Nombre de domaines de mise à jour

- Microsoft.Storage/storageAccounts

- Microsoft.Compute/virtualMachines
    - Réplique :  
            - Disques de données (sans données)  
            - Taille de machine virtuelle  
            - Système d’exploitation  
            - Configuration du compte de stockage de diagnostic  
            - Configuration IP publique  
            - Interface réseau  
            - Adresse IP privée de l’interface réseau  
            - Configuration du groupe de sécurité réseau  
            - Configuration du groupe à haute disponibilité  

> [!Note]  
> Crée uniquement des disques managés pour le disque du système d'exploitation et les disques de données. L'utilisation de comptes de stockage n'est actuellement pas prise en charge. 

### <a name="limitations"></a>Limites

L’outil peut répliquer les ressources d’un abonnement à un autre dans la mesure où les fournisseurs de ressources de l’abonnement cible prennent en charge toutes les ressources et options répliquées à partir de l’abonnement source.

Pour garantir la réussite de la réplication, assurez-vous que les versions des fournisseurs de ressources de l'abonnement cible correspondent à celles de l'abonnement source.

En cas de réplication d’une instance Azure commerciale vers une autre instance Azure commerciale, ou d’un abonnement à l’intérieur d’une instance Azure Stack Hub vers un autre abonnement dans la même instance Azure Stack Hub, la réplication des comptes de stockage entraîne des problèmes. En effet, les noms des comptes de stockage doivent être uniques dans toutes les instances Azure commerciales ou dans tous les abonnements d’une région/instance Azure Stack Hub. La réplication de comptes de stockage entre différentes instances Azure Stack Hub aboutit, car celles-ci correspondent à des régions/instances distinctes.



## <a name="next-steps"></a>Étapes suivantes

[Différences et considérations relatives aux réseaux Azure Stack Hub](azure-stack-network-differences.md)  
