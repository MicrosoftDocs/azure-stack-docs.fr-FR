---
title: Notes de publication de la version 1.1.93.0 du fournisseur de ressources MySQL Azure Stack Hub
description: Consultez les notes de publication pour découvrir les nouveautés incluses dans la mise à jour 1.1.93.0 du fournisseur de ressources MySQL Azure Stack Hub.
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: f3e9e177e4d335c667754a308698ed362c6535b2
ms.sourcegitcommit: af4374755cb4875a7cbed405b821f5703fa1c8cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95812746"
---
# <a name="mysql-resource-provider-11930-release-notes"></a>Notes de publication de la version 1.1.93.0 du fournisseur de ressources MySQL

Ces notes de publication décrivent les améliorations et les problèmes connus liés à la version 1.1.93.0 du fournisseur de ressources MySQL.

## <a name="build-reference"></a>Référence de build
Téléchargez le binaire du fournisseur de ressources MySQL, puis exécutez le fichier auto-extracteur pour extraire le contenu dans un répertoire temporaire. Le fournisseur de ressources dispose d'une build Azure Stack Hub minimale correspondante. La version minimale d’Azure Stack Hub requise pour installer cette version du fournisseur de ressources MySQL est mentionnée ci-dessous :

> |Versions d’Azure Stack Hub prises en charge|Version du fournisseur de ressources MySQL|
> |-----|-----|
> |Version 2008, 2005|[MySQL RP version 1.1.93.0](https://aka.ms/azshmysqlrp11930)|  
> |     |     |

> [!IMPORTANT]
> Appliquez la mise à jour minimale prise en charge d’Azure Stack Hub sur votre système intégré Azure Stack Hub avant de déployer la dernière version du fournisseur de ressources MySQL.

## <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Cette version du fournisseur de ressources MySQL Azure Stack Hub inclut les améliorations et correctifs suivants :

- **Mettez à jour la machine virtuelle de base sur un serveur Windows spécialisé.** Cette version de Windows Server est conçue pour l’infrastructure d’Azure Stack Hub Add-On RP et n’est pas visible sur la place de marché du client. Veillez à télécharger l’image de **Microsoft AzureStack Add-on RP Windows Server INTERNE UNIQUEMENT** avant le déploiement ou la mise à niveau vers cette version du fournisseur de ressources MySQL.
- **Prendre en charge la suppression des métadonnées de base de données orphelines et héberger les métadonnées du serveur.** Lorsqu’un serveur d’hébergement ne peut plus être connecté, le locataire a la possibilité de supprimer les métadonnées de la base de données orpheline du portail. En l’absence de métadonnées de base de données orphelines liées au serveur d’hébergement, l’opérateur est en mesure de supprimer les métadonnées du serveur d’hébergement orphelin du portail d’administration.
- **Définissez KeyVaultPfxPassword comme argument facultatif lors de la rotation des secrets.** Pour plus d’informations, consultez [ce document](azure-stack-sql-resource-provider-maintain.md#secrets-rotation).
- **Correctifs de bogues mineurs.**

Nous vous recommandons d’appliquer le correctif du fournisseur de ressources MySQL 1.1.93.0 après la mise à niveau d’Azure Stack Hub vers la version 2005.

## <a name="known-issues"></a>Problèmes connus
Aucun.

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur le déploiement d'un fournisseur de ressources MySQL](azure-stack-mysql-resource-provider.md).
- [Préparer le déploiement du fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).
- [Mettre à niveau le fournisseur de ressources MySQL à partir d’une version précédente](azure-stack-mysql-resource-provider-update.md).
