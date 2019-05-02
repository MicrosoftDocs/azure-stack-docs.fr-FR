---
title: Installer Visual Studio et se connecter à Azure Stack | Microsoft Docs
description: Découvrez les étapes nécessaires pour installer Visual Studio et se connecter à Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/08/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 11d389b96ed730d6395231ecf24eced6a65fbae8
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64311747"
---
# <a name="install-visual-studio-and-connect-to-azure-stack"></a>Installer Visual Studio et se connecter à Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez utiliser Visual Studio pour écrire et déployer des [modèles](azure-stack-arm-templates.md) Azure Resource Manager dans Azure Stack. Les étapes décrites dans cet article expliquent comment installer Visual Studio sur [Azure Stack](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp), ou sur un ordinateur externe si vous envisagez d’utiliser Azure Stack par [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn).

## <a name="install-visual-studio"></a>Installation de Visual Studio

1. Téléchargez et exécutez [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).  

2. Ouvrez **Microsoft Web Platform Installer**.

3. Recherchez **Visual Studio Community 2015 avec Kit de développement logiciel (SDK) Microsoft Azure - 2.9.6**. Cliquez sur **Ajouter** et **Installer**.

4. Désinstallez la version de **Microsoft Azure PowerShell** installée dans le cadre du SDK Azure.

    ![Capture d’écran des étapes d’installation de WebPI](./media/azure-stack-install-visual-studio/image1.png)

5. [Installer PowerShell pour Azure Stack](../operator/azure-stack-powershell-install.md)

6. Redémarrez le système d’exploitation après l’installation.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Se connecter à Azure Stack avec Azure AD

1. Lancez Visual Studio.

2. Dans le menu **Affichage**, sélectionnez **Cloud Explorer**.

3. Dans le nouveau volet, sélectionnez **Ajouter un compte** et connectez-vous avec vos informations d’identification Azure Active Directory (Azure AD).  

    ![Capture d’écran de Cloud Explorer une fois connecté et connecté à Azure Stack](./media/azure-stack-install-visual-studio/image2.png)

Une fois connecté, vous pouvez [déployer des modèles](azure-stack-deploy-template-visual-studio.md) ou parcourir les types de ressources et les groupes de ressources disponibles pour créer vos propres modèles.  

## <a name="connect-to-azure-stack-with-ad-fs"></a>Se connecter à Azure Stack avec AD FS

1. Lancez Visual Studio.

2. Dans **Outils**, sélectionnez **Options**.

3. Développez **Environnement** dans le **volet de navigation** et sélectionnez **Comptes**.

4. Sélectionnez **Ajouter**, puis entrez le point de terminaison Azure Resource Manager utilisateur. Pour le kit de développement Azure Stack, l’URL est : `https://management.local.azurestack/external`.  Pour les systèmes intégrés Azure Stack, l’URL est : `https://management.[Region}.[External FQDN]`.

    ![X](./media/azure-stack-install-visual-studio/image5.png)

5. Sélectionnez **Ajouter**.  

    Visual Studio appelle Azure Resource Manager et découvre les points de terminaison, notamment le point de terminaison d’authentification pour les services AD FS (Active Directory Federated Services).

    ![Capture d’écran de Cloud Explorer une fois connecté et connecté à Azure Stack](./media/azure-stack-install-visual-studio/image6.png)

6. Dans le menu **Affichage**, sélectionnez **Cloud Explorer**.

7. Sélectionnez **Ajouter un compte** et connectez-vous avec vos informations d’identification AD FS.  

    ![Cloud Explorer](./media/azure-stack-install-visual-studio/image7.png)

    Cloud Explorer interroge les abonnements disponibles. Vous pouvez sélectionner un abonnement disponible en vue de le gérer.

    ![Cloud Explorer](./media/azure-stack-install-visual-studio/image8.png)

8. Explorez vos ressources, groupes de ressources ou modèles de déploiement existants.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’[installation côte à côte](/visualstudio/install/install-visual-studio-versions-side-by-side) avec d’autres versions de Visual Studio.
- [Développer des modèles pour Azure Stack](azure-stack-develop-templates.md).