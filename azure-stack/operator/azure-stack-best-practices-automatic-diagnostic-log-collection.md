---
title: Meilleures pratiques pour la collecte automatique des journaux Azure Stack Hub
description: Meilleures pratiques pour la collecte automatique des journaux dans Azure Stack Hub Aide + support
author: justinha
ms.topic: article
ms.date: 07/25/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 56af62717b4cd32d7a5130d1d324bd40bf61adb9
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76878426"
---
# <a name="best-practices-for-automatic-azure-stack-hub-log-collection"></a>Meilleures pratiques pour la collecte automatique des journaux Azure Stack Hub 

Cette rubrique décrit les meilleures pratiques pour la gestion de la collecte automatique des journaux de diagnostic pour Azure Stack Hub. 

## <a name="collecting-logs-from-multiple-azure-stack-hub-systems"></a>Collecte des journaux de plusieurs systèmes Azure Stack Hub

Configurez un conteneur d’objets blob pour chaque unité d’échelle Azure Stack Hub dont vous souhaitez collecter les journaux. Pour plus d’informations sur la configuration du conteneur d’objets blob, consultez [Configurer la collecte automatique des journaux de diagnostic Azure Stack Hub](azure-stack-configure-automatic-diagnostic-log-collection.md). Il est recommandé d’enregistrer uniquement les journaux de diagnostic de la même unité d’échelle Azure Stack Hub dans un seul conteneur d’objets blob. 

## <a name="retention-policy"></a>Stratégie de rétention

Créez une [règle de gestion du cycle de vie](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) du stockage d’objets blob Azure pour gérer la stratégie de rétention des journaux. Nous vous suggérons de conserver les journaux de diagnostic pendant 30 jours. Pour créer une règle de gestion du cycle de vie dans le stockage Azure, connectez-vous au portail Azure, cliquez sur **Comptes de stockage**, cliquez sur le conteneur d’objets blob, puis, sous **Service BLOB**, cliquez sur **Gestion du cycle de vie**.

![Capture d’écran montrant la gestion du cycle de vie dans le Portail Azure](media/azure-stack-automatic-log-collection/blob-storage-lifecycle-management.png)


## <a name="sas-token-expiration"></a>Expiration du jeton SAS

Configurez l’expiration de l’URL SAS sur deux ans. Si vous renouvelez vos clés de compte de stockage, veillez à régénérer l’URL SAS. Vous devez gérer le jeton SAS conformément aux meilleures pratiques. Pour plus d’informations, consultez [Meilleures pratiques SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#best-practices-when-using-sas).


## <a name="bandwidth-consumption"></a>Consommation de bande passante

La taille moyenne de la collecte des journaux de diagnostic varie selon que la collecte des journaux est à la demande ou automatique. 

Pour la collecte de journaux à la demande, la taille de la collecte des journaux dépend du nombre d’heures collectées. Vous pouvez choisir une fenêtre de 1à 4 heures sur les sept derniers jours. 

Lorsque la collecte automatique des journaux de diagnostic est activée, le service surveille les alertes critiques. Dès qu’une alerte critique est déclenchée et persiste pendant environ 30 minutes, le service collecte et charge les journaux correspondants. La taille de la collecte de journaux est d’environ 2 Go en moyenne. Dans le cas de l’échec d’un correctif et d’une mise à jour, la collecte automatique des journaux ne démarre que si une alerte critique est générée et persiste pendant environ 30 minutes. Nous vous recommandons de suivre les [instructions relatives à la surveillance des correctifs et des mises à jour](azure-stack-updates.md).
L’analyse des alertes, la collecte des journaux et le téléchargement sont transparents pour l’utilisateur. 



Sur un système sain, les journaux ne seront pas collectés du tout. Dans un système défectueux, la collecte des journaux peut s’exécuter deux ou trois fois par jour, mais en général une seule fois. Au plus, il peut s’exécuter jusqu’à dix fois par jour dans le pire des cas.  

Le tableau suivant peut aider les environnements avec des connexions limitées à Azure à tenir compte de l’impact de l’activation de la collecte automatique des journaux.

| Connexion réseau | Impact |
|--------------------|--------|
| Connexion à faible bande passante/latence élevée | Le chargement du journal va prendre un certain temps. | 
| Connexion partagée | Le chargement peut également avoir un impact sur d’autres applications/utilisateurs partageant la connexion réseau. |
| Connexion limitée | Des frais supplémentaires peuvent être facturés par votre fournisseur de services Internet pour l’utilisation supplémentaire du réseau. |


## <a name="managing-costs"></a>Gestion des coûts

Les [frais liés au stockage d’objets blob](https://azure.microsoft.com/pricing/details/storage/blobs/) Azure dépendent de la quantité de données enregistrées tous les mois et d’autres facteurs tels que la redondance des données. Si vous n’avez pas de compte de stockage, vous pouvez vous connecter au portail Azure, cliquer sur **Comptes de stockage** et suivre les étapes pour [créer une URL SAS de conteneur d’objets blob Azure](azure-stack-configure-automatic-diagnostic-log-collection.md).

Il est recommandé de créer une [stratégie de gestion du cycle de vie](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) du stockage d’objets blob Azure pour réduire les coûts de stockage. Pour plus d’informations sur la configuration du compte de stockage, consultez [Configurer la collecte automatique des journaux de diagnostic Azure Stack Hub](azure-stack-configure-automatic-diagnostic-log-collection.md).

## <a name="see-also"></a>Voir aussi

[Configurer la collecte automatique des journaux Azure Stack Hub](azure-stack-best-practices-automatic-diagnostic-log-collection.md)

