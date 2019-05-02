---
title: Mettre à jour Azure App Service hors connexion | Microsoft Docs
description: Instructions détaillées pour la mise à jour d’Azure App Service dans Azure Stack hors connexion
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 02/27/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: anwestg
ms.openlocfilehash: 53b45a6ac5ef8aef1e8d07b242303ed0f248f506
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308831"
---
# <a name="offline-update-of-azure-app-service-on-azure-stack"></a>Mise à jour hors connexion d’Azure App Service sur Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

> [!IMPORTANT]
> Appliquez la mise à jour 1901 ou version ultérieure à votre système intégré Azure Stack ou déployez le dernier Kit de développement Azure Stack avant de déployer Azure App Service 1.5. 

En suivant les instructions de cet article, vous pouvez mettre à niveau le [fournisseur de ressources App Service](azure-stack-app-service-overview.md) déployé dans un environnement Azure Stack qui :

* n’est pas connecté à internet ;
* sécurisé par les services de fédération Active Directory (AD FS).

> [!IMPORTANT]
> Avant d’exécuter la mise à niveau, assurez-vous d’avoir déjà effectué le [déploiement d’Azure App Service sur le fournisseur de ressources Azure Stack](azure-stack-app-service-deploy-offline.md) et que vous avez lu les [notes de publication](azure-stack-app-service-release-notes-update-five.md) qui accompagnent la version 1.5, afin d’en savoir plus sur les nouvelles fonctionnalités, les correctifs et les problèmes connus qui pourraient affecter votre déploiement.

## <a name="run-the-app-service-resource-provider-installer"></a>Exécuter le programme d’installation du fournisseur de ressources App Service

Pour mettre à niveau le fournisseur de ressources App Service dans un environnement Azure Stack, vous devez effectuer les tâches suivantes :

1. Télécharger le [Programme d’installation App Service](https://aka.ms/appsvcupdate4installer)
2. Créer un package de mise à niveau en mode hors connexion
3. Exécuter le programme d’installation App Service (appservice.exe) et procéder à la mise à niveau

Au cours de ce processus, la mise à niveau va :

* Détecter le déploiement précédent d’App Service
* Effectuer un chargement sur le stockage
* Mettre à niveau tous les rôles App Service (contrôleurs, gestion, frontal, publication et Worker)
* Mettre à jour les définitions de groupe identique d’App Service
* Mettre à jour le manifeste du fournisseur de ressources App Service

## <a name="create-an-offline-upgrade-package"></a>Créer un package de mise à niveau en mode hors connexion

Pour mettre à niveau App Service dans un environnement déconnecté, vous devez d’abord créer un package de mise à niveau hors connexion sur une machine connectée à Internet.

1. Exécutez appservice.exe en tant qu’administrateur

    ![Programme d’installation App Service][1]

2. Cliquez sur **Avancé** > **Créer un package hors connexion**.

    ![Programme d’installation App Service, option Avancé][2]

3. Le programme d’installation App Service crée un package de mise à niveau hors connexion et affiche le chemin pour y accéder.  Vous pouvez cliquer sur **Ouvrir le dossier** pour ouvrir le dossier dans l’Explorateur de fichiers.

4. Copiez le programme d’installation AppService.exe et le package de mise à niveau hors connexion sur l’ordinateur hôte Azure Stack.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack"></a>Exécuter la mise à niveau d’App Service sur Azure Stack

> [!IMPORTANT]
> Le programme d’installation d’App Service doit être exécuté sur une machine pouvant atteindre le point de terminaison Azure Stack Administrator Azure Resource Manager.
>
>

1. Exécutez appservice.exe en tant qu’administrateur.

    ![Programme d’installation App Service][1]

2. Cliquez sur **Avancé** > **Effectuer l’installation ou la mise à niveau hors connexion**.

    ![Programme d’installation App Service, option Avancé][2]

3. Accédez à l’emplacement du package de mise à niveau hors connexion que vous avez créé, puis cliquez sur **Suivant**.

4. Consultez et acceptez les termes du contrat de licence du logiciel Microsoft, puis cliquez sur **Suivant**.

5. Consultez et acceptez les termes du contrat de licence tiers, puis cliquez sur **Suivant**.

6. Assurez-vous que le point de terminaison Azure Stack Azure Resource Manager et les informations sur le locataire Active Directory sont corrects. Si vous avez utilisé les paramètres par défaut au cours du déploiement du Kit de développement Azure Stack, vous pouvez accepter les valeurs par défaut ici. Toutefois, si vous avez personnalisé les options lors du déploiement d’Azure Stack, vous devez modifier les valeurs dans cette fenêtre. Par exemple, si vous utilisez le suffixe de domaine *mycloud.com*, vous devez modifier votre point de terminaison Azure Stack Azure Resource Manager sur *management.region.mycloud.com*. Après avoir confirmé vos informations, cliquez sur **suivant**.

    ![Informations sur Azure Stack Cloud][3]

7. Sur la page suivante :

   1. Cliquez sur le bouton **Se connecter** situé en regard de la zone **Abonnements Azure Stack**.
      * Si vous utilisez Azure Active Directory (Azure AD), entrez votre compte et mot de passe d’administrateur Azure AD que vous avez indiqués lors du déploiement d’Azure Stack. Cliquez sur **Se connecter**.
      * Si vous utilisez Active Directory Federation Services (AD FS), fournissez votre compte d’administrateur. Par exemple : _cloudadmin@azurestack.local_. Entrez votre mot de passe, puis cliquez sur **Se connecter**.
   2. Dans la zone **Abonnements Azure Stack**, sélectionnez **Abonnement au fournisseur par défaut**.
   3. Dans la zone **Emplacements Azure Stack**, sélectionnez l’emplacement qui correspond à la région où vous effectuez le déploiement. Par exemple, sélectionnez **local** si effectuez votre déploiement sur le Kit de développement Azure Stack.
   4. Si un déploiement App Service existant est détecté, le groupe de ressources et le compte de stockage sont renseignés et grisés.
   5. Cliquez sur **Suivant** pour passer en revue le résumé de la mise à niveau.

      ![Installation d’App Service détectée][4]

8. Sur la page de résumé :
   1. Vérifiez les choix effectués. Pour apporter des modifications, utilisez les boutons **Précédent** pour visiter les pages précédentes.
   2. Si les configurations sont correctes, cochez la case.
   3. Pour lancer la mise à niveau, cliquez sur **Suivant**.

       ![Résumé de la mise à niveau d’App Service][5]

9. Page de progression de la mise à niveau :
    1. Suivez la progression de la mise à niveau. La durée de la mise à niveau d’App Service sur Azure Stack varie en fonction des nombre d’instances de rôle déployées.
    2. Une fois la mise à niveau terminée avec succès, cliquez sur **Quitter**.

        ![Progression de la mise à niveau d’App Service][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également tester d’autres [services PaaS](azure-stack-offer-services-overview.md).

* [Fournisseur de ressources SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md)
