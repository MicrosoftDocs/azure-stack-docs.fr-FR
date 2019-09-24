---
title: Déployer App Service dans Azure Stack | Microsoft Docs
description: Découvrez comment déployer App Service dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 219d8bcf884945353b08186324edc23feb028964
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974959"
---
# <a name="deploy-app-service-in-azure-stack"></a>Déployer App Service dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Cet article explique comment déployer App Service dans Azure Stack.

> [!IMPORTANT]
> Appliquez la mise à jour 1907 à votre système intégré Azure Stack ou déployez le dernier kit de développement Azure Stack (ASDK) avant de déployer Azure App Service 1.7.

Vous pouvez donner à vos utilisateurs la possibilité de créer des applications web et API. Pour permettre aux utilisateurs de créer ces applications, vous devez :

- Ajouter le [fournisseur de ressources App Service](azure-stack-app-service-overview.md) à votre déploiement Azure Stack comme décrit dans cet article.
- Après avoir installé le fournisseur de ressources App Service, vous pouvez l’inclure dans vos offres et vos plans. Les utilisateurs peuvent ensuite s’abonner pour obtenir le service et commencer à créer des applications.

> [!IMPORTANT]
> Avant d’exécuter le programme d’installation du fournisseur de ressources, assurez-vous d’avoir suivi les recommandations de la section [Avant de démarrer](azure-stack-app-service-before-you-get-started.md) et d’avoir lu les [notes de publication](azure-stack-app-service-release-notes-update-seven.md) qui accompagnent la version 1.7. Ce contenu vous éclaire sur les nouvelles fonctionnalités, les correctifs et les problèmes connus qui pourraient impacter votre déploiement.

## <a name="run-the-app-service-resource-provider-installer"></a>Exécuter le programme d’installation du fournisseur de ressources App Service

L’installation du fournisseur de ressources App Service prend au moins une heure. Le temps nécessaire dépend du nombre d’instances de rôle que vous déployez. Lors du déploiement, le programme d’installation exécute les tâches suivantes :

- Crée un conteneur d’objets blob dans le compte de stockage Azure Stack spécifié.
- Crée une zone DNS et les entrées pour App Service.
- Inscrit le fournisseur de ressources App Service.
- Inscrit les éléments de la galerie App Service.

Pour déployer le fournisseur de ressources App Service, procédez comme suit :

1. Exécutez appservice.exe en tant qu’administrateur sur un ordinateur ayant accès au point de terminaison de gestion des ressources Azure de l’administrateur Azure Stack.

2. Sélectionnez **Déployer App Service ou effectuer une mise à niveau vers la dernière version**.

    ![Programme d’installation App Service][1]

3. Consultez et acceptez les termes du contrat de licence logiciel Microsoft, puis sélectionnez **Suivant**.

4. Consultez et acceptez les termes du contrat de licence tiers, puis sélectionnez **Suivant**.

5. Vérifiez l’exactitude des informations de configuration du Cloud App Service. Si vous avez utilisé les paramètres par défaut durant le déploiement d’ASDK, vous pouvez accepter les valeurs par défaut. Toutefois, si vous avez personnalisé les options lors du déploiement d’ASDK, ou que vous effectuez le déploiement sur un système Azure Stack intégré, vous devrez modifier les valeurs dans cette fenêtre de façon à indiquer les différences.

   Par exemple, si vous utilisez le suffixe de domaine mycloud.com, vous devez remplacer votre point de terminaison Azure Resource Manager de locataire Azure Stack par management.&lt;région&gt;.mycloud.com. Vérifiez les paramètres, puis sélectionnez **Suivant** pour les enregistrer.

   ![Programme d’installation App Service][2]

6. Sur la page suivante du programme d’installation d’App Service, procédez comme suit :

    a. Sélectionnez **Se connecter** situé en regard de la zone **Abonnements Azure Stack**.

   - Si vous utilisez Azure Active Directory (Azure AD), entrez votre compte et mot de passe d’administrateur Azure AD que vous avez indiqués lors du déploiement d’Azure Stack. Sélectionnez **Connexion**.
   - Si vous utilisez Active Directory Federation Services (AD FS), fournissez votre compte d’administrateur. Par exemple : cloudadmin@azurestack.local. Entrez votre mot de passe, puis sélectionnez **Se connecter**.

   b. Dans **Abonnements Azure Stack**, sélectionnez **Abonnement au fournisseur par défaut**.

     > [!IMPORTANT]
     > App Service **doit** être déployé sur **l’abonnement du fournisseur par défaut**.

   c. Dans **Emplacements Azure Stack**, sélectionnez l’emplacement qui correspond à la région où vous effectuez le déploiement. Par exemple, sélectionnez **local** si vous effectuez le déploiement sur ASDK.

    ![Programme d’installation App Service][3]

7. Maintenant, vous pouvez déployer dans un réseau virtuel existant que vous avez configuré [en suivant ces étapes](azure-stack-app-service-before-you-get-started.md#virtual-network), ou laisser le programme d’installation d’App Service créer un nouveau réseau et de nouveaux sous-réseaux virtuels. Pour créer un VNet, procédez comme suit :

   a. Sélectionnez **Créer un réseau virtuel avec les paramètres par défaut**, acceptez les valeurs par défaut, puis cliquez sur **Suivant**.

   b. Vous pouvez aussi sélectionner **Utiliser un réseau virtuel et des sous-réseaux existants**. Effectuez les actions suivantes :

     - Sélectionnez le **groupe de ressources** qui contient votre réseau virtuel.
     - Choisissez le nom du **réseau virtuel** sur lequel vous souhaitez déployer.
     - Sélectionnez les valeurs **Sous-réseau** correspondant à chacun des sous-réseaux de rôle nécessaires.
     - Sélectionnez **Suivant**.

   ![Programme d’installation App Service][4]

8. Entrez les informations du partage de fichiers, puis sélectionnez **Suivant**. L’adresse du partage de fichiers doit utiliser le nom de domaine complet (FQDN) ou bien l’adresse IP de votre serveur de fichiers. Par exemple, \\\appservicefileserver.local.cloudapp.azurestack.external\websites, ou \\\10.0.0.1\websites.  Si vous utilisez un serveur de fichiers qui est joint à un domaine, vous devez entrer le nom d’utilisateur complet incluant le domaine (par exemple, myfileserverdomain\FileShareOwner).

   >[!NOTE]
   >Le programme d’installation tente de tester la connectivité au partage de fichiers avant de continuer. Toutefois, si vous effectuez un déploiement vers un réseau virtuel existant, ce test de connectivité peut échouer. Vous recevez un avertissement et un message d’invite pour continuer. Si les informations du partage de fichiers sont correctes, continuez le déploiement.

   ![Programme d’installation App Service][7]

9. Sur la page suivante du programme d’installation d’App Service, procédez comme suit :

   a. Dans la zone **ID d’application d’identité**, entrez le GUID de l’application que vous utilisez pour l’identité (à partir d’Azure AD).

   b. Dans la zone **fichier de certificat d’application d’identité**, entrez (ou accédez à) l’emplacement du fichier de certificat.

   c. Dans la zone **Mot de passe du certificat d’application d’identité**, entrez le mot de passe du certificat. Ce mot de passe est celui que vous avez noté lorsque vous avez utilisé le script pour créer les certificats.

   d. Dans la zone **fichier de certificat racine Azure Resource Manager**, entrez (ou accédez à) l’emplacement du fichier de certificat.

   e. Sélectionnez **Suivant**.

   ![Programme d’installation App Service][9]

10. Pour chacune des trois zones de texte de fichier de certificat, cliquez sur **Parcourir** et accédez au fichier de certificat approprié. Vous devez fournir le mot de passe pour chaque certificat. Ces certificats sont ceux que vous avez créés lors de l’étape [Créer les certificats requis](azure-stack-app-service-before-you-get-started.md#get-certificates). Cliquez sur **Suivant** après avoir entré toutes les informations.

    | Box | Exemple de nom de fichier de certificat |
    | --- | --- |
    | **Fichier de certificat SSL par défaut d’App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **Fichier de certificat SSL d’API App Service** | api.appservice.local.AzureStack.external.pfx |
    | **Fichier de certificat SSL Publisher App Service** | ftp.appservice.local.AzureStack.external.pfx |

    Si vous avez utilisé un suffixe de domaine différent lorsque vous avez créé les certificats, vos noms de fichier n’utilisent pas *local.AzureStack.external*. À la place, utilisez vos informations de domaine personnalisées.

    ![Programme d’installation App Service][10]

11. Entrez les détails SQL Server de l’instance de serveur utilisée pour héberger la base de données du fournisseur de ressources App Service, puis sélectionnez **Suivant**. Le programme d’installation valide les propriétés de connexion SQL.<br><br>Le programme d’installation App Service tente de tester la connectivité au serveur SQL Server avant de continuer. Si vous effectuez un déploiement vers un réseau virtuel existant, ce test de connectivité peut échouer. Vous recevez un avertissement et un message d’invite pour continuer. Si les informations SQL Server sont correctes, continuez le déploiement.

    ![Programme d’installation App Service][11]

12. Passez en revue de l’instance de rôle et les options de la référence SKU. Les valeurs par défaut sont remplies avec le nombre minimal d’instances et la référence SKU minimale pour chaque rôle dans un déploiement ASDK. Un résumé des exigences en termes de processeur virtuel et de mémoire est fourni pour vous aider à planifier votre déploiement. Une fois vos sélections effectuées, sélectionnez **Suivant**.

    >[!NOTE]
    >Pour les déploiements de production, suivez le guide dans [Planification de la capacité pour les rôles serveur Azure App Service dans Azure Stack](azure-stack-app-service-capacity-planning.md).

    | Role | Nombre minimal d’instances | Nombre minimal de références (SKU) | Notes |
    | --- | --- | --- | --- |
    | Controller | 1 | Standard_A2 - (2 processeurs virtuels, 3584 Mo) | Gère et maintient l’intégrité du Cloud App Service. |
    | gestion | 1 | Standard_A2 - (2 processeurs virtuels, 3 584 Mo) | Gère les points de terminaison App Service Azure Resource Manager et d’API, les extensions du portail (admin, locataire, portail Functions) et du service des données. Pour prendre en charge le basculement, augmenter les instances recommandées à 2. |
    | Publisher | 1 | Standard_A1 - (1 processeur virtuel, 1 792 Mo) | Publie du contenu via FTP et un déploiement web. |
    | FrontEnd | 1 | Standard_A1 - (1 processeur virtuel, 1 792 Mo) | Route les requêtes vers les applications App Service. |
    | Worker partagé | 1 | Standard_A1 - (1 processeur virtuel, 1 792 Mo) | Héberge les applications web ou d’API et les applications Azure Functions. Il peut être nécessaire d’ajouter plus d’instances. En tant qu’opérateur, vous pouvez définir votre offre et choisir n’importe quel niveau de référence. Les niveaux doivent avoir au minimum un processeur virtuel. |

    ![Programme d’installation App Service][13]

    >[!NOTE]
    >**Windows Server 2016 Core n’est pas une image de plateforme prise en charge pour une utilisation avec Azure App Service sur Azure Stack.  N’utilisez pas d’images d’évaluation pour les déploiements en production.**

13. Dans la zone **Sélectionner l’image de plateforme**, choisissez votre image de machine virtuelle Windows Server 2016 de déploiement parmi les images disponibles dans le fournisseur de ressources de calcul pour le cloud App Service. Sélectionnez **Suivant**.

14. Sur la page suivante du programme d’installation d’App Service, procédez comme suit :

     a. Entrez le nom d’utilisateur et le mot de passe de l’administrateur de la machine virtuelle ayant le rôle de worker.

     b. Entrez le nom d’utilisateur et le mot de passe de l’administrateur des machines virtuelles ayant un autre rôle.

     c. Sélectionnez **Suivant**.

    ![Programme d’installation App Service][15]

15. Sur la page suivante du programme d’installation d’App Service, procédez comme suit :

    a. Vérifiez les choix effectués. Pour apporter des modifications, utilisez les boutons **Précédent** pour visiter les pages précédentes.

    b. Si les configurations sont correctes, cochez la case.

    c. Pour démarrer le déploiement, sélectionnez **Suivant**.

    ![Programme d’installation App Service][16]

16. Sur la page suivante du programme d’installation d’App Service, procédez comme suit :

    a. Suivez la progression de l’installation. Le déploiement d’App Service sur Azure Stack prend environ 60 minutes avec les sélections par défaut.

    b. Une fois le programme d’installation terminé avec succès, sélectionnez **Quitter**.

    ![Programme d’installation App Service][17]

## <a name="post-deployment-steps"></a>Étapes de post-déploiement

> [!IMPORTANT]
> Si vous avez indiqué le fournisseur de ressources App Service avec une instance SQL Always On, vous **devez** [ajouter les bases de données appservice_hosting et appservice_metering à un groupe de disponibilité](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) et synchroniser les bases de données pour éviter toute perte de service en cas de basculement d’une base de données.

Si vous effectuez un déploiement sur un réseau virtuel existant en utilisant une adresse IP interne pour vous connecter à votre serveur de fichiers, vous devez ajouter une règle de sécurité de trafic sortant. Cette règle active le trafic SMB entre le sous-réseau worker et le serveur de fichiers. Dans le portail administrateur, accédez au groupe de sécurité réseau WorkersNsg, puis ajoutez une règle de sécurité sortante ayant les propriétés suivantes :

- Source : Quelconque
- Plage de ports source : : *
- Destination : Adresses IP
- Plage d’adresses IP de destination : plage d’adresses IP de votre serveur de fichiers
- Plage de ports de destination : 445
- Protocole : TCP
- Action : AUTORISER
- Priorité : 700
- Nom : Outbound_Allow_SMB445

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Valider l’installation App Service sur Azure Stack

1. Dans le portail administrateur Azure Stack, allez dans **Administration - App Service**.

2. Dans la vue d’ensemble, sous les statuts, vérifiez que le **Statut** affiche **Tous les rôles sont prêts**.

    ![Administration d’App Service](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-app-service-on-azure-stack"></a>Tester App Service sur Azure Stack

Après avoir déployé et inscrit le fournisseur de ressources App Service, testez-le pour vous assurer que les utilisateurs peuvent déployer des applications web et d’API.

>[!NOTE]
>Vous devez créer une offre avec l’espace de noms Microsoft.Web dans le plan. Vous devez également avoir un abonnement locataire qui s’abonne à cette offre. Pour plus d’informations, consultez [Créer une offre](azure-stack-create-offer.md) et [Créer un plan](azure-stack-create-plan.md).
>
>Vous *devez* avoir un abonnement locataire pour créer des applications qui utilisent App Service sur Azure Stack. Les seules fonctionnalités qu’un administrateur de service peut effectuer dans le portail administrateur sont liées à l’administration du fournisseur de ressources App Service. Ces fonctionnalités incluent l’ajout de capacité, la configuration de sources de déploiement, l’ajout de niveaux Worker et de références SKU.
>
>Pour créer des applications web, d’API et Azure Functions, vous devez utiliser le portail du locataire et disposer d’un abonnement locataire.
>

Pour créer une application web de test, procédez comme suit :

1. Sur le portail utilisateur Azure Stack, sélectionnez **Créer une ressource** > **Web + Mobile** > **Application web**.

2. Sous **application Web**, entrez un nom dans **application Web**.

3. Sous **Groupe de ressources**, sélectionnez **Nouveau**. Entrez un nom pour le **groupe de ressources**.

4. Sélectionnez **Plan App Service/Emplacement** > **Créer**.

5. Sous **plan App Service**, entrez un nom pour le **plan App Service**.

6. Sélectionnez **Niveau tarifaire** > **Libre-Partagé** ou **Partagé-Partagé** > **Sélectionnez** > **OK** > **Créer**.

7. Une vignette pour la nouvelle application web s’affiche dans le tableau de bord. Sélectionnez la vignette.

8. Dans le panneau **Application web**, cliquez sur **Parcourir** pour afficher le site web par défaut pour cette application.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Déployer un site web WordPress, DNN ou Django (facultatif)

1. Dans le portail du locataire Azure Stack, sélectionnez **+** , accédez à la Place de marché Azure, déployez un site web Django et attendez que l’opération se termine. La plateforme web Django utilise une base de données basée sur système de fichiers. Elle ne nécessite aucun fournisseur de ressources supplémentaire, comme SQL ou MySQL.

2. Si vous avez également déployé un fournisseur de ressources MySQL, vous pouvez déployer un site web WordPress à partir de la Place de marché. Lorsque vous êtes invité à entrer les paramètres de la base de données, entrez le nom d’utilisateur *User1\@Server1*, avec le nom d’utilisateur et le nom de serveur de votre choix.

3. Si vous avez également déployé un fournisseur de ressources SQL Server, vous pouvez déployer un site web DNN à partir de la Place de marché. Lorsque vous êtes invité à entrer les paramètres de la base de données, sélectionnez une base de données sur l’ordinateur exécutant SQL Server connecté à votre fournisseur de ressources.

## <a name="next-steps"></a>Étapes suivantes

Anticipez les opérations d’administration supplémentaires pour App Service sur Azure Stack :

- [Planification de la capacité](azure-stack-app-service-capacity-planning.md)
- [Configurer des sources de déploiement](azure-stack-app-service-configure-deployment-sources.md)

<!--Image references-->
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
