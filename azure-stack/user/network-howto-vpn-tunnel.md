---
title: Configurer un tunnel VPN site à site multiple dans Azure Stack Hub
description: Découvrez comment configurer un tunnel VPN site à site multiple dans Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 09/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: c1b48e551a1c94456174cbe5725da72d76f78219
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77636333"
---
# <a name="how-to-set-up-a-multiple-site-to-site-vpn-tunnel-in-azure-stack-hub"></a>Configurer un tunnel VPN site à site multiple dans Azure Stack Hub

Cet article explique comment utiliser un modèle Resource Manager Azure Stack Hub pour déployer la solution. La solution crée plusieurs groupes de ressources avec des réseaux virtuels associés et permet de connecter ces systèmes.

Les modèles sont disponibles dans le référentiel GitHub [Azure intelligent Edge Patterns](https://github.com/Azure-Samples/azure-intelligent-edge-patterns). Le modèle se trouve dans le dossier **rras-gre-vnet-vnet**. 

## <a name="scenarios"></a>Scénarios

![](./media/azure-stack-network-howto-vpn-tunnel/scenarios.png)

## <a name="create-multiple-vpn-tunnels"></a>Créer plusieurs tunnels VPN

![](./media/azure-stack-network-howto-vpn-tunnel/image1.png)

-  Déployez une application à trois niveaux : Web, App et DB.

-  Déployez les deux premiers modèles sur des instances Azure Stack Hub distinctes.

-  **WebTier** sera déployé sur PPE1 et **AppTier** sur PPE2.

-  Connectez **WebTier** et **AppTier** à l’aide d’un tunnel IKE.

-  Connectez **AppTier** à un système local que vous appellerez**DBTier**.

## <a name="steps-to-deploy-multiple-vpns"></a>Procédure de déploiement de plusieurs VPN

Cette procédure comprend plusieurs étapes. Pour cette solution, vous allez utiliser le portail Azure Stack Hub. Vous pouvez cependant utiliser PowerShell, Azure CLI ou d’autres chaînes d’outils d’infrastructure en tant que code pour capturer les sorties et les utiliser en tant qu'entrées.

![texte de remplacement](./media/azure-stack-network-howto-vpn-tunnel/image2.png)

## <a name="walkthrough"></a>Procédure pas à pas

### <a name="deploy-web-tier-to-azure-stack-hub-instances-ppe1"></a>Déployer le niveau web sur des instances Azure Stack Hub PPE1

1.  Ouvrez le portail utilisateur Azure Stack Hub, puis sélectionnez **Créer une ressource**.

2.  Sélectionnez **Déploiement de modèle**.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image3.png)

3.  Copiez et collez le contenu du fichier azuredeploy.json du référentiel **azure-intelligent-edge-patterns/rras-vnet-vpntunnel** vers la fenêtre du modèle. Les ressources contenues dans le modèle s’affichent. Sélectionnez **Enregistrer**.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image4.png)

4.  Entrez un nom de **groupe de ressources** et vérifiez les paramètres.

    > ![Remarque]  
    > L’espace d’adressage WebTier correspond à **10.10.0.0/16** et vous pouvez voir que l’emplacement du groupe de ressources est **PPE1**

    ![](./media/azure-stack-network-howto-vpn-tunnel/image5.png)

### <a name="deploy-app-tier-to-the-second-azure-stack-hub-instances"></a>Déployer le niveau application sur des secondes instances Azure Stack Hub

Vous pouvez suivre la même procédure que pour le niveau **WebTier**, mais des paramètres différents s'affichent ici :

> [!Note]  
> L’espace d’adressage AppTier correspond à **10.20.0.0/16** et vous pouvez voir que l’emplacement du groupe de ressources est **WestUS2**

![](./media/azure-stack-network-howto-vpn-tunnel/image6.png)

### <a name="review-the-deployments-for-web-tier-and-app-tier-and-capture-outputs"></a>Examiner les déploiements des niveaux web et application et capturer les sorties

1.  Vérifiez que le déploiement a abouti. Sélectionnez **Sorties**.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image7.png)

3.  Copiez les quatre premières valeurs dans votre application Bloc-notes.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image8.png)

5.  Répétez cette opération pour le déploiement **AppTier**.

![](./media/azure-stack-network-howto-vpn-tunnel/image9.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image10.png)

### <a name="create-tunnel-from-web-tier-to-app-tier"></a>Créer un tunnel entre le niveau web et le niveau application

1.  Ouvrez le portail utilisateur Azure Stack Hub, puis sélectionnez **Créer une ressource**.

2.  Sélectionnez **Déploiement de modèle**.

3.  Collez le contenu du fichier **azuredeploy.tunnel.ike.json**.

4.  Sélectionnez **Modifier les paramètres**.

![](./media/azure-stack-network-howto-vpn-tunnel/image11.png)

### <a name="create-tunnel-from-app-tier-to-web-tier"></a>Créer un tunnel entre le niveau application et le niveau web

1.  Ouvrez le portail utilisateur Azure Stack Hub, puis sélectionnez **Créer une ressource**.

2.  Sélectionnez **Déploiement de modèle**.

3.  Collez le contenu du fichier **azuredeploy.tunnel.ike.json**.

4.  Sélectionnez **Modifier les paramètres**.

![](./media/azure-stack-network-howto-vpn-tunnel/image12.png)

### <a name="viewing-tunnel-deployment"></a>Affichage du déploiement du tunnel

Si vous affichez la sortie de l’extension de script personnalisé, vous pouvez voir le tunnel en cours de création et afficher sont état. Vous notez qu'un côté attend que l'autre côté soit prêt pour **se connecter**, et que l'autre côté indique **connecté** une fois déployé.

![](./media/azure-stack-network-howto-vpn-tunnel/image13.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image14.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image15.png)

### <a name="troubleshooting-on-the-rras-vm"></a>Résolution des problèmes sur la machine virtuelle RRAS

1.  Remplacez la règle RDP **Refuser** par **Autoriser**.

2.  Connectez-vous au système avec un client Bureau à distance à l'aide des informations d’identification que vous avez définies lors du déploiement.

3.  Ouvrez PowerShell à l'aide d'une invite de commandes avec élévation de privilèges et exécutez `get-VPNS2SInterface`.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image16.png)

5.  Utilisez les cmdlets **RemoteAccess** pour gérer le système.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image17.png)

### <a name="install-rras-on-a-on-premises-vm-db-tier"></a>Installer RRAS sur un niveau Base de données de machine virtuelle locale

1.  La cible est une image Windows 2016.

2.  Si vous copiez le script `Add-Site2SiteIKE.ps1` à partir du référentiel et l’exécutez localement, ce script installe les ressources **WindowsFeature** et **RemoteAccess**.

    > [!Note]
    > En fonction de votre environnement, vous serez peut-être amené à redémarrer votre système.

    Pour plus d'informations, reportez-vous à la configuration réseau de la machine locale.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image18.png)

3.  Exécutez le script en ajoutant les paramètres **Sortie** enregistrés lors du déploiement du modèle AppTier.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image19.png)

5.  Le tunnel est maintenant configuré et attend la connexion AppTier.

### <a name="configure-app-tier-to-db-tier"></a>Configurer le niveau application sur le niveau DB

1.  Ouvrez le portail utilisateur Azure Stack Hub, puis sélectionnez **Créer une ressource**.

2.  Sélectionnez **Déploiement de modèle**.

3.  Collez le contenu du fichier **azuredeploy.tunnel.ike.json**.

4.  Sélectionnez **Modifier les paramètres**.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image20.png)

5.  Vérifiez que AppTier est sélectionné et que le réseau interne distant est défini sur 10.99.0.1.

### <a name="confirm-tunnel-between-app-tier-and-db-tier"></a>Vérifier le tunnel entre le niveau application et le niveau DB

1.  Pour vérifier le tunnel sans vous connecter à la machine virtuelle, exécutez une extension de script personnalisé.

2.  Accédez à la machine virtuelle RRAS (AppTier).

3.  Sélectionnez **Extensions** et exécutez l'**extension de script personnalisé**.

4.  Accédez au répertoire de scripts dans le référentiel **azure-intelligent-edge-patterns/rras-vnet-vpntunnel**. Sélectionnez **Get-VPNS2SInterfaceStatus.ps1**.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image21.png)

5.  Si vous activez le protocole RDP (Remote Desktop Protocol), vous connectez, ouvrez PowerShell et exécutez `get-vpns2sinterface`, vous pouvez voir que le tunnel est connecté.

    **DBTier**

    ![](./media/azure-stack-network-howto-vpn-tunnel/image22.png)

    **AppTier**

    ![](./media/azure-stack-network-howto-vpn-tunnel/image23.png)

    > [!Note]  
    > Vous pouvez tester le protocole RDP d'une première à une deuxième machine, et inversement.

    > [!Note]  
    > Pour implémenter cette solution localement, vous devez déployer des itinéraires vers le réseau distant Azure Stack Hub dans votre infrastructure de basculement ou, au minimum, sur des machines virtuelles spécifiques.

### <a name="deploying-a-gre-tunnel"></a>Déployer un tunnel GRE

Pour ce modèle, cette procédure pas à pas a utilisé le [modèle IKE](network-howto-vpn-tunnel-ipsec.md). Cela étant, vous pouvez également déployer un [tunnel GRE](network-howto-vpn-tunnel-gre.md). Ce tunnel offre un débit plus élevé.

La procédure est quasiment la même. Toutefois, lorsque vous déployez le modèle de tunnel sur l’infrastructure existante, vous devez utiliser les sorties de l’autre système pour les trois premières entrées. Vous devez connaître la ressource **LOCALTUNNELGATEWAY** du groupe de ressources dans lequel vous procédez au déploiement plutôt que le groupe de ressources auquel vous tentez de vous connecter.

![](./media/azure-stack-network-howto-vpn-tunnel/image24.png)

## <a name="next-steps"></a>Étapes suivantes

[Différences et considérations relatives aux réseaux Azure Stack Hub](azure-stack-network-differences.md)  
[Créer un tunnel VPN à l’aide de GRE](network-howto-vpn-tunnel-gre.md)  
[Créer un tunnel VPN à l’aide d’IPSEC](network-howto-vpn-tunnel-ipsec.md)