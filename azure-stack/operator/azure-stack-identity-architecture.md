---
title: Architecture d’identité pour Azure Stack | Microsoft Docs
description: Découvrez l’architecture d’identité pour Azure Stack et les différences entre Azure AD et AD FS.
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
ms.openlocfilehash: 364028183445df7e74828605439bfd7b3784f01f
ms.sourcegitcommit: 451cfaa24b349393f36ae9d646d4d311a14dd1fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72019404"
---
# <a name="identity-architecture-for-azure-stack"></a>Architecture d’identité pour Azure Stack

Quand vous choisissez un fournisseur d’identité à utiliser avec Azure Stack, vous devez comprendre les différences importantes entre les options d’Azure Active Directory (Azure AD) et les services de fédération Active Directory (AD FS).

## <a name="capabilities-and-limitations"></a>Capacités et limitations

Le fournisseur d’identité que vous choisissez peut limiter vos options, dont la prise en charge d’architecture mutualisée.

|Capacité ou scénario        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Connecté à Internet     |OUI       |Facultatif|
|Prise en charge d’architecture mutualisée     |OUI       |Non      |
|Proposer des articles dans la place de marché |OUI       |Oui (nécessite l’utilisation de l’outil [Syndication de Place de marché hors ligne](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario))|
|Prise en charge de la bibliothèque d’authentification Active Directory (ADAL) |OUI |OUI|
|Prise en charge des outils tels que Azure CLI, Visual Studio et PowerShell  |OUI |OUI|
|Créer des principaux de service via le portail Azure     |OUI |Non|
|Créer des principaux de service avec des certificats      |OUI |OUI|
|Créer des principaux de service avec des secrets (clés)    |OUI |OUI|
|Les applications peuvent utiliser le service Graph           |OUI |Non|
|Les applications peuvent utiliser le fournisseur d’identité pour se connecter |OUI |Oui (les applications doivent fédérer avec vos instances AD FS locales) |

## <a name="topologies"></a>Topologies

Les sections suivantes traitent des différentes topologies d’identité que vous pouvez utiliser.

### <a name="azure-ad-single-tenant-topology"></a>Azure AD : topologie de client unique

Par défaut, lorsque vous installez Azure Stack et utilisez Azure AD, Azure Stack utilise une topologie de client unique.

Une topologie de client unique est utile quand :
- Tous les utilisateurs sont compris dans le même client.
- Un fournisseur de services héberge une instance Azure Stack pour une organisation.

![Topologie de client unique Azure Stack avec Azure AD](media/azure-stack-identity-architecture/single-tenant.png)

Cette topologie présente les caractéristiques suivantes :

- Azure Stack enregistre toutes les applications et services dans le même annuaire du locataire Azure AD.
- Azure Stack n’authentifie que les utilisateurs et applications de cet annuaire, jetons compris.
- Les identités pour administrateurs (opérateurs cloud) et les utilisateurs client sont dans le même répertoire de client.
- Pour permettre à un utilisateur d’un autre répertoire d’accéder à cet environnement Azure Stack, vous devez [inviter l’utilisateur en tant qu’invité](azure-stack-identity-overview.md#guest-users) au répertoire de client.

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD : topologie mutualisée

Les opérateurs cloud peuvent configurer Azure Stack pour autoriser l’accès aux applications par les locataires d’une ou plusieurs organisations. Les utilisateurs accèdent aux applications via le portail de l’utilisateur Azure Stack. Dans cette configuration, le portail administrateur (dont se sert l’opérateur cloud) est limité aux utilisateurs d’un annuaire unique.

Une topologie d’architecture mutualisée est utile quand :

- Un fournisseur de services souhaite autoriser des utilisateurs de plusieurs organisations à accéder à Azure Stack.

![Topologie mutualisée Azure Stack avec Azure AD](media/azure-stack-identity-architecture/multi-tenant.png)

Cette topologie présente les caractéristiques suivantes :

- L’accès aux ressources doit se faire pour chaque organisation.
- Les utilisateurs d’une organisation ne devraient pas être capables d’autoriser l’accès aux ressources à des utilisateurs qui ne font pas partie de leur organisation.
- Les identités des administrateurs (opérateurs cloud) peuvent se trouver dans des répertoires de client différents des identités des utilisateurs. Cette séparation offre une isolation de compte au niveau du fournisseur d’identité.
 
### <a name="ad-fs"></a>AD FS

La topologie AD FS est nécessaire lorsqu’une des conditions suivantes s’applique :

- Azure Stack ne se connecte pas à Internet.
- Azure Stack peut se connecter à Internet, mais vous choisissez d’utiliser AD FS comme fournisseur d’identité.
  
![Topologie Azure Stack utilisant AD FS](media/azure-stack-identity-architecture/adfs.png)

Cette topologie présente les caractéristiques suivantes :

- Pour prendre en charge l’utilisation de cette topologie en production, vous devez intégrer l’instance AD FS Azure Stack dans une instance AD FS existante sauvegardée dans Active Directory, via une approbation de fédération.
- Vous pouvez intégrer le service Graph dans Azure Stack avec votre instance Active Directory existante. Vous pouvez également utiliser le service API Graph basé sur OData qui prend en charge les API cohérentes avec l’API Graph Azure AD.

  Pour interagir avec votre instance Active Directory, l’API Graph nécessite des informations d’identification utilisateur de votre instance Active Directory correspondant à une autorisation en lecture seule.
  - L’instance AD FS intégrée est basée sur Windows Server 2016.
  - Vos instances AD FS et Active Directory doivent être basées sur Windows Server 2012 ou version ultérieure.
  
  Entre votre instance Active Directory et l’instance AD FS intégrée, les interactions ne sont pas limitées à OpenID Connect, et peuvent utiliser n’importe quel protocole mutuel pris en charge.
  - Les comptes utilisateurs sont créés et gérés dans votre instance Active Directory locale.
  - Les principaux de service et les inscriptions d’applications sont gérés dans l’instance Active Directory intégrée.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de l’identité](azure-stack-identity-overview.md)
- [Intégration au centre de données - Identité](azure-stack-integrate-identity.md)
