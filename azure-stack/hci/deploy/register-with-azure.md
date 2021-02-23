---
title: Connecter Azure Stack HCI à Azure
description: Découvrez comment inscrire des clusters Azure Stack HCI auprès d’Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 02/10/2020
ms.openlocfilehash: 3711a0e11bac59f00ce51027ea9544f6858dd297
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487321"
---
# <a name="connect-azure-stack-hci-to-azure"></a>Connecter Azure Stack HCI à Azure

> S’applique à : Azure Stack HCI, v20H2

Azure Stack HCI est fourni en tant que service Azure et doit être inscrit dans les 30 jours suivant l’installation, selon les conditions des services en ligne Azure. Cette rubrique explique comment inscrire votre cluster Azure Stack HCI à [Azure Arc](https://azure.microsoft.com/services/azure-arc/) pour la supervision, le support, la facturation et les services hybrides. Après l’inscription, une ressource Azure Resource Manager est créée pour représenter chaque cluster Azure Stack HCI local, étendant le plan de gestion Azure à Azure Stack HCI. Les informations sont régulièrement synchronisées entre la ressource Azure et le ou les clusters locaux. L’inscription Azure Arc est une fonctionnalité native du système d’exploitation Azure Stack HCI : aucun agent n’est donc nécessaire pour effectuer l’inscription.

   > [!IMPORTANT]
   > L’inscription auprès d’Azure est requise, et votre cluster n’est pas entièrement pris en charge tant que votre inscription n’est pas activée. Si vous n’inscrivez pas votre cluster auprès d’Azure juste après le déploiement, ou si votre cluster est inscrit mais qu’il ne s’est pas connecté à Azure depuis plus de 30 jours, le système n’autorise pas la création ni l’ajout de nouvelles machines virtuelles. Dans de tels cas, le message d’erreur suivant s’affiche quand vous tentez de créer des machines virtuelles :
   >
   > *Échec lors de la configuration du rôle de machine virtuelle pour « nom_machine virtuelle ». le travail a échoué. Erreur lors de l’ouverture des rôles en cluster pour « nom_machine virtuelle ». Le service auquel vous accédez a une licence pour un nombre particulier de connexions. Il n’est plus possible d’établir des connexions au service pour le moment, car le nombre maximal de connexions autorisées est déjà atteint.*
   >
   > La solution consiste à autoriser la connectivité sortante vers Azure et à vérifier que votre cluster est bien inscrit comme cela est décrit dans cette rubrique.

## <a name="prerequisites-for-registration"></a>Prérequis pour l’inscription

Vous ne pouvez pas vous inscrire auprès d’Azure tant que vous n’avez pas créé un cluster Azure Stack HCI. Pour que le cluster soit pris en charge, les nœuds de cluster doivent être des serveurs physiques. Les machines virtuelles peuvent être utilisées pour les tests, mais elles doivent prendre en charge UEFI (Unified Extensible Firmware Interface), ce qui signifie que vous ne pouvez pas utiliser des machines virtuelles Hyper-V de génération 1.

Le plus simple est de demander à l’administrateur Azure AD d’effectuer l’inscription à l’aide de Windows Admin Center ou de PowerShell.

   > [!IMPORTANT]
   > Pour inscrire un cluster Azure Stack HCI à l’aide de Windows Admin Center, vous devez commencer par [inscrire Windows Admin Center auprès d’Azure](../manage/register-windows-admin-center.md) en utilisant l’ID (locataire) Azure Active Directory que vous comptez utiliser pour l’inscription du cluster.

### <a name="internet-access"></a>Accès à Internet

Azure Stack HCI doit se connecter régulièrement au cloud public Azure. Si la connectivité sortante est limitée par votre pare-feu d’entreprise externe ou votre serveur proxy, ceux-ci doivent être configurés pour autoriser l’accès sortant vers le port 443 (HTTPS) sur un nombre limité d’adresses IP Azure connues. Pour plus d’informations sur la façon de préparer vos pare-feu et configurer un serveur proxy, consultez [Configurer des pare-feu pour Azure Stack HCI](../concepts/configure-firewalls.md).

   > [!NOTE]
   > Le processus d’inscription tente de contacter PowerShell Gallery pour vérifier que vous disposez de la dernière version des modules PowerShell nécessaires, comme AZ et AzureAD. Même si PowerShell Gallery est hébergé sur Azure, il n’a actuellement pas d’étiquette de service. Si vous ne pouvez pas exécuter l’applet de commande ci-dessus à partir d’un ordinateur de gestion disposant d’un accès Internet sortant, nous vous recommandons de télécharger les modules et de les transférer manuellement vers un nœud de cluster où vous exécuterez la commande `Register-AzStackHCI`. Vous pouvez également [installer les modules dans un scénario déconnecté](/powershell/scripting/gallery/how-to/working-with-local-psrepositories?view=powershell-7.1#installing-powershellget-on-a-disconnected-system).

### <a name="azure-subscription-and-permissions"></a>Abonnement et autorisations Azure

Si vous n’avez pas encore de compte Azure, [créez-en un](https://azure.microsoft.com/).

Vous pouvez utiliser un abonnement existant de n’importe quel type :
- Compte gratuit avec crédits Azure [pour les étudiants](https://azure.microsoft.com/free/students/) ou les [abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)
- Abonnement [Paiement à l’utilisation](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) avec carte de crédit
- Abonnement obtenu via un Contrat Entreprise
- Abonnement obtenu via le programme Fournisseur de solutions cloud

L’utilisateur qui inscrit le cluster doit disposer d’autorisations d’abonnement Azure pour :

- Inscrire un fournisseur de ressources
- Créer/obtenir/supprimer des ressources et des groupes de ressources Azure

Si votre abonnement Azure s’effectue par le biais d’un contrat entreprise ou d’un fournisseur de solutions cloud, le moyen le plus simple consiste à demander à votre administrateur d’abonnement Azure d’attribuer un rôle Azure intégré « Propriétaire » ou « Contributeur » à votre abonnement. Toutefois, certains administrateurs peuvent préférer une option plus restrictive. Dans ce cas, il est possible de créer un rôle Azure personnalisé spécifique pour l’inscription Azure Stack HCI en effectuant les étapes suivantes :

1. Créez un fichier JSON nommé **customHCIRole.json** avec le contenu suivant. Veillez à remplacer <subscriptionID> par votre ID d’abonnement Azure. Pour obtenir votre ID d’abonnement, visitez [portal.azure.com](https://portal.azure.com), accédez à Abonnements, puis copiez/collez votre ID à partir de la liste.

   ```json
   {
     "Name": "Azure Stack HCI registration role”,
     "Id": null,
     "IsCustom": true,
     "Description": "Custom Azure role to allow subscription-level access to register Azure Stack HCI",
     "Actions": [
       "Microsoft.Resources/subscriptions/resourceGroups/write",
       "Microsoft.Resources/subscriptions/resourceGroups/read",
       "Microsoft.Resources/subscriptions/resourceGroups/delete",
       "Microsoft.AzureStackHCI/register/action",
       "Microsoft.AzureStackHCI/Unregister/Action",
       "Microsoft.AzureStackHCI/clusters/*"
     ],
     "NotActions": [
     ],
   "AssignableScopes": [
       "/subscriptions/<subscriptionId>"
     ]
   }
   ```

2. Créez le rôle personnalisé :

   ```powershell
   New-AzRoleDefinition -InputFile <path to customHCIRole.json>
   ```

3. Affectez le rôle personnalisé à l’utilisateur :

   ```powershell
   $user = get-AzAdUser -DisplayName <userdisplayname>
   $role = Get-AzRoleDefinition -Name "Azure Stack HCI registration role"
   New-AzRoleAssignment -ObjectId $user.Id -RoleDefinitionId $role.Id -Scope /subscriptions/<subscriptionid>
   ```

### <a name="azure-active-directory-permissions"></a>Autorisations Azure Active Directory

Vous devez également disposer d’autorisations Azure Active Directory appropriées pour effectuer le processus d’inscription. Si vous ne les avez pas encore, demandez à votre administrateur Azure AD de vous accorder son consentement ou de vous déléguer les autorisations. Pour plus d’informations, consultez [Gérer l’inscription Azure](../manage/manage-azure-registration.md#azure-active-directory-app-permissions).

## <a name="register-a-cluster-using-windows-admin-center"></a>Inscrire un cluster en utilisant Windows Admin Center

Le moyen le plus simple d’inscrire votre cluster Azure Stack HCI consiste à utiliser Windows Admin Center. L’utilisateur doit disposer des [autorisations Azure Active Directory](../manage/manage-azure-registration.md#azure-active-directory-app-permissions). Si ce n’est pas le cas, le processus d’inscription s’interrompra dans l’attente de l’approbation de l’administrateur.

1. Avant de démarrer le processus d’inscription, vous devez [inscrire Windows Admin Center auprès d’Azure](../manage/register-windows-admin-center.md) si ce n’est déjà fait.

   > [!IMPORTANT]
   > Lorsque vous inscrivez Windows Admin Center auprès d’Azure, il est important d’utiliser l’ID (locataire) Azure Active Directory que vous comptez utiliser pour l’inscription du cluster réel. Un ID de locataire Azure AD représente une instance Azure AD qui comprend des comptes et des groupes, tandis qu’un ID d’abonnement Azure représente un contrat d’utilisation des ressources Azure impliquant des frais. Pour connaître votre ID de locataire, rendez-vous sur le site [portal.azure.com](https://portal.azure.com), puis sélectionnez **Azure Active Directory**. Votre ID de locataire s’affichera sous **Informations sur le locataire**. Pour connaître votre ID d’abonnement Azure, accédez à **Abonnements**, puis copiez-collez votre ID à partir de la liste.

2. Ouvrez Windows Admin Center, puis sélectionnez **Paramètres** tout en bas du menu **Outils** situé sur la gauche. Ensuite, sélectionnez **Inscription Azure Stack HCI** en bas du menu **Paramètres**. Si votre cluster n’est pas encore inscrit auprès d’Azure, **État de l’inscription** indiquera **Non inscrit**. Pour continuer, cliquez sur le bouton **Inscrire**.

3. Pour terminer le processus d’inscription, vous devez vous authentifier (vous connecter) à l’aide de votre compte Azure. Pour que vous puissiez procéder à l’inscription, votre compte doit avoir accès à l’abonnement Azure que vous avez spécifié au moment d’inscrire la passerelle Windows Admin Center. Copiez le code fourni, accédez à microsoft.com/devicelogin sur un autre appareil (comme votre PC ou téléphone), à entrer le code et à vous y connecter. Le workflow d’inscription détecte que vous vous êtes connecté et effectue le processus. Vous devez alors être en mesure de voir votre cluster dans le portail Azure.

## <a name="register-a-cluster-using-powershell"></a>Inscrire un cluster à l’aide de PowerShell

Utilisez la procédure suivante pour inscrire un cluster Azure Stack HCI auprès d’Azure à l’aide d’un PC de gestion.

1. Installez les applets de commande nécessaires sur votre PC de gestion. Si vous inscrivez un cluster déployé à partir de l’image en disponibilité générale actuelle d’Azure Stack HCI, exécutez simplement la commande suivante. Si votre cluster a été déployé à partir de l’image en préversion publique, vérifiez que vous avez appliqué la mise à jour de la préversion du 23 novembre 2020 (KB4586852) à chaque serveur du cluster avant d’essayer de vous inscrire auprès d’Azure.

   ```PowerShell
   Install-Module -Name Az.StackHCI
   ```

   > [!NOTE]
   > - Vous pouvez voir une invite comme **Voulez-vous que PowerShellGet installe et importe le fournisseur NuGet maintenant ?** , à laquelle vous devez répondre **Oui** (O).
   > - Ensuite, vous pouvez voir l’invite **Voulez-vous vraiment installer les modules à partir de PSGallery ?** , sélectionnez **Oui** (O).

2. Effectuez l’inscription à l’aide du nom de n’importe quel serveur du cluster. Pour obtenir votre ID d’abonnement Azure, rendez-vous sur le site [portal.azure.com](https://portal.azure.com), accédez à **Abonnements**, puis copiez-collez votre ID à partir de la liste.

   ```PowerShell
   Register-AzStackHCI  -SubscriptionId "<subscription_ID>" -ComputerName Server1
   ```

   Cette syntaxe inscrit le cluster (dont Server1 est membre), en tant qu’utilisateur actuel, auprès de l’environnement cloud et de la région Azure par défaut, et utilise des noms intelligents par défaut pour la ressource et le groupe de ressources Azure. Pour spécifier ces valeurs, vous pouvez également ajouter les paramètres facultatifs `-Region`, `-ResourceName` et `-ResourceGroupName` à cette commande.

   L’utilisateur qui exécute l’applet de commande `Register-AzStackHCI` doit disposer des [autorisations Azure Active Directory](../manage/manage-azure-registration.md#azure-active-directory-app-permissions). Si ce n’est pas le cas, le processus d’inscription s’interrompra dans l’attente de l’approbation de l’administrateur. Une fois les autorisations accordées, réexécutez `Register-AzStackHCI` pour finaliser l’inscription.

3. S’authentifier avec Azure

   Pour terminer le processus d’inscription, vous devez vous authentifier (vous connecter) à l’aide de votre compte Azure. Pour que vous puissiez procéder à l’inscription, votre compte doit avoir accès à l’abonnement Azure, spécifié à l’étape 2 ci-dessus. Copiez le code fourni, accédez à microsoft.com/devicelogin sur un autre appareil (comme votre PC ou téléphone), à entrer le code et à vous y connecter. Le workflow d’inscription détecte que vous vous êtes connecté et effectue le processus. Vous devez alors être en mesure de voir votre cluster dans le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à :

- [Valider le cluster](validate.md)

