---
title: Problèmes et erreurs de connectivité liés à l’utilisation d’Azure Stack Hub
description: Résolution des problèmes et erreurs liés à l'utilisation d'Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 79f34d685998a6719bc580504d4d26a8e4be093f
ms.sourcegitcommit: 2377c6947cf846fd2a4a0274c41326293a2a239c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77075987"
---
# <a name="usage-connectivity-errors"></a>Erreurs de connectivité d’utilisation

Les données d’utilisation d’Azure Stack Hub sont envoyées à Azure par le composant [*Azure Bridge*](azure-stack-usage-reporting.md) fourni dans Azure Stack Hub. Si le pont disponible dans Azure Stack Hub ne parvient pas à se connecter au service d'utilisation Azure, l'erreur suivante s'affiche :

![Erreur d’utilisation du pont](media/azure-stack-usage-issues/usageerror2.png)

La fenêtre peut fournir plus d’informations sur l’erreur et sa résolution :

![résolution de l’erreur](media/azure-stack-usage-issues/usageerror3.png)

## <a name="resolve-connectivity-issues"></a>Résoudre les problèmes de connectivité

Pour limiter les effets de ce problème, essayez d’effectuer les étapes suivantes :

- Vérifiez que la configuration réseau autorise Azure Bridge à se connecter au service distant.

- Accédez au panneau [**Gestion des régions** > **Propriétés**](azure-stack-registration.md#verify-azure-stack-hub-registration) pour rechercher l’ID d’abonnement Azure utilisé pour l’inscription, le groupe de ressources et le nom de la ressource d’inscription. Vérifier que la ressource d’inscription existe sous l’ID d’abonnement Azure approprié dans le portail Azure. Pour ce faire, accédez à **Toutes les ressources** créées sous l’ID d’abonnement Azure et vérifiez la zone **Afficher les types masqués**. Si vous ne trouvez pas la ressource d'inscription, suivez les étapes de la section [Renouveler ou modifier l'inscription](azure-stack-registration.md#renew-or-change-registration) pour réinscrire votre instance d'Azure Stack Hub.

  ![Portail](media/azure-stack-usage-issues/stackres.png)

## <a name="error-codes"></a>Codes d’erreur

Cette section décrit la représentation les codes d’erreur d’utilisation.

| Code d'erreur                 | Problème                                                                                                                                             | Correction                                                                                                                                                                                                                                                                                        |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NetworkError               | Le pont Azure Stack Hub ne peut pas envoyer de requête au point de terminaison du service d'utilisation Azure.                                                            | Vérifiez si un proxy bloque ou intercepte l’accès au point de terminaison de service de l’utilisation.                                                                                                                                                                                                             |
| RequestTimedOut            | La requête a été envoyée depuis Azure Bridge, mais le service d’utilisation dans Azure n’a pas répondu dans le délai imparti.                             | Vérifiez si un proxy bloque ou intercepte l’accès au point de terminaison de service de l’utilisation.                                                                                                                                                                                                                        |
| LoginError                 | Impossible de s’authentifier auprès de Microsoft Azure Active Directory.                                                                                                             | Vérifiez que le point de terminaison de connexion Azure AD est accessible à partir de toutes les machines virtuelles XRP d'Azure Stack Hub.                                                                                                                                                                                                                     |
| CertificateValidationError | Azure Bridge n’est pas en mesure d’envoyer la requête, car il n’est pas en mesure de s’authentifier auprès du service Azure.                                    | Vérifiez s'il existe un proxy qui intercepte le trafic HTTPS entre l'ordinateur Azure Stack Hub XRP et le point de terminaison de la passerelle d'utilisation.                                                                                                                                                                                      |
| Non autorisé               | Le pont Azure n'est pas en mesure d'envoyer (push) les données au service d'utilisation Azure, car le service Azure ne parvient pas à authentifier le pont Azure Stack Hub. | Vérifiez si la ressource d'inscription a été modifiée et, si tel est le cas, réinscrivez Azure Stack Hub. <br><br> Parfois, un problème de synchronisation entre Azure Stack Hub et Azure AD peut provoquer cette défaillance. Dans ce cas, vérifiez que les heures des machines virtuelles XRP d'Azure Stack Hub sont synchronisées avec celles d'Azure AD. |
|                            |                                                                                                                                                   |                                                                                                                                                                                                                                                                                                    |

En outre, vous devrez peut-être fournir les fichiers journaux pour les composants Azure Bridge, WAS et WASPublic en suivant [ces étapes](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus d’informations sur la [communication de données d’utilisation d’Azure Stack Hub à Azure](azure-stack-usage-reporting.md).
- Pour consulter les messages d’erreur s’ils sont déclenchés dans votre processus d’inscription, consultez [Code d’erreur d’utilisation et de facturation](azure-stack-registration-errors.md).
- Apprenez-en davantage sur l’[Infrastructure de rapports d’utilisation pour les fournisseurs de solutions cloud](azure-stack-csp-ref-infrastructure.md).
