---
title: Utiliser le portail administrateur dans Azure Stack Hub
description: Découvrez comment utiliser le portail administrateur dans Azure Stack Hub.
author: justinha
ms.topic: quickstart
ms.date: 06/07/2019
ms.author: justinha
ms.reviewer: efemmano
ms.lastreviewed: 06/07/2019
ms.openlocfilehash: 358837da19ad34f82f81a94da0c89b165574ea49
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77699250"
---
# <a name="use-the-administrator-portal-in-azure-stack-hub"></a>Utiliser le portail administrateur dans Azure Stack Hub

Il existe deux portails dans Azure Stack Hub : le portail administrateur et le portail utilisateur. En tant qu’opérateur Azure Stack Hub, vous utilisez le portail administrateur pour la gestion et les opérations quotidiennes d’Azure Stack Hub.

## <a name="access-the-administrator-portal"></a>Accéder au portail administrateur

Pour accéder au portail administrateur, accédez à l’URL du portail, puis connectez-vous en utilisant les informations d’identification de votre opérateur Azure Stack Hub. Pour un système intégré, l’URL du portail dépend du nom de la région et du nom de domaine complet (FQDN) externe de votre déploiement d’Azure Stack Hub. L’URL du portail administrateur est toujours le même pour les déploiements du Kit de développement Azure Stack (ASDK).

| Environnement | URL du portail administrateur |   
| -- | -- | 
| ASDK| https://adminportal.local.azurestack.external  |
| Systèmes intégrés | https://adminportal.&lt;*region*&gt;.&lt;*FQDN*&gt; | 
| | |

> [!TIP]
> Dans un environnement ASDK, vous devez d’abord vérifier que vous pouvez [vous connecter à l’hôte du Kit de développement](../asdk/asdk-connect.md) via Connexion Bureau à distance ou un réseau privé virtuel (VPN).

 ![Portail administrateur Azure Stack Hub](media/azure-stack-manage-portals/admin-portal.png)

Le fuseau horaire par défaut pour tous les déploiements Azure Stack Hub est défini sur le Temps universel coordonné (UTC).

Dans le portail d’administration, vous pouvez effectuer ces opérations :

* [Inscrire Azure Stack Hub auprès d’Azure](azure-stack-registration.md)
* [Renseigner la Place de marché](azure-stack-download-azure-marketplace-item.md)
* [Créer des plans, offres et abonnements pour les utilisateurs](service-plan-offer-subscription-overview.md)
* [Surveiller l’intégrité et des alertes](azure-stack-monitor-health.md)
* [Gérer les mises à jour Azure Stack Hub](azure-stack-updates.md)

La vignette du **tutoriel de démarrage rapide** fournit des liens vers de la documentation en ligne pour les tâches les plus courantes.

Bien qu’un opérateur puisse créer des ressources telles que des machines virtuelles, des réseaux virtuels et des comptes de stockage dans le portail administrateur, vous devez [vous connecter au portail utilisateur](../user/azure-stack-use-portal.md) pour créer et tester des ressources.

>[!NOTE]
>Le lien **Créer une machine virtuelle** dans la mosaïque des tutoriels de démarrage rapide vous amène à créer une machine virtuelle dans le portail administrateur, mais cette opération vise uniquement à vérifier qu’Azure Stack Hub a bien été déployé.

## <a name="understand-subscription-behavior"></a>Comprendre le comportement de l’abonnement

Il existe trois abonnements créés par défaut dans le portail administrateur : consommation, fournisseur par défaut et contrôle. En tant qu’opérateur, vous utiliserez principalement l’*abonnement Fournisseur par défaut*. Vous ne pouvez pas ajouter d’autres abonnements et les utiliser dans le portail administrateur.

Les autres abonnements sont créés par les utilisateurs dans le portail utilisateur en fonction des plans et des offres qui leur sont proposés. Toutefois, le portail utilisateur ne fournit pas d’accès aux fonctionnalités d’administration ou d’exploitation du portail administrateur.

Les portails administrateur et utilisateur sont secondés par des instances distinctes d’Azure Resource Manager. En raison de cette séparation d’Azure Resource Manager, les abonnements ne sont pas partagés entre les portails. Par exemple, si vous-même, en tant qu’opérateur Azure Stack Hub, vous vous connectez au portail utilisateur, vous ne pouvez pas accéder à l’*abonnement Fournisseur par défaut*. Bien que vous n’ayez accès à aucune fonction d’administration, vous pouvez créer des abonnements pour vous-même à partir d’offres publiques disponibles. Tant que vous êtes connecté au portail utilisateur, vous êtes considéré comme un utilisateur de locataire.

  >[!NOTE]
  >Dans un environnement ASDK, si un utilisateur appartient au même annuaire de locataire que l’opérateur Azure Stack Hub, il peut en théorie se connecter au portail administrateur. Cependant, il ne pourra pas accéder aux fonctions administratives ni ajouter d’abonnements pour accéder aux offres mises à sa disposition dans le portail utilisateur.

## <a name="administrator-portal-tips"></a>Conseils pour le portail administrateur

### <a name="customize-the-dashboard"></a>Personnaliser le tableau de bord

Le tableau de bord contient une série de vignettes par défaut. Vous pouvez sélectionner **Modifier le tableau de bord** pour modifier le tableau de bord par défaut, ou **Nouveau tableau de bord** pour ajouter un tableau de bord personnalisé. Vous pouvez également ajouter des vignettes à un tableau de bord. Par exemple, sélectionnez **+ Create a resource** (+ Créer une ressource), cliquez avec le bouton droit sur **Offers + Plans** (Offres + plans), puis sélectionnez **Pin to dashboard** (Épingler au tableau de bord).

Parfois, il se peut qu’un tableau de bord vide s’affiche dans le portail. Pour récupérer le tableau de bord, cliquez sur **Modifier le tableau de bord**, puis cliquez avec le bouton droit sur **Rétablir l’état par défaut**.

### <a name="quick-access-to-online-documentation"></a>Accès rapide à la documentation en ligne

Pour accéder à la documentation de l’opérateur Azure Stack Hub, utilisez l’icône d’aide et de support (point d’interrogation) située dans le coin supérieur droit du portail d’administration. Déplacez votre curseur vers l’icône, puis sélectionnez **Aide + support**.

### <a name="quick-access-to-help-and-support"></a>Accès rapide à l’aide et au support

Si vous cliquez successivement sur l’icône d’aide (point d’interrogation) dans le coin supérieur droit du portail d’administration, sur **Aide + support**, puis sur **Nouvelle demande de support** sous **Support**, vous obtenez l’un des résultats suivants :

- Si vous utilisez un système intégré, cette action ouvre un site sur lequel vous pouvez ouvrir directement un ticket de support auprès des services de support technique Microsoft. Reportez-vous à la section [Où obtenir un support technique ?](azure-stack-manage-basics.md#where-to-get-support) pour comprendre quand vous devez vous adresser au support technique Microsoft ou au support technique de votre fournisseur de matériel OEM.
- Si vous utilisez le Kit ASDK, cette action ouvre directement le [site des forums Azure Stack Hub](https://social.msdn.microsoft.com/Forums/home?forum=AzureStack). Ces forums sont consultés régulièrement. Le Kit ASDK étant un environnement d’évaluation, les services de support technique Microsoft n’assurent aucun support officiel.

### <a name="quick-access-to-the-azure-roadmap"></a>Accès rapide à la feuille de route Azure

Si vous sélectionnez **Aide et support** (le point d’interrogation) dans le coin supérieur droit du portail de l’administrateur et que vous sélectionnez **Feuille de route Azure**, un nouvel onglet de navigateur s’ouvre et vous amène à la feuille de route Azure. En tapant **Azure Stack Hub** dans la zone de recherche **Produits**, vous pouvez voir toutes les mises à jour de feuille de route Azure Stack Hub.

## <a name="next-steps"></a>Étapes suivantes

[Inscrivez Azure Stack Hub auprès d’Azure](azure-stack-registration.md) et renseignez [Place de marché Azure Stack Hub](azure-stack-marketplace.md) avec les éléments que vous proposez à vos utilisateurs.
