---
title: Codes d’erreur d’inscription de l’utilisation et de la facturation dans Azure Stack Hub
description: Découvrez les codes d’erreur d’inscription de l’utilisation et de la facturation dans Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 12/15/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: b229d4019e6e7ed99826e311fc62da341c1203dd
ms.sourcegitcommit: a53ea4a28e715c80a99fa89e9d364bc4556558de
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97576936"
---
# <a name="usage-and-billing-registration-error-codes"></a>Codes d’erreur d’inscription d’utilisation et de facturation

Si vous êtes un fournisseur de solutions Cloud (CSP), les messages d’erreur suivants peuvent s’afficher lors de l’[ajout de locataires](azure-stack-csp-ref-operations.md#add-tenant-to-registration) à une inscription afin de rendre compte de l’utilisation par rapport à l’ID d’abonnement Azure du client.

## <a name="list-of-registration-error-codes"></a>Liste des codes d’erreur d’inscription

| Error   | Détails  | Commentaires  |
|---|---|---|
| RegistrationNotFound | L’inscription fournie est introuvable. Vérifiez que les informations suivantes ont été fournies correctement :<br>1. Identificateur d’abonnement (valeur fournie : **identificateur de l’abonnement**),<br>2. Groupe de ressources (valeur fournie : **groupe de ressources**),<br>3. Nom d’inscription (valeur fournie : **nom de l’inscription**). | Cette erreur se produit généralement quand les informations pointant vers l’inscription initiale ne sont pas correctes. Pour vérifier le groupe de ressources et le nom de votre inscription, consultez le portail Azure, en répertoriant toutes les ressources. Si vous trouvez plusieurs ressources d’inscription, examinez la valeur **CloudDeploymentID** dans les propriétés, puis sélectionnez l’inscription dont la valeur **CloudDeploymentID** correspond à celle de votre cloud. Pour trouver la valeur **CloudDeploymentID**, vous pouvez utiliser la commande PowerShell suivante sur Azure Stack Hub :<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| BadCustomerSubscriptionId | L’**identificateur d’abonnement du client** et l’identificateur d’abonnement du **nom d’inscription** fournis ne sont pas détenus par le même fournisseur de solutions Cloud Microsoft. Vérifiez que l’identificateur d’abonnement du client est correct. L’ID de l’abonnement client est sensible à la casse. Si le problème persiste, contactez le support technique. | Cette erreur survient quand l’abonnement du client est un abonnement CSP, mais qu’il est associé à un partenaire CSP différent de celui auquel est associé l’abonnement utilisé pour l’inscription initiale. Cette vérification est effectuée afin d’éviter une situation qui entraînerait la facturation d’un partenaire CSP non responsable de l’environnement Azure Stack Hub utilisé. |
| InvalidCustomerSubscriptionId  | La valeur de l’**identificateur d’abonnement du client** n’est pas valide. Vérifiez qu’un abonnement Azure valide est fourni. |   |
| CustomerSubscriptionNotFound  | L’**identificateur d’abonnement du client** est introuvable sous le **nom d’inscription**. Vérifiez qu’un abonnement Azure valide est utilisé et que l’identificateur d’abonnement a été ajouté à l’inscription à l’aide de l’opération PUT. | Cette erreur se produit quand vous tentez de vérifier qu’un locataire a été ajouté à un abonnement et que l’abonnement du client est introuvable pour être associé à l’inscription. Le client n’a pas été ajouté à l’inscription, ou l’ID de l’abonnement a été écrit de manière incorrecte. |
| UnauthorizedCspRegistration | Le **nom d’inscription** fourni n’est pas approuvé pour utiliser la multilocation. Envoyez un e-mail à azstCSP@microsoft.com et incluez le nom, le groupe de ressources et l’identificateur d’abonnement de votre inscription utilisés pour l’inscription. | Une inscription doit être approuvée pour la multi-location par Microsoft avant que vous puissiez y ajouter des locataires. |
| CustomerSubscriptionsNotAllowed | Les opérations d’abonnement de client ne sont pas prises en charge pour les clients déconnectés. Pour utiliser cette fonctionnalité, réinscrivez-vous avec une licence de paiement à l’utilisation. | L’inscription à laquelle vous tentez d’ajouter des locataires est une inscription de capacité. Par conséquent, lorsque l’inscription a été créée, le paramètre `BillingModel Capacity` a été utilisé. Seules les inscriptions avec paiement à l’utilisation sont autorisées à ajouter des locataires. Vous devez vous réinscrire en utilisant le paramètre `BillingModel PayAsYouUse`. |
| InvalidCSPSubscription | L’**identificateur d’abonnement du client** fourni n’est pas un abonnement CSP valide. Vérifiez qu’un abonnement Azure valide est fourni. | Cette erreur est probablement due à une saisie incorrecte de l’abonnement du client. |
| MetadataResolverBadGatewayError | Un des serveurs en amont a renvoyé une erreur inattendue. Réessayez plus tard. Si le problème persiste, contactez le support technique. |

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur l’[Infrastructure de rapports d’utilisation pour les fournisseurs de solutions cloud](azure-stack-csp-ref-infrastructure.md).
- Pour en savoir plus sur le programme CSP, consultez la page [Solutions cloud](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Pour en savoir plus sur la récupération d’informations d’utilisation de ressources à partir d’Azure Stack Hub, consultez [Utilisation et facturation dans Azure Stack Hub](azure-stack-billing-and-chargeback.md).
