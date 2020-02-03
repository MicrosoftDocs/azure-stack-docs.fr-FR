---
title: Gérer l’utilisation et la facturation d’Azure Stack Hub en tant que fournisseur de solutions cloud
description: Découvrez comment inscrire Azure Stack Hub en tant fournisseur de solutions cloud, et ajouter des clients pour la facturation.
author: sethmanheim
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 131a3e0cacc1d08df6f2f925845d8dbf21386162
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76876556"
---
# <a name="manage-usage-and-billing-for-azure-stack-hub-as-a-cloud-solution-provider"></a>Gérer l’utilisation et la facturation d’Azure Stack Hub en tant que fournisseur de solutions cloud

Cet article décrit comment inscrire Azure Stack Hub en tant que fournisseur de solutions cloud (CSP) et comment ajouter des clients.

En tant que fournisseur de services cloud, vous travaillez avec différents clients qui utilisent votre infrastructure Azure Stack Hub. Chaque client possède un abonnement de fournisseur de services cloud dans Azure. Vous devez diriger l’utilisation de votre infrastructure Azure Stack Hub vers chaque abonnement d’utilisateur.

La figure suivante illustre les étapes nécessaires pour choisir votre compte de services partagés et inscrire le compte Azure auprès du compte Azure Stack Hub. Une fois l’inscription effectuée, vous pouvez embarquer vos clients finaux :

[![Processus d’activation de l’utilisation et de la gestion en tant que fournisseur de services cloud](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "Processus d’activation de l’utilisation et de la gestion en tant que fournisseur de services cloud")](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>Créer un abonnement CSP ou APSS

### <a name="csp-subscription-types"></a>Types d'abonnement CSP

Choisissez le type de compte de services partagés que vous utilisez pour Azure Stack Hub. Les types d’abonnements pouvant être utilisés pour l’inscription d’un système Azure Stack Hub mutualisé sont les suivants :

- Fournisseur de solutions cloud
- Abonnement Partner Shared Services

#### <a name="azure-partner-shared-services"></a>Azure Partner Shared Services

Les abonnements APSS (Azure Partner Shared Services) sont le choix privilégié pour l’inscription quand un distributeur de CSP ou un CSP direct exploite Azure Stack Hub.

Les abonnements APSS sont associés à un locataire de services partagés. Quand vous inscrivez Azure Stack Hub, vous fournissez les informations d’identification d’un compte propriétaire de l’abonnement. Le compte que vous utilisez pour inscrire Azure Stack Hub peut être différent du compte administrateur que vous utilisez pour le déploiement. En outre, les deux comptes n’ont pas besoin d’appartenir au même domaine ; vous pouvez déployer à l’aide du locataire que vous utilisez déjà. Par exemple, vous pouvez utiliser `ContosoCSP.onmicrosoft.com`, puis vous inscrire à l’aide d’un autre locataire ; par exemple, `IURContosoCSP.onmicrosoft.com`. Vous devez penser à vous connecter à l’aide de `ContosoCSP.onmicrosoft.com` lors de l’administration quotidienne d’Azure Stack Hub. Vous vous connectez à Azure en utilisant `IURContosoCSP.onmicrosoft.com` quand vous devez effectuer des opérations d’inscription.

Pour obtenir une description des abonnements APSS et de leur création, consultez [Ajouter Azure Partner Shared Services](/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>Abonnements CSP

Les abonnements CSP sont le choix privilégié pour l’inscription quand un revendeur de CSP ou un client final exploite Azure Stack Hub.

## <a name="register-azure-stack-hub"></a>Inscrire Azure Stack Hub

Utilisez l’abonnement APSS créé en suivant les informations contenues dans la section précédente pour inscrire Azure Stack Hub dans Azure. Pour plus d’informations, consultez [Inscrire Azure Stack Hub dans votre abonnement Azure](azure-stack-registration.md).

## <a name="add-end-customer"></a>Ajouter un client final

Pour configurer Azure Stack Hub afin que l’utilisation des ressources d’un nouveau locataire soit signalée à son abonnement CSP, voir [Ajouter un locataire pour l’utilisation et la facturation sur Azure Stack Hub](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Facturer les abonnements appropriés

Azure Stack Hub utilise une fonctionnalité appelée *inscription*. Il s’agit d’un objet stocké dans Azure. L’objet d’inscription documente les abonnements Azure à utiliser pour facturer une infrastructure Azure Stack Hub donnée. Cette section traite de l’importance de l’inscription.

L’inscription permet à Azure Stack Hub d’effectuer les opérations suivantes :

- transférer des [données d’utilisation d’Azure Stack Hub](azure-stack-billing-and-chargeback.md) au serveur de commerce Azure, et facturer un abonnement Azure ;
- signaler l’utilisation de chaque client sur un autre abonnement avec un déploiement Azure Stack Hub mutualisé. La mutualisation permet à Azure Stack Hub de prendre en charge différentes organisations sur la même instance Azure Stack Hub.

Pour chaque Azure Stack Hub, il existe un seul abonnement par défaut et de nombreux abonnements de locataires. L’abonnement par défaut est un abonnement Azure qui est facturé s’il n’existe aucun abonnement spécifique au locataire. Il doit être le premier abonnement inscrit. Pour que la génération de rapports sur l’utilisation de plusieurs locataires fonctionne, l’abonnement doit être un abonnement CSP ou APSS.

L’inscription est alors mise à jour avec un abonnement Azure pour chaque locataire utilisant Azure Stack Hub. Les abonnements de locataires doivent être de type CSP et doivent être associés au partenaire qui possède l’abonnement par défaut. Vous ne pouvez pas inscrire les clients d’une autre personne.

Quand Azure Stack Hub transfère des informations d’utilisation à Azure global, un service d’Azure consulte l’inscription et mappe l’utilisation de chaque locataire à l’abonnement du locataire approprié. Si un locataire n’a pas été inscrit, cette utilisation est placée dans l’abonnement par défaut de l’instance Azure Stack Hub dont elle provient.

Étant donné que les abonnements de locataire sont des abonnements CSP, leur facture est envoyée au partenaire CSP et les informations d’utilisation ne sont pas visibles par le client final.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le programme CSP, consultez [Programme des fournisseurs de solutions cloud](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Pour en savoir plus sur la récupération d’informations sur utilisation des ressources à partir d’Azure Stack Hub, voir [Utilisation et facturation dans Azure Stack Hub](azure-stack-billing-and-chargeback.md).
