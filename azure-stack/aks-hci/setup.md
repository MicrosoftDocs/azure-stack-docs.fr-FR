---
title: Démarrage rapide en vue de configurer Azure Kubernetes Service sur Azure Stack HCI à l’aide de Windows Admin Center
description: Découvrez comment configurer Azure Kubernetes Service sur Azure Stack HCI à l’aide de Windows Admin Center
author: davannaw-msft
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: 2ac65146c78c2ed1aaf9b98ee50392a13be050c4
ms.sourcegitcommit: be445f183d003106192f039990d1fb8ee151c8d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253993"
---
# <a name="quickstart-set-up-azure-kubernetes-service-on-azure-stack-hci-using-windows-admin-center"></a>Démarrage rapide : Configurer Azure Kubernetes Service sur Azure Stack HCI à l’aide de Windows Admin Center

> S’applique à : Azure Stack HCI

Dans ce démarrage rapide, vous configurez Azure Kubernetes Service sur Azure Stack HCI à l’aide de Windows Admin Center. Si vous préférez utiliser PowerShell, consultez [Configurer avec PowerShell](setup-powershell.md).

La configuration implique les tâches suivantes :

* Télécharger Azure Kubernetes Service sur Azure Stack HCI
* Configurer Windows Admin Center, si vous ne l’avez pas encore fait
* Installer l’extension Azure Kubernetes Service pour Azure Stack HCI pour Windows Admin Center
* Configurer un hôte Azure Kubernetes Service sur le système où vous souhaitez déployer le cluster Kubernetes

Avant de commencer, assurez-vous que toutes les conditions préalables sont remplies dans la page [Configuration système requise](.\system-requirements.md).

## <a name="download-azure-kubernetes-service-on-azure-stack-hci"></a>Télécharger Azure Kubernetes Service sur Azure Stack HCI

Si vous n’avez pas encore téléchargé le logiciel en préversion, consultez [Inscrivez-vous pour évaluer AKS sur Azure Stack HCI](https://aka.ms/AKS-HCI-Evaluate). Vous serez invité à télécharger AKS sur Azure Stack HCI et Windows Admin Center.

## <a name="setting-up-windows-admin-center"></a>Configuration de Windows Admin Center

Si vous n’avez pas encore installé Windows Admin Center, consultez [Installer Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install). Pour la préversion publique d’Azure Kubernetes Service sur Azure Stack HCI, vous devez télécharger et exécuter Windows Admin Center sur un ordinateur Windows 10. Pour le moment, seul le mode Bureau de Windows Admin Center est compatible avec Azure Kubernetes Service sur Azure Stack HCI. La fonctionnalité Azure Kubernetes Service sur Azure Stack HCI est disponible uniquement avec les versions 2009 et ultérieures de Windows Admin Center.

## <a name="installing-the-azure-kubernetes-service-extension"></a>Installation de l’extension Azure Kubernetes Service

Après avoir obtenu les fichiers de la préversion publique d’Azure Kubernetes Service sur Azure Stack HCI, vous devez enregistrer le fichier `.nupkg` localement ou sur un partage SMB et ajouter le chemin d’accès du fichier à la liste « Flux » dans le gestionnaire d’extensions de Windows Admin Center. Le fichier `.nupkg` est un package NuGet qui contient l’extension Windows Admin Center.

Pour accéder à votre flux d’extensions existant, ouvrez Windows Admin Center et sélectionnez l’engrenage dans le coin supérieur droit de l’écran. Le menu Paramètres s’affiche. Les flux d’extension se trouvent dans la section **Passerelle** du menu **Extensions** . Accédez à l’onglet **Flux** et sélectionnez **Ajouter** . Dans ce volet, collez le chemin d’accès à votre copie de l’extension Azure Kubernetes Service sur Azure Stack HCI, puis sélectionnez **Ajouter** . Si le chemin d’accès de votre fichier a été ajouté avec succès, vous recevrez une notification de réussite. 

Maintenant que nous avons ajouté le flux, l’extension Azure Kubernetes Service sur Azure Stack HCI figure dans la liste des extensions disponibles. Une fois l’extension sélectionnée, sélectionnez **Installer** dans le haut du tableau pour installer cette extension. Windows Admin Center se recharge une fois l’installation terminée. 

[ ![Affichage de la liste d’extensions disponibles dans le gestionnaire d’extensions de Windows Admin Center.](.\media\setup\extension-manager.png) ](.\media\setup\extension-manager.png#lightbox)

## <a name="setting-up-an-azure-kubernetes-service-host"></a>Configuration d’un hôte Azure Kubernetes Service

Une dernière étape doit être effectuée avant la création effective d’un cluster Kubernetes. Vous devez configurer un hôte de service Azure Kubernetes sur le système où vous souhaitez déployer le cluster Kubernetes. Ce système doit être un cluster Azure Stack HCI. 

> [!NOTE] 
> Pour le moment, il n’est pas possible de configurer des hôtes Azure Kubernetes Service sur deux systèmes indépendants dans le but de les fusionner lors de la création d’un cluster Kubernetes. 

Cette configuration peut être effectuée à l’aide du nouvel outil Azure Kubernetes Service. 

Cet outil permet d’installer et de télécharger les packages nécessaires, ainsi que de créer un cluster de gestion qui fournit les principaux services Kubernetes et orchestre les charges de travail d’application. 

Avant d’utiliser cet outil, ouvrez PowerShell et exécutez la commande suivante sur chaque nœud pour vous assurer que la connexion Azure n’est pas bloquée par les paramètres de la machine :
```PowerShell
az login
```

Nos paramètres système étant vérifiés, commençons : 
1. Sélectionnez **Configurer** pour lancer l’Assistant Configuration.
2. Passez en revue les conditions préalables requises pour la machine sur laquelle vous exécutez Windows Admin Center, le cluster Azure Stack HCI auquel vous êtes connecté, ainsi que le réseau. Veillez également à être connecté à un compte Azure sur Windows Admin Center et vérifiez que l’abonnement Azure que vous prévoyez d’utiliser n’est pas arrivé à expiration. Quand vous avez terminé, cliquez sur **Suivant** .
3. Dans la page **Vérifications système** de l’assistant, effectuez toutes les actions requises, telles que la connexion de votre passerelle Windows Admin Center à Azure. Cette étape permet de vérifier que Windows Admin Center et le système qui hébergeront Azure Kubernetes Service possèdent les configurations appropriées pour continuer. Lorsque vous avez terminé, sélectionnez **Suivant** .
4. À l’étape **Configuration de l’hôte** , configurez l’ordinateur qui hébergera Azure Kubernetes Service. Nous vous recommandons de sélectionner **Télécharger automatiquement les mises à jour** . Cliquez sur **Suivant** quand vous avez terminé. Cette étape de l’Assistant vous demande de configurer les informations suivantes :
    * détails de l’hôte, tels que le nom du cluster de gestion et le dossier de stockage des images de machine virtuelle
    * mise en réseau des machines virtuelles, qui s’applique à toutes les machines virtuelles Linux et Windows (nœuds) créées pour exécuter des conteneurs et orchestrer la gestion des conteneurs. 
    * paramètres d’équilibrage de charge, qui définissent le pool d’adresses utilisé pour les services externes

    ![Représente l’étape de configuration de l’hôte de l’Assistant hôte Azure Kubernetes Service.](.\media\setup\host-configuration.png)

5. Inscrivez-vous auprès d’Azure et choisissez d’envoyer des données de diagnostic à Microsoft dans le cadre de l’ **inscription Azure** . Bien que cette page demande un abonnement et un groupe de ressources Azure, la configuration et l’utilisation d’Azure Kubernetes Service sont gratuites en préversion publique. Les données de diagnostic que vous envoyez à Microsoft permettront d’assurer la sécurité et la mise à jour du service, de résoudre les problèmes et d’améliorer le produit. Une fois vos sélections effectuées, sélectionnez **Suivant** .
6. Passez en revue toutes vos sélections effectuées jusqu’à présent à l’étape **Examiner et créer** . Si vous êtes satisfait de vos sélections, sélectionnez **Configurer** pour commencer l’installation de l’hôte. 
7. Dans la page **Progression de l’installation** , vous pouvez suivre la progression de la configuration de votre hôte. À ce stade, vous êtes invité à ouvrir Windows Admin Center dans un nouvel onglet et à poursuivre vos tâches de gestion. 
8. Si le déploiement s’effectue correctement, les étapes suivantes s’affichent et le bouton **Terminer** est activé. La sélection **Download management cluster kubeconfig** (Télécharger management cluster kubeconfig) dans **Étapes suivantes** déclenche un téléchargement sans refermer l’assistant. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez installé Windows Admin Center et l’extension Azure Kubernetes Service pour Azure Stack HCI. Vous avez également configuré un hôte Azure Kubernetes Service sur le système où vous allez déployer un cluster Kubernetes.

Vous êtes maintenant prêt à poursuivre la [création d’un cluster Kubernetes dans Windows Admin Center](create-kubernetes-cluster.md).