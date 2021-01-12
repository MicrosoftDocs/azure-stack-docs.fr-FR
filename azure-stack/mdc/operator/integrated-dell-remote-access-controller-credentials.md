---
title: Informations d’identification iDRAC - MDC
description: Découvrez comment mettre à jour les informations d’identification iDRAC (Integrated Dell Remote Access Controller) dans un Modular Data Center (MDC).
author: troettinger
ms.author: thoroet
ms.service: azure-stack
ms.topic: article
ms.date: 10/27/2020
ms.reviewer: justinha
ms.lastreviewed: 10/27/2020
ms.openlocfilehash: d0d2f522038d0788d4942db68b36e5bb21884619
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910735"
---
# <a name="update-credentials-for-the-integrated-dell-remote-access-controller---modular-data-center-mdc"></a>Mettre à jour les informations d’identification iDRAC - Modular Data Center (MDC)

Cette section explique comment modifier les informations d'identification du contrôleur iDRAC (integrated Dell Remote Access Controller). 

## <a name="prerequisites"></a>Prérequis

Avant d'entamer la procédure : 

- Utilisez le Bureau à distance pour vous connecter à la machine virtuelle MGMT. 
- Vérifiez que vous disposez des nouvelles informations d'identification du compte ou des comptes. 
 
## <a name="update-the-idrac-credentials"></a>Mettre à jour les informations d'identification iDRAC

Pour mettre à jour les informations d'identification iDRAC de tous les serveurs PowerEdge (nœuds d'unité d'échelle et HLH) de l'environnement :

1. Dans un navigateur web, connectez-vous à : https://<iDRAC_IP>. 
1. Accédez à **Paramètres iDRAC** > **Utilisateurs**. 
1. Sélectionnez l'utilisateur que vous souhaitez modifier, puis cliquez sur **Modifier**. 
1. Dans la fenêtre **Modifier l'utilisateur**, entrez le nouveau mot de passe dans les champs **Mot de passe** et **Confirmer le mot de passe**, comme illustré ci-dessous : 

   ![Capture d'écran montrant les informations utilisateur](../operator/media/idrac-credentials/enter-user.png)

1. Cliquez sur **Enregistrer**, puis sur **OK**. 

## <a name="next-steps"></a>Étapes suivantes

[Effectuer la rotation des secrets dans Azure Stack Hub](../../operator/azure-stack-rotate-secrets.md)