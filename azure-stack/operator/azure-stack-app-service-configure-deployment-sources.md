---
title: Configurer des sources de déploiement pour App Services sur Azure Stack Hub
description: Apprenez à configurer des sources de déploiement (Git, GitHub, BitBucket, Dropbox et OneDrive) pour App Services sur Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 03/11/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: f2498804505aec7b4e4131032758c09a6ff8168d
ms.sourcegitcommit: b2337a9309c52aac9f5a1ffd89f1426d6c178ad5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87250875"
---
# <a name="configure-deployment-sources-for-app-services-on-azure-stack-hub"></a>Configurer des sources de déploiement pour App Services sur Azure Stack Hub

App Service sur Azure Stack Hub prend en charge le déploiement à la demande à partir de différents fournisseurs de contrôle de code source. Cette fonctionnalité permet aux développeurs d’applications d’effectuer un déploiement directement à partir de leurs dépôts de contrôle de code source. Si les utilisateurs souhaitent configurer App Service pour qu'il se connecte à leurs référentiels, un opérateur de cloud doit d'abord configurer l'intégration entre App Service sur Azure Stack Hub et le fournisseur de contrôle de code source.  

En plus du Git local, les fournisseurs de contrôle de code source suivants sont pris en charge :

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>Afficher les sources de déploiement dans l’administration App Service

1. Connectez-vous au portail administrateur Azure Stack Hub en tant qu’administrateur du service.
2. Accédez à **Tous les Services** et sélectionnez **App Service**.

    ![Administrateur du fournisseur de ressources App Service][1]

3. Sélectionnez **Source control configuration** (Configuration du contrôle de code source). Vous pouvez voir la liste de toutes les sources de déploiement configurées.

    ![Configuration du contrôle de code source de l’administrateur du fournisseur de ressources App Service][2]

## <a name="configure-github"></a>Configurer GitHub

Vous avez besoin d’un compte GitHub pour effectuer cette tâche. Vous voudrez peut-être utiliser un compte d’entreprise, plutôt qu’un compte personnel.

1. Connectez-vous à GitHub, accédez à <https://www.github.com/settings/developers>, puis sélectionnez **Register a new application** (Inscrire une nouvelle application).

    ![GitHub - Inscrire une nouvelle application][3]

2. Entrez un **nom d’application**. Par exemple, **App Service sur Azure Stack Hub**.
3. Entrez **l’URL de la page d’accueil**. L'URL de la page d'accueil doit être l'adresse du portail Azure Stack Hub. Par exemple : `https://portal.<region>.<FQDN>`. Pour plus d’informations sur le nom de domaine complet (FQDN) Azure Stack Hub, consultez [Espace de noms DNS Azure Stack Hub](azure-stack-integrate-dns.md#azure-stack-hub-dns-namespace).
4. Entrez une **description de l’application**.
5. Entrez **l’URL de rappel d’autorisation**. Dans un déploiement par défaut d'Azure Stack Hub, l'URL apparaît sous la forme `https://portal.<region>.<FQDN>/TokenAuthorize`. 
6. Sélectionnez **Register application** (Inscrire l’application). Une page s’affiche, qui indique l’**ID client** et le **secret client** de l’application.

    ![GitHub - Inscription de l’application terminée][5]

7. Dans un nouvel onglet ou une nouvelle fenêtre de navigateur, connectez-vous au portail d’administration Azure Stack Hub en tant qu’administrateur du service.
8. Accédez à **Fournisseurs de ressources** et sélectionnez **Administrateur du fournisseur de ressources App Service**.
9. Sélectionnez **Source control configuration** (Configuration du contrôle de code source).
10. Copiez et collez **l’ID client** et la **clé secrète client** dans les zones de saisie correspondantes pour GitHub.
11. Sélectionnez **Enregistrer**.

## <a name="configure-bitbucket"></a>Configurer BitBucket

Vous avez besoin d’un compte BitBucket pour effectuer cette tâche. Vous voudrez peut-être utiliser un compte d’entreprise, plutôt qu’un compte personnel.

1. Connectez-vous à BitBucket et accédez à **Intégrations** dans votre compte.

    ![Tableau de bord BitBucket - Intégrations][7]

2. Sélectionnez **OAuth** sous Gestion de l’accès et sur **Add consumer** (Ajouter un consommateur).

    ![BitBucket - Ajouter un consommateur OAuth][8]

3. Entrez un **nom** pour le consommateur. Par exemple, **App Service sur Azure Stack Hub**.
4. Entrez une **description** pour l’application.
5. Entrez **l’URL de rappel**. Dans un déploiement par défaut d'Azure Stack Hub, l'URL de rappel apparaît sous la forme `https://portal.<region>.<FQDN>/TokenAuthorize`. L’URL doit respecter la mise en majuscules indiquée ici pour une intégration BitBucket réussie.
6. Entrez **l’URL**. Cette URL doit être celle du portail Azure Stack Hub. Par exemple : `https://portal.<region>.<FQDN>`.
7. Sélectionnez les **autorisations** requises :

    - **Référentiels** : *Lire*
    - **Webhooks** : *Lecture et écriture*

8. Sélectionnez **Enregistrer**. Cette nouvelle application s’affiche maintenant avec la **clé** et le **secret** sous **OAuth consumers** (Consommateurs OAuth).

    ![Liste des applications BitBucket][9]

9. Dans un nouvel onglet ou une nouvelle fenêtre de navigateur, connectez-vous au portail d’administration Azure Stack Hub en tant qu’administrateur du service.
10. Accédez à **Fournisseurs de ressources** et sélectionnez **Administrateur du fournisseur de ressources App Service**.
11. Sélectionnez **Source control configuration** (Configuration du contrôle de code source).
12. Copiez et collez la **clé** dans la zone de saisie **ID client** et la **clé secrète** dans la zone de saisie **Clé secrète client** pour BitBucket.
13. Sélectionnez **Enregistrer**.

## <a name="configure-onedrive"></a>Configurer OneDrive

Vous devez disposer d’un compte Microsoft associé à un compte OneDrive pour effectuer cette tâche. Vous voudrez peut-être utiliser un compte d’entreprise, plutôt qu’un compte personnel.

> [!NOTE]
> Les comptes OneDrive Entreprise ne sont pas pris en charge actuellement.

1. Accédez à <https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm> et connectez-vous en utilisant votre compte Microsoft.
2. Sous **Mes applications**, sélectionnez **Ajouter une application**.

    ![Applications OneDrive][10]

3. Entrez un **nom** pour l’inscription de la nouvelle application, entrez **App Service sur Azure Stack Hub**, puis sélectionnez **Créer une application**.
4. L’écran suivant liste les propriétés de votre nouvelle application. Enregistrez **l’ID d’application** dans un emplacement temporaire.

    ![Propriétés de l’application OneDrive][11]

5. Sous **Secrets de l’application**, sélectionnez **Générer un nouveau mot de passe**. Notez le **nouveau mot de passe créé**. Ce mot de passe correspond à votre secret d’application, et vous ne pouvez plus le récupérer après avoir sélectionné **OK**.
6. Sous **Plateformes**, sélectionnez **Ajouter une plateforme**, puis sélectionnez **Web**.
7. Entrez **l’URI de redirection**. Dans un déploiement par défaut d’Azure Stack Hub, l’URI de redirection apparaît sous la forme `https://portal.<region>.<FQDN>/TokenAuthorize`.

    ![Application OneDrive - Ajouter la plateforme web][12]

8. Ajoutez les autorisations appropriées sous **Autorisations pour Microsoft Graph** - **Autorisations déléguées**.

    - **Files.ReadWrite.AppFolder**
    - **User. Read** ![Application OneDrive - Autorisations pour Microsoft Graph][13]

9. Sélectionnez **Enregistrer**.
10. Dans un nouvel onglet ou une nouvelle fenêtre de navigateur, connectez-vous au portail d’administration Azure Stack Hub en tant qu’administrateur du service.
11. Accédez à **Fournisseurs de ressources** et sélectionnez **Administrateur du fournisseur de ressources App Service**.
12. Sélectionnez **Source control configuration** (Configuration du contrôle de code source).
13. Copiez et collez **l’ID d’application** dans la zone de saisie **ID client** et le **mot de passe** dans la zone de saisie **Clé secrète client** pour OneDrive.
14. Sélectionnez **Enregistrer**.

## <a name="configure-dropbox"></a>Configurer Dropbox

> [!NOTE]
> Vous devez avoir un compte Dropbox pour effectuer cette tâche. Vous voudrez peut-être utiliser un compte d’entreprise, plutôt qu’un compte personnel.

1. Accédez à <https://www.dropbox.com/developers/apps> et connectez-vous en utilisant les informations d’identification de votre compte DropBox.
2. Sélectionnez **Créer une application**.

    ![Applications Dropbox][14]

3. Sélectionnez **Dropbox API** (API Dropbox).
4. Définissez le niveau d’accès sur **App Folder** (Dossier d’application).
5. Entrez le **nom** de votre application.

    ![Inscription d’une application Dropbox][15]

6. Sélectionnez **Créer une application**. Une page listant les paramètres de l’application, notamment **Clé d’application** et **Secret d’application**, s’affiche.
7. Vérifiez que le **nom du dossier de l'application** est défini sur **App Service sur Azure Stack Hub**.
8. Définissez **l’URI de redirection OAuth 2** et sélectionnez **Ajouter**. Dans un déploiement par défaut d’Azure Stack Hub, l’URI de redirection apparaît sous la forme `https://portal.<region>.<FQDN>/TokenAuthorize`.

    ![Configuration de l’application Dropbox][16]

9. Dans un nouvel onglet ou une nouvelle fenêtre de navigateur, connectez-vous au portail d’administration Azure Stack Hub en tant qu’administrateur du service.
10. Accédez à **Fournisseurs de ressources** et sélectionnez **Administrateur du fournisseur de ressources App Service**.
11. Sélectionnez **Source control configuration** (Configuration du contrôle de code source).
12. Copiez et collez la **clé d’application** dans la zone de saisie **ID client** et la **clé secrète d’application** dans la zone de saisie **Clé secrète client** pour Dropbox.
13. Sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Les utilisateurs peuvent désormais utiliser les sources de déploiement pour le [déploiement continu](/azure/app-service/deploy-continuous-deployment), le [déploiement Git local](/azure/app-service/deploy-local-git), et la [synchronisation de dossiers de cloud](/azure/app-service/deploy-content-sync).

<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png
