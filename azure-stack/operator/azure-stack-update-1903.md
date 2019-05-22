---
title: Mise à jour 1903 d’Azure Stack | Microsoft Docs
description: 'Découvrez la mise à jour 1903 pour les systèmes intégrés Azure Stack : nouveautés, problèmes connus et emplacement de téléchargement.'
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/20/2019
ms.openlocfilehash: 04cec74873869f19c7bd762753f7fe89f51c184d
ms.sourcegitcommit: 39ba6d18781aed98b29ac5e08aac2d75c37bf18c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65386738"
---
# <a name="azure-stack-1903-update"></a>Mise à jour 1903 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit le contenu de la mise à jour 1903. La mise à jour inclut des améliorations, des corrections de bogues et de nouvelles fonctionnalités pour cette version d’Azure Stack. Cet article décrit également les problèmes connus dans cette version, et contient un lien permettant de télécharger la mise à jour. Les problèmes connus sont divisés en problèmes directement liés au processus de mise à jour et problèmes propres à la build (après installation).

> [!IMPORTANT]
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="build-reference"></a>Référence de build

Le numéro de build de la mise à jour 1903 d’Azure Stack est **1.1903.0.35**.

> [!IMPORTANT]
> La charge utile 1903 n’inclut aucune version ASDK.

## <a name="hotfixes"></a>Correctifs logiciels

Azure Stack publie des correctifs logiciels à intervalles réguliers. Avant d’installer la mise à jour 1903 d’Azure Stack, veillez à installer le [dernier correctif logiciel d’Azure Stack](#azure-stack-hotfixes) pour la build 1902.

Les correctifs logiciels Azure Stack sont uniquement applicables aux systèmes intégrés Azure Stack. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

> [!TIP]
> Pour rester informé des publications des correctifs logiciels d’Azure Stack, abonnez-vous aux flux *RSS* ou *Atom* suivants :
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Correctifs logiciels d’Azure Stack

- **1902** : [KB 4500637 – Correctif logiciel d’Azure Stack 1.1902.3.75](https://support.microsoft.com/help/4500637)
- **1903** : [KB 4500638 – Correctif logiciel d’Azure Stack 1.1903.2.39](https://support.microsoft.com/help/4500638)

## <a name="improvements"></a>Améliorations

- La charge utile de la mise à jour 1903 contient une mise à jour pour les composants Azure Stack qui n’incluent pas le système d’exploitation sous-jacent hébergeant Azure Stack. Cela permet d’inclure certaines mises à jour dans l’étendue. Par conséquent, la mise à jour 1903 devrait prendre moins de temps (environ 16 heures, mais la durée exacte peut varier). Cette diminution dans le runtime est spécifique à la mise à jour 1903, et les mises à jour ultérieures peuvent contenir des mises à jour du système d’exploitation, ce qui implique différents runtimes. Les prochaines mises à jour contiendront des informations similaires sur le temps nécessaire à leur application en fonction de la charge utile incluse.

- Correction d’un bogue sur le réseau qui empêchait l’application des modifications apportées à la valeur **Délai d’inactivité (minutes)** d’une **adresse IP publique**. Auparavant, les modifications apportées à cette valeur étaient ignorées, de sorte que, quelles que soient ces modifications, la valeur par défaut était de 4 minutes. Ce paramètre contrôle la durée (en minutes) de maintien d’une connexion TCP ouverte sans utiliser les clients pour envoyer des messages keep-alive. Notez que ce bogue n’affectait que les adresses IP publiques au niveau de l’instance, et non les adresses IP publiques attribuées à un équilibreur de charge.

- Améliorations apportées à la fiabilité du moteur de mise à jour, notamment la mise à jour automatique des problèmes courants afin d’appliquer les mises à jour sans interruption.

- Améliorations apportées à la détection et à la correction des conditions d’espace disque faible.

- Azure Stack prend à présent en charge les agents Windows Azure Linux dont la version est supérieure à 2.2.35. Cette prise en charge permet aux clients de maintenir la cohérence des images Linux entre Azure et Azure Stack. Elle a été ajoutée dans le cadre des correctifs 1901 et 1902.

### <a name="secret-management"></a>Gestion des secrets

- Azure Stack prend désormais en charge la rotation du certificat racine utilisé par des certificats pour la rotation des secrets externe. Pour plus d’informations, [consultez cet article](azure-stack-rotate-secrets.md).

- Les performances de la build 1903 ont été améliorées pour la rotation des secrets afin de réduire le temps nécessaire à l’exécution de cette rotation.

## <a name="prerequisites"></a>Prérequis

> [!IMPORTANT]
> Avant d’installer la mise à jour 1903, installez le [dernier correctif logiciel d’Azure Stack](#azure-stack-hotfixes) pour la build 1902.

- Veillez à utiliser la dernière version de l’outil [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) pour planifier et dimensionner votre charge de travail. La dernière version contient des corrections de bogues et fournit de nouvelles fonctionnalités publiées à chaque mise à jour d’Azure Stack.

- Avant de démarrer l’installation de cette mise à jour, exécutez [Test-AzureStack](azure-stack-diagnostic-test.md) avec le paramètre suivant pour valider l’état de votre Azure Stack et résoudre les éventuels problèmes opérationnels détectés, y compris tous les avertissements et les échecs. Examinez aussi les alertes actives et résolvez toutes celles qui nécessitent une action :

    ```powershell
   Test-AzureStack -Group UpdateReadiness
    ```

- Lorsqu’Azure Stack est géré par System Center Operations Manager, veillez à mettre à jour le [Pack d’administration pour Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) vers la version 1.0.3.11 avant d’appliquer la version 1903.

- Le format de package pour la mise à jour d’Azure Stack est passé de **.bin/.exe/.xml** à **.zip/.xml** depuis la version 1902. Les clients avec des unités d’échelle Azure Stack connectées verront le message **Mise à jour disponible** dans le portail. Les clients qui ne sont pas connectés peuvent désormais simplement télécharger et importer le fichier .zip qui contient le fichier .xml correspondant.

<!-- ## New features -->

<!-- ## Fixed issues -->

<!-- ## Common vulnerabilities and exposures -->

## <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour

- Quand vous exécutez la commande [Test-AzureStack](azure-stack-diagnostic-test.md), un message d’avertissement du contrôleur de gestion de la carte de base (BMC) s’affiche. Vous pouvez ignorer cet avertissement sans problème.

<!-- 2468613 - IS -->
- Pendant l’installation de cette mise à jour, des alertes avec le titre **Erreur - Le modèle de FaultType UserAccounts.New est manquant.** peuvent s’afficher. Vous pouvez ignorer ces alertes de manière sécurisée. Elles disparaîtront automatiquement une fois l’installation de cette mise à jour terminée.

## <a name="post-update-steps"></a>Étapes après la mise à jour

- Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, voir [Correctifs logiciels](#hotfixes) ainsi que notre [Stratégie de maintenance](azure-stack-servicing-policy.md).

- Récupérez les clés de chiffrement de données au repos et stockez-les en toute sécurité hors de votre déploiement Azure Stack. Suivez les [instructions sur la façon de récupérer les clés](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

Les éléments suivants sont des problèmes connus qui apparaissent après l’installation de cette build.

### <a name="portal"></a>Portail

- Dans le tableau de bord du portail utilisateur, lorsque vous essayez de cliquer sur la vignette **Commentaires**, un onglet de navigateur vide s’ouvre. Pour résoudre ce problème, vous pouvez utiliser [Azure Stack User Voice](https://aka.ms/azurestackuservoice) pour déposer une demande User Voice.

<!-- 2930820 - IS ASDK -->
- Dans les portails d’administration et utilisateur, si vous recherchez « Docker », l’élément est incorrectement retourné dans les résultats. Il n’est pas disponible dans Azure Stack. Si vous essayez de le créer, un panneau indiquant une erreur s’affiche.

<!-- 2931230 - IS  ASDK -->
- Les plans ajoutés à un abonnement utilisateur comme plan d’extension ne peuvent pas être supprimés, même quand vous supprimez le plan de l’abonnement utilisateur. Le plan est conservé jusqu’à ce que les abonnements qui référencent le plan d’extension soient aussi supprimés.

<!-- TBD - IS ASDK -->
- Les deux types d’abonnements d’administration qui ont été introduits avec la version 1804 ne doivent pas être utilisés. Les types d’abonnements sont **Abonnement de contrôle** et **Abonnement de consommation**. Ces types d’abonnements sont visibles dans les nouveaux environnements Azure Stack depuis la version 1804, mais ils ne sont pas encore prêts à être utilisés. Vous devez continuer à utiliser le type d’abonnement **Fournisseur par défaut**.

<!-- 3557860 - IS ASDK -->
- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.

<!-- 1663805 - IS ASDK --> 
- Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack. Pour contourner ce problème, utilisez [PowerShell pour vérifier les autorisations](/powershell/module/azurerm.resources/get-azurermroleassignment).

<!-- Daniel 3/28 -->
- Dans le portail de l’utilisateur, lorsque vous accédez à un objet blob au sein d’un compte de stockage et essayez d’ouvrir la rubrique **Stratégie d'accès** dans l’arborescence de navigation, la fenêtre suivante ne s’ouvre pas. Pour contourner ce problème, les applets de commande PowerShell suivantes permettent de créer, récupérer, définir et supprimer des stratégies d’accès, respectivement :

  - [New-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/new-azurestoragecontainerstoredaccesspolicy)
  - [Get-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/get-azurestoragecontainerstoredaccesspolicy)
  - [Set-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/set-azurestoragecontainerstoredaccesspolicy)
  - [Remove-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/remove-azurestoragecontainerstoredaccesspolicy)

  
<!-- Daniel 3/28 -->
- Dans le portail de l’utilisateur, lorsque vous essayez d’uploader un objet blob à l’aide de l’option **OAuth (préversion)**, la tâche échoue avec un message d’erreur. Pour contourner ce problème, uploadez l’objet blob à l’aide de l’option **SAS**.

- Lorsque vous êtes connecté aux portails Azure Stack, vous pouvez consulter les notifications concernant le portail public Azure. Vous pouvez ignorer ces notifications car elles ne s’appliquent pas à Azure Stack (par exemple, « 1 nouvelle mise à jour - Les mises à jour suivantes sont maintenant disponibles : Mise à jour d’avril 2019 du Portail Azure »).

- Dans le tableau de bord du portail utilisateur, lorsque vous sélectionnez la vignette **Commentaires**, un onglet de navigateur vide s’ouvre. Pour résoudre ce problème, vous pouvez utiliser [Azure Stack User Voice](https://aka.ms/azurestackuservoice) pour déposer une demande User Voice.

<!-- ### Health and monitoring -->

### <a name="compute"></a>Calcul

- Lors de la création d'une machine virtuelle Windows, l’erreur suivante peut s’afficher :

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   Cette erreur se produit si vous activez les diagnostics de démarrage sur une machine virtuelle alors que vous avez supprimé votre compte de stockage des diagnostics de démarrage. Pour contourner ce problème, recréez le compte de stockage avec le même nom que celui utilisé précédemment.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- Quand vous créez un groupe de machines virtuelles identiques, l’option CentOS 7.2 est proposée pour le déploiement. Étant donné que cette image n’est pas disponible sur Azure Stack, sélectionnez un autre système d’exploitation pour votre déploiement, ou choisissez un modèle Azure Resource Manager spécifiant une autre image CentOS qui a été téléchargée par l’opérateur avant le déploiement à partir de la Place de marché.

<!-- TBD - IS ASDK -->
- Après avoir appliqué la mise à jour 1903, vous rencontrerez peut-être les problèmes suivants lors du déploiement de machines virtuelles avec la fonctionnalité Disques managés :

   - Si l’abonnement a été créé avant la mise à jour 1808, le déploiement d’une machine virtuelle avec la fonctionnalité Disques managés peut échouer avec un message d’erreur interne. Pour résoudre cette erreur, effectuez les étapes suivantes pour chaque abonnement :
      1. Dans le portail locataire, accédez à **Abonnements** et recherchez l’abonnement. Sélectionnez **Fournisseurs de ressources**, **Microsoft.Compute**, puis **Réinscrire**.
      2. Sous le même abonnement, accédez à **Contrôle d’accès (IAM)** et vérifiez que l’élément **Azure Stack – Managed Disks** est répertorié.
   - Si vous avez configuré un environnement multilocataire, le déploiement de machines virtuelles dans un abonnement associé à un annuaire invité peut échouer avec un message d’erreur interne. Pour résoudre le problème, effectuez les étapes décrites dans [cet article](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) afin de reconfigurer chacun de vos annuaires invités.

- Une machine virtuelle 18.04 Ubuntu créée avec une autorisation SSH activée ne vous permet pas d’utiliser les clés SSH pour vous connecter. Pour contourner ce problème, utilisez un accès à la machine virtuelle pour l’extension Linux afin d’implémenter des clés SSH après l’approvisionnement, ou utilisez une authentification par mot de passe.

- Si vous n’avez pas d’hôte de cycle de vie du matériel (HLH) : avant la build 1902, vous deviez définir la stratégie de groupe **Configuration de l’ordinateur\Paramètres Windows\Paramètres de sécurité\Stratégies Locales\Options de sécurité** sur **Envoyer LM et NTLM – utiliser la sécurité de session NTLMv2 si négociée**. Depuis la build 1902, vous devez la laisser en tant que **Non définie** ou la définir sur **Envoyer une réponse NTLMv2 uniquement** (qui est la valeur par défaut). Sinon, vous ne pourrez pas établir de session PowerShell à distance et vous verrez un message d’erreur **L’accès est refusé** :

   ```powershell
   $Session = New-PSSession -ComputerName x.x.x.x -ConfigurationName PrivilegedEndpoint -Credential $Cred
   New-PSSession : [x.x.x.x] Connecting to remote server x.x.x.x failed with the following error message : Access is denied. For more information, see the
   about_Remote_Troubleshooting Help topic.
   At line:1 char:12
   + $Session = New-PSSession -ComputerName x.x.x.x -ConfigurationNa ...
   +            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      + CategoryInfo          : OpenError: (System.Manageme....RemoteRunspace:RemoteRunspace) [New-PSSession], PSRemotingTransportException
      + FullyQualifiedErrorId : AccessDenied,PSSessionOpenFailed
   ```

- Vous ne pouvez pas supprimer un groupe identique à partir du panneau **Virtual Machine Scale Sets**. Pour résoudre ce problème, sélectionnez le groupe identique que vous souhaitez supprimer, puis cliquez sur le bouton **Supprimer** dans le volet **Vue d’ensemble**.

### <a name="networking"></a>Mise en réseau

<!-- 3239127 - IS, ASDK -->
- Dans le portail Azure Stack, lorsque vous modifiez une adresse IP statique pour une configuration IP liée à une carte réseau connectée à une instance de machine virtuelle, le message d’avertissement suivant s’affiche :

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`

    Vous pouvez ignorer ce message. L’adresse IP changera même si l’instance de machine virtuelle ne redémarre pas.

<!-- 3632798 - IS, ASDK -->
- Dans le portail, si vous ajoutez une règle de sécurité de trafic entrant et sélectionnez **Balise du service** en tant que source, plusieurs options s’affichent dans la liste **Balise source**, qui ne sont pas disponibles pour Azure Stack. Les seules options valides dans Azure Stack sont les suivantes :

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**

  Les autres options ne sont pas pris en charge en tant que balises sources dans Azure Stack. De même, si vous ajoutez une règle de sécurité de trafic sortant et sélectionnez **Balise du service** en tant que destination, la même liste d’options s’affiche pour **Balise source**. Les seules options valides sont les mêmes que pour **Balise source**, comme décrit dans la liste précédente.

- Les Groupes de sécurité réseau (NSG) ne fonctionnent pas de la même manière dans Azure Stack que dans le reste d'Azure. Dans Azure, vous pouvez définir plusieurs ports sur une règle NSG (en utilisant les modèles du portail, de PowerShell et de Resource Manager). Dans Azure Stack, cependant, vous ne pouvez définir qu’un seul port sur une règle NSG via la portail. Pour contourner ce problème, utilisez un modèle Resource Manager ou PowerShell qui vous permettra de définir ces autres règles.

<!-- 3203799 - IS, ASDK -->
- Azure Stack ne permet pas d’attacher plus de quatre interfaces réseau à une instance de machine virtuelle, quelle que soit la taille de cette instance.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK -->
- Avant de créer votre première fonction Azure dans l’abonnement, vous devez inscrire le fournisseur de ressources de stockage.

<!-- ### Usage -->


<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>syslog

- La configuration syslog n’est pas conservée lors d’un cycle de mise à jour et, par conséquent, le client perd sa configuration et les messages syslog ne sont plus transférés. Ce problème s’applique à toutes les versions d’Azure Stack depuis la disponibilité générale du client syslog (1809). Pour contourner ce problème, reconfigurez le client syslog après avoir appliqué une mise à jour Azure Stack.

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1903 d’Azure Stack à partir de [cet emplacement](https://aka.ms/azurestackupdatedownload).

Dans les scénarios connectés, et uniquement dans ceux-ci, les déploiements Azure Stack consultent régulièrement un point de terminaison sécurisé et vous avertissent automatiquement si une mise à jour est disponible pour votre cloud. Pour plus d’informations, voir la documentation sur la [gestion des mises à jour pour Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](azure-stack-updates.md).
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](azure-stack-apply-updates.md).
- Pour passer en revue la stratégie de maintenance pour les systèmes intégrés Azure Stack et pour connaître la marche à suivre afin de conserver votre système dans un état de prise en charge, consultez [Stratégie de maintenance Azure Stack](azure-stack-servicing-policy.md).
- Pour utiliser le Point de terminaison privilégié (PEP) afin de surveiller et de reprendre les mises à jour, consultez [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](azure-stack-monitor-update.md).
