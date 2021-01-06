---
title: Notes de publication de la version 1.1.93.x du fournisseur de ressources SQL Azure Stack Hub
description: Consultez les notes de publication pour découvrir les nouveautés incluses dans la mise à jour 1.1.93.x du fournisseur de ressources SQL Azure Stack Hub.
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: 80caef7369a3cf5ebe79338bc907fa9b31d1a759
ms.sourcegitcommit: a745662c7a5a18f135accf3f70d8508b57e83e2b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97737910"
---
# <a name="sql-resource-provider-1193x-release-notes"></a>Notes de publication de la version 1.1.93.x du fournisseur de ressources SQL

Ces notes de publication décrivent les améliorations et les problèmes connus du fournisseur de ressources SQL version 1.1.93.x.

## <a name="build-reference"></a>Référence de build
Téléchargez le binaire du fournisseur de ressources SQL, puis exécutez le fichier auto-extracteur pour extraire le contenu dans un répertoire temporaire. Le fournisseur de ressources dispose d'une build Azure Stack Hub minimale correspondante. La version minimale d’Azure Stack Hub requise pour installer cette version du fournisseur de ressources SQL est mentionnée ci-dessous :

> |Versions d’Azure Stack Hub prises en charge|Version du fournisseur de ressources SQL|
> |-----|-----|
> |Version 2008, 2005|[SQL RP version 1.1.93.1](https://aka.ms/azshsqlrp11931)|  
> |     |     |

> [!IMPORTANT]
> Appliquez la mise à jour minimale prise en charge d’Azure Stack Hub sur votre système intégré Azure Stack Hub avant de déployer la dernière version du fournisseur de ressources MySQL.

## <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Cette version du fournisseur de ressources SQL Azure Stack Hub inclut les améliorations et correctifs suivants :

- **Mettez à jour la machine virtuelle de base sur un serveur Windows spécialisé.** Cette version de Windows Server est conçue pour l’infrastructure d’Azure Stack Hub Add-On RP et n’est pas visible sur la place de marché du client. Veillez à télécharger l’image **Microsoft AzureStack Add-On RP Windows Server** avant le déploiement ou la mise à niveau vers cette version du fournisseur de ressources SQL.
- **Prendre en charge la suppression des métadonnées de base de données orphelines et héberger les métadonnées du serveur.** Lorsqu’un serveur d’hébergement ne peut plus être connecté, le locataire a la possibilité de supprimer les métadonnées de la base de données orpheline du portail. En l’absence de métadonnées de base de données orphelines liées au serveur d’hébergement, l’opérateur est en mesure de supprimer les métadonnées du serveur d’hébergement orphelin du portail d’administration.
- **Définissez KeyVaultPfxPassword comme argument facultatif lors de la rotation des secrets.** Pour plus d’informations, consultez [ce document](azure-stack-sql-resource-provider-maintain.md#secrets-rotation).
- **Correctifs de bogues mineurs.**

Nous vous recommandons d’appliquer le correctif 1.1.93.1 du fournisseur de ressources SQL après la mise à niveau d’Azure Stack Hub vers la version 2005.

## <a name="known-issue"></a>Problème connu
Le déploiement de la version 1.1.93.0 peut échouer si le mauvais AzureRmContext est utilisé. Il est recommandé d’effectuer une mise à niveau directement vers la version 1.1.93.1. Si vous avez déjà effectué la mise à niveau vers 1.1.93.0, vous pouvez ignorer sans problème la version 1.1.93.1.

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur le déploiement d'un fournisseur de ressources SQL](azure-stack-sql-resource-provider.md).
- [Préparer le déploiement du fournisseur de ressources SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).
- [Mettre à niveau le fournisseur de ressources SQL à partir d’une version précédente](azure-stack-sql-resource-provider-update.md).

