---
title: Configurer la collecte automatique des journaux Azure Stack | Microsoft Docs
description: Découvrez comment configurer la collecte automatique des journaux dans Azure Stack Aide et support.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: efab23f12086fee2e4f5c14a70f95717ac9669b9
ms.sourcegitcommit: b752f4e6733d9ebe56dbd171a14528dcb9a693fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68522057"
---
# <a name="configure-automatic-azure-stack-diagnostic-log-collection"></a>Configurer la collecte automatique des journaux de diagnostic Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Nous vous recommandons de configurer la fonctionnalité de collecte automatique des journaux de diagnostic afin de simplifier la collecte des journaux et l’expérience du support technique. Si les conditions d’intégrité du système doivent être examinées, les journaux peuvent être automatiquement téléchargés à des fins d'analyse par les services de support technique Microsoft (CSS). 

## <a name="create-an-azure-blob-container-sas-url"></a>Créer une URL SAP de conteneur d'objets blob Azure 

Avant de configurer la collecte automatique des journaux, vous devez obtenir une signature d’accès partagé (SAP) pour un conteneur d’objets blob. Une signature d’accès partagé vous permet d'accorder l’accès aux ressources dans votre compte de stockage sans partager les clés de votre compte. Vous pouvez enregistrer les fichiers journaux Azure Stack dans un conteneur d’objets blob dans Azure, puis fournir l’URL SAR dans laquelle CSS peut collecter les journaux. 

### <a name="prerequisites"></a>Prérequis

Vous pouvez utiliser un conteneur d’objets blob nouveau ou existant dans Azure. Pour créer un conteneur d’objets blob dans Azure, vous devez disposer au minimum du [rôle de contributeur de stockage d'objets blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) ou de l'[autorisation spécifique](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). Les administrateurs généraux disposent également de l’autorisation nécessaire. 

Pour connaître les meilleures pratiques en matière de choix des paramètres de compte de stockage de collecte de journaux, consultez [Meilleures pratiques pour la collecte automatique des journaux Azure Stack](azure-stack-best-practices-automatic-diagnostic-log-collection.md). Pour plus d’informations sur les types de comptes de stockage, consultez [Vue d’ensemble des comptes de stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

### <a name="create-a-blob-storage-account"></a>Création d’un compte de stockage d’objets blob
 
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Cliquez sur **Compte de stockage** > **Ajouter**. 
1. Créez un conteneur d’objets blob avec les paramètres suivants :
   - **Abonnement**: Choisissez votre abonnement Azure.
   - **Groupe de ressources** : spécifiez un groupe de ressources.
   - **Nom du compte de stockage** : spécifiez un nom de compte de stockage unique.
   - **Emplacement** : sélectionnez un centre de données conformément à la stratégie de votre entreprise.
   - **Performances** : sélectionnez Standard.
   - **Type de compte** Sélectionnez StorageV2 (usage général v2). 
   - **Réplication** : sélectionnez Stockage localement redondant (LRS)
   - **Niveau d’accès** : sélectionnez Froid.

   ![Capture d’écran montrant les propriétés du conteneur d’objets blob](media/azure-stack-automatic-log-collection/azure-stack-log-collection-create-storage-account.png)

1. Cliquez sur **Vérifier + créer**, puis sur **Créer**.  

### <a name="create-a-blob-container"></a>Création d’un conteneur d’objets blob 

1. Une fois le déploiement réussi, cliquez sur **Accéder à la ressource**. Vous pouvez également épingler le compte de stockage au tableau de bord afin d'y accéder facilement. 
1. Cliquez sur **Explorateur Stockage (préversion)** , cliquez avec le bouton droit sur **Conteneurs d'objets blob**, puis cliquez sur **Créer un conteneur d’objets blob**. 
1. Entrez un nom pour le conteneur, puis cliquez sur **OK**.

## <a name="create-a-sas-url"></a>Créer un URL SAP

1. Cliquez avec le bouton droit sur le conteneur, puis cliquez sur **Obtenir une signature d’accès partagé**. 
   
   ![Capture d’écran montrant comment obtenir la signature d’accès partagé d’un conteneur d’objets blob](media/azure-stack-automatic-log-collection/get-sas.png)

1. Sélectionnez les propriétés suivantes :
   - Heure de début : vous pouvez reculer l'heure de début 
   - Heure d’expiration : deux ans
   - Fuseau horaire : UTC
   - Autorisations : lecture, écriture et énumération

   ![Capture d’écran montrant les propriétés de signature d’accès partagé](media/azure-stack-automatic-log-collection/sas-properties.png) 

1. Cliquez sur **Créer**.  

Copiez l’URL et entrez-la lorsque vous [configurez la collecte automatique des journaux](azure-stack-configure-automatic-diagnostic-log-collection.md). Pour plus d’informations sur les URL SAP, consultez [Utilisation des signatures d’accès partagé (SAP)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1). 


## <a name="steps-to-configure-automatic-log-collection"></a>Procédure de configuration de la collecte automatique des journaux

Pour ajouter l'URL SAP à l’interface utilisateur de collecte des journaux, procédez comme suit : 

1. Connectez-vous au portail d’administration d’Azure Stack.
1. Ouvrez **Aide et support - Vue d'ensemble**.
1. Cliquez sur **Paramètres de collecte automatique**.

   ![Capture d’écran montrant où activer la collecte des journaux dans Aide et support](media/azure-stack-automatic-log-collection/azure-stack-automatic-log-collection.png)

1. Définissez la collecte automatique des journaux sur **Activé**.
1. Entrez l’URL de signature d’accès partagé (SAP) du conteneur d’objets blob du compte de stockage.

   ![Capture d’écran montrant l’URL SAP blob](media/azure-stack-automatic-log-collection/azure-stack-enable-automatic-log-collection.png)

>[!NOTE]
>La collecte automatique des journaux peut être désactivée et réactivée à tout moment. La configuration de l’URL SAP ne change pas. Si la collecte automatique des journaux est réactivée, l’URL SAP entrée précédemment est soumise aux mêmes vérifications de validation et une URL SAP expirée sera rejetée. 


## <a name="view-log-collection"></a>Afficher la collecte des journaux

L’historique des journaux collectés à partir d'Azure Stack s'affiche sur la page **Collecte de journaux** dans Aide et support, avec les dates et heures suivantes :

- **Heure de collecte** : heure à laquelle l'opération de collecte des journaux a commencé 
- **Date de début** : date à laquelle vous souhaitez commencer à collecter des journaux
- **Date de fin** : date de fin de la collecte

![Capture d’écran montrant les collectes de journaux](media/azure-stack-automatic-log-collection/azure-stack-log-collection.png)

Si la collecte des journaux de diagnostic échoue, vérifiez que l’URL SAP est valide. Si le problème persiste ou si vous constatez plusieurs échecs, contactez Microsoft CSS pour obtenir de l’aide. 

Les opérateurs peuvent également vérifier les journaux collectés automatiquement dans le compte de stockage. Par exemple, cette capture d’écran montre les collectes de journaux à l’aide de l’Explorateur Stockage (préversion) dans le portail Azure :

![Capture d’écran montrant les collectes de journaux](media/azure-stack-automatic-log-collection/check-storage-account.png)


## <a name="see-also"></a>Voir aussi

[Gestion des données client et des journaux Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

[Utilisation des signatures d’accès partagé (SAP)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

[Meilleures pratiques pour la collecte automatique des journaux Azure Stack](azure-stack-best-practices-automatic-diagnostic-log-collection.md)





