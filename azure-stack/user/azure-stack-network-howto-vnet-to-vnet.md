---
title: Guide pratique pour connecter deux réseaux Azure Stack à l’aide de l’appairage VNET | Microsoft Docs
description: Découvrez comment connecter deux réseaux Azure Stack à l’aide de l’appairage VNET.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/03/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: 7a9f293c35856a9d1e29652a097d789f86b7b03c
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71962471"
---
# <a name="how-to-connect-two-vnets-through-peering"></a>Guide pratique pour connecter deux réseaux virtuels via l’appairage

Cet article explique comment créer une connexion entre deux réseaux virtuels dans le même environnement. Pendant la configuration des connexions, vous allez découvrir le fonctionnement des passerelles VPN dans Azure Stack. Connectez deux réseaux virtuels dans le même environnement Azure Stack à l’aide de Fortinet FortiGate. Cette procédure déploie deux réseaux virtuels avec une appliance virtuelle réseau FortiGate dans chaque réseau virtuel dans un groupe de ressources distinct. Il détaille également les modifications requises pour configurer un VPN IPSec entre les deux réseaux virtuels. Répétez les étapes de cet article pour chaque déploiement de réseau virtuel.

## <a name="prerequisites"></a>Prérequis

-   Accès à un système intégré Azure Stack ou kit ASDK permettant de mettre en œuvre les exigences de calcul, de réseau et de ressources nécessaires pour cette solution.

-  Une solution d’appliance virtuelle réseau téléchargée et publiée sur la Place de marché Azure Stack. Une appliance virtuelle réseau contrôle le flux du trafic réseau depuis un réseau de périmètre vers d’autres réseaux ou sous-réseaux. Cette procédure utilise la [solution de machine virtuelle unique Pare-feu Fortigate de nouvelle génération](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

-  Au moins deux fichiers de licence FortiGate disponibles pour activer l’appliance virtuelle réseau FortiGate. Pour plus d’informations sur la façon d’obtenir ces licences, consultez l’article de la bibliothèque de documents Fortinet intitulé [Registering and downloading your license](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

    Cette procédure utilise le [déploiement Single FortiGate-VM](ttps://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment). Vous trouverez des instructions sur la façon de connecter l’appliance virtuelle réseau FortiGate au réseau virtuel Azure Stack dans votre réseau local.

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

> [!Note]
> \* Choisissez un autre ensemble d’espaces d’adressage et de préfixes de sous-réseau si les paramètres ci-dessus chevauchent de quelque façon que ce soit l’environnement réseau local, y compris le pool d’adresses IP virtuelles d’un des environnements Azure Stack. Assurez-vous également que les plages d’adresses ne se chevauchent pas.

## <a name="deploy-the-fortigate-ngfw"></a>Déployer le Pare-feu Fortigate de nouvelle génération

1.  Ouvrez le portail utilisateur Azure Stack.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image5.png)

2.  Sélectionnez **Créer une ressource**, puis recherchez `FortiGate`.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image6.png)

3.  Sélectionnez le **Pare-feu Fortigate de nouvelle génération**, puis **Créer**.

4.  Indiquez les **Informations de base** à l’aide des paramètres du tableau [Paramètres de déploiement](#deployment-parameters).

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image7.png)

5.  Sélectionnez **OK**.

6.  Indiquez les détails relatifs au réseau virtuel, aux sous-réseaux et à la taille de machine virtuelle à l’aide du tableau [Paramètres de déploiement](#deployment-parameters).

    > [!Warning] 
    > Si le réseau local chevauche la plage d’adresses IP `172.16.0.0/16`, vous devez sélectionner et configurer un autre ensemble de plage réseau et de sous-réseaux. Si vous souhaitez utiliser des noms et des plages différents de ceux indiqués dans le tableau [Paramètres de déploiement](#deployment-parameters), utilisez des paramètres qui **n’entrent pas** en conflit avec le réseau local. Faites attention quand vous définissez la plage d’adresses IP de réseau virtuel et les plages de sous-réseaux au sein du réseau virtuel. La plage ne doit pas chevaucher les plages d’adresses IP qui existent dans votre réseau local.

7.  Sélectionnez **OK**.

8.  Configurez l’adresse IP publique pour l’appliance virtuelle réseau FortiGate :

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image8.png)

9.  Sélectionnez **OK**. Et enfin sélectionner **OK**.

10.  Sélectionnez **Create** (Créer).

Le déploiement prendra environ 10 minutes.

## <a name="configure-routes-udrs-for-each-vnet"></a>Configurer des routes (UDR) pour chaque réseau virtuel

Effectuez ces étapes pour les deux déploiements (forti1-rg1 et forti2-rg1).

1. Ouvrez le portail utilisateur Azure Stack.

2. Sélectionnez Groupes de ressources. Tapez `forti1-rg1` dans le filtre, puis double-cliquez sur le groupe de ressources forti1-rg1.

    ![resource group](./media/azure-stack-network-howto-vnet-to-onprem/image9.png)

2. Sélectionnez la ressource **forti1-forti1-InsideSubnet-routes-xxxx**.

3. Sélectionnez **Routes** sous **Paramètres**.

    ![Itinéraires](./media/azure-stack-network-howto-vnet-to-onprem/image10.png)

4. Supprimez la route **to-Internet**.

    ![to-Internet](./media/azure-stack-network-howto-vnet-to-onprem/image11.png)

5. Sélectionnez *Oui*.

6. Sélectionnez **Ajouter** pour ajouter une nouvelle route.

7. Nommez la route `to-onprem`.

8. Entrez la plage réseau IP qui définit la plage réseau du réseau local auquel le VPN se connectera.

9. Sélectionnez **Appliance virtuelle** pour **Type de tronçon suivant** et `172.16.1.4`. Utilisez votre plage d’adresses IP si vous utilisez une plage d’adresses IP différente.

    ![Type de tronçon suivant](./media/azure-stack-network-howto-vnet-to-onprem/image12.png)

10. Sélectionnez **Enregistrer**.

Vous aurez besoin d’un fichier de licence valide de Fortinet pour activer chaque appliance virtuelle réseau FortiGate. Les appliances virtuelles réseau **ne fonctionnent pas** tant que vous n’avez pas activé chacune d’elles. Pour plus d’informations sur la façon d’obtenir un fichier de licence et d’activer l’appliance virtuelle réseau, consultez l’article de la bibliothèque de documents Fortinet intitulé [Registering and downloading your license](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

Vous devrez acquérir deux fichiers de licence, soit un par appliance virtuelle réseau.

## <a name="create-an-ipsec-vpn-between-the-two-nvas"></a>Créer un VPN IPSec entre les deux appliances virtuelles réseau

Une fois les appliances virtuelles réseau activées, procédez comme suit pour créer un VPN IPSec entre celles-ci.

Suivez les étapes ci-dessous pour les appliances virtuelles réseau forti1 et forti2 :

1.  Pour obtenir l’adresse IP publique attribuée, accédez à la page de vue d’ensemble de la machine virtuelle fortiX :

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image13.png)

2.  Copiez l’adresse IP attribuée, ouvrez un navigateur, puis collez-la dans la barre d’adresses. Il est possible que votre navigateur vous avertisse que le certificat de sécurité n’est pas approuvé. Continuez quand même.

4.  Entrez le nom d’utilisateur d’administration et le mot de passe FortiGate que vous avez fournis pendant le déploiement.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image14.png)

5.  Sélectionnez **System** (Système)  > **Firmware** (Microprogramme).

6.  Cochez la case qui indique le microprogramme le plus récent, par exemple, `FortiOS v6.2.0 build0866`.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image15.png)

7.  Sélectionnez **Backup config and upgrade** (Configuration et mise à niveau de la sauvegarde)  > **Continue**.

8.  L’appliance virtuelle réseau met à jour son microprogramme avec la dernière build et redémarre. Le processus prend environ cinq minutes. Reconnectez-vous à la console web FortiGate.

10.  Cliquez sur **VPN** > **IPSec Wizard** (Assistant IPSec).

11. Entrez un nom pour le VPN, par exemple, `conn1` dans **VPN Creation Wizard** (Assistant de création de VPN).

12. Sélectionnez **This site is behind NAT** (Ce site est derrière NAT).

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image16.png)

13. Sélectionnez **Suivant**.

14. Entrez l’adresse IP distante du périphérique VPN local auquel vous allez vous connecter.

15. Sélectionnez **port1** pour **Outgoing Interface** (Interface sortante).

16. Sélectionnez **Pre-shared Key** (Clé prépartagée), puis entrez (et enregistrez) une clé prépartagée. 

    > [!Note]  
    > Vous aurez besoin de cette clé pour configurer la connexion sur le périphérique VPN local ; autrement dit, ils doivent correspondre *exactement*.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image17.png)

17. Sélectionnez **Suivant**.

18. Sélectionnez **port2** pour **Local Interface** (Interface locale).

19. Entrez la plage de sous-réseau local :
    - forti1 : 172.16.0.0/16
    - forti2 : 172.17.0.0/16

    Utilisez votre plage d’adresses IP si vous utilisez une plage d’adresses IP différente.

20. Entrez le ou les sous-réseaux distants appropriés qui représentent le réseau local, auquel vous vous connecterez via le périphérique VPN local.
    - forti1 : 172.16.0.0/16
    - forti2 : 172.17.0.0/16

    Utilisez votre plage d’adresses IP si vous utilisez une plage d’adresses IP différente.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image18.png)

21. Sélectionnez **Créer**

22. Sélectionnez **Network** (Réseau)  > **Interfaces**.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image19.png)

23. Double-cliquez sur **port2**.

24. Choisissez **LAN** dans la liste **Role** et **DHCP** pour le mode d’adressage.

25. Sélectionnez **OK**.

Répétez ces étapes pour l’autre appliance virtuelle réseau.

## <a name="bring-up-all-phase-2-selectors"></a>Afficher tous les sélecteurs de phase 2 

Une fois l’opération ci-dessus effectuée pour les *deux* appliances virtuelles réseau :

1.  Sur la console web FortiGate forti2, sélectionnez **Monitor** (Supervision)  > **IPsec Monitor** (Supervision IPsec). 

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image20.png)

2.  Mettez en surbrillance `conn1` et sélectionnez **Bring Up** (Afficher)  > **All Phase 2 Selectors** (Tous les sélecteurs de phase 2).

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image21.png)

## <a name="test-and-validate-connectivity"></a>Tester et valider la connectivité

Vous devez maintenant être en mesure d’effectuer un routage entre chaque réseau virtuel via les appliances virtuelles réseau FortiGate. Pour valider la connexion, créez une machine virtuelle Azure Stack dans le sous-réseau interne de chaque réseau virtuel. La création d’une machine virtuelle Azure Stack peut être effectuée par le biais du portail, de l’interface CLI ou de PowerShell. Durant la création des machines virtuelles :

-   Les machines virtuelles Azure Stack sont placées sur le **sous-réseau interne** de chaque réseau virtuel.

-   Vous **n’appliquez pas** de groupe de sécurité réseau à la machine virtuelle lors de la création (autrement dit, supprimez le NSG qui est ajouté par défaut si vous créez la machine virtuelle à partir du portail).

-   Assurez-vous que les règles de pare-feu de machines virtuelles autorisent la communication que vous allez utiliser pour tester la connectivité. À des fins de test, il est recommandé de désactiver complètement le pare-feu dans le système d’exploitation, si cela est possible.

## <a name="next-steps"></a>Étapes suivantes

[Différences et considérations relatives aux réseaux Azure Stack](azure-stack-network-differences.md)  
[Proposer une solution réseau dans Azure Stack avec Fortinet FortiGate](../operator/azure-stack-network-solutions-enable.md)  