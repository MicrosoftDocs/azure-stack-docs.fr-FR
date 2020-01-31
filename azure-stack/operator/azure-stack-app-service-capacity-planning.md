---
title: Planification de capacité pour des rôles serveur App Service dans Azure Stack Hub
description: Découvrez comment planifier des rôles serveur App Service dans Azure Stack Hub.
author: BryanLa
ms.topic: article
ms.date: 03/13/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/13/2019
ms.openlocfilehash: 78482b149a8397b9cc441cd97da905b782f9b7d4
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76876318"
---
# <a name="capacity-planning-for-app-service-server-roles-in-azure-stack-hub"></a>Planification de capacité pour des rôles serveur App Service dans Azure Stack Hub

Pour configurer un déploiement prêt pour la production d’Azure App Service sur Azure Stack Hub, vous devez planifier la capacité que vous attendez que le système prenne en charge.  

Cet article fournit des instructions pour le nombre minimal d’instances de calcul et de références SKU de calcul à utiliser pour un déploiement de production.

Vous pouvez planifier votre stratégie de capacité App Service à l’aide de ces instructions.

| Rôle serveur App Service | Nombre d’instances minimal recommandé | Référence SKU de calcul recommandée|
| --- | --- | --- |
| Contrôleur | 2 | A1 |
| Serveur frontal | 2 | A1 |
| Gestion | 2 | A3 |
| Serveur de publication | 2 | A1 |
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

Le rôle du modèle de déploiement Azure App Classic gère les points de terminaison d’App Service Azure Resource Manager et des API, les extensions de portail (administrateur, locataire, Functions) et le service des données. Le rôle de serveur de gestion ne nécessite généralement que 4 Go de RAM environ dans un environnement de production. Toutefois, l’utilisation de l’UC peut augmenter lorsque de nombreuses tâches de gestion (par exemple, la création de sites web) sont effectuées. Pour une haute disponibilité, vous devez disposer de plusieurs serveurs affectés à ce rôle, et au moins deux cœurs par serveur.

## <a name="publisher-role"></a>Rôle de serveur de publication

**Minimum recommandé** : Deux instances de A1 Standard

Si de nombreux utilisateurs publient simultanément, le rôle de serveur de publication peut faire face à une utilisation élevée du processeur. Pour une haute disponibilité, vérifiez que plusieurs rôles de serveur de publication sont disponibles. Le serveur de publication gère uniquement le trafic FTP/FTPS.

## <a name="web-worker-role"></a>Rôle de travail

**Minimum recommandé** : Deux instances de A1 Standard

Pour offrir une haute disponibilité, prévoyez au moins quatre rôles de worker web : deux pour le mode de site web partagé et deux pour chaque niveau de worker dédié que vous prévoyez de proposer. Les modes de calcul partagé et dédié offrent différents niveaux de service aux locataires. Vous aurez peut-être besoin de traitements web supplémentaires si vous avez de nombreux clients :

- Utilisant des niveaux de travail en mode de calcul dédié (qui consomment beaucoup de ressources).
- S’exécutant en mode de calcul partagé.

Après qu’un utilisateur a créé un plan App Service pour une référence (SKU) en mode de calcul dédié, le nombre de traitements web spécifiés dans ce plan App Service n’est plus disponible pour les utilisateurs.

Pour fournir Azure Functions aux utilisateurs dans le modèle de plan de consommation, vous devez déployer des traitements web partagés.

Quand vous choisissez le nombre de traitements web partagés à utiliser, passez en revue les considérations suivantes :

- **Mémoire** : La mémoire est la ressource la plus critique pour un rôle de travail web. Une mémoire insuffisante a un impact sur les performances du site web lorsque la mémoire virtuelle est échangée à partir du disque. Chaque serveur nécessite environ 1,2 Go de RAM pour le système d’exploitation. La RAM au-dessus de ce seuil peut être utilisée pour exécuter des sites web.
- **Pourcentage de sites web actifs** : En règle générale, environ 5 pour cent des applications d’un service Azure App Service sur un déploiement d’Azure Stack Hub sont actives. Toutefois, le pourcentage d’applications actives à un moment donné peut varier. Avec un taux d’applications actives de 5 pour cent, le nombre maximal d’applications à placer dans un service Azure App Service sur un déploiement d’Azure Stack Hub doit être inférieur à 20 fois le nombre de sites web actifs (5 x 20 = 100).
- **Empreinte mémoire moyenne** : L’empreinte mémoire moyenne des applications observée dans les environnements de production s’élève à environ 70 Mo. Sur la base de cette empreinte mémoire, la mémoire allouée sur l’ensemble des ordinateurs ou machines virtuelles avec le rôle de worker web se calcule comme suit :

   `Number of provisioned applications * 70 MB * 5% - (number of web worker roles * 1044 MB)`

   Par exemple, s’il y a 5 000 applications dans un environnement qui exécute 10 rôles de worker web, chaque machine virtuelle ayant un rôle de worker web doit disposer de 7 060 Mo de RAM :

   `5,000 * 70 * 0.05 - (10 * 1044) = 7060 (= about 7 GB)`

   Pour plus d’informations sur l’ajout d’instances de worker supplémentaires, consultez [Ajout de rôles de worker supplémentaires](azure-stack-app-service-add-worker-roles.md).

### <a name="additional-considerations-for-dedicated-workers-during-upgrade-and-maintenance"></a>Considérations supplémentaires pour les workers dédiés au cours de la mise à niveau et la maintenance

Pendant la mise à niveau et la maintenance des workers, Azure App Service sur Azure Stack Hub effectue la maintenance sur 20 % de chaque niveau de worker à tout moment.  Par conséquent, les administrateurs cloud doivent toujours conserver un pool de 20 % de workers non alloués par niveau de worker, afin de garantir que leurs locataires ne subissent pas de perte de service pendant les opérations de mise à niveau et de maintenance.  Par exemple, si vous avez dix workers dans un niveau de worker, assurez-vous de laisser deux workers non alloués pour les besoins de mise à niveau et de maintenance. Si les dix workers sont alloués, vous devez faire un scale-up du niveau de worker pour conserver un pool de workers non alloués. 

Durant la mise à niveau et la maintenance, Azure App Service déplace les charges de travail vers les workers non alloués pour garantir la continuité d’exécution des charges de travail. Toutefois, si aucun worker non alloué n’est disponible pendant la mise à niveau, il y a un risque d’interruption de la charge de travail du locataire. En ce qui concerne les workers partagés, les clients n’ont pas besoin de provisionner des workers supplémentaires, car le service alloue automatiquement les applications de locataire dans les workers disponibles. Pour garantir une haute disponibilité, il faut au minimum deux workers dans ce niveau.

Les administrateurs cloud peuvent superviser l’allocation de leurs niveaux de worker dans la zone d’administration d’App Service sur le portail administrateur Azure Stack Hub. Accédez à App Service, puis sélectionnez Niveaux de worker dans le volet gauche. Le tableau Niveaux de worker indique le nom du niveau de worker, la taille, l’image utilisée, le nombre de workers disponibles (non alloués), le nombre total de workers dans chaque niveau et l’état global du niveau de worker.

![Administration App Service - Niveaux de worker][1]

## <a name="file-server-role"></a>Rôle de serveur de fichiers

Pour le rôle de serveur de fichiers, vous pouvez utiliser le serveur de fichiers autonome à des fins de développement et de test. Par exemple, si vous déployez Azure App Service sur ASDK (Azure Stack Development Kit), vous pouvez utiliser [ce modèle](https://aka.ms/appsvconmasdkfstemplate).  À des fins de production, vous devez utiliser un serveur de fichiers Windows préconfiguré ou un serveur de fichiers préconfiguré autre que Windows.

Dans les environnements de production, le rôle de serveur de fichiers fait face à des E/S de disque intensives. Étant donné que ce rôle héberge tous les fichiers de contenu et d’application des sites web des utilisateurs, vous devez préconfigurer l’une des ressources suivantes pour ce rôle :

- Serveur de fichiers Windows
- Cluster de serveurs de fichiers Windows
- Serveur de fichiers non Windows
- Cluster de serveurs de fichiers non Windows
- Appareil NAS (Network Attached Storage)

Pour plus d’informations, consultez la rubrique [Provisionner un serveur de fichiers](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Étapes suivantes

[Conditions préalables pour le déploiement d’App Service sur Azure Stack Hub](azure-stack-app-service-before-you-get-started.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-capacity-planning/worker-tier-allocation.png