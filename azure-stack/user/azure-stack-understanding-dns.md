---
title: Utiliser iDNS dans Azure Stack Hub
description: Apprenez à utiliser les fonctionnalités et capacités iDNS dans Azure Stack Hub.
author: Justinha
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: Justinha
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 3209c3a51b491bcdf2779e95c58f41cb124cd3f3
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77704860"
---
# <a name="use-idns-in-azure-stack-hub"></a>Utiliser iDNS dans Azure Stack Hub 

iDNS est une fonctionnalité de mise en réseau Azure Stack Hub qui vous permet de résoudre les noms DNS externes (par exemple, https:\//www.bing.com). Elle vous permet également d’enregistrer des noms de réseau virtuel interne. De cette façon, vous pouvez résoudre des machines virtuelles sur le même réseau virtuel par nom plutôt que par adresse IP. Cette approche évite la nécessité de fournir des entrées de serveur DNS personnalisées. Pour plus d’informations sur DNS, consultez l’article [Vue d’ensemble d’Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>Que fait iDNS ?

Avec iDNS dans Azure Stack Hub, vous obtenez les fonctionnalités suivantes, sans avoir à spécifier d'entrées de serveur DNS personnalisées :

- Services de résolution de noms DNS partagés pour les charges de travail de locataire.
- Service DNS faisant autorité pour la résolution de noms et l’enregistrement DNS dans le réseau virtuel du locataire.
- Service DNS récursif pour la résolution de noms Internet à partir de machines virtuelles des abonnés. Les locataires n’ont plus besoin de spécifier des entrées DNS personnalisées pour résoudre les noms Internet (par exemple, www\.Bing.com.)

Vous pouvez toujours configurer votre propre DNS et utiliser des serveurs DNS personnalisés. Toutefois, en utilisant iDNS, vous pouvez résoudre les noms DNS Internet et vous connecter à d’autres machines virtuelles du même réseau virtuel sans avoir à créer d’entrées DNS personnalisées.

## <a name="what-doesnt-idns-do"></a>Quelles sont les opérations non prises en charge par iDNS ?

iDNS ne vous permet pas de créer un enregistrement DNS pour un nom qui peut être résolu en dehors du réseau virtuel.

Dans Azure, vous avez la possibilité de spécifier une étiquette de nom DNS qui est associée à une adresse IP publique. Vous pouvez choisir l’étiquette (préfixe), mais Azure choisit le suffixe, qui est basé sur la région dans laquelle vous créez l’adresse IP publique.

![Exemple d’étiquette de nom DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Dans l’image ci-dessus, Azure crée un enregistrement « A » dans DNS pour l’étiquette de nom DNS spécifiée dans la zone **westus.cloudapp.azure.com**. Le préfixe et le suffixe combinés représentent un [nom de domaine complet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) pouvant être résolu n’importe où sur le réseau Internet public.

Azure Stack Hub prend uniquement en charge iDNS pour l'enregistrement de noms internes. Il ne peut donc pas effectuer les opérations suivantes :

- Créer un enregistrement DNS dans une zone DNS hébergée existante (par exemple, local.azurestack.external).
- Créer une zone DNS (par exemple, Contoso.com).
- Créer un enregistrement dans votre propre zone DNS personnalisée.
- Prendre en charge l’achat de noms de domaine.

## <a name="demo-of-how-idns-works"></a>Démonstration du fonctionnement d’iDNS

Tous les noms d’hôte des machines virtuelles situées sur des réseaux virtuels sont stockés sous la forme d’enregistrements de ressources DNS dans la même zone. Toutefois, sous leur propre compartiment unique défini en tant que GUID, ceux-ci correspondent à l’ID de réseau virtuel de l’infrastructure SDN sur laquelle la machine virtuelle a été déployée. Les noms de domaine complets (FQDN) des machines virtuelles de locataire comprennent le nom d’ordinateur et la chaîne de suffixe DNS du réseau virtuel, au format GUID.

<!--- what does compartment mean? Add a screenshot? can we clarify what we mean by host name and computer name. the description doesn't match the example in the table.--->
 
Voici un simple lab pour illustrer la façon dont cela fonctionne. Nous avons créé 3 machines virtuelles sur un seul réseau virtuel et une autre machine virtuelle sur un réseau virtuel distinct :

<!--- Is DNS Label the right term? If so, we should define it. The column lists FQDNs, afaik. Where does the domain suffix come from? --->
 
|Machine virtuelle    |Réseau virtuel    |IP privée   |Adresse IP publique    | Nom DNS                                |
|------|--------|-------------|-------------|------------------------------------------|
|VM-A1 |VNetA   | 10.0.0.5    |172.31.12.68 |VM-A1-Label.lnv1.cloudapp.azscss.external |
|VM-A2 |VNetA   | 10.0.0.6    |172.31.12.76 |VM-A2-Label.lnv1.cloudapp.azscss.external |
|VM-A3 |VNetA   | 10.0.0.7    |172.31.12.49 |VM-A3-Label.lnv1.cloudapp.azscss.external |
|VM-B1 |VNetB   | 10.0.0.4    |172.31.12.57 |VM-B1-Label.lnv1.cloudapp.azscss.external |
 
 
|Réseau virtuel  |GUID                                 |Chaîne de suffixe DNS                                                  |
|------|-------------------------------------|-------------------------------------------------------------------|
|VNetA |e71e1db5-0a38-460d-8539-705457a4cf75 |e71e1db5-0a38-460d-8539-705457a4cf75.internal.lnv1.azurestack.local|
|VNetB |e8a6e386-bc7a-43e1-a640-61591b5c76dd |e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local|
 
 
Vous pouvez effectuer des tests de résolution de noms pour mieux comprendre le fonctionnement d’iDNS :

<!--- why Linux?--->

À partir de VM-A1 (machine virtuelle Linux) : Recherche de VM-A2. Vous pouvez voir que le suffixe DNS pour VNetA est ajouté et que le nom est résolu en adresse IP privée :
 
```console
carlos@VM-A1:~$ nslookup VM-A2
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-A2.e71e1db5-0a38-460d-8539-705457a4cf75.internal.lnv1.azurestack.local
Address: 10.0.0.6
```
 
La recherche de VM-A2-Label sans fournir le nom de domaine complet échoue, comme prévu :

```console 
carlos@VM-A1:~$ nslookup VM-A2-Label
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-A2-Label: SERVFAIL
```

Si vous fournissez le nom de domaine complet pour l’étiquette DNS, le nom est résolu en adresse IP publique :

```console
carlos@VM-A1:~$ nslookup VM-A2-Label.lnv1.cloudapp.azscss.external
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-A2-Label.lnv1.cloudapp.azscss.external
Address: 172.31.12.76
```
 
La tentative de résolution de VM-B1 (qui appartient à un autre réseau virtuel) échoue, car cet enregistrement n’existe pas dans cette zone.

```console
carlos@caalcobi-vm4:~$ nslookup VM-B1
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-B1: SERVFAIL
```

L’utilisation du nom de domaine complet pour VM-B1 n’aide pas, car cet enregistrement appartient à une autre zone.

```console 
carlos@VM-A1:~$ nslookup VM-B1.e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-B1.e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local: SERVFAIL
```
 
Si vous utilisez le nom de domaine complet pour l’étiquette DNS, alors il est correctement résolu :

``` 
carlos@VM-A1:~$ nslookup VM-B1-Label.lnv1.cloudapp.azscss.external
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-B1-Label.lnv1.cloudapp.azscss.external
Address: 172.31.12.57
```
 
À partir de VM-A3 (machine virtuelle Windows). Remarquez la différence entre les réponses faisant autorité et ne faisant pas autorité.

Enregistrements internes :

```console
C:\Users\carlos>nslookup
Default Server:  UnKnown
Address:  168.63.129.16
 
> VM-A2
Server:  UnKnown
Address:  168.63.129.16
 
Name:    VM-A2.e71e1db5-0a38-460d-8539-705457ª4cf75.internal.lnv1.azurestack.local
Address:  10.0.0.6
```

Enregistrements externes :

```console
> VM-A2-Label.lnv1.cloudapp.azscss.external
Server:  UnKnown
Address:  168.63.129.16
 
Non-authoritative answer:
Name:    VM-A2-Label.lnv1.cloudapp.azscss.external
Address:  172.31.12.76
``` 
 
En bref, vous pouvez en déduire ce qui suit :
 
*   Chaque réseau virtuel a sa propre zone, contenant des enregistrements A pour toutes les adresses IP privées, constituées du nom de machine virtuelle et du suffixe DNS du réseau virtuel (à savoir son GUID).
    *   \<vmname>.\<vnetGUID\>.internal.\<region>.\<stackinternalFQDN>
    *   Ceci est effectué automatiquement
*   Si vous utilisez des adresses IP publiques, vous pouvez également créer des étiquettes DNS pour elles. Celles-ci sont résolues comme n’importe quelle autre adresse externe.
 
 
- Les serveurs iDNS sont les serveurs faisant autorité pour leurs zones DNS internes. Ils servent également de programme de résolution pour les noms publics quand des machines virtuelles de locataire tentent de se connecter à des ressources externes. S’il existe une requête portant sur une ressource externe, les serveurs iDNS la transfèrent aux serveurs DNS faisant autorité à des fins de résolution.
 
Comme vous pouvez le constater dans les résultats du lab, vous contrôlez l’adresse IP utilisée. Si vous utilisez le nom de la machine virtuelle, vous obtenez l’adresse IP privée et, si vous utilisez l’étiquette DNS, vous obtenez l’adresse IP publique.

## <a name="next-steps"></a>Étapes suivantes

[Utilisation de DNS dans Azure Stack Hub](azure-stack-dns.md)
