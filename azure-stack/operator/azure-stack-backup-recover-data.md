---
title: Récupérer suite à une grave perte de données dans Azure Stack | Microsoft Docs
description: Découvrez comment récupérer et restaurer les données de votre infrastructure dans Azure Stack après une grave perte de données.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: 2ECE8580-0BDE-4D4A-9120-1F6771F2E815
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: b2671446594377833609032e27ff02b7c53c763c
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974688"
---
# <a name="recover-from-catastrophic-data-loss"></a>Récupérer des données suite à une perte catastrophique

*S’applique à : systèmes intégrés Azure Stack.*

Azure Stack exécute des services Azure dans votre centre de données et peut s’exécuter sur des environnements aussi petits que quatre nœuds installés dans un seul rack. En revanche, Azure s’exécute dans plusieurs centres de données et zones situés dans plus de 40 régions. Les ressources de l’utilisateur peuvent s’étendre sur plusieurs serveurs, racks, centres de données et régions. Azure Stack permet seulement de déployer l’ensemble de votre cloud sur un même rack. Cette limitation expose votre cloud à la survenue d’événements graves au sein de votre centre de données ou de défaillances dues à des bogues produit importants. Lorsqu’un incident survient, l’instance Azure Stack est mise hors connexion. Toutes les données sont potentiellement irrécupérables.

Selon la cause racine de la perte de données, vous devrez peut-être réparer un seul service d’infrastructure ou restaurer l’intégralité de l’instance Azure Stack. Vous devrez peut-être même effectuer la restauration sur un autre matériel situé au même endroit ou dans un lieu différent.

Ce scénario concerne la récupération de toute votre installation en cas de défaillance, ainsi que le redéploiement du cloud privé.

| Scénario                                                           | Perte de données                            | Considérations                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Récupérer après une grave perte de données due à un sinistre ou à un bogue produit | Toutes les données relatives à l’infrastructure, à l’utilisateur et aux applications | Les applications et données utilisateur sont protégées séparément des données d’infrastructure. |

## <a name="workflows"></a>Workflows

La protection Azure Stack commence par la sauvegarde distincte des données relatives à l’infrastructure et de celles des applications/des locataires. Ce document explique comment protéger l’infrastructure. 

![Workflow de récupération des données Azure Stack — Déploiement](media/azure-stack-backup/azure-stack-backup-workflow1.png)

Dans le pire des cas, lorsque toutes les données sont perdues, la récupération Azure Stack est le processus de restauration des données de l’infrastructure propres à ce déploiement Azure Stack et de toutes les toutes les données utilisateur. 

![Workflow de récupération des données Azure Stack — Redéploiement](media/azure-stack-backup/azure-stack-backup-workflow2.png)

## <a name="restore"></a>Restore

En cas de grave perte de données sans défaillance matérielle, vous devez redéployer Azure Stack. Au cours du redéploiement, vous pouvez spécifier l’emplacement de stockage et les informations d’identification requises pour accéder aux sauvegardes. Dans ce mode, il n’est pas nécessaire de spécifier les services qui doivent être restaurés. Le contrôleur de sauvegarde d’infrastructure injecte un état de plan de contrôle dans le cadre du flux de travail de déploiement.

En cas de sinistre rendant le matériel inutilisable, le redéploiement est possible uniquement sur du nouveau matériel. Le redéploiement peut prendre plusieurs semaines dans l’attente de la commande du matériel de remplacement et de sa réception dans le centre de données. Il est possible de restaurer les données du plan de contrôle à tout moment. Toutefois, la restauration n’est pas prise en charge si la version de l’instance redéployée est ultérieure de plus d’une version à celle utilisée lors de la dernière sauvegarde.

| Mode de déploiement | Point de départ | Point de terminaison                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nouvelle installation   | Build de base | Le fabricant OEM déploie Azure Stack et procède à une mise à jour vers la version la plus récente prise en charge.                                                                                                                                          |
| Mode Récupération   | Build de base | Le fabricant OEM déploie Azure Stack en mode de récupération et gère les exigences de correspondance de version en fonction de la dernière sauvegarde disponible. Le fabricant OEM termine le déploiement par la mise à jour vers la version la plus récente prise en charge. |

## <a name="data-in-backups"></a>Données des sauvegardes

Azure Stack prend en charge un type de déploiement appelé mode de récupération cloud. Ce mode est utilisé uniquement si vous choisissez de récupérer Azure Stack suite à un sinistre ou au bogue relatif à un produit ayant rendu la solution irrécupérable. Ce mode de déploiement ne récupère aucune des données utilisateur stockées dans la solution. L’étendue de ce mode de déploiement se limite à la restauration des données suivantes :

 - Les entrées de déploiement
 - Les données du service d’identité interne (déploiements ADFS)
 - La configuration des identités fédérées (déploiements ADFS)
 - Les certificats racines utilisés par l’autorité de certification interne
 - Les données utilisateur de configuration d’Azure Resource Manager, telles que les abonnements, les plans, les offres, les quotas de stockage, les quotas réseau et les ressources de calcul
 - Les secrets et les coffres Key Vault
 - Les attributions de stratégies RBAC et les attributions de rôles

Aucune ressource Infrastructure as a Service (IaaS) ou Platform as a Service (PaaS) n’est récupérée lors du déploiement. Ces pertes incluent les machines virtuelles IaaS, les comptes de stockage, les objets blob, les tables, la configuration réseau, etc. L’objectif d’une récupération cloud est de garantir que les opérateurs et utilisateurs pourront se reconnecter au portail une fois le déploiement terminé. Les utilisateurs qui se reconnectent ne verront aucune de leurs ressources. Les abonnements des utilisateurs sont restaurés, ainsi que les plans, les offres et les stratégies d’origine définies par l’administrateur. Les utilisateurs qui se reconnectent au système sont soumis aux mêmes contraintes que celles imposées par la solution d’origine avant le sinistre. Une fois la récupération cloud effectuée, l’opérateur peut restaurer manuellement les données à forte valeur ajoutée et tierces des fournisseurs de ressources, ainsi que les données associées.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les meilleures pratiques relatives à l’[utilisation du service de sauvegarde d’infrastructure](azure-stack-backup-best-practices.md).
