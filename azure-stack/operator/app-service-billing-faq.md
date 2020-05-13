---
title: Vue d’ensemble de la facturation d’Azure App Service sur Azure Stack Hub et FAQ
description: Découvrez la facturation d’Azure App Service sur Azure Stack Hub.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 05/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/13/2020
ms.openlocfilehash: 3717308e28b7dfa62534ee1abd4e71ff06361d50
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847943"
---
# <a name="azure-app-service-on-azure-stack-hub-billing-overview-and-faq"></a>Vue d’ensemble de la facturation d’Azure App Service sur Azure Stack Hub et FAQ

Cet article illustre la façon dont les opérateurs cloud sont facturés pour l’offre Azure App Service sur Azure Stack Hub et dont ils peuvent facturer l’utilisation du service à leurs locataires.

## <a name="billing-overview"></a>Présentation de la facturation

Les opérateurs cloud Azure Stack Hub choisissent de déployer Azure App Service sur Azure Stack Hub sur leur empreinte Azure Stack Hub afin de proposer les fonctionnalités de locataire d’Azure App Service et d’Azure Functions à leurs clients. Le fournisseur de ressources Azure App Service est constitué de plusieurs types de rôles qui peuvent être divisés entre l’infrastructure et les niveaux de worker.

Les rôles d’infrastructure ne sont pas facturés car ils sont obligatoires pour l’opération principale du service. Les rôles d’infrastructure peuvent faire l’objet d’un scale-out si nécessaire pour prendre en charge les demandes des locataires de l’opérateur cloud. Les rôles d’infrastructure sont les suivants :

- Controllers
- Rôles de gestion
- Serveurs de publication
- Front ends

Les niveaux de worker comprennent deux types principaux : partagé et dédié. L’utilisation des workers est facturée à l’abonnement de fournisseur par défaut de l’opérateur cloud en fonction des critères suivants.

## <a name="shared-workers"></a>Workers partagés

Les workers partagés sont des plans App Service partagés multilocataires et sans hôte ainsi que des fonctions Azure basées sur la consommation pour de nombreux locataires. Les workers partagés émettent des compteurs d’utilisation quand ils sont marqués comme prêts dans le fournisseur de ressources Azure App Service.

## <a name="dedicated-workers"></a>Workers dédiés

Les workers dédiés sont liés aux plans App Service que les locataires créent. Par exemple, dans la référence (SKU) S1, les locataires peuvent passer à 10 instances. Quand un locataire crée un plan App Service S1, Azure App Service alloue l’une des instances du groupe identique de niveau de worker Small au plan App Service du locataire. Le worker affecté ne peut plus être affecté à d’autres locataires. Si le locataire choisit de mettre à l’échelle le plan App Service pour qu’il comporte 10 instances, neuf autres workers sont supprimés du pool disponible et sont affectés au plan App Service du locataire.

Les compteurs sont émis pour les workers dédiés quand ils sont :

- Marqués comme prêts dans le fournisseur de ressources Azure App Service.
- Affectés à un plan App Service.

Ce modèle de facturation permet aux opérateurs cloud de provisionner un pool de workers dédiés prêts à être utilisés par les clients sans qu’ils aient à payer pour les workers jusqu’à ce qu’ils soient effectivement réservés par le plan App Service de leur locataire.

Par exemple, supposons que vous ayez 20 workers dans le niveau de worker Small. Si vous avez cinq clients qui créent chacun deux plans App Service S1 et que chacun met à l’échelle le plan App Service pour qu’il comporte deux instances, vous n’avez aucun Worker disponible. Par conséquent, il n’y a pas non plus de capacité pour que l’un de vos clients ou que de nouveaux clients effectuent un scale-out ou créent des plans App Service.

Les opérateurs cloud peuvent afficher le nombre actuel de workers disponibles par niveau de worker en examinant les niveaux de worker dans la configuration d’Azure App Service sur l’administration d’Azure Stack Hub.

![Écran App Service - Niveaux de Worker][1]

## <a name="see-customer-usage-by-using-the-azure-stack-hub-usage-service"></a>Consulter l’utilisation du client à l’aide du service d’utilisation Azure Stack Hub

Les opérateurs cloud peuvent interroger l’[API d’utilisation du locataire Azure Stack Hub](azure-stack-tenant-resource-usage-api.md) pour récupérer les informations d’utilisation de leurs clients. Par ailleurs, vous trouverez tous les compteurs individuels qu’App Service émet pour décrire l’utilisation des locataires dans les [Questions fréquentes (FAQ)](azure-stack-usage-related-faq.md). Ces compteurs servent alors à calculer l’utilisation par abonnement client, laquelle pour calculer les frais.

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="how-do-i-license-the-sql-server-and-file-server-infrastructure-required-in-the-prerequisites"></a>Comment obtenir la licence pour SQL Server et l’infrastructure du serveur de fichiers exigée dans les prérequis ?

La gestion des licences pour SQL Server et l’infrastructure du serveur de fichiers, exigée par le fournisseur de ressources Azure App Service, est traitée ici : [Prérequis pour le déploiement d’App Service sur Azure Stack Hub](azure-stack-app-service-before-you-get-started.md#licensing-concerns-for-required-file-server-and-sql-server).

### <a name="the-usage-faq-lists-the-tenant-meters-but-not-the-prices-for-those-meters-where-can-i-find-them"></a>Les Questions fréquentes (FAQ) sur l’utilisation listent les compteurs de locataire, mais pas les prix de ces compteurs. Où les trouver ?

En tant qu’opérateur cloud, vous êtes libres d’appliquer vos propre modèle tarifaire à vos clients. Le service d’utilisation fournit le contrôle de l’utilisation. Vous pouvez alors utiliser la quantité de compteurs à facturer à vos clients en fonction du modèle tarifaire que vous déterminez. La possibilité de définir les tarifs permet aux opérateurs de se différencier des autres opérateurs Azure Stack Hub.

### <a name="as-a-csp-how-can-i-offer-free-and-shared-skus-for-customers-to-try-out-the-service"></a>En tant que CSP, comment puis-je fournir des références SKU gratuites et partagées aux clients qui souhaitent essayer le service ?

En tant qu’opérateur cloud, des frais vous sont facturés pour fournir des références SKU gratuites et partagées, car ces références sont hébergées dans des workers partagés. Pour réduire ces coûts au minimum, vous pouvez choisir de passer au niveau de worker partagé le plus bas.

> [!IMPORTANT] 
> Les paramètres par défaut du programme d’installation des Workers partagés ont été modifiés dans Azure App Service sur Azure Stack Hub 2020 T2 pour les nouvelles installations.  Par défaut, les Workers partagés sont approvisionnés à l’aide de la référence SKU de calcul A4_v2 qui peut être modifiée par l’opérateur pendant ou après l'installation.

Par exemple, pour fournir des références SKU gratuites et partagées de plan App Service ainsi que des fonctions basées sur la consommation, vous avez besoin d’au moins une instance A1 disponible. Les Workers partagés sont multilocataires ; ils peuvent donc héberger plusieurs applications clientes, qui sont isolées et protégées de manière individuelle par le bac à sable App Service. En ajustant le niveau de worker partagé de cette façon, vous pouvez limiter vos dépenses à un coût de une vCPU par mois.

Vous pouvez ensuite choisir de créer un quota pour une utilisation dans un plan, qui fournit uniquement des références SKU gratuites et partagées et qui limite le nombre de plans App Service gratuits et partagés que votre client est autorisé à créer.

## <a name="sample-scripts-to-assist-with-billing"></a>Exemples de scripts d’aide à la facturation

L’équipe Azure App Service a créé des exemples de scripts PowerShell pour faciliter l’interrogation du service d’utilisation Azure Stack Hub. Les opérateurs cloud peuvent utiliser ces exemples de script afin de préparer leur propre facturation de leurs locataires. Vous trouverez les exemples de scripts dans le [référentiel d’outils Azure Stack Hub](https://github.com/Azure/AzureStack-tools) sur GitHub. Les scripts App Service résident dans le [dossier App Service sous Utilisation](https://aka.ms/aa6zku8).

Voici les exemples de scripts disponibles :

- [Get-AppServiceBillingRecords](https://aka.ms/aa6zku2) : cet exemple extrait les données de facturation d’Azure App Service sur Azure Stack Hub à partir de l’API d’utilisation d’Azure Stack Hub.
- [Get-AppServiceSubscriptionUsage](https://aka.ms/aa6zku6) : cet exemple calcule les quantités utilisées d’Azure App Service sur Azure Stack Hub pour chaque abonnement. Ce script calcule les quantités utilisées en fonction des données reçues de l’API d’utilisation et des prix basés sur la quantité fournis par l’opérateur cloud.
- [Suspend-UserSubscriptions](https://aka.ms/aa6zku7) : cet exemple suspend ou active l’abonnement selon la limite d’utilisation spécifiée par l’opérateur cloud.

## <a name="next-steps"></a>Étapes suivantes

- [API d’utilisation des ressources de locataire Azure Stack Hub](azure-stack-tenant-resource-usage-api.md)

<!--Image references-->
[1]: ./media/app-service-billing-faq/app-service-worker-tiers.png
