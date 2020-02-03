---
title: Différences et points à connaître sur le stockage Azure Stack Hub
description: Découvrez les différences entre le stockage Azure Stack Hub et le stockage Azure, ainsi que les points à prendre en compte lorsque vous déployez Azure Stack Hub.
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: 6e5171c105bd806895a04880e0bec0af5fab8855
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883965"
---
# <a name="azure-stack-hub-storage-differences-and-considerations"></a>Stockage Azure Stack Hub : Différences et considérations

Le stockage Azure Stack Hub englobe l'ensemble des services cloud de stockage fournis dans Microsoft Azure Stack Hub. Le stockage Azure Stack Hub fournit des fonctionnalités de gestion des objets blob, des tables, des files d'attente et des comptes dont la sémantique est cohérente avec Azure.

Cet article récapitule les différences connues entre le stockage Azure Stack Hub et les services de stockage Azure. Il énumère également les éléments à prendre en compte lorsque vous déployez Azure Stack Hub. Pour connaître les différences majeures entre Azure global et Azure Stack Hub, consultez l'article [Principales considérations](azure-stack-considerations.md).

## <a name="cheat-sheet-storage-differences"></a>Aide-mémoire : différences entre les stockages

| Fonctionnalité | Azure (global) | Azure Stack Hub |
| --- | --- | --- |
|Stockage Fichier|Partages de fichiers SMB sur le cloud pris en charge|Pas encore pris en charge
|Chiffrement du service de stockage Azure pour les données au repos|Chiffrement AES 256 bits. Prise en charge du chiffrement à l’aide de clés gérées par le client dans Azure Key Vault.|Chiffrement AES 128 bits BitLocker. Le chiffrement à l’aide de clés gérées par le client n’est pas pris en charge.
|Type de compte de stockage|Comptes de stockage V1, V2 et d’objets blob universels|Comptes de stockage à usage général V1 uniquement
|Options de réplication|Stockage localement redondant, stockage géoredondant, stockage géoredondant avec accès en lecture et stockage redondant interzone|Stockage localement redondant.
|Stockage Premium|Fournit des performances élevées et un stockage à faible latence. Seuls sont pris en charge les objets blob de pages dans les comptes de stockage Premium.|Peut être approvisionné, mais sans limite ni garantie de performances. Ne bloque pas l’utilisation d’objets blob de blocs, d’objets blob d’ajout, de tables et de files d’attente dans les comptes de stockage Premium.
|Disques managés|Premium et standard pris en charge|Pris en charge lorsque vous utilisez la version 1808 ou ultérieure.
|Nom de l’objet blob|1 024 caractères (2 048 octets)|880 caractères (1 760 octets)
|Taille maximale d’un objet blob de blocs|4,75 To (100 Mo X 50 000 blocs)|4,75 To (100 Mo x 50 000 blocs) pour la mise à jour 1802 ou une version plus récente. 50 000 x 4 Mo (environ 195 Go) pour les versions précédentes.
|Copie d’instantané d’objet blob de pages|Prise en charge des disques de machine virtuelle non gérés par Sauvegarde Azure attachés à une machine virtuelle en fonctionnement|Pas encore pris en charge.
|Copie d’instantané incrémentiel d’objet blob de pages|Objets blob de pages Azure Premium et standard pris en charge|Pas encore pris en charge.
|Facturation d’objet blob de pages|Des frais sont liés aux pages uniques, qu’elles soient dans l’objet blob ou dans la capture instantanée. Il n’y a aucuns frais supplémentaires pour les captures instantanées associées à un objet blob jusqu’à ce que l’objet blob de base soit mis à jour.|Des frais sont liés à l’objet blob de base et les captures instantanées associées. Il y a des frais supplémentaires pour chaque capture instantanée individuelle.
|Niveaux de stockage pour le Stockage Blob|Niveaux de stockage chaud, à froid et archivage.|Pas encore pris en charge.
|Suppression réversible pour le Stockage Blob|Disponibilité générale|Pas encore pris en charge.
|Taille maximale d’un objet blob de pages|8 To|1 To
|Taille de page d’un objet blob de pages|512 octets|4 Ko
|Taille de la clé de ligne et de la clé de partition de table|1 024 caractères (2 048 octets)|400 caractères (800 octets)
|Instantané d’objet blob|Le nombre maximal d’instantanés d’un objet blob n’est pas limité.|Le nombre maximum d’instantanés d’un objet blob est de 1 000.
|Azure AD Authentication pour le stockage|En préversion|Pas encore pris en charge.
|Objets blob immuables|Disponibilité générale|Pas encore pris en charge.
|Règles de pare-feu et de réseau virtuel pour le stockage|Disponibilité générale|Pas encore pris en charge.|

Il existe également des différences sur le plan des métriques de stockage :

* Les données de transaction dans les métriques de stockage ne font pas la distinction entre la bande passante réseau interne et externe.
* Les données de transaction dans les métriques de stockage ne prennent pas en compte l’accès des machines virtuelles aux disques montés.

## <a name="api-version"></a>Version de l'API

Les versions suivantes sont prises en charge avec le stockage Azure Stack Hub :

API de services de Stockage Azure :

Mise à jour 1811 ou plus récente :

- [2017-11-09](https://docs.microsoft.com/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](https://docs.microsoft.com/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Versions antérieures :

- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

API de gestion des services de Stockage Azure :

Mise à jour 1811 ou plus récente :

- [2017-10-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2017-06-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-12-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-05-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Versions antérieures :

- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

## <a name="powershell-version"></a>Version de PowerShell

Pour le module de stockage PowerShell, tenez compte de la version compatible avec l'API REST. 

| Module | Version prise en charge | Usage |
|----------------|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure.Storage | [4.5.0](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0) | Gère les objets blob, les files d'attente et les tables dans les comptes de stockage Azure Stack Hub |
| AzureRM.Storage | [5.0.4](https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.4) | Crée et gère des comptes de stockage dans Azure Stack Hub |


Pour plus d'informations sur les bibliothèques clientes de stockage Azure Stack Hub prises en charge, consultez : [Bien démarrer avec les outils de développement du stockage Azure Stack Hub](azure-stack-storage-dev.md).

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec les outils de développement du stockage Azure Stack Hub](azure-stack-storage-dev.md)
* [Utiliser des outils de transfert de données pour le stockage Azure Stack Hub](azure-stack-storage-transfer.md)
* [Présentation du stockage Azure Stack Hub](azure-stack-storage-overview.md)
