---
title: Facturation et paiement dans Azure Stack HCI
description: Présentation de la facturation et du paiement dans Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 32c3a0305775c9825e27fe3e8a99911b297a129c
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86868031"
---
# <a name="azure-stack-hci-billing-and-payment"></a>Facturation et paiement dans Azure Stack HCI

> S’applique à : Azure Stack HCI, version 20H2

Azure Stack HCI est un service Azure qui apparaît sur la facture de votre abonnement Azure comme n’importe quel autre service Azure. Aucune licence logicielle locale traditionnelle n’est requise. Toutefois, les machines virtuelles invitées peuvent nécessiter des licences de système d’exploitation individuelles. Les devises et les remises sont gérées de manière centralisée par la plateforme de facturation Azure Commerce, et le client reçoit une facture détaillée unifiée à la fin du mois.

## <a name="what-does-azure-stack-hci-charge-for"></a>Que facture Azure Stack HCI ?

Pour les clients utilisant déjà Azure ou tout autre service cloud, le modèle de facturation de type cloud d’Azure Stack HCI est simple, cohérent et connu. Aucuns frais ne sont facturés pendant la préversion publique. Une fois la préversion terminée, la facturation est basée sur un taux forfaitaire par cœur de processeur physique dans un cluster Azure Stack HCI (des frais d’utilisation supplémentaires s’appliquent si vous utilisez d’autres services Azure).

Le modèle de tarification est différent d’une facturation basée sur l’utilisation ou sur la consommation reposant sur le nombre de machines virtuelles. Bien que le nombre de cœurs de processeur virtuel (VCPU) puisse varier au cours du mois, il n’affecte pas le prix que vous payez pour Azure Stack HCI : votre facture reflète toujours le nombre de cœurs physiques présents dans le cluster.

## <a name="advantages-of-the-azure-stack-hci-billing-model"></a>Avantages du modèle de facturation d’Azure Stack HCI

- C’est simple : il n’y a pas de minimum, pas de maximum et pas de calcul faisant intervenir la mémoire, le stockage ou les entrées/sorties réseau.
- Ce modèle récompense les clients qui exécutent leur infrastructure de virtualisation de manière efficace, avec une densité virtuelle/physique supérieure.
- Vous pouvez facilement déterminer le coût de votre déploiement local d’Azure Stack HCI, car les coûts suivent une échelle prévisible de la périphérie du réseau au centre de données.

## <a name="how-the-number-of-processor-cores-is-assessed"></a>Évaluation du nombre de cœurs de processeur

Pour déterminer le nombre de cœurs présents dans un cluster, Azure Stack HCI examine périodiquement le nombre de cœurs physiques et les signale à Azure. Ne vous inquiétez pas si vous n’êtes connecté qu’occasionnellement ou si votre connexion est interrompue ; cette opération peut se faire plus tard. Vous pouvez ainsi charger plusieurs jours ou semaines de données de base à la fois. Les clients doivent se connecter à Azure au moins une fois tous les 30 jours pour qu’une facture puisse être établie.

Pour charger manuellement des données de base dans Azure, utilisez l’applet de commande **`Sync-AzureStackHCI`** .

## <a name="faq"></a>Questions fréquentes (FAQ)

- J’ai déjà un abonnement Azure. Puis-je l’utiliser pour Azure Stack HCI ? **Oui**
- Le service des finances de mon organisation a déjà approuvé mon budget Azure. Cela couvre-t-il Azure Stack HCI ? **Oui**
- J’ai un engagement Azure à dépenser. Puis-je l’utiliser pour payer Azure Stack HCI ? **Oui**
- J’ai des crédits Azure (que j’ai reçus en tant qu’étudiant ou sous forme de récompense). Puis-je les utiliser pour payer Azure Stack HCI ? **Oui**
- Mon organisation a négocié une remise Accord Entreprise. Celle-ci s’applique-t-elle à Azure Stack HCI ? **Oui**
- Les outils de gestion des coûts du portail Azure fonctionnent-ils avec Azure Stack HCI ? **Oui**
- Les outils tiers ou personnalisés créés avec les API de facturation Azure fonctionnent-ils avec Azure Stack HCI ? **Oui**

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez également :

- [Vue d’ensemble des prix dans Azure](https://azure.microsoft.com/pricing/)
- [Vue d’ensemble d’Azure Cost Management et de la facturation](/azure/cost-management-billing/cost-management-billing-overview)
