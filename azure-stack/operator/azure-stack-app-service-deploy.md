---
title: Déployer App Service dans Azure Stack Hub
description: Découvrez comment déployer App Service dans Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 05/05/2020
ms.author: bryanla
ms.reviewer: anwestg
ms.lastreviewed: 04/13/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: d8b5be96bf3e150308faf01c161d20e180beeb69
ms.sourcegitcommit: 53b0dde60a6435936a5e0cb9e931245f262d637a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91106982"
---
# <a name="deploy-app-service-in-azure-stack-hub"></a>Déployer App Service dans Azure Stack Hub

[!INCLUDE [Azure Stack hub update reminder](../includes/app-service-hub-update-banner.md)]

> [!IMPORTANT]
> Avant d’exécuter le programme d’installation du fournisseur de ressources, vous devez suivre les étapes décrites dans [Avant de commencer](azure-stack-app-service-before-you-get-started.md).

::: zone pivot="state-connected"
Dans cet article, vous allez apprendre à déployer App Service dans Azure Stack Hub afin de permettre à vos utilisateurs de créer des applications web, API et Azure Functions. Vous devez :

- Ajouter le [fournisseur de ressources App Service](azure-stack-app-service-overview.md) à votre déploiement Azure Stack Hub comme décrit dans cet article.
- Après avoir installé le fournisseur de ressources App Service, vous pouvez l’inclure dans vos offres et vos plans. Les utilisateurs peuvent ensuite s’abonner pour obtenir le service et commencer à créer des applications.

## <a name="run-the-app-service-resource-provider-installer"></a>Exécuter le programme d’installation du fournisseur de ressources App Service

L’installation du fournisseur de ressources App Service prend au moins une heure. Le temps nécessaire dépend du nombre d’instances de rôle que vous déployez. Lors du déploiement, le programme d’installation exécute les tâches suivantes :

- Inscription des fournisseurs de ressources requis sur l'abonnement fournisseur par défaut
- Octroi de l'accès contributeur à l'application App Service Identity
- Création d'un groupe de ressources et d'un réseau virtuel (si nécessaire)
- Création de comptes de stockage et de conteneurs pour les artefacts d'installation d'App Service, le service d'utilisation et l'hydratation des ressources
- Téléchargement des artefacts App Service et chargement de ceux-ci sur le compte de stockage App Service
- Déploiement d'App Service
- Inscription du service d'utilisation
- Création des entrées DNS pour App Service
- Inscription des fournisseurs de ressources d'administration et de locataire App Service
- Inscription des éléments de la galerie - Web, API, Function App, plan App Service, WordPress, DNN, Orchard et Django

Pour déployer le fournisseur de ressources App Service, procédez comme suit :

1. Exécutez appservice.exe en tant qu’administrateur sur un ordinateur ayant accès au point de terminaison de gestion des ressources Azure de l’administrateur Azure Stack Hub.

2. Sélectionnez **Déployer App Service ou effectuer une mise à niveau vers la dernière version**.

    ![Capture d’écran montrant l’écran principal du programme d'installation Azure App Service.][1]

3. Consultez et acceptez les termes du contrat de licence logiciel Microsoft, puis sélectionnez **Suivant**.

4. Consultez et acceptez les termes du contrat de licence tiers, puis sélectionnez **Suivant**.

5. Vérifiez l’exactitude des informations de configuration du Cloud App Service. Si vous avez utilisé les paramètres par défaut durant le déploiement d’ASDK, vous pouvez accepter les valeurs par défaut. Toutefois, si vous avez personnalisé les options lors du déploiement d’ASDK, ou que vous effectuez le déploiement sur un système Azure Stack Hub intégré, vous devrez modifier les valeurs dans cette fenêtre de façon à indiquer les différences.

   Par exemple, si vous utilisez le suffixe de domaine mycloud.com, vous devez remplacer votre point de terminaison Azure Resource Manager de locataire Azure Stack Hub par management.&lt;région&gt;.mycloud.com. Vérifiez les paramètres, puis sélectionnez **Suivant** pour les enregistrer.

   ![Capture d’écran montrant l’écran permettant de spécifier les points de terminaison ARM pour App Service.][2]

6. Sur la page du programme d’installation App Service suivante, vous devez vous connecter à votre Azure Stack Hub :

    1. Sélectionnez la méthode de connexion que vous souhaitez utiliser : **Informations d’identification** ou **Principal du service**
 
        - **Informations d'identification**
            - Si vous utilisez Azure Active Directory (Azure AD), entrez le compte et le mot de passe d'administrateur Azure AD que vous avez indiqués lors du déploiement d'Azure Stack Hub. Sélectionnez **Connecter**.
            - Si vous utilisez Active Directory Federation Services (AD FS), fournissez votre compte d’administrateur. Par exemple : cloudadmin@azurestack.local. Entrez votre mot de passe, puis sélectionnez **Se connecter**.

        - **Principal du service**
            - Le principal de service que vous utilisez **doit** disposer de droits de **propriétaire** sur l'**abonnement fournisseur par défaut**
            - Indiquez l'**ID du principal de service**, le **fichier de certificat** et le **mot de passe**, puis sélectionnez **Se connecter**.

    1. Dans **Abonnements Azure Stack Hub**, sélectionnez **Abonnement au fournisseur par défaut**.  Azure App Service sur Azure Stack Hub **doit** être déployé dans l'**abonnement au fournisseur par défaut**.

    1. Dans **Emplacements Azure Stack Hub**, sélectionnez l'emplacement qui correspond à la région où vous effectuez le déploiement. Par exemple, sélectionnez **local** si vous effectuez le déploiement sur ASDK.

    ![Capture d’écran montrant où vous spécifiez les informations d’abonnement Azure Stack Hub dans le programme d’installation App Service.][3]

7. Maintenant, vous pouvez déployer dans un réseau virtuel existant que vous avez configuré [en suivant ces étapes](azure-stack-app-service-before-you-get-started.md#virtual-network), ou laisser le programme d’installation d’App Service créer un nouveau réseau et de nouveaux sous-réseaux virtuels. Pour créer un VNet, procédez comme suit :

   a. Sélectionnez **Créer un réseau virtuel avec les paramètres par défaut**, acceptez les valeurs par défaut, puis cliquez sur **Suivant**.

   b. Vous pouvez aussi sélectionner **Utiliser un réseau virtuel et des sous-réseaux existants**. Effectuez les actions suivantes :

     - Sélectionnez le **groupe de ressources** qui contient votre réseau virtuel.
     - Choisissez le nom du **réseau virtuel** sur lequel vous souhaitez déployer.
     - Sélectionnez les valeurs **Sous-réseau** correspondant à chacun des sous-réseaux de rôle nécessaires.
     - Sélectionnez **Suivant**.

   ![Capture d’écran montrant l’écran dans lequel vous configurez votre réseau virtuel dans le programme d’installation App Service.][4]

8. Entrez les informations du partage de fichiers, puis sélectionnez **Suivant**. L’adresse du partage de fichiers doit utiliser le nom de domaine complet (FQDN) ou bien l’adresse IP de votre serveur de fichiers. Par exemple, \\\appservicefileserver.local.cloudapp.azurestack.external\websites, ou \\\10.0.0.1\websites.  Si vous utilisez un serveur de fichiers qui est joint à un domaine, vous devez entrer le nom d’utilisateur complet incluant le domaine (par exemple, myfileserverdomain\FileShareOwner).

   >[!NOTE]
   >Le programme d’installation tente de tester la connectivité au partage de fichiers avant de continuer. Toutefois, si vous effectuez un déploiement vers un réseau virtuel existant, ce test de connectivité peut échouer. Vous recevez un avertissement et un message d’invite pour continuer. Si les informations du partage de fichiers sont correctes, continuez le déploiement.

   ![Capture d’écran montrant la configuration fileshare dans le programme d’installation App Service.][7]

9. Sur la page suivante du programme d’installation d’App Service, procédez comme suit :

   a. Dans la zone **ID d’application d’identité**, entrez le GUID de l’application d'identité que vous avez créée en tant que [prérequis](azure-stack-app-service-before-you-get-started.md).

   b. Dans la zone **fichier de certificat d’application d’identité**, entrez (ou accédez à) l’emplacement du fichier de certificat.

   c. Dans la zone **Mot de passe du certificat d’application d’identité**, entrez le mot de passe du certificat. Ce mot de passe est celui que vous avez noté lorsque vous avez utilisé le script pour créer les certificats.

   d. Dans la zone **fichier de certificat racine Azure Resource Manager**, entrez (ou accédez à) l’emplacement du fichier de certificat.

   e. Sélectionnez **Suivant**.

   ![Capture d’écran montrant où entrer les informations d’application d’identité dans le programme d’installation App Service.][9]

10. Pour chacune des trois zones de texte de fichier de certificat, cliquez sur **Parcourir** et accédez au fichier de certificat approprié. Vous devez fournir le mot de passe pour chaque certificat. Ces certificats correspondent à ceux que vous avez créés dans [Prérequis pour le déploiement d’App Service sur Azure Stack Hub](azure-stack-app-service-before-you-get-started.md). Cliquez sur **Suivant** après avoir entré toutes les informations.

    | Box | Exemple de nom de fichier de certificat |
    | --- | --- |
    | **Fichier de certificat SSL par défaut d’App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **Fichier de certificat SSL d’API App Service** | api.appservice.local.AzureStack.external.pfx |
    | **Fichier de certificat SSL Publisher App Service** | ftp.appservice.local.AzureStack.external.pfx |

    Si vous avez utilisé un suffixe de domaine différent lorsque vous avez créé les certificats, vos noms de fichier n’utilisent pas *local.AzureStack.external*. À la place, utilisez vos informations de domaine personnalisées.

    ![Capture d’écran montrant où taper les emplacements et les mots de passe des certificats dans le programme d’installation App Service.][10]

11. Entrez les détails SQL Server de l’instance de serveur utilisée pour héberger la base de données du fournisseur de ressources App Service, puis sélectionnez **Suivant**. Le programme d’installation valide les propriétés de connexion SQL.<br><br>Le programme d’installation App Service tente de tester la connectivité au serveur SQL Server avant de continuer. Si vous effectuez un déploiement vers un réseau virtuel existant, ce test de connectivité peut échouer. Vous recevez un avertissement et un message d’invite pour continuer. Si les informations SQL Server sont correctes, continuez le déploiement.

    ![Capture d’écran montrant où entrer les informations de configuration SQL dans le programme d’installation App Service.][11]

12. Passez en revue de l’instance de rôle et les options de la référence SKU. Les valeurs par défaut sont remplies avec le nombre minimal d’instances et la référence SKU minimale pour chaque rôle dans un déploiement de production.  Pour le déploiement d'ASDK, vous pouvez mettre à l’échelle les instances vers des références SKU inférieures afin de réduire le noyau et la validation de la mémoire, mais vous constaterez une dégradation des performances. Un résumé des exigences en termes de processeur virtuel et de mémoire est fourni pour vous aider à planifier votre déploiement. Une fois vos sélections effectuées, sélectionnez **Suivant**.

    >[!NOTE]
    >Pour les déploiements de production, suivez le guide dans [Planification de la capacité pour les rôles serveur Azure App Service dans Azure Stack Hub](azure-stack-app-service-capacity-planning.md).

    | Role | Nombre minimal d’instances | Nombre minimal de références (SKU) | Notes |
    | --- | --- | --- | --- |
    | Contrôleur | 2 | Standard_A4_v2 - (4 cœurs, 8 192 Mo) | Gère et maintient l’intégrité du Cloud App Service. |
    | Gestion | 1 | Standard_D3_v2 - (4 cœurs, 14 336 Mo) | Gère les points de terminaison App Service Azure Resource Manager et d’API, les extensions du portail (admin, locataire, portail Functions) et du service des données. Pour prendre en charge le basculement, définissez le nombre d’instances recommandées sur 2. |
    | Serveur de publication | 1 | Standard_A2_v2 - (2 cœurs, 4 096 Mo) | Publie du contenu via FTP et un déploiement web. |
    | FrontEnd | 1 | Standard_A4_v2 - (4 cœurs, 8 192 Mo) | Route les requêtes vers les applications App Service. |
    | Worker partagé | 1 | Standard_A4_v2 - (4 cœurs, 8 192 Mo) | Héberge les applications web ou d’API et les applications Azure Functions. Il peut être nécessaire d’ajouter plus d’instances. En tant qu’opérateur, vous pouvez définir votre offre et choisir n’importe quel niveau de référence. Les niveaux doivent avoir au minimum un processeur virtuel. |

    ![Capture d’écran montrant où configurer les rôles de travail dans le programme d’installation App Service.][13]

    > [!NOTE]
    > **Windows Server 2016 Core n’est pas une image de plateforme prise en charge pour une utilisation avec Azure App Service sur Azure Stack Hub.  N’utilisez pas d’images d’évaluation pour les déploiements en production.**

13. Dans la zone **Sélectionner l’image de plateforme**, choisissez votre image de machine virtuelle Windows Server 2016 de déploiement parmi les images disponibles dans le fournisseur de ressources de calcul pour le cloud App Service. Sélectionnez **Suivant**.

14. Sur la page suivante du programme d’installation d’App Service, procédez comme suit :

     a. Entrez le nom d’utilisateur et le mot de passe de l’administrateur de la machine virtuelle ayant le rôle de worker.

     b. Entrez le nom d’utilisateur et le mot de passe de l’administrateur des machines virtuelles ayant un autre rôle.

     c. Sélectionnez **Suivant**.

    ![Capture d’écran montrant où configurer les informations de connexion des rôles de travail dans le programme d’installation App Service.][15]

15. Sur la page suivante du programme d’installation d’App Service, procédez comme suit :

    a. Vérifiez les choix effectués. Pour apporter des modifications, utilisez les boutons **Précédent** pour visiter les pages précédentes.

    b. Si les configurations sont correctes, cochez la case.

    c. Pour démarrer le déploiement, sélectionnez **Suivant**.

    ![Capture d’écran montrant les informations de résumé du déploiement de la pile dans le programme d’installation de App Service.][16]

16. Sur la page suivante du programme d’installation d’App Service, procédez comme suit :

    a. Suivez la progression de l’installation. Le déploiement d’App Service sur Azure Stack Hub peut prendre jusqu’à 240 minutes en fonction des sélections par défaut et de l’ancienneté de l’image de Windows 2016 Datacenter de base.

    b. Une fois le programme d’installation terminé avec succès, sélectionnez **Quitter**.

    ![Capture d’écran montrant la progression du déploiement dans le programme d’installation App Service.][17]

## <a name="post-deployment-steps"></a>Étapes de post-déploiement

> [!IMPORTANT]
> Si vous avez indiqué le fournisseur de ressources App Service avec une instance SQL Always On, vous **devez** [ajouter les bases de données appservice_hosting et appservice_metering à un groupe de disponibilité](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) et synchroniser les bases de données pour éviter toute perte de service en cas de basculement d’une base de données.

Si vous effectuez un déploiement sur un réseau virtuel existant en utilisant une adresse IP interne pour vous connecter à votre serveur de fichiers, vous devez ajouter une règle de sécurité de trafic sortant. Cette règle active le trafic SMB entre le sous-réseau worker et le serveur de fichiers. Dans le portail administrateur, accédez au groupe de sécurité réseau WorkersNsg, puis ajoutez une règle de sécurité sortante ayant les propriétés suivantes :

- Source : Quelconque
- Plage de ports source : : *
- Destination : Adresses IP
- Plage d’adresses IP de destination : plage d’adresses IP de votre serveur de fichiers
- Plage de ports de destination : 445
- Protocole : TCP
- Action : Allow
- Priorité : 700
- Nom : Outbound_Allow_SMB445

## <a name="validate-the-app-service-on-azure-stack-hub-installation"></a>Valider l’installation App Service sur Azure Stack Hub

1. Dans le portail administrateur Azure Stack Hub, allez dans **Administration - App Service**.

2. Dans la vue d’ensemble, sous les statuts, vérifiez que le **Statut** affiche **Tous les rôles sont prêts**.

    ![Administration d’App Service](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-app-service-on-azure-stack-hub"></a>Tester App Service sur Azure Stack Hub

Après avoir déployé et inscrit le fournisseur de ressources App Service, testez-le pour vous assurer que les utilisateurs peuvent déployer des applications web et d’API.

>[!NOTE]
>Vous devez créer une offre avec l’espace de noms Microsoft.Web dans le plan. Vous devez également avoir un abonnement locataire qui s’abonne à cette offre. Pour plus d’informations, consultez [Créer une offre](azure-stack-create-offer.md) et [Créer un plan](azure-stack-create-plan.md).
>
>Vous *devez* avoir un abonnement locataire pour créer des applications qui utilisent App Service sur Azure Stack Hub. Les seules fonctionnalités qu’un administrateur de service peut effectuer dans le portail administrateur sont liées à l’administration du fournisseur de ressources App Service. Ces fonctionnalités incluent l’ajout de capacité, la configuration de sources de déploiement, l’ajout de niveaux Worker et de références SKU.
>
>Pour créer des applications web, d’API et Azure Functions, vous devez utiliser le portail utilisateur et disposer d’un abonnement locataire.
>

Pour créer une application web de test, procédez comme suit :

1. Sur le portail utilisateur Azure Stack Hub, sélectionnez **Créer une ressource** > **Web + Mobile** > **Application web**.

2. Sous **application Web**, entrez un nom dans **application Web**.

3. Sous **Groupe de ressources**, sélectionnez **Nouveau**. Entrez un nom pour le **groupe de ressources**.

4. Sélectionnez **Plan App Service/Emplacement** > **Créer**.

5. Sous **plan App Service**, entrez un nom pour le **plan App Service**.

6. Sélectionnez **Niveau tarifaire** > **Libre-Partagé** ou **Partagé-Partagé** > **Sélectionnez** > **OK** > **Créer**.

7. Une vignette pour la nouvelle application web s’affiche dans le tableau de bord. Sélectionnez la vignette.

8. Dans le panneau **Application web**, cliquez sur **Parcourir** pour afficher le site web par défaut pour cette application.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Déployer un site web WordPress, DNN ou Django (facultatif)

1. Dans le portail utilisateur Azure Stack Hub, sélectionnez **+** , accédez à la Place de marché Azure, déployez un site web Django et attendez que l’opération se termine. La plateforme web Django utilise une base de données basée sur système de fichiers. Elle ne nécessite aucun fournisseur de ressources supplémentaire, comme SQL ou MySQL.

2. Si vous avez également déployé un fournisseur de ressources MySQL, vous pouvez déployer un site web WordPress à partir de la Place de marché. Lorsque vous êtes invité à entrer les paramètres de la base de données, entrez le nom d’utilisateur *User1\@Server1*, avec le nom d’utilisateur et le nom de serveur de votre choix.

3. Si vous avez également déployé un fournisseur de ressources SQL Server, vous pouvez déployer un site web DNN à partir de la Place de marché. Lorsque vous êtes invité à entrer les paramètres de la base de données, sélectionnez une base de données sur l’ordinateur exécutant SQL Server connecté à votre fournisseur de ressources.
::: zone-end



<!----------------------------------------- DISCONNECTED PIVOT -------------------------------------------->
::: zone pivot="state-disconnected"
Dans cet article, vous allez apprendre à déployer le [fournisseur de ressources Azure App Service](azure-stack-app-service-overview.md) dans un environnement Azure Stack Hub qui :
- n’est pas connecté à internet.
- est sécurisé par les services AD FS (Active Directory Federation Services).

Pour ajouter le fournisseur de ressources Azure App Service à votre déploiement Azure Stack Hub hors connexion, vous devez effectuer ces tâches de niveau supérieur :

1. Effectuez les [étapes préalables](azure-stack-app-service-before-you-get-started.md) (telles que l’achat de certificats, ce qui peut prendre quelques jours avant leur réception).
2. [Téléchargez et extrayez les fichiers d’installation et helper](azure-stack-app-service-before-you-get-started.md) sur une machine connectée à Internet.
3. Créez un package d’installation hors connexion.
4. Exécutez le fichier du programme d’installation appservice.exe.

## <a name="create-an-offline-installation-package"></a>Créer un package d'installation hors connexion

Pour déployer Azure App Service dans un environnement hors connexion, créez d’abord un package d’installation hors connexion sur une machine connectée à Internet.

1. Exécutez le programme d’installation AppService.exe sur une machine connectée à Internet. 

2. Sélectionnez **Avancé** > **Créer un package d’installation hors connexion**. Cette étape prend plusieurs minutes.

    ![Créer un package hors connexion dans le programme d’installation Azure App Service][31]

3. Le programme d’installation Azure App Service crée un package d’installation hors connexion et affiche le chemin pour y accéder. Vous pouvez sélectionner **Ouvrir le dossier** pour ouvrir le dossier dans l’Explorateur de fichiers.

    ![Package d’installation hors connexion généré dans le programme d’installation Azure App Service](media/azure-stack-app-service-deploy-offline/image02.png)

4. Copiez le programme d'installation (AppService.exe) et le package d'installation hors connexion sur un ordinateur connecté à votre instance d'Azure Stack Hub.

## <a name="complete-the-offline-installation-of-azure-app-service-on-azure-stack-hub"></a>Terminer l’installation hors connexion d’Azure App Service sur Azure Stack Hub

1. Exécutez appservice.exe en tant qu’administrateur sur un ordinateur ayant accès au point de terminaison de gestion des ressources Azure de l’administrateur Azure Stack Hub.

1. Sélectionnez **Avancé** > **Effectuer l’installation hors connexion**.

    ![Effectuer une installation hors connexion dans le programme d’installation Azure App Service][32]

1. Accédez à l’emplacement du package d’installation hors connexion que vous avez créé, puis sélectionnez **Suivant**.

    ![Spécifier le chemin du package d’installation hors connexion dans le programme d’installation Azure App Service](media/azure-stack-app-service-deploy-offline/image04.png)

1. Passez en revue et acceptez les termes du contrat de licence logiciel Microsoft, puis sélectionnez **Suivant**.

1. Passez en revue et acceptez les termes du contrat de licence tiers, puis sélectionnez **Suivant**.


1. Vérifiez que les informations de configuration du cloud Azure App Service sont correctes. Si vous avez utilisé les paramètres par défaut durant le déploiement de l’ASDK, vous pouvez accepter les valeurs par défaut. Cependant, si vous avez personnalisé les options lors du déploiement d’Azure Stack Hub, ou que vous effectuez le déploiement sur un système intégré, vous devez modifier les valeurs dans cette fenêtre de façon à refléter ces modifications. Par exemple, si vous utilisez le suffixe de domaine mycloud.com, vous devez remplacer votre point de terminaison Azure Resource Manager de locataire Azure Stack Hub par `management.<region>.mycloud.com`. Après avoir vérifié vos informations, sélectionnez **Suivant**.

    ![Configurer le cloud Azure App Service dans le programme d’installation Azure App Service][33]

1. Sur la page du programme d’installation App Service suivante, vous devez vous connecter à votre Azure Stack Hub :

    1. Sélectionnez la méthode de connexion que vous souhaitez utiliser : **Informations d’identification** ou **Principal du service**
        - **Informations d'identification**
            - Si vous utilisez Azure Active Directory (Azure AD), entrez le compte et le mot de passe d'administrateur Azure AD que vous avez indiqués lors du déploiement d'Azure Stack Hub. Sélectionnez **Connecter**.
            - Si vous utilisez Active Directory Federation Services (AD FS), fournissez votre compte d’administrateur. Par exemple : cloudadmin@azurestack.local. Entrez votre mot de passe, puis sélectionnez **Se connecter**.
        - **Principal du service**
            - Le principal de service que vous utilisez **doit** disposer de droits de **propriétaire** sur l'**abonnement fournisseur par défaut**
            - Indiquez l'**ID du principal de service**, le **fichier de certificat** et le **mot de passe**, puis sélectionnez **Se connecter**.

    1. Dans **Abonnements Azure Stack Hub**, sélectionnez **Abonnement au fournisseur par défaut**.  Azure App Service sur Azure Stack Hub **doit** être déployé dans l'**abonnement au fournisseur par défaut**.

    1. Dans **Emplacements Azure Stack Hub**, sélectionnez l'emplacement qui correspond à la région où vous effectuez le déploiement. Par exemple, sélectionnez **local** si vous effectuez le déploiement sur ASDK.

1. Vous pouvez autoriser le programme d’installation Azure App Service à créer un réseau virtuel et des sous-réseaux associés. Vous pouvez aussi déployer sur un réseau virtuel existant, comme ce qui est configuré via [ces étapes](azure-stack-app-service-before-you-get-started.md#virtual-network).
   - Pour utiliser la méthode du programme d’installation Azure App Service, sélectionnez **Créer un réseau virtuel avec les paramètres par défaut**, acceptez les valeurs par défaut, puis sélectionnez **Suivant**.
   - Pour déployer sur un réseau existant, sélectionnez **Utiliser le réseau virtuel et les sous-réseaux existants**, puis :
       1. Sélectionnez l’option **Groupe de ressources** qui contient votre réseau virtuel.
       2. Choisissez le nom du **réseau virtuel** sur lequel vous voulez déployer.
       3. Sélectionnez les valeurs **Sous-réseau** correspondant à chacun des sous-réseaux de rôle nécessaires.
       4. Sélectionnez **Suivant**.

      ![Informations sur le réseau et le sous-réseau virtuels dans le programme d’installation Azure App Service][35]

1. Entrez les informations du partage de fichiers, puis sélectionnez **Suivant**. L’adresse du partage de fichiers doit utiliser le nom de domaine complet (FQDN) ou bien l’adresse IP de votre serveur de fichiers. Par exemple : \\\appservicefileserver.local.cloudapp.azurestack.external\websites ou \\\10.0.0.1\websites.  Si vous utilisez un serveur de fichiers qui est joint à un domaine, vous devez entrer le nom d’utilisateur complet incluant le domaine. Par exemple : `<myfileserverdomain>\<FileShareOwner>`.

    > [!NOTE]
    > Le programme d’installation tente de tester la connectivité au partage de fichiers avant de continuer. Cependant, si vous avez choisi de déployer sur un réseau virtuel existant, il est possible que le programme d’installation ne puisse pas se connecter au partage de fichiers et affiche un avertissement vous demandant si vous voulez continuer. Vérifiez les informations du partage de fichiers et continuez si elles sont correctes.

   ![Informations sur le partage de fichiers dans le programme d’installation Azure App Service][38]

1. Sur la page suivante :
    1. Dans la zone **ID d’application d’identité**, entrez le GUID de l’application d'identité que vous avez créée en tant que [prérequis](azure-stack-app-service-before-you-get-started.md).
    1. Dans la zone **fichier de certificat d’application d’identité**, entrez (ou accédez à) l’emplacement du fichier de certificat.
    1. Dans la zone **Mot de passe du certificat d’application d’identité**, entrez le mot de passe du certificat. Ce mot de passe est celui que vous avez noté lorsque vous avez utilisé le script pour créer les certificats.
    1. Dans la zone **fichier de certificat racine Azure Resource Manager**, entrez (ou accédez à) l’emplacement du fichier de certificat.
    1. Sélectionnez **Suivant**.

    ![Entrer l’ID d’application et les informations de certificat dans le programme d’installation Azure App Service][40]

1. Pour chacune des trois zones de texte de fichier de certificat, cliquez sur **Parcourir** et accédez au fichier de certificat approprié. Vous devez fournir le mot de passe pour chaque certificat. Ces certificats correspondent à ceux que vous avez créés dans [Prérequis pour le déploiement d’App Service sur Azure Stack Hub](azure-stack-app-service-before-you-get-started.md). Cliquez sur **Suivant** après avoir entré toutes les informations.

    | Box | Exemple de nom de fichier de certificat |
    | --- | --- |
    | **Fichier de certificat SSL par défaut d’App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **Fichier de certificat SSL d’API App Service** | api.appservice.local.AzureStack.external.pfx |
    | **Fichier de certificat SSL Publisher App Service** | ftp.appservice.local.AzureStack.external.pfx |

    Si vous avez utilisé un suffixe de domaine différent lorsque vous avez créé les certificats, vos noms de fichier n’utilisent pas *local.AzureStack.external*. À la place, utilisez vos informations de domaine personnalisées.

    ![Entrer informations de certificat SSL dans le programme d’installation Azure App Service][41]

1. Entrez les détails SQL Server de l’instance de serveur utilisée pour héberger les bases de données du fournisseur de ressources Azure App Service, puis sélectionnez **Suivant**. Le programme d’installation valide les propriétés de connexion SQL. Pour le nom du serveur SQL Server, vous **devez** entrer l’adresse IP interne ou le nom de domaine complet (FQDN).

    > [!NOTE]
    > Le programme d’installation tente de tester la connectivité à l’ordinateur exécutant SQL Server avant de continuer. Cependant, si vous avez choisi d’effectuer le déploiement sur un réseau virtuel existant, il est possible que le programme d’installation ne puisse pas se connecter à l’ordinateur exécutant SQL Server et affiche un avertissement vous demandant si vous voulez continuer. Vérifiez les informations du serveur SQL Server et continuez si elles sont correctes.
    >
    > À compter d’Azure App Service sur Azure Stack Hub 1.3 et version ultérieure, le programme d’installation vérifie si l’ordinateur exécutant SQL Server a l’autonomie de la base de données activée au niveau de SQL Server. Si ce n’est pas le cas, l’exception suivante s’affiche :
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;
    >    GO
    >    RECONFIGURE;
    >    GO
    >    ***********************************************************
    > ```
    > Pour plus d’informations, consultez les [notes de publication pour Azure App Service sur Azure Stack Hub 1.3](azure-stack-app-service-release-notes-update-three.md).

    ![Entrer les informations sur le serveur SQL Server dans le programme d’installation Azure App Service][42]

1. Passez en revue de l’instance de rôle et les options de la référence SKU. Les valeurs par défaut sont remplies avec le nombre minimal d’instances et la référence SKU minimale pour chaque rôle dans un déploiement de production.  Pour le déploiement d'ASDK, vous pouvez mettre à l’échelle les instances vers des références SKU inférieures afin de réduire le noyau et la validation de la mémoire, mais vous constaterez une dégradation des performances.  Un résumé des exigences en termes de processeur virtuel et de mémoire est fourni pour vous aider à planifier votre déploiement. Une fois vos sélections effectuées, sélectionnez **Suivant**.

     > [!NOTE]
     > Pour les déploiements de production, suivez l’aide fournie par la page [Planification de la capacité pour les rôles serveur Azure App Service dans Azure Stack Hub](azure-stack-app-service-capacity-planning.md).
     >
     >

    
    | Role | Nombre minimal d’instances | Nombre minimal de références (SKU) | Notes |
    | --- | --- | --- | --- |
    | Contrôleur | 2 | Standard_A4_v2 - (4 cœurs, 8 192 Mo) | Gère et maintient l’intégrité du Cloud App Service. |
    | Gestion | 1 | Standard_D3_v2 - (4 cœurs, 14 336 Mo) | Gère les points de terminaison App Service Azure Resource Manager et d’API, les extensions du portail (admin, locataire, portail Functions) et du service des données. Pour prendre en charge le basculement, définissez le nombre d’instances recommandées sur 2. |
    | Serveur de publication | 1 | Standard_A2_v2 - (2 cœurs, 4 096 Mo) | Publie du contenu via FTP et un déploiement web. |
    | FrontEnd | 1 | Standard_A4_v2 - (4 cœurs, 8 192 Mo) | Route les requêtes vers les applications App Service. |
    | Worker partagé | 1 | Standard_A4_v2 - (4 cœurs, 8 192 Mo) | Héberge les applications web ou d’API et les applications Azure Functions. Il peut être nécessaire d’ajouter plus d’instances. En tant qu’opérateur, vous pouvez définir votre offre et choisir n’importe quel niveau de référence. Les niveaux doivent avoir au minimum un processeur virtuel. |

    ![Définir des niveaux de rôle et des options de référence SKU dans le programme d’installation Azure App Service][44]

1. Dans la zone **Sélectionner l’image de plateforme**, choisissez votre image de machine virtuelle Windows Server 2016 de déploiement parmi les images disponibles dans le fournisseur de ressources de calcul pour le cloud Azure App Service. Sélectionnez **Suivant**.

    > [!NOTE]
    > Windows Server 2016 Core *n’est pas* une image de plateforme prise en charge pour une utilisation avec Azure App Service sur Azure Stack Hub.  N’utilisez pas d’images d’évaluation pour les déploiements en production. Azure App Service sur Azure Stack Hub nécessite l’activation de Microsoft .NET 3.5.1 SP1 sur l’image utilisée pour le déploiement. Cette fonctionnalité n’est pas activée sur les images Windows Server 2016 syndiquées sur la Place de marché. Par conséquent, vous devez créer et utiliser une image Windows Server 2016 avec cette fonctionnalité pré-activée.
    >
    > Pour plus d’informations sur la création d’une image personnalisée et sur son ajout à la Place de marché, consultez [Ajouter une image de machine virtuelle personnalisée à Azure Stack Hub](azure-stack-add-vm-image.md). Lorsque vous ajoutez l’image à la Place de marché, veillez à spécifier :
    >
    >- Serveur de publication = MicrosoftWindowsServer
    >- Offre = WindowsServer
    >- Référence SKU = 2016-Datacenter
    >- Version = Spécifier la dernière version

1. Sur la page suivante :
     1. Entrez le nom d’utilisateur et le mot de passe de l’administrateur de la machine virtuelle ayant le rôle de worker.
     2. Entrez le nom d’utilisateur et le mot de passe de l’administrateur de machines virtuelles Autres rôles.
     3. Sélectionnez **Suivant**.

    ![Entrer le rôle Administrateurs de machines virtuelles dans le programme d’installation Azure App Service][46]

1. Sur la page de résumé :
    1. Vérifiez les choix effectués. Pour apporter des modifications, utilisez les boutons **Précédent** pour visiter les pages précédentes.
    2. Si les configurations sont correctes, cochez la case.
    3. Pour démarrer le déploiement, sélectionnez **Suivant**.

    ![Résumé des sélections effectuées dans le programme d’installation Azure App Service][47]

1. Sur la page suivante :
    1. Suivez la progression de l’installation. Le déploiement d’App Service sur Azure Stack Hub peut prendre jusqu’à 240 minutes en fonction des sélections par défaut et de l’ancienneté de l’image de Windows 2016 Datacenter de base.

    2. Une fois l’exécution du programme d’installation terminée, sélectionnez **Quitter**.

    ![Suivre le processus d’installation dans le programme d’installation Azure App Service][48]

## <a name="post-deployment-steps"></a>Étapes de post-déploiement

> [!IMPORTANT]
> Si vous avez spécifié le fournisseur de ressources Azure App Service avec une instance SQL Always On, vous **devez** [ajouter les bases de données appservice_hosting et appservice_metering à un groupe de disponibilité](/sql/database-engine/availability-groups/windows/availability-group-add-a-database). Vous devez aussi synchroniser les bases de données pour éviter toute perte de service en cas de basculement d’une base de données.

Si vous avez choisi de procéder au déploiement dans un réseau virtuel existant et une adresse IP interne pour vous connecter à votre serveur de fichiers, vous devez ajouter une règle de sécurité sortante, qui autorise le trafic SMB entre le sous-réseau Worker et le serveur de fichiers. Dans le portail administrateur, accédez au groupe de sécurité réseau WorkersNsg, puis ajoutez une règle de sécurité sortante ayant les propriétés suivantes :

- Source : Quelconque
- Plage de ports source : : *
- Destination : Adresses IP
- Plage d’adresses IP de destination : plage d’adresses IP de votre serveur de fichiers
- Plage de ports de destination : 445
- Protocole : TCP
- Action : Allow
- Priorité : 700
- Nom : Outbound_Allow_SMB445

## <a name="validate-the-azure-app-service-on-azure-stack-hub-installation"></a>Valider l’installation Azure App Service sur Azure Stack Hub

1. Dans le portail administrateur Azure Stack Hub, allez dans **Administration - App Service**.

1. Dans la vue d’ensemble, sous les statuts, vérifiez que le **Statut** affiche **Tous les rôles sont prêts**.

    ![Vue d’ensemble de l’administration d’Azure App Service](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-azure-app-service-on-azure-stack-hub"></a>Tester Azure App Service sur Azure Stack Hub

Après avoir déployé et inscrit le fournisseur de ressources Azure App Service, testez-le pour vous assurer que les utilisateurs peuvent déployer des applications web et d’API.

> [!NOTE]
> Vous devez créer une offre avec l’espace de noms Microsoft.Web dans le plan. Ensuite, vous devez avoir un abonnement de locataire qui s’abonne à cette offre. Pour plus d’informations, consultez [Créer une offre](azure-stack-create-offer.md) et [Créer un plan](azure-stack-create-plan.md).
>
> Vous *devez* avoir un abonnement locataire pour créer des applications qui utilisent Azure App Service sur Azure Stack Hub. Les seules fonctionnalités qu’un administrateur de service peut effectuer dans le portail administrateur sont liées à l’administration du fournisseur de ressources Azure App Service. Ces fonctionnalités incluent l’ajout de capacité, la configuration de sources de déploiement, l’ajout de niveaux Worker et de références.
>
> À compter de la troisième préversion technique, vous devez utiliser le portail du locataire et disposer d’un abonnement utilisateur pour créer des applications web, d’API et Azure Functions.

1. Sur le portail utilisateur Azure Stack Hub, sélectionnez **Créer une ressource** > **Web + Mobile** > **Application web**.

1. Dans le panneau **Application web**, tapez un nom dans la zone **Application web**.

1. Sous **Groupe de ressources**, sélectionnez **Nouveau**. Tapez un nom dans la zone **Groupe de ressources**.

1. Sélectionnez **Plan App Service/Emplacement** > **Créer**.

1. Dans le panneau **Plan App Service**, tapez un nom dans la zone **Plan App Service**.

1. Sélectionnez **Niveau tarifaire** > **Libre-Partagé** ou **Partagé-Partagé** > **Sélectionnez** > **OK** > **Créer**.

1. En moins d’une minute, une vignette pour la nouvelle application web apparaît dans le tableau de bord. Sélectionnez la vignette.

1. Dans le panneau **Application web**, cliquez sur **Parcourir** pour voir le site web par défaut pour cette application.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Déployer un site web WordPress, DNN ou Django (facultatif)

1. Dans le portail utilisateur Azure Stack Hub, sélectionnez **+** , accédez à la Place de marché Azure, déployez un site web Django et attendez que l’opération se termine. La plateforme web Django utilise une base de données basée sur système de fichiers. Elle ne nécessite aucun fournisseur de ressources supplémentaire, comme SQL ou MySQL.

1. Si vous avez également déployé un fournisseur de ressources MySQL, vous pouvez déployer un site web WordPress à partir de la Place de marché Azure. Lorsque vous êtes invité à entrer les paramètres de la base de données, entrez le nom d’utilisateur *User1\@Server1*, avec le nom d’utilisateur et le nom de serveur de votre choix.

1. Si vous avez également déployé un fournisseur de ressources SQL Server, vous pouvez déployer un site web DNN à partir de la Place de marché Azure. Quand vous êtes invité à entrer les paramètres de la base de données, sélectionnez une base de données sur l’ordinateur exécutant SQL Server qui est connectée à votre fournisseur de ressources.

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Anticipez les opérations d’administration supplémentaires pour App Service sur Azure Stack Hub :

- [Planification de la capacité](azure-stack-app-service-capacity-planning.md)
- [Configurer des sources de déploiement](azure-stack-app-service-configure-deployment-sources.md)

<!-- Connected image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config-with-values.png
[7]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[8]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration-error.png
[9]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[10]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[11]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration-error.png
[13]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[14]: ./media/azure-stack-app-service-deploy/app-service-windows-image-selection.png
[15]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[16]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[17]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png

<!-- Disconnected image references-->
[31]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[32]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[33]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[34]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[35]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[36]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[37]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[38]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[39]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[40]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[41]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[42]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[43]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[44]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[45]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[46]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[47]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[48]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
