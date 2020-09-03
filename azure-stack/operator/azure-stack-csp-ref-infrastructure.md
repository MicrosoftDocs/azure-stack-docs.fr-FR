---
title: Infrastructure des rapports d’utilisation pour les fournisseurs de solutions cloud dans Azure Stack Hub
description: Découvrez plus d’informations sur l’infrastructure des rapports d’utilisation utilisée pour suivre l’utilisation des locataires gérés par un fournisseur de solutions cloud (CSP).
author: sethmanheim
ms.topic: article
ms.date: 08/27/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: f3d5ba6cfe8f8006de7dd11c03194ebf29ca698c
ms.sourcegitcommit: 28894abb31423046f6775aadef490c467f1b1820
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2020
ms.locfileid: "89089675"
---
# <a name="usage-reporting-infrastructure-for-cloud-solution-providers"></a>Infrastructure des rapports d’utilisation pour les fournisseurs de solutions cloud

Azure Stack Hub inclut l’infrastructure nécessaire pour suivre l’utilisation au moment où elle se produit et la transfère à Azure. Dans Azure, Azure Commerce transfère les [données d’utilisation et de facturation](azure-stack-billing-and-chargeback.md) aux abonnements Azure appropriés. Ce processus fonctionne de la même façon que le suivi de l’utilisation dans le cloud Azure global.

Certains concepts sont cohérents entre Azure global et Azure Stack Hub. Azure Stack Hub a des abonnements locaux qui remplissent un rôle similaire à un abonnement Azure. Les abonnements locaux sont valides uniquement localement. Les abonnements locaux sont mappés à des abonnements Azure quand l’utilisation est transférée à Azure.

Azure Stack Hub dispose de compteurs d’utilisation locale. L’utilisation locale est mappée aux compteurs utilisés dans Azure Commerce. Toutefois, les ID de compteurs sont différents. Les compteurs disponibles localement sont plus nombreux que ceux que Microsoft utilise pour la facturation.

Il existe des différences entre la façon dont la tarification des services est fixée dans Azure Stack Hub et dans Azure. Par exemple, dans Azure Stack Hub, les frais pour les machines virtuelles reposent uniquement sur la mémoire à tores magnétiques virtuelle (vcore)/les heures, avec le même tarif pour toutes les séries de machine virtuelle, contrairement à Azure. Cela s’explique par le fait que dans Azure global, les différents prix reflètent les différents matériels. Dans Azure Stack Hub, le client fournit le matériel donc il n’y a aucune raison d’appliquer un tarif différent pour les différentes catégories de machines virtuelles.

Vous trouverez plus d’informations sur les compteurs Azure Stack Hub utilisés dans Azure Commerce et leurs prix dans l’Espace partenaires. Le processus est le même que pour les services Azure :

1. Dans l’Espace partenaires, accédez au menu **Tableau de bord**, puis sélectionnez **Vente** et **Tarifs et offres**.
2. Sous **Services basés sur l’utilisation**, sélectionnez **Actuelle**.
3. Ouvrez la feuille de calcul**Liste des prix Azure dans l’environnement CSP global**.
4. Filtrez sur **Region = Azure Stack Hub**.

## <a name="terms-used-for-billing-and-usage"></a>Termes utilisés pour la facturation et l’utilisation

Les termes et concepts suivants sont utilisés pour l’utilisation et la facturation dans Azure Stack Hub :

| Terme | Définition |
| --- | --- |
| Partenaire CSP direct | Un partenaire CSP direct reçoit une facture directement de Microsoft pour l’utilisation d’Azure et d’Azure Stack Hub et facture directement les clients. |
| Fournisseur de solutions cloud (CSP) indirect | Les revendeurs indirects travaillent avec un fournisseur indirect (également appelé serveur de distribution). Les revendeurs recrutement des clients finaux. Le fournisseur indirect maintient la relation de facturation avec Microsoft, gère la facturation du client et fournit des services supplémentaires tels que le support technique. |
| Client final | Les clients finaux sont les entreprises et les organismes publics ayant des applications et d’autres charges de travail qui s’exécutent sur Azure Stack Hub. |

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le programme CSP, consultez la page [Solutions cloud](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Pour en savoir plus sur la récupération d’informations d’utilisation de ressources à partir d’Azure Stack Hub, voir [Utilisation et facturation dans Azure Stack Hub](azure-stack-billing-and-chargeback.md).
