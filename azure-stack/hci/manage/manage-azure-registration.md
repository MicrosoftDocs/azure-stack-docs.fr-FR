---
title: Gérer l’inscription Azure pour Azure Stack HCI
description: Explique comment gérer votre inscription Azure pour Azure Stack HCI et comment connaître l’état de l’inscription à l’aide de PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 12/10/2020
ms.openlocfilehash: 9acbb273ea67d989f3ec1e1e88c51a96dd440256
ms.sourcegitcommit: 97ecba06aeabf2f30de240ac283b9bb2d49d62f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97010870"
---
# <a name="manage-azure-registration"></a>Gérer une inscription Azure

> S’applique à Azure Stack HCI v20H2

Une fois que vous avez créé un cluster Azure Stack HCI, vous devez [inscrire le cluster auprès d’Azure Arc](../deploy/register-with-azure.md). Une fois le cluster inscrit, les informations sont régulièrement synchronisées entre le cluster local et le cloud. Cette rubrique explique comment connaître l’état de votre inscription, octroyer des autorisations Azure Active Directory et désinscrire votre cluster quand vous êtes prêt à le désaffecter.

## <a name="understanding-registration-status"></a>Connaître l’état de l’inscription

Pour connaître l’état de l’inscription, utilisez l’applet de commande PowerShell `Get-AzureStackHCI`, ainsi que les propriétés `ClusterStatus`, `RegistrationStatus` et `ConnectionStatus`. Par exemple, après l’installation du système d’exploitation Azure Stack HCI, avant de créer ou de joindre un cluster, la propriété `ClusterStatus` indique l’état « not yet » (non inscrit) :

:::image type="content" source="media/manage-azure-registration/1-get-azurestackhci.png" alt-text="État de l’inscription Azure avant la création du cluster":::

Une fois le cluster créé, seul `RegistrationStatus` indique l’état « not yet » :

:::image type="content" source="media/manage-azure-registration/2-get-azurestackhci.png" alt-text="État de l’inscription Azure après la création du cluster":::

Azure Stack HCI doit être inscrit dans les 30 jours suivant l’installation, selon les conditions des services en ligne Azure. S’il n’est pas mis en cluster au bout de 30 jours, `ClusterStatus` affiche `OutOfPolicy`, et s’il n’est pas inscrit au bout de 30 jours, `RegistrationStatus` affiche `OutOfPolicy`.

Une fois le cluster inscrit, vous pouvez voir la date et l’heure auxquelles `ConnectionStatus` et `LastConnected` ont eu lieu, généralement au cours des dernières 24 heures, sauf si le cluster est temporairement déconnecté d’Internet. Un cluster Azure Stack HCI peut fonctionner entièrement hors connexion pendant 30 jours consécutifs.

:::image type="content" source="media/manage-azure-registration/3-get-azurestackhci.png" alt-text="État de l’inscription Azure après l’inscription":::

Si la période maximale est dépassée, `ConnectionStatus` affiche `OutOfPolicy`.

## <a name="azure-active-directory-app-permissions"></a>Autorisations d’application Azure Active Directory

En plus de créer une ressource Azure dans votre abonnement, l’inscription d’Azure Stack HCI crée une identité d’application, qui est similaire à un utilisateur d’un point de vue conceptuel, dans votre locataire Azure Active Directory. L’identité de l’application hérite du nom du cluster. Cette identité agit pour le compte du service cloud Azure Stack HCI au sein de votre abonnement, lorsque cela est nécessaire.

Si l’utilisateur qui exécute `Register-AzureStackHCI` est un administrateur Azure Active Directory ou s’il dispose d’autorisations suffisantes, cela se produit automatiquement et aucune action supplémentaire n’est nécessaire. Si ce n’est pas le cas, une approbation peut être nécessaire de la part de votre administrateur Azure Active Directory pour finaliser l’inscription. Votre administrateur peut soit explicitement accorder son consentement à l’application, soit déléguer des autorisations afin que vous puissiez accorder un consentement à l’application :

:::image type="content" source="media/manage-azure-registration/aad-permissions.png" alt-text="Diagramme des identités et des autorisations Azure Active Directory" border="false":::

Pour accorder un consentement, accédez à [portal.azure.com](https://portal.azure.com), puis connectez-vous avec un compte Azure disposant des autorisations suffisantes sur Azure Active Directory. Accédez à **Azure Active Directory**, puis à **Inscriptions d’applications**. Sélectionnez l’identité d’application nommée d’après votre cluster, puis accédez à **Autorisations de l’API**.

Pour la version en disponibilité générale d’Azure Stack HCI, l’application nécessite les autorisations suivantes, qui sont différentes des autorisations d’application nécessaires dans la préversion publique :

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Cluster.Read

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Cluster.ReadWrite

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.ClusterNode.Read

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.ClusterNode.ReadWrite
```

Pour la préversion publique, les autorisations d’application étaient les suivantes (celles-ci sont désormais dépréciées) :

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Census.Sync

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Billing.Sync
```

L’obtention de l’approbation de votre administrateur Azure Active Directory peut prendre un certain temps. Par conséquent, l’applet de commande `Register-AzureStackHCI` s’arrête en laissant l’inscription à l’état « pending admin consent » (en attente du consentement administrateur), autrement dit, l’inscription n’a été effectuée qu’en partie. Une fois le consentement accordé, réexécutez `Register-AzureStackHCI` pour finaliser l’inscription.

## <a name="azure-active-directory-user-permissions"></a>Autorisations d’utilisateur Azure Active Directory

L’utilisateur qui exécute Register-AzStackHCI a besoin d’autorisations Azure AD pour :

- Créer/obtenir/définir/supprimer des applications Azure AD (New/Get/Set/Remove-AzureADApplication)
- Créer/obtenir un principal du service Azure AD (New/Get-New-AzureADServicePrincipal)
- Gérer les secrets d’application AD (New/Get/Remove-AzureADApplicationKeyCredential)
- Accorder un consentement pour utiliser des autorisations d’application spécifiques (New/Get/Remove AzureADServiceAppRoleAssignments)

Pour ce faire, trois options s’offrent à vous.

### <a name="option-1-allow-any-user-to-register-applications"></a>Option 1 : Autoriser tout utilisateur à inscrire des applications

Dans Azure Active Directory, accédez à **Paramètres utilisateur > Inscriptions d’applications**. Sous **Les utilisateurs peuvent inscrire des applications**, sélectionnez **Oui**.

Cela autorise tout utilisateur à inscrire des applications. Toutefois, l’utilisateur doit toujours demander à l’administrateur Azure AD d’accorder son consentement lors de l’inscription d’un cluster. Notez qu’il s’agit d’un paramètre de niveau locataire. Par conséquent, il peut ne pas convenir aux clients de grandes entreprises.

### <a name="option-2-assign-cloud-application-administration-role"></a>Option n°2 : Affecter un rôle Administration d’application cloud

Affectez le rôle Azure AD intégré « Administration d’application cloud » à l’utilisateur. Cela permet à l’utilisateur d’inscrire des clusters sans avoir besoin d’un consentement d’administrateur AD supplémentaire.

### <a name="option-3-create-a-custom-ad-role-and-consent-policy"></a>Option 3 : Créer un rôle AD et une stratégie de consentement personnalisés

L’option la plus restrictive consiste à créer un rôle AD personnalisé avec une stratégie de consentement personnalisée qui délègue le consentement administrateur à l’échelle du locataire pour les autorisations nécessaires au service Azure Stack HCI. Quand ce rôle personnalisé leur est attribué, les utilisateurs sont en mesure d’effectuer une inscription et d’accorder leur consentement sans avoir besoin d’un consentement d’administrateur AD supplémentaire.

   > [!NOTE]
   > Cette option exige une licence Azure AD Premium, et utilise des rôles AD personnalisés et des fonctionnalités de stratégie de consentement personnalisées qui sont actuellement en préversion publique.

   1. Connectez-vous à Azure AD :
   
      ```powershell
      Connect-AzureAD
      ```

   2. Créez une stratégie de consentement personnalisée :

      ```powershell
      New-AzureADMSPermissionGrantPolicy -Id "AzSHCI-registration-consent-policy" -DisplayName "Azure Stack HCI registration admin app consent policy" -Description "Azure Stack HCI registration admin app consent policy"
      ```

   3. Ajoutez une condition qui inclut les autorisations d’application nécessaires pour le service Azure Stack HCI, qui contient l’ID d’application 1322e676-dee7-41ee-a874-ac923822781c. Notez que les autorisations suivantes s’appliquent à la version en disponibilité générale d’Azure Stack HCI et qu’elles ne fonctionnent pas avec la préversion publique, sauf si vous avez appliqué la [mise à jour de la préversion du 23 novembre 2020 (KB4586852)](../release-notes.md) à chaque serveur de votre cluster et que vous avez téléchargé la version 0.4.1 ou une version ultérieure du module Az.StackHCI.
   
      ```powershell
      New-AzureADMSPermissionGrantConditionSet -PolicyId "AzSHCI-registration-consent-policy" -ConditionSetType "includes" -PermissionType "application" -ResourceApplication "1322e676-dee7-41ee-a874-ac923822781c" -Permissions "bbe8afc9-f3ba-4955-bb5f-1cfb6960b242","8fa5445e-80fb-4c71-a3b1-9a16a81a1966","493bd689-9082-40db-a506-11f40b68128f","2344a320-6a09-4530-bed7-c90485b5e5e2"
      ```

   4. Accordez des autorisations pour permettre l’inscription d’Azure Stack HCI, en notant la stratégie de consentement personnalisée créée à l’étape 2 :
   
      ```powershell
      $displayName = "Azure Stack HCI Registration Administrator "
      $description = "Custom AD role to allow registering Azure Stack HCI "
      $templateId = (New-Guid).Guid
      $allowedResourceAction =
      @(
             "microsoft.directory/applications/createAsOwner",
             "microsoft.directory/applications/delete",
             "microsoft.directory/applications/standard/read",
             "microsoft.directory/applications/credentials/update",
             "microsoft.directory/applications/permissions/update",
             "microsoft.directory/servicePrincipals/appRoleAssignedTo/update",
             "microsoft.directory/servicePrincipals/appRoleAssignedTo/read",
             "microsoft.directory/servicePrincipals/appRoleAssignments/read",
             "microsoft.directory/servicePrincipals/createAsOwner",
             "microsoft.directory/servicePrincipals/credentials/update",
             "microsoft.directory/servicePrincipals/permissions/update",
             "microsoft.directory/servicePrincipals/standard/read",
             "microsoft.directory/servicePrincipals/managePermissionGrantsForAll.AzSHCI-registration-consent-policy"
      )
      $rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
      ```

   5. Créez le nouveau rôle AD personnalisé :

      ```powershell
      $customADRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
      ```

   6. Attribuez le nouveau rôle AD personnalisé à l’utilisateur qui inscrit le cluster Azure Stack HCI auprès d’Azure en suivant [ces instructions](/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal?context=/azure/active-directory/roles/context/ugr-context).

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
