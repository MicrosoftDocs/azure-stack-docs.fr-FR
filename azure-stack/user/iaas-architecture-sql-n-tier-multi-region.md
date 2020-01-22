---
title: Exécuter une application multiniveau dans plusieurs régions Azure Stack Hub à des fins de haute disponibilité | Microsoft Docs
description: Découvrez comment exécuter une application multiniveau dans plusieurs régions Azure Stack Hub pour une haute disponibilité.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/01/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: fc3b9d3e620bfd017f7d5870a8e334c1d9ace579
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75818500"
---
# <a name="run-an-n-tier-application-in-multiple-azure-stack-hub-regions-for-high-availability"></a>Exécuter une application multiniveau dans plusieurs régions Azure Stack Hub pour une haute disponibilité

Cette architecture de référence présente un ensemble de pratiques éprouvées pour l’exécution d’une application multiniveau dans plusieurs régions Azure Stack Hub, afin de bénéficier d’une haute disponibilité et d’une infrastructure de récupération d’urgence fiable. Dans ce document, Traffic Manager est utilisé pour obtenir une haute disponibilité. Toutefois, si Traffic Manager n’est pas un choix préféré dans votre environnement, une paire d’équilibreurs de charge hautement disponibles peut également le remplacer.

> [!Note]  
> Notez que l’instance Traffic Manager utilisée dans l’architecture ci-dessous doit être configurée dans Azure et que les points de terminaison utilisés pour configurer le profil Traffic Manager doivent être des adresses IP routables publiquement.

## <a name="architecture"></a>Architecture

Cette architecture repose sur celle décrite dans l’article [Application multiniveau avec SQL Server](iaas-architecture-windows-sql-n-tier.md).

![Architecture réseau à haute disponibilité pour les applications multiniveaux Azure](./media/iaas-architecturesql-n-tier-multi-region/image1.png)

-   **Régions primaires et secondaires**. Pour obtenir une plus haute disponibilité, utilisez deux régions. L’une est la région primaire, tandis que l’autre sert au basculement.

-   **Azure Traffic Manager**. [Traffic Manager](https://azure.microsoft.com/services/traffic-manager) achemine les requêtes entrantes vers l’une des régions. Pendant le fonctionnement normal, il achemine les requêtes vers la région primaire. Si cette région n’est plus disponible, Traffic Manager bascule vers la région secondaire. Pour plus d’informations, consultez la section [Configuration de Traffic Manager](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server#traffic-manager-configuration).

-   **Groupe de ressources**. Créez des [groupes de ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) distincts pour la région primaire et la région secondaire. Vous obtenez ainsi la flexibilité nécessaire pour gérer chaque région comme une collection de ressources unique. Par exemple, vous pourriez redéployer une région sans arrêter l’autre. [Liez les groupes de ressources](https://docs.microsoft.com/azure/resource-group-link-resources) afin de pouvoir exécuter une requête pour répertorier toutes les ressources de l’application.

-   **Réseaux virtuels**. Créez un réseau virtuel distinct pour chaque région. Vérifiez que les espaces d’adressage ne se chevauchent pas.

-   **Groupe de disponibilité SQL Server Always On**. Si vous utilisez SQL Server, nous vous recommandons d’utiliser des [groupes de disponibilité AlwaysOn SQL](https://msdn.microsoft.com/library/hh510230.aspx) pour la haute disponibilité. Créez un groupe de disponibilité unique qui comprend les instances de SQL Server dans les deux régions.

-   **Connexion VPN de réseau virtuel à réseau virtuel**. Comme VNET Peering n’est pas encore disponible sur Azure Stack Hub, utilisez une connexion VPN de réseau virtuel à réseau virtuel pour connecter les deux réseaux virtuels. Pour plus d’informations, voir [Réseau virtuel à réseau virtuel dans Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-network-howto-vnet-to-vnet?view=azs-1908).

## <a name="recommendations"></a>Recommandations

Une architecture multirégion peut offrir une meilleure disponibilité qu’un déploiement dans une seule région. Si une interruption de service régionale affecte la région primaire, vous pouvez utiliser [Traffic Manager](https://azure.microsoft.com/services/traffic-manager) pour basculer vers la région secondaire. Cette architecture peut également se révéler utile en cas de défaillance d’un sous-système spécifique de l’application.

Plusieurs approches générales permettent de bénéficier d’une haute disponibilité dans l’ensemble des régions :

-   **Mode actif/passif avec serveur de secours**. Le trafic est dirigé vers une région, tandis que l’autre région est en attente sur le serveur de secours. Le terme « serveur de secours » signifie que les machines virtuelles de la région secondaire sont allouées et en cours d’exécution en permanence.

-   **Mode actif/passif avec reprise progressive**. Le trafic est dirigé vers une région, tandis que l’autre région est en attente sur le centre de données de reprise progressive. Le terme « reprise progressive » signifie que les machines virtuelles de la région secondaire ne sont pas allouées tant qu’elles ne sont pas requises pour le basculement. La mise en œuvre de cette approche se révèle moins coûteuse, mais nécessite davantage de temps en cas de défaillance.

-   **Actif/actif**. Les deux régions sont actives, et la charge de travail des requêtes est équilibrée entre les régions. Si l’une des régions n’est plus disponible, elle est mise hors service.

Cette architecture de référence est axée sur le mode actif/passif avec serveur de secours, et utilise Traffic Manager pour le basculement. Vous pouvez déployer un petit nombre de machines virtuelles pour le serveur de secours, puis effectuer un scale-out en fonction des besoins.

### <a name="traffic-manager-configuration"></a>Configuration de Traffic Manager

Considérez les points suivants lors de la configuration de Traffic Manager :

-   **Routage**. Traffic Manager prend en charge plusieurs [algorithmes de routage](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods). Pour le scénario décrit dans cet article, utilisez le routage *par priorité* (auparavant désigné sous le terme de routage *par basculement*). Quand cette méthode de routage est configurée, Traffic Manager envoie toutes les requêtes à la région primaire, sauf si elle devient inaccessible. À ce moment-là, les requêtes basculent automatiquement vers la région secondaire. Consultez [Configurer la méthode de routage de basculement](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-failover-routing-method).

-   **Sonde d’intégrité**. Traffic Manager utilise une [sonde](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) HTTP (ou HTTPS) pour superviser la disponibilité de chaque région. La sonde vérifie la présence d’une réponse HTTP 200 pour un chemin d’URL spécifié. Une bonne pratique consiste à créer un point de terminaison qui signale l’intégrité globale de l’application et à utiliser ce point de terminaison pour la sonde d’intégrité. Dans le cas contraire, la sonde risque de signaler un point de terminaison intègre alors que des parties critiques de l’application sont défaillantes. Pour plus d’informations, consultez [Modèle Supervision de point de terminaison d’intégrité](https://docs.microsoft.com/azure/architecture/patterns/health-endpoint-monitoring).

Quand Traffic Manager déclenche un basculement, l’application reste inaccessible aux clients pendant un certain laps de temps. Ce laps de temps dépend des facteurs suivants :

-   La sonde d’intégrité doit détecter que la région primaire est devenue inaccessible.

-   Les serveurs DNS (Domain Name Service) doivent mettre à jour les enregistrements DNS mis en cache pour l’adresse IP, qui dépend de la durée de vie (TTL) DNS. La valeur TTL par défaut est de 300 secondes (5 minutes), mais vous pouvez configurer cette valeur quand vous créez le profil Traffic Manager.

Pour plus d’informations, consultez [À propos de la supervision de Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring).

En cas de basculement de Traffic Manager, nous vous recommandons de procéder à une restauration manuelle plutôt que d’implémenter une restauration automatique. Dans le cas contraire, l’application risque d’alterner continuellement entre les régions. Vérifiez que tous les sous-systèmes de l’application sont intègres avant d’effectuer la restauration automatique.

Notez que Traffic Manager procède à une restauration automatique par défaut. Pour éviter cela, diminuez manuellement la priorité de la région primaire après un événement de basculement. Par exemple, supposez que la région primaire a la priorité 1, et que la base de données secondaire a la priorité 2. Après un basculement, définissez la priorité de la région primaire sur la valeur 3 afin d’empêcher la restauration automatique. Quand vous êtes prêt à rebasculer vers cette région, redéfinissez sa priorité sur la valeur 1.

La commande [Azure CLI](https://docs.microsoft.com/cli/azure/) suivante met à jour la priorité :

```cli  
az network traffic-manager endpoint update --resource-group <resource-group> --profile-name <profile>
    --name <endpoint-name> --type externalEndpoints --priority 3
```

Une autre approche consiste à désactiver temporairement le point de terminaison jusqu’à ce que vous soyez prêt à effectuer la restauration automatique :

```cli
az network traffic-manager endpoint update --resource-group <resource-group> --profile-name <profile>
    --name <endpoint-name> --type externalEndpoints --endpoint-status Disabled
```

En fonction de la cause d’un basculement, vous devrez peut-être redéployer les ressources au sein d’une région. Avant d’effectuer une restauration automatique, exécutez un test de disponibilité opérationnelle. Le test doit vérifier entre autres ce qui suit :

-   Les machines virtuelles sont configurées correctement. (Tous les logiciels nécessaires sont installés, IIS est en cours d’exécution, et ainsi de suite.)

-   Les sous-systèmes d’application sont intègres.

-   Test fonctionnel. (Par exemple, le niveau de la base de données est accessible à partir du niveau Web.)

### <a name="configure-sql-server-always-on-availability-groups"></a>Configurer les groupes de disponibilité SQL Server AlwaysOn

Avec les versions antérieures à Windows Server 2016, les groupes de disponibilité SQL Server AlwaysOn nécessitent un contrôleur de domaine et tous les nœuds du groupe de disponibilité doivent être dans le même domaine Active Directory (AD).

Pour configurer le groupe de disponibilité

-   Au minimum, placez deux contrôleurs de domaine dans chaque région.

-   Donnez à chaque contrôleur de domaine une adresse IP statique.

-   Créez un [réseau virtuel](https://docs.microsoft.com/azure-stack/user/azure-stack-vpn-gateway-about-vpn-gateways) pour activer la communication entre les deux réseaux virtuels.

-   Pour chaque réseau virtuel, ajoutez les adresses IP des contrôleurs de domaine (des deux régions) à la liste des serveurs DNS. Vous pouvez utiliser la commande CLI suivante. Pour plus d’informations, consultez [Modifier les serveurs DNS](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network#change-dns-servers).

    ```cli
    az network vnet update --resource-group <resource-group> --name <vnet-name> --dns-servers "10.0.0.4,10.0.0.6,172.16.0.4,172.16.0.6"
    ```

-   Créez un cluster [WSFC (Clustering de basculement Windows Server)](https://msdn.microsoft.com/library/hh270278.aspx) qui inclut les instances de SQL Server dans les deux régions.

-   Créez un groupe de disponibilité SQL Server AlwaysOn qui inclut les instances de SQL Server dans les régions primaire et secondaire. Pour connaître les étapes, consultez [Extending AlwaysOn Availability Group to Remote Azure Datacenter (PowerShell) (Extension de groupe de disponibilité AlwaysOn à un centre de données Azure à distance (PowerShell)](https://techcommunity.microsoft.com/t5/DataCAT/Extending-AlwaysOn-Availability-Group-to-Remote-Azure-Datacenter/ba-p/305217).

    -   Placez le réplica principal dans la région primaire.

    -   Placez un ou plusieurs réplicas secondaires dans la région primaire. Configurez-les pour qu’ils utilisent la validation synchrone avec basculement automatique.

    -   Placez un ou plusieurs réplicas secondaires dans la région secondaire. Pour des raisons de performances, configurez-les afin qu’ils utilisent la validation *asynchrone*. (Dans le cas contraire, toutes les transactions T-SQL doivent attendre un aller-retour sur le réseau vers la région secondaire.)

> [!Note]  
> Les réplicas avec validation asynchrone ne prennent pas en charge le basculement automatique.

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Avec une application multiniveau complexe, vous n’aurez peut-être pas besoin de répliquer l’ensemble de l’application dans la région secondaire. Au lieu de cela, vous pourrez simplement répliquer un sous-système critique nécessaire pour prendre en charge la continuité d’activité.

Traffic Manager est un point de défaillance possible dans le système. Si le service Traffic Manager échoue, les clients ne peuvent plus accéder à votre application pendant le temps d’arrêt. Consultez le [contrat SLA de Traffic Manager](https://azure.microsoft.com/support/legal/sla/traffic-manager) et déterminez si Traffic Manager peut à lui seul répondre à vos exigences métiers en matière de haute disponibilité. Si tel n’est pas le cas, envisagez d’ajouter une autre solution de gestion du trafic en guise de restauration automatique. En cas de défaillance du service Azure Traffic Manager, modifiez vos enregistrements CNAME dans DNS pour qu’ils pointent vers l’autre service de gestion du trafic. (Cette opération doit être effectuée manuellement, et votre application reste inaccessible tant que ces modifications DNS n’ont pas été propagées.)

Pour le cluster SQL Server, deux scénarios de basculement doivent être pris en compte :

-   Tous les réplicas de base de données SQL Server dans la région primaire échouent. Cela peut par exemple se produire pendant une panne régionale. Dans ce cas, vous devez faire basculer manuellement le groupe de disponibilité, même si Traffic Manager bascule automatiquement vers le frontend. Suivez les étapes de l’article [Perform a Forced Manual Failover of a SQL Server Availability Group](https://msdn.microsoft.com/library/ff877957.aspx) (Effectuer un basculement manuel forcé d’un groupe de disponibilité SQL Server), qui explique comment effectuer un basculement forcé à l’aide de SQL Server Management Studio, Transact-SQL ou PowerShell dans SQL Server 2016.

    > [!Warning]  
    > Avec le basculement forcé, il existe un risque de perte de données. Une fois la région primaire de nouveau en ligne, prenez un instantané de la base de données et utilisez [tablediff](https://msdn.microsoft.com/library/ms162843.aspx) pour rechercher les différences.

-   Traffic Manager bascule vers la région secondaire, mais le réplica de base de données SQL Server principal est toujours disponible. Par exemple, le niveau frontend peut échouer sans affecter les machines virtuelles SQL Server. Dans ce cas, le trafic Internet est acheminé vers la région secondaire, et cette région peut toujours se connecter au réplica principal. Toutefois, il y aura une latence accrue, car les connexions SQL Server traversent différentes régions. Dans ce cas, vous devez effectuer un basculement manuel comme suit :

    1.  Faites basculer temporairement un réplica de base de données SQL Server dans la région secondaire sur la validation *synchrone*. Ainsi, vous ne perdrez aucune donnée pendant le basculement.

    2.  Basculez vers ce réplica.

    3.  Quand vous rebasculez vers la région primaire, restaurez le paramètre de validation asynchrone.

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Quand vous mettez à jour votre déploiement, mettez à jour une seule région à la fois, afin de réduire le risque de défaillance globale due à une configuration incorrecte ou à une erreur dans l’application.

Testez la résilience aux défaillances du système. Voici quelques scénarios courants de défaillance à tester :

-   Arrêt des instances de machine virtuelle.

-   Pression sur les ressources telles que le processeur et la mémoire.

-   Déconnexion/délai de réseau.

-   Blocage des processus.

-   Expiration des certificats.

-   Simulation de défaillances matérielles.

-   Arrêt du service DNS sur les contrôleurs de domaine.

Mesurez les temps de récupération et vérifiez qu’ils répondent aux besoins de votre entreprise. Testez également des combinaisons de défaillances.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les modèles Azure Cloud, consultez [Modèles de conception cloud](https://docs.microsoft.com/azure/architecture/patterns).