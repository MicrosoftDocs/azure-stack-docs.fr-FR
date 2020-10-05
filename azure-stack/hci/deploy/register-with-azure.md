---
title: Connecter Azure Stack HCI à Azure
description: Comment inscrire Azure Stack HCI auprès d’Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/24/2020
ms.openlocfilehash: 621662110b2e842a432183dfb7415f3c8365907b
ms.sourcegitcommit: 034e61836038ca75199a0180337257189601cd12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91230493"
---
# <a name="connect-azure-stack-hci-to-azure"></a>Connecter Azure Stack HCI à Azure

> S’applique à : Azure Stack HCI, v20H2

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

Utilisez la procédure suivante pour inscrire un cluster Azure Stack HCI auprès d’Azure :

1. Connectez-vous à l’un des nœuds de cluster en ouvrant une session PowerShell et en entrant la commande suivante :

   ```PowerShell
   Enter-PSSession <server-name>
   ```

2. Installez le module PowerShell pour Azure Stack HCI :

   ```PowerShell
   Install-WindowsFeature RSAT-Azure-Stack-HCI
   ```

3. Installez les cmdlets nécessaires :

   ```PowerShell
   Install-Module Az.StackHCI
   ```

   > [!NOTE]
   > 1. Vous pouvez voir une invite comme « Voulez-vous que PowerShellGet installe et importe le fournisseur NuGet maintenant ? ». à laquelle vous devez répondre Oui (Y).
   > 2. Vous pouvez après cela voir une invite « Voulez-vous vraiment installer les modules à partir de PSGallery ? », à laquelle vous devez répondre Oui (Y).
   > 3. Enfin, vous supposerez sans doute que l’installation de l’ensemble du module **Az** va inclure le sous-module **StackHCI** : ce sera le cas ultérieurement. Cependant, selon la convention standard d’Azure PowerShell, les sous-modules en préversion ne sont pas inclus automatiquement : vous devez les spécifier explicitement. Par conséquent pour l’instant, vous devez demander explicitement **Az.StackHCI** comme indiqué ci-dessus.

4. Effectuez l’inscription réelle :

   ```PowerShell
   Register-AzStackHCI  -SubscriptionId "<subscription_ID>" [-ResourceName] [-ResourceGroupName]
   ```

   Cette syntaxe inscrit le cluster local (dont le serveur local est membre), en tant qu’utilisateur actuel, auprès de l’environnement cloud et de la région Azure par défaut, et utilise des noms intelligents par défaut pour la ressource et le groupe de ressources Azure, mais vous pouvez ajouter des paramètres à cette commande pour spécifier ces valeurs, si vous le souhaitez.

   Gardez à l’esprit que l’utilisateur qui exécute l’applet de commande `Register-AzStackHCI` doit disposer des autorisations [Azure Active Directory](../manage/manage-azure-registration.md#azure-active-directory-permissions) ; sinon, le processus d’inscription ne se terminera pas et se fermera et l’inscription sera toujours en attente du consentement de l’administrateur. Une fois les autorisations accordées, réexécutez `Register-AzStackHCI` pour finaliser l’inscription.

5. S’authentifier avec Azure

   Pour terminer le processus d’inscription, vous devez vous authentifier (vous connecter) à l’aide de votre compte Azure. Votre compte doit avoir accès à l’abonnement Azure, spécifié à l’étape 4 ci-dessus, pour pouvoir procéder à l’inscription. Copiez le code fourni, accédez à microsoft.com/devicelogin sur un autre appareil (comme votre PC ou téléphone), à entrer le code et à vous y connecter. Il s’agit de la même expérience que celle que Microsoft utilise pour d’autres appareils avec des modalités d’entrée limitées, comme Xbox.

Le workflow d’inscription détecte que vous vous êtes connecté et effectue le processus. Vous devez alors être en mesure de voir votre cluster dans le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à :

- [Valider le cluster](validate.md)
- [Créer des volumes](../manage/create-volumes.md)
