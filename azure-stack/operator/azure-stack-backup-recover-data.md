---
title: Récupérer suite à une perte catastrophique de données dans Azure Stack à l’aide du service de sauvegarde d’infrastructure | Microsoft Docs
description: Lorsqu’une défaillance catastrophique provoque l’échec d’Azure Stack, vous pouvez restaurer vos données d’infrastructure lors du rétablissement de votre déploiement Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 2ECE8580-0BDE-4D4A-9120-1F6771F2E815
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: f459804d33871ec97bf50e55ed01685001dcbfc7
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617917"
---
# <a name="recover-from-catastrophic-data-loss"></a>Récupérer des données suite à une perte catastrophique

*S’applique à : systèmes intégrés Azure Stack.*

Azure Stack exécute des services Azure dans votre centre de données et peut s’exécuter sur des environnements aussi petits que quatre nœuds installés dans un seul rack. En revanche, Azure s’exécute dans plusieurs centres de données et zones situés dans plus de 40 régions. Les ressources de l’utilisateur peuvent s’étendre sur plusieurs serveurs, racks, centres de données et régions. Actuellement, Azure Stack vous permet uniquement de déployer l’ensemble de votre cloud sur un rack unique. Cela expose votre cloud au risque d’événements catastrophiques pouvant survenir dans votre centre de données ou de défaillances dues à des bogues de produits importants. Lorsqu’un incident survient, l’instance Azure Stack est mise hors connexion. Toutes les données sont potentiellement irrécupérables.

Selon la cause racine de la perte de données, vous devrez peut-être réparer un seul service d’infrastructure ou restaurer l’intégralité de l’instance Azure Stack. Vous devrez peut-être même effectuer la restauration sur un autre matériel situé au même endroit ou dans un lieu différent.

Ce scénario couvre la récupération de toute votre installation en cas de défaillance de l’équipement et de redéploiement du cloud privé.

| Scénario                                                           | Perte de données                            | Considérations                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Récupérer suite à une perte catastrophique de données en raison d’un sinistre ou d’un bogue relatif à un produit | Toutes les données relatives à l’infrastructure, à l’utilisateur et aux applications | Les applications et données utilisateur sont protégées séparément des données d’infrastructure |

## <a name="workflows"></a>Flux de travail

La protection Azure Stack commence par la sauvegarde distincte des données relatives à l’infrastructure et de celles des applications/des locataires. Ce document explique comment protéger l’infrastructure. 

![Déploiement initial d’Azure Stack](media/azure-stack-backup/azure-stack-backup-workflow1.png)

Dans le pire des cas, lorsque toutes les données sont perdues, la récupération Azure Stack est le processus de restauration des données de l’infrastructure propres à ce déploiement Azure Stack et de toutes les toutes les données utilisateur. 

![Redéployer Azure Stack](media/azure-stack-backup/azure-stack-backup-workflow2.png)

## <a name="restore"></a>Restore

En cas de perte catastrophique de données, mais d’un matériel encore utilisable, le redéploiement d’Azure Stack est requis. Au cours du redéploiement, vous pouvez spécifier l’emplacement de stockage et les informations d’identification requises pour accéder aux sauvegardes. Dans ce mode, il est inutile de spécifier les services qui doivent être restaurés. Le contrôleur de sauvegarde d’infrastructure injecte un état de plan de contrôle dans le cadre du flux de travail de déploiement.

En cas de sinistre rendant le matériel inutilisable, le redéploiement est possible uniquement sur du nouveau matériel. Le redéploiement peut prendre plusieurs semaines dans l’attente de la commande du matériel de remplacement et de sa réception dans le centre de données. Il est possible de restaurer les données du plan de contrôle à tout moment. Toutefois, la restauration n’est pas prise en charge si la version de l’instance redéployée est supérieure (une version d’écart) à la version utilisée lors de la dernière sauvegarde. 

| Mode de déploiement | Point de départ | Point de terminaison                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nouvelle installation   | Build de base | Le fabricant OEM déploie Azure Stack et procède à une mise à jour vers la version la plus récente prise en charge.                                                                                                                                          |
| Mode Récupération   | Build de base | Le fabricant OEM déploie Azure Stack en mode de récupération et gère les exigences de correspondance de version en fonction de la dernière sauvegarde disponible. Le fabricant OEM termine le déploiement par la mise à jour vers la version la plus récente prise en charge. |

## <a name="data-in-backups"></a>Données des sauvegardes

Azure Stack prend en charge un type de déploiement appelé mode de récupération cloud. Ce mode est utilisé uniquement si vous choisissez de récupérer Azure Stack suite à un sinistre ou au bogue relatif à un produit ayant rendu la solution irrécupérable. Ce mode de déploiement ne récupère pas toutes les données utilisateur stockées dans la solution. L’étendue de ce mode de déploiement se limite à la restauration des données suivantes :

 - Les entrées de déploiement
 - les systèmes d’identité internes
 - La configuration des identités fédérées (déploiements déconnectés)
 - les certificats racines utilisés par l’autorité de certification interne
 - Les données utilisateur de configuration d’Azure Resource Manager, telles que les abonnements, les forfaits, les offres et les quotas pour les ressources relatives au calcul, au réseau et au stockage
 - Les secrets et les coffres KeyVault
 - Les attributions de stratégies RBAC et les attributions de rôles 

Aucune ressource Infrastructure as a Service (IaaS) ou Platform as a Service (PaaS) n’est récupérée lors du déploiement. Autrement dit, les machines virtuelles IaaS, les comptes de stockage, les objets BLOB, les tables, la configuration etc., sont perdues. L’objectif d’une récupération cloud est de s’assurer que les opérateurs et utilisateurs peuvent se reconnecter au portail une fois le déploiement terminé. Les utilisateurs qui se reconnectent ne voient plus leurs ressources. Les abonnements d’origine des utilisateurs sont restaurés, ainsi que les forfaits d’origine et les stratégies d’offres définies par l’administrateur. Les utilisateurs qui se reconnectent au système sont soumis aux mêmes contraintes que celles imposées par la solution d’origine avant le sinistre. Une fois la récupération cloud effectuée, l’opérateur peut restaurer manuellement les données à forte valeur ajoutée et tierces des fournisseurs de ressources, ainsi que les données associées.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les meilleures pratiques relatives à l’[utilisation du service de sauvegarde d’infrastructure](azure-stack-backup-best-practices.md).
