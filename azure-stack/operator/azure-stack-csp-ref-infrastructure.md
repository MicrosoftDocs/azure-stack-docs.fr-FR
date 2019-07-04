---
title: Infrastructure des rapports d’utilisation pour les fournisseurs de services cloud | Microsoft Docs
description: Azure Stack inclut l’infrastructure nécessaire pour suivre l’utilisation prise en charge par un fournisseur de services cloud (CSP) au moment où elle se produit et la transfère à Azure.
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
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 0b4ff1e6c76bedc4618bfa527b0045d7bfce41af
ms.sourcegitcommit: bcaad8b7db2ea596018d973cb29283d8c6daebfb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2019
ms.locfileid: "67419470"
---
# <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>Infrastructure des rapports d’utilisation pour les fournisseurs de services Cloud

Azure Stack inclut l’infrastructure nécessaire pour suivre l’utilisation au moment où elle se produit et la transfère à Azure. Dans Azure, Azure Commerce traite les données d’utilisation et facture l’utilisation aux abonnements Azure appropriés. Cela se déroule de la même façon que la surveillance du suivi de l’utilisation dans le cloud Azure global.

Certains concepts sont cohérents entre Azure global et Azure Stack. Azure Stack a des abonnements locaux qui remplissent un rôle similaire à un abonnement Azure. Les abonnements locaux sont valides uniquement localement. Les abonnements locaux sont mappés à des abonnements Azure quand l’utilisation est transférée à Azure.

Azure Stack dispose de compteurs d’utilisation locale. L’utilisation locale est mappée aux compteurs utilisés dans Azure Commerce. Toutefois, les ID de compteurs sont différents. Les compteurs disponibles localement sont plus nombreux que ceux que Microsoft utilise pour la facturation.

Il existe des différences entre la façon dont la tarification des services est fixée dans Azure Stack et dans Azure. Par exemple, dans Azure Stack, les frais pour les machines virtuelles reposent uniquement sur la mémoire à tores magnétiques virtuelle (vcore)/les heures, avec le même tarif pour toutes les séries de machine virtuelle, contrairement à Azure. Cela s’explique par le fait que dans Azure global, les différents prix reflètent les différents matériels. Dans Azure Stack, étant donné que le client fournit le matériel, il n’y a aucune raison d’appliquer une tarification différente pour les différentes catégories de machine virtuelle.

Vous trouverez plus d’informations sur les compteurs Azure Stack utilisés dans Azure Commerce et leurs prix dans l’Espace partenaires, comme vous le feriez pour les services Azure :

1. Dans l’Espace partenaires, accédez au **menu Tableau de bord**, sélectionnez **Vente**, puis **Tarifs et offres**.
2. Sous **Services basés sur l’utilisation**, sélectionnez **Actuelle**.
3. Ouvrez la feuille de calcul**Liste des prix Azure dans l’environnement CSP global**.
4. Filtrez sur **Region = Azure Stack**.

## <a name="terms-used-for-billing-and-usage"></a>Termes utilisés pour la facturation et l’utilisation

Les termes et concepts suivants sont utilisés pour l’utilisation et la facturation dans Azure Stack :

| Terme | Définition |
| --- | --- |
| Partenaire CSP direct | Un partenaire Fournisseur de services cloud (CSP) direct reçoit une facture directement de Microsoft pour l’utilisation d’Azure et d’Azure Stack et facture directement les clients. |
| Fournisseur de solutions cloud (CSP) indirect | Les revendeurs indirects travaillent avec un fournisseur indirect (également appelé serveur de distribution). Les revendeurs recrutement des clients finaux. Le fournisseur indirect maintient la relation de facturation avec Microsoft, gère la facturation du client et fournit des services supplémentaires tels que le support technique. |
| Client final | Les clients finaux sont les entreprises et les organismes publics qui possèdent les applications et d’autres charges de travail qui s’exécutent sur Azure Stack. |

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le programme CSP, consultez la page [Solutions cloud](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Pour en savoir plus sur la récupération d’informations d’utilisation de ressources à partir d’Azure Stack, consultez [Usage and billing in Azure Stack](azure-stack-billing-and-chargeback.md) (Utilisation et facturation dans Azure Stack).
