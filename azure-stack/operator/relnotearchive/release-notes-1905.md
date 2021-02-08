---
title: Notes de publication pour Azure Stack 1905 | Microsoft Docs
description: En savoir plus sur la mise à jour 1905 pour les systèmes intégrés Azure Stack
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
ms.date: 06/14/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/14/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 0f26f27cea7fabaaec9c6fd1507294f9bcbe3372
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248653"
---
# <a name="azure-stack-1905-update"></a>Mise à jour 1905 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit le contenu du package de mise à jour 1905. La mise à jour inclut des améliorations, nouveautés et correctifs pour cette version d’Azure Stack. Cet article contient les informations suivantes :

- [Description des nouveautés, améliorations, correctifs et mises à jour de sécurité](#whats-in-this-update)
- [Planification des mises à jour](#update-planning)

> [!IMPORTANT]  
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="build-reference"></a>Référence de build

Le numéro de build de la mise à jour 1905 d’Azure Stack est **1.1905.0.40**.

### <a name="update-type"></a>Type de mise à jour

Le type de build de la mise à jour 1905 d’Azure Stack est **Complet**. Par conséquent, la mise à jour 1905 a un runtime plus long que les mises à jour rapides, telles que 1903 et 1904. Les runtimes exacts des mises à jour complètes dépendent généralement du nombre de nœuds que votre instance Azure Stack contient, de la capacité utilisée sur votre système par les charges de travail clientes, de la connectivité réseau de votre système (s’il est connecté à Internet) et de la configuration de votre matériel système. La mise à jour 1905 présentait les runtimes attendus suivants dans nos tests internes : 4 nœuds - 35 heures, 8 nœuds - 45 heures, 12 nœuds - 55 heures, 16 nœuds - 70 heures. Les runtimes de la mise à jour 1905 plus longs que ces valeurs attendues ne sont pas rares et ne nécessitent aucune action de la part des opérateurs Azure Stack, sauf si la mise à jour échoue. Pour plus d’informations sur les types de build de mise à jour, voir [Gérer les mises à jour dans Azure Stack](../azure-stack-updates.md).

## <a name="whats-in-this-update"></a>Éléments de cette mise à jour

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Avec cette mise à jour, le moteur de mise à jour dans Azure Stack peut mettre à jour le microprogramme des nœuds d’unité d’échelle. Cela nécessite un package de mise à jour compatible des partenaires fournisseurs de matériel. Contactez votre fournisseur de matériel pour en savoir plus sur la disponibilité.

- Windows Server 2019 est désormais pris en charge et disponible pour la syndication par le biais de la Place de marché Azure Stack.
Avec cette mise à jour, Windows Server 2019 peut être activé correctement sur un hôte 2016.

- Avec la nouvelle [extension Azure Account Visual Studio Code](../../user/azure-stack-dev-start-vscode-azure.md), les développeurs peuvent cibler Azure Stack en se connectant et accédant aux abonnements, ainsi que divers autres services. L’extension Azure fonctionne aussi bien sur les environnements Azure Active Directory (Azure AD) et AD FS, et ne nécessite qu’une petite modification des paramètres utilisateur de Visual Studio Code. Visual Studio Code exige que le principal de service reçoive une autorisation à s’exécuter sur cet environnement. Pour ce faire, importez le script d’identité et exécutez les cmdlets spécifiées dans [Architecture mutualisée dans Azure Stack](../azure-stack-enable-multitenancy.md). Ceci nécessite une mise à jour du répertoire de base et l’inscription du répertoire du locataire invité pour chaque annuaire. Une alerte s’affiche après la mise à jour vers la version 1905 ou une version ultérieure, pour mettre à jour le locataire du répertoire de base pour lequel le principal de service Visual Studio Code est inclus. 

### <a name="improvements"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->
- Dans le cadre de l’application de TLS 1.2 sur Azure Stack, les extensions suivantes ont été mises à jour avec ces versions :

  - microsoft.customscriptextension-arm-1.9.3
  - microsoft.iaasdiagnostics-1.12.2.2
  - microsoft.antimalware-windows-arm-1.5.5.9
  - microsoft.dsc-arm-2.77.0.0
  - microsoft.vmaccessforlinux-1.5.2

  Téléchargez ces nouvelles versions d’extensions immédiatement afin d’éviter l’échec des nouveaux déploiements de l’extension quand TLS 1.2 sera appliqué dans une version ultérieure. Définissez toujours **autoUpgradeMinorVersion=true** pour que les mises à jour de version mineure des extensions (par exemple, version 1.8 à 1.9) soient effectuées automatiquement.

- La nouvelle fonctionnalité **Aide et support - Vue d’ensemble** du portail Azure Stack permet aux opérateurs de se renseigner sur leurs options de support, de bénéficier d’une aide spécialisée et d’en savoir plus sur Azure Stack plus facilement. Sur les systèmes intégrés, la création d’une demande de support présélectionne le service Azure Stack. Nous recommandons fortement aux clients d’utiliser ce moyen pour envoyer des tickets de support, plutôt que d’utiliser le portail Azure général. Pour plus d’informations, consultez [Aide et support de Microsoft Azure Stack](../azure-stack-help-and-support-overview.md).

- Quand plusieurs annuaires Azure Active Directory sont intégrés (par le biais de [ce processus](../azure-stack-enable-multitenancy.md)), il est possible de ne pas réexécuter le script lors de certaines mises à jour, ou lorsque des droits sont insuffisants en raison des modifications apportées à l’autorisation du principal de Service Azure AD. Ceci peut entraîner divers problèmes, d’un blocage de l’accès à certaines fonctionnalités à des erreurs plus discrètes qui sont difficiles à tracer jusqu’au problème d’origine. Pour éviter ce problème, la version 1905 introduit une nouvelle fonctionnalité qui vérifie les autorisations et déclenche une alerte en présence de certains problèmes de configuration. Cette validation s’exécute toutes les heures et affiche les actions de correction requises pour corriger le problème. L’alerte est fermée quand tous les locataires présentent un état sain.

- Plus grande fiabilité des opérations de sauvegarde de l’infrastructure pendant le basculement du service.

- Une nouvelle version du [plug-in Nagios pour Azure Stack](../azure-stack-integrate-monitor.md#integrate-with-nagios) est disponible. Elle utilise les [bibliothèques d’authentification Azure Active Directory](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) pour l’authentification. Désormais, le plug-in prend également en charge les déploiements Azure AD et Active Directory Federation Services (AD FS) d’Azure Stack. Pour plus d’informations, consultez le site [Nagios Exchange pour les plug-ins](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details).

- Un nouveau profil hybride **2019-03-01-Hybrid** a été publié. Il prend en charge toutes les dernières fonctionnalités disponibles dans Azure Stack. Azure PowerShell et Azure CLI prennent tous les deux en charge le profil **2019-03-01-Hybrid**. Les SDK .NET, Ruby, Node.js, Go et Python ont publié des packages qui prennent en charge le profil **2019-03-01-Hybrid**. La documentation respective et certains exemples ont été mis à jour pour refléter les modifications.

- Le [SDK Node.js](https://www.npmjs.com/search?q=2019-03-01-hybrid) prend désormais en charge les profils d’API. Les packages qui prennent en charge le profil **2019-03-01-Hybrid** sont publiés.

- La mise à jour Azure Stack 1905 ajoute deux nouveaux rôles d’infrastructure pour améliorer la fiabilité et la capacité de prise en charge de la plateforme :

  - **Anneau d’infrastructure** : À l’avenir, l’anneau d’infrastructure hébergera des versions en conteneur des rôles d’infrastructure existants (par exemple, xrp), qui nécessitent actuellement leurs propres machines virtuelles d’infrastructure désignées. Cela va améliorer la fiabilité de la plateforme et réduire le nombre de machines virtuelles d’infrastructure nécessaires à Azure Stack. Cela réduira ensuite la future consommation globale des ressources des rôles d’infrastructure Azure Stack.
  - **Anneau de support** À l’avenir, l’anneau de support servira à gérer les scénarios de prise en charge améliorée pour les clients.  

  En outre, nous avons ajouté une instance supplémentaire de la machine virtuelle du contrôleur de domaine pour améliorer la disponibilité de ce rôle.

  Ces modifications vont augmenter la consommation des ressources de l’infrastructure Azure Stack de la manière suivante :
  
    | Référence SKU Azure Stack | Augmentation de la consommation des capacités de calcul | Augmentation de la consommation de mémoire |
    | -- | -- | -- |
    |4 nœuds|22 processeurs virtuels|28 Go|
    |8 nœuds|38 processeurs virtuels|44 Go|
    |12 nœuds|54 processeurs virtuels|60 Go|
    |16 nœuds|70 processeurs virtuels|76 Go|
  
### <a name="changes"></a>Modifications

- Pour améliorer la fiabilité et la disponibilité durant les scénarios de maintenance planifiée et non planifiée, Azure Stack fournit une instance de rôle d’infrastructure supplémentaire pour les services de domaine.

- Avec cette mise à jour, pendant les opérations de réparation et d’ajout de nœuds, le matériel est validé pour garantir l’homogénéité des nœuds d’unité d’échelle au sein d’une unité d’échelle.

- Si des sauvegardes planifiées échouent et que la période de rétention définie est dépassée, le contrôleur de sauvegarde de l’infrastructure s’assure qu’au moins une sauvegarde réussie est conservée. 

### <a name="fixes"></a>Correctifs

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Correction d’un problème qui provoquait l’affichage d’un avertissement d’**agent hôte de calcul** après le redémarrage d’un nœud dans l’unité d’échelle.

- Correction des problèmes dans la gestion de la Place de marché dans le portail administrateur qui provoquaient l’affichage de résultats incorrects après l’application de filtres et l’affichage de noms d’éditeurs en double dans la liste filtrée des éditeurs. Les performances ont également été améliorées pour afficher les résultats plus rapidement.

- Correction d’un problème dans le panneau des sauvegardes disponibles qui montrait une nouvelle sauvegarde comme disponible avant qu’elle ait été chargée à l’emplacement de stockage externe. La sauvegarde disponible est désormais listée seulement après son chargement à l’emplacement de stockage. 

<!-- ICM: 114819337; Task: 4408136 -->
- Correction d’un problème avec l’obtention des clés de récupération pendant une opération de sauvegarde. 

<!-- Bug: 4525587 -->
- Correction d’un problème avec la mise à jour OEM qui affichait la version comme étant « non définie » dans le portail de l’opérateur.

### <a name="security-updates"></a>Mises à jour de sécurité

Pour plus d’informations sur les mises à jour de sécurité dans cette mise à jour d’Azure Stack, consultez [Mises à jour de sécurité Azure Stack](../release-notes-security-updates.md).

## <a name="update-planning"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Problèmes connus](known-issues-1905.md)
- [Mises à jour de sécurité](../release-notes-security-updates.md)
- [Liste de vérification des activités avant et après l’application de la mise à jour](../release-notes-checklist.md)

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1905 d’Azure Stack à partir de la [page de téléchargement d’Azure Stack](https://aka.ms/azurestackupdatedownload). Lorsque vous utilisez l’outil de téléchargement, veillez à utiliser la version la plus récente et pas une copie mise en cache à partir de votre répertoire de téléchargements.

## <a name="hotfixes"></a>Correctifs

Azure Stack publie des correctifs logiciels à intervalles réguliers. Avant d’installer la mise à jour 1905 d’Azure Stack, veillez à installer le dernier correctif logiciel d’Azure Stack pour la build 1904.

Les correctifs logiciels Azure Stack sont uniquement applicables aux systèmes intégrés Azure Stack. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

### <a name="before-applying-the-1905-update"></a>Avant d’appliquer la mise à jour 1905

La version 1905 d’Azure Stack doit être appliquée à la version 1904 avec les correctifs suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif Azure Stack 1.1904.4.45](https://support.microsoft.com/help/4505688)

### <a name="after-successfully-applying-the-1905-update"></a>Une fois la mise à jour 1905 correctement appliquée

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](../azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack hotfix 1.1905.3.48](https://support.microsoft.com/help/4510078)

## <a name="automatic-update-notifications"></a>Notifications de mise à jour automatique

Les clients utilisant des systèmes qui peuvent accéder à Internet à partir du réseau d’infrastructure voient le message **Mise à jour disponible** dans le portail opérateur. Les systèmes sans accès à Internet peuvent télécharger et importer le fichier .zip avec le fichier .xml correspondant.

> [!TIP]  
> Pour rester informé des publications des correctifs logiciels d’Azure Stack, abonnez-vous aux flux *RSS* ou *Atom* suivants :
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](../azure-stack-updates.md).  
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](../azure-stack-apply-updates.md).
- Pour passer en revue la stratégie de maintenance pour les systèmes intégrés Azure Stack et pour connaître la marche à suivre afin de conserver votre système dans un état de prise en charge, consultez [Stratégie de maintenance Azure Stack](../azure-stack-servicing-policy.md).  
- Pour utiliser le Point de terminaison privilégié (PEP) afin de surveiller et de reprendre les mises à jour, consultez [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](../azure-stack-monitor-update.md).  

