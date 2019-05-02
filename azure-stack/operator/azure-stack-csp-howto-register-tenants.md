---
title: Ajouter des locataires pour l’utilisation et la facturation sur Azure Stack | Microsoft Docs
description: Les étapes requises permettent d’ajouter un utilisateur final à Azure Stack géré par un fournisseur de services cloud.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/05/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: alfredop
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 8e177944a5f57c9475287325b705fac34ec513c0
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307823"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Ajouter un locataire pour l’utilisation et la facturation sur Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit les étapes requises pour ajouter un utilisateur final à un déploiement Azure Stack géré par un fournisseur de services cloud. Quand le nouveau locataire utilise des ressources, Azure Stack signale l’utilisation à son abonnement de fournisseur de services cloud.

Les fournisseurs de services cloud proposent souvent des services à plusieurs clients finaux (locataires) sur leur déploiement Azure Stack. L’ajout de locataires à l’inscription Azure Stack garantit que l’utilisation de chaque locataire sera signalée et facturée sur l’abonnement de fournisseur de services cloud correspondant. Si vous n’effectuez pas les étapes décrites dans cet article, l’utilisation du locataire est facturée pour l’abonnement utilisé dans l’inscription initiale d’Azure Stack. Avant de pouvoir ajouter un client final à Azure Stack pour suivre l’utilisation et gérer son locataire, vous devez configurer Azure Stack en tant que fournisseur de services cloud. Pour les étapes et les ressources, consultez [Gérer l’utilisation et la facturation pour Azure Stack comme fournisseur de services cloud](azure-stack-add-manage-billing-as-a-csp.md).

La figure suivante illustre les étapes qu’un fournisseur de services cloud doit suivre pour permettre à un nouveau client d’utiliser Azure Stack et pour configurer le suivi de l’utilisation pour le client. En ajoutant le client final, vous pouvez également gérer les ressources dans Azure Stack. Deux options s’offrent à vous pour gérer ses ressources :

1. Vous pouvez conserver le client final et fournir les informations d’identification de l’abonnement Azure Stack local au client final.  
2. L’utilisateur final peut travailler avec son abonnement en local et ajouter le fournisseur de services cloud en tant qu’invité avec des autorisations de propriétaire.  

## <a name="steps-to-add-an-end-customer"></a>Étapes à suivre pour l’ajout d’un client final

![Configurer le fournisseur de services cloud pour le suivi de l’utilisation et pour gérer le compte du client final](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

### <a name="create-a-new-customer-in-partner-center"></a>Créer un client dans l’Espace partenaires

Dans l’Espace partenaires, créez un abonnement Azure pour le client. Pour obtenir des instructions, consultez [Ajouter un nouveau client](https://msdn.microsoft.com/partner-center/add-a-new-customer).

### <a name="create-an-azure-subscription-for-the-end-customer"></a>Créer un abonnement Azure pour le client final

Une fois que vous avez créé un enregistrement de votre client dans l’Espace partenaires, vous pouvez lui vendre des abonnements à des produits figurant dans le catalogue. Pour obtenir des instructions, consultez [Créer un abonnement](https://msdn.microsoft.com/partner-center/create-a-new-subscription).

### <a name="create-a-guest-user-in-the-end-customer-directory"></a>Créer un utilisateur invité dans le répertoire de client final

Si le client final gère son propre compte, créez un utilisateur invité dans son répertoire et envoyez-lui les informations. L’utilisateur final ajoute ensuite l’invité, et élève l’autorisation d’invité pour qu’elle soit définie sur **Propriétaire** pour le compte de fournisseur de services cloud Azure Stack.

### <a name="update-the-registration-with-the-end-customer-subscription"></a>Mettre à jour l’inscription avec l’abonnement du client final

Mettez à jour votre inscription avec l’abonnement de nouveau client. Azure signale l’utilisation du client à l’aide de l’identité du client dans l’Espace partenaires. Cette étape garantit que l’utilisation de chaque client est signalée dans l’abonnement de fournisseur de services cloud individuel de ce client. Cela permet de faciliter le suivi de l’utilisation de l’utilisateur et la facturation.

> [!NOTE]  
> Pour pouvoir effectuer cette étape, vous devez [avoir inscrit Azure Stack](azure-stack-registration.md ).

1. Ouvrez Windows PowerShell avec une invite élevée et exécutez :  
    `Add-AzureRmAccount -EnvironmentName AzureChinaCloud`
2. Entrez vos informations d’identification Azure.
3. Dans la session PowerShell, exécutez :

   ```powershell
   New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties <PSObject>
   ```

### <a name="new-azurermresource-powershell-parameters"></a>Paramètres PowerShell New-AzureRmResource

La section suivante décrit les paramètres de la cmdlet **New-AzureRmResource** :

| Paramètre | Description |
| --- | --- |
|registrationSubscriptionID | L’abonnement Azure qui était utilisé au moment de l’inscription initiale d’Azure Stack.|
| customerSubscriptionID | L’abonnement Azure (pas Azure Stack) appartenant au client à inscrire. Doit être créé dans l’offre de fournisseur de services cloud ; dans la pratique, cela signifie via l’Espace partenaires. Si un client dispose de plus d’un locataire Azure Active Directory, cet abonnement doit être créé dans le locataire qui sera utilisé pour se connecter à Azure Stack. L’ID d’abonnement client doit utiliser des lettres minuscules. |
| resourceGroup | Le groupe de ressources Azure dans lequel est stockée votre inscription. |
| registrationName | Le nom de l’inscription de votre compte Azure Stack. Il s’agit d’un objet stocké dans Azure. |
| properties | Spécifie les propriétés de la ressource. Utilisez ce paramètre pour spécifier les valeurs des propriétés spécifiques au type de ressource.

> [!NOTE]  
> Les locataires doivent être inscrits auprès de chaque Azure Stack qu’ils utilisent. Si vous avez deux déploiements Azure Stack utilisés par un client, vous devez mettre à jour les inscriptions initiales de chaque déploiement avec l’abonnement du locataire.

### <a name="onboard-tenant-to-azure-stack"></a>Intégrer le locataire à Azure Stack

Configurez Azure Stack pour prendre en charge des utilisateurs de plusieurs locataires Azure AD pour utiliser les services dans Azure Stack. Pour obtenir des instructions, consultez, [Activer l’architecture multilocataire dans Azure Stack](azure-stack-enable-multitenancy.md).

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Créer une ressource locale dans le locataire de client final dans Azure Stack

Une fois que vous avez ajouté le nouveau client à Azure Stack, ou que le locataire de client final a activé votre compte invité doté des privilèges de propriétaire, vérifiez que vous pouvez créer une ressource dans son locataire. Par exemple, il peut [Créer une machine virtuelle Windows avec le portail Azure Stack](../user/azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour consulter les messages d’erreur s’ils sont déclenchés dans votre processus d’inscription, consultez [Usage and billing error codes](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes) (Code d’erreur d’utilisation et de facturation).
- Pour en savoir plus sur la récupération d’informations d’utilisation de ressources à partir d’Azure Stack, consultez [Usage and billing in Azure Stack](azure-stack-billing-and-chargeback.md) (Utilisation et facturation dans Azure Stack).
- Pour vérifier comment un client final peut vous ajouter, en tant que fournisseur de services cloud, en tant que responsable pour son locataire Azure Stack, vor [Enable a Cloud Service Provider to manage your Azure Stack subscription](../user/azure-stack-csp-enable-billing-usage-tracking.md) (Autoriser un fournisseur de services cloud pour gérer votre abonnement Azure Stack).
