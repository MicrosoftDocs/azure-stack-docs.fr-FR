---
title: Démarrage rapide en vue de créer un cluster Kubernetes à l’aide de Windows Admin Center
description: Découvrez comment créer un cluster Kubernetes à l’aide de Windows Admin Center
author: davannaw-msft
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: dawhite
ms.openlocfilehash: 7a9c1bf5d89348175b7ac25a149fe01f384964a2
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612401"
---
# <a name="quickstart-create-a-kubernetes-cluster-on-azure-stack-hci-using-windows-admin-center"></a>Démarrage rapide : Créer un cluster Kubernetes sur Azure Stack HCI à l’aide de Windows Admin Center

> S’applique à : Azure Stack HCI, Windows Server 2019 Datacenter

Une fois que vous avez configuré votre hôte Azure Kubernetes Service, vous pouvez créer un cluster Kubernetes à l’aide de Windows Admin Center. Si vous préférez utiliser PowerShell, consultez [Créer un cluster Kubernetes avec PowerShell](create-kubernetes-cluster-powershell.md).

Avant de passer à l’Assistant Création d’un cluster Kubernetes, assurez-vous d’avoir [configuré Azure Kubernetes Service](setup.md) et vérifiez la [configuration système requise](system-requirements.md) si vous ne l’avez pas encore fait. L’Assistant Création d’un cluster Kubernetes est accessible via la page [Toutes les connexions](#creating-a-kubernetes-cluster-from-the-all-connections-page) ou le [tableau de bord de l’hôte Azure Kubernetes Service](#creating-a-kubernetes-cluster-from-the-azure-kubernetes-service-host-dashboard).

## <a name="creating-a-kubernetes-cluster-from-the-all-connections-page"></a>Création d’un cluster Kubernetes à partir de la page Toutes les connexions 

Il existe deux façons de créer un cluster Kubernetes dans Windows Admin Center. Vous pouvez le faire à partir de la page **Toutes les connexions** . Procédez comme suit, puis passez à la section [Assistant Création d’un cluster Kubernetes](#the-kubernetes-cluster-create-wizard) : 

1. Pour commencer à créer un cluster Kubernetes dans Windows Admin Center, appuyez sur le bouton **Ajouter** dans l’écran de la passerelle. 
2. Dans le panneau **Ajouter ou créer des ressources** , sous **Cluster Kubernetes (préversion)** , sélectionnez **Créer** pour lancer l’assistant cluster Kubernetes. Bien que présent sous les clusters Kubernetes dans la préversion publique, le bouton « Ajouter » n’est pas fonctionnel. À tout moment pendant le processus de création du cluster Kubernetes, vous pouvez quitter l’assistant, mais notez que, dans ce cas, votre progression ne sera pas enregistrée. 


    ![Représente le panneau « Add or create resources » (Ajouter ou créer des ressources) dans Windows Admin Center, qui comprend désormais la nouvelle mosaïque pour les clusters Kubernetes.](.\media\create-kubernetes-cluster\add-connection.png)
  
## <a name="creating-a-kubernetes-cluster-from-the-azure-kubernetes-service-host-dashboard"></a>Création d’un cluster Kubernetes à partir du tableau de bord de l’hôte Azure Kubernetes Service  

Vous pouvez également créer un cluster Kubernetes via le tableau de bord de l’hôte Azure Kubernetes Service. Ce tableau de bord se trouve dans l’outil Azure Kubernetes Service lorsque vous êtes connecté au système qui utilise un hôte Azure Kubernetes Service. Procédez comme suit, puis passez à la section [Assistant Création d’un cluster Kubernetes](#the-kubernetes-cluster-create-wizard) : 

1. Connectez-vous au système sur lequel vous souhaitez créer votre cluster Kubernetes et accédez à l’outil **Azure Kubernetes Service**. Un hôte Azure Kubernetes Service doit déjà être configuré sur ce système.
2. Sélectionnez le bouton **Ajouter un cluster** sous l’en-tête **Cluster Kubernetes**.

![Représente le tableau de bord de l’outil Azure Kubernetes Service qui s’affiche après la configuration d’un hôte Azure Kubernetes Service.](.\media\setup\dashboard.png)
  
## <a name="the-kubernetes-cluster-create-wizard"></a>Assistant Création d’un cluster Kubernetes
Vous avez accédé à l’Assistant Création d’un cluster Kubernetes via la page **Toutes les connexions** ou l’outil Azure Kubernetes Service. C’est parti :  

1. Passez en revue les conditions requises pour le système qui hébergera le cluster Kubernetes et celles de Windows Admin Center. Quand vous avez terminé, sélectionnez **Suivant**. 
2. Sur la page **De base**, configurez les informations relatives à votre cluster, telles que l’intégration Azure Arc, les informations sur l’hôte Azure Kubernetes Service et la taille du pool de nœuds principal. Le champ de l’hôte Azure Kubernetes Service nécessite le nom de domaine complet du cluster Azure Stack HCI ou Windows Server 2019 Datacenter que vous utilisiez lors de la consultation de la page de [configuration](setup.md). Vous devez avoir achevé la configuration de l’ordinateur hôte pour ce système via l’outil Azure Kubernetes Service. Dans la préversion publique, le nombre de nœuds n’est pas modifiable et sa valeur par défaut est 2, mais la taille du nœud peut être configurée pour les charges de travail plus volumineuses. Quand vous avez terminé, sélectionnez **Suivant**.

 [ ![Représente la page des informations de base de l’Assistant cluster Kubernetes.](.\media\create-kubernetes-cluster\basics.png) ](.\media\create-kubernetes-cluster\basics.png#lightbox)
 
3. Vous pouvez configurer des pools de nœuds supplémentaires pour exécuter vos charges de travail sur la page **Pools de nœuds**. Pendant la préversion publique, vous pouvez ajouter jusqu’à un pool de nœuds Windows et un pool de nœuds Linux (en plus du pool de nœuds système). Si vous souhaitez activer l’intégration d’Azure Arc plus tard dans cet Assistant, vous aurez besoin d’au moins un pool de nœuds Linux qui n’est pas le pool de nœuds principal. Quand vous avez terminé, cliquez sur **Suivant**.
4. Spécifiez votre configuration réseau sur la page **Mise en réseau**. Si vous sélectionnez « Avancé », vous pouvez personnaliser la plage d’adresses utilisée lors de la création d’un réseau virtuel pour les nœuds du cluster. Si vous sélectionnez « De base », les réseaux virtuels des nœuds du cluster sont créés avec une plage d’adresses par défaut. Les paramètres réseau (équilibrage de charge, stratégie réseau et routage d’application HTTP) ne peuvent pas être modifiés dans la préversion publique. Quand vous avez terminé, sélectionnez **Suivant**.

    [ ![Représente la page Mise en réseau de l’Assistant cluster Kubernetes.](.\media\create-kubernetes-cluster\networking.png) ](\media\create-kubernetes-cluster\networking.png#lightbox)

5. Sur la page **Intégration**, connectez votre cluster à des services supplémentaires, tels que le stockage persistant. Vous devez configurer un stockage persistant sur cette page. Dans la préversion publique, l’emplacement de stockage persistant est par défaut l’emplacement de stockage sélectionné lors de la configuration de l’hôte. Ce champ n’est pas modifiable actuellement. Quand vous avez terminé, cliquez sur **Suivant**.
6. Passez en revue vos sélections dans la page **Vérifier + créer**. Lorsque vous êtes satisfait, sélectionnez **Créer** pour commencer le déploiement. La progression de votre déploiement s’affiche en haut de cette page. 
7. Une fois le déploiement terminé, la page **Étapes suivantes** décrit comment gérer votre cluster. Cette page contient également votre clé SSH. Si vous avez choisi de désactiver l’intégration d’Azure Arc à l’étape précédente, certaines informations et instructions de cette page peuvent ne pas être disponibles ou fonctionnelles.

> [!IMPORTANT] 
> Nous vous recommandons d’enregistrer votre clé SSH dans un emplacement sécurisé et accessible.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé un cluster Kubernetes. Pour en savoir plus sur Azure Kubernetes Service sur Azure Stack HCI et découvrir comment déployer et gérer des applications Linux dans AKS sur Azure Stack HCI, consultez :

- [Tutoriel : Déployer des applications Linux dans Azure Kubernetes Service sur Azure Stack HCI](deploy-linux-application.md)
