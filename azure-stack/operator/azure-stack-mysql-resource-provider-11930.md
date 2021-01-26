---
title: Notes de publication de la version 1.1.93.0 du fournisseur de ressources MySQL Azure Stack Hub
description: Consultez les notes de publication pour découvrir les nouveautés incluses dans la mise à jour 1.1.93.0 du fournisseur de ressources MySQL Azure Stack Hub.
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: 225c765642dc48b299c899d71eb0d0ce13d4852d
ms.sourcegitcommit: 0983c1f90734b7ea5e23ae614eeaed38f9cb3c9a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571363"
---
# <a name="mysql-resource-provider-1193x-release-notes"></a>Notes de publication de la version 1.1.93.x du fournisseur de ressources MySQL

Ces notes de publication décrivent les améliorations et les problèmes connus dans la version 1.1.93.x du fournisseur de ressources MySQL.

## <a name="build-reference"></a>Référence de build
Téléchargez le binaire du fournisseur de ressources MySQL, puis exécutez le fichier auto-extracteur pour extraire le contenu dans un répertoire temporaire. Le fournisseur de ressources dispose d'une build Azure Stack Hub minimale correspondante. La version minimale d’Azure Stack Hub requise pour installer cette version du fournisseur de ressources MySQL est mentionnée ci-dessous :

> |Versions d’Azure Stack Hub prises en charge|Version du fournisseur de ressources MySQL|
> |-----|-----|
> |Version 2008, 2005|[MySQL RP version 1.1.93.1](https://aka.ms/azshmysqlrp11931)|  
> |     |     |

> [!IMPORTANT]
> Appliquez la mise à jour minimale prise en charge d’Azure Stack Hub sur votre système intégré Azure Stack Hub avant de déployer la dernière version du fournisseur de ressources MySQL.

## <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Cette version du fournisseur de ressources MySQL Azure Stack Hub inclut les améliorations et correctifs suivants :

- **Mettez à jour la machine virtuelle de base sur un serveur Windows spécialisé.** Cette version de Windows Server est conçue pour l’infrastructure d’Azure Stack Hub Add-On RP et n’est pas visible sur la place de marché du client. Veillez à télécharger l’image **Microsoft AzureStack Add-On RP Windows Server** avant le déploiement ou la mise à niveau vers cette version du fournisseur de ressources MySQL.
- **Prendre en charge la suppression des métadonnées de base de données orphelines et héberger les métadonnées du serveur.** Lorsqu’un serveur d’hébergement ne peut plus être connecté, le locataire a la possibilité de supprimer les métadonnées de la base de données orpheline du portail. En l’absence de métadonnées de base de données orphelines liées au serveur d’hébergement, l’opérateur est en mesure de supprimer les métadonnées du serveur d’hébergement orphelin du portail d’administration.
- **Définissez KeyVaultPfxPassword comme argument facultatif lors de la rotation des secrets.** Pour plus d’informations, consultez [ce document](azure-stack-sql-resource-provider-maintain.md#secrets-rotation).
- **Correctifs de bogues mineurs.**

Nous vous recommandons d’appliquer le correctif 1.1.93.1 du fournisseur de ressources MySQL après la mise à niveau d’Azure Stack Hub vers la version 2005.

## <a name="known-issues"></a>Problèmes connus
Le déploiement de la version 1.1.93.0 peut échouer si le mauvais AzureRmContext est utilisé. Il est recommandé d’effectuer une mise à niveau directement vers la version 1.1.93.1. Si vous avez déjà effectué la mise à niveau vers 1.1.93.0, vous pouvez ignorer en toute sécurité la version 1.1.93.1.

Lors du redéploiement du fournisseur de ressources MySQL alors que la même version a déjà été déployée (par exemple, quand le fournisseur de ressources MySQL 1.1.93.1 est déjà déployé et que la même version est de nouveau déployée), la machine virtuelle qui héberge le fournisseur de ressources MySQL est arrêtée. Pour résoudre ce problème, accédez au portail d’administration, recherchez et redémarrez la machine virtuelle nommée mysqlvm\<version\> dans le groupe de ressources nommé system.\<region\>.mysqladapter.

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur le déploiement d'un fournisseur de ressources MySQL](azure-stack-mysql-resource-provider.md).
- [Préparer le déploiement du fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).
- [Mettre à niveau le fournisseur de ressources MySQL à partir d’une version précédente](azure-stack-mysql-resource-provider-update.md).
