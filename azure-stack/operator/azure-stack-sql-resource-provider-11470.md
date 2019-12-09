---
title: Notes de publication de la version 1.1.47.0 du fournisseur de ressources SQL Azure Stack | Microsoft Docs
description: Découvrez le contenu de la dernière mise à jour du fournisseur de ressources SQL Azure Stack, notamment les problèmes connus et l'emplacement de téléchargement.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: justinha
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: 703edc0cc2c5229c3301db0b68a400507fe4c872
ms.sourcegitcommit: 11e0c2d9abbc0a2506f992976b3c9f8ca4e746b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74823527"
---
# <a name="sql-resource-provider-11470-release-notes"></a>Notes de publication de la version 1.1.47.0 du fournisseur de ressources SQL

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Ces notes de publication décrivent les améliorations et les problèmes connus du fournisseur de ressources SQL version 1.1.47.0.

## <a name="build-reference"></a>Référence de build
Téléchargez le binaire du fournisseur de ressources SQL, puis exécutez le fichier auto-extracteur pour extraire le contenu dans un répertoire temporaire. Le fournisseur de ressources possède une build Azure Stack minimale correspondante. La version minimale d'Azure Stack requise pour installer cette version du fournisseur de ressources SQL est mentionnée ci-dessous :

> |Version minimale d’Azure Stack|Version du fournisseur de ressources SQL|
> |-----|-----|
> |Version 1910 (1.1910.0.58)|[SQL RP version 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Appliquez la mise à jour minimale prise en charge d'Azure Stack sur votre système intégré Azure Stack ou déployez le Kit de développement Azure Stack (ASDK) le plus récent avant de déployer la dernière version du fournisseur de ressources SQL.

## <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Cette version du fournisseur de ressources SQL Azure Stack est un correctif logiciel permettant de rendre le fournisseur de ressources compatible avec certaines des modifications apportées récemment au portail dans le cadre de la mise à jour 1910. Cette version ne contient pas de nouvelles fonctionnalités.

Elle prend également en charge la dernière version de l’API Azure Stack (profil 2019-03-01-hybrid) et du module Azure Stack PowerShell (1.8.0). Ainsi, pendant le déploiement et la mise à jour, vous n’avez pas à installer d’anciennes versions des modules.

Suivez le processus de mise à jour du fournisseur de ressources pour appliquer le correctif du fournisseur de ressources SQL 1.1.47.0 après la mise à niveau d’Azure Stack vers la version 1910. Cela vous aidera à résoudre un problème connu du portail d’administration, lors duquel la supervision de la capacité ne cesse de charger dans le fournisseur de ressources SQL.

## <a name="known-issues"></a>Problèmes connus

Aucune.

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur le déploiement d'un fournisseur de ressources SQL](azure-stack-sql-resource-provider.md).

[Préparer le déploiement du fournisseur de ressources SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Mettre à niveau le fournisseur de ressources SQL à partir d’une version précédente](azure-stack-sql-resource-provider-update.md). 
