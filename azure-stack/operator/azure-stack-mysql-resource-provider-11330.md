---
title: Notes de publication de la version 1.1.33.0 du fournisseur de ressources MySQL Azure Stack Hub
description: Consultez les notes de publication pour découvrir les nouveautés incluses dans la mise à jour 1.1.33.0 du fournisseur de ressources MySQL Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 4cab915bc4b824c1a178d94aaa571ca71c9c948b
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77698910"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>Notes de publication de la version 1.1.33.0 du fournisseur de ressources MySQL

Ces notes de publication décrivent les améliorations et les problèmes connus liés à la version 1.1.33.0 du fournisseur de ressources MySQL.

## <a name="build-reference"></a>Référence de build
Téléchargez le binaire du fournisseur de ressources MySQL, puis exécutez le fichier auto-extracteur pour extraire le contenu dans un répertoire temporaire. Le fournisseur de ressources dispose d'une build Azure Stack Hub minimale correspondante. La version minimale d’Azure Stack Hub requise pour installer cette version du fournisseur de ressources MySQL est mentionnée ci-dessous :

> |Version minimale d'Azure Stack Hub|Version du fournisseur de ressources MySQL|
> |-----|-----|
> |Version 1808 (1.1808.0.97)|[MySQL RP version 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Appliquez la mise à jour minimale prise en charge d’Azure Stack Hub à votre système intégré Azure Stack Hub, ou déployez le Kit de développement Azure Stack Hub (ASDK) le plus récent avant de déployer la dernière version du fournisseur de ressources MySQL.

## <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs
Cette version du fournisseur de ressources MySQL Azure Stack Hub inclut les améliorations et correctifs suivants :

### <a name="fixes"></a>Correctifs

- **L’extension du portail du fournisseur de ressources MySQL risque de choisir le mauvais abonnement**. Le fournisseur de ressources MySQL utilise des appels Azure Resource Manager pour déterminer le premier abonnement d’administration de service à utiliser, qui risque de ne pas être l’*abonnement de fournisseur par défaut*. Si c’est le cas, le fournisseur de ressources MySQL ne fonctionne pas normalement.

- **Le serveur d’hébergement MySQL ne liste pas les bases de données hébergées.** Les bases de données créées par l’utilisateur risquent de ne pas être listées lors de l’affichage des ressources de locataire des serveurs d’hébergement MySQL.

- **Le déploiement du fournisseur de ressources MySQL précédent (1.1.30.0) peut échouer si TLS 1.2 n’est pas activé**. Nous avons mis à jour le fournisseur de ressources MySQL 1.1.33.0 pour activer TLS 1.2 lors du déploiement du fournisseur de ressources, de la mise à jour du fournisseur de ressources ou de la rotation des secrets.

- **La rotation des secrets du fournisseur de ressources MySQL échoue**. Nous avons résolu un problème lié à l’apparition du code d’erreur suivant lors de la rotation des secrets : `New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Problèmes connus

- **Une heure entière peut être nécessaire avant que les références SKU MySQL n’apparaissent dans le portail**. Il peut se passer une heure avant que les références SKU nouvellement créées soient visibles et utilisables lors de la création de nouvelles bases de données MySQL. 

    **Solution de contournement** : aucune.

- **Connexions MySQL réutilisées**. Toute tentative de création d'une nouvelle connexion MySQL avec le même nom d'utilisateur qu'une connexion existante associée au même abonnement entraînera la réutilisation de la même connexion et du mot de passe existant.

    **Solution de contournement** : utilisez des noms d'utilisateur différents lors de la création de nouvelles connexions associées au même abonnement, ou créez des connexions avec le même nom d'utilisateur mais sous des abonnements différents.

- **Les connexions MySQL partagées provoquent une incohérence au niveau des données**. Si une connexion MySQL est partagée avec plusieurs bases de données SQL associées au même abonnement, la modification du mot de passe de connexion entraînera une incohérence au niveau des données.

    **Solution de contournement** : utilisez toujours des connexions distinctes pour les différentes bases de données associées à un même abonnement.


### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Problèmes connus pour les administrateurs de cloud utilisant Azure Stack Hub
Voir la documentation dans les [notes de publication d’Azure Stack Hub](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur le déploiement d'un fournisseur de ressources MySQL](azure-stack-mysql-resource-provider.md).

[Préparer le déploiement du fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Mettre à niveau le fournisseur de ressources MySQL à partir d’une version précédente](azure-stack-mysql-resource-provider-update.md). 