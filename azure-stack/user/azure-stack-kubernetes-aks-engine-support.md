---
title: Stratégies de support pour le moteur AKS sur Azure Stack Hub
description: Cette rubrique contient les stratégies de support pour le moteur AKS sur Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/0102020
ms.openlocfilehash: 9969447ab737c6913576c73b2e53453a6cb8e1f6
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102231759"
---
# <a name="support-policies-for-aks-engine-on-azure-stack-hub"></a>Stratégies de support pour le moteur AKS sur Azure Stack Hub

Cet article fournit des informations sur les stratégies de support technique et les limitations pour le moteur AKS sur Azure Stack Hub. L’article décrit également l’élément Kubernetes de la Place de marché, des composants open source tiers et la gestion de la sécurité ou des correctifs. 

## <a name="self-managed-kubernetes-clusters-on-azure-stack-hub-with-aks-engine"></a>Clusters Kubernetes automanagés sur Azure Stack Hub avec le moteur AKS

Les composants cloud infrastructure as a service (IaaS), tels que les composants réseau ou de calcul, permettent aux utilisateurs d’accéder à des contrôles de niveau inférieur et à des options de personnalisation. Le moteur AKS permet à l’utilisateur d’établir en toute transparence des clusters Kubernetes à l’aide de ces composants IaaS. Les utilisateurs peuvent alors y accéder et influer sur tous les aspects de ses déploiements.

Lors de la création d’un cluster, le client définit les maîtres et les nœuds Worker Kubernetes créés par le moteur AKS. Les charges de travail du client sont exécutées sur ces nœuds. Les clients possèdent et peuvent voir ou modifier le maître et les nœuds Worker. Apporter des modifications aux nœuds sans faire preuve de prudence peut conduire à la perte de données et de charges de travail, entravant ainsi le bon fonctionnement du cluster. En outre, les opérations du moteur AKS, comme une mise à niveau ou une mise à l’échelle, se substituent à toutes les modifications hors limites. Par exemple, si le cluster contient des pods statiques, ceux-ci ne sont pas conservés après une opération de mise à niveau du moteur AKS.

Étant donné que les nœuds de cluster du client exécutent du code privé et stockent des données sensibles, le Support Microsoft peut y accéder de manière limitée uniquement. Le Support Microsoft ne peut pas se connecter, exécuter des commandes ou afficher les journaux de ces nœuds sans autorisation expresse du client ou de l’assistance.

## <a name="version-support"></a>Prise en charge de la version

La prise en charge de la version du moteur AKS suit le modèle établi par le reste de la stratégie de prise en charge d’Azure Stack Hub, à savoir que la prise en charge d’une version du moteur AKS sur Azure Stack Hub est basée sur la formule n-2. Par exemple, si la dernière version du moteur AKS est v0.55.0, les versions prises en charge sont : 0.48.0, 0.51.0, 0.55.0. Il est également important que la version suive la version de mise à jour d’Azure Stack Hub et le mappage correspondant à la version prise en charge du moteur AKS. Ces informations sont conservées dans les [notes de publication du moteur AKS](kubernetes-aks-engine-release-notes.md#aks-engine-and-azure-stack-version-mapping).

## <a name="aks-engine-supported-areas"></a>Domaines de prise en charge du moteur AKS

Microsoft fournit un support technique pour les éléments suivants :

-  Problèmes avec les commandes du moteur AKS : déployer, générer, mettre à niveau et mettre à l’échelle. L’outil doit être cohérent avec son comportement sur Azure.
-  Problèmes avec un cluster Kubernetes déployé en suivant les informations données dans [Vue d’ensemble du moteur AKS](azure-stack-kubernetes-aks-engine-overview.md).
-  Problèmes de connectivité à d’autres services Azure Stack Hub. 
-  Problèmes de connectivité avec l’API Kubernetes.
-  Problèmes liés à la fonctionnalité du fournisseur Azure Stack Hub Kubernetes et à la connectivité à Azure Resource Manager.
-  Problèmes liés à la configuration générée par le moteur AKS des artefacts natifs Azure Stack Hub comme les équilibreurs de charge, les groupes de sécurité réseau, les réseaux virtuels, les sous-réseaux, les interfaces réseau, la table de routage, les groupes à haute disponibilité, les adresses IP publiques, le compte de stockage et les machines virtuelles. 
-  Problèmes liés aux performances et à la latence du réseau.
-  Problèmes liés à l’image de base AKS utilisée par le moteur AKS dans les déploiements déconnectés 

## <a name="aks-engine-areas-not-supported"></a>Domaines non pris en charge du moteur AKS

Microsoft n’assure pas de support technique pour les éléments suivants :

-  Utilisation du moteur AKS sur Azure.
-  Élément Kubernetes de la Place de marché Azure Stack Hub.
-  Utilisation des options et modules complémentaires de définition de cluster du moteur AKS ci-après.
    -  Modules complémentaires non pris en charge :  
            - Identité du pod Azure AD  
            -  ACI Connector  
            -  Blobfuse Flex Volume  
            -  Cluster Autoscaler  
            -  Container Monitoring  
            -  KeyVault Flex Volume  
            -  NVIDIA Device Plugin  
            -  Rescheduler  
            -  SMB Flex Volume  
        
    -  Options de définition de cluster non prises en charge :  
            -  Under KubernetesConfig:  
                    -  cloudControllerManagerConfig  
                    -  enableDataEncryptionAtRest  
                    -  enableEncryptionWithExternalKms  
                    -  enablePodSecurityPolicy  
                    -  etcdEncryptionKey  
                    -  useInstanceMetadata  
                    -  useManagedIdentity  
                    -  azureCNIURLLinux  
                    -  azureCNIURLWindows  
            -  Under masterProfile:  
                    -  availabilityZones  
            -  Under agentPoolProfiles:  
                    -  availabilityZones  
                    -  singlePlacementGroup  
                    -  scaleSetPriority  
                    -  scaleSetEvictionPolicy  
                    -  acceleratedNetworkingEnabled  
                    -  acceleratedNetworkingEnabledWindows

-  Modifications de configuration Kubernetes conservées en dehors du magasin de configuration Kubernetes etcd. Par exemple, les pods statiques s’exécutant dans des nœuds du cluster.
-  Questions sur l’utilisation de Kubernetes. Par exemple, le Support Microsoft ne fournit pas de conseils sur la façon dont les contrôleurs d’entrée doivent être créés, sur l’utilisation des charges de travail d’application ou sur l’application d’outils ou de packages logiciels open source ou tiers.
-  Projets open source tiers non fournis avec le cluster Kubernetes déployé par le moteur AKS. Ces projets peuvent inclure Kubeadm, Kubespray, Native, Istio, Helm, Envoy, etc.
-  Logiciels tiers. Ces logiciels peuvent inclure des outils d’analyse de sécurité et des logiciels ou appareils réseau.
-  Problèmes de solutions multifournisseur ou multicloud. Par exemple, Microsoft n’assure pas le support des problèmes liés à l’exécution d’une solution de fournisseur cloud multipublic fédérée.
-  Personnalisations du réseau autres que celles listées dans la section [Domaines de prise en charge du moteur AKS](#aks-engine-supported-areas).
-  Les environnements de production doivent utiliser des clusters Kubernetes à haut niveau de disponibilité, c’est-à-dire des clusters déployés avec au minimum trois nœuds principaux et trois nœuds d’agent. Cela est le strict minimum pouvant être pris en charge dans les déploiements de production.

##  <a name="security-issues-and-patching"></a>Problèmes de sécurité et application de correctifs

Si un défaut de sécurité se trouve dans un ou plusieurs composants du moteur AKS ou du fournisseur Kubernetes pour Azure Stack Hub, Microsoft met à la disposition de ses clients un correctif qui permet de corriger les clusters affectés afin d’atténuer le problème. L’équipe peut également donner des conseils en matière de mise à niveau aux utilisateurs. Notez que les correctifs peuvent exiger un temps d’arrêt du cluster. Lorsque des redémarrages sont obligatoires, Microsoft en informe les clients. Si les utilisateurs n’appliquent pas les correctifs conformément aux instructions de Microsoft, leur cluster continuera d’être vulnérable au problème de sécurité.

## <a name="kubernetes-marketplace-item"></a>Élément Kubernetes de la Place de marché

Les utilisateurs peuvent télécharger un élément de la Place de marché Kubernetes, qui leur permet de déployer indirectement des clusters Kubernetes à l’aide du moteur AKS par le biais d’un modèle dans le portail utilisateur Azure Stack Hub. Cela rend ce processus plus simple que l’utilisation directe du moteur AKS. Cet élément de Place de marché est un outil utile pour configurer rapidement des clusters à des fins de démonstration, de test et de développement. Il n’est pas destiné à la production et n’est donc pas inclus dans l’ensemble d’éléments pris en charge par Microsoft.

## <a name="preview-features"></a>Fonctionnalités en préversion

Pour les fonctionnalités et fonctions qui exigent des tests étendus et des commentaires des utilisateurs, Microsoft publie de nouvelles fonctionnalités d’évaluation ou des fonctionnalités appartenant à un indicateur de fonctionnalité. Considérez ces fonctionnalités comme des fonctionnalités bêta ou en version préliminaire. Les fonctionnalités d’évaluation ou fonctionnalités avec indicateur de fonctionnalité ne sont pas destinées aux environnements de production. Les modifications continues apportées aux fonctionnalités et aux comportements, corrections de bogues et autres types de modifications peuvent entraîner des temps d’arrêt et un manque de stabilité des clusters. Ces fonctionnalités ne sont pas prises en charge par Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [le moteur AKS sur Azure Stack Hub](azure-stack-kubernetes-aks-engine-overview.md)