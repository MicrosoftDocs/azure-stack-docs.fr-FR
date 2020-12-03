---
title: Configurer un tunnel VPN site à site multiple dans Azure Stack Hub
description: Découvrez comment configurer un tunnel VPN site à site multiple dans Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: c7462bebc919b85db64dae9c4a07fdc1ca276e33
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525776"
---
# <a name="how-to-set-up-multiple-site-to-site-vpn-tunnels-in-azure-stack-hub"></a>Configurer plusieurs tunnels VPN site à site dans Azure Stack Hub

Cet article explique comment utiliser un modèle Resource Manager Azure Stack Hub pour déployer la solution. La solution crée plusieurs groupes de ressources avec des réseaux virtuels associés et permet de connecter ces systèmes.

Les modèles sont disponibles dans le référentiel GitHub [Azure intelligent Edge Patterns](https://github.com/Azure-Samples/azure-intelligent-edge-patterns). Le modèle se trouve dans le dossier **rras-gre-vnet-vnet**. 

## <a name="scenarios"></a>Scénarios

![Cinq scénarios VPN sont présentés sous forme de schémas : entre deux groupes de ressources au sein d'un même abonnement ; entre deux groupes, chacun dans son propre abonnement ; entre deux groupes, dans des instances de pile distinctes ; entre un groupe et des ressources locales ; et plusieurs tunnels VPN.](./media/azure-stack-network-howto-vpn-tunnel/scenarios.svg)

## <a name="create-multiple-vpn-tunnels"></a>Créer plusieurs tunnels VPN

![Le schéma montre deux groupes de ressources, chacun dans son propre abonnement et dans sa propre instance de pile, connectés par VPN ; et l'un de ces deux groupes est connecté aux ressources locales par VPN.](./media/azure-stack-network-howto-vpn-tunnel/azure-stack-network-vpn-tunnel1.svg)

-  Déployez une application à trois niveaux : Web, App et DB.

-  Déployez les deux premiers modèles sur des instances Azure Stack Hub distinctes.

-  **WebTier** sera déployé sur PPE1 et **AppTier** sur PPE2.

-  Connectez **WebTier** et **AppTier** à l’aide d’un tunnel IKE.

-  Connectez **AppTier** à un système local que vous appellerez **DBTier**.

## <a name="steps-to-deploy-multiple-vpns"></a>Procédure de déploiement de plusieurs VPN

Cette procédure comprend plusieurs étapes. Pour cette solution, vous allez utiliser le portail Azure Stack Hub. Vous pouvez cependant utiliser PowerShell, Azure CLI ou d’autres chaînes d’outils d’infrastructure en tant que code pour capturer les sorties et les utiliser en tant qu'entrées.

![Le schéma illustre les cinq étapes à suivre pour déployer des tunnels VPN entre deux infrastructures. Les deux premières étapes créent deux infrastructures à partir d'un modèle. Les deux étapes suivantes créent deux tunnels VPN à partir d'un modèle, et la dernière étape connecte les tunnels.](./media/azure-stack-network-howto-vpn-tunnel/azure-stack-network-vpn-tunnel2.svg)

## <a name="walkthrough"></a>Procédure pas à pas

### <a name="deploy-web-tier-to-azure-stack-hub-instances-ppe1"></a>Déployer le niveau web sur des instances Azure Stack Hub PPE1

1. Ouvrez le portail utilisateur Azure Stack Hub, puis sélectionnez **Créer une ressource**.

2. Sélectionnez **Déploiement de modèle**.

    ![La boîte de dialogue « Tableau de bord > Nouveau » contient différentes options. Le bouton « Template deployment » est en surbrillance.](./media/azure-stack-network-howto-vpn-tunnel/image3.png)

3. Copiez et collez le contenu du fichier azuredeploy.json du référentiel **azure-intelligent-edge-patterns/rras-vnet-vpntunnel** vers la fenêtre du modèle. Les ressources contenues dans le modèle s’affichent. Sélectionnez **Enregistrer**.

    ![La boîte de dialogue « Tableau de bord > Nouveau > Déployer un modèle de solution > Modifier le modèle » comporte une fenêtre dans laquelle le fichier azuredeploy.json est collé.](./media/azure-stack-network-howto-vpn-tunnel/image4.png)

4. Entrez un nom de **groupe de ressources** et vérifiez les paramètres.

    > [!Note]  
    > L’espace d’adressage WebTier correspond à **10.10.0.0/16** et vous pouvez voir que l’emplacement du groupe de ressources est **PPE1**

    ![La boîte de dialogue « Tableau de bord > Nouveau > Déployer un modèle de solution > Paramètres » comporte une zone de texte « Groupe de ressources » et une case d'option. Le bouton « Créer nouveau » est sélectionné, et le texte « WebTier » est affiché.](./media/azure-stack-network-howto-vpn-tunnel/image5.png)

### <a name="deploy-app-tier-to-the-second-azure-stack-hub-instances"></a>Déployer le niveau application sur des secondes instances Azure Stack Hub

Vous pouvez suivre la même procédure que pour le niveau **WebTier**, mais des paramètres différents s'affichent ici :

> [!NOTE]  
> L’espace d’adressage AppTier correspond à **10.20.0.0/16** et vous pouvez voir que l’emplacement du groupe de ressources est **WestUS2**

![La boîte de dialogue « Tableau de bord > Déployer un modèle de solution > Paramètres » comporte une zone de texte « Groupe de ressources » et une case d'option. Le bouton « Créer nouveau » est sélectionné, et le texte « AppTier » est affiché. Huit autres zones de texte en surbrillance contiennent les paramètres du modèle.](./media/azure-stack-network-howto-vpn-tunnel/image6.png)

### <a name="review-the-deployments-for-web-tier-and-app-tier-and-capture-outputs"></a>Examiner les déploiements des niveaux web et application et capturer les sorties

1. Vérifiez que le déploiement a abouti. Sélectionnez **Sorties**.

    ![L'option Sorties est en surbrillance dans la boîte de dialogue « Tableau de bord > Microsoft.Template - Vue d'ensemble ». Le message « Votre déploiement est terminé » est affiché, suivi d'une liste de ressources pour le groupe WebTier, avec le nom, le type, l'état de chacune d'elles, ainsi qu'un lien vers les détails.](./media/azure-stack-network-howto-vpn-tunnel/image7.png)

1. Copiez les quatre premières valeurs dans votre application Bloc-notes.

    ![La boîte de dialogue « Tableau de bord > Microsoft.Template - Sorties » est affichée. Les quatre zones de texte à copier à partir du déploiement du niveau Web sont en surbrillance : LOCALTUNNELENDPOINT, LOCALVNETADDRESSSPACE, LOCALVNETGATEWAY et LOCALTUNNELGATEWAY.](./media/azure-stack-network-howto-vpn-tunnel/image8.png)

1. Répétez cette opération pour le déploiement **AppTier**.

    ![L'option Sorties est en surbrillance dans la boîte de dialogue « Tableau de bord > Microsoft.Template - Vue d'ensemble ». Le message « Votre déploiement est terminé » est affiché, suivi d'une liste de ressources pour le groupe AppTier, avec le nom, le type, l'état de chacune d'elles, ainsi qu'un lien vers les détails.](./media/azure-stack-network-howto-vpn-tunnel/image9.png)

    ![La boîte de dialogue « Tableau de bord > Microsoft.Template - Sorties » est affichée. Les quatre zones de texte à copier à partir du déploiement du niveau Application sont en surbrillance : LOCALTUNNELENDPOINT, LOCALVNETADDRESSSPACE, LOCALVNETGATEWAY et LOCALTUNNELGATEWAY.](./media/azure-stack-network-howto-vpn-tunnel/image10.png)

### <a name="create-tunnel-from-web-tier-to-app-tier"></a>Créer un tunnel entre le niveau web et le niveau application

1. Ouvrez le portail utilisateur Azure Stack Hub, puis sélectionnez **Créer une ressource**.

2. Sélectionnez **Déploiement de modèle**.

3. Collez le contenu du fichier **azuredeploy.tunnel.ike.json**.

4. Sélectionnez **Modifier les paramètres**.

![La boîte de dialogue « Tableau de bord > Nouveau > Déployer un modèle de solution > Paramètres » comporte une zone de texte « Groupe de ressources » et une case d'option. Le bouton « Utiliser l'existant » est sélectionné, et le texte « WebTier » est affiché. Quatre autres zones de texte en surbrillance contiennent les paramètres du modèle.](./media/azure-stack-network-howto-vpn-tunnel/image11.png)

### <a name="create-tunnel-from-app-tier-to-web-tier"></a>Créer un tunnel entre le niveau application et le niveau web

1. Ouvrez le portail utilisateur Azure Stack Hub, puis sélectionnez **Créer une ressource**.

2. Sélectionnez **Déploiement de modèle**.

3. Collez le contenu du fichier **azuredeploy.tunnel.ike.json**.

4. Sélectionnez **Modifier les paramètres**.

![La boîte de dialogue « Tableau de bord > Nouveau > Déployer un modèle de solution > Paramètres » comporte une zone de texte « Groupe de ressources » et une case d'option. Le bouton « Utiliser l'existant » est sélectionné, et le texte « AppTier » est affiché. Quatre autres zones de texte en surbrillance contiennent les paramètres du modèle.](./media/azure-stack-network-howto-vpn-tunnel/image12.png)

### <a name="viewing-tunnel-deployment"></a>Affichage du déploiement du tunnel

Si vous affichez la sortie de l’extension de script personnalisé, vous pouvez voir le tunnel en cours de création et afficher sont état. Vous notez qu'un côté attend que l'autre côté soit prêt pour **se connecter**, et que l'autre côté indique **connecté** une fois déployé.

![La boîte de dialogue « Tableau de bord > Groupes de ressources > WebTier > WebTier-RRAS - Extensions » est affichée. Deux extensions sont répertoriées : CustomScriptExtension, qui est en surbrillance, et InstallRRAS. Les deux affichent l'état Approvisionnement réussi.](./media/azure-stack-network-howto-vpn-tunnel/image13.png)

![La boîte de dialogue « Tableau de bord > Groupes de ressources > WebTier > WebTier-RRAS - Extensions > CustomScriptExtension » est affichée. La valeur d'ÉTAT DÉTAILLÉ est « Approvisionnement réussi ».](./media/azure-stack-network-howto-vpn-tunnel/image14.png)

![La sortie des extensions de script personnalisées relatives à AppTier et WebTier est affichée dans des fenêtres parallèles du Bloc-notes. AppTier affiche l'état de tunnel Connexion en cours. WebTier affiche Connecté.](./media/azure-stack-network-howto-vpn-tunnel/image15.png)

### <a name="troubleshooting-on-the-rras-vm"></a>Résolution des problèmes sur la machine virtuelle RRAS

1. Remplacez la règle Bureau à distance (RDP) **Refuser** par **Autoriser**.

1. Connectez-vous au système avec un client RDP à l'aide des informations d'identification que vous avez définies lors du déploiement.

1. Ouvrez PowerShell à l'aide d'une invite de commandes avec élévation de privilèges et exécutez `get-VpnS2SInterface`.

    ![La fenêtre PowerShell présente l'exécution de la commande Get-VpnS2SInterface, qui affiche les détails de l'interface de site à site. L'état de la connexion (ConnectionState) est Connecté.](./media/azure-stack-network-howto-vpn-tunnel/image16.png)

1. Utilisez les cmdlets **RemoteAccess** pour gérer le système.

    ![La fenêtre cmd.exe qui s'exécute au niveau Application affiche la sortie de la commande ipconfig. Deux fenêtres sont superposées : une fenêtre de connexion RDP se connectant au niveau Web et une fenêtre de sécurité Windows se connectant au niveau Web.](./media/azure-stack-network-howto-vpn-tunnel/image17.png)

### <a name="install-rras-on-an-on-premises-vm-db-tier"></a>Installer RRAS sur un niveau Base de données de machine virtuelle locale

1. La cible est une image Windows 2016.

1. Si vous copiez le script `Add-Site2SiteIKE.ps1` à partir du référentiel et l’exécutez localement, ce script installe les ressources **WindowsFeature** et **RemoteAccess**.

    > [!NOTE]
    > En fonction de votre environnement, vous serez peut-être amené à redémarrer votre système.

    Pour plus d'informations, reportez-vous à la configuration réseau de la machine locale.

    ![Vous disposez d'une fenêtre cmd.exe, qui affiche la sortie ipconfig, ainsi que d'une fenêtre Centre Réseau et partage.](./media/azure-stack-network-howto-vpn-tunnel/image18.png)

1. Exécutez le script en ajoutant les paramètres **Sortie** enregistrés lors du déploiement du modèle AppTier.

    ![Le script Add-Site2SiteIKE.psa est exécuté dans une fenêtre PowerShell, et la sortie est affichée.](./media/azure-stack-network-howto-vpn-tunnel/image19.png)

1. Le tunnel est maintenant configuré et attend la connexion AppTier.

### <a name="configure-app-tier-to-db-tier"></a>Configurer le niveau application sur le niveau DB

1. Ouvrez le portail utilisateur Azure Stack Hub, puis sélectionnez **Créer une ressource**.

2. Sélectionnez **Déploiement de modèle**.

3. Collez le contenu du fichier **azuredeploy.tunnel.ike.json**.

4. Sélectionnez **Modifier les paramètres**.

    ![La boîte de dialogue « Tableau de bord > Nouveau > Déployer un modèle de solution > Paramètres » comporte une zone de texte « Groupe de ressources » et une case d'option. Le bouton « Utiliser l'existant » est sélectionné, et le texte « AppTier » est affiché. Trois autres zones de texte en surbrillance contiennent les paramètres du modèle.](./media/azure-stack-network-howto-vpn-tunnel/image20.png)

5. Vérifiez que AppTier est sélectionné et que le réseau interne distant est défini sur 10.99.0.1.

### <a name="confirm-tunnel-between-app-tier-and-db-tier"></a>Vérifier le tunnel entre le niveau application et le niveau DB

1. Pour vérifier le tunnel sans vous connecter à la machine virtuelle, exécutez une extension de script personnalisé.

2. Accédez à la machine virtuelle RRAS (AppTier).

3. Sélectionnez **Extensions** et exécutez l'**extension de script personnalisé**.

4. Accédez au répertoire de scripts dans le référentiel **azure-intelligent-edge-patterns/rras-vnet-vpntunnel**. Sélectionnez **Get-VPNS2SInterfaceStatus.ps1**.

    ![La boîte de dialogue « Tableau de bord > Groupes de ressources > AppTier > AppTier-RRAS - Extensions > CustomScriptExtension » est affichée. La valeur d'ÉTAT DÉTAILLÉ est « Approvisionnement réussi ». Les détails figurent dans une fenêtre superposée du Bloc-notes.](./media/azure-stack-network-howto-vpn-tunnel/image21.png)

5. Si vous activez le protocole RDP (Remote Desktop Protocol), vous connectez, ouvrez PowerShell et exécutez `get-vpns2sinterface`, vous pouvez voir que le tunnel est connecté.

    **DBTier**

    ![Sur DBTier, la fenêtre PowerShell présente l'exécution de la commande Get-VpnS2SInterface, qui affiche les détails de l'interface de site à site. L'état de la connexion (ConnectionState) est Connecté.](./media/azure-stack-network-howto-vpn-tunnel/image22.png)

    **AppTier**

    ![Sur AppTier, la fenêtre PowerShell présente l'exécution de la commande Get-VpnS2SInterface, qui affiche les détails de l'interface de site à site. L'état de la connexion (ConnectionState) est Connecté pour deux destinations.](./media/azure-stack-network-howto-vpn-tunnel/image23.png)

    > [!NOTE]  
    > Vous pouvez tester le protocole RDP d'une première à une deuxième machine, et inversement.

    > [!NOTE]  
    > Pour implémenter cette solution localement, vous devez déployer des itinéraires vers le réseau distant Azure Stack Hub dans votre infrastructure de basculement ou, au minimum, sur des machines virtuelles spécifiques.

### <a name="deploying-a-gre-tunnel"></a>Déployer un tunnel GRE

Pour ce modèle, cette procédure pas à pas a utilisé le [modèle IKE](network-howto-vpn-tunnel-ipsec.md). Cela étant, vous pouvez également déployer un [tunnel GRE](network-howto-vpn-tunnel-gre.md). Ce tunnel offre un débit plus élevé.

La procédure est quasiment la même. Toutefois, lorsque vous déployez le modèle de tunnel sur l’infrastructure existante, vous devez utiliser les sorties de l’autre système pour les trois premières entrées. Vous devez connaître la ressource **LOCALTUNNELGATEWAY** du groupe de ressources dans lequel vous procédez au déploiement plutôt que le groupe de ressources auquel vous tentez de vous connecter.

![La boîte de dialogue « Tableau de bord > Nouveau > Déployer un modèle de solution > Paramètres » comporte une zone de texte « Groupe de ressources » et une case d'option. Le bouton « Utiliser l'existant » est sélectionné, et le texte « AppTier » est affiché. Quatre autres zones de texte en surbrillance contiennent des données issues des sorties WebTier.](./media/azure-stack-network-howto-vpn-tunnel/image24.png)

## <a name="next-steps"></a>Étapes suivantes

[Différences et considérations relatives aux réseaux Azure Stack Hub](azure-stack-network-differences.md)  
[Créer un tunnel VPN à l’aide de GRE](network-howto-vpn-tunnel-gre.md)  
[Créer un tunnel VPN à l’aide d’IPSEC](network-howto-vpn-tunnel-ipsec.md)