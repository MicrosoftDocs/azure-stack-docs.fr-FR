---
title: Notes de publication de la version 1.1.33.0 du fournisseur de ressources SQL Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Consultez les notes de publication concernant la mise à jour 1.1.33.0 du fournisseur de ressources SQL Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 024632c7dd80a7c028271ded7aea6a2b4ef46be0
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882888"
---
# <a name="sql-resource-provider-11330-release-notes"></a>Notes de publication de la version 1.1.33.0 du fournisseur de ressources SQL

Ces notes de publication décrivent les améliorations et les problèmes connus du fournisseur de ressources SQL version 1.1.33.0.

## <a name="build-reference"></a>Référence de build
Téléchargez le binaire du fournisseur de ressources SQL, puis exécutez le fichier auto-extracteur pour extraire le contenu dans un répertoire temporaire. Le fournisseur de ressources dispose d'une build Azure Stack Hub minimale correspondante. La version minimale d’Azure Stack Hub requise pour installer cette version du fournisseur de ressources SQL est mentionnée ci-dessous :

> |Version minimale d'Azure Stack Hub|Version du fournisseur de ressources SQL|
> |-----|-----|
> |Version 1808 (1.1808.0.97)|[SQL RP version 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Appliquez la mise à jour minimale prise en charge d’Azure Stack Hub sur votre système intégré Azure Stack Hub avant de déployer la dernière version du fournisseur de ressources SQL.

## <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs
Cette version du fournisseur de ressources SQL Azure Stack Hub inclut les améliorations et correctifs suivants :

### <a name="fixes"></a>Correctifs

- **L’extension du portail du fournisseur de ressources SQL risque de choisir le mauvais abonnement**. Le fournisseur de ressources SQL utilise des appels Azure Resource Manager pour déterminer le premier abonnement d’administration de service à utiliser, qui risque de ne pas être l’*abonnement de fournisseur par défaut*. Si c’est le cas, le fournisseur de ressources SQL ne fonctionne pas normalement.

- **Le serveur d’hébergement SQL ne liste pas les bases de données hébergées.** Les bases de données créées par l’utilisateur risquent de ne pas être listées lors de l’affichage des ressources de locataire des serveurs d’hébergement SQL.

- **Le déploiement du fournisseur de ressources SQL précédent (1.1.30.0) peut échouer si TLS 1.2 n’est pas activé**. Nous avons mis à jour le fournisseur de ressources SQL 1.1.33.0 pour activer TLS 1.2 lors du déploiement du fournisseur de ressources, de la mise à jour du fournisseur de ressources ou de la rotation des secrets.

- **La rotation des secrets du fournisseur de ressources SQL échoue**. Nous avons résolu le problème et le code d’erreur est maintenant le suivant lors de la rotation des secrets : `New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Problèmes connus

- **Une heure entière peut être nécessaire avant que les références SKU SQL n’apparaissent dans le portail**. Il peut se passer une heure avant que les références SKU nouvellement créées soient visibles et utilisables lors de la création de nouvelles bases de données SQL.

    **Solution de contournement** : Aucun.

- **Connexions SQL réutilisées**. Toute tentative de création d'une nouvelle connexion SQL avec le même nom d'utilisateur qu'une connexion existante associée au même abonnement entraînera la réutilisation de la même connexion et du mot de passe existant.

    **Solution de contournement** : utilisez des noms d’utilisateurs différents lors de la création de nouvelles connexions associées au même abonnement, ou créez des connexions avec le même nom d’utilisateur mais sous des abonnements différents.

- **Les connexions SQL partagées provoquent une incohérence au niveau des données**. Si une connexion SQL est partagée avec plusieurs bases de données SQL associées au même abonnement, la modification du mot de passe de connexion entraînera une incohérence au niveau des données.

    **Solution de contournement** : utilisez toujours des connexions distinctes pour les différentes bases de données associées à un même abonnement.

- **Le fournisseur de ressources SQL ne parvient pas à ajouter l’écouteur SQL Server Always On**. Lorsque vous utilisez l’adresse IP de l’écouteur SQL Server AlwaysOn, la machine virtuelle du fournisseur de ressources SQL ne peut pas résoudre le nom d’hôte de l’écouteur.

    **Solution de contournement** : Vérifiez que DNS fonctionne correctement pour résoudre l’adresse IP de l’écouteur en nom d’hôte de l’écouteur.

### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Problèmes connus des administrateurs cloud utilisant Azure Stack Hub
Reportez-vous à la documentation relative aux [notes de publication d’Azure Stack Hub](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur le déploiement d'un fournisseur de ressources SQL](azure-stack-sql-resource-provider.md).

[Préparer le déploiement du fournisseur de ressources SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Mettre à niveau le fournisseur de ressources SQL à partir d’une version précédente](azure-stack-sql-resource-provider-update.md).