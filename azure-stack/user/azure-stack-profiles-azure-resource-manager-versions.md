---
title: Versions des API du fournisseur de ressources prises en charge par les profils dans Azure Stack Hub
description: Découvrez les versions des API Azure Resource Manager prises en charge par les profils dans Azure Stack Hub.
author: sethmanheim
ms.custom: contperfq4
ms.topic: article
ms.date: 08/28/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/08/2019
ms.openlocfilehash: 16e34ffaeb32e4652c83a3ee668235e79190b74d
ms.sourcegitcommit: 28894abb31423046f6775aadef490c467f1b1820
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2020
ms.locfileid: "89089658"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack-hub"></a>Versions des API du fournisseur de ressources prises en charge par les profils dans Azure Stack Hub

Vous pouvez trouver le fournisseur de ressources et les numéros de version pour chaque profil d’API utilisé par Azure Stack Hub dans cet article. Les tableaux de cet article répertorient les versions prises en charge pour chaque fournisseur de ressources et les versions API des profils. Chaque fournisseur de ressources contient un ensemble de types de ressources et de numéros de version spécifiques.

Le profil de l’API utilise trois conventions d’affectation de noms :

- **le plus récent**
- **yyyy-mm-dd-hybrid**
- **yyyy-mm-dd-profile**

Pour obtenir une explication des profils d’API et de la cadence de publication des versions d’Azure Stack Hub, consultez [Gérer les profils de version des API dans Azure Stack Hub](azure-stack-version-profiles.md).

> [!NOTE]
> Le **dernier** profil d’API contient la dernière version de l’API de fournisseur de ressources et n’est pas listé dans cet article.

## <a name="overview-of-the-2019-03-01-hybrid-profile"></a>Vue d’ensemble du profil 2019-03-01-hybrid

| Fournisseur de ressources | Api-version |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-12-01 |
| Microsoft.Network | 2017-10-01<br>La passerelle VPN sera 2017-10-01 |
| Microsoft.Storage (plan de données) | 2017-11-09 |
| Microsoft.Storage (plan de contrôle) | 2017-10-01 |
| Microsoft.Web | 2018-02-01 |
| Microsoft.KeyVault | 2016-10-01 (pas de modification) |
| Microsoft.Resources (Azure Resource Manager lui-même) | 2016-06-01 |
| Microsoft.Authorization (opérations de stratégie) | 2016-09-01 |
| Microsoft.Insights | 2018-01-01 |

Pour une liste des versions pour chaque type de ressource pour les fournisseurs dans le profil de l’API, consultez [Détails du profil 2019-03-01-hybrid](#details-for-the-2019-03-01-hybrid-profile).

## <a name="details-for-the-2019-03-01-hybrid-profile"></a>Détails du profil 2019-03-01-hybrid

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Le contrôle d’accès en fonction du rôle vous permet de gérer les actions que les utilisateurs de votre organisation peuvent entreprendre sur les ressources. Vous pouvez définir des rôles, assigner des rôles aux utilisateurs ou aux groupes et obtenir des informations sur les autorisations. Pour plus d’informations, consultez [Autorisation](/rest/api/authorization/).

| Types de ressources | Versions des API |
|---------------------|--------------------|
| Verrous | 2016-09-01 |
| Opérations | 2015-07-01 |
| Autorisations | 2015-07-01 |
| Affectations de stratégies | 2016-12-01 |
| Définitions de stratégies | 2016-12-01 |
| Opérations de fournisseur | 2015-07-01 |
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
| Groupes à haute disponibilité | 2017-12-01 |
| Emplacements | 2017-12-01 |
| Emplacements/opérations | 2017-12-01 |
| Emplacements/serveurs de publication | 2017-12-01 |
| Emplacements/utilisations | 2017-12-01 |
| Emplacements/Tailles de machines virtuelles | 2017-12-01 |
| Opérations | 2017-12-01 |
| Virtual Machines | 2017-12-01 |
| Machines virtuelles/extensions | 2017-12-01 |
| Virtual Machine Scale Sets | 2017-12-01 |
| Groupes de machines virtuelles identiques/extensions | 2017-12-01 |
| Groupes de machines virtuelles identiques/interfaces réseau | 2017-12-01 |
| Groupes de machines virtuelles identiques/machines virtuelles | 2017-12-01|
| Groupes de machines virtuelles identiques/Machines virtuelles/Interfaces réseau | 2017-12-01 |

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

Gérer vos coffres de clés, ainsi que les clés, les secrets et les certificats dans votre coffre de clés. Pour plus d’informations, consultez les [informations de référence sur l’API REST Azure Key Vault](/rest/api/keyvault/).

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
| Connexions | 2017-10-01 |
| Zones DNS | 2016-04-01 |
| Équilibreurs de charge | 2017-10-01 |
| Passerelle de réseau local | 2017-10-01 |
| Emplacements | 2017-10-01|
| Emplacement/operationResults | 2017-10-01 |
| Emplacements/opérations | 2017-10-01 |
| Emplacements/utilisations |2017-10-01 |
| Interfaces réseau | 2017-10-01 |
| Network Security Group | 2017-10-01 |
| Opérations | 2017-10-01 |
| Adresse IP publique | 2017-10-01 |
| Tables de routage | 2017-10-01 |
| Passerelle de réseau virtuel | 2017-10-01 |
| Virtual Network | 2017-10-01 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager vous permet de déployer et de gérer l’infrastructure pour vos solutions Azure. Vous organisez les ressources associées dans des groupes de ressources et vous déployez vos ressources avec des modèles JSON. Pour une introduction au déploiement et à la gestion des ressources avec Resource Manager, consultez [Vue d’ensemble d’Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

| Types de ressources | Versions des API |
|-----------------------------------------|-------------------|
| Déploiements | 2018-05-01 |
| Déploiements/opérations | 2018-05-01 |
| Liens | 2018-05-01 |
| Emplacements | 2018-05-01 |
| Opérations | 2018-05-01 |
| Fournisseurs | 2018-05-01 |
| Groupes de ressources| 2018-05-01 |
| Ressources | 2018-05-01 |
| Abonnements | 2018-05-01 |
| Abonnements/emplacements | 2016-06-01 |
| Abonnements/résultats des opérations | 2018-05-01 |
| Abonnements/fournisseurs | 2018-05-01 |
| Abonnements/groupes de ressources | 2018-05-01 |
| Subscriptions/resourceGroups/resources | 2018-05-01 |
| Abonnements/ressources | 2018-05-01 |
| Subscriptions/tagNames | 2018-05-01 |
| Subscriptions/tagNames/tagValues | 2018-05-01 |
| Locataires | 2016-06-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

Le fournisseur de ressources de stockage (SRP) vous permet de gérer par programmation votre compte de stockage et vos clés. Pour plus d’informations, consultez [Informations de référence sur l’API REST du fournisseur de ressources de stockage Azure](/rest/api/storagerp/).

| Types de ressources | Versions des API |
|-------------------------|--------------|
| Vérification de la disponibilité des noms | 2017-10-01 |
| Emplacements | 2017-10-01 |
| Emplacements/quotas | 2017-10-01 |
| Opérations | 2017-10-01 |
| StorageAccounts | 2017-10-01 |
| Utilisations | 2017-10-01 |

## <a name="next-steps"></a>Étapes suivantes

- [Installer PowerShell pour Azure Stack Hub](../operator/azure-stack-powershell-install.md)
- [Configurer l’environnement Azure Stack Hub PowerShell](azure-stack-powershell-configure-user.md)  
