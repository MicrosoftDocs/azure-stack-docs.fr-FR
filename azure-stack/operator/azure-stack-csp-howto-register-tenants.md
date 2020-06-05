---
title: Ajouter des locataires pour l’utilisation et la facturation sur Azure Stack Hub
description: Découvrez comment ajouter un locataire pour l’utilisation et la facturation sur Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 5/28/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 5/28/2020
ms.openlocfilehash: 08185a25c608c735aa99ca7f7d2b060c8b67042b
ms.sourcegitcommit: 804f94f288859027b8249d138b14e8bc1501e009
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84158382"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack-hub"></a>Ajouter un locataire pour l’utilisation et la facturation sur Azure Stack Hub

Cet article explique comment ajouter un locataire à un déploiement Azure Stack Hub géré par un fournisseur de solutions cloud (CSP). Quand le nouveau locataire utilise des ressources, Azure Stack Hub signale l’utilisation à son abonnement de fournisseur de services cloud.

Les fournisseurs de services cloud proposent souvent des services à plusieurs clients finaux (locataires) sur leur déploiement Azure Stack Hub. L’ajout de locataires à l’inscription Azure Stack Hub garantit que l’utilisation de chaque locataire sera signalée et facturée sur l’abonnement de fournisseur de services cloud correspondant. Si vous n’effectuez pas les étapes décrites dans cet article, l’utilisation du locataire est facturée pour l’abonnement utilisé dans l’inscription initiale d’Azure Stack Hub. Avant d'ajouter un client final à Azure Stack Hub pour suivre l’utilisation et gérer son locataire, vous devez configurer Azure Stack Hub en tant que fournisseur de services cloud. Pour les étapes et les ressources, consultez [Gérer l’utilisation et la facturation pour Azure Stack Hub comme fournisseur de solutions cloud](azure-stack-add-manage-billing-as-a-csp.md).

La figure suivante illustre les étapes qu’un fournisseur de services cloud doit suivre pour permettre à un nouveau client final d’utiliser Azure Stack Hub, et configurer le suivi de l’utilisation pour le client. En ajoutant le client final, vous pouvez également gérer les ressources dans Azure Stack Hub. Deux options s’offrent à vous pour gérer ses ressources :

- Vous pouvez conserver le client final et fournir les informations d’identification de l’abonnement Azure Stack Hub local au client final.  
- L’utilisateur final peut travailler avec son abonnement en local et ajouter le fournisseur de services cloud en tant qu’invité avec des autorisations de propriétaire.

## <a name="add-an-end-customer"></a>Ajouter un client final

Avant d'ajouter un client final, vous devez activer la facturation multi-locataires sur votre inscription. Pour activer la facturation multi-locataires, envoyez l'ID d'abonnement d'inscription, le nom du groupe de ressources et le nom de l’inscription à `azstcsp@microsoft.com`. Comptez généralement 1 à 2 jours ouvrables pour que l’architecture multilocataire se mette en place.

Procédez comme suit pour ajouter un client final, comme illustré dans la figure suivante :

![Configurer le fournisseur de solutions cloud pour le suivi de l’utilisation et pour gérer le compte du client final](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

### <a name="create-a-new-customer-in-partner-center"></a>Créer un client dans l’Espace partenaires

Dans l’Espace partenaires, créez un abonnement Azure pour le client. Pour obtenir des instructions, consultez [Ajouter un nouveau client](/partner-center/add-a-new-customer).

### <a name="create-an-azure-subscription-for-the-end-customer"></a>Créer un abonnement Azure pour le client final

Une fois que vous avez créé un enregistrement de votre client dans l’Espace partenaires, vous pouvez lui vendre des abonnements à des produits dans le catalogue. Pour obtenir des instructions, consultez [Créer un abonnement](/partner-center/create-a-new-subscription).

### <a name="create-a-guest-user-in-the-end-customer-directory"></a>Créer un utilisateur invité dans le répertoire de client final

Par défaut, en tant que CSP, vous n’avez pas accès à l’abonnement Azure Stack Hub du client final. Toutefois, si votre client souhaite vous permettre de gérer ses ressources, il peut ajouter votre compte en tant que propriétaire/contributeur à son abonnement Azure Stack Hub. Pour ce faire, il doit ajouter votre compte en tant qu’utilisateur invité à son locataire Azure AD. Si possible, utilisez un compte différent de votre compte Azure CSP pour gérer l’abonnement Azure Stack Hub de votre client. Cela vous garantit de toujours conserver l’accès à l’abonnement Azure de votre client.

### <a name="update-the-registration-with-the-end-customer-subscription"></a>Mettre à jour l’inscription avec l’abonnement du client final

Mettez à jour votre inscription avec l’abonnement de nouveau client. Azure signale l’utilisation du client à l’aide de l’identité du client dans l’Espace partenaires. Cette étape garantit que l’utilisation de chaque client est signalée dans l’abonnement de fournisseur de services cloud individuel de ce client. Cela facilite le suivi de l’utilisation et de la facturation. Pour effectuer cette étape, vous devez d’abord [inscrire Azure Stack Hub](azure-stack-registration.md).

1. Ouvrez Windows PowerShell dans une invite de commandes avec élévation des privilèges, puis exécutez la commande suivante :  

   ```powershell
   Add-AzureRmAccount
   ```

   >[!NOTE]
   > Si votre session expire, si votre mot de passe a changé ou si vous souhaitez simplement changer de compte, exécutez l’applet de commande suivante avant de vous connecter en utilisant **Add-AzureRmAccount** : `Remove-AzureRmAccount-Scope Process`.

2. Entrez vos informations d’identification Azure.
3. Dans la session PowerShell, exécutez :

   ```powershell
   New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
   ```

### <a name="new-azurermresource-powershell-parameters"></a>Paramètres PowerShell New-AzureRmResource

La section suivante décrit les paramètres de la cmdlet **New-AzureRmResource** :

| Paramètre | Description |
| --- | --- |
|registrationSubscriptionID | L’abonnement Azure qui était utilisé au moment de l’inscription initiale d’Azure Stack Hub.|
| customerSubscriptionID | L’abonnement Azure (pas Azure Stack Hub) appartenant au client à inscrire. Doit être créé dans l'offre CSP. En pratique, cela veut dire via l’Espace partenaires. Si un client dispose de plus d’un locataire Azure Active Directory, cet abonnement doit être créé dans le locataire qui sera utilisé pour se connecter à Azure Stack Hub. L’ID de l’abonnement client est sensible à la casse. |
| resourceGroup | Le groupe de ressources Azure dans lequel est stockée votre inscription. |
| registrationName | Le nom de l’inscription de votre compte Azure Stack Hub. Il s’agit d’un objet stocké dans Azure. 

> [!NOTE]  
> Les locataires doivent être inscrits auprès de chaque Azure Stack Hub qu’ils utilisent. Si vous avez deux déploiements Azure Stack Hub utilisés par un client, vous devez mettre à jour les inscriptions initiales de chaque déploiement avec l’abonnement du locataire.

### <a name="onboard-tenant-to-azure-stack-hub"></a>Intégrer le locataire à Azure Stack Hub

Configurez Azure Stack Hub pour prendre en charge des utilisateurs de plusieurs locataires Azure AD pour utiliser les services dans Azure Stack Hub. Pour obtenir des instructions, consultez [Activer l’architecture multilocataire dans Azure Stack Hub](azure-stack-enable-multitenancy.md).

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack-hub"></a>Créer une ressource locale dans le locataire de client final dans Azure Stack Hub

Une fois que vous avez ajouté le nouveau client à Azure Stack Hub, ou que le locataire de client final a activé votre compte invité doté des privilèges de propriétaire, vérifiez que vous pouvez créer une ressource dans son locataire. Par exemple, il peut [Créer une machine virtuelle Windows avec le portail Azure Stack Hub](../user/azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour consulter les messages d’erreur s’ils sont déclenchés dans votre processus d’inscription, consultez [Code d’erreur d’utilisation et de facturation](azure-stack-registration-errors.md).
- Pour en savoir plus sur la récupération d’informations d’utilisation de ressources à partir d’Azure Stack Hub, consultez [Utilisation et facturation dans Azure Stack Hub](azure-stack-billing-and-chargeback.md).
- Pour vérifier comment un client final peut vous ajouter en qualité de CSP, en tant que responsable pour son locataire Azure Stack Hub, consultez [Autoriser un fournisseur de solutions cloud à gérer votre abonnement Azure Stack Hub](../user/azure-stack-csp-enable-billing-usage-tracking.md).
