---
title: Comment déployer F5 sur deux instances d’Azure Stack Hub
description: Découvrez comment déployer F5 sur deux instances d’Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: 8deb2905f0d151fb1a2ce196efd2ba8eb5748c85
ms.sourcegitcommit: 9557a5029cf329599f5b523c68e8305b876108d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88965209"
---
# <a name="how-to-deploy-f5-across-two-azure-stack-hub-instances"></a>Comment déployer F5 sur deux instances d’Azure Stack Hub

Cet article vous explique comment configurer un équilibreur de charge externe sur deux environnements Azure Stack Hub. Vous pouvez utiliser cette configuration pour gérer différentes charges de travail. Dans le cadre de cet article, vous allez déployer F5 comme solution d’équilibrage de charge globale sur deux instances d’Azure Stack Hub indépendantes. Vous allez également déployer une application web à charge équilibrée s’exécutant sur un serveur NGINX sur les deux instances. Les applications s’exécuteront derrière une paire de basculement haute disponibilité constituée de deux appliances virtuelles F5.

Vous trouverez les modèles Azure Resource Manager dans le dépôt GitHub [f5-azurestack-gslb](https://github.com/Mikej81/f5-azurestack-gslb).

## <a name="overview-of-load-balancing-with-f5"></a>Vue d’ensemble de l’équilibrage de charge avec F5

L'équilibreur de charge, composant matériel de F5, peut se trouver en dehors d'Azure Stack Hub et dans le centre de données qui héberge Azure Stack Hub. Azure Stack Hub ne dispose d'aucune fonctionnalité native permettant d'équilibrer les charges de travail entre deux déploiements distincts d'Azure Stack Hub. F5 BIG-IP Virtual Edition (VE) s’exécute sur les deux plateformes. Cette configuration prend en charge la parité entre les architectures Azure et Azure Stack Hub en s'appuyant sur la réplication des services d'application de prise en charge. Vous pouvez développer une application dans un environnement et la déplacer vers un autre. Vous pouvez également mettre en miroir l'intégralité de l'instance Azure Stack Hub prête pour la production, y compris les mêmes stratégies, services d'application et configurations BIG-IP. Cette approche vous évite de passer de nombreuses heures à refactoriser et tester une application et vous permet de passer à l’écriture du code.

La sécurisation des applications et de leurs données est souvent une préoccupation pour les développeurs qui déplacent des applications vers le cloud public. Pourtant, cette préoccupation n’a pas lieu d’être. Vous pouvez créer une application dans votre environnement Azure Stack Hub, tandis qu'un architecte de sécurité configure les paramètres nécessaires sur le pare-feu d'applications web de F5. La pile entière peut être répliquée dans Azure Stack Hub, en sachant que l'application sera protégée par ce pare-feu d'applications web leader sur le marché. Le recours à des stratégies et ensembles de règles identiques élimine le risque de vulnérabilités auquel pourrait vous exposer l’utilisation d’autres pare-feu d’applications web.

La Place de Marché Azure Stack Hub est distincte de celle d'Azure. Seuls certains éléments sont ajoutés. Dans le cas présent, vous souhaitez créer un groupe de ressources sur chacune des instances d'Azure Stack Hub et déployer l'appliance virtuelle F5 déjà disponible. Vous verrez qu'une adresse **IP publique**  sera nécessaire pour autoriser la connectivité réseau entre les deux instances d'Azure Stack Hub. Il s’agit essentiellement de deux îlots, et l’adresse **IP publique** leur permet de communiquer entre les deux sites.

## <a name="prerequisites-for-big-ip-ve"></a>Prérequis pour BIG-IP VE

-  Téléchargez **F5 BIG-IP VE - ALL (BYOL, 2 Boot Locations)** sur chaque Place de Marché Azure Stack Hub. Si elles ne sont pas disponibles dans votre portail, contactez votre opérateur cloud.

-  Vous trouverez le modèle Azure Resource Manager dans le dépôt GitHub suivant : https://github.com/Mikej81/f5-azurestack-gslb.

## <a name="deploy-f5-big-ip-ve-on-each-instance"></a>Déployer F5 BIG-IP VE sur chaque instance

Procédez au déploiement sur les instances A et B d'Azure Stack Hub.

1. Connectez-vous au portail utilisateur Azure Stack Hub.

2. Sélectionnez **+ Créer une ressource**.

3. Faites une recherche dans la Place de Marché en tapant `F5`.

4. Sélectionnez **F5 BIG-IP VE – ALL (BYOL, 2 Boot Locations)** .

    ![La boîte de dialogue « Tableau de bord > Nouveau > Place du marché > Tout > F5 BIG-IP VE – ALL (BYOL, 2 Boot Locations) » affiche F5 dans la zone de recherche. Le résultat de la recherche unique est « F5 BIG-IP VE – ALL (BYOL, 2 Boot Locations) ».](./media/network-howto-f5/image1.png)

5. En bas de la page suivante, sélectionnez **Créer**.

    ![La boîte de dialogue « F5 BIG-IP VE – ALL (BYOL, 2 Boot Locations) » fournit des informations sur BIG-IP VE et les modules que vous pouvez déployer selon votre licence. Elle contient également un bouton Créer.](./media/network-howto-f5/image2.png)

6. Créez un groupe de ressources nommé **F5-GSLB**.

7. Utilisez les valeurs suivantes comme exemple pour effectuer le déploiement :

    ![La page Entrées de la boîte de dialogue Microsoft.Template affiche 15 zones de texte, parmi lesquelles VIRTUALMACHINENAME et ADMINUSERNAME, contenant des valeurs pour un exemple de déploiement.](./media/network-howto-f5/image3.png)

8. Vérifiez que le déploiement s’effectue correctement.

    ![La page Vue d’ensemble de la boîte de dialogue Microsoft.Template indique « Votre déploiement a été effectué » et fournit les détails correspondants.](./media/network-howto-f5/image4.png)

    > [!NOTE]  
    > Chaque déploiement de BIG-IP doit prendre environ 20 minutes.

## <a name="configure-big-ip-appliances"></a>Configurer les appliances BIG-IP

Suivez les étapes ci-dessous pour les instances A et B d'Azure Stack Hub.

1. Connectez-vous au portail utilisateur Azure Stack Hub sur l'instance A pour voir les ressources créées à partir du déploiement du modèle BIG-IP.

    ![La page Vue d’ensemble de la boîte de dialogue F5-GSLB répertorie les ressources déployées, ainsi que les informations associées.](./media/network-howto-f5/image18.png)

2. Suivez les instructions de F5 concernant les [éléments de configuration de BIG-IP](https://clouddocs.f5.com/training/community/dns/html/class1/class1.html). 

3. Configurez la liste d'adresses IP étendues BIG-IP pour l'écoute sur les deux appliances déployées sur les instances A et B d'Azure Stack Hub. Pour obtenir des instructions, consultez [Configuration BIG-IP GTM](https://techdocs.f5.com/kb/en-us/products/big-ip_gtm/manuals/product/gtm-concepts-11-5-0/4.html).


4. Vérifiez le basculement des appliances BIG-IP. Sur un système de test, configurez vos serveurs DNS pour qu’ils utilisent les éléments suivants :
    - Instance A d'Azure Stack Hub = adresse IP publique `f5stack1-ext`
    - Instance B d'Azure Stack Hub = adresse IP publique `f5stack1-ext`

5. Accédez à `www.contoso.com`. Votre navigateur charge la page NGINX par défaut.

## <a name="create-a-dns-sync-group"></a>Créer un groupe de synchronisation DNS

1. Activez le compte racine pour établir l’approbation. Suivez les instructions de l’article [Changing system maintenance account passwords (11.x - 15.x)](https://support.f5.com/csp/article/K13121). Après avoir défini l’approbation (échange de certificat), désactivez le compte racine.

1. Connectez-vous à l’appliance BIG-IP et créez un groupe de synchronisation DNS. Pour obtenir des instructions, consultez la page [Creating BIG-IP DNS Sync Group](https://f5-dns-automation-demo-12-1-x.readthedocs.io/en/latest/lab2/sync-group.html).

    > [!NOTE]  
    > Vous trouverez l’adresse IP locale de l’appliance BIG-IP dans votre groupe de ressources **F5-GSLB**. L’interface réseau est « f5stack1-ext » et vous souhaitez vous connecter à l’adresse IP publique ou privée (en fonction de l’accès).

    ![La boîte de dialogue « DNS > GSLB : Centres de données : Liste des centres de données » répertorie les centres de données et l’état. Elle contient les boutons Activer, Désactiver et Supprimer à appliquer aux centres de données sélectionnés.](./media/network-howto-f5/image5.png)
          
    ![La boîte de dialogue « DNS > GSLB : Serveurs : Liste de serveurs » répertorie les serveurs et l’état. Elle contient les boutons Activer, Désactiver, Supprimer et Reconnecter à appliquer aux serveurs sélectionnés.](./media/network-howto-f5/image6.png)

1. Sélectionnez le nouveau groupe de ressources **F5-GSLB** et sélectionnez la machine virtuelle **f5stack1**. Sous **Settings**, sélectionnez **Networking**.

## <a name="post-install-configurations"></a>Configurations après installation

Au terme de l'installation, vous devez configurer les groupes de sécurité réseau Azure Stack Hub et verrouiller les adresses IP sources.

1. Désactivez le port 22 après l’établissement de l’approbation.

2. Quand votre système est en ligne, bloquez les groupes de sécurité réseau sources. Le groupe de sécurité réseau de gestion doit être verrouillé sur la source de gestion. Le groupe de sécurité réseau externe (4353/TCP) doit être verrouillé sur l’autre instance pour la synchronisation. Le port 443 doit également être verrouillé jusqu’à ce que des applications avec des serveurs virtuels soient déployées.

3. La règle GTM_DNS est définie pour autoriser le trafic entrant sur le port 53 (DNS), et le programme de résolution BIG-IP commencera à fonctionner une fois les écouteurs créés.

    ![La page fStack1-ext de la boîte de dialogue Interface réseau affiche des informations sur l’interface fstack1-ext, et sur son groupe de sécurité réseau, fstack1-ext-nsg. Elle contient des onglets pour afficher les règles de port d’entrée ou les règles de port de sortie.](./media/network-howto-f5/image7.png)

4. Déployez une charge de travail d'application web de base dans votre environnement Azure Stack Hub pour équilibrer les charges derrière BIG-IP. Vous trouverez un exemple d’utilisation du serveur NGINX dans [Deploying NGINX and NGINX Plus on Docker](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-docker/).

    > [!NOTE]  
    > Déployez une instance de NGINX sur les instances A et B d'Azure Stack Hub.

5. Une fois NGINX déployé dans un conteneur Docker sur une machine virtuelle Ubuntu au sein de chaque instance d'Azure Stack Hub, vérifiez que vous avez accès à la page web par défaut sur les serveurs.

    ![La page « Bienvenue dans nginx. » indique que le serveur web nginx a été correctement installé et qu’une configuration supplémentaire est requise. Elle contient deux liens menant aux informations de support.](./media/network-howto-f5/image8.png)

6. Connectez-vous à l’interface de gestion de l’appliance BIG-IP. Dans cet exemple, utilisez l’adresse IP publique **f5-stack1-ext**.

    ![L’écran de connexion de l’utilitaire de configuration BIG-IP requiert un nom d’utilisateur et un mot de passe.](./media/network-howto-f5/image9.png)

7. Publiez l’accès à NGINX par le biais de l’appliance BIG-IP.
    
    -  La tâche suivante consiste à configurer l’appliance BIG-IP avec un serveur virtuel et un pool pour autoriser l’accès Internet entrant à l’application WordPress. Vous devez d’abord identifier l’adresse IP privée de l’instance NGINX.

8. Connectez-vous au portail utilisateur Azure Stack Hub. 

9. Sélectionnez votre interface réseau NGINX.

    ![La page Vue d’ensemble de la boîte de dialogue « Tableau de bord > Groupes de ressources > NGINX > ubuntu2673 » affiche des informations sur l’interface réseau ubuntu2673.](./media/network-howto-f5/image10.png)

10. À partir de la console BIG-IP, accédez à **Local traffic > Pools > Pool List** et sélectionnez **+** . Configurez le pool à l’aide des valeurs présentées dans le tableau. Laissez tous les autres champs sur leurs valeurs par défaut.

    ![Le volet gauche permet de naviguer pour créer un nouveau pool. Le volet droit est intitulé « Local Traffic >> Pools : Pool List >> New Pool » et fournit des fonctionnalités pour spécifier les informations relatives au nouveau pool. Un bouton Terminé est présent.](./media/network-howto-f5/image11.png)
    
    | Clé | Valeur |
    | --- | --- |
    | Nom | NGINX_Pool |
    | Health Monitor (Moniteur d’intégrité) | HTTPS |
    | Node Name | NGINX |
    | Adresse | \<your NGINX private IP address> |
    | Service Port | 443 |

11. Sélectionnez **Finished**. Quand la configuration est correcte, l’état du pool est vert.

    ![Le volet droit est intitulé « Local Traffic >> Pools : Pool List », et le pool créé est la seule entrée de la liste.](./media/network-howto-f5/image12.png)

    Vous devez maintenant configurer le serveur virtuel. Pour cela, vous devez d’abord rechercher l’adresse IP privée de votre appliance F5 BIG-IP.

12. À partir de la console BIG-IP, accédez à **Network > Self IPs** (Adresses Self IP).

    ![Le volet gauche permet de naviguer pour afficher les adresses Self IP. Le volet droit est intitulé « Network >> Self IPs ». Deux adresses Self IP sont répertoriées, et la première, self_2nic, est mise en surbrillance.](./media/network-howto-f5/image13.png)

13. Créez un serveur virtuel en accédant à **Local Traffic** > **Virtual Servers** > **Virtual Server List** et en sélectionnant **+** . Configurez le pool à l’aide des valeurs présentées dans le tableau. Laissez tous les autres champs sur leurs valeurs par défaut.

    | Clé | Valeur |
    | --- | --- |
    |Nom | NGINX |
    |Destination Address | \<Self IP address of the BIG-IP> |
    |Service Port | 443 |
    |SSL Profile (Client) | clientssl |
    |Source Address Translation | Auto Map |
        
    ![Le volet gauche permet d’accéder au volet droit vers « Local Traffic >> Virtual Servers : Virtual Server List >> NGINX », où les informations requises sont entrées.](./media/network-howto-f5/image14.png)

    ![Cette page permet d’entrer des informations supplémentaires. Elle contient également les boutons Mettre à jour et Supprimer.](./media/network-howto-f5/image15.png)

14. Vous avez terminé la configuration BIG-IP pour l’application NGINX. Pour vérifier que tout fonctionne correctement, parcourez le site et consultez les statistiques de F5.

15. Dans un navigateur accédez à `https://<F5-public-VIP-IP>` et assurez-vous que votre page NGINX par défaut s’affiche.

    ![La page « Bienvenue dans nginx. » indique que le serveur web nginx a été correctement installé et qu’une configuration supplémentaire est requise. Elle contient deux liens menant aux informations de support.](./media/network-howto-f5/image16.png)

16. À présent, consultez les statistiques de votre serveur virtuel pour vérifier le flux de trafic en accédant à **Statistics > Module Statistics > Local Traffic**.

17. Sous **Statistics Type**, sélectionnez **Virtual Servers**.

    ![Le volet gauche a accédé au volet droit « Statistics >> Module Statistics : Local Traffic >> Virtual Servers », et la liste affiche, entre autres, le serveur virtuel NGINX. NGINX est mis en surbrillance.](./media/network-howto-f5/image17.png)


## <a name="for-more-information"></a>Informations supplémentaires

Les liens ci-après vous permettent d’accéder à des articles de référence sur l’utilisation de F5 :

- [Datacenter Availability Services Using BIG-IP DNS](https://clouddocs.f5.com/training/community/dns/html/class3/class3.html)
- [Deploying the BIG-IP System with HTTP Applications](https://www.f5.com/content/dam/f5/corp/global/pdf/deployment-guides/iapp-http-dg.pdf)
- [Creating a wide IP for GSLB](https://clouddocs.f5.com/training/community/big-iq-cloud-edition/html/class10/module2/lab1.html)

## <a name="next-steps"></a>Étapes suivantes

[Différences et considérations relatives aux réseaux Azure Stack Hub](azure-stack-network-differences.md) 
