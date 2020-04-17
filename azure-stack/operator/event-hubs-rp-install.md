---
title: Guide pratique pour installer Event Hubs sur Azure Stack Hub
description: Découvrez comment installer le fournisseur de ressources Event Hubs sur Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: e07d311c8edbe140834a020af489ae49d8380d86
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "80424601"
---
# <a name="how-to-install-event-hubs-on-azure-stack-hub"></a>Guide pratique pour installer Event Hubs sur Azure Stack Hub

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

Cet article vous montre comment télécharger et installer le fournisseur de ressources Event Hubs afin de pouvoir l’offrir aux clients dans le cadre de l’abonnement.

## <a name="download-packages"></a>Télécharger des packages

Avant de pouvoir installer Event Hubs sur Azure Stack Hub, vous devez télécharger le fournisseur de ressources et ses packages dépendants. Vous avez deux options, en fonction de votre situation ou de vos besoins :

- Télécharger Event Hubs dans le cadre d’un scénario connecté.
- Télécharger Event Hubs dans le cadre d’un scénario déconnecté ou partiellement connecté.

Si vous ne connaissez pas la fonctionnalité **Gestion de la Place de marché** du portail administrateur Azure Stack Hub, passez en revue [Télécharger des éléments de la Place de marché Azure et les publier sur Azure Stack Hub](azure-stack-download-azure-marketplace-item.md). Cet article vous guide tout au long du processus de téléchargement des éléments à partir d’Azure vers la Place de marché Azure Stack Hub. Il couvre les deux scénarios, connecté et déconnecté. 

### <a name="download-event-hubs---connected-scenario"></a>Télécharger Event Hubs, scénario connecté

> [!NOTE]
> Le processus de téléchargement peut prendre entre 30 minutes et 2 heures, en fonction de la latence du réseau et des packages existants sur votre instance Azure Stack Hub. 

Suivez ces instructions si votre instance Azure Stack Hub dispose d’une connectivité Internet :

1. Connectez-vous au portail d’administration Azure Stack Hub.
2. Sélectionnez **Gestion de la Place de marché** sur la gauche.
3. Sélectionnez **Fournisseurs de ressources**.
4. Sélectionnez **+ Ajouter à partir d’Azure**.
5. Recherchez « Event Hubs » à l’aide de la barre de recherche.
6. Sélectionnez la ligne « Event Hubs » dans les résultats de la recherche. 
7. Dans la page de téléchargement « Event Hubs », sélectionnez la version Event Hubs que vous souhaitez installer, puis **Télécharger** en bas de la page. 
   [![Packages dans Gestion de la Place de marché](media/event-hubs-rp-install/1-marketplace-management-download.png)](media/event-hubs-rp-install/1-marketplace-management-download.png#lightbox)

Notez que d’autres packages logiciels sont téléchargés en même temps qu’Event Hubs, notamment :

- Microsoft Azure Stack Hub Add-On RP Windows Server INTERNE UNIQUEMENT
- PowerShell Desired State Configuration

Une fois le processus de téléchargement terminé, passez à la [section Prérequis pour l’installation](#install-prerequisites).

### <a name="download-event-hubs---disconnected-or-partially-connected-scenario"></a>Télécharger Event Hubs, scénario déconnecté ou partiellement connecté

Tout d’abord, vous téléchargez les packages sur votre ordinateur local, puis vous les importez dans votre instance Azure Stack Hub.

1. Si ce n’est pas déjà fait, suivez les instructions de [Télécharger des éléments de la Place de marché - Scénario déconnecté ou partiellement connecté](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario). Ici, vous téléchargez et exécutez l’outil de syndication de la Place de marché, qui vous permet de télécharger les packages Event Hubs.
2. Quand la fenêtre « Éléments de la Place de marché Azure » de l’outil de syndication s’ouvre, recherchez et sélectionnez « Event Hubs » pour télécharger les packages requis sur votre ordinateur local.
3. Une fois le téléchargement terminé, vous importez les packages sur votre instance Azure Stack Hub et les publiez sur la Place de marché. 

## <a name="installation"></a>Installation 

1. Si ce n’est pas déjà fait, connectez-vous au portail administrateur Azure Stack Hub.
2. Sélectionnez **Gestion de la Place de marché** sur la gauche, puis **Fournisseurs de ressources**.
3. Une fois qu’Event Hubs et d’autres logiciels requis ont été téléchargés, **Gestion de la Place de marché** doit afficher les packages « Event Hubs » avec l’état « Non installé ». Il peut y avoir d’autres packages qui affichent l’état « Téléchargé ». Sélectionnez la ligne « Event Hubs » que vous souhaitez installer.
   [![Packages téléchargés dans Gestion de la Place de marché](media/event-hubs-rp-install/2-marketplace-management-downloaded.png)](media/event-hubs-rp-install/2-marketplace-management-downloaded.png#lightbox)
 
4. La page d’installation des packages Event Hubs doit afficher une bannière bleue en haut. Sélectionnez la bannière pour démarrer l’installation d’Event Hubs.
   [![Event Hubs dans Gestion de la Place de marché - démarrer l’installation](media/event-hubs-rp-install/3-marketplace-management-install-ready.png)](media/event-hubs-rp-install/3-marketplace-management-install-ready.png#lightbox)

### <a name="install-prerequisites"></a>Prérequis à installer

1. Vous êtes ensuite dirigé vers la page d’installation. Sélectionnez **Prérequis pour l’installation** pour commencer le processus d’installation.
   ![Event Hubs dans Gestion de la Place de marché - prérequis](media/event-hubs-rp-install/4-marketplace-management-install-prereqs-start.png)
 
2. Attendez la fin de l’installation des prérequis. Vous devez voir une coche verte en regard de **Prérequis pour l’installation** avant de passer à l’étape suivante.

   ![Event Hubs dans Gestion de la Place de marché - prérequis](media/event-hubs-rp-install/5-marketplace-management-install-prereqs-succeeded.png)

### <a name="prepare-secrets"></a>Préparer les secrets 

1. Sous l’étape **2. Préparer les secrets**, sélectionnez **Ajouter un certificat** et le panneau **Ajouter un certificat** s’affiche.
   ![Event Hubs dans Gestion de la Place de marché - préparer les secrets](media/event-hubs-rp-install/6-marketplace-management-install-prepare-secrets.png)

2. Sélectionnez le bouton Parcourir sur **Ajouter un certificat**, juste à droite du champ de nom de fichier du certificat.
3. Sélectionnez le fichier de certificat .pfx que vous avez obtenu lors de la finalisation des prérequis. Pour plus d’informations, consultez [les prérequis pour l’installation](event-hubs-rp-prerequisites.md). 

4. Entrez le mot de passe fourni afin de créer une chaîne sécurisée pour le certificat SSL Event Hubs. Sélectionnez ensuite **Ajouter**.
   ![Event Hubs dans Gestion de la Place de marché - ajouter un certificat](media/event-hubs-rp-install/7-marketplace-management-install-prepare-secrets-add-cert.png)

### <a name="install-resource-provider"></a>Installer le fournisseur de ressources

1. Lorsque l’installation du certificat aboutit, vous devez voir une coche verte en regard de **Préparer les secrets** avant de passer à l’étape suivante. À présent, sélectionnez le bouton **Installer** en regard de **3 Installer le fournisseur de ressources**.
   ![Event Hubs dans Gestion de la Place de marché - démarrer l’installation](media/event-hubs-rp-install/8-marketplace-management-install-start.png)
 
2. La page suivante s’affiche, indiquant que le fournisseur de ressources Event Hubs est en cours d’installation.
   [![Event Hubs dans Gestion de la Place de marché - installation](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png)](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png#lightbox)
 
3. Attendez la notification de fin de l’installation. Ce processus prend généralement une ou plusieurs heures, en fonction de votre type Azure Stack Hub. 
   [![Event Hubs dans Gestion de la Place de marché - installation terminée](media/event-hubs-rp-install/10-marketplace-management-install-complete.png)](media/event-hubs-rp-install/10-marketplace-management-install-complete.png#lightbox)

4. Vérifiez que l’installation d’Event Hubs a réussi, en revenant à la page **Gestion de la Place de marché**, **Fournisseurs de ressources**. L’état d’Event Hubs doit indiquer « Installé ».
   ![Event Hubs dans Gestion de la Place de marché disponible](media/event-hubs-rp-install/11-marketplace-management-rps-installed.png)

## <a name="register-event-hubs"></a>Inscrire Event Hubs

Vous devez maintenant inscrire le fournisseur de ressources Event Hubs. L’inscription vous permet d’utiliser la page d’administration Event Hubs pour gérer le service.

1. Dans le portail administrateur, sélectionnez **Tous les services** en haut à gauche.
2. Sélectionnez **Abonnements**. Vous voyez une liste d’abonnements. 
   > [!NOTE]
   > Veillez à ne pas sélectionner **Abonnements utilisateur**
3. Sélectionnez **Abonnement Fournisseur par défaut** dans la page **Abonnements**.
4. Sélectionnez **Fournisseurs de ressources** à gauche de la page **Abonnement Fournisseur par défaut**.
5. Dans le champ **Filtrer par nom** en haut, recherchez la chaîne « EventHub ».
6. Examinez la colonne **État** des lignes du fournisseur de ressources « Microsoft.EventHub » et « Microsoft.EventHub.Admin ».
7. Si l’une d’elles présente l’état « Désinscrit », sélectionnez chaque fournisseur, puis **Inscrire**. 
   ![Fournisseurs de ressources désinscrits](media/event-hubs-rp-install/12-default-subscription-rps-unregistered.png)
8. Après quelques secondes, sélectionnez **Actualiser**. Vous devez maintenant voir le fournisseur de ressources avec l’état Inscrit. 
9. Vous devez maintenant voir Microsoft.EventHub et Microsoft.EventHub.Admin avec l’état « Inscrit ».
   ![Fournisseurs de ressources inscrits](media/event-hubs-rp-install/13-default-subscription-rps-registered.png)

10. Revenez à la page **Tous les services**.
11. Recherchez « Event Hubs ». Vous devez maintenant voir « Event Hubs », qui correspond à votre point d’entrée pour la page d’administration Event Hubs. 
   ![Services disponibles - Event Hubs](media/event-hubs-rp-install/14-all-service-event-hubs.png)
 
## <a name="next-steps"></a>Étapes suivantes

Avant que les utilisateurs ne puissent déployer des ressources Event Hubs, vous devez créer un ou plusieurs plans, offres et abonnements. 

- S’il s’agit de la première fois que vous offrez un service, commencez par le tutoriel [Offrir des services aux utilisateurs](tutorial-offer-services.md). Poursuivez avec le tutoriel suivant, [Tester une offre de service](tutorial-test-offer.md).
- Une fois que vous êtes familiarisé avec le concept d’offre de service, créez une offre et un plan qui incluent le fournisseur de ressources Event Hubs. Créez ensuite un abonnement pour vos utilisateurs ou donnez-leur les informations sur l’offre pour qu’ils puissent créer leur propre abonnement. Pour référence, vous pouvez également suivre la série d’articles sous [Vue d’ensemble des services, des plans, des offres et des abonnements](service-plan-offer-subscription-overview.md).

Pour rechercher des mises à jour, consultez [Guide pratique pour mettre à jour Event Hubs sur Azure Stack Hub](resource-provider-apply-updates.md).

Si vous devez supprimer le fournisseur de ressources, consultez [Supprimer le fournisseur de ressources Event Hubs](event-hubs-rp-remove.md)

Pour en savoir plus sur l’expérience utilisateur, consultez [Vue d’ensemble d’Event Hubs sur Azure Stack Hub](../user/event-hubs-overview.md) dans les documents utilisateur.