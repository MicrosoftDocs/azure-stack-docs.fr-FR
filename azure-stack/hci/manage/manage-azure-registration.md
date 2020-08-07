---
title: Gérer l’inscription Azure pour Azure Stack HCI
description: Explique comment gérer votre inscription Azure pour Azure Stack HCI et comment connaître l’état de l’inscription à l’aide de PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: 696ef552dcf49f31fb613a22393617e653f7e10d
ms.sourcegitcommit: eb91a28a19a74f799b093ae2a705f7f6e4c5cd49
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87436457"
---
# <a name="manage-azure-registration"></a>Gérer une inscription Azure

> S’applique à Azure Stack HCI v20H2

Une fois que vous avez créé un cluster Azure Stack HCI, vous devez [inscrire le cluster auprès d’Azure Arc](../deploy/register-with-azure.md). Une fois le cluster inscrit, les informations sont régulièrement synchronisées entre le cluster local et le cloud. Cette rubrique explique comment connaître l’état de votre inscription et comment désinscrire votre cluster lorsque vous êtes prêt à le retirer.

## <a name="understanding-registration-status"></a>Connaître l’état de l’inscription

Pour connaître l’état de l’inscription, utilisez l’applet de commande PowerShell `Get-AzureStackHCI`, ainsi que les propriétés `ClusterStatus`, `RegistrationStatus` et `ConnectionStatus`. Par exemple, après l’installation du système d’exploitation Azure Stack HCI, avant de créer ou de joindre un cluster, la propriété `ClusterStatus` indique l’état « not yet » (non inscrit) :

:::image type="content" source="media/manage-azure-registration/1-get-azurestackhci.png" alt-text="État de l’inscription Azure avant la création du cluster":::

Une fois le cluster créé, seul `RegistrationStatus` indique l’état « not yet » :

:::image type="content" source="media/manage-azure-registration/2-get-azurestackhci.png" alt-text="État de l’inscription Azure après la création du cluster":::

Azure Stack HCI doit être inscrit dans les 30 jours suivant l’installation, selon les conditions des services en ligne Azure. S’il n’est pas mis en cluster au bout de 30 jours, `ClusterStatus` affiche `OutOfPolicy`, et s’il n’est pas inscrit au bout de 30 jours, `RegistrationStatus` affiche `OutOfPolicy`.

Une fois le cluster inscrit, vous pouvez voir la date et l’heure auxquelles `ConnectionStatus` et `LastConnected` ont eu lieu, généralement au cours des dernières 24 heures, sauf si le cluster est temporairement déconnecté d’Internet. Un cluster Azure Stack HCI peut fonctionner entièrement hors connexion pendant 30 jours consécutifs.

:::image type="content" source="media/manage-azure-registration/3-get-azurestackhci.png" alt-text="État de l’inscription Azure après l’inscription":::

Si la période maximale est dépassée, `ConnectionStatus` affiche `OutOfPolicy`.

## <a name="azure-active-directory-permissions"></a>Autorisations Azure Active Directory

En plus de créer une ressource Azure dans votre abonnement, l’inscription d’Azure Stack HCI crée une identité d’application, qui est similaire à un utilisateur d’un point de vue conceptuel, dans votre locataire Azure Active Directory. L’identité de l’application hérite du nom du cluster. Cette identité agit pour le compte du service cloud Azure Stack HCI au sein de votre abonnement, lorsque cela est nécessaire.

Si l’utilisateur qui exécute `Register-AzureStackHCI` est un administrateur Azure Active Directory ou s’il dispose d’autorisations suffisantes, cela se produit automatiquement et aucune action supplémentaire n’est nécessaire. Si ce n’est pas le cas, une approbation peut être nécessaire de la part de votre administrateur Azure Active Directory pour finaliser l’inscription. Votre administrateur peut soit explicitement accorder son consentement à l’application, soit déléguer des autorisations afin que vous puissiez accorder un consentement à l’application :

:::image type="content" source="media/manage-azure-registration/aad-permissions.png" alt-text="Diagramme des identités et des autorisations Azure Active Directory" border="false":::

Pour accorder le consentement, accédez à portal.azure.com, puis connectez-vous avec un compte Azure disposant des autorisations suffisantes dans Azure Active Directory. Accédez à **Azure Active Directory**, puis à **Inscriptions d’applications**. Sélectionnez l’identité d’application nommée d’après votre cluster, puis accédez à **Autorisations de l’API**.

L’application exige deux autorisations :

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Census.Sync

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Billing.Sync
```

L’obtention de l’approbation de votre administrateur Azure Active Directory peut prendre un certain temps. Par conséquent, l’applet de commande `Register-AzureStackHCI` s’arrête en laissant l’inscription à l’état « pending admin consent » (en attente du consentement administrateur), autrement dit, l’inscription n’a été effectuée qu’en partie. Une fois le consentement accordé, réexécutez `Register-AzureStackHCI` pour finaliser l’inscription.

## <a name="unregister-azure-stack-hci-with-azure"></a>Désinscrire Azure Stack HCI avec Azure

Lorsque vous êtes prêt à désactiver votre cluster Azure Stack HCI, utilisez l’applet de commande `Unregister-AzStackHCI` pour le désinscrire. La désinscription ne vous permet plus d’utiliser les fonctionnalités de supervision, de support et de facturation qui étaient disponibles par le biais d’Azure Arc. La ressource Azure représentant le cluster et l’identité d’application Azure Active Directory sont supprimées. Toutefois, le groupe de ressources ne l’est pas, car il peut contenir d’autres ressources non liées au cluster.

Si vous exécutez l’applet de commande `Unregister-AzStackHCI` sur un nœud de cluster, utilisez cette syntaxe et spécifiez votre ID d’abonnement Azure, ainsi que le nom de ressource du cluster Azure Stack HCI que vous souhaitez désinscrire :

```PowerShell
Unregister-AzStackHCI -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Vous serez invité à visiter microsoft.com/devicelogin sur un autre appareil (comme votre PC ou téléphone), à entrer le code et à vous y connecter pour vous authentifier auprès d’Azure.

Si vous exécutez l’applet de commande à partir d’un PC de gestion, vous devez également spécifier le nom d’un serveur dans le cluster :

```PowerShell
Unregister-AzStackHCI -ComputerName ClusterNode1 -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Une fenêtre de connexion Azure interactive s’affichera. Les invites exactes que vous voyez varient en fonction de vos paramètres de sécurité (par exemple, l’authentification à 2 facteurs). Suivez les invites pour vous connecter.

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des informations connexes, reportez-vous également à :

- [Connecter Azure Stack HCI à Azure](../deploy/register-with-azure.md)
- [Superviser Azure Stack HCI avec Azure Monitor](azure-monitor.md)
