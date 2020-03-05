---
title: Notes de publication de la version 1.1.30.0 du fournisseur de ressources MySQL Azure Stack Hub
description: Consultez les notes de publication pour découvrir les nouveautés incluses dans la mise à jour 1.1.30.0 du fournisseur de ressources MySQL Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: jiahan
ms.lastreviewed: 12/10/2019
ms.openlocfilehash: 710b5d48e4ce906eb357c5e17cff4132bcd747ba
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698927"
---
# <a name="mysql-resource-provider-11300-release-notes"></a>Notes de publication 1.1.30.0 de fournisseur de ressources MySQL

Ces notes de publication décrivent les améliorations et les problèmes connus liés à la version 1.1.30.0 du fournisseur de ressources MySQL.

## <a name="build-reference"></a>Référence de build
Téléchargez le binaire du fournisseur de ressources MySQL, puis exécutez le fichier auto-extracteur pour extraire le contenu dans un répertoire temporaire. Le fournisseur de ressources dispose d'une build Azure Stack Hub minimale correspondante. La version minimale d’Azure Stack Hub requise pour installer cette version du fournisseur de ressources MySQL est mentionnée ci-dessous :

> |Version minimale d'Azure Stack Hub|Version du fournisseur de ressources MySQL|
> |-----|-----|
> |Mise à jour Azure Stack Hub 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Appliquez la mise à jour minimale prise en charge d’Azure Stack Hub à votre système intégré Azure Stack Hub, ou déployez le Kit de développement Azure Stack Hub (ASDK) le plus récent avant de déployer la dernière version du fournisseur de ressources MySQL.

## <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs
Cette version du fournisseur de ressources MySQL Azure Stack Hub inclut les améliorations et correctifs suivants :

- **Données de télémétrie activées pour les déploiements du fournisseur de ressources MySQL**. La collecte des données de télémétrie a été activée pour les déploiements du fournisseur de ressources MySQL. Les données de télémétrie collectées englobent les données de déploiement du fournisseur de ressources, les heures de début et de fin, l'état de sortie, les messages de sortie et les détails des erreurs (le cas échéant).

- **Mise à jour du chiffrement TLS 1.2**. Activation de la prise en charge de TLS 1.2 uniquement pour la communication du fournisseur de ressources avec les composants Azure Stack Hub internes. 

### <a name="fixes"></a>Correctifs

- **Compatibilité avec Azure Stack Hub PowerShell du fournisseur de ressources MySQL**. Le fournisseur de ressources MySQL a été mis à jour pour fonctionner avec le profil Azure Stack Hub 2018-03-01-hybrid PowerShell et pour assurer la compatibilité avec AzureRM 1.3.0 et versions ultérieures.

- **Panneau de modification du mot de passe de connexion MySQL**. Problème résolu : le mot de passe ne pouvait pas être changé sur le panneau de modification du mot de passe. Liens supprimés des notifications de changement de mot de passe.

## <a name="known-issues"></a>Problèmes connus

- **Une heure entière peut être nécessaire avant que les références SKU MySQL n’apparaissent dans le portail**. Il peut se passer une heure avant que les références SKU nouvellement créées soient visibles et utilisables lors de la création de nouvelles bases de données MySQL.

    **Solution de contournement** : Aucun.

- **Connexions MySQL réutilisées**. Toute tentative de création d'une nouvelle connexion MySQL avec le même nom d'utilisateur qu'une connexion existante associée au même abonnement entraînera la réutilisation de la même connexion et du mot de passe existant.

    **Solution de contournement** : utilisez des noms d’utilisateurs différents lors de la création de nouvelles connexions associées au même abonnement, ou créez des connexions avec le même nom d’utilisateur mais sous des abonnements différents.

- **Configuration requise pour la prise en charge de TLS 1.2**. Si vous tentez de déployer ou de mettre à jour le fournisseur de ressources MySQL à partir d’un ordinateur où TLS 1.2 n’est pas activé, l’opération peut échouer. Exécutez la commande PowerShell suivante sur l’ordinateur utilisé pour déployer ou mettre à jour le fournisseur de ressources afin de vérifier que TLS 1.2 est signalé comme étant pris en charge :

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Si **Tls12** ne figure pas dans la sortie de la commande, TLS 1.2 n’est pas activé sur l’ordinateur.

    **Solution de contournement** : exécutez la commande PowerShell suivante pour activer TLS 1.2, puis commencez le déploiement du fournisseur de ressources ou mettez à jour le script à partir de la même session PowerShell :

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
 
### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Problèmes connus des administrateurs cloud utilisant Azure Stack Hub
Voir la documentation dans les [notes de publication d’Azure Stack Hub](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur le déploiement d'un fournisseur de ressources MySQL](azure-stack-mysql-resource-provider.md).

[Préparer le déploiement du fournisseur de ressources MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Mettre à niveau le fournisseur de ressources MySQL à partir d’une version précédente](azure-stack-mysql-resource-provider-update.md). 