---
title: Versions des API du fournisseur de ressources prises en charge par des profils dans Azure Stack | Microsoft Docs
description: En savoir plus sur la version d’Azure Resource Manager prise en charge par des profils dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 2e73e6e00503171aafea4ae254af0427a3e245ac
ms.sourcegitcommit: a78c0d143eadcab65a601746b9ea24be28091ad2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212320"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Versions des API du fournisseur de ressources prises en charge par des profils dans Azure Stack

Vous pouvez trouver le fournisseur de ressources et les numéros de version pour chaque profil d’API utilisé par Azure Stack dans cet article. Les tableaux de cet article répertorient les versions prises en charge pour chaque fournisseur de ressources et les versions API des profils. Chaque fournisseur de ressources contient un ensemble de types de ressources et de numéros de version spécifiques.

Le profil de l’API utilise trois conventions d’affectation de noms :

- **le plus récent**
- **yyyy-mm-dd-hybrid**
- **yyyy-mm-dd-profile**

Pour obtenir une explication des profils d’API et de la cadence de publication de versions pour Azure Stack, consultez [Gérer les profils de version des API dans Azure Stack](azure-stack-version-profiles.md).

> [!Note]  
> Le **dernier** profil d’API contient la dernière version de l’API de fournisseur de ressources et n’est pas listé dans cet article.

## <a name="overview-of-2018---03-01-hybrid"></a>Vue d’ensemble de 2018-03-01-hybride

| Fournisseur de ressources | Api-version |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-03-30 |
| Microsoft.Network | 2017-10-01<br>La passerelle VPN sera 2017-03-01 |
| Microsoft.Storage (plan de données) | 2017-04-17 |
| Microsoft.Storage (plan de contrôle) | 2016-01-01 |
| Microsoft. Web | 2016-08-01<br>à savoir la version la plus récente (à l’heure actuelle) dans Azure |
| Microsoft.KeyVault | 2016-10-01 (pas de modification) |
| Microsoft.Resources         (Azure Resource Manager lui-même) | 2016-02-01 |
| Microsoft.Authorization   (opérations de stratégie) | 2015-11-01 |
| Microsoft.Insights | 2015-11-01 |
| Microsoft.Keyvault | 2016-10-01 |
| Stratégie | 2016-10-01 |
| Ressources | 2016-10-01 |
| Resources_Links | 2016-10-01 |
| Resources_Locks | 2016-10-01 |
| Abonnements | 2016-10-01 |

Pour une liste des versions pour chaque type de ressource pour les fournisseurs dans le profil de l’API, consultez le profil [Détails pour 2018-03-01-hybride](#details-for-the-2018-03-01-hybrid).

## <a name="details-for-the-2018-03-01-hybrid"></a>Détails de 2018-03-01-hybride

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Vous utilisez le contrôle d’accès en fonction du rôle pour gérer les actions que les utilisateurs de votre organisation peuvent entreprendre sur les ressources. Cet ensemble d’opérations vous permet de définir des rôles, d’assigner des rôles aux utilisateurs ou aux groupes, et d’obtenir des informations sur les autorisations. Pour plus d’informations, consultez [Autorisation](/rest/api/authorization/).

| Types de ressources | Versions des API |
|---------------------|--------------------|
| Verrous | 2017-04-01 |
| Opérations | 2015-07-01 |
| Autorisations | 2015-07-01 |
| Affectations de stratégies | 2016-12-01 (2017-06-01-préversion) |
| Définitions de stratégies | 2016-12-01 |
| Opérations de fournisseur | 2015-07-01-préversion |
| Affectations de rôles | 2015-07-01 |
| Définitions de rôles | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| Type de ressource | Version de l'API |
|----------------------------------|----------------------|
| Abonnements de fournisseur délégué | 2015-06-01 - préversion |
| Agrégats d’utilisation déléguée | 2015-06-01 - préversion |
| Estimation des dépenses de la ressource | 2015-06-01 - préversion |
| Opérations | 2015-06-01 - préversion |
| Agrégats d’utilisation de l’abonné | 2015-06-01 - préversion |
| Agrégats d’utilisation | 2015-06-01 - préversion |

### <a name="microsoftcompute"></a>Microsoft.Compute

Les API de calcul Azure vous donnent un accès par programme aux machines virtuelles et à leurs ressources connexes. Pour plus d’informations, consultez [Calcul Azure](/rest/api/compute/).

| Type de ressource | Version de l'API |
|---------------------------------------------------------------|-------------|
| Groupes à haute disponibilité | 2016-03-30 |
| Emplacements | 2016-03-30 |
| Emplacements/opérations | 2016-03-30 |
| Emplacements/serveurs de publication | 2016-03-30 |
| Emplacements/utilisations | 2016-03-30 |
| Emplacements/Tailles de machines virtuelles | 2016-03-30 |
| Opérations | 2016-03-30 |
| Virtual Machines | 2016-03-30 |
| Machines virtuelles/extensions | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| Groupes de machines virtuelles identiques/extensions | 2016-03-30 |
| Groupes de machines virtuelles identiques/interfaces réseau | 2016-03-30 |
| Groupes de machines virtuelles identiques/machines virtuelles | 2016-03-30 |
| Groupes de machines virtuelles identiques/Machines virtuelles/Interfaces réseau | 2016-03-30 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Type de ressource | Version de l'API |
|------------------|-------------|
| Collecte | 2015-04-01 |
| Contenu de collecte | 2015-04-01 |
| Extraction de collecte | 2015-04-01 |
| Éléments de la galerie | 2015-04-01 |
| Opérations | 2015-04-01 |
| Portail | 2015-04-01 |
| Recherche | 2015-04-01 |
| Suggérer | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| Types de ressources | Versions des API |
|--------------------|--------------------|
| Opérations | 2015-04-01 |
| Types d’événements | 2015-04-01 |
| Catégories d’événements | 2015-04-01 |
| Définitions de métriques | 2018-01-01 |
| Mesures | 2018-01-01 |
| Paramètres de diagnostic | 2017-05-01-preview |
| Catégories de paramètres de diagnostic | 2017-05-01-preview |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Gestion de vos coffres de clés, ainsi que des clés, des secrets et des certificats dans vos coffres de clés. Pour plus d’informations, consultez les [informations de référence sur l’API REST Azure Key Vault](/rest/api/keyvault/).

| Types de ressources | Versions des API |
|-------------------------|--------------|
| Opérations | 2016-10-01 |
| Coffres | 2016-10-01 |
| Coffres/Stratégies d’accès | 2016-10-01 |
| Coffres/secrets | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

Le résultat de l’appel des opérations est une représentation sous forme de liste des opérations cloud du réseau disponible. Pour plus d’informations, consultez [API REST Operation](/rest/api/operation/).

| Types de ressources | Versions des API |
|---------------------------|--------------|
| connexions | 2015-06-15 |
| Zones DNS | 2016-04-01 |
| Équilibreurs de charge | 2015-06-15 |
| Passerelle de réseau local | 2015-06-15 |
| Emplacements | 2016-04-01 |
| Emplacement/operationResults | 2016-04-01 |
| Emplacements/opérations | 2016-04-01 |
| Emplacements/utilisations | 2016-04-01 |
| Interfaces réseau | 2015-06-15 |
| Network Security Group | 2015-06-15 |
| Opérations | 2015-06-15 |
| Adresse IP publique | 2015-06-15 |
| Tables de routage | 2015-06-15 |
| Passerelle de réseau virtuel | 2015-06-15 |
| Virtual Network | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager vous permet de déployer et de gérer l’infrastructure pour vos solutions Azure. Vous organisez les ressources associées dans des groupes de ressources et vous déployez vos ressources avec des modèles JSON. Pour une introduction au déploiement et à la gestion des ressources avec Resource Manager, consultez [Vue d’ensemble d’Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

| Types de ressources | Versions des API |
|-----------------------------------------|-------------------|
| Inscriptions à l’application | 2015-01-01 |
| Vérifier le nom de la ressource | 2016-09-01 |
| Fournisseurs délégués | 2015-01-01 |
| Fournisseurs délégués/offres | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Déploiements | 2016-09-01 |
| Déploiements/opérations | 2016-09-01 |
| Métadonnées d’extensions | 2015-01-01 |
| Liens | 2016-09-01 |
| Emplacements | 2015-01-01 |
| Offres | 2015-01-01 |
| Opérations | 2015-01-01 |
| Fournisseurs | 2017-08-01 |
| Groupes de ressources | 2016-09-01 |
| Ressources | 2016-09-01 |
| Abonnements | 2016-09-01 |
| Abonnements/emplacement | 2016-09-01 |
| Abonnements/résultats de l’opération | 2016-09-01 |
| Abonnements/fournisseurs | 2017-08-01 |
| Abonnements/Groupes de ressources | 2016-09-01 |
| Subscriptions/resourceGroups/resources | 2016-09-01 |
| Abonnements/ressources | 2016-09-01 |
| Subscriptions/tagNames | 2016-09-01 |
| Subscriptions/tagNames/tagValues | 2016-09-01 |
| Locataires | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

Le fournisseur de ressources de stockage (SRP) vous permet de gérer par programme votre compte de stockage et vos clés. Pour plus d’informations, consultez [Informations de référence sur l’API REST du fournisseur de ressources de stockage Azure](/rest/api/storagerp/).

| Types de ressources | Versions des API |
|-------------------------|--------------|
| Vérifier la disponibilité du nom | 2016-01-01 |
| Emplacements | 2016-01-01 |
| Emplacements/quotas | 2016-01-01 |
| Opérations | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Utilisations | 2016-01-01 |

## <a name="details-for-the-2018-03-01-hybrid"></a>Détails de 2018-03-01-hybride

### <a name="microsoft-authorization"></a>Autorisation Microsoft

| Types de ressources | Versions des API |
|---------------------|---------------------------------|
| Verrous | 2017-04-01 |
| Opérations | 2015-07-01 |
| Autorisations | 2015-07-01 |
| Affectations de stratégies | 2016-12-01 (2017-06-01-préversion) |
| Définitions de stratégies | 2016-12-01 |
| Opérations de fournisseur | 2015-07-01-préversion |
| Affectations de rôles | 2015-07-01 |
| Définitions de rôles | 2015-07-01 |

### <a name="microsoftcompute"></a>Microsoft.Compute

| Type de ressource | Version de l'API |
|---------------------------------------------------------------|-------------|
| Groupes à haute disponibilité | 2016-03-30 |
| Emplacements | 2016-03-30 |
| Emplacements/opérations | 2016-03-30 |
| Emplacements/serveurs de publication | 2016-03-30 |
| Emplacements/utilisations | 2016-03-30 |
| Emplacements/Tailles de machines virtuelles | 2016-03-30 |
| Opérations | 2016-03-30 |
| Virtual Machines | 2016-03-30 |
| Machines virtuelles/extensions | 2016-03-30 |
| Virtual Machine Scale Sets | 2016-03-30 |
| Groupes de machines virtuelles identiques/extensions | 2016-03-30 |
| Groupes de machines virtuelles identiques/interfaces réseau | 2016-03-30 |
| Groupes de machines virtuelles identiques/machines virtuelles | 2016-03-30 |
| Groupes de machines virtuelles identiques/Machines virtuelles/Interfaces réseau | 2016-03-30 |

### <a name="microsoftnetwork"></a>Microsoft.Network

| Types de ressources | Versions des API |
|---------------------------|--------------|
| connexions | 2015-06-15 |
| Zones DNS | 2016-04-01 |
| Équilibreurs de charge | 2015-06-15 |
| Passerelle de réseau local | 2015-06-15 |
| Emplacements | 2016-04-01 |
| Emplacement/operationResults | 2016-04-01 |
| Emplacements/opérations | 2016-04-01 |
| Emplacements/utilisations | 2016-04-01 |
| Interfaces réseau | 2015-06-15 |
| Network Security Group | 2015-06-15 |
| Opérations | 2015-06-15 |
| Adresse IP publique | 2015-06-15 |
| Tables de routage | 2015-06-15 |
| Passerelle de réseau virtuel | 2015-06-15 |
| Virtual Network | 2015-06-15 |

### <a name="microsoftresources"></a>Microsoft.Resources

| Types de ressources | Versions des API |
|-----------------------------------------|--------------|
| Inscriptions à l’application | 2015-01-01 |
| Vérifier le nom de la ressource | 2016-09-01 |
| Fournisseurs délégués | 2015-01-01 |
| Fournisseurs délégués/offres | 2015-01-01 |
| DelegatedProviders/offers/estimatePrice | 2015-01-01 |
| Déploiements | 2016-09-01 |
| Déploiements/opérations | 2016-09-01 |
| Métadonnées d’extensions | 2015-01-01 |
| Liens | 2016-09-01 |
| Emplacements | 2015-01-01 |
| Offres | 2015-01-01 |
| Opérations | 2015-01-01 |
| Fournisseurs | 2017-08-01 |
| Groupes de ressources | 2016-09-01 |
| Ressources | 2016-09-01 |
| Abonnements | 2016-09-01 |
| Abonnements/emplacement | 2016-09-01 |
| Abonnements/résultats de l’opération | 2016-09-01 |
| Abonnements/fournisseurs | 2017-08-01 |
| Abonnements/Groupes de ressources | 2016-09-01 |
| Subscriptions/resourceGroups/resources | 2016-09-01 |
| Abonnements/ressources | 2016-09-01 |
| Subscriptions/tagNames | 2016-09-01 |
| Subscriptions/tagNames/tagValues | 2016-09-01 |
| Locataires | 2017-08-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

| Types de ressources | Versions des API |
|-------------------------|--------------|
| Vérifier la disponibilité du nom | 2016-01-01 |
| Emplacements | 2016-01-01 |
| Emplacements/quotas | 2016-01-01 |
| Opérations | 2016-01-01 |
| StorageAccounts | 2016-01-01 |
| Utilisations | 2016-01-01 |

## <a name="next-steps"></a>Étapes suivantes

- [Installer PowerShell pour Azure Stack](../operator/azure-stack-powershell-install.md)
- [Configurez l’environnement PowerShell de l’utilisateur Azure Stack.](azure-stack-powershell-configure-user.md)  
