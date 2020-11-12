---
title: Signaler les données d'utilisation Azure Stack Hub à Azure
titleSuffix: Azure Stack Hub
description: Découvrez comment signaler les données d’utilisation Azure Stack Hub à Azure.
services: azure-stack
documentationcenter: ''
author: sethmanheim
ms.topic: article
ms.date: 11/09/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: e11eedc6cade8b3546182d2abf66a0dbb7e1c91a
ms.sourcegitcommit: 980be7813e6f39fb59926174a5d3e0d392b04293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414212"
---
# <a name="report-azure-stack-hub-usage-data-to-azure"></a>Signaler les données d'utilisation Azure Stack Hub à Azure

Les données d’utilisation, également appelées « données de consommation », représentent la quantité de ressources utilisées.

Les systèmes Azure Stack Hub à plusieurs nœuds qui utilisent un modèle de facturation basé sur la consommation doivent communiquer les données d'utilisation à Azure à des fins de facturation. Les opérateurs Azure Stack Hub doivent configurer leur instance d'Azure Stack Hub pour communiquer les données d'utilisation à Azure.

> [!IMPORTANT]
> Toutes les charges de travail [doivent être déployées dans le cadre d'abonnements de locataire](#are-users-charged-for-the-infrastructure-vms), conformément aux termes du contrat de licence d'Azure Stack Hub.

Les rapports relatifs aux données d’utilisation sont nécessaires aux utilisateurs des systèmes Azure Stack Hub à plusieurs nœuds qui attribuent des licences selon le modèle de paiement à l’utilisation. Ceci est facultatif pour les clients qui attribuent des licences sous le modèle de capacité (voir [Comment acheter Azure Stack](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)). Pour les utilisateurs du Kit de développement Azure Stack (ASDK), les opérateurs Azure Stack Hub peuvent communiquer des données d'utilisation et tester la fonctionnalité. Toutefois, l’utilisation qu’elles impliquent n’est pas facturée aux utilisateurs.

![Flux de facturation pour les données d’utilisation dans Azure Stack Hub](media/azure-stack-usage-reporting/billing-flow.svg)

Les données d'utilisation sont envoyées d'Azure Stack Hub à Azure via Azure Bridge. Dans Azure, le système de commerce traite les données d’utilisation et génère la facture. Une fois la facture générée, le propriétaire de l’abonnement Azure peut l’afficher et la télécharger à partir du [Centre des comptes Azure](https://account.windowsazure.com/subscriptions). Pour savoir comment Azure Stack Hub est concédé sous licence, consultez le [document relatif à l’empaquetage et aux tarifs d’Azure Stack Hub](https://go.microsoft.com/fwlink/?LinkId=842847).

## <a name="set-up-usage-data-reporting"></a>Configurer la génération de rapports de données d’utilisation

Pour configurer la génération de rapports sur les données d'utilisation, vous devez [inscrire votre instance Azure Stack Hub auprès d'Azure](azure-stack-registration.md). Le composant Azure Bridge d’Azure Stack Hub est configuré dans le cadre du processus d’inscription. Le composant Azure Bridge connecte Azure Stack Hub à Azure. Les données d'utilisation suivantes sont envoyées d'Azure Stack Hub à Azure :

- **ID du compteur**  : ID unique de la ressource consommée.
- **Quantité** : quantité d’utilisation des ressources.
- **Emplacement**  : emplacement où la ressource Azure Stack Hub actuelle est déployée.
- **URI de ressource**  : URI complet de la ressource pour laquelle l’utilisation est signalée.
- **ID d'abonnement**  : ID d'abonnement de l'utilisateur Azure Stack Hub, qui est l'abonnement (Azure Stack Hub) local.
- **Heure**  : heure de début et de fin des données d’utilisation. Il existe un décalage entre le moment où ces ressources sont utilisées dans Azure Stack Hub et celui où les données d'utilisation sont communiquées au commerce. Azure Stack Hub agrège les données d'utilisation toutes les 24 heures, et la communication des données d'utilisation au pipeline Commerce d'Azure prend encore plusieurs heures. Par conséquent, l’utilisation qui se produit peu de temps avant minuit peut s’afficher dans Azure le jour suivant.

## <a name="generate-usage-data-reporting"></a>Générer des rapports de données d’utilisation

- Pour tester la génération de rapports sur les données d'utilisation, créez quelques ressources dans Azure Stack Hub. Par exemple, vous pouvez créer un [compte de stockage](azure-stack-provision-storage-account.md), une [machine virtuelle Windows Server](../user/azure-stack-create-vm-template.md) et une machine virtuelle Linux avec des références (SKU) De base et Standard pour voir comment l’utilisation des cœurs est signalée. Les données d’utilisation de différents types de ressources sont signalées sous différents compteurs.

- Laissez vos ressources s’exécuter pendant quelques heures. Les informations d’utilisation sont collectées environ une fois par heure. Après avoir été collectées, ces données sont transmises à Azure et traitées dans le système de commerce Azure. Ce processus peut prendre plusieurs heures.

## <a name="view-usage---csp-subscriptions"></a>Voir l’utilisation : abonnements CSP

Si vous avez inscrit votre service Azure Stack Hub via un abonnement CSP, vous pouvez consulter votre utilisation et vos frais de la même façon que pour votre consommation Azure. L’utilisation d’Azure Stack Hub est incluse dans votre facture et dans le fichier de rapprochement, qui est disponible dans l’[Espace partenaires](https://partnercenter.microsoft.com/partner/home). Le fichier de réconciliation est mis à jour tous les mois. Si vous avez besoin d'accéder à des informations récentes sur l'utilisation d'Azure Stack Hub, vous pouvez utiliser les API de l'Espace partenaires.

![Afficher les données de facturation et d’utilisation d’Azure Stack Hub dans l’Espace partenaires Microsoft](media/azure-stack-usage-reporting/partner-center.png)

## <a name="view-usage---enterprise-agreement-subscriptions"></a>Voir l’utilisation : abonnements Contrat Entreprise

Si vous avez inscrit votre service Azure Stack Hub par le biais d'un abonnement Accord Entreprise, vous pouvez consulter votre utilisation et vos frais sur le [portail EA](https://ea.azure.com/). L'utilisation d'Azure Stack Hub est incluse dans les téléchargements avancés ; l'utilisation d'Azure figure sous la section Rapports de ce portail.

## <a name="view-usage---other-subscriptions"></a>Voir l’utilisation : autres abonnements

Si vous avez inscrit votre service Azure Stack Hub par le biais d’un autre type d’abonnement (par exemple, un abonnement avec paiement à l’utilisation), vous pouvez consulter l’utilisation et les frais dans le Centre des comptes Azure. Connectez-vous au [Centre des comptes Azure](https://account.windowsazure.com/subscriptions) en tant qu’administrateur de compte Azure, puis sélectionnez l’abonnement Azure que vous avez utilisé pour inscrire Azure Stack Hub. Vous pouvez consulter les données d’utilisation Azure Stack Hub et le montant facturé pour chacune des ressources utilisées, comme illustré ci-dessous :

![Afficher les flux de facturation et d’utilisation dans le Centre des comptes Azure](media/azure-stack-usage-reporting/pricing-details.png)

Pour le kit ASDK, les ressources Azure Stack Hub ne sont pas facturées. Par conséquent, le prix affiché est de 0,00 $.

## <a name="which-azure-stack-hub-deployments-are-charged"></a>Quels sont les déploiements Azure Stack Hub facturés ?

L’utilisation des ressources est gratuite pour l’ASDK. Pour les systèmes Azure Stack Hub à plusieurs nœuds, les machines virtuelles de charge de travail, les services de stockage et App Services sont facturés.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Les utilisateurs sont-ils facturés pour les machines virtuelles d’infrastructure ?

Non. Les données d'utilisation de certaines machines virtuelles du fournisseur de ressources Azure Stack Hub sont communiquées à Azure, mais rien n'est facturé pour ces machines virtuelles ni pour celles créées pendant le déploiement pour activer l'infrastructure Azure Stack Hub.  

Les utilisateurs sont facturés seulement pour les machines virtuelles qui s’exécutent sous les abonnements du locataire. Toutes les charges de travail doivent être déployées dans le cadre d'abonnements de locataire, conformément aux termes du contrat de licence d'Azure Stack Hub.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-hub-how-do-i-do-it"></a>Je dispose d'une licence Windows Server que je souhaite utiliser sur Azure Stack Hub. Comment dois-je procéder ?

L’utilisation des licences existantes évite la génération de compteurs d’utilisation. Les licences Windows Server existantes peuvent être utilisées dans Azure Stack Hub. Ce processus est décrit à la section « Utilisation de logiciels existants avec Azure Stack Hub » du [Guide de gestion des licences Azure Stack Hub](https://go.microsoft.com/fwlink/?LinkId=851536). Pour pouvoir utiliser leurs licences existantes, les clients doivent déployer leurs machines virtuelles Windows Server comme décrit dans l’article [Hybrid Benefit pour la licence Windows Server](/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Quel abonnement est facturé pour la consommation de ressources ?

L’abonnement fourni lors de l’[inscription d’Azure Stack Hub auprès d’Azure](azure-stack-registration.md) est facturé.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Quels types d’abonnements sont pris en charge pour la génération de rapports de données d’utilisation ?

Pour les systèmes Azure Stack Hub à plusieurs nœuds, les abonnements Accord Entreprise (EA) et CSP sont pris en charge. Pour l’ASDK, les abonnements Contrat Entreprise, Paiement à l’utilisation, CSP et MSDN prennent en charge les rapports de données d’utilisation.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>La génération de rapports de données d’utilisation fonctionne-t-elle avec les clouds souverains ?

Dans l’ASDK, la génération de rapports d’utilisation des données exige que les abonnements soient créés dans le système Azure global. Les abonnements créés dans l’un des clouds souverains (clouds Azure Government, Azure Allemagne et Azure Chine 21Vianet) ne peuvent pas être inscrits auprès d’Azure. Ils ne prennent donc pas en charge les rapports de données d’utilisation.

## <a name="why-doesnt-the-usage-reported-in-azure-stack-hub-match-the-report-generated-from-azure-account-center"></a>Pourquoi l'utilisation signalée dans Azure Stack Hub ne correspond-elle pas au rapport généré à partir du Centre des comptes Azure ?

Il existe toujours un décalage entre le moment où les données d’utilisation sont communiquées par les API d’utilisation Azure Stack Hub et celui où elles sont communiquées dans le Centre des comptes Azure. Ce décalage correspond au délai nécessaire pour charger les données d'utilisation d'Azure Stack Hub vers le commerce Azure. En raison de ce décalage, l’utilisation qui se produit peu de temps avant minuit peut s’afficher dans Azure le jour suivant. Si vous utilisez les [API d’utilisation d’Azure Stack Hub](azure-stack-provider-resource-api.md) et que vous comparez les résultats avec l’utilisation signalée sur le portail de facturation Azure, vous pouvez constater une différence.

## <a name="next-steps"></a>Étapes suivantes

- [API d’utilisation du fournisseur](azure-stack-provider-resource-api.md)  
- [API d’utilisation du locataire](azure-stack-tenant-resource-usage-api.md)
- [Forum Aux Questions sur l’utilisation](azure-stack-usage-related-faq.md)
- [Gérer l’utilisation et la facturation en tant que fournisseur de solutions cloud](azure-stack-add-manage-billing-as-a-csp.md)
