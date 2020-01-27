---
title: Connecter l’Explorateur Stockage à un compte de stockage ou abonnement Azure Stack Hub | Microsoft Docs
description: Découvrir comment connecter l’Explorateur de stockage à un abonnement Azure Stack Hub
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 7c01e509dc77e2a036ad640ee5376ffda37f903f
ms.sourcegitcommit: 7dd685fddf2f5d7a0c0a20fb8830ca5a061ed031
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76259832"
---
# <a name="connect-storage-explorer-to-an-azure-stack-hub-subscription-or-a-storage-account"></a>Connecter l’Explorateur de stockage à un compte de stockage ou un abonnement Azure Stack Hub

Dans cet article, vous allez découvrir comment vous connecter à vos comptes de stockage et abonnements Azure Stack Hub à l’aide de l’[Explorateur Stockage Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer). L’Explorateur Stockage est une application autonome qui vous permet d’utiliser facilement les données de stockage Azure Stack Hub sur Windows, macOS et Linux.

> [!NOTE]  
> Il existe plusieurs outils qui permettent de déplacer des données vers et à partir du stockage Azure Stack Hub. Pour plus d’informations, consultez [Outils de transfert de données pour le stockage Azure Stack Hub](azure-stack-storage-transfer.md).

S’il n’est pas déjà installé, [téléchargez l’Explorateur Stockage](https://www.storageexplorer.com/) et installez-le.

Une fois que vous êtes connecté à un compte de stockage ou abonnement Azure Stack Hub, vous pouvez consulter les [articles sur l’Explorateur Stockage Azure](/azure/vs-azure-tools-storage-manage-with-storage-explorer) pour utiliser vos données Azure Stack Hub. 

## <a name="prepare-for-connecting-to-azure-stack-hub"></a>Se préparer à la connexion à Azure Stack Hub

Vous avez besoin d’un accès direct à Azure Stack Hub ou à une connexion VPN pour que l’Explorateur Stockage puisse accéder à l’abonnement Azure Stack Hub. Pour savoir comment configurer une connexion VPN à Azure Stack Hub, consultez [Connect to Azure Stack Hub](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) (Se connecter à Azure Stack Hub).

> [!Note]  
> Pour l’ASDK, si vous vous y connectez via un VPN, n’utilisez pas le certificat racine (CA.cer) qui a été créé pendant le processus de configuration du VPN.  Il s’agit d’un certificat codé DER qui n’autorise pas l’Explorateur Stockage à récupérer vos abonnements Azure Stack Hub. Utilisez les étapes suivantes pour exporter un certificat codé en base 64 à utiliser avec l’Explorateur Stockage.

Pour les systèmes intégrés qui sont déconnectés et pour le Kit ASDK, il est recommandé d’utiliser une autorité de certification d’entreprise interne pour exporter le certificat racine au format base 64, puis de l’importer dans Explorateur Stockage Azure.  

### <a name="export-and-then-import-the-azure-stack-hub-certificate"></a>Exporter et importer le certificat Azure Stack Hub

Exportez, puis importez le certificat Azure Stack Hub pour les systèmes intégrés déconnectés et pour le Kit ASDK. Pour les systèmes intégrés connectés, le certificat étant signé publiquement, cette étape n’est pas nécessaire.

1. Ouvrez `mmc.exe` sur une machine hôte Azure Stack Hub ou sur une machine locale avec une connexion VPN à Azure Stack Hub. 

2. Dans **Fichier**, sélectionnez **Ajouter/Supprimer un composant logiciel enfichable**. Sélectionnez **Certificats** dans la liste Composants logiciels enfichables disponibles. 

3. Sélectionnez **Compte d’ordinateur**, puis sélectionnez **Suivant**. Sélectionnez **Ordinateur local**, puis **Terminer**.

4.  Sous **Racine de la console\Certificats (ordinateur local)\Autorités de certification racines de confiance\Certificats**, recherchez **AzureStackSelfSignedRootCert**.

    ![Charger le certificat racine Azure Stack Hub via mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

5. Cliquez avec le bouton droit sur le certificat, sélectionnez **Toutes les tâches** > **Exporter**, puis suivez les instructions pour exporter le certificat avec **X.509 encodé en base 64 (.cer)** .

    Le certificat exporté sera utilisé à l’étape suivante.

6. Démarrez l’Explorateur Stockage. Si vous voyez la boîte de dialogue **Connexion au stockage Azure**, cliquez sur Annuler.

7. Dans le menu **Modifier**, sélectionnez **Certificats SSL**, puis sélectionnez **Importer les certificats**. Utilisez la boîte de dialogue du sélecteur de fichier pour rechercher et ouvrir le certificat que vous avez exporté à l’étape précédente.

    Après avoir importé le certificat, vous êtes invité à redémarrer l’Explorateur Stockage.

    ![Importer le certificat dans l’Explorateur Stockage](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

8. Après le redémarrage de l’Explorateur Stockage, sélectionnez le menu **Modifier** et vérifiez si l’option **Cibler API Azure Stack Hub** est sélectionnée. Si elle ne l’est pas, sélectionnez **Cibler Azure Stack Hub**, puis redémarrez l’Explorateur Stockage pour appliquer la modification. Cette configuration est requise pour la compatibilité avec votre environnement Azure Stack Hub.

    ![S’assurer que l’option Target Azure Stack Hub (Cibler Azure Stack) est sélectionnée](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-hub-subscription-with-azure-ad"></a>Connexion à un abonnement Azure Stack Hub avec Azure AD

Suivez les étapes ci-après pour connecter l’Explorateur Stockage à un abonnement Azure Stack Hub, qui appartient à un compte Azure Active Directory (Azure AD).

1. Dans le volet gauche de l’Explorateur Stockage, sélectionnez **Gérer les comptes**.  
    Tous les abonnements Microsoft auxquels vous êtes connecté s’affichent.

2. Pour vous connecter à l’abonnement Azure Stack Hub, sélectionnez **Ajouter un compte**.

    ![Ajouter un compte Azure Stack Hub](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. Dans la boîte de dialogue Connexion au stockage Azure, sous **Environnement Azure**, sélectionnez **Azure**, **Azure Chine 21Vianet**, **Azure - Allemagne**, **Azure - Gouvernement des États-Unis** ou **Ajouter un nouvel environnement**. Cela dépend du compte Azure Stack Hub utilisé. Pour vous connecter au compte Azure Stack Hub associé à au moins un abonnement Azure Stack Hub actif, sélectionnez **Connexion**.

    ![Se connecter au Stockage Azure](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Une fois que vous êtes connecté avec un compte Azure Stack Hub, le volet gauche est renseigné avec les abonnements Azure Stack Hub associés à ce compte. Sélectionnez les abonnements Azure Stack Hub que vous souhaitez utiliser, puis cliquez sur **Appliquer**. (La case à cocher **Tous les abonnements** permet de sélectionner ou de désélectionner l’ensemble des abonnements Azure Stack Hub répertoriés.)

    ![Sélectionner les abonnements Azure Stack Hub après avoir renseigné la boîte de dialogue Custom Cloud Environment (Environnement cloud personnalisé)](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    Le volet de gauche affiche les comptes de stockage associés aux abonnements Azure Stack Hub sélectionnés.

    ![Liste des comptes de stockage, y compris les comptes d’abonnement Azure Stack Hub](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-hub-subscription-with-ad-fs-account"></a>Connexion à un abonnement Azure Stack Hub avec un compte AD FS

> [!Note]  
> L’expérience de connexion Azure Federated Services (AD FS) prend en charge l’explorateur de stockage 1.2.0 ou versions ultérieures avec Azure Stack Hub 1804 ou mise à jour plus récente.
Suivez les étapes ci-après pour connecter l’Explorateur Stockage à un abonnement Azure Stack Hub, qui appartient à un compte AD FS.

1. Sélectionnez **Gérer les comptes**. L’Explorateur répertorie les abonnements Microsoft auxquels vous vous êtes connecté.
2. Pour vous connecter à l’abonnement Azure Stack Hub, sélectionnez **Ajouter un compte**.

    ![Ajouter un compte - Explorateur Stockage](media/azure-stack-storage-connect-se/add-an-account.png)

3. Sélectionnez **Suivant**. Dans la boîte de dialogue Connexion au stockage Azure, sous **Environnement Azure**, sélectionnez **Use Custom Environment** (Utiliser un environnement personnalisé), puis cliquez sur **Suivant**.

    ![Se connecter à Stockage Azure](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. Entrez toutes les informations requises de l’environnement personnalisé Azure Stack Hub. 

    | Champ | Notes |
    | ---   | ---   |
    | Nom de l’environnement | Le champ peut être personnalisé par l’utilisateur. |
    | Point de terminaison Azure Resource Manager | Les exemples de points de terminaison de ressource Azure Resource Manager du Kit de développement Azure Stack.<br>Pour les opérateurs : https://adminmanagement.local.azurestack.external <br> Pour les utilisateurs : https://management.local.azurestack.external |

    Si vous utilisez un système intégré Azure Stack Hub et que vous ne connaissez pas votre point de terminaison de gestion, contactez votre opérateur.

    ![Ajouter un compte - Environnements personnalisés](./media/azure-stack-storage-connect-se/custom-environments.png)

5. Pour vous connecter au compte Azure Stack Hub associé à au moins un abonnement Azure Stack Hub actif, sélectionnez **Connexion**.



6. Sélectionnez les abonnements Azure Stack Hub que vous souhaitez utiliser, puis cliquez sur **Appliquer**.

    ![Account management](./media/azure-stack-storage-connect-se/account-management.png)

    Le volet de gauche affiche les comptes de stockage associés aux abonnements Azure Stack Hub sélectionnés.

    ![Liste des abonnements associés](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-hub-storage-account"></a>Se connecter à un compte de stockage Azure Stack Hub

Vous pouvez également vous connecter à un compte de stockage Azure Stack Hub à l’aide du nom de compte de stockage et de la paire de clés.

1. Dans le volet gauche de l’Explorateur Stockage, sélectionnez Gérer les comptes. Tous les comptes Microsoft auxquels vous êtes connecté sont affichés.

    ![Ajouter un compte - Explorateur Stockage](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Pour vous connecter à l’abonnement Azure Stack Hub, sélectionnez **Ajouter un compte**.

    ![Ajouter un compte - Se connecter à Stockage Azure](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. Dans la boîte de dialogue Se connecter au Stockage Azure, sélectionnez **Utiliser un nom et une clé de compte de stockage**.

4. Entrez le nom de votre compte dans le champ **Nom du compte** et collez la clé de compte dans la zone de texte **Clé de compte**. Ensuite, sélectionnez **Autres (entrez les informations ci-dessous)** dans **Domaine des points de terminaison de stockage** et entrez le point de terminaison Azure Stack Hub.

    Un point de terminaison Azure Stack Hub comprend deux parties : le nom d’une région et le domaine Azure Stack Hub. Dans le Kit de développement Azure Stack, le point de terminaison par défaut est **local.azurestack.external**. Contactez votre administrateur cloud si vous ne connaissez pas votre point de terminaison.

    ![Joindre un nom et une clé](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Sélectionnez **Connecter**.
6. Une fois le compte de stockage joint, il s’affiche avec la mention (**Externe, Autre**) ajoutée à son nom.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Étapes suivantes

* [Prise en main de l’Explorateur Stockage](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Stockage Azure Stack Hub : différences et considérations](azure-stack-acs-differences.md)
* Pour en savoir plus sur le stockage Azure, consultez la rubrique [Présentation du stockage Microsoft Azure](/azure/storage/common/storage-introduction).
