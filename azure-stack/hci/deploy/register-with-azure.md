---
title: Connecter Azure Stack HCI à Azure
description: Comment inscrire Azure Stack HCI auprès d’Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: f45d2ada3b9699688b69b8848490b19961c500ac
ms.sourcegitcommit: f0c032b300d9c640653b1f795a6ea1439e049a6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87333822"
---
# <a name="connect-azure-stack-hci-to-azure"></a>Connecter Azure Stack HCI à Azure

> S’applique à Azure Stack HCI v20H2 ; Windows Server 2019

Azure Stack HCI est fourni en tant que service Azure et doit être inscrit dans les 30 jours suivant l’installation, selon les conditions des services en ligne Azure. Cette rubrique explique comment inscrire votre cluster Azure Stack HCI à [Azure Arc](https://azure.microsoft.com/services/azure-arc/) pour la supervision, le support, la facturation et les services hybrides. Après l’inscription, une ressource Azure Resource Manager est créée pour représenter chaque cluster Azure Stack HCI local, étendant le plan de gestion Azure à Azure Stack HCI. Les informations sont régulièrement synchronisées entre la ressource Azure et le cluster local. 

## <a name="prerequisites-for-registration"></a>Prérequis pour l’inscription

Vous ne pouvez pas vous inscrire à Azure avant d’avoir créé un cluster Azure Stack HCI. Les nœuds peuvent être des machines physiques ou des machines virtuelles, mais elles doivent disposer d’UEFI (Unified Extensible Firmware Interface), ce qui signifie que vous ne pouvez pas utiliser des machines virtuelles Hyper-V de génération 1. L’inscription Azure Arc est une fonctionnalité native d’Azure Stack HCI : aucun agent n’est donc nécessaire.

### <a name="internet-access"></a>Accès à Internet

Les nœuds Azure Stack HCI nécessitent une connexion au cloud pour pouvoir se connecter à Azure. Par exemple, une commande ping sortante doit réussir :

```PowerShell
C:\> ping bing.com
```

### <a name="azure-subscription"></a>Abonnement Azure

Si vous n’avez pas encore de compte Azure, [créez-en un](https://azure.microsoft.com/). 

Vous pouvez utiliser un abonnement existant de n’importe quel type :
- Compte gratuit avec crédits Azure [pour les étudiants](https://azure.microsoft.com/free/students/) ou les [abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)
- Abonnement [Paiement à l’utilisation](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) avec carte de crédit
- Abonnement obtenu via un Contrat Entreprise
- Abonnement obtenu via le programme Fournisseur de solutions cloud

### <a name="azure-active-directory-permissions"></a>Autorisations Azure Active Directory

Vous devez disposer d’autorisations Azure Active Directory pour effectuer le processus d’inscription. Si vous n’en avez pas encore, demandez à votre administrateur Azure AD de vous accorder les autorisations ou de vous les déléguer. Pour plus d’informations, consultez [Gérer l’inscription Azure](../manage/manage-azure-registration.md#azure-active-directory-permissions).

## <a name="register-using-powershell"></a>S’inscrire en utilisant PowerShell

Installez le module PowerShell pour Azure Stack HCI en exécutant la commande PowerShell suivante sur un ou plusieurs serveurs d’un cluster exécutant le système d’exploitation Azure Stack HCI :

```PowerShell
Install-WindowsFeature RSAT-Azure-Stack-HCI -ComputerName Server1
```

Installez les applets de commande requises sur un nœud de cluster ou un PC de gestion :

```PowerShell
Install-Module Az.StackHCI
```
   > [!NOTE]
   > 1. Vous pouvez voir une invite comme « Voulez-vous que PowerShellGet installe et importe le fournisseur NuGet maintenant ? ». à laquelle vous devez répondre Oui (Y).
   > 2. Vous pouvez après cela voir une invite « Voulez-vous vraiment installer les modules à partir de PSGallery ? », à laquelle vous devez répondre Oui (Y).
   > 3. Enfin, vous supposerez sans doute que l’installation de l’ensemble du module **Az** va inclure le sous-module **StackHCI** : ce sera le cas ultérieurement. Cependant, selon la convention standard d’Azure PowerShell, les sous-modules en préversion ne sont pas inclus automatiquement : vous devez les spécifier explicitement. Par conséquent pour l’instant, vous devez demander explicitement **Az.StackHCI** comme indiqué ci-dessus.

Pour une expérience plus simple, exécutez la commande suivante sur un nœud de cluster Azure Stack HCI (vous êtes alors invité à vous connecter à Azure) :

```PowerShell
Register-AzStackHCI  -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" [-ResourceName] [-ResourceGroupName]
```

La syntaxe minimale nécessite seulement l’ID de votre abonnement Azure. Cette syntaxe inscrit le cluster local (dont le serveur local est membre), en tant qu’utilisateur actuel, auprès de l’environnement cloud et de la région Azure par défaut, et utilise des noms intelligents par défaut pour la ressource et le groupe de ressources Azure. 

Si vous préférez inscrire le cluster à l’aide d’un PC de gestion, fournissez le paramètre **-ComputerName** avec le nom d’un serveur du cluster et vos informations d’identification, si nécessaire :

```PowerShell
Register-AzStackHCI  -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ComputerName Server1 [–Credential] [-ResourceName] [-ResourceGroupName]
```

Par défaut, la ressource Azure créée pour représenter le cluster Azure Stack HCI hérite du nom du cluster et elle est placée dans un nouveau groupe de ressources portant le même nom, plus le suffixe « -rg ». Vous pouvez spécifier un autre nom de ressource ou placer la ressource dans un groupe de ressources existant avec les paramètres facultatifs répertoriés ci-dessus.

Gardez à l’esprit que l’utilisateur qui exécute l’applet de commande `Register-AzStackHCI` doit disposer des autorisations [Azure Active Directory](../manage/manage-azure-registration.md#azure-active-directory-permissions) ; sinon, le processus d’inscription ne se terminera pas et se fermera et l’inscription sera toujours en attente du consentement de l’administrateur. Une fois les autorisations accordées, réexécutez `Register-AzStackHCI` pour finaliser l’inscription.

   > [!NOTE]
   > Si après l’inscription vous recevez un message d’erreur similaire au message ci-dessous, **réessayez l’inscription dans les 24-48 heures**. L’intégration d’Azure est toujours en cours de déploiement dans toutes les régions. Vous pouvez néanmoins continuer votre évaluation : aucune des fonctionnalités n’en sera affectée. Veillez simplement à revenir et à vous inscrire ultérieurement !
   >
   > `Register-AzStackHCI : Azure Stack HCI is not yet available in region <regionName>`
   >
   > Pour vérifier si Azure Stack HCI est disponible dans votre région Azure, [utilisez cet outil](https://azure.microsoft.com/global-infrastructure/services/) et recherchez « hci ».

## <a name="authenticate-with-azure"></a>S’authentifier avec Azure
Une fois que les dépendances ont été installées et que les paramètres ont été validés, vous devez vous authentifier (vous connecter) en utilisant votre compte Azure. Votre compte doit avoir accès à l’abonnement Azure qui a été spécifié pour pouvoir procéder à l’inscription.

Si vous avez exécuté `Register-AzStackHCI` localement sur le système d’exploitation Azure Stack HCI, qui ne peut pas afficher une fenêtre de connexion Azure interactive, vous êtes invité à visiter microsoft.com/devicelogin sur un autre appareil (comme votre PC ou votre téléphone), à entrer le code et à vous connecter. Il s’agit de la même expérience que celle que Microsoft utilise pour d’autres appareils avec des modalités d’entrée limitées, comme Xbox.

Si vous avez exécuté `Register-AzStackHCI` à distance à partir d’un PC de gestion avec expérience de poste de travail, comme Windows 10, une fenêtre de connexion Azure interactive s’affiche. Les invites exactes que vous voyez varient en fonction de vos paramètres de sécurité (par exemple, l’authentification à 2 facteurs). Suivez les invites pour vous connecter.

Le workflow d’inscription détecte que vous vous êtes connecté et effectue le processus. Vous devez alors être en mesure de voir votre cluster dans le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à :

- [Valider le cluster](validate.md)
- [Créer des volumes](../manage/create-volumes.md)
