---
title: Démarrage rapide en vue de créer un cluster Kubernetes à l’aide de Windows Admin Center
description: Découvrez comment créer un cluster Kubernetes à l’aide de Windows Admin Center
author: davannaw-msft
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: ddaac062a147440b4ce290ad988629c417a5932a
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90949362"
---
# <a name="quickstart-create-a-kubernetes-cluster-on-azure-stack-hci-using-windows-admin-center"></a>Démarrage rapide : Créer un cluster Kubernetes sur Azure Stack HCI à l’aide de Windows Admin Center

> S’applique à : Azure Stack HCI

Une fois que vous avez configuré votre hôte Azure Kubernetes Service, vous pouvez créer un cluster Kubernetes à l’aide de Windows Admin Center. Si vous préférez utiliser PowerShell, consultez [Créer un cluster Kubernetes avec PowerShell](create-kubernetes-cluster-powershell.md).

C’est parti :

1. [Configurez Azure Kubernetes Service](setup.md) et vérifiez la [configuration système requise](system-requirements.md) si vous ne l’avez pas déjà fait.
1. Pour commencer à créer un cluster Kubernetes dans Windows Admin Center, appuyez sur le bouton **Ajouter** dans l’écran de la passerelle.
2. Dans le panneau **Ajouter ou créer des ressources** , sous **Cluster Kubernetes (préversion)** , sélectionnez **Créer** pour lancer l’assistant cluster Kubernetes. Bien que présent sous les clusters Kubernetes dans la préversion publique, le bouton « Ajouter » n’est pas fonctionnel. À tout moment pendant le processus de création du cluster Kubernetes, vous pouvez quitter l’assistant, mais notez que, dans ce cas, votre progression ne sera pas enregistrée. 

    ![Représente le panneau « Add or create resources » (Ajouter ou créer des ressources) dans Windows Admin Center, qui comprend désormais la nouvelle mosaïque pour les clusters Kubernetes.](.\media\create-kubernetes-cluster\add-connection.png)

3. Passez en revue les conditions requises pour le système qui hébergera le cluster Kubernetes et celles de Windows Admin Center. Quand vous avez terminé, sélectionnez **Suivant**. 
4. Sur la page **De base**, configurez les informations relatives à votre cluster, telles que l’intégration Azure Arc, les informations sur l’hôte Azure Kubernetes Service et la taille du pool de nœuds principal.  Informations sur l’hôte Azure Kubernetes Service et taille du pool de nœuds principal. Le champ hôte Azure Kubernetes Service nécessite le nom de domaine complet du cluster Azure Stack HCI sur lequel vous souhaitez déployer votre cluster Kubernetes. Vous devez avoir achevé la configuration de l’ordinateur hôte pour ce système via l’outil Azure Kubernetes Service. Dans la préversion publique, le nombre de nœuds est non modifiable et sa valeur par défaut est 2, mais la taille du nœud peut être configurée pour les charges de travail plus volumineuses. Quand vous avez terminé, sélectionnez **Suivant**.

    ![Représente la page des informations de base de l’Assistant cluster Kubernetes.](.\media\create-kubernetes-cluster\basics.png)

5. Vous pouvez configurer des pools de nœuds supplémentaires pour exécuter vos charges de travail sur la page **Pools de nœuds**. Pendant la préversion publique, vous pouvez ajouter jusqu’à un pool de nœuds Windows et un pool de nœuds Linux (en plus du pool de nœuds système). Quand vous avez terminé, cliquez sur **Suivant**.
6. Spécifiez votre configuration réseau sur la page **Mise en réseau**. Si vous sélectionnez « Avancé », vous pouvez personnaliser la plage d’adresses utilisée lors de la création d’un réseau virtuel pour les nœuds du cluster. Si vous sélectionnez « De base », les réseaux virtuels des nœuds du cluster sont créés avec une plage d’adresses par défaut. Les paramètres réseau (équilibrage de charge, stratégie réseau et routage d’application HTTP) ne peuvent pas être modifiés dans la préversion publique. Quand vous avez terminé, sélectionnez **Suivant**.

    ![Représente la page Mise en réseau de l’Assistant cluster Kubernetes.](.\media\create-kubernetes-cluster\networking.png)

7. Sur la page **Intégration**, connectez votre cluster à des services supplémentaires, tels que le tableau de bord Kubernetes, le stockage persistant et Azure Monitor. Le stockage persistant est le seul service requis pour configurer sur cette page. En préversion publique, l’emplacement de stockage persistant est par défaut l’emplacement de stockage sélectionné lors de la configuration de l’hôte. Ce champ n’est pas modifiable actuellement. Quand vous avez terminé, cliquez sur **Suivant**.
8. Passez en revue vos sélections dans la page **Vérifier + créer**. Lorsque vous êtes satisfait, sélectionnez **Créer** pour commencer le déploiement. La progression de votre déploiement s’affiche en haut de cette page. 
9. Une fois le déploiement terminé, la page **Étapes suivantes** décrit comment gérer votre cluster. Cette page contient également votre clé SSH et votre jeton de tableau de bord Kubernetes. Si vous avez choisi de désactiver le tableau de bord Kubernetes ou l’intégration d’Azure Arc à l’étape précédente, certaines informations et instructions de cette page peuvent ne pas être disponibles ou fonctionnelles.

> [!IMPORTANT] 
> Nous vous recommandons d’enregistrer votre clé SSH et votre jeton de tableau de bord Kubernetes dans un emplacement sécurisé et accessible.

Dans ce guide de démarrage rapide, vous configurez un hôte Azure Kubernetes Service et déployé un cluster Kubernetes. 

Pour en savoir plus sur Service Azure Kubernetes sur Azure Stack HCI et découvrir comment déployer et gérer des applications Linux dans Azure Kubernetes Service sur Azure Stack HCI, consultez ce didacticiel.
