---
title: Connecter Azure Stack HCI à Azure
description: Comment inscrire Azure Stack HCI auprès d’Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/10/2020
ms.openlocfilehash: e56718e080638eb6349625f644c837798c001a1d
ms.sourcegitcommit: 97ecba06aeabf2f30de240ac283b9bb2d49d62f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97010853"
---
# <a name="connect-azure-stack-hci-to-azure"></a>Connecter Azure Stack HCI à Azure

> S’applique à : Azure Stack HCI, v20H2

Azure Stack HCI est fourni en tant que service Azure et doit être inscrit dans les 30 jours suivant l’installation, selon les conditions des services en ligne Azure. Cette rubrique explique comment inscrire votre cluster Azure Stack HCI à [Azure Arc](https://azure.microsoft.com/services/azure-arc/) pour la supervision, le support, la facturation et les services hybrides. Après l’inscription, une ressource Azure Resource Manager est créée pour représenter chaque cluster Azure Stack HCI local, étendant le plan de gestion Azure à Azure Stack HCI. Les informations sont régulièrement synchronisées entre la ressource Azure et le ou les clusters locaux.

   > [!IMPORTANT]
   > L’inscription auprès d’Azure est obligatoire. Tant que votre cluster n’est pas inscrit auprès d’Azure, le système d’exploitation Azure Stack HCI n’est pas sous licence valide, n’est pas pris en charge et a des fonctionnalités réduites (par exemple, vous ne pouvez pas créer de machines virtuelles).

## <a name="prerequisites-for-registration"></a>Prérequis pour l’inscription

Vous ne pouvez pas vous inscrire auprès d’Azure tant que vous n’avez pas créé un cluster Azure Stack HCI. Pour que le cluster soit pris en charge, les nœuds de cluster doivent être des serveurs physiques. Les machines virtuelles peuvent être utilisées pour les tests, mais elles doivent prendre en charge UEFI (Unified Extensible Firmware Interface), ce qui signifie que vous ne pouvez pas utiliser des machines virtuelles Hyper-V de génération 1. L’inscription Azure Arc est une fonctionnalité native du système d’exploitation Azure Stack HCI : aucun agent n’est donc nécessaire pour effectuer l’inscription.

### <a name="internet-access"></a>Accès à Internet

Azure Stack HCI doit se connecter régulièrement au cloud public Azure. Si la connectivité sortante est limitée par votre pare-feu d’entreprise externe ou votre serveur proxy, ceux-ci doivent être configurés pour autoriser l’accès sortant vers le port 443 (HTTPS) sur un nombre limité d’adresses IP Azure connues. 

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

## <a name="register-using-powershell"></a>S’inscrire en utilisant PowerShell

Utilisez la procédure suivante pour inscrire un cluster Azure Stack HCI auprès d’Azure à l’aide d’un PC de gestion.

1. Installez les applets de commande nécessaires sur votre PC de gestion. Si vous inscrivez un cluster déployé à partir de l’image en disponibilité générale d’Azure Stack HCI, exécutez simplement la commande suivante. Si votre cluster a été déployé à partir de l’image en préversion publique, vérifiez que vous avez appliqué la mise à jour de la préversion du 23 novembre 2020 (KB4586852) à chaque serveur du cluster avant d’essayer de vous inscrire.

   ```PowerShell
   Install-Module -Name Az.StackHCI
   ```

   > [!NOTE]
   > - Vous pouvez voir une invite comme « Voulez-vous que PowerShellGet installe et importe le fournisseur NuGet maintenant ? ». à laquelle vous devez répondre Oui (Y).
   > - Vous pouvez après cela voir une invite « Voulez-vous vraiment installer les modules à partir de PSGallery ? », à laquelle vous devez répondre Oui (Y).

2. Effectuez l’inscription à l’aide du nom de n’importe quel serveur du cluster. Pour obtenir votre ID d’abonnement Azure, visitez [portal.azure.com](https://portal.azure.com), accédez à Abonnements, puis copiez/collez votre ID à partir de la liste.

   ```PowerShell
   Register-AzStackHCI  -SubscriptionId "<subscription_ID>" -ComputerName Server1 [–Credential] [-ResourceName] [-ResourceGroupName]
   ```

   Cette syntaxe inscrit le cluster (dont Server1 est membre), en tant qu’utilisateur actuel, auprès de l’environnement cloud et de la région Azure par défaut, et utilise des noms intelligents par défaut pour la ressource et le groupe de ressources Azure, mais vous pouvez ajouter des paramètres à cette commande pour spécifier ces valeurs, si vous le souhaitez.

   Gardez à l’esprit que l’utilisateur qui exécute l’applet de commande `Register-AzStackHCI` doit disposer des autorisations [Azure Active Directory](../manage/manage-azure-registration.md#azure-active-directory-app-permissions) ; sinon, le processus d’inscription ne se terminera pas et se fermera et l’inscription sera toujours en attente du consentement de l’administrateur. Une fois les autorisations accordées, réexécutez `Register-AzStackHCI` pour finaliser l’inscription.

3. S’authentifier avec Azure

   Pour terminer le processus d’inscription, vous devez vous authentifier (vous connecter) à l’aide de votre compte Azure. Votre compte doit avoir accès à l’abonnement Azure, spécifié à l’étape 4 ci-dessus, pour pouvoir procéder à l’inscription. Copiez le code fourni, accédez à microsoft.com/devicelogin sur un autre appareil (comme votre PC ou téléphone), à entrer le code et à vous y connecter. Il s’agit de la même expérience que celle que Microsoft utilise pour d’autres appareils avec des modalités d’entrée limitées, comme Xbox.

Le workflow d’inscription détecte que vous vous êtes connecté et effectue le processus. Vous devez alors être en mesure de voir votre cluster dans le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à :

- [Valider le cluster](validate.md)

