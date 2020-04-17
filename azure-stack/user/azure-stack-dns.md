---
title: DNS dans Azure Stack Hub
description: Apprenez-en davantage sur le DNS dans Azure Stack Hub, ainsi que sur la création et la gestion des zones DNS.
author: sethmanheim
ms.topic: article
ms.date: 01/24/2020
ms.author: sethm
ms.lastreviewed: 01/05/2020
ms.openlocfilehash: b9afba6322b80d487f4ba7d4324adcd42a8fd85a
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77703007"
---
# <a name="use-dns-in-azure-stack-hub"></a>Utiliser DNS dans Azure Stack Hub

Azure Stack Hub prend en charge les fonctionnalités Azure DNS suivantes :

* Résolution de nom d’hôte DNS.
* Créer et gérer des enregistrements et des zones DNS à l’aide de l’API.

## <a name="support-for-dns-hostname-resolution"></a>Prise en charge de la résolution de nom d’hôte DNS

Vous pouvez spécifier une étiquette de nom de domaine DNS pour les ressources de l’adresse IP publique. Azure Stack Hub utilise **domainnamelabel.location.cloudapp.azurestack.external** comme nom d’étiquette, et le mappe à l’adresse IP publique dans les serveurs DNS gérés par Azure Stack Hub.

Par exemple, si vous créez une ressource d’adresse IP publique avec **contoso** comme étiquette de nom de domaine à l’emplacement Azure Stack Hub local, le [nom de domaine complet (FQDN)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) **contoso.local.cloudapp.azurestack.external** est résolu en l’adresse IP publique de la ressource. Vous pouvez utiliser ce FQDN pour créer un enregistrement CNAME de domaine personnalisé pointant vers l’adresse IP publique dans Azure Stack Hub.

Pour en savoir plus sur la résolution de noms, consultez l’article [Résolution DNS](/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT]
> Chaque étiquette de nom de domaine que vous créez doit être unique dans son emplacement Azure Stack Hub.

La capture de l’écran suivante montre la boîte de dialogue **Créer une adresse IP publique** pour créer une adresse IP publique à l’aide du portail :

![Création d’une adresse IP publique](media/azure-stack-dns/image01.png)

### <a name="example-scenario"></a>Exemple de scénario

Vous pouvez avoir un équilibreur de charge traitant des requêtes à partir d’une application web. Derrière l’équilibreur de charge se trouve un site web s’exécutant sur une ou plusieurs machines virtuelles. Vous pouvez accéder au site web avec équilibrage de charge à l’aide d’un nom DNS, au lieu d’une adresse IP.

## <a name="create-and-manage-dns-zones-and-records-using-the-apis"></a>Créer et gérer des enregistrements et des zones DNS à l’aide des API

Vous pouvez créer et gérer des enregistrements et des zones DNS dans Azure Stack Hub.

Azure Stack Hub fournit un service DNS similaire à celui d’Azure, à l’aide d’API cohérentes avec les API Azure DNS.  En hébergeant vos domaines dans le DNS Azure Stack Hub, vous pouvez gérer vos enregistrements DNS à l’aide des mêmes informations d’identification, API et outils. Vous pouvez également utiliser la même facturation et le même support que vos autres services Azure.

L’infrastructure DNS Azure Stack Hub est plus compacte que celle d’Azure. La taille et l’emplacement d’un déploiement Azure Stack Hub affectent l’étendue, l’échelle et les performances du DNS. Cela signifie également que les performances, la disponibilité, la distribution mondiale et la haute disponibilité peuvent varier d’un déploiement à l’autre.

## <a name="comparison-with-azure-dns"></a>Comparaison avec le système DNS Azure

Le DNS dans Azure Stack Hub est similaire au DNS dans Azure, à quelques exceptions importantes près :

* **Il ne prend pas en charge les enregistrements AAAA** : Azure Stack Hub ne prend pas en charge les enregistrements AAAA parce qu’il ne prend pas en charge les adresses IPv6. Il s’agit d’une différence essentielle entre les DNS dans Azure et dans Azure Stack Hub.

* **Il n’est pas multilocataire** : Le service DNS dans Azure Stack Hub n’est pas mutualisé. Les locataires ne peuvent pas créer la même zone DNS. Seul le premier abonnement qui tente de créer la zone y parvient, et les requêtes suivantes échouent. Il s’agit d’une autre différence majeure entre systèmes DNS Azure et Azure Stack Hub.

* **Balises, métadonnées et ETags** : Il existe des différences mineures dans la façon dont Azure Stack Hub gère les balises, les métadonnées, les ETags et les limites.

Pour en savoir plus sur le système Azure DNS, consultez [Enregistrements et zones DNS](/azure/dns/dns-zones-records).

### <a name="tags"></a>Balises

Le DNS Azure Stack Hub prend en charge l’utilisation de balises Azure Resource Manager sur des ressources de zone DNS. Il ne prend pas en charge les balises sur les jeux d’enregistrements DNS. Par contre, les **métadonnées** sont prises en charge sur les jeux d’enregistrements DNS, comme expliqué dans la prochaine section.

### <a name="metadata"></a>Métadonnées

À la place de balises de jeu d’enregistrements, le DNS Azure Stack Hub prend en charge l’annotation des jeux d’enregistrements à l’aide de *métadonnées*. Comme des balises, les métadonnées permettent d’associer des paires nom-valeur à chaque jeu d’enregistrements. Les métadonnées peuvent être utiles par exemple pour indiquer l’objectif de chaque jeu d’enregistrements. Contrairement aux balises, vous ne pouvez pas utiliser les métadonnées pour produire une vue filtrée de votre facture Azure. Et vous ne pouvez pas non plus les spécifier dans une stratégie Azure Resource Manager.

### <a name="etags"></a>Etags

Supposons que deux personnes ou deux processus tentent de modifier un enregistrement DNS en même temps. Lequel gagne ? Et le gagnant sait-il qu’il a remplacé les modifications créées par quelqu’un d’autre ?

Le DNS Azure Stack Hub utilise des *ETags* pour gérer en toute sécurité des modifications simultanées de la même ressource. Les ETags sont différents des *Balises* Azure Resource Manager. Chaque ressource DNS (zone ou jeu d’enregistrements) est associée à un Etag. Chaque fois qu’une ressource est récupérée, son ETag l’est également. Lorsque vous mettez à jour une ressource, vous pouvez choisir de retransmettre l’ETag pour que le DNS Azure Stack Hub vérifie qu’il correspond à celui se trouvant sur le serveur. Étant donné que chaque mise à jour d’une ressource entraîne la régénération de l’Etag, l’absence de concordance entre les Etags indique qu’une modification simultanée a eu lieu. Les Etags sont également utilisés quand vous créez une ressource pour vous assurer que cette ressource n’existe pas déjà.

Par défaut, les applets de commandes PowerShell du DNS Azure Stack Hub utilisent des ETags pour bloquer des modifications apportées simultanément à des zones et à des jeux d’enregistrements. Le commutateur facultatif `-Overwrite` permet de supprimer les vérifications Etag. Sans contrôles Etag, les modifications simultanées qui se sont produites sont remplacées.

Au niveau de l’API REST du DNS Azure Stack Hub, les ETags sont spécifiés à l’aide d’en-têtes HTTP. Leur comportement est décrit dans le tableau suivant :

| En-tête | Comportement|
|--------|---------|
| None   | PUT réussit toujours (aucune vérification Etag).|
| If-match| PUT ne réussit que si la ressource existe et que l’Etag correspond.|
| If-match *| PUT réussit seulement si la ressource existe.|
| If-none-match *| PUT réussit seulement si la ressource n’existe pas.|

### <a name="limits"></a>limites

Les limites par défaut s’appliquant lors de l’utilisation du DNS Azure Stack Hub sont les suivantes :

| Ressource| Limite par défaut|
|---------|--------------|
| Zones par abonnement| 100|
| Jeux d’enregistrements par zone| 5 000|
| Enregistrements par jeu d’enregistrements| 20|

## <a name="next-steps"></a>Étapes suivantes

* [Introduction aux iDNS pour Azure Stack Hub](azure-stack-understanding-dns.md)
