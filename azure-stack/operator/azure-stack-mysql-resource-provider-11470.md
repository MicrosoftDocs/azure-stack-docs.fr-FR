---
title: Notes de publication de la version 1.1.47.0 du fournisseur de ressources MySQL pour Azure Stack Hub | Microsoft Docs
description: Consultez les notes de publication pour découvrir les nouveautés incluses dans la mise à jour 1.1.47.0 du fournisseur de ressources MySQL pour Azure Stack Hub.
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
ms.openlocfilehash: 43bbfb0c044dc90986a099c1d5729ad54082dc88
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882213"
---
# <a name="mysql-resource-provider-11470-release-notes"></a>Notes de publication de la version 1.1.47.0 du fournisseur de ressources MySQL

Ces notes de publication décrivent les améliorations et les problèmes connus liés à la version 1.1.47.0 du fournisseur de ressources MySQL.

## <a name="build-reference"></a>Référence de build
Téléchargez le binaire du fournisseur de ressources MySQL, puis exécutez le fichier auto-extracteur pour extraire le contenu dans un répertoire temporaire. Le fournisseur de ressources possède une build Azure Stack Hub minimale correspondante. La version minimale d’Azure Stack Hub requise pour installer cette version du fournisseur de ressources MySQL est mentionnée ci-dessous :

> |Version minimale d’Azure Stack Hub|Version du fournisseur de ressources MySQL|
> |-----|-----|
> |Version 1910 (1.1910.0.58)|[MySQL RP version 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Appliquez la mise à jour minimale prise en charge d’Azure Stack Hub à votre système intégré Azure Stack Hub, ou déployez le Kit de développement Azure Stack Hub (ASDK) le plus récent avant de déployer la dernière version du fournisseur de ressources MySQL.

## <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Cette version du fournisseur de ressources MySQL pour Azure Stack Hub est un correctif logiciel permettant de rendre le fournisseur de ressources compatible avec certaines des modifications apportées récemment au portail dans le cadre de la mise à jour 1910. Cette version ne contient pas de nouvelles fonctionnalités.

Elle prend également en charge la dernière version de l’API Azure Stack Hub (profil 2019-03-01-hybrid) et du module Azure Stack Hub PowerShell (1.8.0). Ainsi, pendant le déploiement et la mise à jour, vous n’avez pas à installer d’anciennes versions des modules.

Nous vous recommandons d’appliquer le correctif du fournisseur de ressources MySQL 1.1.47.0 après la mise à niveau d’Azure Stack Hub vers la version 1910.

## <a name="known-issues"></a>Problèmes connus

Aucun.

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur le déploiement d'un fournisseur de ressources MySQL](azure-stack-mysql-resource-provider.md).

[Préparer le déploiement du fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Mettre à niveau le fournisseur de ressources MySQL à partir d’une version précédente](azure-stack-mysql-resource-provider-update.md). 
