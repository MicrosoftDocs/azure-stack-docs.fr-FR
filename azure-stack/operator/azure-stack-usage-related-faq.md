---
title: Questions fréquentes (FAQ) sur l’API d’utilisation
description: Liste de compteurs Azure Stack Hub, comparaison avec les API d’utilisation Azure, Heure d’utilisation et Heure du rapport, codes d’erreur.
author: sethmanheim
ms.topic: article
ms.date: 01/27/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 02/26/2019
ms.openlocfilehash: 18539c666683c6e35c8736616405f961b63adf40
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76880655"
---
# <a name="frequently-asked-questions-about-azure-stack-hub-usage"></a>Questions fréquentes sur l’utilisation d’Azure Stack Hub

Cet article répond à certaines questions fréquentes sur l’utilisation d’Azure Stack Hub et l’API d’utilisation d’Azure Stack Hub.

## <a name="what-meter-ids-can-i-see"></a>Quels ID de compteur sont visibles ?

Des rapports d’utilisation sont générés pour les fournisseurs de ressources suivants :

### <a name="network"></a>Réseau
  
**ID du compteur** : F271A8A388C44D93956A063E1D2FA80B  
**Nom du compteur** : Static IP Address Usage  
**Unité** : Adresses IP  
**Remarques**: Nombre d’adresses IP utilisées. Si vous appelez l’API d’utilisation avec une granularité journalière, le compteur retourne l’adresse IP multipliée par le nombre d’heures.  
  
**ID du compteur** : 9E2739BA86744796B465F64674B822BA  
**Nom du compteur** : Dynamic IP Address Usage  
**Unité** : Adresses IP  
**Remarques**: Nombre d’adresses IP utilisées. Si vous appelez l’API d’utilisation avec une granularité journalière, le compteur retourne l’adresse IP multipliée par le nombre d’heures.  
  
### <a name="storage"></a>Stockage
  
**ID du compteur** : B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Nom du compteur** : TableCapacity  
**Unité** : Go\*heures  
**Remarques**: Capacité totale consommée par table.  
  
**ID du compteur** : B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Nom du compteur** : PageBlobCapacity  
**Unité** : Go\*heures  
**Remarques**: Capacité totale consommée par objet blob de pages.  
  
**ID du compteur** : B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Nom du compteur** : QueueCapacity  
**Unité** : Go\*heures  
**Remarques**: Capacité totale consommée par file d’attente.  
  
**ID du compteur** : 09F8879E-87E9-4305-A572-4B7BE209F857  
**Nom du compteur** : BlockBlobCapacity  
**Unité** : Go\*heures  
**Remarques**: Capacité totale consommée par objet blob de blocs.  
  
**ID du compteur** : B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Nom du compteur** : TableTransactions  
**Unité** : Nombre de requêtes en 10 000 s  
**Remarques**: Requêtes de service de Table (par 10 000).  
  
**ID du compteur** : 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Nom du compteur** : TableDataTransIn  
**Unité** : Données en entrée, en Go  
**Remarques**: Entrée de données de service de Table, en Go.  
  
**ID du compteur** : 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Nom du compteur** : TableDataTransOut  
**Unité** : Sortie en Go  
**Remarques**: Sortie de données de service de Table, en Go.
  
**ID du compteur** : 43DAF82B-4618-444A-B994-40C23F7CD438  
**Nom du compteur** : BlobTransactions  
**Unité** : Nombre de requêtes en 10 000 s  
**Remarques**: Demandes de service BLOB (par 10 000).  
  
**ID du compteur** : 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Nom du compteur** : BlobDataTransIn  
**Unité** : Données en entrée, en Go  
**Remarques**: Entrée de données de service BLOB, en Go.  
  
**ID du compteur** : 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Nom du compteur** : BlobDataTransOut  
**Unité** : Sortie en Go  
**Remarques**: Sortie de données de service BLOB, en Go.  
  
**ID du compteur** : EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Nom du compteur** : QueueTransactions  
**Unité** : Nombre de requêtes en 10 000 s  
**Remarques**: Demandes de service de File d’attente (par 10 000).  
  
**ID du compteur** : E518E809-E369-4A45-9274-2017B29FFF25  
**Nom du compteur** : QueueDataTransIn  
**Unité** : Données en entrée, en Go  
**Remarques**: Entrée de données de service de File d’attente, en Go.  
  
**ID du compteur** : DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Nom du compteur** : QueueDataTransOut  
**Unité** : Sortie en Go  
**Remarques**: Sortie de données de service de File d’attente, en Go  

### <a name="compute"></a>Calcul
  
**ID du compteur** : FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Nom du compteur** : Base VM Size Hours  
**Unité** : Heures cœur virtuel  
**Remarques**: Nombre de cœurs virtuels multiplié par les heures d’exécution de la machine virtuelle.  
  
**ID du compteur** : 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Nom du compteur** : Windows VM Size Hours  
**Unité** : Heures cœur virtuel  
**Remarques**: Nombre de cœurs virtuels multiplié par les heures d’exécution de la machine virtuelle.  
  
**ID du compteur** : 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Nom du compteur** : VM size hours  
**Unité** : Heures de machine virtuelle  
**Remarques**: Capture à la fois la machine virtuelle de base et la machine virtuelle Windows. Ne s’ajuste pas en fonction des cœurs.  
  
### <a name="managed-disks"></a>Disques managés

**ID du compteur** : 380874f9-300c-48e0-95a0-d2d9a21ade8f   
**Nom du compteur** : S4   
**Unité** : nombre de disques\*mois   
**Remarques**: Disque managé Standard (32 Go) 

**ID du compteur** : 1b77d90f-427b-4435-b4f1-d78adec53222   
**Nom du compteur** : S6   
**Unité** : nombre de disques\*mois   
**Remarques**: Disque managé Standard (64 Go) 

**ID du compteur** : d5f7731b-f639-404a-89d0-e46186e22c8d   
**Nom du compteur** : S10   
**Unité** : nombre de disques\*mois   
**Remarques**: Disque managé Standard (128 Go) 

**ID du compteur** : ff85ef31-da5b-4eac-95dd-a69d6f97b18a   
**Nom du compteur** : S15   
**Unité** : nombre de disques\*mois   
**Remarques**: Disque managé Standard (256 Go) 

**ID du compteur** : 88ea9228-457a-4091-adc9-ad5194f30b6e   
**Nom du compteur** : S20   
**Unité** : nombre de disques\*mois      
**Remarques**: Disque managé Standard (512 Go) 

**ID du compteur** : 5b1db88a-8596-4002-8052-347947c26940   
**Nom du compteur** : S30   
**Unité** : nombre de disques\*mois   
**Remarques**: Disque managé Standard (1024 Go) 

**ID du compteur** : 7660b45b-b29d-49cb-b816-59f30fbab011   
**Nom du compteur** : P4   
**Unité** : nombre de disques\*mois   
**Remarques**: Disque managé Premium (32 Go) 

**ID du compteur** : 817007fd-a077-477f-bc01-b876f27205fd   
**Nom du compteur** : P6   
**Unité** : nombre de disques\*mois   
**Remarques**: Disque managé Premium (64 Go) 

**ID du compteur** : e554b6bc-96cd-4938-a5b5-0da990278519   
**Nom du compteur** : P10   
**Unité** : nombre de disques\*mois   
**Remarques**: Disque managé Premium (128 Go)  

**ID du compteur** : cdc0f53a-62a9-4472-a06c-e99a23b02907   
**Nom du compteur** : P15  
**Unité** : nombre de disques\*mois   
**Remarques**: Disque managé Premium (256 Go) 

**ID du compteur** : b9cb2d1a-84c2-4275-aa8b-70d2145d59aa   
**Nom du compteur** : P20   
**Unité** : nombre de disques\*mois   
**Remarques**: Disque managé Premium (512 Go) 

**ID du compteur** : 06bde724-9f94-43c0-84c3-d0fc54538369   
**Nom du compteur** : P30   
**Unité** : nombre de disques\*mois   
**Remarques**: Disque managé Premium (1024 Go) 

**ID du compteur** : 7ba084ec-ef9c-4d64-a179-7732c6cb5e28   
**Nom du compteur** : ActualStandardDiskSize   
**Unité** : Go\*mois      
**Remarques**: taille réelle du disque managé Standard  

**ID du compteur** : daef389a-06e5-4684-a7f7-8813d9f792d5  
**Nom du compteur** : ActualPremiumDiskSize   
**Unité** : Go\*mois      
**Remarques**: taille réelle du disque managé Premium 

**ID du compteur** : 108fa95b-be0d-4cd9-96e8-5b0d59505df1  
**Nom du compteur** : ActualStandardSnapshotSize   
**Unité** : Go\*mois   
**Remarques**: taille réelle de la capture instantanée sur le disque managé Standard.  

**ID du compteur** : 578ae51d-4ef9-42f9-85ae-42b52d3d83ac   
**Nom du compteur** : ActualPremiumSnapshotSize   
**Unité** : Go\*mois   
**Remarques**: Taille réelle de la capture instantanée sur le disque managé Premium.   

**ID du compteur** : 5d76e09f-4567-452a-94cc-7d1f097761f0   
**Nom du compteur** : S4   
**Unité** : nombre de disques\*heures   
**Remarques**: Disque managé Standard (32 Go) - Déprécié 

**ID du compteur** : dc9fc6a9-0782-432a-b8dc-978130457494   
**Nom du compteur** : S6   
**Unité** : nombre de disques\*heures   
**Remarques**: Disque managé Standard (64 Go) - Déprécié 

**ID du compteur** : e5572fce-9f58-49d7-840c-b168c0f01fff   
**Nom du compteur** : S10   
**Unité** : nombre de disques\*heures   
**Remarques**: Disque managé Standard (128 Go) - Déprécié 

**ID du compteur** : 9a8caedd-1195-4cd5-80b4-a4c22f9302b8   
**Nom du compteur** : S15   
**Unité** : nombre de disques\*heures   
**Remarques**: Disque managé Standard (256 Go) - Déprécié 

**ID du compteur** : 5938f8da-0ecd-4c48-8d5a-c7c6c23546be   
**Nom du compteur** : S20   
**Unité** : nombre de disques\*heures      
**Remarques**: Disque managé Standard (512 Go) - Déprécié 

**ID du compteur** : 7705a158-bd8b-4b2b-b4c2-0782343b81e6   
**Nom du compteur** : S30   
**Unité** : nombre de disques\*heures   
**Remarques**: Disque managé Standard (1024 Go) - Déprécié 

**ID du compteur** : 5c105f5f-cbdf-435c-b49b-3c7174856dcc   
**Nom du compteur** : P4   
**Unité** : nombre de disques\*heures   
**Remarques**: Disque managé Premium (32 Go) - Déprécié 

**ID du compteur** : 518b412b-1927-4f25-985f-4aea24e55c4f   
**Nom du compteur** : P6   
**Unité** : nombre de disques\*heures   
**Remarques**: Disque managé Premium (64 Go) - Déprécié 

**ID du compteur** : 5cfb1fed-0902-49e3-8217-9add946fd624   
**Nom du compteur** : P10   
**Unité** : nombre de disques\*heures   
**Remarques**: Disque managé Premium (128 Go) - Déprécié  

**ID du compteur** : 8de91c94-f740-4d9a-b665-bd5974fa08d4   
**Nom du compteur** : P15  
**Unité** : nombre de disques\*heures   
**Remarques**: Disque managé Premium (256 Go) - Déprécié 

**ID du compteur** : c7e7839c-293b-4761-ae4c-848eda91130b   
**Nom du compteur** : P20   
**Unité** : nombre de disques\*heures   
**Remarques**: Disque managé Premium (512 Go) - Déprécié 

**ID du compteur** : 9f502103-adf4-4488-b494-456c95d23a9f   
**Nom du compteur** : P30   
**Unité** : nombre de disques\*heures   
**Remarques**: Disque managé Premium (1024 Go) - Déprécié 

**ID du compteur** : 8a409390-1913-40ae-917b-08d0f16f3c38   
**Nom du compteur** : ActualStandardDiskSize   
**Unité** : octets\*heures      
**Remarques**: taille réelle du disque managé Standard - Déconseillé  

**ID du compteur** : 1273b16f-8458-4c34-8ce2-a515de551ef6  
**Nom du compteur** : ActualPremiumDiskSize   
**Unité** : octets\*heures      
**Remarques**: taille réelle du disque managé Premium - Déconseillé 

**ID du compteur** : 89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**Nom du compteur** : ActualStandardSnapshotSize   
**Unité** : octets\*heures   
**Remarques**: taille réelle de la capture instantanée sur le disque managé Standard - Déconseillé 

**ID du compteur** : 95b0c03f-8a82-4524-8961-ccfbf575f536   
**Nom du compteur** : ActualPremiumSnapshotSize   
**Unité** : octets\*heures   
**Remarques**: Taille réelle de la capture instantanée sur le disque managé Premium (déconseillé) 

**ID du compteur** : 75d4b707-1027-4403-9986-6ec7c05579c8 **Nom du compteur** : ActualStandardSnapshotSize **Unité** : Go\*mois **Remarques** : taille réelle de la capture instantanée sur le disque managé Standard - Déconseillé  

**ID du compteur** : 5ca1cbb9-6f14-4e76-8be8-1ca91547965e **Nom du compteur** : ActualPremiumSnapshotSize **Unité** : Go\*mois **Remarques** : Taille réelle de la capture instantanée sur le disque managé Premium (déconseillé)  

### <a name="sql-rp"></a>Sql RP
  
**ID du compteur** : CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Nom du compteur** : DatabaseSizeHourSqlMeter  
**Unité** : Mo\*heures  
**Remarques**: Capacité totale de la base de données à sa création. Si vous appelez l’API d’utilisation avec une granularité journalière, le compteur retourne les Mo multipliés par le nombre d’heures.  
  
### <a name="mysql-rp"></a>MySql RP   
  
**ID du compteur** : E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Nom du compteur** : DatabaseSizeHourMySqlMeter  
**Unité** : Mo\*heures  
**Remarques**: Capacité totale de la base de données à sa création. Si vous appelez l’API d’utilisation avec une granularité journalière, le compteur retourne les Mo multipliés par le nombre d’heures.    
### <a name="key-vault"></a>Key Vault   
  
**ID du compteur** : EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Nom du compteur** : Key Vault transactions  
**Unité** : Nombre de requêtes en 10 000 s  
**Remarques**: Nombre de requêtes d’API REST reçues par le plan de données Key Vault.  
  
**ID du compteur** : 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Nom du compteur** : Transactions de clés avancées  
**Unité** :  10 000 transactions  
**Remarques**: RSA 3K/4K, transactions de clés ECC (préversion).  
  
### <a name="app-service"></a>App Service   
  
**ID du compteur** : 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Nom du compteur** : App Service  
**Unité** : Heures cœur virtuel  
**Remarques**: Nombre de cœurs virtuels utilisés pour exécuter App Service. Remarque : Microsoft utilise ce compteur pour facturer App Service sur Azure Stack Hub. Les fournisseurs de solutions cloud peuvent utiliser les autres compteurs App Service (ci-dessous) pour calculer l’utilisation de leurs locataires.  
  
**ID du compteur** : 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Nom du compteur** : Demandes de fonctions  
**Unité** : 10 requêtes  
**Remarques**: Nombre total d’exécutions demandées (pour 10 exécutions). Une exécution est comptée chaque fois qu’une fonction s’exécute en réponse à un événement ou est déclenchée par une liaison.  
  
**ID du compteur** : D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Nom du compteur** : Fonctions – Calcul  
**Unité** :  Go/s  
**Remarques**:  Consommation de ressources en gigaoctets/seconde (Go/s). La **consommation de ressources constatée** est calculée en multipliant la taille moyenne de la mémoire en Go par la durée en millisecondes nécessaire à l’exécution de la fonction. La mémoire utilisée par une fonction est mesurée en arrondissant aux 128 Mo les plus proches, pour une mémoire maximale de 1 536 Mo, le temps d’exécution étant calculé en arrondissant à la milliseconde la plus proche. Le délai d’exécution et la mémoire minimum pour une seule exécution de fonction sont respectivement de 100 ms et de 128 Mo.  
  
**ID du compteur** : 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Nom du compteur** : Heures partagés App Service  
**Unité** : 1 heure  
**Remarques**: Par heure d’utilisation du plan App Service partagé. Les plans sont facturés par application.  
  
**ID du compteur** : 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Nom du compteur** : Heures Free App Service  
**Unité** : 1 heure  
**Remarques**: Par heure d’utilisation du plan App Service gratuit. Les plans sont facturés par application.  
  
**ID du compteur** : 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Nom du compteur** : Heures réduites App Service Standard  
**Unité** : 1 heure  
**Remarques**: Le calcul dépend de la taille et du nombre d’instances.  
  
**ID du compteur** : 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Nom du compteur** : Heures moyennes App Service Standard  
**Unité** : 1 heure  
**Remarques**: Le calcul dépend de la taille et du nombre d’instances.  
  
**ID du compteur** : 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Nom du compteur** : Heures nombreuses App Service Standard  
**Unité** : 1 heure  
**Remarques**: Le calcul dépend de la taille et du nombre d’instances.  
  
### <a name="custom-worker-tiers"></a>Niveaux de travail personnalisés   
  
**ID du compteur** : *Niveaux de travail personnalisés*  
**Nom du compteur** : Niveaux de travail personnalisés  
**Unité** : Heures  
**Remarques**: L’identifiant de compteur déterministe est créé en fonction de la référence SKU et du nom du niveau de travail personnalisé. Il est unique pour chaque niveau de travail personnalisé.  
  
**ID du compteur** : 264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**Nom du compteur** : SNI SSL  
**Unité** : Par liaison SNI SSL  
**Remarques**: App Service prend en charge deux types de connexion SSL : connexions SSL avec Indication du nom du serveur (SNI) et connexions SSL avec Adresse IP. Les connexions SSL basées sur SNI fonctionnent sur les navigateurs modernes, alors que les connexions SSL avec Adresse IP fonctionnent sur tous les navigateurs.  
  
**ID du compteur** : 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Nom du compteur** : IP SSL  
**Unité** : Par liaison SSL basée sur IP  
**Remarques**: App Service prend en charge deux types de connexion SSL : connexions SSL avec Indication du nom du serveur (SNI) et connexions SSL avec Adresse IP. Les connexions SSL basées sur SNI fonctionnent sur les navigateurs modernes, alors que les connexions SSL avec Adresse IP fonctionnent sur tous les navigateurs.  
  
**ID du compteur** : 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Nom du compteur** :  Traitement Web  
**Unité** :  
**Remarques**: Calculé par site actif par heure.  
  
**ID du compteur** : 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Nom du compteur** : Bande passante de sortie externe  
**Unité** : Go  
**Remarques**: Total d’octets de la réponse à la demande entrante + total d’octets de la demande sortante + total d’octets de la réponse à la demande FTP entrante + total d’octets de la réponse à la demande de déploiement web entrante.  
  
## <a name="how-do-the-azure-stack-hub-usage-apis-compare-to-the-azure-usage-apihttpsdocsmicrosoftcomazurebillingbilling-usage-rate-card-overviewazure-resource-usage-api-preview-currently-in-public-preview"></a>En quoi les API d’utilisation d’Azure Stack Hub sont-elles comparables aux [API d’utilisation d’Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-usage-api-preview) (actuellement en préversion publique) ?

* L’API d’utilisation du locataire est cohérente avec l’API Azure, à une exception près : l’indicateur *showDetails* n’est actuellement pas pris en charge dans Azure Stack Hub.
* L’API d’utilisation du fournisseur s’applique uniquement à Azure Stack Hub.
* Actuellement, l’[API RateCard](/azure/billing/billing-usage-rate-card-overview#azure-resource-ratecard-api-preview) qui est disponible dans Azure n’est pas disponible dans Azure Stack Hub.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Quelle est la différence entre l’Heure d’utilisation et l’Heure du rapport ?

Les rapports de données d’utilisation comportent deux valeurs de durée principales :

* **Heure du rapport**. Heure à laquelle l’événement d’utilisation est entré dans le système d’utilisation.
* **Heure d’utilisation**. Heure à laquelle la ressource Azure Stack Hub a été consommée.

Vous pouvez voir une différence entre l’heure d’utilisation et l’heure du rapport pour un événement d’utilisation spécifique. Ce décalage peut atteindre plusieurs heures dans n’importe quel environnement.

Actuellement, vous pouvez interroger uniquement par **Heure du rapport**.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Que signifient les codes d’erreur de l’API d’utilisation suivants ?

| **Code d’état HTTP** | **Code d’erreur** | **Description** |
| --- | --- | --- |
| 400 - Demande incorrecte |*NoApiVersion* |Le paramètre de requête *api-version* est manquant. |
| 400 - Demande incorrecte |*InvalidProperty* |Une propriété est manquante ou a une valeur non valide. Le message indiqué dans le code d’erreur du corps de la réponse identifie la propriété manquante. |
| 400 - Demande incorrecte |*RequestEndTimeIsInFuture* |La valeur *ReportedEndTime* est dans le futur. Les valeurs dans le futur ne sont pas autorisées pour cet argument. |
| 400 - Demande incorrecte |*SubscriberIdIsNotDirectTenant* |Un appel d’API de fournisseur a utilisé un ID d’abonnement qui n’est pas un locataire valide de l’appelant. |
| 400 - Demande incorrecte |*SubscriptionIdMissingInRequest* |L’ID d’abonnement de l’appelant est manquant. |
| 400 - Demande incorrecte |*InvalidAggregationGranularity* |Une granularité d’agrégation non valide a été demandée. Les valeurs valides sont « quotidienne » et « horaire ». |
| 503 |*ServiceUnavailable* |Une erreur renouvelable s’est produite, car le service est occupé ou l’appel est limité. |

## <a name="what-is-the-policy-for-charging-for-vms"></a>Quelle est la stratégie de facturation pour les machines virtuelles ?

Les machines virtuelles en cours d’exécution ou arrêtés génèrent des données d’utilisation. De manière cohérente avec Azure, la désallocation est nécessaire pour arrêter l’émission de données d’utilisation. Si le portail n’est pas disponible mais que le fournisseur de ressources de calcul est en cours d’exécution, une utilisation est émise.

## <a name="how-do-i-extract-usage-data-from-the-azure-stack-hub-usage-apis"></a>Comment puis-je extraire les données d’utilisation des API d’utilisation d’Azure Stack Hub ?

La façon la plus simple d’extraire les données d’utilisation des API d’utilisation locale Azure Stack Hub consiste à utiliser le [script de récapitulatif d’utilisation sur GitHub](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/Usagesummary.ps1). Le script nécessite les dates de début et de fin comme paramètres d’entrée.

Vous pouvez également utiliser les API REST, comme expliqué dans les articles [API Utilisation des ressources de fournisseur](azure-stack-provider-resource-api.md) et [API Utilisation des ressources de client](azure-stack-tenant-resource-usage-api.md).

## <a name="how-can-i-associate-usage-extracted-from-azure-usage-apis-to-a-specific-azure-stack-hub-user-subscription"></a>Comment puis-je associer l’utilisation extraite des API d’utilisation d’Azure à un abonnement utilisateur Azure Stack Hub spécifique ?

Les enregistrements d’utilisation incluent un jeu de propriétés appelé **additionalinfo**, qui inclut l’identifiant d’abonnement Azure Stack Hub. Il s'agit de l'abonnement utilisateur émettant l'enregistrement d'utilisation correspondant.

## <a name="next-steps"></a>Étapes suivantes

* [Facturation des clients et facturation interne dans Azure Stack Hub](azure-stack-billing-and-chargeback.md)
* [API Utilisation des ressources de fournisseur](azure-stack-provider-resource-api.md)
* [API d’utilisation des ressources de locataire](azure-stack-tenant-resource-usage-api.md)
