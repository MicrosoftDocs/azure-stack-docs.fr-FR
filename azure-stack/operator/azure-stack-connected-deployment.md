---
title: Décisions de déploiement connecté à Azure pour les systèmes intégrés Azure Stack Hub | Microsoft Docs
description: Prenez des décisions de planification pour des déploiements connectés à Azure de systèmes intégrés Azure Stack Hub, notamment concernant la facturation et l’identité.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: c5889e9382f41f03d06d30faf056599f8f07e69a
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76534020"
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-hub-integrated-systems"></a>Décisions de la planification de déploiement connecté à Azure de systèmes intégrés Azure Stack Hub
Une fois que vous avez décidé [comment intégrer Azure Stack Hub dans votre environnement de cloud hybride](azure-stack-connection-models.md), vous pouvez finaliser vos décisions de déploiement Azure Stack Hub.

Un déploiement d’Azure Stack Hub connecté à Azure signifie que vous pouvez avoir Azure Active Directory (Azure AD) ou les services de fédération Active Directory (AD FS) comme magasin d’identités. Vous pouvez également choisir entre un modèle de facturation à l’utilisation ou un modèle de facturation selon la capacité. L’option par défaut est un déploiement connecté, car il permet aux clients de tirer le meilleur parti d’Azure Stack Hub, en particulier pour des scénarios de cloud hybride impliquant tant Azure qu’Azure Stack Hub.

## <a name="choose-an-identity-store"></a>Choisir un magasin d’identités
Dans le cas d’un déploiement connecté, vous pouvez choisir Azure AD ou AD FS comme magasin d’identités. Dans le cas d’un déploiement déconnecté, sans connexion Internet, vous ne pouvez utiliser que AD FS.

Le choix du magasin d’identités n’a aucune incidence sur les machines virtuelles de locataire. Les machines virtuelles de locataire peuvent choisir le magasin d’identités auquel elles seront connectées en fonction de leur configuration : Azure AD, magasin joint à un domaine Windows Server Active Directory, groupe de travail, etc. Cela n’a aucun lien avec la décision du fournisseur d’identité Azure Stack Hub.

Par exemple, vous pouvez toujours déployer des machines virtuelles de locataire IaaS en plus d’Azure Stack Hub, et les joindre à un domaine Active Directory d’entreprise afin d’utiliser des comptes à partir de là. Vous n’êtes pas obligé d’utiliser le magasin d’identités Azure AD que vous sélectionnez ici pour ces comptes.

### <a name="azure-ad-identity-store"></a>Magasin d’identités Azure AD
Lorsque vous utilisez Azure AD comme magasin d’identités, vous avez besoin de deux comptes Azure AD : un compte administrateur général et un compte de facturation. Ces comptes peuvent être les mêmes comptes ou des comptes distincts. S’il est plus facile et pratique d’utiliser le même compte d’utilisateur lorsque vous disposez d’un nombre limité de comptes Azure, il sera parfois nécessaire d’utiliser deux comptes pour répondre aux besoins de votre entreprise :

1. **Compte administrateur général** (requis uniquement pour les déploiements connectés). Ce compte Azure est utilisé pour créer des applications et principaux de service pour les services d’infrastructure Azure Stack Hub dans Azure AD. Ce compte doit disposer d’autorisations d’administrateur sur l’annuaire dans lequel vous allez déployer votre système Azure Stack Hub. Il deviendra l’administrateur général de l’opérateur cloud pour l’utilisateur Azure AD et sera utilisé dans le cadre des tâches suivantes :

    - Approvisionner et déléguer des applications et principaux de service pour tous les services Azure Stack Hub qui interagissent avec Azure AD et l’API Graph.
    - Comme compte de l’administrateur de service. Ce compte est celui du propriétaire de l’abonnement du fournisseur par défaut (vous pouvez modifier ce paramètre ultérieurement). Vous pouvez vous connecter au portail d’administration Azure Stack Hub à l’aide de ce compte, et l’utiliser pour créer des offres et des plans, définir des quotas et effectuer d’autres fonctions d’administration dans Azure Stack Hub.

2. **Compte de facturation** (requis pour les déploiements connectés et déconnectés). Ce compte Azure est utilisé pour établir la relation de facturation entre votre système intégré Azure Stack Hub et le serveur principal de commerce Azure. Il s’agit du compte auquel sont facturés les frais relatifs à Azure Stack Hub. Ce compte sera également utilisé pour offrir des éléments dans le Marketplace et d’autres scénarios hybrides.

### <a name="ad-fs-identity-store"></a>Magasin d’identités AD FS
Choisissez cette option si vous souhaitez utiliser votre propre magasin d’identités, tel que votre instance Active Directory d’entreprise, pour vos comptes d’administrateur de service.  

## <a name="choose-a-billing-model"></a>Choisir un modèle de facturation
Vous pouvez choisir un modèle de facturation **à l’utilisation** ou **selon la capacité**. Les déploiements de modèles de facturation à l’utilisation doivent pouvoir rendre compte de l’utilisation des rapports via une connexion à Azure au moins une fois tous les 30 jours. Par conséquent, le modèle de facturation à l’utilisation est uniquement disponible pour des déploiements connectés.  

### <a name="pay-as-you-use"></a>Facturation à l’utilisation
Avec le modèle de facturation à l’utilisation, les ressources utilisées sont facturées sur un abonnement Azure. Vous payez uniquement lorsque vous utilisez les services Azure Stack Hub. Si vous choisissez ce modèle, vous aurez besoin d’un abonnement Azure et de l’ID de compte associé à cet abonnement (par exemple, serviceadmin@contoso.onmicrosoft.com). Les abonnements EA, CSP et CSL sont pris en charge. Le rapport d’utilisation est configuré lors de [l’inscription d’Azure Stack Hub](azure-stack-registration.md).

> [!NOTE]
> Dans la plupart des cas, les entreprises utiliseront des abonnements EA et les fournisseurs de services utiliseront des abonnements CSP ou CSL.

Si vous vous apprêtez à utiliser un abonnement CSP, consultez le tableau ci-dessous pour savoir quel abonnement CSP utiliser en fonction de votre situation :

|Scénario|Domaine et options d'abonnement|
|-----|-----|
|Vous êtes un **Partenaire de CSP direct** ou un **Fournisseur de CSP indirect**, et vous allez exploiter l’Azure Stack Hub|Utilisez un abonnement CSL (Common Service Layer).<br>     or<br>Créez un locataire Azure AD avec un nom descriptif dans l’Espace partenaires. Par exemple, &lt;votre organisation>CSPAdmin avec un abonnement Azure CSP associé.|
|Vous êtes un **Revendeur de CSP indirect** et allez exploiter l’Azure Stack Hub.|Demandez à votre fournisseur CSP indirect de créer un locataire Azure AD pour votre organisation avec un abonnement CSP Azure associé à l’aide de l’Espace partenaires.|

### <a name="capacity-based-billing"></a>Facturation selon la capacité
Si vous décidez d’utiliser le modèle de facturation de capacité, vous devez acheter une référence (SKU) de plan de capacité Azure Stack Hub correspondant à la capacité de votre système. Pour acheter la quantité appropriée, vous devez connaître le nombre de cœurs physiques composant votre infrastructure Azure Stack Hub.

La facturation selon la capacité nécessite un abonnement Azure EA (Enterprise Agreement) à des fins d’inscription. La raison est que l’inscription configure la disponibilité des éléments dans le Marketplace, ce qui nécessite un abonnement Azure. L’abonnement n’est pas utilisé pour Azure Stack Hub.

## <a name="learn-more"></a>En savoir plus
- Pour plus d’informations sur les cas d’utilisation, l’achat, les partenaires et les fabricants de matériel OEM, voir la page du produit [Azure Stack Hub](https://azure.microsoft.com/overview/azure-stack/).
- Pour plus d’informations sur la feuille de route et la disponibilité géographique des systèmes intégrés Azure Stack Hub, voir le livre blanc : [Azure Stack Hub: An extension of Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Pour plus d’informations sur l’empaquetage et les tarifs de Microsoft Azure Stack Hub, [téléchargez le fichier PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Étapes suivantes
[Intégration au réseau du centre de données](azure-stack-network.md)