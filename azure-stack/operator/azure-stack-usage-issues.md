---
title: Problèmes de connectivité de l’utilisation et erreurs dans Azure Stack | Microsoft Docs
description: Résolution des erreurs et problèmes d’utilisation Azure Stack.
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
ms.date: 06/27/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 57c546ec3583c9e04594e4da542a3c2ce3f72c62
ms.sourcegitcommit: bcaad8b7db2ea596018d973cb29283d8c6daebfb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2019
ms.locfileid: "67419914"
---
# <a name="usage-connectivity-errors"></a>Erreurs de connectivité d’utilisation

Les données d’utilisation Azure Stack sont envoyées à Azure par le composant [*Azure Bridge* ](azure-stack-usage-reporting.md)dans Azure Stack. Si le pont dans Azure Stack ne peut pas se connecter au service d’utilisation d’Azure, vous verrez l’erreur suivante :

![Erreur d’utilisation du pont](media/azure-stack-usage-issues/usageerror2.png)

La fenêtre peut fournir plus d’informations sur l’erreur et sa résolution :

![résolution de l’erreur](media/azure-stack-usage-issues/usageerror3.png)

## <a name="resolve-connectivity-issues"></a>Résoudre les problèmes de connectivité

Pour limiter les effets de ce problème, essayez d’effectuer les étapes suivantes :

- Vérifiez que la configuration réseau autorise Azure Bridge à se connecter au service distant.

- Accédez au panneau [**Gestion des régions** > **Propriétés**](azure-stack-registration.md#verify-azure-stack-registration) pour rechercher l’ID d’abonnement Azure utilisé pour l’inscription, le groupe de ressources et le nom de la ressource d’inscription. Vérifier que la ressource d’inscription existe sous l’ID d’abonnement Azure approprié dans le portail Azure. Pour ce faire, accédez à **Toutes les ressources** créées sous l’ID d’abonnement Azure et vérifiez la zone **Afficher les types masqués**. Si vous ne trouvez pas la ressource d’inscription, suivez les étapes de la section [Renouveler ou modifier l’inscription](azure-stack-registration.md#renew-or-change-registration) pour réinscrire votre système Azure Stack.

  ![Portail](media/azure-stack-usage-issues/stackres.png)

## <a name="error-codes"></a>Codes d’erreur

Cette section décrit la représentation les codes d’erreur d’utilisation.

| Code d'erreur                 | Problème                                                                                                                                             | Correction                                                                                                                                                                                                                                                                                        |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NetworkError               | Azure Stack Bridge ne peut pas envoyer de requête au point de terminaison du service d’utilisation dans Azure.                                                            | Vérifiez si un proxy bloque ou intercepte l’accès au point de terminaison de service de l’utilisation.                                                                                                                                                                                                             |
| RequestTimedOut            | La requête a été envoyée depuis Azure Bridge, mais le service d’utilisation dans Azure n’a pas répondu dans le délai imparti.                             | Vérifiez si un proxy bloque ou intercepte l’accès au point de terminaison de service de l’utilisation.                                                                                                                                                                                                                        |
| LoginError                 | Impossible de s’authentifier auprès de Microsoft Azure Active Directory.                                                                                                             | Vérifiez que le point de terminaison de connexion Azure AD est accessible à partir de toutes les machines virtuelles XRP dans Azure Stack.                                                                                                                                                                                                                     |
| CertificateValidationError | Azure Bridge n’est pas en mesure d’envoyer la requête, car il n’est pas en mesure de s’authentifier auprès du service Azure.                                    | Vérifiez si un proxy intercepte le trafic HTTPS entre l’ordinateur Azure Stack XRP et le point de terminaison de la passerelle d’utilisation.                                                                                                                                                                                      |
| Non autorisé               | Azure Bridge n’est pas en mesure de transmettre les données au service d’utilisation dans Azure, car le service Azure n’est pas en mesure d’authentifier Azure Stack Bridge. | Vérifiez si la ressource d’inscription a été modifiée et si tel est le cas, réinscrivez Azure Stack. <br><br> Parfois, un problème de synchronisation entre Azure Stack et Azure AD peut provoquer cette défaillance. Dans ce cas, vérifiez que les heures sur les machines virtuelles XRP sur Azure Stack sont synchronisées avec celles d’Azure AD. |
|                            |                                                                                                                                                   |                                                                                                                                                                                                                                                                                                    |

En outre, vous devrez peut-être fournir les fichiers journaux pour les composants Azure Bridge, WAS et WASPublic en suivant [ces étapes](azure-stack-diagnostics.md#log-collection-tool).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez-en plus sur la [communication de données d’utilisation d’Azure Stack à Azure](azure-stack-usage-reporting.md).
- Pour consulter les messages d’erreur s’ils sont déclenchés dans votre processus d’inscription, consultez [Code d’erreur d’utilisation et de facturation](azure-stack-registration-errors.md).
- Plus d’informations sur les [Infrastructure des rapports d’utilisation pour les fournisseurs de services Cloud](azure-stack-csp-ref-infrastructure.md).
