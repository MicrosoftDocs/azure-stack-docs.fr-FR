---
title: Configurer une passerelle VPN pour Azure Stack Hub
description: Découvrez comment configurer une passerelle VPN pour Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: 17ba031e6aa1c9e7c0699309d98183ed38cbd4e7
ms.sourcegitcommit: 6306e0c2506106ad01ff50010f36466f3325d0a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84631368"
---
# <a name="set-up-vpn-gateway-for-azure-stack-hub-using-fortigate-nva"></a>Configurer une passerelle VPN pour Azure Stack Hub avec l’appliance virtuelle réseau FortiGate

Cet article explique comment créer une connexion VPN avec votre environnement Azure Stack Hub. Une passerelle VPN est un type de passerelle de réseau virtuel qui achemine le trafic chiffré entre votre réseau virtuel dans Azure Stack Hub et une passerelle VPN distante. La procédure suivante déploie un réseau virtuel avec une appliance virtuelle réseau FortiGate au sein d’un groupe de ressources. Elle indique également les étapes permettant de configurer un VPN IPSec sur l’appliance virtuelle réseau FortiGate.

## <a name="prerequisites"></a>Prérequis

-  Accès à un système intégré Azure Stack Hub permettant de mettre en œuvre les exigences de calcul, de réseau et de ressources nécessaires pour cette solution. 

    > [!Note]  
    > Ces instructions **ne fonctionnent pas** avec un Kit de développement Azure Stack (ASDK) en raison des limitations réseau dans le kit ASDK. Pour plus d’informations, consultez [Exigences et éléments à prendre en compte pour ASDK](https://docs.microsoft.com/azure-stack/asdk/asdk-deploy-considerations).

-  Accès à un périphérique VPN dans le réseau local qui héberge le système intégré Azure Stack Hub. Le périphérique doit créer un tunnel IPSec, qui répond aux paramètres décrits dans les [paramètres de déploiement](#deployment-parameters).

-  Une solution d’appliance virtuelle réseau disponible dans votre Place de marché Azure Stack Hub. Une appliance virtuelle réseau contrôle le flux du trafic réseau depuis un réseau de périmètre vers d’autres réseaux ou sous-réseaux. Cette procédure utilise la [solution de machine virtuelle unique Pare-feu Fortigate de nouvelle génération](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

    > [!Note]  
    > Si vous ne disposez pas des licences **Fortinet FortiGate-VM For Azure BYOL** et **FortiGate NGFW - Single VM Deployment (BYOL)** dans votre Place de marché Azure Stack Hub, contactez votre opérateur cloud.

-  Pour activer l’appliance virtuelle réseau FortiGate, vous devez disposer d’au moins un fichier de licence FortiGate. Pour plus d’informations sur la façon d’acquérir ces licences, consultez l’article de la bibliothèque de documents Fortinet intitulé [Registering and downloading your license](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

    Cette procédure utilise le [déploiement Single FortiGate-VM](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment). Vous trouverez des instructions sur la façon de connecter l’appliance virtuelle réseau FortiGate au réseau virtuel Azure Stack Hub dans votre réseau local.

    Pour plus d’informations sur le déploiement de la solution FortiGate dans une configuration active-passive (HA), consultez l’article de la bibliothèque de documents Fortinet intitulé [HA for FortiGate-VM on Azure](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure).

## <a name="deployment-parameters"></a>Paramètres de déploiement

Le tableau suivant récapitule les paramètres utilisés dans ces déploiements pour référence.

| Paramètre | Valeur |
|-----------------------------------|---------------------------|
| Nom de l’instance FortiGate | forti1 |
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

> [!Note]
> \* Choisissez un autre ensemble d’espaces d’adressage et de préfixes de sous-réseau si `172.16.0.0/16` chevauche le réseau local ou le pool d’adresses IP virtuelles Azure Stack Hub.

## <a name="deploy-the-fortigate-ngfw-marketplace-items"></a>Déployer les éléments de la Place de marché concernant le Pare-feu Fortigate de nouvelle génération

1. Connectez-vous au portail utilisateur Azure Stack Hub.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image5.png)

1. Sélectionnez **Créer une ressource**, puis recherchez `FortiGate`.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image6.png)

2. Sélectionnez le **Pare-feu Fortigate de nouvelle génération**, puis **Créer**.

3. Indiquez les **Informations de base** à l’aide des paramètres du tableau [Paramètres de déploiement](#deployment-parameters).

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image7.png)

1. Sélectionnez **OK**.

2. Indiquez les détails relatifs au réseau virtuel, aux sous-réseaux et à la taille de machine virtuelle à l’aide du tableau [Paramètres de déploiement](#deployment-parameters).

    > [!Warning] 
    > Si le réseau local chevauche la plage d’adresses IP `172.16.0.0/16`, vous devez sélectionner et configurer un autre ensemble de plage réseau et de sous-réseaux. Si vous souhaitez utiliser des noms et des plages différents de ceux indiqués dans le tableau [Paramètres de déploiement](#deployment-parameters), utilisez des paramètres qui **n’entrent pas** en conflit avec le réseau local. Faites attention quand vous définissez la plage d’adresses IP de réseau virtuel et les plages de sous-réseaux au sein du réseau virtuel. La plage ne doit pas chevaucher les plages d’adresses IP qui existent dans votre réseau local.

3. Sélectionnez **OK**.

4. Configurez l’adresse IP publique pour l’appliance virtuelle réseau FortiGate :

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image8.png)

5. Sélectionnez **OK**. Et enfin sélectionner **OK**.

6. Sélectionnez **Create** (Créer).

    Le déploiement prendra environ 10 minutes.

## <a name="configure-routes-udr-for-the-vnet"></a>Configurer des routes (UDR) pour le réseau virtuel

1. Connectez-vous au portail utilisateur Azure Stack Hub.

2. Sélectionnez Groupes de ressources. Tapez `forti1-rg1` dans le filtre, puis double-cliquez sur le groupe de ressources forti1-rg1.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image9.png)

2. Sélectionnez la ressource « forti1-forti1-InsideSubnet-routes-xxxx ».

3. Sélectionnez **Routes** sous **Paramètres**.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image10.png)

4. Supprimez la route **to-Internet**.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image11.png)

5. Sélectionnez *Oui*.

6. Sélectionnez **Ajouter** pour ajouter une nouvelle route.

7. Nommez la route `to-onprem`.

8. Entrez la plage réseau IP qui définit la plage réseau du réseau local auquel le VPN se connectera.

9. Sélectionnez **Appliance virtuelle** pour **Type de tronçon suivant** et `172.16.1.4`. Utilisez votre plage d’adresses IP si vous utilisez une plage d’adresses IP différente.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image12.png)

10. Sélectionnez **Enregistrer**.

## <a name="activate-the-fortigate-nva"></a>Activer l’appliance virtuelle réseau FortiGate

Activez l’appliance virtuelle réseau FortiGate et configurez une connexion VPN IPSec sur chaque appliance virtuelle réseau.

Pour activer chaque appliance virtuelle réseau FortiGate, vous aurez besoin d’un fichier de licence valide de Fortinet. Les appliances virtuelles réseau **ne fonctionnent pas** tant que vous n’avez pas activé chacune d’elles. Pour plus d’informations sur la façon d’obtenir un fichier de licence et d’activer l’appliance virtuelle réseau, consultez l’article de la bibliothèque de documents Fortinet intitulé [Registering and downloading your license](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

Une fois que vous avez activé les appliances virtuelles réseau, créez un tunnel VPN IPSec sur celles-ci.

1. Connectez-vous au portail utilisateur Azure Stack Hub.

2. Sélectionnez Groupes de ressources. Entrez `forti1` dans le filtre, puis double-cliquez sur le groupe de ressources forti1.

3. Double-cliquez sur la machine virtuelle **forti1** dans la liste des types de ressources dans le panneau du groupe de ressources.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image13.png)

4. Copiez l’adresse IP attribuée, ouvrez un navigateur, puis collez-la dans la barre d’adresses. Le site peut déclencher un avertissement indiquant que le certificat de sécurité n’est pas approuvé. Continuez quand même.

5. Entrez le nom d’utilisateur d’administration et le mot de passe FortiGate que vous avez fournis pendant le déploiement.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image14.png)

6. Sélectionnez **System** (Système)  > **Firmware** (Microprogramme).

7. Cochez la case qui indique le microprogramme le plus récent, par exemple, `FortiOS v6.2.0 build0866`.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image15.png)

8. Sélectionnez **Backup config and upgrade** (Configuration et mise à niveau de la sauvegarde)  > **Continue**.

9. L’appliance virtuelle réseau met à jour son microprogramme avec la dernière build et redémarre. Le processus prend environ cinq minutes. Reconnectez-vous à la console web FortiGate.

10. Cliquez sur **VPN** > **IPSec Wizard** (Assistant IPSec).

11. Entrez un nom pour le VPN, par exemple, `conn1` dans **VPN Creation Wizard** (Assistant de création de VPN).

12. Sélectionnez **This site is behind NAT** (Ce site est derrière NAT).

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image16.png)

13. Sélectionnez **Suivant**.

14. Entrez l’adresse IP distante du périphérique VPN local auquel vous allez vous connecter.

15. Sélectionnez **port1** pour **Outgoing Interface** (Interface sortante).

16. Sélectionnez **Pre-shared Key** (Clé prépartagée), puis entrez (et enregistrez) une clé prépartagée. 

    > [!Note]  
    > Vous aurez besoin de cette clé pour configurer la connexion sur le périphérique VPN local ; autrement dit, ils doivent correspondre *exactement*.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image17.png)

17. Sélectionnez **Suivant**.

18. Sélectionnez **port2** pour **Local Interface** (Interface locale).

19. Entrez la plage de sous-réseau local :
    - forti1 : 172.16.0.0/16
    - forti2 : 172.17.0.0/16

    Utilisez votre plage d’adresses IP si vous utilisez une plage d’adresses IP différente.

20. Entrez le ou les sous-réseaux distants appropriés qui représentent le réseau local, auquel vous vous connecterez via le périphérique VPN local.

    [](./media/azure-stack-network-howto-vnet-to-onprem/image18.png)

21. Sélectionnez **Créer**

22. Sélectionnez **Network** (Réseau)  > **Interfaces**.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image19.png)

23. Double-cliquez sur **port2**.

24. Choisissez **LAN** dans la liste **Role** et **DHCP** pour le mode d’adressage.

25. Sélectionnez **OK**.

## <a name="configure-the-on-premises-vpn"></a>Configurer le VPN local

Vous devez configurer le périphérique VPN local pour créer le tunnel VPN IPSec. Le tableau suivant liste les paramètres dont vous aurez besoin pour configurer le périphérique VPN local. Pour plus d’informations sur la configuration du périphérique VPN local, reportez-vous à la documentation de votre périphérique.

| Paramètre | Valeur |
| --- | --- |
| Adresse IP de la passerelle distante | Adresse IP publique attribuée à forti1 (consultez [Activer l’appliance virtuelle réseau FortiGate](#activate-the-fortigate-nva)). |
| Réseau IP distant | 172.16.0.0/16 (si vous utilisez la plage d’adresses IP dans ces instructions pour le réseau virtuel). |
| Méthode d’authentification : clé prépartagée (PSK) | Issue de l’étape 16.
| Version IKE | 1 |
| Mode IKE | Principal (protection des ID) |
| Algorithmes pour la proposition Phase 1 | AES128-SHA256, AES256-SHA256, AES128-SHA1, AES256-SHA1 |
| Groupes Diffie-Hellman | 14, 5 |

## <a name="create-the-vpn-tunnel"></a>Créer le tunnel VPN

Une fois le périphérique VPN local correctement configuré, le tunnel VPN peut être établi.

À partir de l’appliance virtuelle réseau FortiGate :

1. Sur la console web FortiGate forti1, accédez à **Monitor** (Supervision)  > **IPsec Monitor** (Supervision IPsec).

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image20.png)

2. Mettez en surbrillance **conn1** et sélectionnez **Bring Up** (Afficher)  > **All Phase 2 Selectors** (Tous les sélecteurs de phase 2).

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image21.png)

## <a name="test-and-validate-connectivity"></a>Tester et valider la connectivité

Vous pouvez effectuer un routage entre le réseau virtuel et le réseau local via le périphérique VPN local.

Pour valider la connexion :

1. Créez une machine virtuelle dans les réseaux virtuels Azure Stack Hub et un système sur le réseau local. Pour créer une machine virtuelle, vous pouvez suivre les instructions du [Guide de démarrage rapide : Créez une machine virtuelle Windows Server avec le portail Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal).

2. Quand vous créez la machine virtuelle Azure Stack Hub et que vous préparez le système local, vérifiez les points suivants :

-  La machine virtuelle Azure Stack Hub est placée sur le **sous-réseau interne** du réseau virtuel.

-  Le système local est placé sur le réseau local au sein de la plage d’adresses IP définie, tel que défini dans la configuration IPSec. Vérifiez également que l’adresse IP de l’interface locale du périphérique VPN local est fournie au système local en tant que route qui peut atteindre le réseau virtuel Azure Stack Hub, par exemple, `172.16.0.0/16`.

-  **N’appliquez pas** de groupe de sécurité réseau à la machine virtuelle Azure Stack Hub lors de la création. Vous devrez peut-être supprimer le NSG qui est ajouté par défaut si vous créez la machine virtuelle à partir du portail.

-  Assurez-vous que le système d’exploitation du système local et le système d’exploitation de la machine virtuelle Azure Stack Hub n’ont pas de règles de pare-feu qui interdisent la communication que vous allez utiliser pour tester la connectivité. À des fins de test, il est recommandé de désactiver complètement le pare-feu dans le système d’exploitation des deux systèmes.

## <a name="next-steps"></a>Étapes suivantes

[Différences et considérations relatives aux réseaux Azure Stack Hub](azure-stack-network-differences.md)  
[Proposer une solution réseau dans Azure Stack Hub avec Fortinet FortiGate](../operator/azure-stack-network-solutions-enable.md)  
