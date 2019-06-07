---
title: Notes de publication pour Azure Stack 1904 | Microsoft Docs
description: 'Découvrez la mise à jour 1904 pour les systèmes intégrés Azure Stack : nouveautés, problèmes connus, emplacement de téléchargement et notes de publications Azure Stack archivées.'
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
ms.date: 05/30/2019
ms.author: sethm
ms.reviewer: ''
ms.lastreviewed: 05/30/2019
ms.openlocfilehash: 8d9603184e824bff5efc2536b3c4a89fdd87d685
ms.sourcegitcommit: a427e72e4f3b6cd6000b1459af9bbf221e049e08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66506349"
---
# <a name="azure-stack-1904-update"></a>Mise à jour 1904 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit le contenu de la mise à jour 1904. La mise à jour inclut des améliorations, nouveautés et correctifs pour cette version d’Azure Stack. Cet article contient les informations suivantes :

- [Description des nouveautés, améliorations, correctifs et mises à jour de sécurité](azure-stack-release-notes-1904.md#whats-in-this-update)
- [Planification des mises à jour](azure-stack-release-notes-1904.md#update-planning)

> [!IMPORTANT]  
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="archived-release-notes"></a>Notes de publication archivées

Vous pouvez voir les [versions antérieures des notes de publication d’Azure Stack dans la galerie TechNet](http://aka.ms/azsarchivedrelnotes). Ces notes de publication archivées sont fournies uniquement à des fins de référence et n’impliquent aucune prise en charge de ces versions. Pour obtenir de l’aide, contactez les services de support technique Microsoft.

## <a name="build-reference"></a>Référence de build

Le numéro de build de la mise à jour 1904 d’Azure Stack est **1.1904.0.36**.

## <a name="whats-in-this-update"></a>Éléments de cette mise à jour

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

### <a name="improvements"></a>Améliorations

<!-- Changes and product improvements with tangible customer-facing value. -->

- La charge utile de la mise à jour 1904 contient une mise à jour pour les composants Azure Stack qui n’incluent pas le système d’exploitation sous-jacent hébergeant Azure Stack. Cela permet d’inclure certaines mises à jour dans l’étendue. Par conséquent, la mise à jour 1904 devrait prendre moins de temps (environ 16 heures, mais la durée exacte peut varier). Cette diminution dans le runtime est semblable à la mise à jour 1903. Toutefois, les mises à jour ultérieures peuvent contenir des mises à jour du système d’exploitation, ce qui implique différents runtimes. Les prochaines mises à jour contiendront des informations similaires sur le temps nécessaire à leur application en fonction de la charge utile incluse.

- Des améliorations significatives ont été apportées à la pile Mise en réseau à définition logicielle (SDN, Software Defined Networking) dans la version 1904. Ces améliorations augmentent la maintenance globale et la fiabilité de la pile SDN dans Azure Stack.

- Ajout d’une notification dans le portail administrateur, lorsque l’utilisateur actuellement connecté n’a pas les autorisations nécessaires, qui permet de charger correctement le tableau de bord. Elle contient également un lien vers la documentation qui explique les comptes qui ont des autorisations appropriées, selon le fournisseur d’identité utilisé pendant le déploiement.

- Ajout d’améliorations de la résilience et du temps d’activité des machines virtuelles, ce qui résout le scénario dans lequel toutes les machines virtuelles sont mises hors connexion si le volume de stockage contenant les fichiers de configuration des machines virtuelles est mis hors connexion.

<!-- 1901,2,3 related hotfix -->
- Ajout de l’optimisation du nombre de machines virtuelles évacuées simultanément et ajout d’un plafond sur la bande passante consommée, pour répondre aux baisses de tension ou pannes des machines virtuelles si le réseau est surchargé. Cette modification améliore la durée de fonctionnement des machines virtuelles lors de la mise à jour du système.

<!-- 1901,2,3 related hotfix -->
- Amélioration de la limitation de ressources lorsqu’un système est en cours d’exécution à grande échelle pour offrir une protection contre les processus internes qui épuisent les ressources de la plateforme, ce qui entraîne des échecs d’opérations dans le portail.

- Amélioration des fonctionnalités de filtrage permettant aux opérateurs d’appliquer plusieurs filtres en même temps. Vous pouvez effectuer un tri seulement sur la colonne **Nom** dans la nouvelle interface utilisateur.

- Améliorations apportées au processus de suppression d’offres, de plans, de quotas et d’abonnements. Vous pouvez maintenant correctement supprimer des offres, des quotas, des plans et des abonnements à partir du portail administrateur si l’objet que vous souhaitez supprimer n’a aucune dépendance. Pour plus d’informations, consultez [cet article](azure-stack-delete-offer.md).  

<!-- this applies to bug 3725384 and bug #4225643 -->
- Amélioration du volume de messages Syslog en filtrant les événements inutiles et en fournissant un paramètre de configuration pour sélectionner le niveau de gravité souhaité pour les messages transférés. Pour plus d’informations sur la façon de configurer le niveau de gravité, consultez [Intégration au centre de données Azure Stack - transfert syslog](azure-stack-integrate-security.md).

- À partir de la mise à jour 1904, l’infrastructure Azure Stack consomme 12 Go supplémentaires + (4 Go * nombre d’hôtes Azure Stack). Cela signifie que dans un tampon de 4 nœuds, une consommation de capacité supplémentaire de 28 Go (12 Go + 4 Go * 4) est reflétée dans l’écran Capacité du portail administrateur Azure Stack. Votre mise à jour vers la version 1904 doit réussir même si la consommation de mémoire supplémentaire entraîne le dépassement de la capacité de votre tampon Azure Stack. Si votre tampon Azure Stack a une utilisation supérieure en mémoire une fois que la mise à jour est effectuée, une alerte reflétant cet état s’affiche et indique les étapes de correction pour libérer certaines machines virtuelles.

<!--this applied to Bug 1473487 -->
- Ajout d’une nouvelle fonctionnalité à l’applet de commande **Get-AzureStackLog** en incorporant le paramètre supplémentaire `-OutputSASUri`. Vous pouvez maintenant collecter des journaux Azure Stack à partir de votre environnement et les stocker dans le conteneur d’objets blob Stockage Azure spécifié. Pour plus d’informations, consultez [Diagnostics Azure Stack](azure-stack-diagnostics.md#examples).

- Ajout d’un nouveau contrôle de la mémoire dans le groupe **Test-AzureStack** `UpdateReadiness`, qui vérifie si vous avez suffisamment de mémoire disponible sur la pile pour que la mise à jour s’effectue correctement.

<!-- Bug/Task 4311058 -->
- Améliorations apportées à **la commande Test-AzureStack** pour l’évaluation de l’intégrité de Service Fabric.

<!-- feature: 2976966 -->
- Améliorations apportées aux mises à jour du matériel, ce qui réduit le temps nécessaire pour effectuer la mise à jour du microprogramme des lecteurs à 2 à 4 heures. Le moteur de mise à jour détermine dynamiquement les portions de la mise à jour qui doivent s’exécuter, en fonction de contenu du package.

<!-- Feature 3906611 -->
- Ajout de vérifications préalables de la fiabilité des opérations pour empêcher les opérations d’instance de rôle d’infrastructure perturbatrices qui affectent la disponibilité.

<!-- Feature 3780326 -->
- Améliorations de l’idempotence du plan d’action de sauvegarde de l’infrastructure.

<!--Bug/Task 3139609 -->
- Améliorations apportées à la collecte des journaux Azure Stack. Ces améliorations réduisent le temps nécessaire pour récupérer l’ensemble des journaux. De plus, l’applet de commande [Get-AzureStackLog](azure-stack-diagnostics.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system) ne génère plus de journaux par défaut pour le rôle OEM. Vous devez exécuter l’applet de commande [Invoke-AzureStackOnDemandLog](azure-stack-diagnostics.md#invoke-azurestackondemandlog), en spécifiant le rôle pour récupérer les journaux OEM. Pour plus d’informations, consultez [Diagnostics Azure Stack](azure-stack-diagnostics.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system).

- Azure Stack surveille à présent l’URL de données de fédération fournie pour l’intégration de centre de données avec ADFS. Cela améliore la fiabilité au cours de la rotation des secrets de l’instance ADFS cliente ou de la batterie de serveurs.

### <a name="changes"></a>Changements

<!-- Feature 3906611 -->
- Suppression de l’option qui permet aux opérateurs Azure Stack d’arrêter les instances de rôle d’infrastructure dans le portail d’administration. La fonctionnalité de redémarrage garantit une tentative d’arrêt propre avant le redémarrage de l’instance de rôle d’infrastructure. Pour les scénarios avancés, la fonctionnalité d’API et PowerShell reste disponible.

<!-- Feature ## 4199257 -->
- Il existe une nouvelle expérience de gestion de la Place de marché, avec des écrans distincts pour les images de la Place de marché et les fournisseurs de ressources. Pour l’instant, la fenêtre **Fournisseurs de ressources** est vide, mais dans les futures versions, de nouvelles offres de services PaaS s’afficheront et seront gérées dans la fenêtre **Fournisseurs de ressources**.

<!-- Feature ## 4199257 -->
- Modifications de l’expérience de mise à jour dans le portail des opérateurs. Il existe une nouvelle grille pour les mises à jour pour les fournisseur de ressources. La possibilité de mettre à jour des fournisseurs de ressources n’est pas encore disponible.

<!-- Task ## 3748423  -->
- Modifications de l’expérience d’installation des mises à jour dans le portail des opérateurs. Pour aider les opérateurs Azure Stack à réagir de façon appropriée à un problème de mise à jour, le portail fournit à présent des recommandations plus spécifiques basées sur l’intégrité de l’unité d’échelle, dérivées automatiquement en exécutant **Test-AzureStack** et en analysant les résultats. En fonction du résultat, il demande à l’opérateur d’effectuer l’une des deux actions suivantes :

  - Une alerte d’avertissement « soft » s’affiche dans le portail. Elle indique « La mise à jour la plus récente nécessite votre attention. Microsoft recommande l’ouverture d’une demande de service pendant les heures ouvrées normales. Dans le cadre du processus de mise à jour, la commande Test-AzureStack est exécutée et en fonction de la sortie, nous générons l’alerte la plus appropriée. Dans ce cas, « Test-AzureStack a réussi ».

  - Une alerte critique « hard » s’affiche dans le portail qui indique « Échec de la mise à jour la plus récente. Microsoft recommande l’ouverture d’une demande de service dès que possible. Dans le cadre du processus de mise à jour, la commande Test-AzureStack est exécutée et en fonction de la sortie, nous générons l’alerte la plus appropriée. Dans ce cas, Test-AzureStack a également échoué ».

- Mise à jour de l’agent Azure Linux version 2.2.38.0. Cette prise en charge permet aux clients de maintenir la cohérence des images Linux entre Azure et Azure Stack.

- Modifications des journaux d’activité de mise à jour dans le portail des opérateurs. Les demandes de récupération de journaux d’activité des mises à jour réussies ne sont plus disponibles. Les journaux d’activité des mises à jour ayant échoué sont toujours téléchargeables, car ils sont exploitables à des fins de diagnostics.

### <a name="fixes"></a>Correctifs

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Correction du problème suivant : la configuration syslog n’était pas conservée lors d’un cycle de mise à jour et, par conséquent, le client perdait sa configuration et les messages syslog n’étaient plus transférés. La configuration Syslog est à présent conservée.

- Correction du problème suivant : CRP bloquait la libération de machines virtuelles. Précédemment, si une machine virtuelle contenait plusieurs disques managés volumineux, la libération de la machine virtuelle pouvait échouer avec une erreur de délai d’attente.

- Correction du problème suivant : le moteur Windows Defender avait un impact sur l’accès au stockage d’unité d’échelle.

- Correction du problème suivant : dans le portail utilisateur, la fenêtre Stratégie d’accès pour les comptes de stockage blob ne se chargeait pas.

- Correction du problème suivant : dans les portails administrateur et utilisateur, des notifications erronées au sujet du portail Azure mondial s’affichaient.

- Correction du problème suivant : dans le portail utilisateur, la sélection de la vignette **Commentaires** ouvrait un onglet de navigateur vide.

- Correction du problème suivant : dans le portail, la modification d’une adresse IP statique pour une configuration IP qui était liée à une carte réseau attachée à une instance de machine virtuelle entraînant l’affichage d’un message d’erreur.

- Correction du problème suivant : dans le portail utilisateur, une tentative de **connexion d’une interface réseau** à une machine virtuelle existante via la fenêtre **Mise en réseau** échouait avec un message d’erreur.

- Correction du problème suivant : Azure Stack ne permettait pas d’attacher plus de 4 interfaces réseau à une instance de machine virtuelle.

- Correction du problème suivant : dans le portail, l’ajout d’une règle de sécurité de trafic entrant et la sélection d’une **balise de service** en tant que source avait pour conséquence l’affichage de plusieurs options qui n’étaient pas disponibles pour Azure Stack.

- Correction du problème suivant : les groupes de sécurité réseau (NSG) ne fonctionnaient pas de la même manière dans Azure Stack que dans le reste d’Azure.

- Correction du problème suivant dans la gestion de la Place de marché : tous les produits téléchargés étaient masqués si l’inscription expirait ou était supprimée.

- Correction du problème suivant : l’émission d’une commande **Set-AzureRmVirtualNetworkGatewayConnection** dans PowerShell en direction d’une connexion de passerelle de réseau virtuel existante échouait avec le message d’erreur **La clé partagée configurée n’est pas valide...** .

- Correction du problème suivant : le fournisseur de ressources réseau (NRP) n’était pas synchronisé avec le contrôleur de réseau, ce qui entraînait des ressources en double demandées. Dans certains cas, cela avait pour conséquence qu’une ressource parente était dans un état d’erreur.

- Correction du problème suivant : quand un utilisateur avait reçu le rôle de contributeur à un abonnement, mais n’avait pas reçu explicitement des autorisations de lecture, l’erreur **... Le client « somelogonaccount@domain.com » avec l’ID d’objet {GUID} n’est pas autorisé à effectuer l’action...** était générée lors de la tentative d’enregistrement d’une modification de ressource.

- Correction du problème suivant : l’écran de gestion de la Place de marché était vide si l’outil de syndication hors connexion était utilisé pour charger des images et qu’aucune d’elles n’avait un URI d’icône.

- Correction d’un problème qui empêchait de supprimer de la gestion de la Place de marché les produits dont le téléchargement avait échoué.

### <a name="security-updates"></a>Mises à jour de sécurité

Cette mise à jour d’Azure Stack n’inclut pas les mises à jour de sécurité pour le système d’exploitation sous-jacent qui héberge Azure Stack. Pour plus d’informations, consultez [Mises à jour de sécurité d’Azure Stack](azure-stack-release-notes-security-updates-1904.md).

## <a name="update-planning"></a>Planification des mises à jour

Avant d’appliquer la mise à jour, veillez à consulter les informations suivantes :

- [Problèmes connus](azure-stack-release-notes-known-issues-1904.md)
- [Mises à jour de sécurité](azure-stack-release-notes-security-updates-1904.md)
- [Liste de vérification des activités avant et après l’application de la mise à jour](azure-stack-release-notes-checklist.md)

> [!NOTE]
> Veillez à utiliser la dernière version de l’outil [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner)  pour planifier et dimensionner votre charge de travail. La dernière version contient des corrections de bogues et fournit de nouvelles fonctionnalités publiées à chaque mise à jour d’Azure Stack.

## <a name="download-the-update"></a>Télécharger la mise à jour

Vous pouvez télécharger la mise à jour 1904 d’Azure Stack à partir de la [page de téléchargement d’Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Correctifs logiciels

Azure Stack publie des correctifs logiciels à intervalles réguliers. Avant d’installer la mise à jour 1904 d’Azure Stack, veillez à installer le dernier correctif logiciel d’Azure Stack pour la build 1903.

Les correctifs logiciels Azure Stack sont uniquement applicables aux systèmes intégrés Azure Stack. N’essayez pas d’installer des correctifs logiciels sur l’ASDK.

### <a name="before-applying-the-1904-update"></a>Avant d’appliquer la mise à jour 1904

La version 1904 d’Azure Stack doit être appliquée à la version 1903 avec les correctifs suivants :

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Correctif Azure Stack 1.1903.2.39](https://support.microsoft.com/help/4500638)

### <a name="after-successfully-applying-the-1904-update"></a>Une fois la mise à jour 1904 correctement appliquée

Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez notre [stratégie de maintenance](azure-stack-servicing-policy.md).

- [Correctif Azure Stack 1.1904.4.45](https://support.microsoft.com/help/4505688)

## <a name="automatic-update-notifications"></a>Notifications de mise à jour automatique

Les clients utilisant des systèmes qui peuvent accéder à Internet à partir du réseau d’infrastructure voient le message **Mise à jour disponible** dans le portail opérateur. Les systèmes sans accès à Internet peuvent télécharger et importer le fichier .zip avec le fichier .xml correspondant.

> [!TIP]  
> Pour rester informé des publications des correctifs logiciels d’Azure Stack, abonnez-vous aux flux *RSS* ou *Atom* suivants :
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="next-steps"></a>Étapes suivantes

- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](azure-stack-updates.md).  
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](azure-stack-apply-updates.md).
- Pour passer en revue la stratégie de maintenance pour les systèmes intégrés Azure Stack et pour connaître la marche à suivre afin de conserver votre système dans un état de prise en charge, consultez [Stratégie de maintenance Azure Stack](azure-stack-servicing-policy.md).  
- Pour utiliser le Point de terminaison privilégié (PEP) afin de surveiller et de reprendre les mises à jour, consultez [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](azure-stack-monitor-update.md).  

