---
title: Métriques prises en charge pour Azure Monitor sur Azure Stack Hub
description: Découvrez les métriques prises en charge pour Azure Monitor sur Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 50064d480c1900fdd03fd08c182427b1a77399d5
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874028"
---
# <a name="supported-metrics-for-azure-monitor-on-azure-stack-hub"></a>Métriques prises en charge pour Azure Monitor sur Azure Stack Hub

Vous pouvez récupérer vos métriques à partir d’Azure Monitor sur Azure Stack Hub de la même façon que dans Azure global. Vous pouvez créer vos métriques sur le portail, les récupérer à partir de l’API REST ou bien les interroger avec PowerShell ou l’interface de ligne de commande.

Les tableaux suivants répertorient les métriques disponibles avec le pipeline de métriques d’Azure Monitor sur Azure Stack Hub. Pour interroger ces métriques et y accéder, utilisez la version du profil de l’API datant du **01/01/2018**. Pour plus d’informations sur les profils d’API et Azure Stack Hub, consultez [Gérer les profils de version des API dans Azure Stack Hub](azure-stack-version-profiles.md).

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| Métrique | Nom d’affichage de la métrique | Unité | Type d’agrégation | Description | Dimensions |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| Percentage CPU | Percentage CPU | Pourcentage | Average | Pourcentage d’unités Compute affectées actuellement utilisées par les machines virtuelles. | Aucune dimension |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| Métrique | Nom d’affichage de la métrique | Unité | Type d’agrégation | Description | Dimensions |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Capacité utilisée | Octets | Average | Capacité utilisée du compte. | Aucune dimension |
| Transactions | Transactions | Count | Total | Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre comprend les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses. | ResponseType, GeoType, ApiName |
| Entrée | Entrée | Octets | Total | Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure. | GeoType, ApiName |
| Sortie | Sortie | Octets | Total | Quantité de données de sortie, en octets. Ce nombre comprend les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables. | GeoType, ApiName |
| SuccessServerLatency | Latence du serveur avec requête réussie | Millisecondes | Average | Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latence E2E de réussite | Millisecondes | Average | Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse. | GeoType, ApiName |
| Disponibilité | Disponibilité | Pourcentage | Average | Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. Calculez la disponibilité en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| Métrique | Nom d’affichage de la métrique | Unité | Type d’agrégation | Description | Dimensions |
|--------|---------------------|------|------------------|-------------|------------|
| BlobCapacity | Capacité d’objet blob | Octets | Total | Quantité de stockage utilisée par le service BLOB du compte de stockage, en octets. | BlobType |
| BlobCount | Nombre d’objets blob | Count | Total | Nombre d’objets blob dans le service Blob du compte de stockage. | BlobType |
| ContainerCount | Nombre de conteneurs d’objets blob | Count | Average | Nombre de conteneurs dans le service Blob du compte de stockage. | Aucune dimension |
| Transactions | Transactions | Count | Total | Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre comprend les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses. | ResponseType, GeoType, ApiName |
| Entrée | Entrée | Octets | Total | Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure. | GeoType, ApiName |
| Sortie | Sortie | Octets | Total | Quantité de données de sortie, en octets. Ce nombre comprend les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables. | GeoType, ApiName |
| SuccessServerLatency | Latence du serveur avec requête réussie | Millisecondes | Average | Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latence E2E de réussite | Millisecondes | Average | Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse. | GeoType, ApiName |
| Disponibilité | Disponibilité | Pourcentage | Average | Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. Calculez la disponibilité en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| Métrique | Nom d’affichage de la métrique | Unité | Type d’agrégation | Description | Dimensions |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Capacité de la table | Octets | Average | Quantité de stockage utilisée par le service de Table du compte de stockage, en octets. | Aucune dimension |
| TableCount | Nombre de tables | Count | Average | Nombre de tables dans le service de Table du compte de stockage. | Aucune dimension |
| TableEntityCount | Nombre d’entités de table | Count | Average | Nombre d’entités de table dans le service de Table du compte de stockage. | Aucune dimension |
| Transactions | Transactions | Count | Total | Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre comprend les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses. | ResponseType, GeoType, ApiName |
| Entrée | Entrée | Octets | Total | Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure. | GeoType, ApiName |
| Sortie | Sortie | Octets | Total | Quantité de données de sortie, en octets. Ce nombre comprend les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables. | GeoType, ApiName |
| SuccessServerLatency | Latence du serveur avec requête réussie | Millisecondes | Average | Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latence E2E de réussite | Millisecondes | Average | Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse. | GeoType, ApiName |
| Disponibilité | Disponibilité | Pourcentage | Average | Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. Calculez la disponibilité en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée. | GeoType, ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| Métrique | Nom d’affichage de la métrique | Unité | Type d’agrégation | Description | Dimensions |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Capacité de la file d’attente | Octets | Average | Quantité de stockage utilisée par le service File d’attente du compte de stockage, en octets. | Aucune dimension |
| QueueCount | Nombre de files d’attente | Count | Average | Nombre de files d’attente dans le service de File d’attente du compte de stockage. | Aucune dimension |
| QueueMessageCount | Nombre de messages dans la file d’attente | Count | Average | Nombre approximatif de messages en file d’attente dans le service File d’attente du compte de stockage. | Aucune dimension |
| Transactions | Transactions | Count | Total | Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre comprend les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. Utilisez la dimension ResponseType pour connaître le nombre des différents types de réponses. | ResponseType, GeoType, ApiName |
| Entrée | Entrée | Octets | Total | Quantité de données d’entrée, en octets. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure. | GeoType, ApiName |
| Sortie | Sortie | Octets | Total | Quantité de données de sortie, en octets. Ce nombre comprend les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables. | GeoType, ApiName |
| SuccessServerLatency | Latence du serveur avec requête réussie | Millisecondes | Average | Latence moyenne utilisée par Stockage Microsoft Azure pour traiter une requête réussie, en millisecondes. Cette valeur n’inclut pas la latence réseau spécifiée dans AverageE2ELatency. | GeoType, ApiName |
| SuccessE2ELatency | Latence E2E de réussite | Millisecondes | Average | Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée, en millisecondes. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse. | GeoType, ApiName |
| Disponibilité | Disponibilité | Pourcentage | Average | Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. Calculez la disponibilité en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris celles qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée. | GeoType, ApiName |

## <a name="next-steps"></a>Étapes suivantes

Apprenez-en davantage sur [Azure Monitor sur Azure Stack Hub](azure-stack-metrics-azure-data.md).
