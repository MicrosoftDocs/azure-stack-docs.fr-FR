---
title: Démarrage rapide en vue de configurer Azure Kubernetes Service sur Azure Stack HCI à l’aide de Windows Admin Center
description: Découvrez comment configurer Azure Kubernetes Service sur Azure Stack HCI à l’aide de Windows Admin Center
author: davannaw-msft
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: dawhite
ms.openlocfilehash: 99f3e98ff24044c74c9065bb98965dcf640c4032
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612537"
---
# <a name="quickstart-set-up-azure-kubernetes-service-on-azure-stack-hci-using-windows-admin-center"></a>Démarrage rapide : Configurer Azure Kubernetes Service sur Azure Stack HCI à l’aide de Windows Admin Center

> S’applique à : Azure Stack HCI, Windows Server 2019 Datacenter

Dans ce démarrage rapide, vous configurez Azure Kubernetes Service sur Azure Stack HCI à l’aide de Windows Admin Center. Si vous préférez utiliser PowerShell, consultez [Configurer avec PowerShell](setup-powershell.md).

La configuration implique les tâches suivantes :

* Télécharger Azure Kubernetes Service sur Azure Stack HCI
* Configurer Windows Admin Center, si vous ne l’avez pas encore fait
* Installer l’extension Azure Kubernetes Service pour Windows Admin Center
* Configurer un hôte Azure Kubernetes Service sur le système où vous souhaitez déployer le cluster Kubernetes

Avant de commencer, assurez-vous que toutes les conditions préalables sont remplies dans la page [Configuration système requise](.\system-requirements.md).

## <a name="download-azure-kubernetes-service-on-azure-stack-hci"></a>Télécharger Azure Kubernetes Service sur Azure Stack HCI

Si vous n’avez pas encore téléchargé le logiciel en préversion, consultez [Inscrivez-vous pour évaluer AKS sur Azure Stack HCI](https://aka.ms/AKS-HCI-Evaluate). Vous serez invité à télécharger AKS sur Azure Stack HCI et Windows Admin Center.

## <a name="setting-up-windows-admin-center"></a>Configuration de Windows Admin Center

Si vous n’avez pas encore installé Windows Admin Center, consultez [Installer Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install). Pour la préversion publique d’Azure Kubernetes Service sur Azure Stack HCI, vous devez télécharger et exécuter Windows Admin Center sur un ordinateur Windows 10. La fonctionnalité Azure Kubernetes Service sur Azure Stack HCI est disponible uniquement avec les versions 2009 et ultérieures de Windows Admin Center.

## <a name="installing-the-azure-kubernetes-service-extension"></a>Installation de l’extension Azure Kubernetes Service

Après avoir obtenu les fichiers de la préversion publique d’Azure Kubernetes Service sur Azure Stack HCI, vous devez enregistrer le fichier `.nupkg` localement ou sur un partage SMB et ajouter le chemin d’accès du fichier à la liste « Flux » dans le gestionnaire d’extensions de Windows Admin Center. Le fichier `.nupkg` est un package NuGet qui contient l’extension Windows Admin Center.

Pour accéder à votre flux d’extensions existant, ouvrez Windows Admin Center et sélectionnez l’engrenage dans le coin supérieur droit de l’écran. Le menu Paramètres s’affiche. Les flux d’extension se trouvent dans la section **Passerelle** du menu **Extensions**. Accédez à l’onglet **Flux** et sélectionnez **Ajouter**. Dans ce volet, collez le chemin d’accès à votre copie de l’extension Azure Kubernetes Service, puis sélectionnez **Ajouter**. Si le chemin d’accès de votre fichier a été ajouté avec succès, vous recevrez une notification de réussite. 

Maintenant que nous avons ajouté le flux, l’extension Azure Kubernetes Service figure dans la liste des extensions disponibles. Une fois l’extension sélectionnée, sélectionnez **Installer** dans le haut du tableau pour installer cette extension. Windows Admin Center se recharge une fois l’installation terminée. 

[ ![Affichage de la liste d’extensions disponibles dans le gestionnaire d’extensions de Windows Admin Center.](.\media\setup\extension-manager.png) ](.\media\setup\extension-manager.png#lightbox)

## <a name="setting-up-an-azure-kubernetes-service-host"></a>Configuration d’un hôte Azure Kubernetes Service

Une dernière étape doit être effectuée avant la création effective d’un cluster Kubernetes. Vous devez configurer un hôte de service Azure Kubernetes sur le système où vous souhaitez déployer le cluster Kubernetes. Ce système peut être un cluster Windows Server 2019 Datacenter, un nœud Windows Server 2019 Datacenter ou un cluster Azure Stack HCI comprenant de deux à quatre nœuds. 

> [!NOTE] 
> Pour le moment, il n’est pas possible de configurer des hôtes Azure Kubernetes Service sur deux systèmes indépendants dans le but de les fusionner lors de la création d’un cluster Kubernetes. 

Cette configuration peut être effectuée à l’aide du nouvel outil Azure Kubernetes Service. 

Cet outil permet d’installer et de télécharger les packages nécessaires, ainsi que de créer un cluster de gestion qui fournit les principaux services Kubernetes et orchestre les charges de travail d’application. 


Nos paramètres système étant vérifiés, commençons : 
1. Sélectionnez **Configurer** pour lancer l’Assistant Configuration.
2. Passez en revue les conditions préalables requises pour la machine sur laquelle vous exécutez Windows Admin Center, le cluster auquel vous êtes connecté, ainsi que le réseau. Veillez également à être connecté à un compte Azure sur Windows Admin Center et vérifiez que l’abonnement Azure que vous prévoyez d’utiliser n’est pas arrivé à expiration. Quand vous avez terminé, cliquez sur **Suivant**.
3. Dans la page **Vérifications système** de l’assistant, effectuez toutes les actions requises, telles que la connexion de votre passerelle Windows Admin Center à Azure. Cette étape permet de vérifier que Windows Admin Center et le système qui hébergeront Azure Kubernetes Service possèdent les configurations appropriées pour continuer. Lorsque vous avez terminé, sélectionnez **Suivant**.
4. À l’étape **Configuration de l’hôte**, configurez l’ordinateur qui hébergera Azure Kubernetes Service. Nous vous recommandons de sélectionner **Télécharger automatiquement les mises à jour**. Cliquez sur **Suivant** quand vous avez terminé. Cette étape de l’Assistant vous demande de configurer les informations suivantes :
    * détails de l’hôte, tels que le nom du cluster de gestion et le dossier de stockage des images de machine virtuelle
    * mise en réseau des machines virtuelles, qui s’applique à toutes les machines virtuelles Linux et Windows (nœuds) créées pour exécuter des conteneurs et orchestrer la gestion des conteneurs. 
    * paramètres d’équilibrage de charge, qui définissent le pool d’adresses utilisé pour les services externes

    ![Représente l’étape de configuration de l’hôte de l’Assistant hôte Azure Kubernetes Service.](.\media\setup\host-configuration.png)

5. Passez en revue toutes vos sélections à l’étape **Examiner et créer**. Si vous êtes satisfait de vos sélections, sélectionnez **Suivant** pour commencer l’installation de l’hôte. 
6. Dans la page **Progression de l’installation**, vous pouvez suivre la progression de la configuration de votre hôte. À ce stade, vous êtes invité à ouvrir Windows Admin Center dans un nouvel onglet et à poursuivre vos tâches de gestion. 
7. Si le déploiement s’effectue correctement, le tableau de bord de gestion qui s’affiche ensuite vous permet de créer et gérer vos clusters Kubernetes. Ce tableau de bord, comme le reste des services Azure Kubernetes sur Azure Stack HCI, est en préversion et sera mis à jour avec des fonctionnalités supplémentaires dans les versions ultérieures. 
 
  ![Illustre le tableau de bord de gestion d’Azure Kubernetes sur Azure Stack HCI.](.\media\setup\dashboard.png)
 
## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez installé Windows Admin Center et l’extension Azure Kubernetes Service. Vous avez également configuré un hôte Azure Kubernetes Service sur le système où vous allez déployer un cluster Kubernetes.

Vous êtes maintenant prêt à poursuivre la [création d’un cluster Kubernetes dans Windows Admin Center](create-kubernetes-cluster.md).
