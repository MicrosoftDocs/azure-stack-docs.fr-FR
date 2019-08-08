---
title: DNS dans Azure Stack | Microsoft Docs
description: En savoir plus sur le DNS dans Azure Stack et sur la création et la gestion des zones DNS.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2019
ms.author: sethm
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 8bfe15ad19e4aaec45492aa98cfb2ef02294742a
ms.sourcegitcommit: b3dac698f2e1834491c2f9af56a80e95654f11f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68658486"
---
# <a name="use-dns-in-azure-stack"></a>Utilisation de DNS dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Azure Stack prend en charge les fonctionnalités Azure DNS suivantes :

* Résolution de nom d’hôte DNS.
* Créer et gérer des enregistrements et des zones DNS à l’aide de l’API.

## <a name="support-for-dns-hostname-resolution"></a>Prise en charge de la résolution de nom d’hôte DNS

Vous pouvez spécifier une étiquette de nom de domaine DNS pour les ressources de l’adresse IP publique. Azure Stack utilise **domainnamelabel.location.cloudapp.azurestack.external** comme nom d’étiquette et mappe ce nom à l’adresse IP publique dans les serveurs DNS gérés par Azure Stack.

Par exemple, si vous créez une ressource d’adresse IP publique avec **contoso** comme étiquette de nom de domaine à l’emplacement Azure Stack local, le [nom de domaine complet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) **contoso.local.cloudapp.azurestack.external** est résolu en adresse IP publique de la ressource. Vous pouvez utiliser ce nom de domaine complet pour créer un enregistrement CNAME de domaine personnalisé qui pointe vers l’adresse IP publique dans Azure Stack.

Pour en savoir plus sur la résolution de noms, consultez l’article [Résolution DNS](/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT]
> Chaque étiquette de nom de domaine que vous créez doit être unique dans son emplacement Azure Stack.

La capture de l’écran suivante montre la boîte de dialogue **Créer une adresse IP publique** pour créer une adresse IP publique à l’aide du portail :

![Création d’une adresse IP publique](media/azure-stack-dns/image01.png)

### <a name="example-scenario"></a>Exemple de scénario

Vous pouvez avoir un équilibreur de charge traitant des requêtes à partir d’une application web. Derrière l’équilibreur de charge se trouve un site web s’exécutant sur une ou plusieurs machines virtuelles. Vous pouvez accéder au site web avec équilibrage de charge à l’aide d’un nom DNS, au lieu d’une adresse IP.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>Créer et gérer des enregistrements et des zones DNS à l’aide de l’API

Vous pouvez créer et gérer des enregistrements et des zones DNS dans Azure Stack.

Azure Stack fournit un service DNS similaire à celui d’Azure, à l’aide d’API qui sont cohérentes avec les API DNS Azure.  En hébergeant vos domaines dans le système DNS Azure Stack, vous pouvez gérer vos enregistrements DNS à l’aide des mêmes informations d’identification, API et outils. Vous pouvez également utiliser la même facturation et le même support que vos autres services Azure.

L’infrastructure DNS Azure Stack est plus compacte que celle d’Azure. La taille et l’emplacement d’un déploiement Azure Stack affectent l’étendue, l’échelle et les performances DNS. Cela signifie également que les performances, la disponibilité, la distribution mondiale et la haute disponibilité peuvent varier d’un déploiement à l’autre.

## <a name="comparison-with-azure-dns"></a>Comparaison avec le système DNS Azure

Le système DNS dans Azure Stack est semblable au système DNS dans Azure, mais présente quelques exceptions importantes :

* **Il ne prend pas en charge les enregistrements AAAA** : Azure Stack ne prend pas en charge les enregistrements AAAA, car il ne prend pas en charge les adresses IPv6. Il s’agit d’une différence essentielle entre les systèmes DNS Azure et Azure Stack.

* **Il n’est pas multi-locataire** : Le service DNS dans Azure Stack n’est pas multilocataire. Les locataires ne peuvent pas créer la même zone DNS. Seul le premier abonnement qui tente de créer la zone y parvient, et les requêtes suivantes échouent. Il s’agit d’une autre grande différence entre les systèmes DNS Azure et Azure Stack.

* **Balises, métadonnées et ETags** : Il existe des différences mineures dans la façon dont Azure Stack gère les balises, les métadonnées, les ETags et les limites.

Pour en savoir plus sur le système Azure DNS, consultez [Enregistrements et zones DNS](/azure/dns/dns-zones-records).

### <a name="tags"></a>Balises

Le système DNS Azure Stack prend en charge l’utilisation de balises Azure Resource Manager sur des ressources de zone DNS. Il ne prend pas en charge les balises sur les jeux d’enregistrements DNS. Par contre, les **métadonnées** sont prises en charge sur les jeux d’enregistrements DNS, comme expliqué dans la prochaine section.

### <a name="metadata"></a>Métadonnées

À la place de balises de jeu d’enregistrements, le système DNS Azure Stack prend en charge l’annotation des jeux d’enregistrements à l’aide de *métadonnées*. Comme des balises, les métadonnées permettent d’associer des paires nom-valeur à chaque jeu d’enregistrements. Les métadonnées peuvent être utiles par exemple pour indiquer l’objectif de chaque jeu d’enregistrements. Contrairement aux balises, vous ne pouvez pas utiliser les métadonnées pour produire une vue filtrée de votre facture Azure. Et vous ne pouvez pas non plus les spécifier dans une stratégie Azure Resource Manager.

### <a name="etags"></a>Etags

Supposons que deux personnes ou deux processus tentent de modifier un enregistrement DNS en même temps. Lequel gagne ? Et le gagnant sait-il qu’il a remplacé les modifications créées par quelqu’un d’autre ?

Le système DNS Azure Stack utilise des *ETags* pour gérer les modifications simultanées de la même ressource en toute sécurité. Les ETags sont différents des *Balises* Azure Resource Manager. Chaque ressource DNS (zone ou jeu d’enregistrements) est associée à un Etag. Chaque fois qu’une ressource est récupérée, son ETag l’est également. Lorsque vous mettez à jour une ressource, vous pouvez choisir de retransmettre l’ETag pour que le système DNS Azure Stack vérifie que l’ETag qui se trouve sur le serveur correspond. Étant donné que chaque mise à jour d’une ressource entraîne la régénération de l’Etag, l’absence de concordance entre les Etags indique qu’une modification simultanée a eu lieu. Les Etags sont également utilisés lorsque vous créez une ressource pour vous assurer que cette ressource n’existe pas déjà.

Par défaut, les applets de commandes PowerShell dans le système DNS Azure Stack utilisent des ETags pour bloquer les modifications simultanées apportées à des zones et des jeux d’enregistrements. Le commutateur facultatif `-Overwrite` permet de supprimer les vérifications Etag. Sans contrôles Etag, les modifications simultanées qui se sont produites sont remplacées.

Au niveau de l’API REST du système DNS Azure Stack, les ETags sont spécifiés à l’aide d’en-têtes HTTP. Leur comportement est décrit dans le tableau suivant :

| En-tête | Comportement|
|--------|---------|
| Aucun   | PUT réussit toujours (aucune vérification Etag).|
| If-match| PUT ne réussit que si la ressource existe et que l’Etag correspond.|
| If-match *| PUT réussit seulement si la ressource existe.|
| If-none-match *| PUT réussit seulement si la ressource n’existe pas.|

### <a name="limits"></a>limites

Les limites par défaut suivantes s’appliquent lors de l’utilisation du système DNS Azure Stack :

| Ressource| Limite par défaut|
|---------|--------------|
| Zones par abonnement| 100|
| Jeux d’enregistrements par zone| 5 000|
| Enregistrements par jeu d’enregistrements| 20|

## <a name="next-steps"></a>Étapes suivantes

* [Introduction aux noms de domaine internationaux (IDN) pour Azure Stack](azure-stack-understanding-dns.md)
