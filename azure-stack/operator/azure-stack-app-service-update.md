---
title: Mettre à jour Azure App Service sur Azure Stack Hub
description: Apprenez à mettre à jour Azure App Service sur Azure Stack Hub.
author: BryanLa
ms.topic: article
ms.date: 05/05/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 5dc8599a62c4b9e10d603bf2f02f18bf5024e203
ms.sourcegitcommit: edb60c948b445537e9411d6261c6c78359b71d0b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2020
ms.locfileid: "91810658"
---
# <a name="update-azure-app-service-on-azure-stack-hub"></a>Mettre à jour Azure App Service sur Azure Stack Hub

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

::: zone pivot="state-connected"
Cet article explique comment mettre à niveau le [fournisseur de ressources Azure App Service](azure-stack-app-service-overview.md) lorsqu'il est déployé dans un environnement Azure Stack Hub connecté à Internet.

> [!IMPORTANT]
> Avant d'exécuter la mise à niveau, vous devez procéder au [déploiement d'Azure App Service sur Azure Stack Hub](azure-stack-app-service-deploy.md). 

## <a name="run-the-azure-app-service-resource-provider-installer"></a>Exécuter le programme d'installation du fournisseur de ressources Azure App Service

Au cours de ce processus, la mise à niveau va :

* Détecter les déploiements précédents d'Azure App Service
* Préparer l’intégralité des packages de mise à jour et des nouvelles versions de toutes les bibliothèques OSS à déployer
* Charger les données dans le stockage
* Mettre à niveau tous les rôles Azure App Service (contrôleurs, gestion, front-end, publication et Worker)
* Mettre à jour les définitions de groupe identique d'Azure App Service
* Mettre à jour le manifeste du fournisseur de ressources Azure App Service

> [!IMPORTANT]
> Le programme d'installation d'Azure App Service doit être exécuté sur une machine pouvant atteindre le point de terminaison Azure Stack Hub Administrator Azure Resource Manager.

Pour mettre à niveau votre déploiement d'Azure App Service sur Azure Stack Hub, procédez comme suit :

1. Téléchargez le [programme d'installation d'Azure App Service](https://aka.ms/appsvcupdateQ2installer).

2. Exécutez appservice.exe en tant qu’administrateur.

    ![Capture d’écran montrant comment démarrer le processus de déploiement ou de mise à jour dans le programme d’installation App Service.][1]

3. Cliquez sur **Déployer Azure App Service ou effectuer une mise à niveau vers la dernière version**.

4. Consultez et acceptez les termes du contrat de licence du logiciel Microsoft, puis cliquez sur **Suivant**.

5. Consultez et acceptez les termes du contrat de licence tiers, puis cliquez sur **Suivant**.

6. Vérifiez que le point de terminaison Azure Stack Hub Azure Resource Manager et les informations relatives au locataire Active Directory sont corrects. Si vous avez utilisé les paramètres par défaut durant le déploiement de l’ASDK, vous pouvez accepter les valeurs par défaut. Toutefois, si vous avez personnalisé les options lors du déploiement d'Azure Stack Hub, vous devez modifier les valeurs dans cette fenêtre. Par exemple, si vous utilisez le suffixe de domaine *mycloud.com*, vous devez modifier votre point de terminaison Azure Stack Hub Azure Resource Manager en le définissant sur *management.region.mycloud.com*. Après avoir vérifié vos informations, cliquez sur **Suivant**.

    ![Capture d’écran montrant où configurer les points de terminaison ARM dans le programme d’installation App Service.][2]

7. Sur la page suivante :

    1. Sélectionnez la méthode de connexion que vous souhaitez utiliser : **Informations d'identification** ou **Principal de service**
        - **Informations d'identification**
            - Si vous utilisez Azure Active Directory (Azure AD), entrez le compte et le mot de passe d'administrateur Azure AD que vous avez indiqués lors du déploiement d'Azure Stack Hub. Sélectionnez **Connecter**.
            - Si vous utilisez Active Directory Federation Services (AD FS), fournissez votre compte d’administrateur. Par exemple : cloudadmin@azurestack.local. Entrez votre mot de passe, puis sélectionnez **Se connecter**.
        - **Principal du service**
            - Le principal de service que vous utilisez **doit** disposer de droits de **propriétaire** sur l'**abonnement fournisseur par défaut**
            - Fournissez l'**ID du principal de service**, le **Fichier de certificat** et le **Mot de passe**, puis sélectionnez **Se connecter**.

    1. Dans **Abonnements Azure Stack Hub**, sélectionnez **Abonnement au fournisseur par défaut**.    Azure App Service sur Azure Stack Hub **doit** être déployé dans l'**abonnement au fournisseur par défaut**.

    1. Dans **Emplacements Azure Stack Hub**, sélectionnez l'emplacement qui correspond à la région où vous effectuez le déploiement. Par exemple, sélectionnez **local** si vous effectuez le déploiement sur ASDK.

    1. Si un déploiement Azure App Service est détecté, le groupe de ressources et le compte de stockage sont déjà renseignés et ne sont pas disponibles.

      ![Capture d’écran montrant où vous spécifiez les informations d’abonnement Azure Stack Hub dans le programme d’installation App Service.][3]

8. Sur la page de résumé :
   1. Vérifiez les choix effectués. Pour apporter des modifications, utilisez les boutons **Précédent** pour visiter les pages précédentes.
   2. Si les configurations sont correctes, cochez la case.
   3. Pour lancer la mise à niveau, cliquez sur **Suivant**.

       ![Capture d’écran montrant le résumé de la mise à jour App Service dans le programme d’installation.][4]

9. Page de progression de la mise à niveau :
    1. Suivez la progression de la mise à niveau. La durée de la mise à niveau d'Azure App Service sur Azure Stack Hub varie en fonction du nombre d'instances de rôle déployées.
    2. Une fois la mise à niveau terminée avec succès, cliquez sur **Quitter**.

        ![Capture d’écran montrant la progression du déploiement dans le programme d’installation App Service.][5]
::: zone-end

::: zone pivot="state-disconnected"
Cet article explique comment mettre à niveau le [fournisseur de ressources Azure App Service](azure-stack-app-service-overview.md) déployé dans un environnement Azure Stack Hub qui :

* n’est pas connecté à internet ;
* est sécurisé par les services de fédération Active Directory (AD FS).

> [!IMPORTANT]
> Avant d'exécuter la mise à niveau, vous devez procéder au [déploiement d'Azure App Service sur Azure Stack Hub dans un environnement déconnecté](./azure-stack-app-service-deploy.md?pivots=state-disconnected&view=azs-2002). 

## <a name="run-the-app-service-resource-provider-installer"></a>Exécuter le programme d’installation du fournisseur de ressources App Service

Pour mettre à niveau le fournisseur de ressources App Service dans un environnement Azure Stack Hub, vous devez effectuer les tâches suivantes :

1. Téléchargez le [programme d’installation d’Azure App Service](https://aka.ms/appsvcupdate8installer).
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

    ![Capture d’écran montrant comment démarrer une mise à jour dans un environnement de déconnexion.][11]

2. Cliquez sur **Avancé** > **Créer un package hors connexion**.

    ![Capture d’écran montrant comment créer un package hors connexion dans le programme d’installation App Service.][12]

3. Le programme d’installation Azure App Service crée un package de mise à niveau hors connexion et affiche le chemin pour y accéder.  Vous pouvez cliquer sur **Ouvrir le dossier** pour ouvrir le dossier dans l’Explorateur de fichiers.

4. Copiez le programme d'installation (AppService.exe) et le package d'installation hors connexion sur un ordinateur connecté à votre instance d'Azure Stack Hub.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack-hub"></a>Exécuter la mise à niveau d’App Service sur Azure Stack Hub

> [!IMPORTANT]
> Le programme d’installation d’Azure App Service doit être exécuté sur une machine pouvant atteindre le point de terminaison Azure Stack Hub Administrator Azure Resource Manager.

1. Exécutez appservice.exe en tant qu’administrateur.

    ![Capture d’écran montrant comment démarrer une mise à jour.][11]

2. Cliquez sur **Avancé** > **Effectuer l’installation ou la mise à niveau hors connexion**.

    ![Capture d’écran montrant comment effectuer une installation ou une mise à jour hors connexion dans le programme d’installation App Service.][12]

3. Accédez à l’emplacement du package de mise à niveau hors connexion que vous avez créé, puis cliquez sur **Suivant**.

4. Consultez et acceptez les termes du contrat de licence du logiciel Microsoft, puis cliquez sur **Suivant**.

5. Consultez et acceptez les termes du contrat de licence tiers, puis cliquez sur **Suivant**.

6. Assurez-vous que le point de terminaison Azure Stack Hub Azure Resource Manager et les informations sur le locataire Active Directory sont corrects. Si vous avez utilisé les paramètres par défaut au cours du déploiement du Kit de développement Azure Stack, vous pouvez accepter les valeurs par défaut ici. Toutefois, si vous avez personnalisé les options lors du déploiement d'Azure Stack Hub, vous devez modifier les valeurs dans cette fenêtre. Par exemple, si vous utilisez le suffixe de domaine *mycloud.com*, vous devez modifier votre point de terminaison Azure Stack Hub Azure Resource Manager en le définissant sur *management.region.mycloud.com*. Après avoir confirmé vos informations, cliquez sur **suivant**.

    ![Capture d’écran montrant où configurer les points de terminaison ARM dans le programme d’installation.][13]

7. Sur la page suivante :

   1. Sélectionnez la méthode de connexion que vous souhaitez utiliser : **Informations d'identification** ou **Principal de service**
        - **Informations d'identification**
            - Si vous utilisez Azure Active Directory (Azure AD), entrez le compte et le mot de passe d'administrateur Azure AD que vous avez indiqués lors du déploiement d'Azure Stack Hub. Sélectionnez **Connecter**.
            - Si vous utilisez Active Directory Federation Services (AD FS), fournissez votre compte d’administrateur. Par exemple : cloudadmin@azurestack.local. Entrez votre mot de passe, puis sélectionnez **Se connecter**.
        - **Principal du service**
            - Le principal de service que vous utilisez **doit** disposer de droits de **propriétaire** sur l'**abonnement fournisseur par défaut**
            - Fournissez l'**ID du principal de service**, le **Fichier de certificat** et le **Mot de passe**, puis sélectionnez **Se connecter**.

   1. Dans **Abonnements Azure Stack Hub**, sélectionnez **Abonnement au fournisseur par défaut**.  Azure App Service sur Azure Stack Hub **doit** être déployé dans l'**abonnement au fournisseur par défaut**.

   1. Dans **Emplacements Azure Stack Hub**, sélectionnez l'emplacement qui correspond à la région où vous effectuez le déploiement. Par exemple, sélectionnez **local** si vous effectuez le déploiement sur ASDK.
   
   1. Si un déploiement App Service existant est détecté, le groupe de ressources et le compte de stockage sont renseignés et grisés.

      ![Capture d’écran montrant où configurer les abonnements Azure Stack Hub dans le programme d’installation.][14]
8. Sur la page de résumé :
   1. Vérifiez les choix effectués. Pour apporter des modifications, utilisez les boutons **Précédent** pour visiter les pages précédentes.
   2. Si les configurations sont correctes, cochez la case.
   3. Pour lancer la mise à niveau, cliquez sur **Suivant**.

       ![Capture d’écran montrant le résumé des informations collectées dans le programme d’installation.][15]

9. Page de progression de la mise à niveau :
    1. Suivez la progression de la mise à niveau. La durée de la mise à niveau d’App Service sur Azure Stack Hub varie en fonction du nombre d’instances de rôle déployées.
    2. Une fois la mise à niveau terminée avec succès, cliquez sur **Quitter**.

        ![Capture d’écran montrant que la mise à jour s’est terminée avec succès.][16]
::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Anticipez les opérations d'administration supplémentaires pour Azure App Service sur Azure Stack Hub :

* [Planifier une capacité supplémentaire](azure-stack-app-service-capacity-planning.md)
* [Ajouter de la capacité supplémentaire](azure-stack-app-service-add-worker-roles.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

[11]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[12]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[13]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[14]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[15]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[16]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png
