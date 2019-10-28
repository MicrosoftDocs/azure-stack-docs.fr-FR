---
title: Gérer les comptes de stockage Azure Stack | Microsoft Docs
description: Découvrez comment rechercher, gérer, restaurer et récupérer des comptes de stockage Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 9af79442a0fb56e4d6a9cef99741b0180e84304c
ms.sourcegitcommit: b5eb024d170f12e51cc852aa2c72eabf26792d8d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72534190"
---
# <a name="manage-azure-stack-storage-accounts"></a>Gérer des comptes de stockage Azure Stack

Découvrez comment gérer des comptes de stockage Azure Stack. Rechercher et récupérer de la capacité de stockage en fonction des besoins de l’entreprise.

## <a name="find-a-storage-account"></a>Rechercher un compte de stockage

La liste des comptes de stockage de la région peut être affichée dans Azure Stack en procédant comme suit :

1. Connectez-vous au [portail d’administration](https://adminportal.local.azurestack.external).

2. Sélectionnez **Tous les services** > **Stockage** > **Comptes de stockage**.

   ![Comptes de stockage Azure Stack](media/azure-stack-manage-storage-accounts/image4.png)

Par défaut, les 10 premiers comptes sont affichés. Vous pouvez choisir d’en afficher plus en cliquant sur le lien **Charger plus** en bas de la liste.

Ou

Si vous êtes intéressé par un compte de stockage particulier, vous pouvez **filtrer et extraire les comptes appropriés** uniquement.

**Pour filtrer les comptes :**

1. Sélectionnez **Filtrer** en haut du volet.
2. Dans le volet Filtrer, vous pouvez spécifier un **nom de compte**, un **ID d’abonnement** ou un **statut** pour affiner la liste des comptes de stockage à afficher. Utilisez-les pour filtrer selon vos besoins.
3. Lorsque vous tapez, la liste applique automatiquement le filtre.

    ![Filtrer des comptes de stockage Azure Stack](media/azure-stack-manage-storage-accounts/image5.png)

4. Pour réinitialiser le filtre, sélectionnez **Filtrer**, effacez les sélections, puis mettez à jour la liste.

La zone de texte de recherche (en haut du volet de la liste de comptes de stockage) vous permet de mettre en surbrillance le texte sélectionné dans la liste des comptes. Vous pouvez l’utiliser lorsque le nom complet ou l’ID n’est pas facilement disponible.

Vous pouvez utiliser ici du texte libre pour rechercher le compte qui vous intéresse.

![Rechercher des comptes de stockage Azure Stack](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Accéder aux détails du compte
Une fois que vous avez trouvé les comptes qui vous intéressent, vous pouvez sélectionner un compte particulier pour afficher certains détails. Un nouveau volet s’ouvre et affiche les détails du compte. Ces détails incluent notamment le type de compte, l’heure de création et l’emplacement.

![Détails du compte de stockage](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Récupérer un compte supprimé
Il peut être parfois nécessaire de récupérer un compte supprimé.

Dans Azure Stack, il existe un moyen simple de le faire :

1. Accédez à la liste de comptes de stockage. Pour plus d’informations, consultez [Rechercher un compte de stockage](azure-stack-manage-storage-accounts.md) en haut de cet article.
2. Localisez ce compte particulier dans la liste. Il peut être nécessaire de filtrer.
3. Vérifiez l’*état* du compte. Il doit être **Supprimé**.
4. Sélectionnez le compte pour ouvrir le volet des détails du compte.
5. En haut de ce volet, recherchez le bouton **Récupérer** et sélectionnez-le.
6. Sélectionnez **Oui** pour confirmer.

   ![Confirmation de récupération du compte de stockage](media/azure-stack-manage-storage-accounts/image8.png)

7. La récupération est en cours. Attendez un message indiquant que l’opération est terminée. Vous pouvez aussi sélectionner l’icône représentant une cloche en haut du portail, afin d’afficher les indications sur la progression.

   ![Récupération du comptes de stockage réussie](media/azure-stack-manage-storage-accounts/image9.png)

   Une fois que le compte récupéré est synchronisé, il peut à nouveau être utilisé.

### <a name="some-gotchas"></a>Quelques astuces
* Votre compte supprimé affiche un état **hors conservation**.
  
  « Hors rétention » signifie que le compte supprimé a dépassé la période de conservation et qu’il n’est peut-être pas récupérable.

* Votre compte supprimé n’apparaît pas dans la liste des comptes.
  
  Il se peut que votre compte ne s’affiche pas dans la liste des comptes lorsque les comptes supprimés ont déjà été effacés. Dans ce cas, il ne peut pas être récupéré. Pour en avoir plus, voir [Récupérer de la capacité](#reclaim) dans cet article.

## <a name="set-the-retention-period"></a>Définir la période de conservation
Le paramètre de période de conservation permet à un opérateur cloud de spécifier une période de temps en jours (entre 0 et 9 999 jours) pendant laquelle un compte supprimé peut être récupéré. La période de rétention par défaut est définie sur 0 jour. Pour ce paramètre, la valeur « 0 » signifie qu’un compte supprimé n’est plus conservé et qu’il est marqué comme devant faire l’objet d’un nettoyage périodique de la mémoire.

**Pour changer la période de conservation :**

1. Connectez-vous au [portail d’administration](https://adminportal.local.azurestack.external).
2. Sélectionnez **Tous les services** > **Gestion des régions** sous **Administration**.
3. Sélectionnez **Fournisseurs de ressources** > **Stockage** > **Paramètres**. Votre chemin d’accès est Accueil > *région* - Fournisseurs de ressources > Stockage.
4. Sélectionnez **Configuration**, puis modifiez la valeur de la période de conservation.

   Définissez le nombre de jours et enregistrez-le.

   Cette valeur est prise en compte immédiatement et est appliquée à toute votre région.

   ![Modifier la période de rétention dans le portail d’administration](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Récupérer de la capacité
Un des effets secondaires de la période de conservation est qu’un compte supprimé continue à consommer de la capacité jusqu’à ce qu’il sorte de cette période de conservation. En tant qu’opérateur cloud, vous pouvez avoir besoin d’un moyen de récupérer l’espace du compte supprimé, même si la période de conservation n’a pas encore expiré.

Vous pouvez récupérer de la capacité en utilisant le portail ou PowerShell.

**Pour récupérer de la capacité en utilisant le portail :**
1. Accédez au volet des comptes de stockage. Consultez Rechercher un compte de stockage.
2. Sélectionnez **Récupérer de l’espace** en haut du volet.
3. Lisez le message, puis sélectionnez **OK**.

    ![Récupérer de l’espace dans les comptes de stockage](media/azure-stack-manage-storage-accounts/image11.png)

4. Attendez une notification de réussite de l’opération. Consultez l’icône de cloche dans le portail.

    ![L’espace a été correctement récupéré](media/azure-stack-manage-storage-accounts/image12.png)

5. Actualisez la page Comptes de stockage. Les comptes supprimés ne figurent plus dans la liste, car ils ont été supprimés définitivement.

Vous pouvez aussi utiliser PowerShell pour remplacer explicitement la période de rétention et récupérer immédiatement de la capacité.

**Pour récupérer de la capacité en utilisant PowerShell :**

1. Vérifiez qu’Azure PowerShell est installé et configuré. Dans le cas contraire, suivez ces instructions : 
   * Pour installer la dernière version d’Azure PowerShell et l’associer à votre abonnement Azure, consultez [Installer et configurer Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Pour plus d’informations sur les applets de commande Azure Resource Manager, consultez [Utilisation d’Azure PowerShell avec Azure Resource Manager](https://go.microsoft.com/fwlink/?LinkId=394767).
2. Exécutez les applets de commande suivantes :

> [!NOTE]  
> Si vous exécutez ces applets de commande, vous supprimez définitivement le compte et son contenu. Il n’est pas récupérable. Utilisez cette option avec précaution.

```powershell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
```

Pour plus d’informations, consultez la [documentation Azure Stack PowerShell](https://docs.microsoft.com/powershell/azure/azure-stack/overview).
 

## <a name="next-steps"></a>Étapes suivantes

 - Pour plus d’informations sur la gestion des autorisations, consultez [Définir les autorisation d’accès à l’aide du contrôle d’accès en fonction du rôle](azure-stack-manage-permissions.md).
 - Pour plus d’informations sur la gestion de la capacité de stockage pour Azure Stack, consultez [Gérer la capacité de stockage pour Azure Stack](azure-stack-manage-storage-shares.md).
