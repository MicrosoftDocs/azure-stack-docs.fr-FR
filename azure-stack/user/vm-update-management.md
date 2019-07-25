---
title: Gestion et mise à jour des machines virtuelles avec Azure Stack | Microsoft Docs
description: Découvrez comment utiliser les solutions Azure Monitor pour machines virtuelles, Update Management, Change Tracking et Inventory dans Azure Automation pour gérer les machines virtuelles Windows et Linux déployées dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: rtiberiu
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: 1e43f5df97ff91e7117759027499ffa6cd0df7c8
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418250"
---
# <a name="azure-stack-vm-update-and-management"></a>Gestion et mise à jour des machines virtuelles avec Azure Stack
Vous pouvez utiliser les fonctionnalités des solutions Azure Automation suivantes pour gérer les machines virtuelles Windows et Linux déployées avec Azure Stack :

- **[Update Management](https://docs.microsoft.com/azure/automation/automation-update-management)** . La solution Update Management vous permet d’évaluer rapidement l’état des mises à jour disponibles sur tous les ordinateurs d’agent et de gérer le processus d’installation des mises à jour obligatoires pour ces machines virtuelles Windows et Linux.

- **[Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking)** . Les modifications apportées aux logiciels installés, aux services Windows, aux fichiers et au Registre Windows ainsi qu’aux démons Linux sur les serveurs supervisés sont envoyées au service cloud Azure Monitor pour traitement. La logique est appliquée aux données reçues et le service cloud enregistre les données. En utilisant les informations du tableau de bord de suivi des modifications, vous pouvez facilement voir les modifications apportées à votre infrastructure de serveur.

- **[Inventory](https://docs.microsoft.com/azure/automation/automation-vm-inventory)** . Le suivi Inventory pour une machine virtuelle dans Azure Stack fournit une interface utilisateur basée sur le navigateur pour définir et configurer la collecte d’inventaire.

- **[Azure Monitor pour machines virtuelles](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)** . Azure Monitor pour machines virtuelles supervise les machines virtuelles et groupes de machines virtuelles identiques Azure et Azure Stack à grande échelle. Il analyse les performances et l’intégrité des machines virtuelles Windows et Linux, et supervise leurs processus et dépendances vis-à-vis d’autres ressources et des processus externes. 

> [!IMPORTANT]
> Ces solutions sont les mêmes que celles utilisées pour gérer des machines virtuelles Azure. Les machines virtuelles Azure et Azure Stack sont gérées de la même façon, à partir de la même interface et avec les mêmes outils. Les machines virtuelles Azure Stack sont également soumises aux mêmes tarifs que les machines virtuelles Azure quand vous utilisez les solutions Update Management, Change Tracking, Inventory et Azure Monitor pour machines Virtuelles avec Azure Stack.

## <a name="prerequisites"></a>Prérequis
Plusieurs prérequis doivent être satisfaits avant de pouvoir utiliser ces fonctionnalités pour mettre à jour et gérer des machines virtuelles Azure Stack. Ces prérequis incluent les étapes qui doivent être suivies dans le portail Azure, ainsi que le portail d’administration Azure Stack.

### <a name="in-the-azure-portal"></a>Dans le portail Azure
Pour utiliser les fonctionnalités Azure Monitor pour machines virtuelles, Inventory, Change Tracking et Update Management d’Azure Automation sur les machines virtuelles Azure Stack, vous devez d’abord activer ces solutions dans Azure.

> [!TIP]
> Si ces fonctionnalités sont déjà activées pour les machines virtuelles Azure, vous pouvez utiliser vos informations d’identification d’espace de travail Log Analytics préexistantes. Si vous avez déjà une clé primaire et un ID d’espace de travail Log Analytics que vous souhaitez utiliser, passez directement à [la section suivante](./vm-update-management.md#in-the-azure-stack-administration-portal). Sinon, poursuivez dans cette section pour créer un espace de travail Log Analytics et un compte Automation.

La première étape de l’activation de ces solutions consiste à [créer un espace de travail Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace) dans votre abonnement Azure. Un espace de travail Log Analytics est un environnement de journalisation Azure Monitor unique qui a un référentiel de données, des sources de données et des solutions qui lui sont propres. Une fois que vous avez créé un espace de travail, notez la clé et l’ID. Pour afficher ces informations, accédez au panneau de l’espace de travail, cliquez sur **Paramètres avancés** et passez en revue les valeurs pour **ID de l’espace de travail** et **Clé primaire**. 

Vous devez ensuite [créer un compte Automation](https://docs.microsoft.com/azure/automation/automation-create-standalone-account). Un compte Automation est un conteneur pour vos ressources Azure Automation. Il fournit un moyen de séparer vos environnements ou de mieux organiser vos flux de travail et ressources Automation. Une fois que le compte Automation est créé, vous devez activer les fonctionnalités Inventory, Change Tracking et Update Management. Pour ce faire, effectuez les étapes suivantes pour activer chaque fonctionnalité :

1. Dans le portail Azure, accédez au compte Automation que vous souhaitez utiliser.

2. Sélectionnez la solution à activer (**Inventory**, **Change Tracking**, ou **Update Management**).

3. Utilisez la liste déroulante **Sélectionner un espace de travail...** pour sélectionner l’espace de travail Log Analytics à utiliser.

4. Vérifiez que toutes les informations restantes sont correctes, puis cliquez sur **Activer** pour activer la solution.

5. Répétez les étapes 2 à 4 pour activer l’ensemble des trois solutions. 

   [![](media/vm-update-management/1-sm.PNG "Activer les fonctionnalités de compte Automation")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="enable-azure-monitor-for-vms"></a>Activer Azure Monitor pour machines virtuelles

Azure Monitor pour machines virtuelles surveille les machines virtuelles et groupes de machines virtuelles identiques à grande échelle. Il analyse les performances et l’intégrité des machines virtuelles Windows et Linux, et supervise leurs processus et dépendances vis-à-vis d’autres ressources et des processus externes.

La solution Azure Monitor pour machines virtuelles prend en charge le monitoring des performances et des dépendances d’applications des machines virtuelles hébergées localement ou auprès d’un autre fournisseur de cloud. Trois fonctionnalités clés offrent des insights approfondis :

1. Composants logiques de machines virtuelles Azure sur Windows et Linux : sont mesurés par rapport aux critères d’intégrité préconfigurés et vous avertissent lorsque la condition évaluée est remplie. 

2. Graphiques prédéfinis de tendances des performances : affichent les mesures de performances principales du système d’exploitation de la machine virtuelle invitée.

3. Carte des dépendances : affiche les composants interconnectés avec la machine virtuelle de différents groupes de ressources et abonnements.

Après avoir créé l’espace de travail Log Analytics, vous devez activer les compteurs de performances dans l’espace de travail pour la collection sur les machines virtuelles Linux et Windows, et installer et activer les solutions ServiceMap et InfrastructureInsights dans votre espace de travail. Ce processus est décrit dans le guide [Déployer Azure Monitor pour machines virtuelles](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-onboard#how-to-enable-azure-monitor-for-vms-preview).

### <a name="in-the-azure-stack-administration-portal"></a>Dans le portail d’administration Azure Stack
Après avoir activé les solutions Azure Automation dans le portail Azure, vous devez vous connecter au portail d’administration Azure Stack en tant qu’administrateur cloud, puis télécharger les éléments d’extension **Azure Monitor, Update and Configuration Management** et **Azure Monitor, Update and Configuration Management for Linux** de la Place de marché Azure Stack. 

   ![Élément d’extension Azure Monitor, Update and Configuration Management de la Place de marché](media/vm-update-management/2.PNG) 

Pour activer la solution Map d’Azure Monitor pour machines virtuelles et obtenir des insights sur les dépendances avec le réseau, vous devez également télécharger l’extension **Azure Monitor Dependency Agent** :

   ![Azure Monitor Dependency Agent](media/vm-update-management/2-dependency.PNG) 

## <a name="enable-update-management-for-azure-stack-virtual-machines"></a>Activer Update Management pour les machines virtuelles Azure Stack
Effectuez les étapes suivantes pour activer la gestion des mises à jour pour les machines virtuelles Azure Stack.

1. Connectez-vous au portail utilisateur Azure Stack.

2. Dans le portail utilisateur Azure Stack, accédez au panneau Extensions des machines virtuelles pour lesquelles vous voulez activer ces solutions, cliquez sur **+ Ajouter**, sélectionnez l’extension **Azure Update and Configuration Management**, puis cliquez sur **Créer** :

   [![](media/vm-update-management/3-sm.PNG "Panneau Extension de machine virtuelle")](media/vm-update-management/3-lg.PNG#lightbox)

3. Indiquez les clé primaire et ID d’espace de travail précédemment créés pour lier l’agent à l’espace de travail Log Analytics et cliquez sur **OK** pour déployer l’extension.

   [![](media/vm-update-management/4-sm.PNG "Indication de la clé et de l’ID d’espace de travail")](media/vm-update-management/4-lg.PNG#lightbox) 

4. Comme décrit dans la [documentation sur la gestion des mises à jour Automation](https://docs.microsoft.com/azure/automation/automation-update-management), vous devez activer la solution Update Management pour chaque machine virtuelle que vous souhaitez gérer. Pour activer la solution pour toutes les machines virtuelles associées à l’espace de travail, sélectionnez **Gestion des mises à jour**, cliquez sur **Gérer des machines**, puis sélectionnez l’option **Activer sur tous les ordinateurs disponibles et à venir**.

   [![](media/vm-update-management/5-sm.PNG "Indication de la clé et de l’ID d’espace de travail")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > Répétez cette étape pour activer chaque solution pour les machines virtuelles Azure Stack qui sont associées à l’espace de travail. 
  
Une fois que l’extension Azure Update and Configuration Management est activée, une analyse est effectuée deux fois par jour pour chaque machine virtuelle gérée. L’API est appelée toutes les 15 minutes pour rechercher l’heure de la dernière mise à jour afin de déterminer si l’état a changé. Si l’état a changé, une analyse de conformité est lancée.

Une fois que les machines virtuelles sont analysées, elles s’affichent dans le compte Azure Automation de la solution Update Management : 

   [![](media/vm-update-management/6-sm.PNG "Indication de la clé et de l’ID d’espace de travail")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> L’affichage sur le tableau de bord des données mises à jour provenant des ordinateurs gérés peut prendre entre 30 minutes et 6 heures.

Les machines virtuelles Azure Stack peuvent maintenant être incluses dans des déploiements de mise à jour planifiés avec des machines virtuelles Azure.

## <a name="enable-azure-monitor-for-vms-running-on-azure-stack"></a>Activer Azure Monitor pour machines virtuelles sur Azure Stack
Une fois que les extensions **Azure Monitor, Update and Configuration Management** et **Azure Monitor Dependency Agent** ont été installées sur la machine virtuelle, celle-ci peut commencer à envoyer des rapports de données à la solution [Azure Monitor pour machines virtuelles](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview). 

> [!TIP]
> L’extension **Azure Monitor Dependency Agent** ne requiert aucun paramètre particulier. L’agent Map Dependency Agent Azure Monitor pour machines virtuelles ne transmet pas de données par lui-même et ne requiert pas de modifications des pare-feu ni des ports. Log Analytics Agent transmet toujours les données Map au service Azure Monitor, directement ou par le biais de la [passerelle OMS](https://docs.microsoft.com/azure/azure-monitor/platform/gateway), si vos stratégies de sécurité n’autorisent pas les ordinateurs du réseau à se connecter à Internet.

Azure Monitor pour les machines virtuelles comprend un ensemble de graphiques de performances qui ciblent divers indicateurs de performance clés (KPI) pour vous aider à déterminer l’intégrité du fonctionnement d’une machine virtuelle. Les graphiques illustrent l’utilisation des ressources sur une période de temps pour que vous puissiez identifier les goulots d’étranglement ou les anomalies. Vous pouvez également basculer sur une perspective répertoriant toutes les machines virtuelles pour afficher l’utilisation des ressources en fonction de la métrique sélectionnée. Il y a beaucoup d’autres éléments à prendre en compte dans la gestion des performances, mais la solution Azure Monitor pour machines virtuelles supervise des indicateurs de performances clés du système d’exploitation liés à l’utilisation du processeur, de la mémoire, de la carte réseau et du disque. La fonctionnalité Performances vient compléter celle de supervision de l’intégrité et permet d’exposer les problèmes indiquant une panne éventuelle d’un composant système, de prendre en charge les réglages et l’optimisation pour améliorer l’efficacité ou de prendre en charge la planification de la capacité.

   ![Onglet Performances Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-performance/vminsights-performance-aggview-01.png)

Lorsque vous visualisez les composants d’application détectés sur des machines virtuelles Windows et Linux s’exécutant dans Azure Stack, vous pouvez observer votre environnement de deux manières avec Azure Monitor pour machines virtuelles : directement depuis une machine virtuelle ou à l’échelle des groupes de machines virtuelles à partir d’Azure Monitor.
L’article [Utiliser la fonctionnalité Map d’Azure Monitor pour machines virtuelles (préversion) pour comprendre les composants d’application](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-maps) vous aide à comprendre ce que les deux perspectives apportent et comment utiliser la fonctionnalité Map.

   ![Onglet Performances Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-maps/map-multivm-azure-monitor-01.png)


## <a name="enable-update-management-using-a-resource-manager-template"></a>Activer Update Management à l’aide d’un modèle Resource Manager
Si vous avez un grand nombre de machines virtuelles Azure Stack, vous pouvez utiliser [ce modèle Azure Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) pour déployer plus facilement la solution sur des machines virtuelles. Le modèle déploie l’extension Microsoft Monitoring Agent sur une machine virtuelle Azure Stack existante et l’ajoute à un espace de travail Azure Log Analytics existant.
 
## <a name="next-steps"></a>Étapes suivantes
[Optimiser les performances des machines virtuelles SQL Server](azure-stack-sql-server-vm-considerations.md)