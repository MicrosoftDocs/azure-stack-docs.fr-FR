---
title: Mettre à jour Azure App Service hors connexion | Microsoft Docs
description: Instructions détaillées pour la mise à jour d’Azure App Service dans Azure Stack Hub hors connexion
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.openlocfilehash: e36e9d7504f956475c1cf1e66daac0d2d4c4bcb3
ms.sourcegitcommit: ce01b2cd114ca8ab5b70c6311b66c58ceb054469
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75924284"
---
# <a name="offline-update-of-azure-app-service-on-azure-stack-hub"></a>Mise à jour hors connexion d’Azure App Service sur Azure Stack Hub

*S’applique à : Systèmes intégrés Azure Stack Hub et kit SDK de développement Azure Stack Hub*

> [!IMPORTANT]
> Appliquez la mise à jour 1910 ou une version ultérieure à votre système intégré Azure Stack Hub ou déployez le dernier kit de développement Azure Stack Hub avant de déployer Azure App Service 1.8.

En suivant les instructions de cet article, vous pouvez mettre à niveau le [fournisseur de ressources Azure App Service](azure-stack-app-service-overview.md) déployé dans un environnement Azure Stack Hub qui :

* n’est pas connecté à internet ;
* sécurisé par les services de fédération Active Directory (AD FS).

> [!IMPORTANT]
> Avant d’exécuter la mise à niveau, vérifiez que vous avez déjà effectué le [déploiement du fournisseur de ressources Azure App Service sur Azure Stack Hub](azure-stack-app-service-deploy-offline.md) et que vous avez lu les [notes de publication](azure-stack-app-service-release-notes-update-eight.md) qui accompagnent la version 1.8, afin d’en savoir plus sur les nouvelles fonctionnalités, les correctifs et les problèmes connus qui pourraient impacter votre déploiement.

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

    ![Programme d’installation Azure App Service][1]

2. Cliquez sur **Avancé** > **Créer un package hors connexion**.

    ![Programme d’installation Azure App Service, option Avancé][2]

3. Le programme d’installation Azure App Service crée un package de mise à niveau hors connexion et affiche le chemin pour y accéder.  Vous pouvez cliquer sur **Ouvrir le dossier** pour ouvrir le dossier dans l’Explorateur de fichiers.

4. Copiez le programme d’installation AppService.exe et le package de mise à niveau hors connexion sur l’ordinateur hôte Azure Stack Hub.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack-hub"></a>Exécuter la mise à niveau d’App Service sur Azure Stack Hub

> [!IMPORTANT]
> Le programme d’installation d’Azure App Service doit être exécuté sur une machine pouvant atteindre le point de terminaison Azure Stack Hub Administrator Azure Resource Manager.

1. Exécutez appservice.exe en tant qu’administrateur.

    ![Programme d’installation Azure App Service][1]

2. Cliquez sur **Avancé** > **Effectuer l’installation ou la mise à niveau hors connexion**.

    ![Programme d’installation Azure App Service, option Avancé][2]

3. Accédez à l’emplacement du package de mise à niveau hors connexion que vous avez créé, puis cliquez sur **Suivant**.

4. Consultez et acceptez les termes du contrat de licence du logiciel Microsoft, puis cliquez sur **Suivant**.

5. Consultez et acceptez les termes du contrat de licence tiers, puis cliquez sur **Suivant**.

6. Assurez-vous que le point de terminaison Azure Stack Hub Azure Resource Manager et les informations sur le locataire Active Directory sont corrects. Si vous avez utilisé les paramètres par défaut au cours du déploiement du Kit de développement Azure Stack Hub, vous pouvez accepter les valeurs par défaut ici. Toutefois, si vous avez personnalisé les options lors du déploiement d’Azure Stack Hub, vous devez modifier les valeurs dans cette fenêtre. Par exemple, si vous utilisez le suffixe de domaine *mycloud.com*, vous devez modifier votre point de terminaison Azure Stack Hub Azure Resource Manager en le définissant sur *management.region.mycloud.com*. Après avoir confirmé vos informations, cliquez sur **suivant**.

    ![Informations sur Azure Stack Hub Cloud][3]

7. Sur la page suivante :

   1. Sélectionnez la méthode de connexion que vous souhaitez utiliser : **Informations d’identification** ou **Principal du service**
        - **Informations d'identification**
            - Si vous utilisez Azure Active Directory (Azure AD), entrez votre compte et mot de passe d’administrateur Azure AD que vous avez indiqués lors du déploiement d’Azure Stack Hub. Sélectionnez **Connecter**.
            - Si vous utilisez Active Directory Federation Services (AD FS), fournissez votre compte d’administrateur. Par exemple : cloudadmin@azurestack.local. Entrez votre mot de passe, puis sélectionnez **Se connecter**.
        - **Principal du service**
            - Le principal du service que vous utilisez **doit** avoir des droits de **propriétaire** sur l’**abonnement fournisseur par défaut**
            - Indiquez l’**ID du principal du service**, le **fichier de certificat** et le **mot de passe**, puis sélectionnez **Se connecter**.

   1. Dans **Abonnements Azure Stack Hub**, sélectionnez **Abonnement au fournisseur par défaut**.  Azure App Service sur Azure Stack Hub **doit** être déployé dans l’**abonnement au fournisseur par défaut**.

   1. Dans **Emplacements Azure Stack Hub**, sélectionnez l’emplacement qui correspond à la région où vous effectuez le déploiement. Par exemple, sélectionnez **local** si vous effectuez le déploiement sur ASDK.
   
   1. Si un déploiement App Service existant est détecté, le groupe de ressources et le compte de stockage sont renseignés et grisés.

      ![Installation d’Azure App Service détectée][4]
8. Sur la page de résumé :
   1. Vérifiez les choix effectués. Pour apporter des modifications, utilisez les boutons **Précédent** pour visiter les pages précédentes.
   2. Si les configurations sont correctes, cochez la case.
   3. Pour lancer la mise à niveau, cliquez sur **Suivant**.

       ![Résumé de la mise à niveau d’Azure App Service][5]

9. Page de progression de la mise à niveau :
    1. Suivez la progression de la mise à niveau. La durée de la mise à niveau d’App Service sur Azure Stack Hub varie en fonction du nombre d’instances de rôle déployées.
    2. Une fois la mise à niveau terminée avec succès, cliquez sur **Quitter**.

        ![Progression de la mise à niveau d’Azure App Service][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>Étapes suivantes

Anticipez les opérations d’administration supplémentaires pour Azure App Service sur Azure Stack Hub :

* [Planifier une capacité supplémentaire](azure-stack-app-service-capacity-planning.md)
* [Ajouter de la capacité supplémentaire](azure-stack-app-service-add-worker-roles.md)
