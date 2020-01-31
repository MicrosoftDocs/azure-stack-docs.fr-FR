---
title: Installer Visual Studio et se connecter à Azure Stack Hub
description: Découvrez comment installer Visual Studio et se connecter à Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 7c5183c88a973756685c5831d10d4f5c6aeb2ce0
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883659"
---
# <a name="install-visual-studio-and-connect-to-azure-stack-hub"></a>Installer Visual Studio et se connecter à Azure Stack Hub

Vous pouvez utiliser Visual Studio pour écrire et déployer des [modèles](azure-stack-arm-templates.md) Azure Resource Manager sur Azure Stack Hub. Les étapes de cet article décrivent l’installation de Visual Studio sur [Azure Stack Hub](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) ou sur un ordinateur externe si vous prévoyez d’utiliser Azure Stack Hub par [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn).

## <a name="install-visual-studio"></a>Installation de Visual Studio

1. Téléchargez et exécutez [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).  

2. Ouvrez **Microsoft Web Platform Installer**.

3. Recherchez **Visual Studio Community 2015 avec Kit de développement logiciel (SDK) Microsoft Azure - 2.9.6**. Cliquez sur **Ajouter** puis sur **Installer**.

4. Désinstallez la version de **Microsoft Azure PowerShell** installée dans le cadre du SDK Azure.

    ![Capture d’écran des étapes d’installation de WebPI](./media/azure-stack-install-visual-studio/image1.png)

5. [Installez PowerShell pour Azure Stack Hub](../operator/azure-stack-powershell-install.md).

6. Redémarrez le système d’exploitation après l’installation.

## <a name="connect-to-azure-stack-hub-with-azure-ad"></a>Se connecter à Azure Stack Hub avec Azure AD

1. Lancez Visual Studio.

2. Dans le menu **Affichage**, sélectionnez **Cloud Explorer**.

3. Dans le nouveau volet, sélectionnez **Ajouter un compte** et connectez-vous avec vos informations d’identification Azure Active Directory (Azure AD).  

    ![Capture d’écran de Cloud Explorer, une fois connecté à Azure Stack Hub](./media/azure-stack-install-visual-studio/image2.png)

Une fois connecté, vous pouvez [déployer des modèles](azure-stack-deploy-template-visual-studio.md) ou parcourir les types de ressources et les groupes de ressources disponibles pour créer vos propres modèles.  

## <a name="connect-to-azure-stack-hub-with-ad-fs"></a>Se connecter à Azure Stack Hub avec AD FS

1. Lancez Visual Studio.

2. Dans **Outils**, sélectionnez **Options**.

3. Développez **Environnement** dans le **volet de navigation** et sélectionnez **Comptes**.

4. Sélectionnez **Ajouter**, puis entrez le point de terminaison Azure Resource Manager utilisateur. Pour le kit de développement Azure Stack (ASDK), l’URL est : `https://management.local.azurestack/external`.  Pour les systèmes intégrés Azure Stack Hub, l’URL est : `https://management.[Region}.[External FQDN]`.

    ![Ajouter un nouveau point de terminaison de découverte Cloud Azure](./media/azure-stack-install-visual-studio/image5.png)

5. Sélectionnez **Ajouter**.  

    Visual Studio appelle Azure Resource Manager et découvre les points de terminaison, notamment le point de terminaison d’authentification pour les services AD FS (Active Directory Federated Services).

    ![Capture d’écran de Cloud Explorer, une fois connecté à Azure Stack Hub](./media/azure-stack-install-visual-studio/image6.png)

6. Dans le menu **Affichage**, sélectionnez **Cloud Explorer**.

7. Sélectionnez **Ajouter un compte** et connectez-vous avec vos informations d’identification AD FS.  

    ![Se connecter à Visual Studio dans Cloud Explorer](./media/azure-stack-install-visual-studio/image7.png)

    Cloud Explorer interroge les abonnements disponibles. Vous pouvez sélectionner un abonnement disponible en vue de le gérer.

    ![Sélectionner des abonnements à gérer dans Cloud Explorer](./media/azure-stack-install-visual-studio/image8.png)

8. Explorez vos ressources, groupes de ressources ou modèles de déploiement existants.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’[installation côte à côte](/visualstudio/install/install-visual-studio-versions-side-by-side) avec d’autres versions de Visual Studio.
- [Développer des modèles pour Azure Stack Hub](azure-stack-develop-templates.md).
