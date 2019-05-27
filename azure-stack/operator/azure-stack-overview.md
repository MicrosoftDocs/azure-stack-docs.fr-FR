---
title: Qu’est-ce qu’Azure Stack ? | Microsoft Docs
description: Découvrez dans quelle mesure Azure Stack vous permet de tester les services Azure dans votre centre de données.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/29/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 05/14/2019
ms.openlocfilehash: b98be2ae02e65fea9356f35f2d2554e57dfb5628
ms.sourcegitcommit: 1655b2ef4d01d69ceeb52bc16f922bdc19cb968d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/15/2019
ms.locfileid: "65706290"
---
# <a name="azure-stack-overview"></a>Vue d’ensemble d’Azure Stack

Azure Stack est une extension d’Azure qui permet d’exécuter des applications dans un environnement local et de fournir des services Azure dans votre centre de données. Avec une plateforme cloud cohérente, les organisations peuvent prendre en toute confiance des décisions informatiques en fonction des besoins métier, plutôt que des décisions métier en fonction de limitations informatiques.

## <a name="why-use-azure-stack"></a>Pourquoi utiliser Azure Stack ?

Azure met à la disposition des développeurs une plateforme complète pour la génération d’applications modernes. Toutefois, certaines applications basées sur le cloud doivent faire face à des obstacles comme la latence, une connectivité intermittente et des réglementations. Azure et Azure Stack offrent de nouveaux cas d’utilisation de cloud hybride pour les applications orientées client et métier internes :

- **Solutions edge et déconnectées**. Répondez aux besoins de latence et de connectivité en traitant les données localement dans Azure Stack, puis en les agrégeant dans Azure pour une analytique plus poussée, avec une logique d’application commune. Vous pouvez même déployer Azure Stack en le déconnectant d’Internet sans connectivité à Azure. Les ateliers, les bateaux de croisière et les puits de mine sont quelques exemples.

- **Applications cloud conformes à diverses réglementations**. Développez et déployez des applications dans Azure, en profitant de l’entière flexibilité de déployer localement avec Azure Stack pour répondre aux exigences réglementaires ou stratégiques, sans modification du code. Les exemples d’applications incluent l’audit global, les rapports financiers, les opérations de change, les jeux en ligne et les notes de frais.

- **Modèle d’application cloud en local**. Utilisez les services Azure, les conteneurs ainsi que les architectures serverless et de microservice pour mettre à jour et étendre des applications existantes ou en créer. Utilisez des processus DevOps cohérents dans Azure (cloud) et Azure Stack (localement) pour accélérer la modernisation des applications hautement stratégiques.

Azure Stack permet ces scénario d’usage en fournissant :

- Une expérience de livraison intégrée pour être rapidement opérationnel avec des systèmes Azure Stack spécialement intégrés de l’un des fournisseurs de matériel partenaires approuvés. Après la livraison, Azure Stack s’intègre facilement au centre de données avec supervision par le biais de l’extension Nagios ou du Pack d’administration System Center Operations Manager.

- Une gestion d’identité souple à l’aide d’Azure Active Directory (Azure AD) pour les environnements hybrides Azure et Azure Stack et en tirant parti des services de fédération Active Directory (AD FS) pour les déploiements déconnectés. 

- Un environnement de développement d’application cohérent au niveau Azure pour optimiser la productivité des développeurs et autoriser des approches DevOps courantes dans les environnements hybrides.

- La livraison de services Azure à partir d’un environnement local en exploitant la puissance de calcul du cloud hybride. Adoptez des pratiques opérationnelles courantes sur Azure et Azure Stack pour déployer et exploiter les services Azure IaaS et PaaS en utilisant les mêmes outils et expériences d’administration qu’Azure. Microsoft apporte une innovation Azure continue à Azure Stack, notamment de nouveaux services Azure, des mises à jour de services existants ainsi que de nouvelles images et applications de la Place de marché Microsoft Azure.

## <a name="azure-stack-architecture"></a>Architecture d’Azure Stack
Les systèmes Azure Stack intégrés sont constitués de racks de 4 à 16 serveurs construits par des fournisseurs de matériel partenaires approuvés et envoyés directement à votre centre de données. Après la livraison, un fournisseur de solutions collabore avec vous pour déployer le système intégré et s’assurer que la solution Azure Stack répond aux besoins de votre entreprise. Vous devez préparer votre centre de données en vous assurant que toutes les exigences d’intégration de centre de données telles que l’alimentation et le refroidissement, la connectivité de frontière et autres sont en place. 

> Pour plus d’informations sur l’expérience d’intégration des centres de données Azure Stack, consultez [Intégration du centre de données Azure Stack](azure-stack-customer-journey.md).

Azure Stack repose sur du matériel standard du secteur et est géré à l’aide des outils que vous utilisez déjà pour la gestion des abonnements Azure. Ainsi, vous pouvez appliquer des processus DevOps cohérents, que vous soyez connecté ou non à Azure. 

L’architecture Azure Stack vous permet de fournir des services Azure à la périphérie pour des emplacements distants ou une connectivité intermittente, sans être déconnecté d’Internet. Vous pouvez créer des solutions hybrides qui traitent les données localement dans Azure Stack et les agrègent ensuite dans Azure en vue d’un traitement ou d’une analytique supplémentaire. Enfin, Azure Stack étant installé localement, vous pouvez satisfaire à des exigences réglementaires ou de stratégie spécifiques avec la flexibilité du déploiement d’applications cloud localement sans modifier de code. 

## <a name="deployment-options"></a>Options de déploiement

### <a name="production-or-evaluation-environments"></a>Environnements de production ou d’évaluation
Azure Stack est proposé en deux options de déploiement pour répondre à vos besoins : systèmes intégrés Azure Stack pour la production et Kit de développement Azure Stack (ASDK) pour l’évaluation d’Azure Stack :

- **Systèmes intégrés Azure Stack**. Les systèmes intégrés Azure Stack sont disponibles par le biais d’un partenariat entre Microsoft et des fournisseurs de matériel partenaires, qui aboutit à la création d’une solution combinant innovation cloud et simplicité de gestion. Comme Azure Stack est offert comme matériel et logiciel système intégré, vous avez la flexibilité et le contrôle dont vous avez besoin, ainsi que la capacité à innover depuis le cloud. Les systèmes intégrés Azure Stack présentent une taille comprise entre 4 et 16 nœuds et sont pris en charge conjointement par le fournisseur de matériel partenaire et Microsoft. Utilisez des systèmes intégrés Azure Stack pour créer des scénarios et déployer de nouvelles solutions pour vos charges de travail de production.

- **Kit de développement Azure Stack**. Le [Kit de développement Azure Stack (ASDK)](../asdk/asdk-what-is.md) est un déploiement à un seul nœud gratuit d’Azure Stack qui vous permet de découvrir et d’évaluer Azure Stack. Vous pouvez également l’utiliser comme environnement de développement pour concevoir des applications à l’aide d’API et d’outils harmonisés avec Azure. Toutefois, le Kit ASDK n’est pas destiné à être utilisé comme environnement de production et présente les limitations suivantes par rapport au déploiement en production de systèmes intégrés complets :

    - Il peut être associé à un seul fournisseur d’identité Azure Active Directory (Azure AD) ou de services de fédération Active Directory (AD FS).
    - Étant donné que tous les composants Azure Stack sont déployés sur un seul ordinateur hôte, les ressources physiques disponibles sont limitées pour les abonnés. Cette configuration n’est pas destinée à l’évaluation des performances ni à la mise à l’échelle.
    - Les scénarios de mise en réseau sont limités à cause des exigences de déploiement de la carte réseau et de l’hôte unique.

### <a name="connection-models"></a>Modèles de connexion
Vous pouvez choisir de déployer Azure Stack **en le connectant** à internet (et à Azure) ou **en l’en déconnectant**. Ce choix détermine les options disponibles pour vos magasin d’identités (Azure AD ou AD FS) et modèle de facturation (paiement à l’utilisation ou facturation selon la capacité).

> Pour plus d’informations, consultez les considérations relatives aux modèles de déploiement [connectés](azure-stack-connected-deployment.md) et [déconnectés](azure-stack-disconnected-deployment.md). 

### <a name="identity-provider"></a>Fournisseur d’identité 
Azure Stack utilise Azure Active Directory (Azure AD) ou Active Directory Federation Services (AD FS) pour fournir des identités. Azure AD est le fournisseur d’identité Microsoft multilocataire basé sur le cloud. La plupart des scénarios hybrides comportant des déploiements connectés à Internet utilisent Azure AD comme magasin d’identités. 

Pour les déploiements déconnectés d’Azure Stack, vous devez utiliser les services AD FS. Les fournisseurs de ressources Azure Stack et les autres applications fonctionnent de façon similaire avec AD FS ou Azure AD. Azure Stack inclut sa propre instance d’Active Directory ainsi qu’une API Graph Active Directory.

> [!IMPORTANT]
> Vous ne pouvez pas changer le fournisseur d’identité après le déploiement. Pour utiliser un autre fournisseur d’identité, vous devez redéployer Azure Stack.

> Pour plus d’informations sur les considérations relatives aux identités Azure Stack, vous pouvez consulter [Vue d’ensemble d’une identité pour Azure Stack](azure-stack-identity-overview.md).

## <a name="how-is-azure-stack-managed"></a>Comment Azure Stack est-il géré ?
Vous pouvez gérer Azure Stack à l’aide du portail d’administration, du portail utilisateur ou de [PowerShell](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1). Les portails Azure Stack sont chacun secondés par des instances distinctes d’Azure Resource Manager. Un **opérateur Azure Stack** utilise le portail d’administration pour gérer Azure Stack et effectuer des opérations telles que la création d’offres locataire, la gestion de l’intégrité du système intégré et la supervision de son état. Le portail utilisateur (également appelé portail du locataire) fournit une expérience de libre-service pour la consommation de ressources cloud telles que des machines virtuelles, des comptes de stockage et des applications web. 

> Pour plus d’informations sur la gestion d’Azure Stack à l’aide du portail d’administration, consultez le [guide de démarrage rapide consacré au portail d’administration Azure Stack](azure-stack-manage-portals.md).

En tant qu’opérateur Azure Stack, vous pouvez fournir un large éventail de services et d’applications, tels que des [machines virtuelles](azure-stack-tutorial-tenant-vm.md), des [applications web](azure-stack-app-service-overview.md) et des bases de données [SQL Server](azure-stack-tutorial-sql.md) et [MySQL Server](azure-stack-tutorial-mysql.md) hautement disponibles. Vous pouvez également utiliser les [modèles Azure Resource Manager fournis dans le guide de démarrage rapide consacré à Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) pour déployer des produits tels que SharePoint ou Exchange. 

À l’aide du portail d’administration, vous pouvez [configurer Azure Stack pour fournir des services](azure-stack-plan-offer-quota-overview.md) aux locataires à l’aide de plans, de quotas, d’offres et d’abonnements. Les locataires peuvent s’abonner à plusieurs offres. Les offres peuvent contenir un ou plusieurs plans, et les plans peuvent contenir un ou plusieurs services. Les opérateurs gèrent aussi la capacité et répondent aux alertes. 

Quand Azure Stack est configuré, un **utilisateur Azure Stack** (également appelé locataire) consomme les services offerts par l’opérateur. Les utilisateurs peuvent provisionner, superviser et gérer les services auxquels ils sont abonnés, tels que les applications web, le stockage et les machines virtuelles.

> Pour en savoir plus sur la gestion d’Azure Stack, notamment les comptes à utiliser selon les situations, les responsabilités classiques de l’opérateur, les informations à communiquer à vos utilisateurs et la façon d’obtenir de l’aide, passez en revue les [principes fondamentaux de l’administration d’Azure Stack](azure-stack-manage-basics.md).

## <a name="resource-providers"></a>Fournisseurs de ressources 
Les fournisseurs de ressources sont les services web qui constituent la base de tous les services Azure Stack IaaS et PaaS. Azure Resource Manager s’appuie sur différents fournisseurs de ressources pour fournir l’accès aux services. Chaque fournisseur de ressources vous permet de configurer et contrôler ses ressources respectives. Les administrateurs de services peuvent également ajouter de nouveaux fournisseurs de ressources personnalisés. 

### <a name="foundational-resource-providers"></a>Fournisseurs de ressources fondamentaux 
Il existe trois fournisseurs de ressources IaaS fondamentaux : 

- **Calcul**. Le fournisseur de ressources de calcul permet aux locataires Azure Stack de créer leurs propres machines virtuelles. Le fournisseur de ressources de calcul inclut la possibilité de créer des machines virtuelles ainsi que des extensions de machine virtuelle. Le service d’extension de machine virtuelle fournit des fonctionnalités IaaS pour les machines virtuelles Windows et Linux. Par exemple, vous pouvez utiliser le fournisseur de ressources de calcul pour provisionner une machine virtuelle Linux et exécuter des scripts Bash pendant le déploiement pour configurer la machine virtuelle.
- **Fournisseur de ressources réseau**. Le fournisseur de ressources réseau offre une série de fonctionnalités SDN (Software Defined Networking) et NFV (Network Function Virtualization) pour le cloud privé. Vous pouvez utiliser le fournisseur de ressources réseau pour créer des ressources telles que des équilibreurs de charge logiciels, des adresses IP publiques, des groupes de sécurité réseau et des réseaux virtuels.
- **Fournisseur de ressources de stockage**. Le fournisseur de ressources de stockage propose quatre services de stockage cohérents au niveau Azure : [objet blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage), [file d’attente](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage), [table](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage) et gestion de comptes [KeyVault](https://docs.microsoft.com/azure/key-vault/) pour la gestion et l’audit des secrets, tels que les mots de passe et les certificats. Le fournisseur de ressources de stockage offre également un service d’administration de stockage cloud pour faciliter l’administration du fournisseur de services de stockage cohérents au niveau Azure. Azure Storage offre la flexibilité nécessaire pour stocker et récupérer de grandes quantités de données non structurées, telles que des documents et des fichiers multimédias avec des objets blob Azure, et de données NoSQL structurées avec les tables Azure. 

### <a name="optional-resource-providers"></a>Fournisseurs de ressources facultatifs
Il existe trois fournisseurs de ressources PaaS facultatifs que vous pouvez déployer et utiliser avec Azure Stack : 

- **App Service**. [Azure App Service sur Azure Stack](azure-stack-app-service-overview.md) est une offre PaaS (platform-as-a-service) de Microsoft Azure disponible sur Azure Stack. Le service permet à vos clients internes ou externes de créer des applications web, d’API et Azure Functions pour la plateforme ou l’appareil de leur choix. 
- **SQL Server**. Utilisez le [fournisseur de ressources SQL Server](azure-stack-sql-resource-provider.md) pour offrir des bases de données SQL en tant que service d’Azure Stack. Après avoir installé le fournisseur de ressources et l’avoir connecté à une ou plusieurs instances SQL Server, vous et vos utilisateurs pouvez créer des bases de données pour des applications natives cloud, des sites web qui utilisent SQL et d’autres charges de travail qui utilisent SQL.
- **MySQL Server**. Utilisez le [fournisseur de ressources MySQL Server](azure-stack-mysql-resource-provider-deploy.md) pour exposer des bases de données MySQL en tant que service d’Azure Stack. Le fournisseur de ressources MySQL s’exécute en tant que service sur une machine virtuelle Windows Server 2016 Server Core.

## <a name="providing-high-availability"></a>Fourniture de la haute disponibilité
Pour garantir la haute disponibilité d'un système de production à plusieurs machines virtuelles dans Azure, ces machines virtuelles sont placées dans un [groupe à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) qui les répartit entre plusieurs domaines d'erreur et de mise à jour. À la plus petite échelle d'Azure Stack, un domaine d'erreur au sein d'un groupe à haute disponibilité est défini comme un nœud unique dans l'unité d'échelle.  

Même si l’infrastructure d’Azure Stack est déjà résistante aux défaillances, la technologie sous-jacente (clustering de basculement) implique toujours un temps d’arrêt pour les machines virtuelles qui se trouvent sur un serveur physique impacté en cas de défaillance matérielle. Azure Stack gère les groupes à haute disponibilité comportant trois domaines d’erreur maximum dans un souci de compatibilité avec Azure.

- **Domaines d’erreur**. Les machines virtuelles placées dans un groupe à haute disponibilité seront physiquement isolées les unes des autres grâce à une répartition aussi équilibrée que possible sur plusieurs domaines d’erreur (nœuds Azure Stack). En cas de défaillance matérielle, les machines virtuelles du domaine d’erreur défaillant sont redémarrées dans d’autres domaines d’erreur, distincts de ceux des autres machines virtuelles du même groupe à haute disponibilité si possible. Une fois le matériel rétabli, les machines virtuelles seront rééquilibrées de façon à maintenir une haute disponibilité. 
 
- **Domaines de mise à jour**. Les domaines de mise à jour représentent un autre concept Azure à l’origine de la haute disponibilité des groupes à haute disponibilité. Un domaine de mise à jour est un groupe logique de matériel sous-jacent pouvant faire l’objet simultanément d’une opération de maintenance. Les machines virtuelles qui se trouvent dans le même domaine de mise à jour sont redémarrées ensemble lors de la maintenance planifiée. Lorsqu’un client crée des machines virtuelles au sein d’un groupe à haute disponibilité, la plateforme Azure les distribue automatiquement dans ces différents domaines de mise à jour. Dans Azure Stack, les machines virtuelles sont migrées en direct sur les autres hôtes en ligne du cluster avant que leur hôte sous-jacent soit mis à jour. Comme il ne se produit aucun temps d’arrêt du côté du client pendant la mise à jour d’un hôte, la fonctionnalité de domaine de mise à jour d’Azure Stack n’existe qu’à des fins de compatibilité des modèles avec Azure. 

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle
Vous pouvez utiliser le contrôle d’accès en fonction du rôle (RBAC) pour accorder l’accès au système aux utilisateurs, groupes et services autorisés en leur attribuant des rôles au niveau de l’abonnement, du groupe de ressources ou de la ressource individuelle. Chaque rôle définit le niveau d’accès dont un utilisateur, un groupe ou un service dispose sur les ressources Microsoft Azure Stack.

Le contrôle d’accès en fonction du rôle Azure Stack comporte trois rôles de base qui s’appliquent à tous les types de ressources : Propriétaire, Contributeur et Lecteur. Le Propriétaire dispose d’un accès total à toutes les ressources, ainsi que le droit de déléguer l’accès à d’autres personnes. Un Contributeur peut créer et gérer tous types de ressources Azure, mais ne peut pas accorder l’accès à d’autres personnes. Le Lecteur peut uniquement afficher les ressources existantes. Les autres rôles RBAC permettent de gérer des ressources Azure spécifiques. Par exemple, le rôle de Contributeur de machine virtuelle autorise la création et la gestion de machines virtuelles, mais pas la gestion du réseau virtuel ou du sous-réseau auquel la machine virtuelle se connecte.

> Pour plus d’informations, consultez [Gérer le contrôle d’accès en fonction du rôle](azure-stack-manage-permissions.md). 

## <a name="reporting-usage-data"></a>Création de rapports sur l’utilisation des données
Azure Stack collecte et agrège les données d’utilisation de tous les fournisseurs de ressources, et il les transmet à Azure en vue de leur traitement par la solution commerciale Azure. Les données d’utilisation collectées sur Azure Stack sont consultables par le biais d’une API REST. Il existe une API client, et des API fournisseur et fournisseur délégué cohérentes au niveau Azure pour obtenir des données d’utilisation sur tous les abonnements du locataire. Ces données peuvent être utilisées pour intégrer un outil ou un service externe de facturation ou de facturation interne. Une fois les données d’utilisation traitées par la solution commerciale Azure, elles peuvent être visualisées dans le portail de facturation Azure.

> Découvrez-en plus sur la [communication de données d’utilisation d’Azure Stack à Azure](azure-stack-usage-reporting.md).

## <a name="next-steps"></a>Étapes suivantes

[Comparer Azure Stack et Azure international](compare-azure-azure-stack.md)

[Informations de base sur l’administration](azure-stack-manage-basics.md)

[Démarrage rapide : Utiliser le portail d’administration Azure Stack](azure-stack-manage-portals.md)