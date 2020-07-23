---
title: Connecter Azure Stack Hub à Azure à l’aide d’un VPN
description: Découvrez comment connecter des réseaux virtuels Azure Stack Hub à des réseaux virtuels Azure à l’aide d’un VPN.
author: sethmanheim
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 10/24/2019
ms.openlocfilehash: dc143c4cfc6beec14891caa7e23d3f058f49eae5
ms.sourcegitcommit: 0aa5f7f20690839661c8bb3bfdbe32f82bec0c64
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86567669"
---
# <a name="connect-azure-stack-hub-to-azure-using-vpn"></a>Connecter Azure Stack Hub à Azure à l’aide d’un VPN

Cet article explique comment créer un VPN de site à site pour connecter un réseau virtuel dans Azure Stack Hub à un réseau virtuel dans Azure.

## <a name="before-you-begin"></a>Avant de commencer

Avant de commencer la configuration de la connexion, vérifiez que vous disposez des éléments suivants :

* Un déploiement de systèmes intégrés Azure Stack Hub (plusieurs nœuds) directement connecté à Internet. Votre plage d’adresses IP publiques externe doit être directement accessible à partir de l’Internet public.
* Un abonnement Azure valide. Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte Azure gratuit ici](https://azure.microsoft.com/free/?b=17.06).

### <a name="vpn-connection-diagram"></a>Diagramme de connexions VPN

La figure ci-après illustre à quoi la configuration d’une connexion devra ressembler lorsque vous aurez terminé :

![Configuration d’une connexion VPN de site à site](media/azure-stack-connect-vpn/azure-stack-connect-vpn-image2.svg)

### <a name="network-configuration-example-values"></a>Exemples de valeurs de configuration réseau

Le tableau d’exemples de configuration réseau montre les valeurs utilisées pour les exemples de cet article. Pour mieux comprendre cet article, vous pouvez utiliser les valeurs suivantes ou vous y référer :

|   |Azure Stack Hub|Azure|
|---------|---------|---------|
|Nom du réseau virtuel     |Azs-VNet|AzureVNet |
|Espace d’adressage du réseau virtuel |10.1.0.0/16|10.100.0.0/16|
|Nom du sous-réseau     |FrontEnd|FrontEnd|
|Plage d’adresses de sous-réseau|10.1.0.0/24 |10.100.0.0/24 |
|Sous-réseau de passerelle     |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>Créer les ressources réseau dans Azure

Tout d’abord, créez les ressources réseau pour Azure. Les instructions ci-après vous indiquent comment créer les ressources à partir du [portail Azure](https://portal.azure.com/).

### <a name="create-the-virtual-network-and-virtual-machine-vm-subnet"></a>Créer le réseau virtuel et le sous-réseau de machine virtuelle

1. Connectez-vous au [portail Azure](https://portal.azure.com/) avec votre compte Azure.
2. Dans le portail utilisateur, sélectionnez **+ Créer une ressource**.
3. Accédez à la **Place de marché**, puis sélectionnez **Mise en réseau**.
4. Sélectionnez **Réseau virtuel**.
5. À l’aide des informations données dans la table de configuration réseau, renseignez les champs **Nom**, **Espace d’adressage**, **Nom de sous-réseau** et **Plage d’adresses de sous-réseau** de manière appropriée pour Azure.
6. Pour le champ **Groupe de ressources**, créez un groupe de ressources ou, si vous en avez déjà un, sélectionnez **Utiliser l’existant**.
7. Sélectionnez l’**emplacement** de votre réseau virtuel.  Si vous utilisez les exemples de valeurs, sélectionnez **USA Est** ou utilisez un autre emplacement.
8. Sélectionnez **Épingler au tableau de bord**.
9. Sélectionnez **Create** (Créer).

### <a name="create-the-gateway-subnet"></a>Créer le sous-réseau de passerelle

1. Ouvrez la ressource de réseau virtuel que vous avez créée (**AzureVNet**) à partir du tableau de bord.
2. Dans la section **Paramètres**, sélectionnez **Sous-réseaux**.
3. Sélectionnez **Sous-réseau de passerelle** pour ajouter un sous-réseau de passerelle au réseau virtuel.
4. Par défaut, le nom du sous-réseau est défini sur **GatewaySubnet**.

   >[!IMPORTANT]
   >Les sous-réseaux de passerelle sont des éléments spéciaux et doivent porter ce nom pour fonctionner correctement.

5. Dans le champ **Plage d’adresses**, vérifiez que l’adresse est **10.100.1.0/24**.
6. Sélectionnez **OK** pour créer le sous-réseau de passerelle.

### <a name="create-the-virtual-network-gateway"></a>Créer la passerelle de réseau virtuel

1. Dans le Portail Azure, sélectionnez **+ Créer une ressource**.

2. Accédez à la **Place de marché**, puis sélectionnez **Mise en réseau**.
3. Dans la liste des ressources réseau, sélectionnez **Passerelle de réseau virtuel**.
4. Dans le champ **Nom**, tapez **Azure-GW**.
5. Pour choisir un réseau virtuel, sélectionnez **Réseau virtuel**. Sélectionnez ensuite **AzureVnet** dans la liste.
6. Sélectionnez **Adresse IP publique**. Quand la section **Choisir une adresse IP publique** s’affiche, sélectionnez **Créer**.
7. Dans le champ **Nom**, tapez **Azure-GW-PiP**, puis sélectionnez **OK**.
8. Vérifiez que l’**abonnement** et l’**emplacement** sont corrects. Vous pouvez épingler la ressource au tableau de bord. Sélectionnez **Create** (Créer).

### <a name="create-the-local-network-gateway-resource"></a>Créer la ressource de passerelle de réseau local

1. Dans le Portail Azure, sélectionnez **+ Créer une ressource**.

2. Accédez à la **Place de marché**, puis sélectionnez **Mise en réseau**.
3. Dans la liste des ressources, sélectionnez **Passerelle de réseau local**.
4. Dans le champ **Nom**, tapez **Azs-GW**.
5. Dans le champ **Adresse IP**, tapez l’adresse IP publique de votre passerelle de réseau virtuel Azure Stack Hub, indiquée précédemment dans le tableau de configuration réseau.
6. Dans le champ **Espace d’adressage**, à partir d’Azure Stack Hub, entrez l’espace d’adressage **10.1.0.0/24** et **10.1.1.0/24** pour **AzureVNet**.
7. Vérifiez l’exactitude des valeurs des champs **Abonnement**, **Groupe de ressources** et **Emplacement**, puis sélectionnez **Créer**.

## <a name="create-the-connection"></a>Créer la connexion

1. Dans le portail utilisateur, sélectionnez **+ Créer une ressource**.
2. Accédez à la **Place de marché**, puis sélectionnez **Mise en réseau**.
3. Dans la liste des ressources, sélectionnez **Connexion**.
4. Dans la section de paramètres **De base**, pour le champ **Type de connexion**, choisissez **Site à site (IPSec)** .
5. Renseignez les champs **Abonnement**, **Groupe de ressources** et **Emplacement**, puis sélectionnez **OK**.
6. Dans la section **Paramètres**, sélectionnez **Passerelle de réseau virtuel**, puis **Azure-GW**.
7. Sélectionnez **Passerelle de réseau local**, puis **Azs-GW**.
8. Dans le champ **Nom de la connexion**, saisissez **Azure-Azs**.
9. Dans le champ **Clé partagée (PSK)** , saisissez **12345**, puis sélectionnez **OK**.

   >[!NOTE]
   >Si vous utilisez une autre valeur pour la clé partagée, n’oubliez pas qu’elle doit correspondre à la valeur de la clé partagée que vous créez à l’autre extrémité de la connexion.

10. Dans la section **Résumé**, vérifiez les paramètres, puis sélectionnez **OK**.

## <a name="create-a-custom-ipsec-policy"></a>Créer une stratégie IPsec personnalisée

Comme les paramètres par défaut Azure Stack Hub pour les stratégies IPSec ont changé pour les [builds 1910 et ultérieures](azure-stack-vpn-gateway-settings.md#ipsecike-parameters), une stratégie IPSec personnalisée est nécessaire pour qu’Azure corresponde à Azure Stack Hub.

1. Créez une stratégie personnalisée :

   ```powershell
     $IPSecPolicy = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup ECP384  `
     -IpsecEncryption GCMAES256 -IpsecIntegrity GCMAES256 -PfsGroup ECP384 -SALifeTimeSeconds 27000 `
     -SADataSizeKilobytes 102400000 
   ```

2. Appliquez la stratégie à la connexion :

   ```powershell
   $Connection = Get-AzVirtualNetworkGatewayConnection -Name myTunnel -ResourceGroupName myRG
   Set-AzVirtualNetworkGatewayConnection -IpsecPolicies $IPSecPolicy -VirtualNetworkGatewayConnection $Connection
   ```

## <a name="create-a-vm"></a>Créer une machine virtuelle

À présent, créez une machine virtuelle dans Azure, puis ajoutez-la au sous-réseau de machine virtuelle dans votre réseau virtuel.

1. Dans le Portail Azure, sélectionnez **+ Créer une ressource**.
2. Accédez à la **Place de marché**, puis sélectionnez **Compute**.
3. Dans la liste des images de machine virtuelle, sélectionnez l’image **Windows Server 2016 Datacenter Eval**.
4. Dans la section **De base**, sous **Nom**, saisissez **AzureVM**.
5. Entrez un nom d’utilisateur et un mot de passe valides. Vous utiliserez ce compte plus tard pour vous connecter à la machine virtuelle que vous créez actuellement.
6. Renseignez les champs **Abonnement**, **Groupe de ressources** et **Emplacement**, puis sélectionnez **OK**.
7. Dans la section **Taille**, sélectionnez une taille de machine virtuelle pour cette instance, puis choisissez **Sélectionner**.
8. Dans la section **Paramètres**, vous pouvez utiliser les paramètres par défaut. Avant de sélectionner **OK**, vérifiez que :

   * Le réseau virtuel **AzureVnet** est sélectionné.
   * Le sous-réseau est défini sur **10.100.0.0/24**.

   Sélectionnez **OK**.

9. Dans la section **Résumé**, vérifiez les paramètres, puis sélectionnez **OK**.

## <a name="create-the-network-resources-in-azure-stack-hub"></a>Créer les ressources réseau dans Azure Stack Hub

Ensuite, créez les ressources réseau dans Azure Stack Hub.

### <a name="sign-in-as-a-user"></a>Connectez-vous en tant qu’utilisateur

Un administrateur de service peut se connecter en tant qu’utilisateur pour tester les plans, les offres et les abonnements mis à la disposition des utilisateurs. Si vous ne l’avez pas encore fait, [créez un compte d’utilisateur](../operator/azure-stack-add-new-user-aad.md) avant de vous connecter.

### <a name="create-the-virtual-network-and-a-vm-subnet"></a>Créer le réseau virtuel et un sous-réseau de machine virtuelle

1. Utilisez un compte d’utilisateur pour vous connecter au portail utilisateur.
2. Dans le portail utilisateur, sélectionnez **+ Créer une ressource**.

    ![Créer un réseau virtuel](media/azure-stack-connect-vpn/image3.png)

3. Accédez à la **Place de marché**, puis sélectionnez **Mise en réseau**.
4. Sélectionnez **Réseau virtuel**.
5. Renseignez les champs **Nom**, **Espace d’adressage**, **Nom de sous-réseau** et **Plage d’adresses de sous-réseau** avec les valeurs qui figurent dans la table de configuration réseau.
6. Le champ **Abonnement** affiche l’abonnement que vous avez créé précédemment.
7. Pour le champ **Groupe de ressources**, créez un groupe de ressources ou, si vous en avez déjà un, sélectionnez **Utiliser l’existant**.
8. Vérifiez l’emplacement par défaut.
9. Sélectionnez **Épingler au tableau de bord**.
10. Sélectionnez **Create** (Créer).

### <a name="create-the-gateway-subnet"></a>Créer le sous-réseau de passerelle

1. Dans le tableau de bord, ouvrez la ressource de réseau virtuel Azs-VNet que vous avez créée.
2. Dans la section **Paramètres**, sélectionnez **Sous-réseaux**.
3. Pour ajouter un sous-réseau de passerelle au réseau virtuel, sélectionnez **Sous-réseau de passerelle**.

    ![Ajouter un sous-réseau de passerelle](media/azure-stack-connect-vpn/image4.png)

4. Par défaut, le nom du sous-réseau est **GatewaySubnet**. Pour que les sous-réseaux de passerelle fonctionnent correctement, ils doivent utiliser le nom **GatewaySubnet**.
5. Dans **Plage d’adresses**, vérifiez que l’adresse est **10.1.1.0/24**.
6. Sélectionnez **OK** pour créer le sous-réseau de passerelle.

### <a name="create-the-virtual-network-gateway"></a>Créer la passerelle de réseau virtuel

1. Dans le portail Azure Stack Hub, sélectionnez **+ Créer une ressource**.
2. Accédez à la **Place de marché**, puis sélectionnez **Mise en réseau**.
3. Dans la liste des ressources réseau, sélectionnez **Passerelle de réseau virtuel**.
4. Dans le champ **Nom**, saisissez **Azs-GW**.
5. Sélectionnez l’élément **Réseau virtuel** pour choisir un réseau virtuel. Sélectionnez **Azs-VNet** dans la liste.
6. Sélectionnez l’élément de menu **Adresse IP publique**. Quand la section **Choisir une adresse IP publique** s’affiche, sélectionnez **Créer**.
7. Dans le champ **Nom**, saisissez **Azs-GW-PiP**, puis sélectionnez **OK**.
8. L’élément **Basé sur itinéraires** est sélectionné par défaut dans le champ **Type de VPN**. Conservez le type de VPN **Basé sur itinéraires**.

9. Vérifiez que l’**abonnement** et l’**emplacement** sont corrects. Vous pouvez épingler la ressource au tableau de bord. Sélectionnez **Create** (Créer).

### <a name="create-the-local-network-gateway"></a>Créer la passerelle de réseau local

Dans Azure Stack Hub, le concept de *passerelle de réseau local* diffère légèrement de celui dans un déploiement Azure.

En effet, dans un déploiement Azure, une passerelle de réseau local représente un appareil physique local (à l’emplacement de l’utilisateur) que vous vous connectez à une passerelle de réseau virtuel dans Azure. Cependant, dans Azure Stack Hub, les deux extrémités de la connexion sont des passerelles de réseau virtuel.

De façon plus générale, la ressource de passerelle de réseau local représente toujours la passerelle distante située à l’autre extrémité de la connexion.

### <a name="create-the-local-network-gateway-resource"></a>Créer la ressource de passerelle de réseau local

1. Connectez-vous au portail Azure Stack Hub.
2. Dans le portail utilisateur, sélectionnez **+ Créer une ressource**.
3. Accédez à la **Place de marché**, puis sélectionnez **Mise en réseau**.
4. Dans la liste des ressources, sélectionnez **Passerelle de réseau local**.
5. Dans le champ **Nom**, tapez **Azure-GW**.
6. Dans le champ **Adresse IP**, tapez l’adresse IP publique de la passerelle de réseau virtuel dans Azure **Azure-GW-PiP**. Cette adresse figure dans la table de configuration réseau, plus haut dans cet article.
7. Dans le champ **Espace d’adressage**, tapez **10.100.0.0/24** et **10.100.1.0/24** comme espace d’adressage du réseau virtuel Azure que vous venez de créer.

8. Vérifiez l’exactitude des valeurs des champs **Abonnement**, **Groupe de ressources** et **Emplacement**, puis sélectionnez **Créer**.

### <a name="create-the-connection"></a>Créer la connexion

1. Dans le portail utilisateur, sélectionnez **+ Créer une ressource**.
2. Accédez à la **Place de marché**, puis sélectionnez **Mise en réseau**.
3. Dans la liste des ressources, sélectionnez **Connexion**.
4. Dans la section des paramètres **De base**, pour le champ **Type de connexion**, sélectionnez **Site à site (IPSec)** .
5. Renseignez les champs **Abonnement**, **Groupe de ressources** et **Emplacement**, puis sélectionnez **OK**.
6. Dans la section **Paramètres**, sélectionnez **Passerelle de réseau virtuel**, puis **Azs-GW**.
7. Sélectionnez **Passerelle de réseau local**, puis **Azure-GW**.
8. Dans le champ **Nom de la connexion**, entrez **Azs-Azure**.
9. Sous **Clé partagée (PSK)** , entrez **12345**, puis sélectionnez **OK**.

10. Dans la section **Résumé**, sélectionnez **OK**.

### <a name="create-a-vm"></a>Créer une machine virtuelle

Pour vérifier la connexion VPN, créez deux machines virtuelles, une dans Azure et l’autre dans Azure Stack Hub. Après avoir créé ces machines virtuelles, vous pouvez les utiliser pour envoyer et recevoir des données via le tunnel VPN.

1. Dans le Portail Azure, sélectionnez **+ Créer une ressource**.
2. Accédez à la **Place de marché**, puis sélectionnez **Compute**.
3. Dans la liste des images de machine virtuelle, sélectionnez l’image **Windows Server 2016 Datacenter Eval**.
4. Dans la section **De base**, sous **Nom**, saisissez **Azs-VM**.
5. Entrez un nom d’utilisateur et un mot de passe valides. Vous utiliserez ce compte plus tard pour vous connecter à la machine virtuelle que vous créez actuellement.
6. Renseignez les champs **Abonnement**, **Groupe de ressources** et **Emplacement**, puis sélectionnez **OK**.
7. Dans la section **Taille**, sélectionnez une taille de machine virtuelle pour cette instance, puis choisissez **Sélectionner**.
8. Dans la section **Paramètres**, acceptez les valeurs par défaut. Assurez-vous que le réseau virtuel **Azs-VNet** est sélectionné. Vérifiez que le sous-réseau est défini sur **10.1.0.0/24**. Sélectionnez ensuite **OK**.

9. Dans la section **Résumé**, vérifiez les paramètres, puis sélectionnez **OK**.

## <a name="test-the-connection"></a>Tester la connexion

Maintenant que la connexion de site à site est établie, vous devez vérifier que les données transitent dans les deux directions. Le moyen le plus simple de tester la connexion est d’effectuer un test ping :

* Connectez-vous à la machine virtuelle que vous avez créée dans Azure Stack Hub et effectuez un test ping sur la machine virtuelle dans Azure.
* Connectez-vous à la machine virtuelle que vous avez créée dans Azure et effectuez un test ping sur la machine virtuelle dans Azure Stack Hub.

>[!NOTE]
>Pour vérifier que le trafic envoyé passe par la connexion de site à site, effectuez le test ping sur l’adresse IP directe (DIP) de la machine virtuelle du sous-réseau distant, et non sur l’adresse IP virtuelle.

### <a name="sign-in-to-the-user-vm-in-azure-stack-hub"></a>Se connecter à la machine virtuelle utilisateur dans Azure Stack Hub

1. Connectez-vous au portail Azure Stack Hub.
2. Sélectionnez **Machines virtuelles** dans la barre de navigation de gauche.
3. Dans la liste des machines virtuelles, recherchez la machine virtuelle **AZs-VM** que vous avez créée précédemment et sélectionnez-la.
4. Dans la section de la machine virtuelle, sélectionnez **Se connecter**, puis ouvrez le fichier Azs-VM.rdp.

     ![Bouton Se connecter](media/azure-stack-connect-vpn/image17.png)

5. Connectez-vous avec le compte que vous avez configuré pendant la création de la machine virtuelle.
6. Ouvrez une invite Windows PowerShell avec des privilèges élevés.
7. Entrez **ipconfig/all**.
8. Dans la sortie, recherchez la valeur **Adresse IPv4**, puis enregistrez l’adresse pour l’utiliser plus tard. Il s’agit de l’adresse sur laquelle vous allez effectuer un test Ping à partir d’Azure. Dans l’exemple d’environnement, l’adresse est **10.1.0.4**, mais peut être différente dans votre environnement. Elle doit faire partie du sous-réseau **10.1.0.0/24** que vous avez créé précédemment.
9. Pour créer une règle de pare-feu qui autorise la machine virtuelle à répondre aux tests ping, exécutez la commande PowerShell suivante :

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>Se connecter à la machine virtuelle locataire dans Azure

1. Connectez-vous au portail Azure.
2. Sélectionnez **Machines virtuelles** dans la barre de navigation de gauche.
3. Dans la liste des machines virtuelles, recherchez la machine virtuelle **Azure-VM** que vous avez créée précédemment et sélectionnez-la.
4. Dans la section de la machine virtuelle, sélectionnez **Se connecter**.
5. Connectez-vous avec le compte que vous avez configuré pendant la création de la machine virtuelle.
6. Ouvrez une fenêtre **Windows PowerShell** avec des privilèges élevés.
7. Entrez **ipconfig/all**.
8. Vous devez normalement voir une adresse IPv4 qui fait partie du sous-réseau **10.100.0.0/24**. Dans l’exemple d’environnement, l’adresse est **10.100.0.4**, mais votre adresse peut être différente.
9. Pour créer une règle de pare-feu qui autorise la machine virtuelle à répondre aux tests ping, exécutez la commande PowerShell suivante :

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

10. À partir de la machine virtuelle dans Azure, effectuez un test ping de la machine virtuelle dans Azure Stack Hub, par le biais du tunnel. Pour cela, effectuez un test Ping sur l’adresse IP directe (DIP) que vous avez enregistrée à partir d’Azs-VM. Dans l’exemple d’environnement, l’adresse est **10.1.0.4**, mais vous devez effectuer le test ping sur l’adresse que vous avez notée dans votre lab. Vous devez obtenir un résultat semblable à celui présenté dans la capture d’écran suivante :

    ![Test ping réussi](media/azure-stack-connect-vpn/image19b.png)

11. Une réponse de la machine virtuelle distante indique que le test a réussi. Vous pouvez fermer la fenêtre de la machine virtuelle.

Vous devez également effectuer un test plus rigoureux de transfert de données, par exemple en copiant des fichiers de tailles différentes dans les deux sens.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Affichage des statistiques de transfert de données via la connexion de passerelle

Si vous souhaitez connaître la quantité de données qui transite par votre connexion de site à site, consultez cette information dans la section **Connexion**. Ce test est également un autre moyen de vérifier que la commande ping que vous venez d’envoyer est bien passée par la connexion VPN.

1. Une fois connecté à la machine virtuelle utilisateur dans Azure Stack Hub, utilisez votre compte d’utilisateur pour vous connecter au portail utilisateur.
2. Accédez à **Toutes les ressources**, puis sélectionnez la connexion **Azs-Azure**. Le panneau **Connexion** s’affiche.
3. La section **Connexion** affiche les statistiques pour **Données entrantes** et **Données sortantes**. Dans la capture d’écran suivante, les valeurs élevées sont dues au transfert de fichiers supplémentaires. Normalement, vous ne devez pas voir de valeurs nulles ici.

    ![Données entrantes et sortantes](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>Étapes suivantes

* [Déployer des applications sur Azure et Azure Stack Hub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/tree/master/hybrid-devops)
