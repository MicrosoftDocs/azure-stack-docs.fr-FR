---
title: Notes de publication de la version 1.1.30.0 du fournisseur de ressources MySQL Azure Stack | Microsoft Docs
description: Découvrez le contenu de la dernière mise à jour du fournisseur de ressources MySQL Azure Stack, notamment les problèmes connus et l'emplacement de téléchargement.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 020fc2f56fe73ab87212ee61496722e8c179e17a
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308285"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>Notes de publication de la version 1.1.33.0 du fournisseur de ressources MySQL

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Ces notes de publication décrivent les améliorations et les problèmes connus liés à la version 1.1.33.0 du fournisseur de ressources MySQL.

## <a name="build-reference"></a>Référence de build
Téléchargez le binaire du fournisseur de ressources MySQL, puis exécutez le fichier auto-extracteur pour extraire le contenu dans un répertoire temporaire. Le fournisseur de ressources possède une build Azure Stack minimale correspondante. La version minimale d'Azure Stack requise pour installer cette version du fournisseur de ressources MySQL est mentionnée ci-dessous :

> |Version minimale d’Azure Stack|Version du fournisseur de ressources MySQL|
> |-----|-----|
> |Version 1808 (1.1808.0.97)|[MySQL RP version 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Appliquez la mise à jour minimale prise en charge d'Azure Stack sur votre système intégré Azure Stack ou déployez le Kit de développement Azure Stack (ASDK) le plus récent avant de déployer la dernière version du fournisseur de ressources MySQL.

## <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs
Cette version du fournisseur de ressources MySQL Azure Stack inclut les améliorations et correctifs suivants :

### <a name="fixes"></a>Correctifs
- **L’extension du portail du fournisseur de ressources MySQL risque de choisir le mauvais abonnement**. Le fournisseur de ressources MySQL utilise des appels Azure Resource Manager pour déterminer le premier abonnement d’administration de service à utiliser, qui risque de ne pas être l’*abonnement de fournisseur par défaut*. Si c’est le cas, le fournisseur de ressources MySQL ne fonctionne pas normalement. 

- **Le serveur d’hébergement MySQL ne liste pas les bases de données hébergées.** Les bases de données créées par l’utilisateur risquent de ne pas être listées lors de l’affichage des ressources de locataire des serveurs d’hébergement MySQL.

- **Le déploiement du fournisseur de ressources MySQL précédent (1.1.30.0) peut échouer si TLS 1.2 n’est pas activé**. Nous avons mis à jour le fournisseur de ressources MySQL 1.1.33.0 pour activer TLS 1.2 lors du déploiement du fournisseur de ressources, de la mise à jour du fournisseur de ressources ou de la rotation des secrets. 

- **La rotation des secrets du fournisseur de ressources MySQL échoue**. Nous avons résolu le problème et le code d’erreur est maintenant le suivant lors de la rotation des secrets : `New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Problèmes connus 

- **Une heure entière peut être nécessaire avant que les références SKU MySQL n’apparaissent dans le portail**. Il peut se passer une heure avant que les références SKU nouvellement créées soient visibles et utilisables lors de la création de nouvelles bases de données MySQL. 

    **Solution de contournement** : Aucune.

- **Connexions MySQL réutilisées**. Toute tentative de création d'une nouvelle connexion MySQL avec le même nom d'utilisateur qu'une connexion existante associée au même abonnement entraînera la réutilisation de la même connexion et du mot de passe existant. 

    **Solution de contournement** : utilisez des noms d’utilisateurs différents lors de la création de nouvelles connexions associées au même abonnement, ou créez des connexions avec le même nom d’utilisateur mais sous des abonnements différents.

- **Les connexions MySQL partagées provoquent une incohérence au niveau des données**. Si une connexion MySQL est partagée avec plusieurs bases de données SQL associées au même abonnement, la modification du mot de passe de connexion entraînera une incohérence au niveau des données.

    **Solution de contournement** : utilisez toujours des connexions distinctes pour les différentes bases de données associées à un même abonnement.


### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Problèmes connus des administrateurs cloud utilisant Azure Stack
Reportez-vous à la documentation des [notes de publication d’Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur le déploiement d'un fournisseur de ressources MySQL](azure-stack-mysql-resource-provider.md).

[Préparer le déploiement du fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Mettre à niveau le fournisseur de ressources MySQL à partir d’une version précédente](azure-stack-mysql-resource-provider-update.md). 