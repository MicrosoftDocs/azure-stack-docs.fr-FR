---
title: Configurer une connectivité cloud hybride dans Azure et Azure Stack Hub
description: Apprenez à configurer une connectivité cloud hybride à l'aide d'Azure et d'Azure Stack Hub.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: a810579dd3dd50b58b357921657e87103dbfe59a
ms.sourcegitcommit: e5b587216a137819444680ec619281c90f37bad9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2020
ms.locfileid: "82166988"
---
# <a name="configure-hybrid-cloud-connectivity-using-azure-and-azure-stack-hub"></a>Configurer une connectivité cloud hybride à l'aide d'Azure et d'Azure Stack Hub

Vous pouvez accéder aux ressources en toute sécurité dans Azure global et Azure Stack Hub à l'aide du modèle de connectivité hybride.

Dans cette solution, vous allez générer un exemple d’environnement pour :

> [!div class="checklist"]
> - Conserver les données localement pour le respect de la confidentialité ou des obligations réglementaires, tout en gardant l’accès aux ressources Azure globales.
> - Gérer un système hérité tout en utilisant un déploiement d’application avec une mise à l’échelle dans le cloud et des ressources dans Azure global.

> [!Tip]  
> ![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack Hub est une extension d’Azure. Azure Stack Hub offre à votre environnement local l’agilité et l’innovation du cloud computing grâce au seul cloud hybride qui vous permette de créer et de déployer des applications hybrides en tout lieu.  
> 
> L’article [Considérations sur la conception d’applications hybrides](overview-app-design-considerations.md) se penche sur les fondements de la qualité logicielle (sélection élective, scalabilité, disponibilité, résilience, facilité de gestion et sécurité) en matière de conception, de déploiement et d’exploitation des applications hybrides. Les considérations de conception vous aident à optimiser la conception d’application hybride, en réduisant les risques dans les environnements de production.

## <a name="prerequisites"></a>Prérequis

Certains composants sont nécessaires pour créer un déploiement de connectivité hybride. La préparation de certains de ces composants pouvant prendre du temps, vous devez planifier en conséquence.

### <a name="azure"></a>Azure

- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Créez une [application web](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts) dans Azure. Notez l’URL de l’application web, car vous en aurez besoin dans la solution.

### <a name="azure-stack-hub"></a>Azure Stack Hub

Un partenaire OEM ou matériel Azure peut déployer une instance Azure Stack Hub de production et tous les utilisateurs peuvent déployer un Kit de développement Azure Stack (ASDK).

- Utilisez votre instance Azure Stack Hub de production ou déployez le kit ASDK à partir de https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >Le déploiement de l’ASDK peut prendre jusqu’à 7 heures, planifiez donc en conséquence.

- Déployez les services PaaS [App Service](../operator/azure-stack-app-service-deploy.md) sur Azure Stack Hub.
- [Créez des plans et des offres](../operator/service-plan-offer-subscription-overview.md) dans l'environnement Azure Stack Hub.
- [Créez un abonnement de locataire](../operator/azure-stack-subscribe-plan-provision-vm.md) dans l'environnement Azure Stack Hub.

### <a name="azure-stack-hub-components"></a>Composants Azure Stack Hub

Un opérateur Azure Stack Hub doit déployer App Service, créer des plans et des offres, créer un abonnement de locataire et ajouter l'image Windows Server 2016. Si vous disposez déjà de ces composants, vérifiez qu’ils répondent aux exigences avant de commencer cette solution.

Cet exemple de solution suppose que vous disposez de connaissances de base sur Azure et Azure Stack Hub. Pour en savoir plus avant de commencer la solution, lisez les articles suivants :

- [Présentation de Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/)
- [Concepts clés d'Azure Stack Hub](../operator/azure-stack-overview.md)

### <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous répondez aux critères suivants avant de commencer la configuration de la connectivité cloud hybride :

- Vous avez besoin d’une adresse IPv4 publique exposée en externe pour votre périphérique VPN. Cette adresse IP ne peut pas se trouver derrière une NAT (traduction d’adresses réseau).
- Toutes les ressources sont déployées dans la même région/le même emplacement.

#### <a name="solution-example-values"></a>Valeurs des exemples de la solution

Nous utilisons les valeurs suivantes dans les exemples de cette solution. Vous pouvez utiliser ces valeurs pour créer un environnement de test ou vous y référer pour mieux comprendre les exemples. Pour plus d’informations sur les paramètres de la passerelle VPN, consultez [À propos des paramètres de la passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Spécifications de la connexion :

- **Type de VPN** : basé sur les routes
- **Type de connexion** : site à site (IPsec)
- **Type de passerelle** : VPN
- **Nom de la connexion Azure** : Azure-Gateway-AzureStack-S2SGateway (cette valeur est automatiquement renseignée sur le portail)
- **Nom de la connexion Azure Stack Hub** : AzureStack-Gateway-Azure-S2SGateway (cette valeur est automatiquement renseignée sur le portail)
- **Clé partagée** : toute clé compatible avec le matériel VPN, avec des valeurs correspondantes des deux côtés de la connexion
- **Abonnement** : abonnement de votre choix
- **Groupe de ressources** : Test-Infra

Adresses IP réseau et sous-réseau :

| Connexion Azure/Azure Stack Hub | Nom | Subnet | Adresse IP |
|---|---|---|---|
| Réseau virtuel Azure | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | Sous-réseau de passerelle<br>10.100.103.0/24 |  |
| Réseau virtuel Azure Stack Hub | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | Sous-réseau de passerelle <br>10.100101.0/24 |  |
| Passerelle de réseau virtuel Azure | Azure-Gateway |  |  |
| Passerelle de réseau virtuel Azure Stack Hub | AzureStack-Gateway |  |  |
| Adresse IP publique | Azure-GatewayPublicIP |  | Déterminée à la création |
| Adresse IP publique Azure Stack Hub | AzureStack-GatewayPublicIP |  | Déterminée à la création |
| Passerelle du réseau local Azure | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Valeur d'adresse IP publique Azure Stack Hub |
| Passerelle de réseau local Azure Stack Hub | Azure-S2SGateway<br>10.100.102.0/23 |  | Adresse IP publique Azure |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack-hub"></a>Créer un réseau virtuel dans Azure global et Azure Stack Hub

Procédez comme suit pour créer un réseau virtuel à l’aide du portail Azure. Vous pouvez utiliser ces [exemples de valeurs](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) si vous utilisez cet article uniquement comme solution. Si vous utilisez cet article pour configurer un environnement de production, remplacez les exemples de paramètres par vos propres valeurs.

> [!IMPORTANT]
> Vous devez vous assurer que les adresses IP ne se chevauchent pas dans les espaces d'adressage du réseau virtuel Azure ou Azure Stack Hub.

Pour créer un réseau virtuel dans Azure :

1. Utilisez votre navigateur pour vous connecter au [portail Azure](https://portal.azure.com/) et connectez-vous avec votre compte Azure.
2. Sélectionnez **Créer une ressource**. Dans le champ **Rechercher dans le marketplace**, entrez « réseau virtuel ». Sélectionnez **Réseau virtuel** dans les résultats.
3. Dans la liste **Sélectionner un modèle de déploiement**, sélectionnez **Gestionnaire des ressources**, puis **Créer**.
4. Dans **Créer un réseau virtuel**, configurez les paramètres du réseau virtuel. Les noms des champs obligatoires sont précédés d’un astérisque rouge.  Lorsque vous entrez une valeur valide, l’astérisque devient une coche verte.

Pour créer un réseau virtuel dans Azure Stack Hub :

1. Répétez les étapes ci-dessus (1 à 4) en utilisant le **portail du locataire** Azure Stack Hub.

## <a name="add-a-gateway-subnet"></a>Ajouter un sous-réseau de passerelle

Avant de connecter votre réseau virtuel à une passerelle, vous devez créer le sous-réseau de passerelle pour le réseau virtuel auquel vous souhaitez vous connecter. Les services de passerelle utilisent les adresses IP que vous spécifiez dans le sous-réseau de passerelle.

Dans le [portail Azure](https://portal.azure.com/), accédez au réseau virtuel Gestionnaire des ressources dans lequel vous souhaitez créer une passerelle de réseau virtuel.

1. Sélectionnez le réseau virtuel pour ouvrir la page **Réseau virtuel**.
2. Dans **PARAMÈTRES**, sélectionnez **Sous-réseaux**.
3. Sur la page **Sous-réseaux**, sélectionnez **+Sous-réseau de passerelle** pour ouvrir la page **Ajouter un sous-réseau**.

    ![Ajouter un sous-réseau de passerelle](media/solution-deployment-guide-connectivity/image4.png)

4. Le **Nom** du sous-réseau est automatiquement rempli avec la valeur « GatewaySubnet ». Cette valeur est nécessaire pour qu’Azure puisse reconnaître le sous-réseau en tant que sous-réseau de passerelle.
5. Modifiez les valeurs de **Plage d’adresses** qui sont fournies pour qu’elles correspondent à vos besoins de configuration, puis sélectionnez **OK**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Créer une passerelle de réseau virtuel dans Azure et Azure Stack

Procédez comme suit pour créer une passerelle de réseau virtuel dans Azure.

1. Sur le côté gauche de la page du portail, sélectionnez **+** , puis entrez « Passerelle de réseau virtuel » dans le champ de recherche.
2. Dans **Résultats**, sélectionnez **Passerelle de réseau virtuel**.
3. Dans **Passerelle de réseau virtuel**, sélectionnez **Créer** pour ouvrir la page **Créer une passerelle de réseau virtuel**.
4. Dans **Créer une passerelle de réseau virtuel**, spécifiez les valeurs de votre passerelle de réseau en utilisant nos **exemples de valeurs du tutoriel**. Incluez les valeurs supplémentaires suivantes :

   - **Référence (SKU)** : De base
   - **Réseau virtuel** : sélectionnez le réseau virtuel que vous avez créé précédemment. Le sous-réseau de passerelle que vous avez créé est automatiquement sélectionné.
   - **Première configuration IP** :  adresse IP publique de votre passerelle.
     - Sélectionnez **Créer une configuration d’IP de passerelle**, la page **Choisir une adresse IP publique** s’affiche.
     - Sélectionnez **+Créer nouveau** pour ouvrir la page **Créer une adresse IP publique**.
     - Donnez un **nom** à votre adresse IP publique. Laissez la référence SKU sur **De base**, puis sélectionnez **OK** pour enregistrer vos modifications.

       > [!Note]
       > Actuellement, la passerelle VPN prend uniquement en charge l’allocation d’adresses IP publiques dynamiques. Toutefois, cela ne signifie pas que l’adresse IP change après son affectation à votre passerelle VPN. L’adresse IP publique change uniquement lorsque la passerelle est supprimée, puis recréée. Un redimensionnement, une réinitialisation ou des autres opérations de maintenance/mise à niveau internes de votre passerelle VPN ne modifient pas l’adresse IP.

5. Vérifiez vos paramètres de passerelle.
6. Sélectionnez **Créer** pour créer la passerelle VPN. Les paramètres de passerelle sont validés et la vignette « Déploiement d’une passerelle de réseau virtuel » s’affiche sur votre tableau de bord.

   >[!Note]
   >La création d’une passerelle peut prendre jusqu’à 45 minutes Vous devrez peut-être actualiser la page du portail pour que l’état terminé apparaisse.

    Une fois la passerelle créée, observez le réseau virtuel dans le portail pour connaître l’adresse IP affectée à la passerelle. Cette dernière apparaît sous la forme d’un appareil connecté. Pour plus d’informations sur la passerelle, sélectionnez l’appareil.

7. Répétez les étapes précédentes (1 à 5) sur votre déploiement Azure Stack Hub.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack-hub"></a>Créer une passerelle de réseau local dans Azure et Azure Stack Hub

La passerelle de réseau local fait généralement référence à votre emplacement local. Vous donnez au site un nom auquel Azure ou Azure Stack Hub peut faire référence, puis vous spécifiez :

- L’adresse IP du périphérique VPN local pour lequel vous créez une connexion.
- Les préfixes d’adresses IP qui seront acheminés via la passerelle VPN vers le périphérique VPN. Les préfixes d’adresses que vous spécifiez sont les préfixes situés sur votre réseau local.

  >[!Note]
  >En cas de changement du réseau local ou si vous devez modifier l’adresse IP publique du périphérique VPN, vous pouvez mettre à jour ces valeurs ultérieurement.

1. Dans le portail Azure, sélectionnez **+Créer une ressource**.
2. Dans la zone de recherche, entrez **Passerelle de réseau local**, puis sélectionnez **Entrée** pour lancer la recherche. Une liste de résultats s’affiche.
3. Sélectionnez **Passerelle de réseau local**, puis **Créer** pour ouvrir la page **Créer une passerelle de réseau local**.
4. Dans **Créer une passerelle de réseau local**, spécifiez les valeurs de votre passerelle de réseau local en utilisant nos **exemples de valeurs du tutoriel**. Incluez les valeurs supplémentaires suivantes :

    - **Adresse IP** : adresse IP publique du périphérique VPN auquel vous souhaitez qu'Azure ou Azure Stack Hub se connecte. Spécifiez une adresse IP publique valide qui ne se trouve pas derrière un NAT pour qu’Azure puisse atteindre l’adresse. Si vous ne disposez pas de l’adresse IP pour l’instant, vous pouvez utiliser une valeur de l’exemple comme un espace réservé. Vous devrez revenir en arrière et remplacer l’espace réservé par l’adresse IP publique de votre périphérique VPN. Azure ne peut pas se connecter à l’appareil tant que vous ne fournissez pas une adresse valide.
    - **Espace d'adressage** : plage d'adresses du réseau que représente ce réseau local. Vous pouvez ajouter plusieurs plages d’espaces d’adressage. Assurez-vous que les plages que vous spécifiez ici ne chevauchent pas les plages d’autres réseaux auxquels vous souhaitez vous connecter. Azure achemine la plage d’adresses que vous spécifiez vers l’adresse IP du périphérique VPN local. Utilisez vos propres valeurs (et non un exemple de valeur) si vous voulez vous connecter à votre site local.
    - **Configurer les paramètres BGP** : À utiliser uniquement durant la configuration de BGP. Dans le cas contraire, ne sélectionnez pas cette option.
    - **Abonnement**: Vérifiez que l’abonnement approprié s’affiche.
    - **Groupe de ressources** : Sélectionnez le groupe de ressources à utiliser. Vous pouvez créer un groupe de ressources ou en sélectionner un déjà créé.
    - **Emplacement** : Sélectionnez l’emplacement dans lequel cet objet va être créé. Vous pouvez sélectionner l’emplacement dans lequel se trouve votre réseau virtuel, mais vous n’êtes pas obligé de le faire.
5. Lorsque vous avez terminé de spécifier les valeurs requises, sélectionnez **Créer** pour créer la passerelle de réseau local.
6. Répétez ces étapes (1 à 5) sur votre déploiement Azure Stack Hub.

## <a name="configure-your-connection"></a>Configurer votre connexion

Les connexions de site à site vers un réseau local nécessitent un appareil VPN. Le périphérique VPN que vous configurez est appelé une connexion. Pour configurer votre connexion, vous avez besoin des éléments suivants :

- Une clé partagée. Clé partagée que vous avez spécifiée lors de la création de la connexion VPN de site à site. Dans nos exemples, nous utilisons une clé partagée basique. Nous vous conseillons de générer une clé plus complexe.
- L’adresse IP publique de votre passerelle de réseau virtuel. Vous pouvez afficher l’adresse IP publique à l’aide du portail Azure, de PowerShell ou de l’interface de ligne de commande. Pour rechercher l’adresse IP publique de votre passerelle VPN à l’aide du portail Azure, accédez à Passerelles de réseau virtuel, puis sélectionnez le nom de votre passerelle.

Procédez comme suit pour créer une connexion VPN de site à site entre votre passerelle de réseau virtuel et votre appareil VPN local.

1. Dans le portail Azure, sélectionnez **+ Créer une ressource**.
2. Recherchez des **connexions**.
3. Dans **Résultats**, sélectionnez **Connexions**.
4. Dans **Connexions**, sélectionnez **Créer**.
5. Dans **Créer une connexion**, configurez les paramètres suivants :

    - **Type de connexion** : Sélectionnez Site à site (IPsec).
    - **Groupe de ressources** : sélectionnez votre groupe de ressources de test.
    - **Passerelle de réseau virtuel** : sélectionnez la passerelle de réseau virtuel que vous avez créée.
    - **Passerelle de réseau local** : sélectionnez la passerelle de réseau local que vous avez créée.
    - **Nom de la connexion** : ce champ est automatiquement renseigné avec les valeurs provenant des deux passerelles.
    - **Clé partagée** : cette valeur doit correspondre à celle que vous utilisez pour votre appareil VPN local. Dans l’exemple du didacticiel, nous avons utilisé « abc123 », mais vous pouvez utiliser une valeur plus complexe. L’important, c’est que cette valeur *DOIT* être identique à celle spécifiée lors de la configuration de votre périphérique VPN.
    - Les valeurs pour **Abonnement**, **Groupe de ressources**, et **Emplacement** sont fixes.

6. Sélectionnez **OK** pour créer votre connexion.

Vous pouvez afficher la connexion dans la page **Connexions** de la passerelle de réseau virtuel. L’état passe de *Inconnu* à *Connexion*, puis à *Réussi*.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les modèles Azure Cloud, consultez [Modèles de conception cloud](https://docs.microsoft.com/azure/architecture/patterns).
