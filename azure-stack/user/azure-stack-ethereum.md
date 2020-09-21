---
title: Déployer un réseau blockchain Ethereum dans Azure Stack Hub
description: Tutoriel utilisant des modèles de solution personnalisés pour déployer et configurer un réseau blockchain Ethereum de consortium sur Azure Stack Hub.
author: PatAltimore
ms.author: patricka
ms.date: 06/03/2019
ms.topic: tutorial
ms.reviewer: seyadava
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: 514baec95a83ef8cc04391e61c37f144ebfaf643
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90571913"
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

## <a name="deployment-use-cases"></a>Cas d’utilisation de déploiement

Le modèle peut déployer le consortium Ethereum pour les leaders et les membres de plusieurs façons. Voici celles que nous avons testées :

- Sur une instance Azure Stack Hub à plusieurs nœuds, avec Azure AD ou AD FS, déployez le leader ou le membre à l’aide du même abonnement ou d’abonnements différents.
- Sur une instance Azure Stack Hub à nœud unique (avec Azure AD), déployez le leader et le membre à l’aide du même abonnement.

### <a name="standalone-and-consortium-leader-deployment"></a>Déploiement des leaders autonomes et de consortium

Le modèle de leader de consortium configure l’empreinte du premier membre dans le réseau. 

1. Téléchargez le [modèle de leader à partir de GitHub](https://aka.ms/aa6z619).
2. Dans le portail du locataire Azure Stack Hub, sélectionnez **+ Créer une ressource > Template deployment** pour effectuer un déploiement à partir d’un modèle personnalisé.
3. Sélectionnez **Modifier un modèle** pour modifier le nouveau modèle personnalisé.
4. Dans le volet d’édition sur la droite, copiez et collez le JSON du modèle de leader que vous avez précédemment téléchargé.
    
    ![Modifier un modèle de leader](./media/azure-stack-ethereum/edit-leader-template.png)

5. Sélectionnez **Enregistrer**.
6. Sélectionnez **Modifier les paramètres** et renseignez les paramètres de modèle de votre déploiement.
    
    ![Modifier les paramètres du modèle de leader](./media/azure-stack-ethereum/edit-leader-parameters.png)

    Nom du paramètre | Description | Valeurs autorisées | Exemple de valeur
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Chaîne utilisée comme base pour nommer les ressources déployées. | Caractères alphanumériques (entre 1 et 6). | eth
    AUTHTYPE | Méthode d’authentification auprès de la machine virtuelle. | Mot de passe ou clé publique SSH. | Mot de passe
    ADMINUSERNAME | Nom d’utilisateur admin pour chaque machine virtuelle déployée. | Entre 1 et 64 caractères. | gethadmin
    ADMINPASSWORD (type d’authentification = mot de passe)| Mot de passe du compte admin pour chacune des machines virtuelles déployées. Le mot de passe doit contenir trois des éléments requis suivants : une majuscule, une minuscule, un chiffre et un caractère spécial. <br />Initialement, toutes les machines virtuelles ont le même mot de passe, que vous pouvez modifier après le provisionnement.|Entre 12 et 72 caractères. |
    ADMINSSHKEY (type d’authentification = sshPublicKey) | Clé de shell sécurisée utilisée pour la session à distance. | |
    GENESISBLOCK | Chaîne JSON représentant le bloc de genèse personnalisé.  La valeur de ce paramètre est facultative. | |
    ETHEREUMACCOUNTPSSWD | Mot de passe admin utilisé pour sécuriser le compte Ethereum. | |
    ETHEREUMACCOUNTPASSPHRASE | Phrase secrète utilisée pour générer la clé privée associée au compte Ethereum. | |
    ETHEREUMNETWORKID | ID de réseau du consortium. | Utilisez une valeur comprise entre 5 et 999 999 999. | 72
    CONSORTIUMMEMBERID | ID associé à chaque membre du réseau de consortium.   | Cet ID doit être unique dans le réseau. | 0
    NUMMININGNODES | Nombre de nœuds d’exploration de données. | Entre 2 et 15. | 2
    MNNODEVMSIZE | Taille de machine virtuelle des nœuds d’exploration de données. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Performances de stockage des nœuds d’exploration de données. | | Standard_LRS
    NUMTXNODES | Nombre de nœuds de transaction. | Entre 1 et 5. | 1
    TXNODEVMSIZE | Taille de machine virtuelle des nœuds de transaction. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Performances de stockage des nœuds de transaction. | | Standard_LRS
    BASEURL | URL de base à partir de laquelle obtenir les modèles de déploiement. | Utilisez la valeur par défaut, sauf si vous souhaitez personnaliser les modèles de déploiement. | 

7. Sélectionnez **OK**.
8. Dans **Déploiement personnalisé**, spécifiez **Abonnement**, **Groupe de ressources** et **Emplacement du groupe de ressources**.
    
    ![Paramètres de déploiement de leader](./media/azure-stack-ethereum/leader-deployment-parameters.png)

    Nom du paramètre | Description | Valeurs autorisées | Exemple de valeur
    ---------------|-------------|----------------|-------------
    Abonnement | Abonnement vers lequel vous déployez le réseau de consortium. | | Abonnement Consommation
    Groupe de ressources | Groupe de ressources sur lequel déployez le réseau de consortium. | | EthereumResources
    Emplacement | Région Azure du groupe de ressources. | | local

8. Sélectionnez **Create** (Créer).

Le déploiement peut prendre 20 minutes ou plus.

Une fois le déploiement terminé, consultez le résumé de déploiement pour **Microsoft.Template** dans la section du déploiement du groupe de ressources. Le résumé contient des valeurs de sortie utilisées pour joindre les membres du consortium.

Pour vérifier le déploiement du leader, accédez au site admin du leader. Vous trouverez l’adresse du site admin dans la section de sortie du déploiement **Microsoft.Template**.  

![Résumé du déploiement du leader](./media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>Déploiement de la jointure d’un membre de consortium

1. Téléchargez le [modèle de membre consortium à partir de GitHub](https://aka.ms/aa6zkua).
2. Dans le portail du locataire Azure Stack Hub, sélectionnez **+ Créer une ressource > Template deployment** pour effectuer un déploiement à partir d’un modèle personnalisé.
3. Sélectionnez **Modifier un modèle** pour modifier le nouveau modèle personnalisé.
4. Dans le volet d’édition sur la droite, copiez et collez le JSON du modèle de leader que vous avez précédemment téléchargé.
5. Sélectionnez **Enregistrer**.
6. Sélectionnez **Modifier les paramètres** et renseignez les paramètres de modèle de votre déploiement.

    Nom du paramètre | Description | Valeurs autorisées | Exemple de valeur
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Chaîne utilisée comme base pour nommer les ressources déployées. | Caractères alphanumériques (entre 1 et 6). | eth
    AUTHTYPE | Méthode d’authentification auprès de la machine virtuelle. | Mot de passe ou clé publique SSH. | Mot de passe
    ADMINUSERNAME | Nom d’utilisateur admin pour chaque machine virtuelle déployée. | Entre 1 et 64 caractères. | gethadmin
    ADMINPASSWORD (type d’authentification = mot de passe)| Mot de passe du compte admin pour chacune des machines virtuelles déployées. Le mot de passe doit contenir trois des éléments requis suivants : une majuscule, une minuscule, un chiffre et un caractère spécial. <br />Initialement, toutes les machines virtuelles ont le même mot de passe, que vous pouvez modifier après le provisionnement.|Entre 12 et 72 caractères. |
    ADMINSSHKEY (type d’authentification = sshPublicKey) | Clé de shell sécurisée utilisée pour la session à distance. | |
    CONSORTIUMMEMBERID | ID associé à chaque membre du réseau de consortium.   | Cet ID doit être unique dans le réseau. | 0
    NUMMININGNODES | Nombre de nœuds d’exploration de données. | Entre 2 et 15. | 2
    MNNODEVMSIZE | Taille de machine virtuelle des nœuds d’exploration de données. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Performances de stockage des nœuds d’exploration de données. | | Standard_LRS
    NUMTXNODES | Nombre de nœuds de transaction. | Entre 1 et 5. | 1
    TXNODEVMSIZE | Taille de machine virtuelle des nœuds de transaction. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Performances de stockage des nœuds de transaction. | | Standard_LRS
    CONSORTIUMDATA | URL pointant vers les données de configuration de consortium pertinentes, fournies par le déploiement d’un autre membre. Vous trouverez cette valeur dans la sortie du déploiement du leader. | |
    REMOTEMEMBERVNETADDRESSSPACE | Adresse IP de la NVA du leader. Vous trouverez cette valeur dans la sortie du déploiement du leader. | | 
    REMOTEMEMBERNVAPUBLICIP | Adresse IP de la NVA du leader. Vous trouverez cette valeur dans la sortie du déploiement du leader. | | 
    CONNECTIONSHAREDKEY | Clé secrète préétablie entre les membres du réseau de consortium qui établissent une connexion. | |
    BASEURL | URL de base pour le modèle. | Utilisez la valeur par défaut, sauf si vous souhaitez personnaliser les modèles de déploiement. | 

7. Sélectionnez **OK**.
8. Dans **Déploiement personnalisé**, spécifiez **Abonnement**, **Groupe de ressources** et **Emplacement du groupe de ressources**.

    Nom du paramètre | Description | Valeurs autorisées | Exemple de valeur
    ---------------|-------------|----------------|-------------
    Abonnement | Abonnement vers lequel vous déployez le réseau de consortium. | | Abonnement Consommation
    Groupe de ressources | Groupe de ressources sur lequel déployez le réseau de consortium. | | MemberResources
    Emplacement | Région Azure du groupe de ressources. | | local

8. Sélectionnez **Create** (Créer).

Le déploiement peut prendre 20 minutes ou plus.

Une fois le déploiement terminé, consultez le résumé de déploiement pour **Microsoft.Template** dans la section du déploiement du groupe de ressources. Le résumé contient des valeurs de sortie utilisées pour connecter les membres du consortium.

Pour vérifier le déploiement du membre, parcourez le site d’administration du membre. Vous trouverez l’adresse du site admin dans la section de sortie du déploiement **Microsoft.Template**.

![Résumé du déploiement du membre](./media/azure-stack-ethereum/ethereum-node-status-2.png)

Comme on le voit dans l’image, le statut des nœuds du membre est **Non en cours d’exécution**. En effet, la connexion entre le membre et le leader n’est pas établie. La connexion entre le membre et le leader est une connexion bidirectionnelle. Lorsque vous déployez le membre, le modèle crée automatiquement la connexion du membre au leader. Pour créer la connexion du leader au membre, passez à l’étape suivante.

### <a name="connect-member-and-leader"></a>Connecter le membre et le leader

Ce modèle crée une connexion du leader à un membre à distance. 

1. Téléchargez le [modèle de connexion de membre et de leader à partir de GitHub](https://aka.ms/aa6zdyt).
2. Dans le portail du locataire Azure Stack Hub, sélectionnez **+ Créer une ressource > Template deployment** pour effectuer un déploiement à partir d’un modèle personnalisé.
3. Sélectionnez **Modifier un modèle** pour modifier le nouveau modèle personnalisé.
4. Dans le volet d’édition sur la droite, copiez et collez le JSON du modèle de leader que vous avez précédemment téléchargé.
    
    ![Modifier le modèle de connexion](./media/azure-stack-ethereum/edit-connect-template.png)

5. Sélectionnez **Enregistrer**.
6. Sélectionnez **Modifier les paramètres** et renseignez les paramètres de modèle de votre déploiement.
    
    ![Modifier les paramètres du modèle de connexion](./media/azure-stack-ethereum/edit-connect-parameters.png)

    Nom du paramètre | Description | Valeurs autorisées | Exemple de valeur
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | Préfixe de nom du leader. Vous trouverez cette valeur dans la sortie du déploiement du leader.  | Caractères alphanumériques (entre 1 et 6). | |
    MEMBERROUTETABLENAME | Nom de la table de routage du leader. Vous trouverez cette valeur dans la sortie du déploiement du leader. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | Espace d’adressage du membre. Vous trouverez cette valeur dans la sortie du déploiement du membre. | |
    CONNECTIONSHAREDKEY | Clé secrète préétablie entre les membres du réseau de consortium qui établissent une connexion.  | |
    REMOTEMEMBERNVAPUBLICIP | Adresse IP de la NVA du membre. Vous trouverez cette valeur dans la sortie du déploiement du membre. | |
    MEMBERNVAPRIVATEIP | Adresse IP NVA privée du leader. Vous trouverez cette valeur dans la sortie du déploiement du leader. | |
    LOCATION | Emplacement de votre environnement Azure Stack Hub. | | local
    BASEURL | URL de base pour le modèle. | Utilisez la valeur par défaut, sauf si vous souhaitez personnaliser les modèles de déploiement. | 

7. Sélectionnez **OK**.
8. Dans **Déploiement personnalisé**, spécifiez **Abonnement**, **Groupe de ressources** et **Emplacement du groupe de ressources**.
    
    ![Paramètres de déploiement de la connexion](./media/azure-stack-ethereum/connect-deployment-parameters.png)

    Nom du paramètre | Description | Valeurs autorisées | Exemple de valeur
    ---------------|-------------|----------------|-------------
    Abonnement | Abonnement du leader. | | Abonnement Consommation
    Groupe de ressources | Groupe de ressources du leader. | | EthereumResources
    Emplacement | Région Azure du groupe de ressources. | | local

8. Sélectionnez **Create** (Créer).

Une fois le déploiement terminé, l’établissement de la communication entre leader et le membre prend quelques minutes. Pour vérifier le déploiement, actualisez le site d’administration du membre. Le statut des nœuds du membre doit être En cours d’exécution.

![Vérifier le déploiement](./media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Ethereum et Azure, consultez [Technologie et applications blockchain](https://azure.microsoft.com/solutions/blockchain/).
