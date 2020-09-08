---
title: Notes de publication de la version 1.1.47.0 du fournisseur de ressources MySQL Azure Stack Hub
description: Consultez les notes de publication pour découvrir les nouveautés incluses dans la mise à jour 1.1.47.0 du fournisseur de ressources MySQL pour Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 11/26/2019
ms.author: justinha
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: 5bc03134a859182909e62c60159049d639eb8ffe
ms.sourcegitcommit: 08a421ab5792ab19cc06b849763be22f051e6d78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89364675"
---
# <a name="mysql-resource-provider-11470-release-notes"></a>Notes de publication de la version 1.1.47.0 du fournisseur de ressources MySQL

Ces notes de publication décrivent les améliorations et les problèmes connus liés à la version 1.1.47.0 du fournisseur de ressources MySQL.

## <a name="build-reference"></a>Référence de build
Téléchargez le binaire du fournisseur de ressources MySQL, puis exécutez le fichier auto-extracteur pour extraire le contenu dans un répertoire temporaire. Le fournisseur de ressources dispose d'une build Azure Stack Hub minimale correspondante. La version minimale d’Azure Stack Hub requise pour installer cette version du fournisseur de ressources MySQL est mentionnée ci-dessous :

> |Version minimale d'Azure Stack Hub|Version du fournisseur de ressources MySQL|
> |-----|-----|
> |Version 1910 (1.1910.0.58)|[MySQL RP version 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> Appliquez la mise à jour minimale prise en charge d’Azure Stack Hub à votre système intégré Azure Stack Hub, ou déployez le Kit de développement Azure Stack Hub (ASDK) le plus récent avant de déployer la dernière version du fournisseur de ressources MySQL.

## <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Cette version du fournisseur de ressources MySQL pour Azure Stack Hub est un correctif logiciel permettant de rendre le fournisseur de ressources compatible avec certaines des modifications apportées récemment au portail dans le cadre de la mise à jour 1910. Il n’existe aucune nouvelle fonctionnalité.

Elle prend également en charge la dernière version de l’API Azure Stack Hub (profil 2019-03-01-hybrid) et du module Azure Stack Hub PowerShell (1.8.0). Ainsi, pendant le déploiement et la mise à jour, vous n’avez pas à installer d’anciennes versions des modules.

Nous vous recommandons d’appliquer le correctif du fournisseur de ressources MySQL 1.1.47.0 après la mise à niveau d’Azure Stack Hub vers la version 1910.

## <a name="known-issues"></a>Problèmes connus

Lors de la [rotation du certificat](azure-stack-mysql-resource-provider-maintain.md#secrets-rotation) pour les systèmes intégrés Azure Stack Hub, l’argument KeyVaultPfxPassword est obligatoire, même si vous n’envisagez pas de mettre à jour le mot de passe du certificat Key Vault.

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur le déploiement d'un fournisseur de ressources MySQL](azure-stack-mysql-resource-provider.md).
- [Préparer le déploiement du fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).
- [Mettre à niveau le fournisseur de ressources MySQL à partir d’une version précédente](azure-stack-mysql-resource-provider-update.md).
