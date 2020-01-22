---
title: Architecture d’identité pour Azure Stack Hub | Microsoft Docs
description: Découvrez l’architecture d’identité pour Azure Stack Hub et les différences entre Azure AD et AD FS.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 952dd9d6333b917a986ce444355f3b9b694976ef
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75818075"
---
# <a name="identity-architecture-for-azure-stack-hub"></a>Architecture d’identité pour Azure Stack Hub

Quand vous choisissez un fournisseur d’identité à utiliser avec Azure Stack Hub, vous devez comprendre les différences importantes entre les options d’Azure Active Directory (Azure AD) et les services de fédération Active Directory (AD FS).

## <a name="capabilities-and-limitations"></a>Capacités et limitations

Le fournisseur d’identité que vous choisissez peut limiter vos options, dont la prise en charge d’architecture mutualisée.

|Capacité ou scénario        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Connecté à Internet     |Oui       |Facultatif|
|Prise en charge d’architecture mutualisée     |Oui       |Non      |
|Proposer des articles dans la place de marché |Oui       |Oui (nécessite l’utilisation de l’outil [Syndication de Place de marché hors ligne](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario))|
|Prise en charge de la bibliothèque d’authentification Active Directory (ADAL) |Oui |Oui|
|Prise en charge des outils tels que Azure CLI, Visual Studio et PowerShell  |Oui |Oui|
|Créer des principaux de service via le portail Azure     |Oui |Non|
|Créer des principaux de service avec des certificats      |Oui |Oui|
|Créer des principaux de service avec des secrets (clés)    |Oui |Oui|
|Les applications peuvent utiliser le service Graph           |Oui |Non|
|Les applications peuvent utiliser le fournisseur d’identité pour se connecter |Oui |Oui (les applications doivent fédérer avec vos instances AD FS locales) |

## <a name="topologies"></a>Topologies

Les sections suivantes traitent des différentes topologies d’identité que vous pouvez utiliser.

### <a name="azure-ad-single-tenant-topology"></a>Azure AD : topologie de client unique

Par défaut, lorsque vous installez Azure Stack Hub et utilisez Azure AD, Azure Stack Hub utilise une topologie de client unique.

Une topologie de client unique est utile quand :
- Tous les utilisateurs sont compris dans le même client.
- Un fournisseur de services héberge une instance Azure Stack Hub pour une organisation.

![Topologie à un seul locataire d’Azure Stack Hub avec Azure AD](media/azure-stack-identity-architecture/single-tenant.png)

Cette topologie présente les caractéristiques suivantes :

- Azure Stack Hub inscrit l’ensemble des applications et services auprès du même annuaire de locataire Azure AD.
- Azure Stack Hub n’authentifie que les utilisateurs et applications de cet annuaire, jetons compris.
- Les identités pour administrateurs (opérateurs cloud) et les utilisateurs client sont dans le même répertoire de client.
- Pour permettre à un utilisateur d’un autre annuaire d’accéder à cet environnement Azure Stack Hub, vous devez [inviter l’utilisateur en tant qu’invité](azure-stack-identity-overview.md#guest-users) à l’annuaire du locataire.

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD : topologie mutualisée

Des opérateurs cloud peuvent configurer Azure Stack Hub pour autoriser l’accès aux applications par les locataires d’une ou plusieurs organisations. Les utilisateurs accèdent aux applications via le portail utilisateur Azure Stack Hub. Dans cette configuration, le portail administrateur (dont se sert l’opérateur cloud) est limité aux utilisateurs d’un annuaire unique.

Une topologie d’architecture mutualisée est utile quand :

- Un fournisseur de services souhaite autoriser des utilisateurs de plusieurs organisations à accéder à Azure Stack Hub.

![Topologie mutualisée Azure Stack Hub avec Azure AD](media/azure-stack-identity-architecture/multi-tenant.png)

Cette topologie présente les caractéristiques suivantes :

- L’accès aux ressources doit se faire pour chaque organisation.
- Les utilisateurs d’une organisation ne devraient pas être capables d’autoriser l’accès aux ressources à des utilisateurs qui ne font pas partie de leur organisation.
- Les identités des administrateurs (opérateurs cloud) peuvent se trouver dans des répertoires de client différents des identités des utilisateurs. Cette séparation offre une isolation de compte au niveau du fournisseur d’identité.
 
### <a name="ad-fs"></a>AD FS

La topologie AD FS est nécessaire lorsqu’une des conditions suivantes s’applique :

- Azure Stack Hub ne se connecte pas à Internet.
- Azure Stack Hub peut se connecter à Internet, mais vous choisissez d’utiliser AD FS comme fournisseur d’identité.
  
![Topologie Azure Stack Hub utilisant AD FS](media/azure-stack-identity-architecture/adfs.png)

Cette topologie présente les caractéristiques suivantes :

- Pour prendre en charge l’utilisation de cette topologie en production, vous devez intégrer l’instance AD FS Azure Stack Hub dans une instance AD FS existante sauvegardée dans Active Directory, via une approbation de fédération.
- Vous pouvez intégrer le service Graph dans Azure Stack Hub avec votre instance Active Directory existante. Vous pouvez également utiliser le service API Graph basé sur OData qui prend en charge les API cohérentes avec l’API Graph Azure AD.

  Pour interagir avec votre instance Active Directory, l’API Graph nécessite des informations d’identification utilisateur de votre instance Active Directory correspondant à une autorisation en lecture seule.
  - L’instance AD FS intégrée est basée sur Windows Server 2016.
  - Vos instances AD FS et Active Directory doivent être basées sur Windows Server 2012 ou version ultérieure.
  
  Entre votre instance Active Directory et l’instance AD FS intégrée, les interactions ne sont pas limitées à OpenID Connect, et peuvent utiliser n’importe quel protocole mutuel pris en charge.
  - Les comptes utilisateurs sont créés et gérés dans votre instance Active Directory locale.
  - Les principaux de service et les inscriptions d’applications sont gérés dans l’instance Active Directory intégrée.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de l’identité](azure-stack-identity-overview.md)
- [Intégration au centre de données - Identité](azure-stack-integrate-identity.md)
