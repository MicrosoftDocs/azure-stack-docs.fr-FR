---
title: Notes de publication de la version 1.1.30.0 du fournisseur de ressources SQL Azure Stack | Microsoft Docs
description: Découvrez le contenu de la dernière mise à jour du fournisseur de ressources SQL Azure Stack, notamment les problèmes connus et l'emplacement de téléchargement.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 22525fc9109bd2c03e28c4ca34b60c7a1f2585fe
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618056"
---
# <a name="sql-resource-provider-11300-release-notes"></a>Notes de publication de la version 1.1.30.0 du fournisseur de ressources SQL

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Ces notes de publication décrivent les améliorations et les problèmes connus liés à la version 1.1.30.0 du fournisseur de ressources SQL.

## <a name="build-reference"></a>Référence de build
Téléchargez le binaire du fournisseur de ressources SQL, puis exécutez le fichier auto-extracteur pour extraire le contenu dans un répertoire temporaire. Le fournisseur de ressources possède une build Azure Stack minimale correspondante. La version minimale d'Azure Stack requise pour installer cette version du fournisseur de ressources SQL est mentionnée ci-dessous :

> |Version minimale d’Azure Stack|Version du fournisseur de ressources SQL|
> |-----|-----|
> |Version 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Appliquez la mise à jour minimale prise en charge d'Azure Stack sur votre système intégré Azure Stack ou déployez le Kit de développement Azure Stack (ASDK) le plus récent avant de déployer la dernière version du fournisseur de ressources SQL.

## <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs
Cette version du fournisseur de ressources SQL Azure Stack inclut les améliorations et correctifs suivants :

- **Données de télémétrie activées pour les déploiements du fournisseur de ressources SQL**. La collecte des données de télémétrie a été activée pour les déploiements du fournisseur de ressources SQL. Les données de télémétrie collectées englobent les données de déploiement du fournisseur de ressources, les heures de début et de fin, l'état de sortie, les messages de sortie et les détails des erreurs (le cas échéant).

- **Mise à jour du chiffrement TLS 1.2**. Activation de la prise en charge de TLS 1.2 uniquement pour la communication du fournisseur de ressources avec les composants Azure Stack internes. 

### <a name="fixes"></a>Correctifs

- **Compatibilité Azure Stack PowerShell du fournisseur de ressources SQL**. Le fournisseur de ressources SQL a été mis à jour pour fonctionner avec le profil Azure Stack 2018-03-01-hybrid PowerShell et pour assurer la compatibilité avec AzureRM 1.3.0 et versions ultérieures.

- **Panneau de modification du mot de passe de connexion SQL**. Problème résolu : le mot de passe ne pouvait pas être changé sur le panneau de modification du mot de passe. Liens supprimés des notifications de changement de mot de passe.

- **Mise à jour du panneau des paramètres du serveur d'hébergement SQL**. Problème résolu : le panneau des paramètres était à tort intitulé « Mot de passe ».

## <a name="known-issues"></a>Problèmes connus 

- **Une heure entière peut être nécessaire avant que les références SKU SQL n’apparaissent dans le portail**. Il peut se passer une heure avant que les références SKU nouvellement créées soient visibles et utilisables lors de la création de nouvelles bases de données SQL. 

    **Solution de contournement** : Aucune.

- **Connexions SQL réutilisées**. Toute tentative de création d'une nouvelle connexion SQL avec le même nom d'utilisateur qu'une connexion existante associée au même abonnement entraînera la réutilisation de la même connexion et du mot de passe existant. 

    **Solution de contournement** : utilisez des noms d’utilisateur différents lors de la création de nouvelles connexions associées au même abonnement, ou créez des connexions avec le même nom d’utilisateur mais sous des abonnements différents.

- **Les connexions SQL partagées provoquent une incohérence au niveau des données**. Si une connexion SQL est partagée avec plusieurs bases de données SQL associées au même abonnement, la modification du mot de passe de connexion entraînera une incohérence au niveau des données.

    **Solution de contournement** : utilisez toujours des connexions distinctes pour les différentes bases de données associées à un même abonnement.

- **Configuration requise pour la prise en charge de TLS 1.2**. Si vous tentez de déployer ou de mettre à jour le fournisseur de ressources SQL à partir d’un ordinateur où TLS 1.2 n’est pas activé, l’opération peut échouer. Exécutez la commande PowerShell suivante sur l’ordinateur utilisé pour déployer ou mettre à jour le fournisseur de ressources afin de vérifier que TLS 1.2 est signalé comme étant pris en charge :

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Si **Tls12** ne figure pas dans la sortie de la commande, TLS 1.2 n’est pas activé sur l’ordinateur.

    **Solution de contournement** : exécutez la commande PowerShell suivante pour activer TLS 1.2, puis commencez le déploiement du fournisseur de ressources ou mettez à jour le script à partir de la même session PowerShell :

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
- **Le fournisseur de ressources SQL ne parvient pas à ajouter l’écouteur SQL Server Always On**. Lorsque vous utilisez l’adresse IP de l’écouteur SQL Server Always On, la machine virtuelle du fournisseur de ressources SQL ne peut pas résoudre le nom d’hôte de l’écouteur.

    **Solution de contournement** : Vérifiez que DNS fonctionne correctement pour résoudre l’adresse IP de l’écouteur en nom d’hôte de l’écouteur.
    
### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Problèmes connus des administrateurs cloud utilisant Azure Stack
Reportez-vous à la documentation des [notes de publication d’Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur le déploiement d'un fournisseur de ressources SQL](azure-stack-sql-resource-provider.md).

[Préparer le déploiement du fournisseur de ressources SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Mettre à niveau le fournisseur de ressources SQL à partir d’une version précédente](azure-stack-sql-resource-provider-update.md). 