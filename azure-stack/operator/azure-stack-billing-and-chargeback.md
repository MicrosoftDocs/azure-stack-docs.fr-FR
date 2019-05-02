---
title: Facturation des clients et rétrofacturation dans Azure Stack | Microsoft Docs
description: Découvrez comment récupérer les informations sur l’utilisation des ressources à partir d’Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: 8e47022d826d5983369fe7849cbde8c00f18838e
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64306725"
---
# <a name="usage-and-billing-in-azure-stack"></a>Utilisation et facturation dans Azure Stack

Cet article décrit la façon dont les utilisateurs d’Azure Stack sont facturés pour l’utilisation des ressources, et explique comment les informations de facturation sont récupérées pour les besoins d’analytique et de rétrofacturation.

Azure Stack collecte et regroupe les données d’utilisation pour les ressources utilisées. Azure Stack transfère ensuite ces données à Azure Commerce. Azure Commerce vous facture l’utilisation d’Azure Stack de la même façon qu’il vous facture l’utilisation d’Azure.

Vous pouvez également obtenir les données d’utilisation et les exporter vers votre propre système de facturation ou de rétrofacturation à l’aide d’un adaptateur de facturation. Vous pouvez aussi les exporter vers un outil décisionnel comme Microsoft Power BI.

## <a name="usage-pipeline"></a>Pipeline d’utilisation

Chaque fournisseur de ressources dans Azure Stack poste des données d’utilisation par utilisation de ressources. Le service d’utilisation regroupe régulièrement (toutes les heures et quotidiennement) les données d’utilisation et les stocke dans la base de données d’utilisation. Les opérateurs et utilisateurs Azure Stack peuvent accéder aux données d’utilisation stockées par le biais des API d’utilisation des ressources Azure Stack.

Si vous avez [inscrit votre instance Azure Stack auprès d’Azure](azure-stack-registration.md ), Azure Stack est configuré pour envoyer les données d’utilisation à Azure Commerce. Une fois les données chargées dans Azure, vous pouvez y accéder par le biais du portail de facturation ou des API d’utilisation des ressources Azure. Pour plus d’informations sur les données d’utilisation transmises à Azure, consultez [Rapports de données d’utilisation](azure-stack-usage-reporting.md).  

L’illustration suivante montre les principaux composants du pipeline d’utilisation :

![Pipeline d’utilisation](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Quelles informations d’utilisation puis-je trouver et comment ?

Les fournisseurs de ressources Azure Stack (comme Calcul, Stockage et Réseau) génèrent des données d’utilisation toutes les heures pour chaque abonnement. Les données d’utilisation contiennent des informations sur la ressource utilisée, comme le nom de la ressource, l’abonnement utilisé et la quantité utilisée. Pour en savoir plus sur les ressources ID de compteurs, consultez [Forum aux questions sur l’API d’utilisation d’Azure Stack](azure-stack-usage-related-faq.md).

Une fois les données d’utilisation collectées, elles sont [transmises à Azure](azure-stack-usage-reporting.md) pour générer une facture, qui peut être affichée via le portail de facturation Azure.

> [!NOTE]  
> Les rapports des données d’utilisation ne sont pas nécessaires pour le Kit de développement Azure Stack ni pour les utilisateurs de systèmes intégrés Azure Stack qui gèrent leur licence dans le modèle de capacité. Pour en savoir plus sur la gestion des licences dans Azure Stack, consultez la [fiche sur les packages et les prix](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

Le portail de facturation Azure affiche les données d’utilisation des ressources facturables. Outre les ressources facturables, Azure Stack capture les données d’utilisation pour un ensemble plus large de ressources, auquel vous pouvez accéder dans votre environnement Azure Stack via des API REST ou des cmdlets PowerShell. Les opérateurs Azure Stack peuvent obtenir les données d’utilisation pour tous les abonnements utilisateur. Par contre, chaque utilisateur peut uniquement obtenir des détails sur son utilisation.

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>Rapports d’utilisation pour les fournisseurs de services cloud multilocataires

Un fournisseur de services cloud (CSP) multilocataire ayant de nombreux clients qui utilisent Azure Stack peut avoir besoin de journaliser l’utilisation par chaque client séparément pour pouvoir facturer l’utilisation sur des abonnements Azure différents.

Chaque client va avoir son identité représentée par un autre locataire Azure Active Directory (Azure AD). Azure Stack prend en charge l’affectation d’un abonnement CSP à chaque locataire Azure AD. Vous pouvez ajouter des locataires et leurs abonnements à l’inscription Azure Stack de base. L’inscription de base est effectuée pour toutes les instances Azure Stack. Si un abonnement n’est pas inscrit pour un locataire, l’utilisateur peut toujours utiliser Azure Stack, et son utilisation est envoyée à l’abonnement utilisé pour l’inscription de base.

## <a name="next-steps"></a>Étapes suivantes

- [S’inscrire auprès d’Azure Stack](azure-stack-registration.md)
- [Signaler les données d’utilisation Azure Stack à Azure](azure-stack-usage-reporting.md)
- [API Utilisation des ressources de fournisseur](azure-stack-provider-resource-api.md)
- [API Utilisation des ressources de client](azure-stack-tenant-resource-usage-api.md)
- [Questions fréquentes (FAQ) relatives à l’utilisation](azure-stack-usage-related-faq.md)