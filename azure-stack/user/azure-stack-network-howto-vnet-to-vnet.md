---
title: Connecter deux réseaux virtuels dans le même environnement Azure Stack
description: Découvrez comment connecter deux réseaux virtuels au sein du même environnement Azure Stack Hub à l’aide de Fortinet FortiGate.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: b9afd01c79590092269bea8cb4e3ca293fa855fd
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525640"
---
# <a name="vnet-to-vnet-connectivity-with-fortigate"></a>Connectivité de réseau virtuel à réseau virtuel avec Fortigate

Cet article explique comment créer une connexion entre deux réseaux virtuels dans le même environnement. Pendant la configuration des connexions, vous allez découvrir le fonctionnement des passerelles VPN dans Azure Stack Hub. Connectez deux réseaux virtuels au sein du même environnement Azure Stack Hub à l’aide de Fortinet FortiGate. Cette procédure déploie deux réseaux virtuels avec une appliance virtuelle réseau FortiGate dans chaque réseau virtuel dans un groupe de ressources distinct. Il détaille également les modifications requises pour configurer un VPN IPSec entre les deux réseaux virtuels. Répétez les étapes de cet article pour chaque déploiement de réseau virtuel.

## <a name="prerequisites"></a>Prérequis

-   L’accès à un système ayant la capacité de se conformer aux exigences de calcul, de réseau et de ressources nécessaires pour cette solution.

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
> \* Choisissez un autre ensemble d’espaces d’adressage et de préfixes de sous-réseau si les paramètres ci-dessus chevauchent de quelque façon que ce soit l’environnement réseau local, y compris le pool d’adresses IP virtuelles d’un des environnements Azure Stack Hub. Assurez-vous également que les plages d’adresses ne se chevauchent pas.

## <a name="deploy-the-fortigate-ngfw"></a>Déployer le Pare-feu Fortigate de nouvelle génération

1.  Connectez-vous au portail utilisateur Azure Stack Hub.

2.  Sélectionnez **Créer une ressource**, puis recherchez `FortiGate`.

    ![La liste des résultats de la recherche affiche FortiGate NGFW - Single VM Deployment.](./media/azure-stack-network-howto-vnet-to-onprem/image6a.png)

3.  Sélectionnez le **Pare-feu Fortigate de nouvelle génération**, puis **Créer**.

4.  Indiquez les **Informations de base** à l’aide des paramètres du tableau [Paramètres de déploiement](#deployment-parameters).

    ![L'écran Informations de base contient les valeurs des paramètres de déploiement sélectionnées et entrées dans les zones de liste et de texte.](./media/azure-stack-network-howto-vnet-to-onprem/image7a.png)

5.  Sélectionnez **OK**.

6.  Indiquez les détails relatifs au réseau virtuel, aux sous-réseaux et à la taille de machine virtuelle à l’aide du tableau [Paramètres de déploiement](#deployment-parameters).

    > [!Warning] 
    > Si le réseau local chevauche la plage d’adresses IP `172.16.0.0/16`, vous devez sélectionner et configurer un autre ensemble de plage réseau et de sous-réseaux. Si vous souhaitez utiliser des noms et des plages différents de ceux indiqués dans le tableau [Paramètres de déploiement](#deployment-parameters), utilisez des paramètres qui **n’entrent pas** en conflit avec le réseau local. Faites attention quand vous définissez la plage d’adresses IP de réseau virtuel et les plages de sous-réseaux au sein du réseau virtuel. La plage ne doit pas chevaucher les plages d’adresses IP qui existent dans votre réseau local.

7.  Sélectionnez **OK**.

8.  Configurez l’adresse IP publique pour l’appliance virtuelle réseau FortiGate :

    ![La boîte de dialogue Attribution d'adresse IP affiche la valeur forti1-publicip1 pour le « Nom de l'adresse IP publique » et Statique pour le « Type d'adresse IP publique ».](./media/azure-stack-network-howto-vnet-to-onprem/image8a.png)

9.  Sélectionnez **OK**. Et enfin sélectionner **OK**.

10.  Sélectionnez **Create** (Créer).

Le déploiement prendra environ 10 minutes.

## <a name="configure-routes-udrs-for-each-vnet"></a>Configurer des routes (UDR) pour chaque réseau virtuel

Effectuez ces étapes pour les deux déploiements (forti1-rg1 et forti2-rg1).

1. Connectez-vous au portail utilisateur Azure Stack Hub.

1. Sélectionnez Groupes de ressources. Tapez `forti1-rg1` dans le filtre, puis double-cliquez sur le groupe de ressources forti1-rg1.

    ![Dix ressources sont répertoriées pour le groupe de ressources forti1-rg1.](./media/azure-stack-network-howto-vnet-to-onprem/image9a.png)

1. Sélectionnez la ressource **forti1-forti1-InsideSubnet-routes-xxxx**.

1. Sélectionnez **Routes** sous **Paramètres**.

    ![Le bouton Routes est sélectionné dans la boîte de dialogue Paramètres.](./media/azure-stack-network-howto-vnet-to-onprem/image10a.png)

1. Supprimez la route **to-Internet**.

    ![La route to-Internet, seule route répertoriée, est sélectionnée. Un bouton Supprimer est disponible.](./media/azure-stack-network-howto-vnet-to-onprem/image11a.png)

1. Sélectionnez *Oui*.

1. Sélectionnez **Ajouter** pour ajouter une nouvelle route.

1. Nommez la route `to-onprem`.

1. Entrez la plage réseau IP qui définit la plage réseau du réseau local auquel le VPN se connectera.

1. Sélectionnez **Appliance virtuelle** pour **Type de tronçon suivant** et `172.16.1.4`. Utilisez votre plage d’adresses IP si vous utilisez une plage d’adresses IP différente.

    ![La boîte de dialogue Ajouter une route affiche les quatre valeurs qui ont été sélectionnées et entrées dans les zones de texte.](./media/azure-stack-network-howto-vnet-to-onprem/image12a.png)

1. Sélectionnez **Enregistrer**.

Vous aurez besoin d’un fichier de licence valide de Fortinet pour activer chaque appliance virtuelle réseau FortiGate. Les appliances virtuelles réseau **ne fonctionnent pas** tant que vous n’avez pas activé chacune d’elles. Pour plus d’informations sur la façon d’obtenir un fichier de licence et d’activer l’appliance virtuelle réseau, consultez l’article de la bibliothèque de documents Fortinet intitulé [Registering and downloading your license](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

Vous devrez acquérir deux fichiers de licence, soit un par appliance virtuelle réseau.

## <a name="create-an-ipsec-vpn-between-the-two-nvas"></a>Créer un VPN IPSec entre les deux appliances virtuelles réseau

Une fois les appliances virtuelles réseau activées, procédez comme suit pour créer un VPN IPSec entre celles-ci.

Suivez les étapes ci-dessous pour les appliances virtuelles réseau forti1 et forti2 :

1. Pour obtenir l’adresse IP publique attribuée, accédez à la page de vue d’ensemble de la machine virtuelle fortiX :

    ![La page de présentation de la machine virtuelle forti1 affiche les valeurs de forti1, comme le « Groupe de ressources » et l'État.](./media/azure-stack-network-howto-vnet-to-vnet/image13a.png)

1. Copiez l’adresse IP attribuée, ouvrez un navigateur, puis collez-la dans la barre d’adresses. Il est possible que votre navigateur vous avertisse que le certificat de sécurité n’est pas approuvé. Continuez quand même.

1. Entrez le nom d’utilisateur d’administration et le mot de passe FortiGate que vous avez fournis pendant le déploiement.

    ![La boîte de dialogue Connexion contient les zones de texte Utilisateur et Mot de passe, ainsi qu'un bouton Connexion.](./media/azure-stack-network-howto-vnet-to-vnet/image14a.png)

1. Sélectionnez **System** (Système)  > **Firmware** (Microprogramme).

1. Cochez la case qui indique le microprogramme le plus récent, par exemple, `FortiOS v6.2.0 build0866`.

    ![La boîte de dialogue Microprogramme contient l'identificateur du microprogramme « FortiOS v6.2.0 build0866 », un lien vers les notes de publication et deux boutons : « Sauvegarder la configuration et mettre à niveau » et « Mettre à niveau ».](./media/azure-stack-network-howto-vnet-to-vnet/image15a.png)

1. Sélectionnez **Backup config and upgrade** (Configuration et mise à niveau de la sauvegarde)  > **Continue**.

1. L’appliance virtuelle réseau met à jour son microprogramme avec la dernière build et redémarre. Le processus prend environ cinq minutes. Reconnectez-vous à la console web FortiGate.

1. Cliquez sur **VPN** > **IPSec Wizard** (Assistant IPSec).

1. Entrez un nom pour le VPN, par exemple, `conn1` dans **VPN Creation Wizard** (Assistant de création de VPN).

1. Sélectionnez **This site is behind NAT** (Ce site est derrière NAT).

    ![La capture d'écran de l'Assistant Création d'un VPN montre qu'il se trouve à la première étape, Configuration du VPN. Les valeurs suivantes sont sélectionnées : « Site à site » pour le Type de modèle, « FortiGate » pour le Type de périphérique distant et « Ce site se trouve derrière NAT » pour la Configuration NAT.](./media/azure-stack-network-howto-vnet-to-vnet/image16a.png)

1. Sélectionnez **Suivant**.

1. Entrez l’adresse IP distante du périphérique VPN local auquel vous allez vous connecter.

1. Sélectionnez **port1** pour **Outgoing Interface** (Interface sortante).

1. Sélectionnez **Pre-shared Key** (Clé prépartagée), puis entrez (et enregistrez) une clé prépartagée. 

    > [!NOTE]  
    > Vous aurez besoin de cette clé pour configurer la connexion sur le périphérique VPN local ; autrement dit, ils doivent correspondre *exactement*.

    ![La capture d'écran de l'Assistant Création d'un VPN montre qu'il se trouve à la deuxième étape, Authentification, et les valeurs sélectionnées sont en surbrillance.](./media/azure-stack-network-howto-vnet-to-vnet/image17a.png)

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

    ![La capture d'écran de l'Assistant Création d'un VPN montre qu'il se trouve à la troisième étape, Stratégie et routage. Elle affiche les valeurs sélectionnées et entrées.](./media/azure-stack-network-howto-vnet-to-vnet/image18a.png)

1. Sélectionnez **Créer**

1. Sélectionnez **Network** (Réseau)  > **Interfaces**.

    ![La liste des interfaces montre deux interfaces : le port1, qui a été configuré, et le port2, qui ne l'a pas été. Des boutons permettent de créer, de modifier et de supprimer des interfaces.](./media/azure-stack-network-howto-vnet-to-vnet/image19a.png)

1. Double-cliquez sur **port2**.

1. Choisissez **LAN** dans la liste **Role** et **DHCP** pour le mode d’adressage.

1. Sélectionnez **OK**.

Répétez ces étapes pour l’autre appliance virtuelle réseau.

## <a name="bring-up-all-phase-2-selectors"></a>Afficher tous les sélecteurs de phase 2 

Une fois l’opération ci-dessus effectuée pour les *deux* appliances virtuelles réseau :

1.  Sur la console web FortiGate forti2, sélectionnez **Monitor** (Supervision)  > **IPsec Monitor** (Supervision IPsec). 

    ![La supervision de la connexion VPN conn1 est répertoriée. Il est indiqué qu'elle est désactivée, tout comme le sélecteur de phase 2 correspondant.](./media/azure-stack-network-howto-vnet-to-vnet/image20a.png)

2.  Mettez en surbrillance `conn1` et sélectionnez **Bring Up** (Afficher)  > **All Phase 2 Selectors** (Tous les sélecteurs de phase 2).

    ![La supervision et le sélecteur de phase 2 sont tous deux présentés comme activés.](./media/azure-stack-network-howto-vnet-to-vnet/image21a.png)

## <a name="test-and-validate-connectivity"></a>Tester et valider la connectivité

Vous devez maintenant être en mesure d’effectuer un routage entre chaque réseau virtuel via les appliances virtuelles réseau FortiGate. Pour valider la connexion, créez une machine virtuelle Azure Stack Hub dans le sous-réseau interne de chaque réseau virtuel. Vous pouvez créer une machine virtuelle Azure Stack Hub via le portail, l’interface CLI ou PowerShell. Durant la création des machines virtuelles :

-   Les machines virtuelles Azure Stack Hub sont placées sur le **sous-réseau interne** de chaque réseau virtuel.

-   Vous **n’appliquez pas** de groupe de sécurité réseau à la machine virtuelle lors de la création (autrement dit, supprimez le NSG qui est ajouté par défaut si vous créez la machine virtuelle à partir du portail).

-   Assurez-vous que les règles de pare-feu de machines virtuelles autorisent la communication que vous allez utiliser pour tester la connectivité. À des fins de test, il est recommandé de désactiver complètement le pare-feu dans le système d’exploitation, si cela est possible.

## <a name="next-steps"></a>Étapes suivantes

[Différences et considérations relatives aux réseaux Azure Stack Hub](azure-stack-network-differences.md)  
[Proposer une solution réseau dans Azure Stack Hub avec Fortinet FortiGate](../operator/azure-stack-network-solutions-enable.md)  
