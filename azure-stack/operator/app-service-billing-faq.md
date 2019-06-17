---
title: Vue d’ensemble de la facturation d’Azure App Service sur Azure Stack et questions fréquentes (FAQ) | Microsoft Docs
description: Informations sur le mode de calcul et de facturation d’Azure App Service sur Azure Stack.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: fa3bc647d11bca915c58aa1bd948881628405776
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828365"
---
# <a name="azure-app-service-on-azure-stack-billing-overview-and-faq"></a>Vue d’ensemble de la facturation d’Azure App Service sur Azure Stack et questions fréquentes (FAQ)

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Les instructions de ce document illustrent la façon dont les opérateurs cloud sont facturés pour l’offre Azure App Service sur Azure Stack et dont ils peuvent, à leur tour, facturer leur utilisation du service à leurs locataires.

## <a name="billing-overview"></a>Présentation de la facturation

Les opérateurs cloud Azure Stack choisissent de déployer Azure App Service sur Azure Stack sur leur tampon Azure Stack afin de proposer les fonctionnalités de locataire d’Azure App Service et d’Azure Functions à leurs clients.  Le fournisseur de ressources Azure App Service est constitué de plusieurs types de rôles qui peuvent être divisés entre l’infrastructure et les niveaux de worker.

Les rôles d’infrastructure ne sont pas facturés car ils sont obligatoires pour l’opération principale du service.  Les rôles d’infrastructure peuvent faire l’objet d’un scale-out si nécessaire pour prendre en charge les demandes des locataires de l’opérateur cloud.  Les rôles d’infrastructure sont les suivants :

- Controllers
- Rôles de gestion
- Éditeurs
- Front ends

Les niveaux de worker comprennent deux types principaux : partagé et dédié. L’utilisation des workers est facturée à l’opérateur cloud en fonction des critères suivants.

## <a name="shared-workers"></a>Workers partagés

Les workers partagés sont des plans App Service partagés multilocataires et sans hôte ainsi que des fonctions Azure Functions basées sur la consommation pour de nombreux locataires. Les workers partagés émettent des compteurs d’utilisation quand ils sont marqués comme prêts dans le fournisseur de ressources Azure App Service.

## <a name="dedicated-workers"></a>Workers dédiés

Les workers dédiés sont liés aux plans App Service que le ou les locataires créent. Par exemple, dans la référence (SKU) S1, les locataires peuvent, par défaut, passer à 10 instances. Par conséquent, quand un locataire crée un plan App Service S1, Azure App Service alloue l’une des instances du groupe identique de niveau de worker Small au plan App Service du locataire. Par la suite, le worker affecté ne peut plus être affecté à un autre locataire.  Si le locataire choisit de mettre à l’échelle le plan App Service pour qu’il comporte 10 instances, 9 autres workers sont supprimés du pool disponible et sont affectés au plan App Service du locataire.

Les compteurs sont émis pour les workers dédiés quand ils sont :

- Marqués comme prêts dans le fournisseur de ressources Azure App Service.
- Affectés à un plan App Service.

Par conséquent, ce modèle de facturation permet aux opérateurs cloud de provisionner un pool de workers dédiés prêts à être utilisés par les clients sans qu’ils aient à payer pour les workers jusqu’à ce qu’ils soient effectivement réservés par le plan App Service de leur locataire. Par exemple, si vous avez 20 workers dans le niveau de Worker Small, que vous avez 5 clients qui créent chacun deux plans App Service S1 et que chacun met à l’échelle le plan App Service pour qu’il comporte deux instances, vous n’aurez aucun worker disponible. Par conséquent, il n’y aura pas non plus de capacité pour que l’un de vos clients ou que de nouveaux clients effectuent un scale-out ou créent des plans App Service. Les opérateurs cloud peuvent afficher le nombre actuel de workers disponibles par niveau de worker en examinant les niveaux de worker dans la configuration d’Azure App Service sur l’administration d’Azure Stack.

![Niveaux de Worker App Service][1]

## <a name="see-customer-usage-using-the-azure-stack-usage-service"></a>Consulter l’utilisation du client à l’aide du service d’utilisation Azure Stack

Les opérateurs cloud peuvent interroger l’[API d’utilisation du locataire Azure Stack](azure-stack-tenant-resource-usage-api.md) pour récupérer les informations d’utilisation de leurs clients. Par ailleurs, vous trouverez tous les compteurs individuels qu’App Service émet pour décrire l’utilisation des locataires dans les [Questions fréquentes (FAQ)](azure-stack-usage-related-faq.md). Ces compteurs peuvent alors servir à calculer l’utilisation par abonnement client, laquelle peut ensuite être utilisée pour calculer les frais.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="how-do-i-license-the-sql-server-and-file-server-infrastructure-required-in-the-pre-requisites"></a>Comment obtenir la licence pour SQL Server et l’infrastructure du serveur de fichiers exigée dans les prérequis ?

La gestion des licences pour SQL et l’infrastructure du serveur de fichiers, exigée par le fournisseur de ressources Azure App Service, est traitée dans l’article Azure App Service sur Azure Stack : [Avant de commencer](azure-stack-app-service-before-you-get-started.md#licensing-concerns-for-required-file-server-and-sql-server).

### <a name="the-usage-faq-lists-the-tenant-meters-but-not-the-prices-for-those-meters-where-can-i-find-them"></a>Les Questions fréquentes (FAQ) sur l’utilisation listent les compteurs de locataire, mais pas les prix de ces compteurs, où les trouver ?

Les opérateurs cloud sont libres d’appliquer leur propre modèle tarifaire à leurs clients finaux. Le service d’utilisation fournit le contrôle de l’utilisation. L’opérateur cloud doit alors utiliser la quantité de compteurs à facturer à ses clients en fonction du modèle tarifaire qu’il détermine. Avoir la possibilité de définir les tarifs permet aux opérateurs de se différencier des autres opérateurs Azure Stack.

### <a name="as-a-csp-how-can-i-offer-free-and-shared-skus-for-customers-to-trial-the-service"></a>En tant que CSP, comment puis-je fournir des références SKU gratuites et partagées aux clients qui souhaitent essayer le service ?

En tant qu’opérateur cloud, des frais vous seront facturés pour fournir des références SKU gratuites et partagées, comme nous le faisons dans Azure, car ces références sont hébergées dans des Workers partagés.  Afin de réduire ces coûts au minimum, vous pouvez choisir de passer au niveau de Worker partagé le plus bas.  Par exemple, pour fournir des références SKU gratuites et partagées de plan App Service ainsi que des fonctions basées sur la consommation, vous devez avoir au moins une instance A1 disponible.  Les Workers partagés sont multilocataires ; ils peuvent donc héberger plusieurs applications clientes, qui sont isolées et protégées de manière individuelle par le bac à sable App Service.  En ajustant le niveau de Worker partagé de cette façon, vous pouvez limiter vos dépenses à un coût de 1vCPU par mois.

De plus, vous pouvez ensuite choisir de créer un quota, pour une utilisation dans un plan, qui fournit uniquement des références SKU gratuites et partagées et qui limite le nombre de plans App Service gratuits et partagés que votre client est autorisé à créer.

## <a name="sample-scripts-to-assist-with-billing"></a>Exemples de scripts d’aide à la facturation

L’équipe Azure App Service a créé des exemples de scripts PowerShell pour faciliter l’interrogation du service d’utilisation Azure Stack, et aider ainsi les opérateurs cloud à préparer leur propre facturation de leurs locataires.  Vous trouverez les exemples de scripts dans le [dépôt d’outils Azure Stack](https://github.com/Azure/AzureStack-tools) sur GitHub, et les scripts App Service dans le [dossier App Service sous Utilisation](https://github.com/Azure/AzureStack-Tools/tree/master/Usage/AppService).

Voici les exemples de scripts disponibles :

- [Get-AppServiceBillingRecords](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Get-AppServiceBillingRecords.ps1) : cet exemple extrait les données de facturation d’Azure App Service sur Azure Stack à partir de l’API d’utilisation d’Azure Stack
- [Get-AppServiceSubscriptionUsage](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Get-AppServiceSubscriptionUsage.ps1) : cet exemple calcule les quantités utilisées d’Azure App Service sur Azure Stack pour chaque abonnement.  Ce script calcule les quantités utilisées en fonction des données reçues de l’API d’utilisation et des prix basés sur la quantité fournis par l’opérateur cloud.
- [Suspend-UserSubscriptions](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Suspend-UserSubscriptions.ps1) : cet exemple suspend ou active l’abonnement selon la limite d’utilisation spécifiée par l’opérateur cloud.

## <a name="next-steps"></a>Étapes suivantes

- [API d’utilisation du locataire Azure Stack](azure-stack-tenant-resource-usage-api.md)

<!--Image references-->
[1]: ./media/app-service-billing-faq/app-service-worker-tiers.png