---
title: Mise à jour 1807 d’Azure Stack | Microsoft Docs
description: Découvrez les nouveautés de la mise à jour 1807 pour les systèmes intégrés Azure Stack, y compris les problèmes connus et l’emplacement de téléchargement de la mise à jour.
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
ms.date: 09/26/2018
ms.author: sethm
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 9dd3647f607dc743c6eff5907ae4a47fa097e764
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248616"
---
# <a name="azure-stack-1807-update"></a>Mise à jour 1807 d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Cet article décrit le contenu de la mise à jour 1807. Cette mise à jour inclut des améliorations, des corrections, des problèmes connus de cette version d’Azure Stack et l’emplacement de téléchargement de la mise à jour. Les problèmes connus sont divisés en problèmes directement liés au processus de mise à jour et en problèmes propres à la build (après l’installation).

> [!IMPORTANT]  
> Cette mise à jour est destinée uniquement aux systèmes intégrés d’Azure Stack. N’appliquez pas cette mise à jour au Kit de développement Azure Stack.

## <a name="build-reference"></a>Référence de build

Le numéro de build de mise à jour d’Azure Stack 1807 est **1.1807.0.76**.  

### <a name="new-features"></a>Nouvelles fonctionnalités

Cette mise à jour inclut les améliorations suivantes pour Azure Stack.

<!-- 1658937 | ASDK, IS --> 
- **Démarrer les sauvegardes selon un calendrier prédéfini** : En tant qu’appliance, Azure Stack peut maintenant lancer automatiquement des sauvegardes périodiques de l’infrastructure afin d’éviter toute intervention humaine. Azure Stack nettoie également automatiquement le partage externe des sauvegardes antérieures à la période de rétention définie. Pour en savoir plus, voir [Activer la sauvegarde d’Azure Stack avec PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 2496385 | ASDK, IS -->  
- **Ajout du temps de transfert des données dans le temps total de sauvegarde.** Pour en savoir plus, voir [Activer la sauvegarde d’Azure Stack avec PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 1702130 | ASDK, IS -->  
- **La capacité externe de sauvegarde affiche maintenant la capacité correcte du partage externe.** (la valeur était codée en dur à 10 Go) Pour en savoir plus, voir [Activer la sauvegarde d’Azure Stack avec PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 2508488 |  IS   -->
- **Augmenter la capacité** en [ajoutant des nœuds d’unité d’échelle supplémentaires](../azure-stack-add-scale-node.md).

<!-- 2753130 |  IS, ASDK   -->  
- **Les modèles Azure Resource Manager prennent désormais en charge l’élément de condition** : Vous pouvez désormais déployer une ressource dans un modèle Azure Resource Manager à l’aide d’une condition. Vous pouvez concevoir votre modèle afin de déployer une ressource en fonction d’une condition, par exemple évaluer si une valeur de paramètre est présente. Pour plus d’informations sur l’utilisation d’un modèle en tant que condition, consultez [Déployer une ressource de manière conditionnelle](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) et [Section Variables des modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) dans la documentation Azure. 

   Vous pouvez également utiliser des modèles pour [déployer des ressources Azure sur plusieurs groupes de ressources ou abonnements](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

<!--2753073 | IS, ASDK -->  
- **La prise en charge de la version de ressource d’API Microsoft.Network a été mise à jour** pour inclure la prise en charge de la version d’API 2017-10-01 à partir de la version 2015-06-15 pour les ressources réseau Azure Stack.  La prise en charge des versions de ressources entre 2017-10-01 et 2015-06-15 n’est pas incluse dans cette version.  Pour connaître les différences de fonctionnalités, consultez [Considérations relatives à la mise en réseau Azure Stack](../../user/azure-stack-network-differences.md).

<!-- 2272116 | IS, ASDK   -->  
- **Azure Stack a ajouté la prise en charge des recherches DNS inversées pour les points de terminaison d’infrastructure Azure Stack externes** (autrement dit pour portal, adminportal, management et adminmanagement). Cela permet de résoudre les noms des points de terminaison externes Azure Stack à partir d’une adresse IP.

<!-- 2780899 |  IS, ASDK   --> 
- **Azure Stack prend désormais en charge l’ajout d’interfaces réseau supplémentaires à une machine virtuelle existante.**  Cette fonctionnalité est disponible à l’aide du portail, de PowerShell et de l’interface CLI. Pour plus d’informations, consultez [Ajouter ou supprimer des interfaces réseau](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) dans la documentation Azure. 

<!-- 2222444 | IS, ASDK   -->  
- **Des améliorations en matière de précision et de résilience ont été apportées aux compteurs d’utilisation de mise en réseau**.  Les compteurs d’utilisation réseau sont désormais plus précis et tiennent compte des abonnements suspendus, des périodes d’interruption et des conditions de concurrence.

<!-- 2753080 | IS -->  
- **Notification de disponibilité de mise à jour.** Les déploiements Azure Stack connectés contactent désormais périodiquement un point de terminaison sécurisé afin de déterminer si une mise à jour est disponible pour votre cloud. Cette notification s’affiche dans la vignette Mise à jour, comme elle le ferait après la vérification et l’importation manuelles d’une nouvelle mise à jour. Découvrez-en plus sur la [gestion des mises à jour pour Azure Stack](../azure-stack-updates.md).

<!-- 2297790 | IS, ASDK -->  
- **Améliorations apportées au client syslog Azure Stack (fonctionnalité en préversion)**. Ce client permet le transfert des journaux d’activité et de l’audit liés à l’infrastructure d’Azure Stack vers un serveur Syslog ou un logiciel SIEM externe à Azure Stack. Le client syslog prend désormais en charge le protocole TCP avec texte brut ou chiffrement TLS 1.2, cette dernière option étant la configuration par défaut. Vous pouvez configurer la connexion TLS avec authentification mutuelle ou serveur uniquement.

  Pour configurer la façon dont le client syslog communique avec le serveur syslog (par exemple le protocole, le chiffrement et l’authentification), utilisez l’applet de commande *Set-SyslogServer*. Cette applet de commande est disponible à partir du point de terminaison privilégié (PEP).

  Pour ajouter le certificat côté client pour l’authentification mutuelle TLS 1.2 du client syslog, utilisez l’applet de commande Set-SyslogClient dans le PEP.

  Avec cette préversion, vous pouvez voir un plus grand nombre d’audits et d’alertes. 

  Cette fonctionnalité étant toujours en préversion, ne vous reposez pas sur elle dans les environnements de production.

  Pour plus d’informations, consultez [Transfert de syslog dans Azure Stack](../azure-stack-integrate-security.md).

<!-- ####### | IS, ASDK | --> 
- **Azure Resource Manager inclut le nom de la région.** Avec cette version, les objets récupérés à partir d’Azure Resource Manager incluront désormais l’attribut de nom de région. Si un script PowerShell existant passe directement l’objet à une autre applet de commande, le script peut générer une erreur et échouer. Ce comportement est conforme à Azure Resource Manager et oblige le client appelant à soustraire l’attribut de région. Pour plus d’informations sur Azure Resource Manager, consultez la [documentation Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/). verify 8-10 mdb -->

<!-- TBD | IS, ASDK -->  
- **Modifications apportées à la fonctionnalité Fournisseurs délégués.** À compter de la version 1807, le modèle Fournisseurs délégués est simplifié afin de mieux s’aligner sur le modèle de revendeur Azure, et les fournisseurs délégués ne pourront pas créer d’autres fournisseurs délégués, ce qui revient à aplanir le modèle et à rendre la fonctionnalité de fournisseur délégué disponible sur un seul niveau. Pour permettre la transition vers le nouveau modèle et la gestion des abonnements, vous pouvez maintenant déplacer les abonnements utilisateur entre des abonnements Fournisseur délégué nouveaux ou existants qui appartiennent au même locataire d’annuaire. Les abonnements utilisateur appartenant à l’abonnement Fournisseur par défaut peuvent également être déplacés vers les abonnements Fournisseur délégué dans le même locataire d’annuaire.  Pour plus d’informations, consultez [Déléguer des offres dans Azure Stack](../azure-stack-delegated-provider.md).

<!-- 2536808 IS ASDK --> 
- **Amélioration du temps de création de machine virtuelle** pour les machines virtuelles qui sont créées avec des images que vous téléchargez à partir de la Place de marché Azure.

<!-- TBD | IS, ASDK -->  
- **Améliorations de facilité d’utilisation d’Azure Stack Capacity Planner**. Azure Stack [Capacity Planner](https://aka.ms/azstackcapacityplanner) offre désormais une expérience simplifiée pour la saisie de cache S2D et de capacité S2D lors de la définition des références (SKU) de solution. La limite de 1000 machines virtuelles a été supprimée.


### <a name="fixed-issues"></a>Problèmes résolus

<!-- TBD | ASDK, IS --> 
- Différentes améliorations ont été apportées au processus de mise à jour afin de le rendre plus fiable. Des correctifs ont également été apportés à l’infrastructure sous-jacente afin de réduire le risque d’interruption pour les charges de travail pendant la mise à jour.

<!--2292271 | ASDK, IS --> 
- Nous avons corrigé un problème qui empêchait l’application d’une limite de quota modifiée aux abonnements existants. Désormais, lorsque vous augmentez la limite de quota d’une ressource réseau faisant partie d’une offre et d’un plan associés à un abonnement d’utilisateur, la nouvelle limite s’applique aux abonnements préexistants, ainsi qu’aux nouveaux abonnements.

<!-- 448955 | IS ASDK --> 
- Il est désormais possible d’interroger correctement les journaux d’activité des systèmes déployés dans un fuseau horaire UTC+N.    

<!-- 2319627 |  ASDK, IS --> 
- La vérification préalable des paramètres de configuration de la sauvegarde (chemin, nom d’utilisateur, mot de passe, clé de chiffrement) ne définit plus de paramètres incorrects pour la configuration de la sauvegarde. (Les paramètres incorrects étaient définis auparavant dans la sauvegarde, et celle-ci échouait lors de son déclenchement.)

<!-- 2215948 |  ASDK, IS -->
- La liste de sauvegarde s’actualise désormais quand vous supprimez manuellement la sauvegarde du partage externe.

<!-- 2332636 | IS -->  
- La mise à jour de cette version ne réinitialise plus le propriétaire par défaut de l’abonnement du fournisseur par défaut à l’utilisateur **CloudAdmin** intégré en cas de déploiement avec AD FS.

<!-- 2360715 |  ASDK, IS -->  
- Quand vous configurez l’intégration du centre de données, vous n’accédez plus au fichier de métadonnées AD FS à partir d’un partage de fichiers Azure. Pour plus d’informations, consultez la rubrique [Configuration de l’intégration AD FS en fournissant un fichier de métadonnées de fédération](../azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

<!-- 2388980 | ASDK, IS --> 
- Nous avons corrigé un problème qui empêchait les utilisateurs d’attribuer une adresse IP publique existante ayant déjà été attribuée à une interface réseau ou à un équilibreur de charge à une nouvelle interface réseau ou à un nouvel équilibreur de charge.  

<!-- 2551834 - IS, ASDK --> 
- Quand vous sélectionnez *Vue d’ensemble* pour un compte de stockage dans le portail d’administration ou utilisateur, le volet *Éléments principaux* affiche désormais correctement toutes les informations attendues. 

<!-- 2551834 - IS, ASDK --> 
- Quand vous sélectionnez *Balises* pour un compte de stockage dans le portail d’administration ou utilisateur, les informations sont désormais affichées correctement.

<!-- TBD - IS ASDK --> 
- Cette version d’Azure Stack corrige le problème qui empêchait l’application des mises à jour de pilote à partir de packages d’extension OEM.

<!-- 2055809- IS ASDK --> 
- Nous avons résolu un problème qui vous empêchait de supprimer des machines virtuelles du panneau de calcul quand la création de la machine virtuelle échouait.  

<!--  2643962 IS ASDK -->  
- L’alerte de *faible capacité mémoire* n’apparaît plus incorrectement.

- **Divers correctifs** pour les performances, la stabilité, la sécurité et le système d’exploitation utilisé par Azure Stack.


<!-- ### Additional releases timed with this update    -->

### <a name="common-vulnerabilities-and-exposures"></a>Failles et menaces courantes
Azure Stack utilise des installations Server Core de Windows Server 2016 pour héberger l’infrastructure clé. Cette version installe les mises à jour suivantes de Windows Server 2016 sur les serveurs d’infrastructure pour Azure Stack : 
- [CVE-2018-8206](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8206)
- [CVE-2018-8222](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8222)
- [CVE-2018-8282](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8282)
- [CVE-2018-8304](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8304)
- [CVE-2018-8307](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8307)
- [CVE-2018-8308](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8308) 
- [CVE-2018-8309](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8309)
- [CVE-2018-8313](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8313)  

Pour plus d’informations sur ces vulnérabilités, cliquez sur les liens précédents ou consultez les articles de la Base de connaissances Microsoft [4338814](https://support.microsoft.com/help/4338814) et [4345418](https://support.microsoft.com/help/4345418).



## <a name="before-you-begin"></a>Avant de commencer

### <a name="prerequisites"></a>Prérequis

- Installez la mise à jour 1805 d’Azure Stack avant d’appliquer la mise à jour 1807.  Il n’existe pas de mise à jour 1806.  

- Installez la dernière mise à jour ou le dernier correctif logiciel disponibles pour la version 1805.  

- Avant de démarrer l’installation de cette mise à jour, exécutez [Test-AzureStack](../azure-stack-diagnostic-test.md) avec les paramètres suivants pour valider l’état de votre Azure Stack et résoudre les éventuels problèmes opérationnels détectés, y compris tous les avertissements et les échecs. Examinez aussi les alertes actives et résolvez toutes celles qui nécessitent une intervention.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>Problèmes connus avec le processus de mise à jour

<!-- 2468613 - IS --> 
- Pendant l’installation de cette mise à jour, des alertes avec le titre *Erreur - Le modèle de FaultType UserAccounts.New est manquant* peuvent s’afficher.  Vous pouvez ignorer ces alertes de manière sécurisée. Elles se fermeront automatiquement une fois l’installation de la mise à jour terminée.

<!-- 2489559 - IS --> 
- Ne tentez pas de créer des machines virtuelles pendant l’installation de cette mise à jour. Pour plus d’informations sur la gestion des mises à jour, consultez [Manage updates in Azure Stack overview](../azure-stack-updates.md) (Vue d’ensemble de la gestion des mises à jour dans Azure Stack).

<!-- 2830461 - IS --> 
- Dans certaines circonstances quand une mise à jour nécessite une attention particulière, l’alerte correspondante peut ne pas être générée. L’état précis apparaît quand même dans le portail et n’est pas affecté.

### <a name="post-update-steps"></a>Étapes après la mise à jour
Après l’installation de cette mise à jour, installez les correctifs logiciels applicables. Pour plus d’informations, consultez les articles suivants de la base de connaissances, ainsi que notre [stratégie de maintenance](../azure-stack-servicing-policy.md). 
- [KB 4464231 - Correctif Azure Stack 1.1807.1.78](https://support.microsoft.com/help/4464231)

<!-- 2933866 IS -->  Après l’installation de cette mise à jour, vous observerez une **amélioration des informations d’état en cas d’échec d’une installation de mise à jour**. Par exemple, les informations sur les échecs d’installations de mises à jour antérieures ont été modifiées pour refléter les deux nouvelles catégories d’état (STATE). Les nouvelles catégories STATE sont *PreparationFailed* et *InstallationFailed*.  

## <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

Les éléments suivants sont des problèmes connus qui apparaissent après l’installation de cette build.

### <a name="portal"></a>Portail

- La documentation technique Azure Stack s’applique à la dernière version. En raison des changements apportés au portail entre les versions, ce que vous voyez dans les portails Azure Stack peut différer du contenu de la documentation. 

- L’option pour [ouvrir une nouvelle demande de support à partir de la liste déroulante](../azure-stack-manage-portals.md#quick-access-to-help-and-support) dans le portail d’administration n’est pas disponible. À la place, pour les systèmes intégrés Azure Stack, utilisez le lien suivant : [https://aka.ms/newsupportrequest](https://aka.ms/newsupportrequest).

<!-- 2931230 IS  ASDK --> 
- Les plans ajoutés à un abonnement utilisateur comme plan d’extension ne peuvent pas être supprimés, même quand vous supprimez le plan de l’abonnement utilisateur. Le plan est conservé jusqu’à ce que les abonnements qui référencent le plan d’extension soient aussi supprimés. 

<!--2760466 IS  ASDK --> 
-  Quand vous installez un nouvel environnement Azure Stack qui exécute cette version, l’alerte qui indique *Activation requise* peut ne pas s’afficher. L’[activation](../azure-stack-registration.md) est nécessaire avant que vous puissiez utiliser la syndication de Place de marché.  

<!-- TBD - IS ASDK --> 
- Les deux types d’abonnements d’administration qui ont été introduits avec la version 1804 ne doivent pas être utilisés. Les types d’abonnements sont **Abonnement de contrôle** et **Abonnement de consommation**. Ces types d’abonnements sont visibles dans les nouveaux environnements Azure Stack depuis la version 1804, mais ils ne sont pas encore prêts à être utilisés. Vous devez continuer à utiliser le type d’abonnement **Fournisseur par défaut**.

<!-- 2403291 - IS ASDK --> 
- Il se peut que vous ne puissiez pas utiliser la barre de défilement horizontale au bas du portail d’administration et du portail utilisateur. Si vous ne pouvez pas accéder à la barre de défilement horizontale, utilisez les barres de navigation pour accéder à un panneau précédent dans le portail, en sélectionnant le nom du panneau que vous souhaitez afficher dans la liste des barres de navigation en haut à gauche du portail.

<!-- TBD - IS --> 
- Il se peut qu’il ne soit pas possible d’afficher les ressources de calcul ou de stockage sur le portail d’administration. Ce problème est dû au fait qu’une erreur s’est produite pendant l’installation de la mise à jour, et que celle-ci a été, à tort, signalée comme réussie. Si ce problème se produit, contactez les services de support technique Microsoft pour obtenir de l’aide.

<!-- TBD - IS --> 
- Il se peut qu’un tableau de bord vide s’affiche sur le portail. Pour récupérer le tableau de bord, sélectionnez l’icône d’engrenage dans l’angle supérieur droit du portail, puis choisissez **Restaurer les paramètres par défaut**.

<!-- TBD - IS ASDK --> 
- La suppression d’abonnements utilisateur aboutit à des ressources orphelines. Pour contourner ce problème, commencez pas supprimer des ressources d’utilisateurs ou la totalité du groupe de ressources, puis supprimez les abonnements utilisateur.

<!-- TBD - IS ASDK --> 
- Vous ne pouvez pas afficher les autorisations définies pour votre abonnement à l’aide des portails Azure Stack. Pour résoudre ce problème, utilisez PowerShell pour vérifier les autorisations.


### <a name="health-and-monitoring"></a>Intégrité et surveillance
<!-- 1264761 - IS ASDK -->  
-   Vous risquez de recevoir des alertes pour le composant du **contrôleur d’intégrité** contenant les informations suivantes :  

   Alerte 1 :
   - NOM : Rôle d’infrastructure défectueux
   - GRAVITÉ : Avertissement
   - COMPOSANT : Contrôleur d’intégrité
   - DESCRIPTION : L’analyseur de pulsations du contrôleur d’intégrité n’est pas disponible. Cela peut affecter les rapports et les métriques d’intégrité.  

  Alerte 2 :
   - NOM : Rôle d’infrastructure défectueux
   - GRAVITÉ : Avertissement
   - COMPOSANT : Contrôleur d’intégrité
   - DESCRIPTION : L’analyseur d’erreur du contrôleur d’intégrité n’est pas disponible. Cela peut affecter les rapports et les métriques d’intégrité.

  Les deux alertes peuvent être ignorées en toute sécurité. Elles se ferment automatiquement au bout d’un moment.  


<!-- 2812138 | IS --> 
- Vous pouvez voir une alerte pour le composant **Stockage** avec les informations suivantes :

   - NOM : Erreur de communication interne du service de stockage  
   - GRAVITÉ : Critique  
   - COMPOSANT : Stockage  
   - DESCRIPTION : Une erreur de communication interne du service de stockage s’est produite lors de l’envoi des requêtes aux nœuds suivants.  

    L’alerte peut être ignorée sans risque, mais vous devez la fermer manuellement.

<!-- 2368581 - IS. ASDK --> 
- En tant qu’opérateur d’Azure Stack, si vous recevez une alerte de mémoire insuffisante et que les machines virtuelles de locataire ne parviennent pas à se déployer à cause d’une **erreur de création de machine virtuelle de structure**, il est possible que l’horodatage d’Azure Stack soit à court de mémoire. Utilisez le [Capacity Planner Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) pour mieux comprendre la capacité disponible pour vos charges de travail.


### <a name="compute"></a>Calcul

<!-- 2724873 - IS --> 
-  En utilisant les cmdlets PowerShell **Start-AzsScaleUnitNode** ou **Stop-AzsScaleunitNode** pour gérer les unités d’échelle, il est possible que la première tentative de démarrage ou d’arrêt de l’unité d’échelle échoue. Si la cmdlet échoue une première fois, exécutez-la une seconde fois. La deuxième exécution doit permettre de terminer l’opération. 

<!-- 2494144 - IS, ASDK --> 
- Quand vous sélectionnez une taille de machine virtuelle pour un déploiement de machine virtuelle, certaines tailles de machine virtuelle de série F ne sont pas visibles dans le sélecteur de taille lorsque vous créez une machine virtuelle. Les tailles de machine virtuelle suivantes n’apparaissent pas dans le sélecteur : *F8s_v2*, *F16s_v2*, *F32s_v2* et *F64s_v2*.  
  Pour résoudre ce problème, utilisez l’une des méthodes suivantes pour déployer une machine virtuelle. Dans chaque méthode, vous devez spécifier la taille de machine virtuelle que vous voulez utiliser.

  - **Modèle Azure Resource Manager :** lorsque vous utilisez un modèle, définissez la valeur *vmSize* dans le modèle pour qu’elle soit égale à la taille de machine virtuelle que vous voulez utiliser. Par exemple, l’entrée suivante permet de déployer une machine virtuelle qui utilise la taille *F32s_v2* :  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI :** Vous pouvez utiliser la commande [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create&preserve-view=true) et spécifiez la taille de machine virtuelle comme paramètre, identique à `--size "Standard_F32s_v2"`.

  - **PowerShell :** avec PowerShell, vous pouvez utiliser [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0&preserve-view=true) avec le paramètre qui spécifie la taille de machine virtuelle, identique à `-VMSize "Standard_F32s_v2"`.


<!-- TBD - IS ASDK --> 
- Les paramètres de mise à l’échelle des groupes de machines virtuelles identiques ne sont pas disponibles dans le portail. Pour résoudre ce problème, vous pouvez utiliser [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). En raison des différences de version de PowerShell, vous devez utiliser le paramètre `-Name` au lieu du paramètre `-VMScaleSetName`.

<!-- TBD - IS --> 
- Lorsque vous créez un groupe à haute disponibilité dans le portail en accédant à **Nouveau** > **Compute** > **Groupe à haute disponibilité**, vous pouvez uniquement créer un groupe à haute disponibilité avec un domaine d’erreur et un domaine de mise à jour de 1. Pour contourner ce problème, lors de la création d’une nouvelle machine virtuelle, créez le groupe à haute disponibilité à l’aide de PowerShell, CLI, ou depuis le portail.

<!-- TBD - IS ASDK --> 
- Lorsque vous créez des machines virtuelles sur le portail utilisateur Azure Stack, le portail affiche un nombre incorrect de disques de données que vous pouvez attacher à une machine virtuelle de la série DS. Les machines virtuelles de série DS peuvent prendre en charge autant de disques de données que la configuration Azure.

<!-- TBD - IS ASDK --> 
- Si l’approvisionnement d’une extension sur un déploiement de machine virtuelle prend trop de temps, les utilisateurs doivent laisser expirer le délai d’attente plutôt que d’essayer d’arrêter le processus pour désallouer ou supprimer la machine virtuelle.  

<!-- 1662991 IS ASDK --> 
- Les diagnostics de machine virtuelle Linux ne sont pas pris en charge dans Azure Stack. Lorsque vous déployez une machine virtuelle Linux en activant les diagnostics de machine virtuelle, le déploiement échoue. Le déploiement échoue également si vous activez les mesures de base de la machine virtuelle Linux dans les paramètres de diagnostic.  

<!-- 2724961- IS ASDK --> 
- Quand vous inscrivez le fournisseur de ressources **Microsoft.Insight** dans les paramètres d’abonnement, puis créez une machine virtuelle Windows en ayant activé les diagnostics du système d’exploitation invité, la page de vue d’ensemble de la machine virtuelle n’affiche pas les données de métriques. 

   Pour trouver les données de métriques, comme le graphique de pourcentage d’UC, accédez au panneau **Métriques** et affichez toutes les métriques d’invité de machine virtuelle Windows prises en charge.

### <a name="networking"></a>Mise en réseau  

<!-- 1766332 - IS ASDK --> 
-  Sous **Mise en réseau**, si vous cliquez sur **Créer une passerelle VPN** pour configurer une connexion VPN, l’option **Basé sur des stratégies** s’affiche dans la liste des types de VPN. Ne sélectionnez pas cette option. Seule l’option **Basé sur itinéraires** est prise en charge dans Azure Stack.

<!-- 1902460 - IS ASDK --> 
- Azure Stack prend en charge une seule *passerelle de réseau local* par adresse IP. Cela est vrai pour tous les abonnements de locataire. Après la création de la première connexion de passerelle de réseau local, les tentatives suivantes de création d’une ressource de passerelle de réseau local avec la même adresse IP sont bloquées.

<!-- 16309153 - IS ASDK --> 
- Sur un réseau virtuel a été créé avec un paramètre de serveur DNS défini sur *Automatique*, vous ne pouvez pas choisir un serveur DNS personnalisé. Les paramètres mis à jour ne sont pas envoyés (par push) aux machines virtuelles dans ce réseau virtuel.

<!-- 2702741 -  IS ASDK --> 
- La conservation des adresses IP publiques déployées à l’aide de la méthode d’allocation dynamique n’est pas garantie après l’émission d’une commande Arrêter/Libérer.

<!-- 2529607 - IS ASDK --> 
- Durant la *rotation des secrets* Azure Stack, une période de deux à cinq minutes s’écoule pendant laquelle les adresses IP publiques sont inaccessibles.

<!-- 2664148 - IS ASDK --> 
- Dans les cas où le locataire accède à ses machines virtuelles à l’aide d’un tunnel VPN S2S, il peut rencontrer un scénario où les tentatives de connexion échouent si le sous-réseau local a été ajouté à la passerelle de réseau local après la création de la passerelle. 


### <a name="sql-and-mysql"></a>SQL et MySQL



<!-- No Number - IS, ASDK -->  
- Les caractères spéciaux, notamment les espaces et les points, ne sont pas pris en charge dans les noms de **Famille** lors de la création d’une référence SKU pour les fournisseurs de ressources SQL et MySQL. 

<!-- TBD - IS --> 
- Seul le fournisseur de ressources est pris en charge pour créer des éléments sur des serveurs qui hébergent SQL ou MySQL. Les éléments créés sur un serveur hôte qui ne sont pas créés par le fournisseur de ressources peuvent entraîner un état qui ne correspond pas.  

<!-- TBD - IS -->
> [!NOTE]   
> Après la mise à jour vers cette version d’Azure Stack, vous pouvez continuer à utiliser les fournisseurs de ressources SQL et MySQL que vous avez déjà déployés.  Nous vous recommandons de mettre à jour SQL et MySQL lorsqu’une nouvelle version est disponible. Comme Azure Stack, appliquez de façon séquentielle les mises à jour aux fournisseurs de ressources SQL et MySQL. Par exemple, si vous utilisez la version 1804, commencez par appliquer la version 1805 avant de passer à la version 1807.  
>  
> L’installation de cette mise à jour n’affecte pas l’utilisation actuelle des fournisseurs de ressources SQL ou MySQL par vos utilisateurs. 
> Quelle que soit la version des fournisseurs de ressources que vous utilisez, vos données utilisateur dans leurs bases de données restent intactes et accessibles.  

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Les utilisateurs doivent inscrire le fournisseur de ressources de stockage avant de créer leur première fonction Azure dans l’abonnement.

<!-- 2489178 - IS ASDK --> 
- Pour monter en puissance l’infrastructure (Workers, gestion, rôles frontaux), vous devez utiliser PowerShell comme décrit dans les notes de publication pour Compute.

<!-- TBD - IS ASDK --> 
- Le déploiement d’App Service n’est possible à l’heure actuelle que sur l’*abonnement du fournisseur par défaut*. 


### <a name="usage"></a>Utilisation  
<!-- TBD - IS ASDK --> 
-  Les données d’utilisation des adresses IP publiques indiquent une même valeur *EventDateTime* pour chaque enregistrement au lieu de l’horodatage *TimeDate* qui s’affiche lors de la création de chaque enregistrement. Pour le moment, vous ne pouvez pas utiliser ces données pour calculer de manière précise l’utilisation des adresses IP publiques.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Télécharger la mise à jour
Vous pouvez télécharger le package de mise à jour Azure Stack 1807 à partir de [cet emplacement](https://aka.ms/azurestackupdatedownload).


## <a name="next-steps"></a>Étapes suivantes
- Pour passer en revue la stratégie de maintenance pour les systèmes intégrés Azure Stack et pour connaître la marche à suivre afin de conserver votre système dans un état de prise en charge, consultez [Stratégie de maintenance Azure Stack](../azure-stack-servicing-policy.md).  
- Pour utiliser le Point de terminaison privilégié (PEP) afin de surveiller et de reprendre les mises à jour, consultez [Surveiller les mises à jour dans Azure Stack à l’aide du point de terminaison privilégié](../azure-stack-monitor-update.md).  
- Pour obtenir une vue d’ensemble de la gestion des mises à jour dans Azure Stack, consultez [Gérer les mises à jour dans Azure Stack - Vue d’ensemble](../azure-stack-updates.md).  
- Pour plus d’informations sur la façon d’appliquer des mises à jour avec Azure Stack, consultez [Effectuer des mises à jour dans Azure Stack](../azure-stack-apply-updates.md).  