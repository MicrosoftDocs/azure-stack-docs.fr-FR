---
title: Planification de la capacité pour les rôles serveur Azure App Service dans Azure Stack | Microsoft Docs
description: Planification de la capacité pour les rôles serveur Azure App Service dans Azure Stack
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/13/2019
ms.openlocfilehash: 8c41bd0241339cc4c29637bc70ef0fb71704feee
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269192"
---
# <a name="capacity-planning-for-azure-app-service-server-roles-in-azure-stack"></a>Planification de la capacité pour les rôles serveur Azure App Service dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Pour configurer un déploiement prêt pour la production d’Azure App Service sur Azure Stack, vous devez planifier la capacité que vous attendez que le système prenne en charge.  

Cet article fournit des instructions pour le nombre minimal d’instances de calcul et de références SKU de calcul à utiliser pour un déploiement de production.

Vous pouvez planifier votre stratégie de capacité App Service à l’aide de ces instructions.

| Rôle serveur App Service | Nombre d’instances minimal recommandé | Référence SKU de calcul recommandée|
| --- | --- | --- |
| Controller | 2 | A1 |
| Serveur frontal | 2 | A1 |
| gestion | 2 | A3 |
| Publisher | 2 | A1 |
| Rôles de travail - partagés | 2 | A1 |
| Rôles de travail - dédiés | 2 par niveau | A1 |

## <a name="controller-role"></a>rôle de contrôleur

**Minimum recommandé** : Deux instances de A1 Standard

Le contrôleur Azure App Service consomme généralement peu d’UC, de mémoire et de ressources réseau. Toutefois, pour la haute disponibilité, vous devez disposer de deux contrôleurs. Deux contrôleurs sont également le nombre maximal de contrôleurs autorisé. Vous pouvez créer le second contrôleur de sites web directement à partir du programme d’installation lors du déploiement.

## <a name="front-end-role"></a>Rôle du front-end

**Minimum recommandé** : Deux instances de A1 Standard

Le front-end achemine les requêtes vers les traitements web en fonction de leur disponibilité. Pour une haute disponibilité, vous devez disposer de plusieurs front-ends. À des fins de planification de la capacité, notez que chaque cœur peut traiter approximativement 100 demandes par seconde.

## <a name="management-role"></a>Rôle de gestion

**Minimum recommandé** : Deux instances de A3 Standard

Le rôle de gestion Azure App Service est responsable des points de terminaison App Service Azure Resource Manager, des points de terminaison des API, des extensions du portail (administration, locataire, portail Functions) et du service des données. Le rôle de serveur de gestion ne nécessite généralement que 4 Go de RAM environ dans un environnement de production. Toutefois, l’utilisation de l’UC peut augmenter lorsque de nombreuses tâches de gestion (par exemple, la création de sites web) sont effectuées. Pour une haute disponibilité, vous devez disposer de plusieurs serveurs affectés à ce rôle, et au moins deux cœurs par serveur.

## <a name="publisher-role"></a>Rôle de serveur de publication

**Minimum recommandé** : Deux instances de A1 Standard

Si de nombreux utilisateurs publient simultanément, le rôle de serveur de publication peut faire face à une utilisation élevée du processeur. Pour une haute disponibilité, vérifiez que plusieurs rôles de serveur de publication sont disponibles. Le serveur de publication gère uniquement le trafic FTP/FTPS.

## <a name="web-worker-role"></a>Rôle de travail

**Minimum recommandé** : Deux instances de A1 Standard

Pour une haute disponibilité, vous devez disposer d’au moins quatre rôles de traitement web : deux pour le mode de site web partagé et deux pour chaque niveau de travail dédié que vous prévoyez de proposer. Les modes de calcul partagé et dédié offrent différents niveaux de service aux locataires. Vous aurez peut-être besoin de traitements web supplémentaires si vous avez de nombreux clients :

- Utilisant des niveaux de travail en mode de calcul dédié (qui consomment beaucoup de ressources).
- S’exécutant en mode de calcul partagé.

Après qu’un utilisateur a créé un plan App Service pour une référence (SKU) en mode de calcul dédié, le nombre de traitements web spécifiés dans ce plan App Service n’est plus disponible pour les utilisateurs.

Pour fournir Azure Functions aux utilisateurs dans le modèle de plan de consommation, vous devez déployer des traitements web partagés.

Quand vous choisissez le nombre de traitements web partagés à utiliser, passez en revue les considérations suivantes :

- **Mémoire** : La mémoire est la ressource la plus critique pour un rôle de travail web. Une mémoire insuffisante a un impact sur les performances du site web lorsque la mémoire virtuelle est échangée à partir du disque. Chaque serveur nécessite environ 1,2 Go de RAM pour le système d’exploitation. La RAM au-dessus de ce seuil peut être utilisée pour exécuter des sites web.
- **Pourcentage de sites web actifs** : D’ordinaire, environ 5 pourcent des applications d’un service Azure App Service sur un déploiement Azure Stack sont actives. Toutefois, le pourcentage d’applications actives à un moment donné peut être supérieur ou inférieur. Avec un taux d’applications actives de 5 %, le nombre maximal d’applications à placer dans un plan Azure App Service sur un déploiement Azure Stack doit être inférieur à 20 fois le nombre de sites web actifs (5 x 20 = 100).
- **Empreinte mémoire moyenne** : L’empreinte mémoire moyenne des applications observées dans les environnements de production s’élève à environ 70 Mo. En utilisant cet encombrement, la mémoire allouée sur l’ensemble des ordinateurs ou machines virtuelles du rôle de traitement web peut être calculée comme suit :

   `Number of provisioned applications * 70 MB * 5% - (number of web worker roles * 1044 MB)`

   Par exemple, s’il y a 5 000 applications dans un environnement qui exécute 10 rôles de traitement web, chaque machine virtuelle du rôle de traitement web doit disposer de 7 060 Mo de RAM :

   `5,000 * 70 * 0.05 - (10 * 1044) = 7060 (= about 7 GB)`

   Pour plus d’informations sur l’ajout d’instances de traitement supplémentaires, consultez [Ajout de rôles de travail supplémentaires](azure-stack-app-service-add-worker-roles.md).

### <a name="additional-considerations-for-dedicated-workers-during-upgrade-and-maintenance"></a>Considérations supplémentaires pour les workers dédiés au cours de la mise à niveau et la maintenance

Pendant la mise à niveau et la maintenance des workers, Azure App Service sur Azure Stack effectue la maintenance sur 20 % de chaque niveau de worker à tout moment.  Par conséquent, les administrateurs cloud doivent toujours conserver un pool de 20 % de workers non alloués par niveau de worker pour garantir que leurs locataires ne subissent pas de perte de service pendant la mise à niveau et la maintenance.  Par exemple, si vous avez 10 workers dans un niveau de worker, vous devez vous assurer que 2 ne sont pas alloués pour permettre la mise à niveau et la maintenance. Si les 10 workers sont alloués, vous devez mettre à l’échelle le niveau de worker pour gérer un pool de workers non alloués. Pendant la mise à niveau et la maintenance, Azure App Service déplace les charges de travail vers les workers non alloués pour garantir le fonctionnement des charges de travail. Toutefois, s’il n’y a aucun worker non alloué disponible au cours de la mise à niveau, alors il existe un risque de temps d’arrêt pour la charge de travail du locataire.  En ce qui concerne les workers partagés, les clients n’ont pas besoin de provisionner des workers supplémentaires, car le service alloue automatiquement des applications de locataire au sein des workers disponibles, à des fins de haute disponibilité. Toutefois, au moins deux workers dans ce niveau sont requis.

Les administrateurs cloud peuvent surveiller leur allocation de niveau de worker dans la zone Administration App Service du portail d’administration Azure Stack.  Accédez à App Service, puis sélectionnez Niveaux de worker dans le volet gauche.  Le tableau Niveaux de worker indique le nom du niveau de worker, la taille, l’image utilisée, le nombre de workers disponibles (non alloués), le nombre total de workers dans chaque niveau et l’état global du niveau de worker.

![Administration App Service - Niveaux de worker][1]

## <a name="file-server-role"></a>Rôle de serveur de fichiers

Pour le rôle de serveur de fichiers, vous pouvez utiliser un serveur de fichiers autonome pour le développement et le test. Par exemple, lors du déploiement d’Azure App Service sur le Kit de développement Azure Stack (ASDK), vous pouvez utiliser [ce modèle](https://aka.ms/appsvconmasdkfstemplate).  À des fins de production, vous devez utiliser un serveur de fichiers Windows préconfiguré ou un serveur de fichiers préconfiguré autre que Windows.

Dans les environnements de production, le rôle de serveur de fichiers fait face à des E/S de disque intensives. Étant donné qu’il héberge tous les fichiers de contenu et d’application des sites web des utilisateurs, vous devez préconfigurer l’un des éléments suivants pour ce rôle :

- Serveur de fichiers Windows
- Cluster de serveurs de fichiers Windows
- Serveur de fichiers non Windows
- Cluster de serveurs de fichiers non Windows
- Appareil NAS (Network Attached Storage)

Pour plus d’informations, consultez l’article suivant : [Provisionner un serveur de fichiers](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez l’article suivant :

[Avant de commencer avec App Service sur Azure Stack](azure-stack-app-service-before-you-get-started.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-capacity-planning/worker-tier-allocation.png