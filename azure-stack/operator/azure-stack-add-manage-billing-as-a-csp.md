---
title: Gérer l’utilisation et la facturation pour Azure Stack comme fournisseur de services cloud | Microsoft Docs
description: Procédure pas à pas d’inscription d’Azure Stack comme fournisseur de services cloud et d’ajout de clients pour la facturation.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 0cd8503ac57c65fb4bcf2c75367caa75bfcc2fe5
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985831"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>Gérer l’utilisation et la facturation pour Azure Stack comme fournisseur de services cloud

*S’applique à : systèmes intégrés Azure Stack*

Cet article vous guide tout au long des procédures d’inscription d’Azure Stack comme fournisseur de services cloud (CSP) et d’ajout de clients.

En tant que fournisseur de services cloud, vous travaillez avec différents clients qui utilisent votre infrastructure Azure Stack. Chaque client possède un abonnement de fournisseur de services cloud dans Azure. Vous devez diriger l’utilisation de votre infrastructure Azure Stack pour chaque abonnement d’utilisateur.

La figure suivante illustre les étapes nécessaires pour choisir votre compte de services partagés et inscrire le compte Azure auprès du compte Azure Stack. Une fois l’inscription effectuée, vous pouvez embarquer vos clients finaux :

[![Processus d’activation de l’utilisation et de la gestion en tant que fournisseur de services cloud](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "Processus d’activation de l’utilisation et de la gestion en tant que fournisseur de services cloud")](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>Créer un abonnement CSP ou APSS

### <a name="cloud-service-provider-subscription-types"></a>Types d’abonnements de fournisseur de services cloud

Choisissez le type de compte de services partagés que vous utilisez pour Azure Stack. Les types d’abonnements pouvant être utilisés pour l’inscription d’un système Azure Stack multi-locataire sont les suivants :

- Fournisseur de services cloud
- Abonnement Partner Shared Services

#### <a name="azure-partner-shared-services"></a>Azure Partner Shared Services

Les abonnements APSS (Azure Partner Shared Services) sont le choix privilégié pour l’inscription quand un serveur de distribution CSP ou CSP direct exploite Azure Stack.

Les abonnements APSS sont associés à un locataire de services partagés. Quand vous inscrivez Azure Stack, vous devez fournir les informations d’identification d’un compte qui est propriétaire de l’abonnement. Le compte que vous utilisez pour inscrire Azure Stack peut être différent du compte administrateur que vous utilisez pour le déploiement. En outre, les deux comptes n’ont pas besoin d’appartenir au même domaine ; vous pouvez déployer à l’aide du locataire que vous utilisez déjà. Par exemple, vous pouvez utiliser `ContosoCSP.onmicrosoft.com`, puis vous inscrire à l’aide d’un autre locataire ; par exemple, `IURContosoCSP.onmicrosoft.com`. Vous devez penser à vous connecter à l’aide de `ContosoCSP.onmicrosoft.com` lorsque vous effectuez des tâches d’administration Azure Stack quotidiennes. Vous vous connectez à Azure en utilisant `IURContosoCSP.onmicrosoft.com` quand vous devez effectuer des opérations d’inscription.

Pour obtenir une description des abonnements APSS et de leur création, consultez [Ajouter Azure Partner Shared Services](https://msdn.microsoft.com/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>Abonnements CSP

Les abonnements de fournisseur de services cloud (CSP) sont le choix privilégié pour l’inscription quand un revendeur CSP ou un client final exploite Azure Stack.

## <a name="register-azure-stack"></a>Inscrire Azure Stack

Utilisez l’abonnement APSS créé en suivant les informations contenues dans la section précédente pour inscrire Azure Stack auprès d’Azure. Pour plus d’informations, consultez [Inscrire Azure Stack auprès de votre abonnement Azure](azure-stack-registration.md).

## <a name="add-end-customer"></a>Ajouter un client final

Pour configurer Azure Stack de sorte que quand un nouveau locataire utilise des ressources, leur utilisation soit signalée à son abonnement de fournisseur de services cloud (CSP), consultez [Ajouter un locataire pour l’utilisation et la facturation à Azure Stack](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Facturer les abonnements appropriés

Azure Stack utilise une fonctionnalité appelée « inscription ». Il s’agit d’un objet stocké dans Azure. L’objet d’inscription documente les abonnements Azure à utiliser pour facturer une infrastructure Azure Stack donnée. Cette section traite de l’importance de l’inscription.

Grâce à l’inscription, Azure Stack peut :

- transférer les données d’utilisation d’Azure Stack à Azure Commerce et facturer un abonnement Azure ;
- signaler l’utilisation de chaque client sur un autre abonnement avec un déploiement Azure Stack multilocataire. L’architecture multi-locataire permet à Azure Stack de prendre en charge différentes organisations sur la même instance d’Azure Stack.

Pour chaque déploiement Azure Stack, il existe un seul abonnement par défaut et autant d’abonnements de locataires. L’abonnement par défaut est un abonnement Azure qui est facturé s’il n’existe aucun abonnement spécifique au locataire. Il doit être le premier abonnement inscrit. Pour que la génération de rapports sur l’utilisation de plusieurs locataires fonctionne, l’abonnement doit être un abonnement CSP ou APSS.

L’inscription est alors mise à jour avec un abonnement Azure pour chaque locataire qui utilise Azure Stack. Les abonnements de locataires doivent être de type CSP et doivent être associés au partenaire qui possède l’abonnement par défaut. Vous ne pouvez pas inscrire les clients d’une autre personne.

Quand Azure Stack transfère des informations d’utilisation à Azure global, un service d’Azure consulte l’inscription et mappe l’utilisation de chaque locataire à l’abonnement de locataire approprié. Si un locataire n’a pas été inscrit, cette utilisation est placée dans l’abonnement par défaut de l’instance Azure Stack dont elle provient.

Étant donné que les abonnements de locataire sont des abonnements CSP, leur facture est envoyée au partenaire CSP et les informations d’utilisation ne sont pas visibles par le client final.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le programme CSP, consultez [Programme des fournisseurs de solutions cloud](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Pour en savoir plus sur la récupération d’informations d’utilisation de ressources à partir d’Azure Stack, consultez [Utilisation et facturation dans Azure Stack](azure-stack-billing-and-chargeback.md).
