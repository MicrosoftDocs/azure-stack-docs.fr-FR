---
title: Vue d’ensemble d’Azure Stack Hub | Microsoft Docs
description: Vue d’ensemble d’Azure Stack Hub et de la façon dont il vous permet d’exécuter des services Azure dans votre centre de données.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/16/2019
ms.author: justinha
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 05/14/2019
ms.openlocfilehash: 9461b7c306eac0c2a1467378a49d4e91e234a629
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595425"
---
# <a name="azure-stack-hub-overview"></a>Vue d’ensemble d’Azure Stack Hub

Azure Stack Hub est une extension d’Azure qui permet d’exécuter des applications dans un environnement local et de fournir des services Azure dans votre centre de données. Avec une plateforme cloud cohérente, les organisations peuvent prendre en toute confiance des décisions informatiques en fonction des besoins métier, plutôt que des décisions métier en fonction de limitations informatiques.

## <a name="why-use-azure-stack-hub-"></a>Pourquoi utiliser Azure Stack Hub ?

Azure met à la disposition des développeurs une plateforme complète pour la création d’applications modernes. Cependant, certaines applications cloud doivent faire face à des obstacles comme la latence, une connectivité intermittente et des réglementations. Azure et Azure Stack Hub permettent de nouveaux cas d’utilisation du cloud hybride pour les applications métier internes et orientées client :

- **Solutions edge et déconnectées**. Répondez aux exigences de latence et de connectivité en traitant les données localement dans Azure Stack Hub, puis en les agrégeant dans Azure pour une analytique plus poussée, avec une logique d’application commune. Vous pouvez même déployer Azure Stack Hub déconnecté d’Internet sans connectivité à Azure. Les ateliers, les bateaux de croisière et les puits de mine sont quelques exemples.

- **Applications cloud conformes à différentes réglementations**. Développez et déployez des applications dans Azure, en profitant d’une flexibilité complète pour déployer localement avec Azure Stack Hub pour répondre à des exigences réglementaires ou stratégiques. Le code n’a pas besoin d’être modifié. L’audit global, les rapports financiers, les opérations de change, les jeux en ligne et les notes de frais sont des exemples d’applications.

- **Modèle d’application cloud locale.** Utilisez les services, les conteneurs ainsi que les architectures serverless et de microservices Azure pour mettre à jour et étendre des applications existantes ou en créer. Utilisez des processus DevOps cohérents sur Azure dans le cloud et sur Azure Stack Hub localement pour accélérer la modernisation des applications critiques.

Azure Stack Hub permet ces scénario d’utilisation en fournissant :

- Une expérience de livraison intégrée pour être rapidement opérationnel avec des systèmes Azure Stack Hub spécialement intégrés de fournisseurs de matériel partenaires approuvés. Après la livraison, Azure Stack Hub s’intègre facilement au centre de données avec une supervision via l’extension Nagios ou du Pack d’administration System Center Operations Manager.

- Une gestion d’identité souple avec Azure Active Directory (Azure AD) pour les environnements hybrides Azure et Azure Stack Hub, et en tirant parti des services de fédération Active Directory (AD FS) pour les déploiements déconnectés.

- Un environnement de développement d’application cohérent au niveau d’Azure pour optimiser la productivité des développeurs et autoriser des approches DevOps courantes dans les environnements hybrides.

- La livraison de services Azure à partir d’un environnement local en exploitant la puissance de calcul du cloud hybride. Adoptez des pratiques opérationnelles courantes sur Azure et Azure Stack Hub pour déployer et exploiter des services Azure IaaS et PaaS en utilisant les mêmes outils et les mêmes expériences d’administration qu’Azure. Microsoft apporte les innovations continues d’Azure à Azure Stack Hub, notamment de nouveaux services Azure, des mises à jour de services existants ainsi que de nouvelles images et applications de la Place de marché Azure.

## <a name="azure-stack-hub-architecture"></a>Architecture d’Azure Stack Hub

Les systèmes Azure Stack Hub intégrés sont constitués de racks de 4 à 16 serveurs construits par des fournisseurs de matériel partenaires approuvés et livrés directement à votre centre de données. Après la livraison, un fournisseur de solutions collabore avec vous pour déployer le système intégré et faire en sorte que la solution Azure Stack Hub réponde aux besoins de votre entreprise. Préparez votre centre de données en vérifiant que toutes les spécifications pour l’intégration du centre de données, comme l’alimentation, le refroidissement et la connectivité périphérique, sont en place.

> Pour plus d’informations sur l’expérience d’intégration des centres de données Azure Stack Hub, consultez [Intégration du centre de données Azure Stack Hub](azure-stack-customer-journey.md).

Azure Stack Hub s’appuie sur du matériel standard du secteur et il est géré à l’aide des outils que vous utilisez déjà pour la gestion des abonnements Azure. Ainsi, vous pouvez appliquer des processus DevOps cohérents, que vous soyez ou non connecté à Azure.

L’architecture d’Azure Stack Hub vous permet de fournir des services Azure à la périphérie pour des emplacements distants ou une connectivité intermittente, en étant déconnecté d’Internet. Vous pouvez créer des solutions hybrides qui traitent les données localement dans Azure Stack Hub, et les agrègent ensuite dans Azure en vue d’un traitement ou d’une analytique supplémentaire. Enfin, Azure Stack Hub étant installé localement, vous pouvez satisfaire à des exigences réglementaires ou stratégiques spécifiques avec la flexibilité du déploiement d’applications cloud localement sans modifier le code.

## <a name="deployment-options"></a>Options de déploiement

### <a name="production-or-evaluation-environments"></a>Environnements de production ou d’évaluation

Azure Stack Hub est proposé avec deux options de déploiement pour répondre à vos besoins : systèmes intégrés Azure Stack Hub pour la production et le Kit de développement Azure Stack (ASDK) Hub pour l’évaluation d’Azure Stack Hub :

- **Systèmes intégrés Azure Stack Hub** : Les systèmes intégrés Azure Stack Hub sont disponibles via un partenariat entre Microsoft et des fournisseurs de matériel partenaires, qui aboutit à la création d’une solution combinant innovation cloud et simplicité de gestion. Comme Azure Stack Hub est offert sous la forme d’un système matériel et logiciel intégré, vous avez la flexibilité et le contrôle dont vous avez besoin, ainsi que la capacité à innover depuis le cloud. Les systèmes intégrés Azure Stack Hub ont une taille comprise entre 4 et 16 nœuds, et ils sont pris en charge conjointement par le fournisseur de matériel partenaire et par Microsoft. Utilisez des systèmes intégrés Azure Stack Hub pour créer des scénarios et déployer de nouvelles solutions pour vos charges de travail de production.

- **Kit de développement Azure Stack (ASDK) Hub** : [Le kit ASDK](../asdk/asdk-what-is.md) est un déploiement gratuit sur un seul nœud d’Azure Stack, que vous pouvez utiliser pour découvrir et d’évaluer Azure Stack Hub. Vous pouvez également l’utiliser comme environnement de développement pour concevoir des applications à l’aide d’API et d’outils harmonisés avec Azure. Toutefois, le Kit ASDK n’est pas destiné à être utilisé comme environnement de production et présente les limitations suivantes par rapport au déploiement en production de systèmes intégrés complets :

    - Le Kit ASDK ne peut être associé qu’à un seul fournisseur d’identité Azure AD ou AD FS.
    - Comme tous les composants Azure Stack Hub sont déployés sur un seul ordinateur hôte, les ressources physiques disponibles sont limitées pour les ressources du locataire. Cette configuration n’est pas destinée à l’évaluation des performances ni à la mise à l’échelle.
    - Les scénarios de mise en réseau sont limités à cause des exigences de déploiement de la carte réseau et de l’hôte unique.

### <a name="connection-models"></a>Modèles de connexion

Vous pouvez choisir de déployer Azure Stack Hub **connecté**  à Internet (et à Azure) ou **déconnecté** d’Internet. Ce choix détermine les options disponibles pour vos magasin d’identités (Azure AD ou AD FS) et modèle de facturation (paiement à l’utilisation ou facturation selon la capacité).

> Pour plus d’informations, consultez les considérations relatives aux modèles de déploiement [connectés](azure-stack-connected-deployment.md) et [déconnectés](azure-stack-disconnected-deployment.md).

### <a name="identity-provider"></a>Fournisseur d’identité 

Azure Stack Hub utilise Azure AD ou AD FS pour fournir des identités. Azure AD est le fournisseur d’identité Microsoft multilocataire basé sur le cloud. La plupart des scénarios hybrides comportant des déploiements connectés à Internet utilisent Azure AD comme magasin d’identités.

Pour les déploiements déconnectés d’Azure Stack Hub, vous devez utiliser AD FS. Les fournisseurs de ressources Azure Stack Hub et les autres applications fonctionnent de façon similaire avec AD FS ou Azure AD. Azure Stack Hub inclut sa propre instance d’Active Directory ainsi qu’une API Graph Active Directory.

> [!IMPORTANT]
> Vous ne pouvez pas changer le fournisseur d’identité après le déploiement. Pour utiliser un autre fournisseur d’identité, vous devez redéployer Azure Stack Hub. Pour plus d’informations sur les considérations relatives aux identités Azure Stack Hub, vous pouvez consulter [Vue d’ensemble de l’identité pour Azure Stack Hub](azure-stack-identity-overview.md).

## <a name="how-is-azure-stack-hub-managed"></a>Comment Azure Stack Hub est-il géré ?

Vous pouvez gérer Azure Stack Hub avec le portail administrateur, le portail utilisateur ou [PowerShell](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1). Les portails Azure Stack Hub sont chacun secondés par des instances distinctes d’Azure Resource Manager. Un **opérateur Azure Stack Hub** utilise le portail administrateur pour gérer Azure Stack Hub, et pour effectuer des opérations comme la création d’offres de locataire, la gestion de l’intégrité du système intégré et la supervision de son état. Le portail utilisateur fournit une expérience en libre-service pour la consommation de ressources cloud comme des machines virtuelles, des comptes de stockage et des applications web.

> Pour plus d’informations sur la gestion d’Azure Stack Hub avec le portail administrateur, consultez le [guide de démarrage rapide consacré au portail d’administration d’Azure Stack Hub](azure-stack-manage-portals.md).

En tant qu’opérateur Azure Stack Hub, vous pouvez fournir un large éventail de services et d’applications, comme des [machines virtuelles](azure-stack-tutorial-tenant-vm.md), des [applications web](azure-stack-app-service-overview.md) et des bases de données [SQL Server](azure-stack-tutorial-sql.md) et [MySQL Server](azure-stack-tutorial-mysql.md) à haute disponibilité. Vous pouvez également utiliser les [modèles Azure Resource Manager fournis dans le guide de démarrage rapide consacré à Azure Stack Hub](https://github.com/Azure/AzureStack-QuickStart-Templates) pour déployer des produits comme SharePoint ou Exchange.

Avec le portail administrateur, vous pouvez [configurer Azure Stack Hub pour fournir des services](service-plan-offer-subscription-overview.md) à des locataires en utilisant des plans, des quotas, des offres et des abonnements. Les locataires peuvent s’abonner à plusieurs offres. Les offres peuvent contenir un ou plusieurs plans, et les plans peuvent contenir un ou plusieurs services. Les opérateurs gèrent aussi la capacité et répondent aux alertes.

Quand Azure Stack Hub est configuré, un **utilisateur Azure Stack Hub** consomme les services offerts par l’opérateur. Les utilisateurs peuvent provisionner, superviser et gérer les services auxquels ils sont abonnés, comme des applications web, du stockage et des machines virtuelles.

> Pour en savoir plus sur la gestion d’Azure Stack Hub, notamment les comptes à utiliser selon les situations, les responsabilités classiques de l’opérateur, les informations à communiquer à vos utilisateurs et la façon d’obtenir de l’aide, passez en revue les [principes fondamentaux de l’administration d’Azure Stack Hub](azure-stack-manage-basics.md).

## <a name="resource-providers"></a>Fournisseurs de ressources

Les fournisseurs de ressources sont les services web qui constituent la base de tous les services IaaS et PaaS d’Azure Stack Hub. Azure Resource Manager s’appuie sur différents fournisseurs de ressources pour fournir l’accès aux services. Chaque fournisseur de ressources vous permet de configurer et contrôler ses ressources respectives. Les administrateurs de services peuvent également ajouter de nouveaux fournisseurs de ressources personnalisés.

### <a name="foundational-resource-providers"></a>Fournisseurs de ressources fondamentaux

Il existe trois fournisseurs de ressources IaaS fondamentaux :

- **Calcul** : Le fournisseur de ressources de calcul permet aux locataires Azure Stack Hub de créer leurs propres machines virtuelles. Le fournisseur de ressources de calcul inclut la possibilité de créer des machines virtuelles ainsi que des extensions de machine virtuelle. Le service d’extension de machine virtuelle fournit des fonctionnalités IaaS pour les machines virtuelles Windows et Linux. Par exemple, vous pouvez utiliser le fournisseur de ressources de calcul pour provisionner une machine virtuelle Linux et exécuter des scripts Bash lors du déploiement pour configurer la machine virtuelle.
- **Fournisseur de ressources réseau** : Le fournisseur de ressources réseau offre une série de fonctionnalités SDN (Software Defined Networking) et NFV (Network Function Virtualization) pour le cloud privé. Vous pouvez utiliser le fournisseur de ressources réseau pour créer des ressources telles que des équilibreurs de charge logiciels, des adresses IP publiques, des groupes de sécurité réseau et des réseaux virtuels.
- **Fournisseur de ressources de stockage** : Le fournisseur de ressources de stockage propose quatre services de stockage cohérents au niveau d’Azure : [objet blob](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage), [file d’attente](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage), [table](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage) et gestion de comptes [Key Vault](https://docs.microsoft.com/azure/key-vault/) pour la gestion et l’audit des secrets, comme les mots de passe et les certificats. Le fournisseur de ressources de stockage offre également un service d’administration de stockage cloud pour faciliter l’administration du fournisseur de services de stockage cohérents au niveau d’Azure. Stockage Azure offre la flexibilité nécessaire pour stocker et récupérer de grandes quantités de données non structurées, comme des documents et des fichiers multimédias avec des objets blob Azure, et de données NoSQL structurées avec des tables Azure.

### <a name="optional-resource-providers"></a>Fournisseurs de ressources facultatifs

Il existe trois fournisseurs de ressources PaaS facultatifs que vous pouvez déployer et utiliser avec Azure Stack Hub :

- **Service d’application** : [Azure App Service sur Azure Stack Hub](azure-stack-app-service-overview.md) est une offre PaaS de Microsoft Azure disponible sur Azure Stack Hub. Le service permet à vos clients internes ou externes de créer des applications web, API et Azure Functions pour la plateforme ou l’appareil de leur choix.
- **SQL Server** : Utilisez le [fournisseur de ressources SQL Server](azure-stack-sql-resource-provider.md) pour offrir des bases de données SQL en tant que service d’Azure Stack Hub. Après avoir installé le fournisseur de ressources et l’avoir connecté à une ou plusieurs instances SQL Server, vous et vos utilisateurs pouvez créer des bases de données pour des applications natives cloud, des sites web qui utilisent SQL et d’autres charges de travail qui utilisent SQL.
- **MySQL Server** : Utilisez le [fournisseur de ressources MySQL Server](azure-stack-mysql-resource-provider-deploy.md) pour exposer des bases de données MySQL en tant que service d’Azure Stack Hub. Le fournisseur de ressources MySQL s’exécute en tant que service sur une machine virtuelle Windows Server 2016 Server Core.

## <a name="providing-high-availability"></a>Fourniture de la haute disponibilité

Pour garantir la haute disponibilité d'un système de production à plusieurs machines virtuelles dans Azure, ces machines virtuelles sont placées dans un [groupe à haute disponibilité](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) qui les répartit entre plusieurs domaines d'erreur et de mise à jour. À la plus petite échelle d’Azure Stack Hub, un domaine d’erreur au sein d’un groupe à haute disponibilité est défini comme un nœud unique dans l’unité d’échelle.  

Même si l’infrastructure d’Azure Stack Hub est déjà résiliente aux défaillances, la technologie sous-jacente (clustering de basculement) implique toujours un temps d’arrêt pour les machines virtuelles qui se trouvent sur un serveur physique impacté en cas de défaillance matérielle. Azure Stack Hub prend ne charge les groupes à haute disponibilité avec un maximum de trois domaines d’erreur, pour rester cohérent avec Azure.

- **Domaines d’erreur** : Les machines virtuelles placées dans un groupe à haute disponibilité sont physiquement isolées les unes des autres grâce à une répartition aussi équilibrée que possible sur plusieurs domaines d’erreur (nœuds Azure Stack Hub). En cas de défaillance matérielle, les machines virtuelles du domaine défaillant sont redémarrées dans d’autres domaines d’erreur. Elles seront conservées dans des domaines d’erreur distincts des autres machines virtuelles, mais si possible dans le même groupe à haute disponibilité. Une fois le matériel rétabli, les machines virtuelles seront rééquilibrées de façon à maintenir une haute disponibilité.

- **Domaines de mise à jour** : Les domaines de mise à jour représentent un autre concept Azure à l’origine de la haute disponibilité des groupes à haute disponibilité. Un domaine de mise à jour est un groupe logique de matériel sous-jacent pouvant faire l’objet simultanément d’une opération de maintenance. Les machines virtuelles qui se trouvent dans le même domaine de mise à jour sont redémarrées ensemble lors de la maintenance planifiée. Lorsqu’un client crée des machines virtuelles au sein d’un groupe à haute disponibilité, la plateforme Azure les distribue automatiquement dans ces différents domaines de mise à jour. Dans Azure Stack Hub, les machines virtuelles sont migrées en direct sur les autres hôtes en ligne du cluster avant que leur hôte sous-jacent soit mis à jour. Comme il ne se produit aucun temps d’arrêt du côté du locataire pendant la mise à jour d’un hôte, la fonctionnalité de domaine de mise à jour d’Azure Stack Hub n’existe que pour des raisons de compatibilité des modèles avec Azure. Les machines virtuelles d’un groupe à haute disponibilité affichent **0** comme numéro de domaine de mise à jour sur le portail.

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Vous pouvez utiliser le contrôle d’accès en fonction du rôle (RBAC) pour accorder l’accès au système aux utilisateurs, groupes et services autorisés en leur attribuant des rôles au niveau de l’abonnement, du groupe de ressources ou de la ressource individuelle. Chaque rôle définit le niveau d’accès dont un utilisateur, un groupe ou un service dispose sur les ressources Microsoft Azure Stack Hub.

Le contrôle d’accès en fonction du rôle d’Azure Stack Hub a trois rôles de base qui s’appliquent à tous les types de ressources : Propriétaire, Contributeur et Lecteur. Le Propriétaire dispose d’un accès total à toutes les ressources, ainsi que le droit de déléguer l’accès à d’autres personnes. Un Contributeur peut créer et gérer tous types de ressources Azure, mais ne peut pas accorder l’accès à d’autres personnes. Le Lecteur peut uniquement afficher les ressources existantes. Les autres rôles RBAC permettent de gérer des ressources Azure spécifiques. Par exemple, le rôle Contributeur de machine virtuelle autorise la création et la gestion de machines virtuelles, mais pas la gestion du réseau virtuel ou du sous-réseau auquel la machine virtuelle se connecte.

> Pour plus d’informations, consultez [Gérer le contrôle d’accès en fonction du rôle](azure-stack-manage-permissions.md).

## <a name="reporting-usage-data"></a>Création de rapports sur l’utilisation des données

Azure Stack Hub collecte et agrège les données d’utilisation de tous les fournisseurs de ressources, et il les transmet à Azure en vue de leur traitement par la solution commerciale Azure. Les données d’utilisation collectées sur Azure Stack Hub sont consultables via une API REST. Il existe une API de locataire, et des API de fournisseur et de fournisseur délégué cohérentes au niveau d’Azure pour obtenir les données d’utilisation sur tous les abonnements du locataire. Ces données peuvent être utilisées pour intégrer un outil ou un service externe de facturation ou de facturation interne. Une fois les données d’utilisation traitées par la solution commerciale Azure, elles peuvent être visualisées dans le portail de facturation Azure.

> Découvrez plus d’informations sur la [communication de données d’utilisation d’Azure Stack Hub à Azure](azure-stack-usage-reporting.md).

## <a name="next-steps"></a>Étapes suivantes

[Comparer Azure Stack Hub et le cloud mondial Azure](compare-azure-azure-stack.md).

[Informations de base sur l’administration](azure-stack-manage-basics.md).

[Démarrage rapide : Utiliser le portail d’administration Azure Stack Hub](azure-stack-manage-portals.md).
