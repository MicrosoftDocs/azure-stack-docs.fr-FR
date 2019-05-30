---
title: Création d’une solution d’analyse des données mises en lots avec Azure et Azure Stack | Microsoft Docs
description: Découvrez comment créer une solution d’analyse des données mises en lots avec Azure et Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 9359c1393229709fc77ee08216a80a26de9135dc
ms.sourcegitcommit: 261df5403ec01c3af5637a76d44bf030f9342410
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252013"
---
# <a name="tutorial-create-a-staged-data-analytics-solution-with-azure-and-azure-stack"></a>Tutoriel : Créer une solution d'analyse des données mises en lots avec Azure et Azure Stack 

*S'applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Apprenez à utiliser à la fois les environnements locaux et de cloud public pour répondre aux besoins des entreprises disposant de plusieurs sites. Azure Stack offre une solution rapide, sécurisée et flexible pour la collecte, le traitement, le stockage et la distribution des données locales et distantes, en particulier lorsque la sécurité, la confidentialité, les stratégies d’entreprise et les exigences réglementaires diffèrent d’un site à l’autre et d’un utilisateur à l’autre.

Dans ce modèle, vos clients collectent des données nécessitant une analyse au point de collecte pour permettre une prise de décisions rapide. Cette collecte de données se produit souvent sans accès à Internet. Lorsque la connexion est établie, vous devrez peut-être effectuer une analyse des données gourmande en ressources pour obtenir des informations supplémentaires. Vous pouvez toujours analyser des données lorsqu’un cloud public est en retard ou non disponible.

Dans ce didacticiel, vous créez un exemple d’environnement pour :

> [!div class="checklist"]
> - Créer l’objet blob de stockage des données brutes.
> - Créer une nouvelle fonction Azure Stack pour déplacer les données propres d’Azure Stack vers Azure.
> - Créer une fonction déclenchée par le stockage Blob.
> - Créer un compte de stockage Azure Stack contenant un objet blob et une file d’attente.
> - Créer une fonction déclenchée par une file d’attente.
> - Tester la fonction déclenchée par une file d’attente.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack est une extension d’Azure. Azure Stack apporte l’agilité et l’innovation du cloud computing à votre environnement local et active le seul cloud hybride qui vous permet de créer et de déployer des applications hybrides en tout lieu.  
> 
> Le livre blanc [Design Considerations for Hybrid Applications](https://aka.ms/hybrid-cloud-applications-pillars) (Étude des conceptions pour les applications hybrides) se penche sur les fondements de la qualité logicielle (sélection élective, extensibilité, disponibilité, résilience, facilité de gestion et sécurité) en matière de conception, de déploiement et d’exploitation des applications hybrides. Les considérations de conception vous aident à optimiser la conception des applications hybrides, en réduisant les risques dans les environnements de production.

## <a name="prerequisites"></a>Prérequis

Certaines tâches de préparation sont nécessaires pour générer cette solution :

-   Une extension Azure Stack installée et opérationnelle (des informations supplémentaires sont disponibles ici : [Vue d’ensemble d’Azure Stack](azure-stack-storage-overview.md)

-   Un abonnement Azure. (Créer un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

-   Télécharger et installer l’[Explorateur de Stockage Microsoft Azure](https://storageexplorer.com/).

-   Vous devrez fournir vos propres données pour que les fonctions puissent les traiter. Les données doivent être générées et disponibles pour leur téléchargement vers le conteneur d’objets blob de stockage Azure Stack.

## <a name="issues-and-considerations"></a>Problèmes et considérations

### <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité

Les fonctions Azure et les solutions de stockage évoluent pour s’adapter au volume de données et aux exigences en matière de traitement. Pour des informations sur l’évolutivité d’Azure et sur les cibles, consultez la [documentation sur l’évolutivité d’Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

### <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Le stockage est la principale considération relative à la disponibilité pour ce modèle. La connexion via des liens rapides est requise pour le traitement et la distribution d’importants volumes de données.

### <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Pensez à la façon dont vos outils de développement et le contrôle de la source vous permettront de gérer votre solution.

## <a name="create-the-raw-data-storage-blob"></a>Créer l’objet blob de stockage des données brutes

Le compte de stockage et le conteneur d’objets blob conservent toutes les données d’origine générées par les activités locales, y compris l’activité des machines et des employés, les données du site, les métriques de production et d’autres rapports.

1.  Connectez-vous au [*portail Azure Stack*](https://portal.local.azurestack.external/).

2.  Sur le portail Azure Stack, développez le menu de gauche pour ouvrir le menu des services, et sélectionnez **Tous les services**. Faites défiler jusqu’à **Stockage**, puis sélectionnez **Comptes de stockage**. Dans la fenêtre Comptes de stockage, sélectionnez **Ajouter**.

3.  Utilisez les informations suivantes pour votre compte :

    a.  Nom : **Votre choix**

    b.  Modèle de déploiement : **Resource Manager**

    c.  Type de compte : **Stockage (Universel v1)**

    d.  Emplacement : **USA Ouest**

    e.  Réplication : **Stockage localement redondant (LRS)**

    f.  Performances : **Standard**

    g.  Transfert sécurisé requis : **Désactivé**

    h.  Abonnement : Choisir une option

    i.  Groupe de ressources : Spécifiez un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant

    j.  Configurer des réseaux virtuels : **Désactivé**

4.  Sélectionnez **Créer pour créer le compte de stockage**.

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image1.png)

5.  Une fois créé, sélectionnez le nom du compte de stockage.

6.  Dans le panneau du compte, sous le titre **SERVICE BLOB**, sélectionnez **Conteneurs**.

7.  En haut du panneau, sélectionnez **+ Conteneur**, puis sélectionnez **Conteneur**.

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image2.png)

8.  Nom : **Votre choix**

9.  Niveau d'accès public : **Conteneur** (accès en lecture anonyme pour les conteneurs et les objets blob)

10.  Sélectionnez **OK**.

## <a name="create-an-azure-stack-function"></a>Créer une fonction Azure Stack

Créez une nouvelle fonction Azure Stack pour déplacer les données propres d’Azure Stack vers Azure.

### <a name="create-the-azure-stack-function-app"></a>Création de l’application de la fonction Azure Stack

1. Connectez-vous au portail [Azure Stack](https://portal.local.azurestack.external).
2. Sélectionnez **Tous les services**.
3. Sélectionnez **Function Apps** (Applications de fonctions) dans le groupe **Web + Mobile**.

4.  Créez l’application de fonction en utilisant les paramètres indiqués dans le tableau sous l’image.

    | Paramètre | Valeur suggérée | Description |
    | ---- | ---- | ---- |
    | Nom de l’application | Nom globalement unique | Nom qui identifie votre nouvelle Function App. Les caractères valides sont `a`-`z`, `0``-9` et `-`. |
    | Abonnement | Votre abonnement | Abonnement sous lequel est créée cette nouvelle application de fonction. |
    | **Groupe de ressources** |  |  |
    | myResourceGroup | Nom du nouveau groupe de ressources dans lequel créer votre Function App. |  |
    | SE |  Windows | L’hébergement serverless est disponible uniquement avec une exécution sur Windows. |
    | **Plan d’hébergement** |  |  |
    | Plan de consommation | Plan d’hébergement qui définit la façon dont les ressources sont allouées à votre Function App. Dans le plan de consommation par défaut, les ressources sont ajoutées dynamiquement comme requis par vos fonctions. Avec cet hébergement serverless, vous payez uniquement pour la durée d’exécution de vos fonctions. |  |
    | Lieu | Région la plus proche de vous | Choisissez une région près de chez vous ou près d’autres services auxquels ont accès vos fonctions. |
    | **Compte de stockage** |  |  |
    | \<compte de stockage créé ci-dessus> | Nom du nouveau compte de stockage utilisé par votre Function App. Les noms des comptes de stockage doivent comporter de 3 à 24 caractères. Le nom ne peut utiliser que des chiffres et des lettres minuscules. Vous pouvez également utiliser un compte existant. |  |

    **Exemple :**

    ![Définir de nouveaux paramètres d’application de fonction](media/azure-stack-solution-staged-data-analytics/image6.png)

5.  Sélectionnez **Créer** pour configurer et déployer l’application de fonction.

6.  Cliquez sur l’icône Notification en haut à droite du portail pour voir le message **Le déploiement a été effectué**.

    ![Définir de nouveaux paramètres d’application de fonction](media/azure-stack-solution-staged-data-analytics/image7.png)

7.  Sélectionnez **Accéder à la ressource** pour afficher la nouvelle application de fonction.

![Function App créée avec succès.](media/azure-stack-solution-staged-data-analytics/image8.png)

### <a name="add-a-function-to-the-azure-stack-function-app"></a>Ajout d’une fonction à l’application de la fonction Azure Stack

1.  Créez une nouvelle fonction en cliquant sur **Fonctions**, puis sur le bouton **+ Nouvelle fonction**.

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image3.png)

2.  Sélectionnez **Déclencheur de minuteur**.

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image4.png)

3.  Sélectionnez le langage **C\#** et nommez la fonction : `upload-to-azure`  Définissez la planification sur `0 0 * * * *`, ce qui, en notation CRON, correspond à une fois par heure.

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image5.png)

## <a name="create-a-blob-storage-triggered-function"></a>Créer une fonction déclenchée par le stockage Blob

1. Développez l’application de fonction, puis sélectionnez le bouton **+** en regard de **Fonctions**.

2. Dans la zone de recherche, saisissez `blob`, puis sélectionnez la langue souhaitée pour le modèle de **déclencheur d’objet Blob**.

   ![Sélectionnez le modèle déclencheur de stockage d’objets blob.](media/azure-stack-solution-staged-data-analytics/image10.png)

3. Utilisez les paramètres spécifiés dans le tableau ci-dessous :

   | Paramètre | Valeur suggérée | Description |
   | ------- | ------- | ------- |
   | Nom | Unique dans votre Function App | Nom de cette fonction déclenchée par l’objet Blob. |
   | path | \<chemin d’accès à partir de l’emplacement de stockage ci-dessus> | Emplacement du stockage Blob analysé. Le nom de fichier de l’objet blob est transmis dans la liaison en tant que paramètre name. |
   | Connexion au compte de stockage | Connexion de l’application de fonction | Vous pouvez utiliser la connexion de compte de stockage qui est déjà utilisée par votre application de fonction ou en créer une. |

   **Exemple :**

   ![Création de la fonction déclenchée par le stockage Blob.](media/azure-stack-solution-staged-data-analytics/image11.png)

4. Sélectionnez **Créer** pour créer la fonction.

### <a name="test-the-function"></a>Tester la fonction

1.  Dans le portail Azure, accédez à la fonction. Développez les **journaux d’activité** en bas de la page et vérifiez que la diffusion en continu des journaux d’activité n’est pas suspendue.

2.  Ouvrez l’Explorateur Stockage et connectez-vous au compte de stockage créé au début de cette section.

3.  Développez le compte de stockage, les **conteneurs d’objets blob** et l’objet blob que vous avez créé précédemment. Sélectionnez **Charger**, puis **Charger des fichiers**.

    ![Chargement d’un fichier dans le conteneur d’objets blob.](media/azure-stack-solution-staged-data-analytics/image12.png)

4.  Dans la boîte de dialogue Charger des fichiers, cliquez sur le champ Fichiers. Accédez à un fichier sur un ordinateur local, par exemple un fichier image, sélectionnez-le, puis sélectionnez **Ouvrir** et **Charger**.

5.  Revenez aux journaux d’activité de fonction et vérifiez que l’objet blob a été lu.

    **Exemple :**

    ![Affichez le message dans les journaux d’activité.](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="create-an-azure-stack-storage-account"></a>Création d’un compte de stockage Azure Stack

Créer un compte de stockage Azure Stack contenant un objet blob et une file d’attente.

### <a name="storage-blob--data-archiving"></a>Archivage des données d’objets blob de stockage

Ce compte de stockage va héberger deux conteneurs. Ces conteneurs sont : un objet blob utilisé pour conserver les données d’archivage et une file d’attente utilisée pour le traitement des données attribuées à la distribution du bureau principal.

Utilisez les étapes et paramètres ci-dessus pour créer un autre compte de stockage et un conteneur d’objets blob à utiliser comme stockage d’archive.

### <a name="storage-queue--filtered-data-holding"></a>Conservation des données filtrées dans la file d’attente de stockage

1.  Utilisez les étapes et paramètres ci-dessus pour accéder au nouveau compte de stockage.

2.  Dans la section Vue d’ensemble du compte de stockage, sélectionnez **File d’attente**.

3.  Sélectionnez **+ File d’attente** et remplissez le champ **Nom** avec un nom pour la nouvelle file d’attente.

4.  Sélectionnez **OK**.

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image14.png)

    ![Alt text](media/azure-stack-solution-staged-data-analytics/image15.png)

## <a name="create-a-queue-triggered-function"></a>Créer une fonction déclenchée par une file d’attente

1.  Procédez comme indiqué dans la section ci-dessus traitant de la création de fonction pour créer une fonction Azure Stack supplémentaire avec un déclencheur de file d’attente au lieu d’un déclencheur d’objet blob.

2.  Utilisez les paramètres spécifiés dans le tableau ci-dessous :

    | Paramètre | Valeur suggérée | Description |
    | ------- | ------- | ------- |
    | Nom | Unique dans votre Function App | Nom de cette fonction déclenchée par la file d’attente. |
    | path | \<chemin d’accès à partir de l’emplacement de stockage ci-dessus> | Emplacement de stockage surveillé. Le nom de fichier de la file d’attente est transmis dans la liaison en tant que paramètre name. |
    | Connexion au compte de stockage | Connexion de l’application de fonction | Vous pouvez utiliser la connexion de compte de stockage qui est déjà utilisée par votre application de fonction ou en créer une. |

3.  Sélectionnez **Créer** pour créer la fonction.

## <a name="test-the-queue-triggered-function"></a>Tester la fonction déclenchée par une file d’attente

1. Dans le portail Azure Stack, accédez à la fonction. Développez les **journaux d’activité** en bas de la page et vérifiez que la diffusion en continu des journaux d’activité n’est pas suspendue.

2. Ouvrez l’Explorateur Stockage et connectez-vous au compte de stockage créé au début de cette section.

3. Développez le compte de stockage, les **conteneurs d’objets blob** et l’objet blob que vous avez créé précédemment. Sélectionnez **Charger**, puis **Charger des fichiers**.

   ![Chargement d’un fichier dans le conteneur d’objets blob.](media/azure-stack-solution-staged-data-analytics/image12.png)

4. Dans la boîte de dialogue Charger des fichiers, cliquez sur le champ Fichiers. Accédez à un fichier sur un ordinateur local, par exemple un fichier image, sélectionnez-le, puis sélectionnez **Ouvrir** et **Charger**.

5. Revenez aux journaux d’activité de fonction et vérifiez que l’objet blob a été lu.

   **Exemple :**

   ![Affichez le message dans les journaux d’activité.](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="securely-stored-and-accessed-compliant-data"></a>Données conformes stockées et accessibles en toute sécurité

Les données d’entreprise globales sont stockées, traitées, distribuées et accessibles en toute sécurité via Azure et Azure Stack Staged Data Analytics, et suivant les directives de point de terminaison personnalisées. Les activités des machines et des employés des bureaux distants, les données de site et les métriques métier sont continuellement compilées, stockées, testées en matière de conformité et distribuées conformément à la stratégie de l’entreprise et aux réglementations régionales.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les modèles Azure Cloud, consultez [Modèles de conception cloud](https://docs.microsoft.com/azure/architecture/patterns).
