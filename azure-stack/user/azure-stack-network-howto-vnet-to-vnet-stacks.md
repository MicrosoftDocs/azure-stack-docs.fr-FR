---
title: Établir une connexion de réseau virtuel à réseau virtuel Azure Stack Hub avec une NVA Fortinet FortiGate
description: Découvrez comment établir une connexion de réseau virtuel à réseau virtuel dans Azure Stack Hub avec l’appliance virtuelle réseau Fortinet FortiGate
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: fbce070042c711a03d0a0352252ee37679c9ab07
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525708"
---
# <a name="vnet-to-vnet-connectivity-between-azure-stack-hub-instances-with-fortinet-fortigate-nva"></a>Connectivité de réseau virtuel à réseau virtuel entre les instances Azure Stack Hub avec Fortinet FortiGate NVA

Dans cet article, vous allez connecter un réseau virtuel dans un environnement Azure Stack Hub à un réseau virtuel dans un autre environnement Azure Stack Hub en utilisant l’appliance virtuelle réseau Fortinet FortiGate.

Cet article traite de la limitation actuelle d'Azure Stack Hub, qui permet aux locataires de configurer une seule connexion VPN entre deux environnements. Les utilisateurs apprennent à configurer une passerelle personnalisée sur une machine virtuelle Linux qui autorise plusieurs connexions VPN entre différents environnements Azure Stack Hub. La procédure décrite dans cet article déploie deux réseaux virtuels avec une appliance virtuelle réseau FortiGate dans chaque réseau virtuel : un déploiement par environnement Azure Stack Hub. Il détaille également les modifications requises pour configurer un VPN IPSec entre les deux réseaux virtuels. Les étapes décrites dans cet article doivent être répétées pour chaque réseau virtuel dans chaque environnement Azure Stack Hub. 

## <a name="prerequisites"></a>Prérequis

-  Accès à un système intégré Azure Stack Hub permettant de mettre en œuvre les exigences de calcul, de réseau et de ressources nécessaires pour cette solution. 

    > [!NOTE]  
    > Ces instructions **ne fonctionnent pas** avec un Kit de développement Azure Stack (ASDK) en raison des limitations réseau dans le kit ASDK. Pour plus d’informations, consultez [Exigences et éléments à prendre en compte pour ASDK](../asdk/asdk-deploy-considerations.md).

-  Une solution d’appliance virtuelle réseau téléchargée et publiée sur la Place de marché Azure Stack Hub. Une appliance virtuelle réseau contrôle le flux du trafic réseau depuis un réseau de périmètre vers d’autres réseaux ou sous-réseaux. Cette procédure utilise la [solution de machine virtuelle unique Pare-feu Fortigate de nouvelle génération](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

-  Au moins deux fichiers de licence FortiGate disponibles pour activer l’appliance virtuelle réseau FortiGate. Pour plus d’informations sur la façon d’obtenir ces licences, consultez l’article de la bibliothèque de documents Fortinet intitulé [Registering and downloading your license](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

    Cette procédure utilise le [déploiement Single FortiGate-VM](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment). Vous trouverez des instructions sur la façon de connecter l’appliance virtuelle réseau FortiGate au réseau virtuel Azure Stack Hub dans votre réseau local.

    Pour plus d’informations sur le déploiement de la solution FortiGate dans une configuration active-passive (HA), consultez l’article de la bibliothèque de documents Fortinet intitulé [HA for FortiGate-VM on Azure](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure).

## <a name="deployment-parameters"></a>Paramètres de déploiement

Le tableau suivant récapitule les paramètres utilisés dans ces déploiements pour référence :

### <a name="deployment-one-forti1"></a>Déploiement 1 : Forti1

| Nom de l’instance FortiGate | Forti1 |
|-----------------------------------|---------------------------|
| License BYOL/Version | 6.0.3 |
| Nom d’utilisateur d’administration FortiGate | fortiadmin |
| Nom du groupe ressources | forti1-rg1 |
| Nom du réseau virtuel | forti1vnet1 |
| Espace d’adressage du réseau virtuel | 172.16.0.0/16* |
| Nom du sous-réseau de réseau virtuel public | forti1-PublicFacingSubnet |
| Préfixe d’adresse de réseau virtuel public | 172.16.0.0/24* |
| Nom de sous-réseau de réseau virtuel interne | forti1-InsideSubnet |
| Préfixe de sous-réseau de réseau virtuel interne | 172.16.1.0/24* |
| Taille de machine virtuelle de l’appliance virtuelle réseau FortiGate | Standard F2s_v2 |
| Nom de l’adresse IP publique | forti1-publicip1 |
| Type d’adresse IP publique | statique |

### <a name="deployment-two-forti2"></a>Déploiement 2 : Forti2

| Nom de l’instance FortiGate | Forti2 |
|-----------------------------------|---------------------------|
| License BYOL/Version | 6.0.3 |
| Nom d’utilisateur d’administration FortiGate | fortiadmin |
| Nom du groupe ressources | forti2-rg1 |
| Nom du réseau virtuel | forti2vnet1 |
| Espace d’adressage du réseau virtuel | 172.17.0.0/16* |
| Nom du sous-réseau de réseau virtuel public | forti2-PublicFacingSubnet |
| Préfixe d’adresse de réseau virtuel public | 172.17.0.0/24* |
| Nom de sous-réseau de réseau virtuel interne | Forti2-InsideSubnet |
| Préfixe de sous-réseau de réseau virtuel interne | 172.17.1.0/24* |
| Taille de machine virtuelle de l’appliance virtuelle réseau FortiGate | Standard F2s_v2 |
| Nom de l’adresse IP publique | Forti2-publicip1 |
| Type d’adresse IP publique | statique |

> [!NOTE]
> \* Choisissez un autre ensemble d’espaces d’adressage et de préfixes de sous-réseau si les paramètres ci-dessus chevauchent de quelque façon que ce soit l’environnement réseau local, y compris le pool d’adresses IP virtuelles d’un des environnements Azure Stack Hub. Assurez-vous également que les plages d’adresses ne se chevauchent pas.**

## <a name="deploy-the-fortigate-ngfw-marketplace-items"></a>Déployer les éléments de la Place de marché concernant le Pare-feu Fortigate de nouvelle génération

Répétez ces étapes pour les deux environnements Azure Stack Hub. 

1. Connectez-vous au portail utilisateur Azure Stack Hub. Veillez à utiliser des informations d’identification qui ont au moins des droits de collaborateur pour un abonnement.

1. Sélectionnez **Créer une ressource**, puis recherchez `FortiGate`.

    ![La capture d'écran montre la recherche de « fortigate » n'a renvoyé qu'une seule ligne de résultats. Le nom de l'élément trouvé est « Pare-feu Fortigate de nouvelle génération - Déploiement d'une seule machine virtuelle (BYOL) ».](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image6.png)

1. Sélectionnez le **Pare-feu Fortigate de nouvelle génération**, puis **Créer**.

1. Indiquez les **Informations de base** à l’aide des paramètres du tableau [Paramètres de déploiement](#deployment-parameters).

    Votre formulaire doit contenir les informations suivantes :

    ![Les zones de texte (telles que Nom de l'instance et Licence BYOL) de la boîte de dialogue De base ont été renseignées avec les valeurs de la table de déploiement.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image7.png)

1. Sélectionnez **OK**.

1. Indiquez les détails relatifs au réseau virtuel, aux sous-réseaux et à la taille de machine virtuelle à partir des [paramètres de déploiement](#deployment-parameters).

    Si vous souhaitez utiliser des noms et des plages différents, veillez à ne pas utiliser de paramètres susceptibles d’entrer en conflit avec les autres ressources de réseau virtuel et FortiGate dans l’autre environnement Azure Stack Hub. Cela est particulièrement vrai lors de la définition de la plage d’adresses IP de réseau virtuel et des plages de sous-réseaux au sein du réseau virtuel. Vérifiez qu’elles ne chevauchent pas les plages d’adresses IP de l’autre réseau virtuel que vous créez.

1. Sélectionnez **OK**.

1. Configurez l’adresse IP publique qui sera utilisée pour l’appliance virtuelle réseau FortiGate :

    ![La zone de texte « Nom de l'adresse IP publique » de la boîte de dialogue Attribution IP affiche la valeur « forti1-publicip1 » (issue de la table de déploiement).](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image8.png)

1. Sélectionnez **OK**, puis **OK**.

1. Sélectionnez **Create** (Créer).

Le déploiement prendra environ 10 minutes. Vous pouvez maintenant répéter les étapes pour créer l’autre déploiement de réseau virtuel et d’appliance virtuelle réseau FortiGate dans l’autre environnement Azure Stack Hub.

## <a name="configure-routes-udrs-for-each-vnet"></a>Configurer des routes (UDR) pour chaque réseau virtuel

Effectuez ces étapes pour les deux déploiements (forti1-rg1 et forti2-rg1).

1. Accédez au groupe de ressources forti1-rg1 dans le portail Azure Stack Hub.

    ![Voici une capture d'écran de la liste des ressources du groupe de ressources forti1-rg1.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image9.png)

2. Sélectionnez la ressource « forti1-forti1-InsideSubnet-routes-xxxx ».

3. Sélectionnez **Routes** sous **Paramètres**.

    ![La capture d'écran montre l'élément Routes en surbrillance sous Paramètres.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image10.png)

4. Supprimez la route **to-Internet**.

    ![La capture d'écran montre la route « to-Internet » en surbrillance. Un bouton Supprimer est disponible.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image11.png)

5. Sélectionnez **Oui**.

6. Sélectionnez **Ajouter**.

7. Nommez la **Route** `to-forti1` ou `to-forti2`. Utilisez votre plage d’adresses IP si vous utilisez une plage d’adresses IP différente.

8. Entrez :
    - forti1 : `172.17.0.0/16`  
    - forti2 : `172.16.0.0/16`  

    Utilisez votre plage d’adresses IP si vous utilisez une plage d’adresses IP différente.

9. Sélectionnez **Appliance virtuelle** pour le **Type de tronçon suivant**.
    - forti1 : `172.16.1.4`  
    - forti2 : `172.17.0.4`  

    Utilisez votre plage d’adresses IP si vous utilisez une plage d’adresses IP différente.

    ![La boîte de dialogue Modifier l'itinéraire de to-forti2 comporte des zones de texte avec des valeurs. Le « Préfixe de l'adresse » est 172.17.0.0/16, le « Type de tronçon suivant » est « Appliance virtuelle » et l'« Adresse du tronçon suivant » est 172.16.1.4.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image12.png)

10. Sélectionnez **Enregistrer**.

Répétez les étapes pour chaque route **InsideSubnet** pour chaque groupe de ressources.

## <a name="activate-the-fortigate-nvas-and-configure-an-ipsec-vpn-connection-on-each-nva"></a>Activer les appliances virtuelles réseau FortiGate et configurer une connexion VPN IPSec sur chaque appliance virtuelle réseau

 Vous aurez besoin d’un fichier de licence valide de Fortinet pour activer chaque appliance virtuelle réseau FortiGate. Les appliances virtuelles réseau **ne fonctionnent pas** tant que vous n’avez pas activé chacune d’elles. Pour plus d’informations sur la façon d’obtenir un fichier de licence et d’activer l’appliance virtuelle réseau, consultez l’article de la bibliothèque de documents Fortinet intitulé [Registering and downloading your license](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

Vous devrez acquérir deux fichiers de licence, soit un par appliance virtuelle réseau.

## <a name="create-an-ipsec-vpn-between-the-two-nvas"></a>Créer un VPN IPSec entre les deux appliances virtuelles réseau

Une fois les appliances virtuelles réseau activées, procédez comme suit pour créer un VPN IPSec entre celles-ci.

Suivez les étapes ci-dessous pour les appliances virtuelles réseau forti1 et forti2 :

1. Pour obtenir l’adresse IP publique attribuée, accédez à la page de vue d’ensemble de la machine virtuelle fortiX :

    ![La page de présentation de forti1 affiche le groupe de ressources, l'état, etc.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image13.png)

1. Copiez l’adresse IP attribuée, ouvrez un navigateur, puis collez-la dans la barre d’adresses. Il est possible que votre navigateur vous avertisse que le certificat de sécurité n’est pas approuvé. Continuez quand même.

1. Entrez le nom d’utilisateur d’administration et le mot de passe FortiGate que vous avez fournis pendant le déploiement.

    ![La capture d'écran montre l'écran de connexion, qui comporte un bouton de connexion et des zones de texte pour le nom d'utilisateur et le mot de passe.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image14.png)

1. Sélectionnez **System** (Système)  > **Firmware** (Microprogramme).

1. Cochez la case qui indique le microprogramme le plus récent, par exemple, `FortiOS v6.2.0 build0866`.

    ![La capture d'écran du microprogramme « FortiOS v6.2.0 build0866 » contient un lien vers les notes de publication et deux boutons : « Sauvegarder la configuration et mettre à niveau » et « Mettre à niveau ».](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image15.png)

1. Sélectionnez **Backup config and upgrade** (Configuration et mise à niveau de la sauvegarde) et Continue quand vous y êtes invité.

1. L’appliance virtuelle réseau met à jour son microprogramme avec la dernière build et redémarre. Le processus prend environ cinq minutes. Reconnectez-vous à la console web FortiGate.

1. Cliquez sur **VPN** > **IPSec Wizard** (Assistant IPSec).

1. Entrez un nom pour le VPN, par exemple, `conn1` dans **VPN Creation Wizard** (Assistant de création de VPN).

1. Sélectionnez **This site is behind NAT** (Ce site est derrière NAT).

    ![La capture d'écran de l'Assistant Création d'un VPN montre qu'il se trouve à la première étape, Configuration du VPN. Les valeurs suivantes sont sélectionnées : « Site à site » pour le Type de modèle, « FortiGate » pour le Type de périphérique distant et « Ce site se trouve derrière NAT » pour la Configuration NAT.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image16.png)

1. Sélectionnez **Suivant**.

1. Entrez l’adresse IP distante du périphérique VPN local auquel vous allez vous connecter.

1. Sélectionnez **port1** pour **Outgoing Interface** (Interface sortante).

1. Sélectionnez **Pre-shared Key** (Clé prépartagée), puis entrez (et enregistrez) une clé prépartagée. 

    > [!NOTE]  
    > Vous aurez besoin de cette clé pour configurer la connexion sur le périphérique VPN local ; autrement dit, ils doivent correspondre *exactement*.

    ![La capture d'écran de l'Assistant Création d'un VPN montre qu'il se trouve à la deuxième étape, Authentification, et les valeurs sélectionnées sont en surbrillance.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image17.png)

1. Sélectionnez **Suivant**.

1. Sélectionnez **port2** pour **Local Interface** (Interface locale).

1. Entrez la plage de sous-réseau local :
    - forti1 : 172.16.0.0/16
    - forti2 : 172.17.0.0/16

    Utilisez votre plage d’adresses IP si vous utilisez une plage d’adresses IP différente.

1. Entrez le ou les sous-réseaux distants appropriés qui représentent le réseau local, auquel vous vous connecterez via le périphérique VPN local.
    - forti1 : 172.16.0.0/16
    - forti2 : 172.17.0.0/16

    Utilisez votre plage d’adresses IP si vous utilisez une plage d’adresses IP différente.

    ![La capture d'écran de l'Assistant Création d'un VPN montre qu'il se trouve à la troisième étape, Stratégie et routage, et affiche les valeurs sélectionnées et entrées.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image18.png)

1. Sélectionnez **Créer**

1. Sélectionnez **Network** (Réseau)  > **Interfaces**.  

    ![La liste des interfaces montre deux interfaces : le port1, qui a été configuré, et le port2, qui ne l'a pas été. Des boutons permettent de créer, de modifier et de supprimer des interfaces.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image19.png)

1. Double-cliquez sur **port2**.

1. Choisissez **LAN** dans la liste **Role** et **DHCP** pour le mode d’adressage.

1. Sélectionnez **OK**.

Répétez ces étapes pour l’autre appliance virtuelle réseau.


## <a name="bring-up-all-phase-2-selectors"></a>Afficher tous les sélecteurs de phase 2 

Une fois l’opération ci-dessus effectuée pour les **deux** appliances virtuelles réseau :

1.  Sur la console web FortiGate forti2, sélectionnez **Monitor** (Supervision)  > **IPsec Monitor** (Supervision IPsec). 

    ![La supervision de la connexion VPN conn1 est répertoriée. Il est indiqué qu'elle est désactivée, tout comme le sélecteur de phase 2 correspondant.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image20.png)

2.  Mettez en surbrillance `conn1` et sélectionnez **Bring Up** (Afficher)  > **All Phase 2 Selectors** (Tous les sélecteurs de phase 2).

    ![La supervision et le sélecteur de phase 2 sont tous deux présentés comme activés.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image21.png)


## <a name="test-and-validate-connectivity"></a>Tester et valider la connectivité

Vous devez maintenant être en mesure d’effectuer un routage entre chaque réseau virtuel via les appliances virtuelles réseau FortiGate. Pour valider la connexion, créez une machine virtuelle Azure Stack Hub dans le sous-réseau interne de chaque réseau virtuel. Vous pouvez créer une machine virtuelle Azure Stack Hub via le portail, l’interface CLI ou PowerShell. Durant la création des machines virtuelles :

-   Les machines virtuelles Azure Stack Hub sont placées sur le **sous-réseau interne** de chaque réseau virtuel.

-   Vous **n’appliquez pas** de groupe de sécurité réseau à la machine virtuelle lors de la création (autrement dit, supprimez le NSG qui est ajouté par défaut si vous créez la machine virtuelle à partir du portail).

-   Assurez-vous que les règles de pare-feu de machine virtuelle autorisent la communication que vous allez utiliser pour tester la connectivité. À des fins de test, il est recommandé de désactiver complètement le pare-feu dans le système d’exploitation, si cela est possible.

## <a name="next-steps"></a>Étapes suivantes

[Différences et considérations relatives aux réseaux Azure Stack Hub](azure-stack-network-differences.md)  
[Proposer une solution réseau dans Azure Stack Hub avec Fortinet FortiGate](../operator/azure-stack-network-solutions-enable.md)  
