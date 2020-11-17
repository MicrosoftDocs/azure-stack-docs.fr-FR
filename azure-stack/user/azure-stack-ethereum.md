---
title: Déployer un réseau blockchain Ethereum dans Azure Stack Hub
description: Tutoriel utilisant des modèles de solution personnalisés pour déployer et configurer un réseau blockchain Ethereum de consortium sur Azure Stack Hub.
author: PatAltimore
ms.author: patricka
ms.date: 10/07/2020
ms.topic: tutorial
ms.reviewer: seyadava
ms.lastreviewed: 10/07/2020
ms.openlocfilehash: f409a2629243c0eb1cfe15ad857aa3e229832bb5
ms.sourcegitcommit: 7b189e5317b8fe5f8ad825565da3607a39a1b899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94493701"
---
# <a name="deploy-an-ethereum-blockchain-network-on-azure-stack-hub"></a>Déployer un réseau blockchain Ethereum sur Azure Stack Hub

Le modèle de solution Ethereum est conçu pour faciliter et accélérer le déploiement et la configuration d’un réseau blockchain Ethereum de consortium de plusieurs membres avec des connaissances minimales au sujet d’Azure et d’Ethereum.

En quelques entrées d’utilisateur et avec un déploiement par clic simple via le portail locataire Azure Stack Hub, chaque membre peut fournir son empreinte réseau. L’empreinte réseau de chaque membre se compose de trois éléments :

1. Un ensemble de nœuds de transaction à charge équilibrée avec lequel une application ou un utilisateur peut interagir pour envoyer des transactions.
2. Un ensemble de nœuds d’exploration de données pour enregistrer des transactions.
3. Une appliance virtuelle réseau (NVA).

Une étape de connexion ultérieure connecte les NVA pour créer un réseau blockchain de plusieurs membres entièrement configuré.

Pour configurer :

- Choisir une architecture de déploiement.
- Déployer un réseau autonome, de leader de consortium ou de membre de consortium.

## <a name="prerequisites"></a>Prérequis

Téléchargez les derniers articles [sur la Place de marché](../operator/azure-stack-download-azure-marketplace-item.md) :

- Ubuntu Server 16.04 LTS
- Windows Server 2016
- Script personnalisé pour Linux 2.0
- Extension de script personnalisé pour Windows

Pour plus d’informations sur les scénarios blockchain, consultez [Modèle de solution de consortium de preuve d’autorité Ethereum](/azure/blockchain/templates/ethereum-poa-deployment).

## <a name="deployment-architecture"></a>Architecture de déploiement

Ce modèle de solution peut déployer un réseau de consortium Ethereum à un ou plusieurs membres. Le réseau virtuel est connecté dans une topologie de chaîne à l’aide de ressources et de connexion et de l’appliance de réseau virtuel.

Le modèle peut déployer le consortium Ethereum pour les leaders et les membres de plusieurs façons. Voici celles que nous avons testées :

- Sur une instance Azure Stack Hub à plusieurs nœuds, avec Azure AD ou AD FS, déployez le leader ou le membre à l’aide du même abonnement ou d’abonnements différents.
- Sur une instance Azure Stack Hub à nœud unique (avec Azure AD), déployez le leader et le membre à l’aide du même abonnement.

## <a name="standalone-and-consortium-leader-deployment"></a>Déploiement des leaders autonomes et de consortium

Le modèle de leader de consortium configure l’empreinte du premier membre dans le réseau. 

1. Téléchargez le [modèle de leader à partir de GitHub](https://aka.ms/aa6z619).
1. Dans le portail du locataire Azure Stack Hub, sélectionnez **+ Créer une ressource > Template deployment** pour effectuer un déploiement à partir d’un modèle personnalisé.
1. Sélectionnez **Créer votre propre modèle dans l’éditeur** pour modifier le nouveau modèle personnalisé.
1. Dans le volet d’édition sur la droite, copiez et collez le JSON du modèle de leader que vous avez précédemment téléchargé.
    
    [![Modifier le modèle avec le modèle de leader collé](./media/azure-stack-ethereum/edit-leader-template.png)](./media/azure-stack-ethereum/edit-leader-template.png#lightbox)

1. Sélectionnez **Enregistrer**.
1. Sous l’onglet **Informations de base**, fournissez les paramètres suivants.

    Nom du paramètre | Description | Exemple de valeur
    ---------------|-------------|-------------
    Abonnement | Abonnement vers lequel vous déployez le réseau de consortium. | Abonnement Consommation
    Resource group | Groupe de ressources sur lequel déployez le réseau de consortium. | EthereumResources
    Région | Région Azure pour les ressources. | local
    Préfixe de nom | Chaîne utilisée comme base pour nommer les ressources déployées. Utilisez un maximum de six caractères alphanumériques. | eth
    Type d’authentification | Méthode d’authentification auprès de la machine virtuelle. Les valeurs autorisées sont le mot de passe ou la clé publique SSH. | Mot de passe
    Nom d’utilisateur administrateur | Nom d’utilisateur admin pour chaque machine virtuelle déployée. Utilisez entre un et 64 caractères. | gethadmin
    Mot de passe admin (Type d’authentification = Mot de passe)| Mot de passe du compte admin pour chacune des machines virtuelles déployées. Le mot de passe doit contenir trois des éléments requis suivants : une majuscule, une minuscule, un chiffre et un caractère spécial. <br />Initialement, toutes les machines virtuelles ont le même mot de passe, que vous pouvez modifier après le provisionnement. Utilisez entre 12 et 72 caractères. |
    Clé SSH admin (type d’authentification = sshPublicKey) | Chaîne de la clé publique RSA de l’interpréteur de commandes sécurisé utilisée pour la connexion distante. |
    Bloc de genèse | Chaîne JSON représentant le bloc de genèse personnalisé.  La valeur de ce paramètre est facultative. |
    Mot de passe de compte Ethereum | Mot de passe admin utilisé pour sécuriser le compte Ethereum. |
    Phrase secrète de compte Ethereum | Phrase secrète utilisée pour générer la clé privée associée au compte Ethereum. Envisagez un mot de passe avec un caractère aléatoire suffisant pour garantir une clé privée forte. |
    ID de réseau Ethereum | ID de réseau du consortium. Utilisez une valeur comprise entre 5 et 999 999 999. | 72
    ID de membre du consortium | ID associé à chaque membre du réseau de consortium. Cet ID doit être unique dans le réseau. | 0
    Nombre de nœuds d’exploration de données | Nombre de nœuds d’exploration de données pour chaque membre du consortium. Utilisez une valeur comprise entre 2 et 15. | 2
    Taille de machine virtuelle des nœuds d’exploration de données | Taille de machine virtuelle des nœuds d’exploration de données. | Standard_A1
    Type de compte de stockage d’exploration de données | Performances de stockage des nœuds d’exploration de données. | Standard_LRS
    Nombre de nœuds de transaction | Nombre de nœuds de transaction avec équilibrage de charge. Utilisez une valeur comprise entre 1 et 5. | 1
    Taille de machine virtuelle des nœuds de transaction | Taille de machine virtuelle des nœuds de transaction. | Standard_A1
    Type de compte de stockage de transaction | Performances de stockage des nœuds de transaction. | Standard_LRS
    URL de base | URL de base où obtenir les modèles de déploiement. Utilisez la valeur par défaut, sauf si vous souhaitez personnaliser les modèles de déploiement. |

1. Sélectionnez **Revoir + créer**. Après la validation, sélectionnez **Créer**.

Le déploiement peut prendre 20 minutes ou plus.

Une fois le déploiement terminé, consultez le résumé de déploiement pour **Microsoft.Template** dans la section du déploiement du groupe de ressources. Le résumé contient des valeurs de sortie utilisées pour joindre les membres du consortium.

Pour vérifier le déploiement du leader, accédez au site admin du leader. Vous trouverez l’adresse du site admin dans la section de sortie du déploiement **Microsoft.Template**.  

![Résumé du déploiement du leader](./media/azure-stack-ethereum/ethereum-node-status.png)

## <a name="joining-consortium-member-deployment"></a>Déploiement de la jointure d’un membre de consortium

1. Téléchargez le [modèle de membre consortium à partir de GitHub](https://aka.ms/aa6zkua).
1. Dans le portail du locataire Azure Stack Hub, sélectionnez **+ Créer une ressource > Template deployment** pour effectuer un déploiement à partir d’un modèle personnalisé.
1. Sélectionnez **Créer votre propre modèle dans l’éditeur** pour modifier le nouveau modèle personnalisé.
1. Dans le volet d’édition sur la droite, copiez et collez le JSON du modèle de membre de consortium que vous avez précédemment téléchargé.
1. Sélectionnez **Enregistrer**.
1. Sous l’onglet **Informations de base**, fournissez les paramètres suivants.

    Nom du paramètre | Description | Exemple de valeur
    ---------------|-------------|-------------
    Abonnement | Abonnement vers lequel vous déployez le réseau de consortium. | Abonnement Consommation
    Resource group | Groupe de ressources sur lequel déployez le réseau de consortium. | EthereumResources
    Région | Région Azure pour les ressources. | local
    Préfixe de nom | Chaîne utilisée comme base pour nommer les ressources déployées. Utilisez un maximum de six caractères alphanumériques. | eth
    Type d’authentification | Méthode d’authentification auprès de la machine virtuelle. Les valeurs autorisées sont le mot de passe ou la clé publique SSH. | Mot de passe
    Nom d’utilisateur administrateur | Nom d’utilisateur admin pour chaque machine virtuelle déployée. Utilisez entre un et 64 caractères. | gethadmin
    Mot de passe admin (Type d’authentification = Mot de passe)| Mot de passe du compte admin pour chacune des machines virtuelles déployées. Le mot de passe doit contenir trois des éléments requis suivants : une majuscule, une minuscule, un chiffre et un caractère spécial. <br />Initialement, toutes les machines virtuelles ont le même mot de passe, que vous pouvez modifier après le provisionnement. Utilisez entre 12 et 72 caractères. |
    Clé SSH admin (type d’authentification = sshPublicKey) | Chaîne de la clé publique RSA de l’interpréteur de commandes sécurisé utilisée pour la connexion distante. |
    Bloc de genèse | Chaîne JSON représentant le bloc de genèse personnalisé. La valeur de ce paramètre est facultative. |
    Mot de passe de compte Ethereum | Mot de passe admin utilisé pour sécuriser le compte Ethereum. |
    Phrase secrète de compte Ethereum | Phrase secrète utilisée pour générer la clé privée associée au compte Ethereum. Envisagez un mot de passe avec un caractère aléatoire suffisant pour garantir une clé privée forte. |
    ID de membre du consortium | ID associé à chaque membre du réseau de consortium. Cet ID doit être unique dans le réseau. | 0
    Nombre de nœuds d’exploration de données | Nombre de nœuds d’exploration de données pour chaque membre du consortium. Utilisez une valeur comprise entre 2 et 15. | 2
    Taille de machine virtuelle des nœuds d’exploration de données | Taille de machine virtuelle des nœuds d’exploration de données. | Standard_A1
    Type de compte de stockage d’exploration de données | Performances de stockage des nœuds d’exploration de données. | Standard_LRS
    Nombre de nœuds de transaction | Nombre de nœuds de transaction avec équilibrage de charge. Utilisez une valeur comprise entre 1 et 5. | 1
    Taille de machine virtuelle des nœuds de transaction | Taille de machine virtuelle des nœuds de transaction. | Standard_A1
    Type de compte de stockage de transaction | Performances de stockage des nœuds de transaction. | Standard_LRS
    Données de consortium | URL pointant vers les données de configuration de consortium pertinentes, fournies par le déploiement d’un autre membre. Vous trouverez cette valeur dans la sortie du déploiement du leader. |
    Espaces d’adressage du réseau virtuel de membre distant | Espace d’adressage du réseau virtuel du leader. Vous trouverez cette valeur dans la sortie du déploiement du leader. |
    IP publique de la NVA de membre distant | Adresse IP de la NVA du leader. Vous trouverez cette valeur dans la sortie du déploiement du leader. |
    Clé partagée de connexion | Clé secrète préétablie entre les membres du réseau de consortium qui établissent une connexion de passerelle. |
    URL de base | URL de base où obtenir les modèles de déploiement. Utilisez la valeur par défaut, sauf si vous souhaitez personnaliser les modèles de déploiement. |
1. Sélectionnez **Revoir + créer**. Après la validation, sélectionnez **Créer**.

Le déploiement peut prendre 20 minutes ou plus.

Une fois le déploiement terminé, consultez le résumé de déploiement pour **Microsoft.Template** dans la section du déploiement du groupe de ressources. Le résumé contient des valeurs de sortie utilisées pour connecter les membres du consortium.

Pour vérifier le déploiement du membre, parcourez le site d’administration du membre. Vous trouverez l’adresse du site admin dans la section de sortie du déploiement **Microsoft.Template**.

![Résumé du déploiement du membre](./media/azure-stack-ethereum/ethereum-node-status-2.png)

Comme on le voit dans l’image, le statut des nœuds du membre est **Non en cours d’exécution**. En effet, la connexion entre le membre et le leader n’est pas établie. La connexion entre le membre et le leader est une connexion bidirectionnelle. Lorsque vous déployez le membre, le modèle crée automatiquement la connexion du membre au leader. Pour créer la connexion du leader au membre, passez à l’étape suivante.

## <a name="connect-member-and-leader"></a>Connecter le membre et le leader

Ce modèle crée une connexion du leader à un membre à distance. 

1. Téléchargez le [modèle de connexion de membre et de leader à partir de GitHub](https://aka.ms/aa6zdyt).
1. Dans le portail du locataire Azure Stack Hub, sélectionnez **+ Créer une ressource > Template deployment** pour effectuer un déploiement à partir d’un modèle personnalisé.
1. Sélectionnez **Créer votre propre modèle dans l’éditeur** pour modifier le nouveau modèle personnalisé.
1. Dans le volet d’édition sur la droite, copiez et collez le JSON du modèle de membre de consortium que vous avez précédemment téléchargé.
1. Sélectionnez **Enregistrer**.
1. Fournissez les paramètres suivants.

    Nom du paramètre | Description | Exemple de valeur
    ---------------|-------------|-------------
    Abonnement | Abonnement vers lequel vous déployez le réseau de consortium. | Abonnement Consommation
    Resource group | Groupe de ressources sur lequel déployez le réseau de consortium. | EthereumResources
    Région | Région Azure pour les ressources. | local
    Préfixe du nom du membre | Chaîne utilisée comme base pour nommer les ressources déployées. Utilisez un maximum de six caractères alphanumériques. | eth
    Nom de la table de routage des membres | Nom de la table de routage du leader. Vous trouverez cette valeur dans la sortie du déploiement du leader. |
    Espaces d’adressage du réseau virtuel de membre distant | Espace d’adressage du membre. Vous trouverez cette valeur dans la sortie du déploiement du membre. |
    IP publique de la NVA de membre distant | Adresse IP de la NVA à laquelle se connecter. Vous trouverez cette valeur dans la sortie du déploiement du membre. |
    Clé partagée de connexion | Clé secrète préétablie entre les membres du réseau de consortium qui établissent une connexion.  |
    IP privée de la NVA du membre | Adresse IP de la NVA du membre. Vous trouverez cette valeur dans la sortie du déploiement du membre. |
    URL de base | URL de base où obtenir les modèles de déploiement. Utilisez la valeur par défaut, sauf si vous souhaitez personnaliser les modèles de déploiement. |
1. Sélectionnez **Revoir + créer**. Après la validation, sélectionnez **Créer**.

Une fois le déploiement terminé, l’établissement de la communication entre leader et le membre prend quelques minutes. Pour vérifier le déploiement, actualisez le site d’administration du membre. Le statut des nœuds du membre doit être En cours d’exécution.

![Vérifier le déploiement](./media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Ethereum et Azure, consultez [Technologie et applications blockchain](https://azure.microsoft.com/solutions/blockchain/).
