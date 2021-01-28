---
title: Démarrage rapide pour créer un cluster Azure Stack HCI et l’inscrire auprès d’Azure
description: Découvrez comment déployer Azure Stack HCI, créer un cluster à l’aide de Windows Admin Center et l’inscrire auprès d’Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 01/22/2021
ms.openlocfilehash: 6527623ba5b42f40eb24cd6269f5d45bbccb0d25
ms.sourcegitcommit: e772df8ac78c86d834a68d1a8be83b7f738019b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98772240"
---
# <a name="quickstart-create-an-azure-stack-hci-cluster-and-register-it-with-azure"></a>Démarrage rapide : Créer un cluster Azure Stack HCI et l’inscrire auprès d’Azure

> S’applique à : Azure Stack HCI, version 20H2

Dans ce guide de démarrage rapide, vous allez apprendre à déployer un cluster Azure Stack HCI à deux serveurs et à site unique, et à l’inscrire auprès d’Azure. Pour les déploiements multisites, consultez [Vue d’ensemble des clusters étendus](../concepts/stretched-clusters.md).

## <a name="before-you-start"></a>Avant de commencer

Avant de créer un cluster, effectuez les opérations suivantes :

* Achetez deux serveurs à partir du [Catalogue Azure Stack HCI](https://hcicatalog.azurewebsites.net) par le biais du partenaire fournisseur de matériel Microsoft de votre choix avec le système d’exploitation Azure Stack HCI préinstallé. Passez en revue la [configuration système requise](../concepts/system-requirements.md) pour garantir que le matériel que vous sélectionnez prendra en charge les charges de travail que vous prévoyez d’exécuter sur le cluster. Pour simplifier la configuration, nous vous recommandons d’utiliser un système avec des cartes réseau à haut débit qui utilisent iWARP.
* Créez un compte d’utilisateur qui est membre du groupe Administrateurs local sur chaque serveur.
* [Obtenez un abonnement Azure](https://azure.microsoft.com/), si vous n’en avez pas déjà un.
* [Installez Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install) sur un PC de gestion, puis [inscrivez Windows Admin Center auprès d’Azure](../manage/register-windows-admin-center.md). Notez que votre ordinateur de gestion doit être joint au même domaine Active Directory que celui dans lequel vous allez créer le cluster, ou à un domaine entièrement fiable.
* Notez les noms de serveur, les noms de domaine, les adresses IP et l’ID du réseau local virtuel pour votre déploiement.

## <a name="create-the-cluster"></a>Créer le cluster

Effectuez les étapes suivantes pour créer un cluster simple à deux nœuds et à site unique. Pour plus d’informations ou pour savoir comment créer un cluster étendu, consultez [Créer un cluster Azure Stack HCI en utilisant Windows Admin Center](create-cluster.md).

1. Dans Windows Admin Center, sous **All connections (Toutes les connexions)** , cliquez sur **Add (Ajouter)** .
1. Dans le volet **Add resources (Ajouter des ressources)** , sous **Windows Server cluster (Cluster Windows Server)** , sélectionnez **Create new (Créer)** .
1. Sous **Choose cluster type (Choisir le type de cluster)** , sélectionnez **Azure Stack HCI**.
1. Sous **Select server locations** (Sélectionner les emplacements des serveurs), sélectionnez **All servers in one site** (Tous les serveurs d’un site).
1. Cliquez sur **Créer**. L’Assistant Création d’un cluster s’affiche à présent. Si la fenêtre contextuelle **Credential Security Service Provider (CredSSP)** (Fournisseur de services de sécurité des informations d’identification (CredSSP)) s’affiche, sélectionnez **Yes** (Oui) pour activer temporairement CredSSP. 

L’Assistant Création d’un cluster comporte cinq sections, chacune avec plusieurs étapes.

1. **Bien démarrer.** Dans cette section, vous allez vérifier les prérequis, ajouter des serveurs, joindre un domaine, installer les fonctionnalités et mises à jour nécessaires, puis redémarrer les serveurs. 
2. **Réseaux.** Cette section de l’Assistant vérifie que les cartes réseau appropriées sont activées et désactive celles que vous n’utilisez pas. Vous allez sélectionner des cartes de gestion, configurer une configuration de commutateur virtuel et définir votre réseau en fournissant des adresses IP.
3. **Clustering.** Cette section vérifie que vos serveurs ont une configuration cohérente et qu’ils sont adaptés au clustering, puis elle crée le cluster réel.
4. **Stockage**. Ensuite, vous allez nettoyer et vérifier les lecteurs, valider votre stockage et activer les espaces de stockage direct.
5. **SDN.** Vous pouvez ignorer la section 5, car nous n’allons pas utiliser SDN (Software Defined Networking) pour ce cluster.

Si vous avez activé le protocole CredSSP dans l’Assistant, vous devrez le désactiver sur chaque serveur pour des raisons de sécurité.

1. Dans Windows Admin Center, sous **All connections (Toutes les connexions)** , sélectionnez le cluster que vous venez de créer.
1. Sous **Outils**, sélectionnez **Serveurs**.
1. Dans le volet droit, sélectionnez le premier serveur du cluster.
1. Sous **Overview (Présentation)** , sélectionnez **Disable CredSSP (Désactiver CredSSP)** . La bannière rouge **CredSSP ENABLED** en haut disparaît.
1. Répétez les étapes 3 et 4 pour le deuxième serveur du cluster.

## <a name="set-up-a-cluster-witness"></a>Configurer un témoin de cluster

La configuration d’une ressource témoin est nécessaire afin que, si l’un des serveurs du cluster est mis hors connexion, l’autre nœud ne devienne alors pas également indisponible. Pour ce guide de démarrage rapide, nous allons utiliser un partage de fichiers SMB se trouvant sur un autre serveur comme témoin. Vous pouvez préférer utiliser un témoin cloud Azure, à condition que tous les nœuds de serveur du cluster disposent d’une connexion Internet fiable. Pour plus d’informations sur les options de témoin, consultez [Configurer un témoin de cluster](../manage/witness.md).

1. Dans Windows Admin Center, sélectionnez **Gestionnaire de cluster** à partir de la flèche déroulante du haut.
1. Sous **Connexions de cluster**, sélectionnez le cluster.
1. Sous **Outils**, sélectionnez **Paramètres**.
1. Dans le volet droit, sélectionnez **Témoin**.
1. Pour **Type de témoin**, sélectionnez **Témoin de partage de fichiers**.
1. Spécifiez un chemin de partage de fichiers, par exemple **\\servername.domain.com\Witness$** , puis fournissez des informations d’identification, si nécessaire.
1. Cliquez sur **Enregistrer**.

## <a name="register-with-azure"></a>Inscription auprès d’Azure

Azure Stack HCI exige une connexion à Azure, et vous aurez besoin d’autorisations Azure Active Directory pour effectuer l’inscription. Si vous ne les avez pas déjà, demandez à votre administrateur Azure AD de vous les accorder ou de vous les déléguer. Pour plus d’informations, consultez [Connecter Azure Stack HCI à Azure](register-with-azure.md). Une fois inscrit, le cluster se connecte automatiquement en arrière-plan.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un cluster Azure Stack HCI et vous l’avez inscrit auprès d’Azure. Vous êtes maintenant prêt à [créer des volumes](../manage/create-volumes.md), puis à [créer des machines virtuelles](../manage/vm.md).