---
title: Facturation et rétrofacturation des clients dans Azure Stack Hub
description: Découvrez comment est facturée l’utilisation des ressources pour les utilisateurs d’Azure Stack Hub, et comment les informations de facturation sont récupérées pour l’analytique et la rétrofacturation.
author: sethmanheim
ms.topic: article
ms.date: 11/09/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: 066538eb3bccb6e9e296a394400f2d1de0583f93
ms.sourcegitcommit: 980be7813e6f39fb59926174a5d3e0d392b04293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414093"
---
# <a name="usage-and-billing-in-azure-stack-hub"></a>Utilisation et facturation dans Azure Stack Hub

Cet article explique comment est facturée l’utilisation des ressources pour les utilisateurs d’Azure Stack Hub, et comment les informations de facturation sont récupérées pour l’analytique et la rétrofacturation.

Azure Stack Hub collecte et regroupe les données d’utilisation des ressources utilisées, puis transfère ces données à Azure Commerce. Azure Commerce vous facture l’utilisation d’Azure Stack Hub de la même façon qu’il vous facture l’utilisation d’Azure.

Vous pouvez également obtenir les données d’utilisation et les exporter vers votre propre système de facturation ou de rétrofacturation à l’aide d’un adaptateur de facturation. Vous pouvez aussi les exporter vers un outil décisionnel comme Microsoft Power BI.

## <a name="usage-pipeline"></a>Pipeline d’utilisation

Chaque fournisseur de ressources dans Azure Stack Hub poste des données d’utilisation par utilisation de ressources. Le service d’utilisation regroupe régulièrement (toutes les heures et quotidiennement) les données d’utilisation et les stocke dans la base de données d’utilisation. Les opérateurs et utilisateurs Azure Stack Hub peuvent accéder aux données d’utilisation stockées par le biais des API d’utilisation des ressources Azure Stack Hub.

Si vous avez [inscrit votre instance Azure Stack Hub auprès d’Azure](azure-stack-registration.md), Azure Stack Hub est configuré pour envoyer les données d’utilisation à Azure Commerce. Une fois les données chargées dans Azure, vous pouvez y accéder par le biais du portail de facturation ou des API d’utilisation des ressources Azure. Pour plus d’informations sur les données d’utilisation transmises à Azure, consultez [Rapports de données d’utilisation](azure-stack-usage-reporting.md).  

L’image suivante montre les principaux composants du pipeline d’utilisation :

![Pipeline d’utilisation](media/azure-stack-billing-and-chargeback/usagepipeline.svg)

## <a name="what-usage-information-can-i-find-and-how"></a>Quelles informations d’utilisation puis-je trouver et comment ?

Les fournisseurs de ressources Azure Stack Hub (comme Calcul, Stockage et Réseau) génèrent des données d’utilisation toutes les heures pour chaque abonnement. Les données d’utilisation contiennent des informations sur la ressource utilisée, comme le nom de la ressource, l’abonnement utilisé et la quantité utilisée. Pour en savoir plus sur les ressources ID de compteurs, consultez [Forum aux questions sur l’API d’utilisation d’Azure Stack](azure-stack-usage-related-faq.md).

Une fois les données d’utilisation collectées, elles sont [transmises à Azure](azure-stack-usage-reporting.md) pour générer une facture, qui peut être affichée via le portail de facturation Azure.

> [!NOTE]  
> Les rapports des données d’utilisation ne sont pas nécessaires pour le Kit de développement Azure Stack ni pour les utilisateurs de systèmes intégrés Azure Stack Hub qui gèrent leur licence dans le modèle de capacité. Pour en savoir plus sur la gestion des licences dans Azure Stack Hub, consultez la [fiche sur les packages et les prix](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

Le portail de facturation Azure affiche les données d’utilisation des ressources facturables. Outre les ressources facturables, Azure Stack Hub capture les données d’utilisation pour un ensemble plus large de ressources, auquel vous pouvez accéder dans votre environnement Azure Stack Hub via des API REST ou des cmdlets PowerShell. Les opérateurs Azure Stack Hub peuvent obtenir les données d’utilisation pour tous les abonnements utilisateur. Par contre, chaque utilisateur peut uniquement obtenir des détails sur son utilisation.

## <a name="usage-reporting-for-multi-tenant-cloud-solution-providers"></a>Rapports d’utilisation pour les fournisseurs de solutions cloud multilocataires

Un fournisseur de solutions cloud (CSP) multilocataires qui utilise Azure Stack Hub peut avoir besoin de journaliser l’utilisation pour chaque client séparément, afin de facturer l’utilisation aux différents abonnements Azure.

Chaque client va avoir son identité représentée par un autre locataire Azure Active Directory (Azure AD). Azure Stack Hub prend en charge l’attribution d’un abonnement CSP à chaque locataire Azure AD. Vous pouvez ajouter des locataires et leurs abonnements à l’inscription Azure Stack Hub de base. L’inscription de base est effectuée pour toutes les instances Azure Stack Hub. Si un abonnement n’est pas inscrit pour un locataire, l’utilisateur peut toujours utiliser Azure Stack Hub, et son utilisation est envoyée à l’abonnement utilisé pour l’inscription de base.

## <a name="next-steps"></a>Étapes suivantes

- [S’inscrire auprès d’Azure Stack Hub](azure-stack-registration.md)
- [Signaler les données d'utilisation Azure Stack Hub à Azure](azure-stack-usage-reporting.md)
- [API Utilisation des ressources de fournisseur](azure-stack-provider-resource-api.md)
- [API d’utilisation des ressources de locataire](azure-stack-tenant-resource-usage-api.md)
- [Questions fréquentes (FAQ) relatives à l’utilisation](azure-stack-usage-related-faq.md)
