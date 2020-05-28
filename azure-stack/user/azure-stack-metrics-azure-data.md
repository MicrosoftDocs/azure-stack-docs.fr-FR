---
title: Utiliser Azure Monitor sur Azure Stack Hub
description: Découvrez comment utiliser Azure Monitor sur Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.lastreviewed: 12/01/2019
ms.openlocfilehash: ea2ccd33bc64c01b4ab5ba56024a54aa82453e63
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84112031"
---
# <a name="use-azure-monitor-on-azure-stack-hub"></a>Utiliser Azure Monitor sur Azure Stack Hub

Cet article fournit une vue d’ensemble du service Azure Monitor dans Azure Stack Hub. Il vous permet de découvrir le fonctionnement d’Azure Monitor ainsi que des informations supplémentaires concernant l’utilisation d’Azure Monitor sur Azure Stack Hub.

Pour une vue d’ensemble d’Azure Monitor, voir l’article concernant Azure global [Bien démarrer avec Azure Monitor sur Azure Stack Hub](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

![Panneau Azure Stack Hub Monitor](./media/azure-stack-metrics-azure-data/azs-monitor.png)

Azure Monitor est un service de plateforme qui fournit une source unique de supervision des ressources Azure. Azure Monitor permet de visualiser, d’interroger, de router et d’archiver les métriques et journaux provenant de ressources Azure, ainsi que d’entreprendre des actions sur ceux-ci. Vous pouvez utiliser ces données à l’aide du portail administrateur Azure Stack Hub, des applets de commande PowerShell Monitor, de l’interface de ligne de commande multiplateforme ou des API REST Azure Monitor. Pour en savoir plus sur la connectivité spécifique prise en charge par Azure Stack Hub, voir [Comment consommer des données de surveillance à partir d’Azure Stack Hub](azure-stack-metrics-monitor.md).

> [!Note]
> Les métriques et les journaux de diagnostic ne sont pas disponibles pour le Kit de développement Azure Stack.

## <a name="prerequisites-for-azure-monitor-on-azure-stack-hub"></a>Conditions préalables pour Azure Monitor sur Azure Stack Hub

Enregistrer le fournisseur de ressources **Microsoft.insights** dans les paramètres relatifs à l’offre d’abonnement du fournisseurs de ressources. Pour vérifier que le fournisseur de ressources est disponible dans l’offre qui est associée à votre abonnement, procédez comme suit :

1. Connectez-vous au portail utilisateur Azure Stack Hub.
2. Sélectionnez **Abonnements**.
3. Sélectionnez l’abonnement que vous souhaitez inscrire.
4. Sélectionnez **Fournisseurs de ressources** sous **Paramètres**. 
5. Recherchez **Microsoft.Insights** dans la liste et vérifiez que l’état est **Inscrit**.

## <a name="overview-of-azure-monitor-on-azure-stack-hub"></a>Vue d’ensemble d’Azure Monitor sur Azure Stack Hub

Tout comme Azure Monitor sur Azure, Azure Monitor sur Azure Stack Hub fournit des métriques et journaux d’activité de base à propos de l’infrastructure pour la plupart des services.

## <a name="azure-monitor-sources-compute-subset"></a>Sources Azure Monitor : sous-ensemble de calcul

![Sources Azure Monitor sur Azure Stack Hub : sous-ensemble de calcul](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.svg)

Le fournisseur de ressources **Microsoft.Compute** dans Azure Stack Hub inclut les éléments suivants :
 - Machines virtuelles 
 - Groupes identiques de machines virtuelles

### <a name="application---diagnostics-logs-app-logs-and-metrics"></a>Application – Journaux de diagnostic, journaux d’application et métriques

Les applications peuvent s’exécuter dans le système d’exploitation d’une machine virtuelle fonctionnant avec le fournisseur de ressources **Microsoft.Compute**. Ces applications et machines virtuelles émettent leur propre ensemble de journaux et de métriques. Azure Monitor s’appuie sur l’extension de diagnostics Azure (Windows ou Linux) pour collecter la plupart des métriques et journaux de niveau application.

Parmi ces types de mesures, on compte :
 - Compteurs de performance
 - Journaux d’application
 - Journaux des événements Windows
 - Source d’événement .NET
 - Journaux d’activité IIS
 - Suivi d’événements pour Windows basé sur les manifestes
 - Vidages sur incident
 - Journaux d’activité d’erreurs client

> [!Note]  
> L’extension Linux Diagnostics sur Azure Stack Hub n’est pas prise en charge.

### <a name="host-and-guest-vm-metrics"></a>Métriques d’hôte et machine virtuelle invitée

Les ressources de calcul répertoriées précédemment disposent d’une machine virtuelle hôte et d’un système d’exploitation invité dédiés. La machine virtuelle hôte et le SE invité sont l’équivalent de la machine virtuelle racine et de la machine virtuelle invitée dans l’hyperviseur Hyper-V. Vous pouvez collecter des métriques pour la machine virtuelle hôte et le système SE invité. Vous pouvez également collecter des journaux de diagnostic pour le système d’exploitation invité. La liste des métriques que vous pouvez collecter pour les machines virtuelles hôtes et invitées sur Azure Stack Hub est disponible dans [Métriques prises en charge avec Azure Monitor sur Azure Stack Hub](azure-stack-metrics-supported.md). 

### <a name="activity-log"></a>Journal d’activité

Vous pouvez effectuer des recherches dans les journaux d’activité afin d’obtenir des informations sur vos ressources de calcul telles qu’elles sont analysées par l’infrastructure Azure Stack Hub. Ce journal contient des informations telles que les dates et heures de création ou de destruction de ressources. Les journaux d’activité sur Azure Stack Hub sont cohérents avec ceux d’Azure. Pour en savoir plus, consultez l’article [Présentation du journal d’activité Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). 


## <a name="azure-monitor-sources-everything-else"></a>Sources Azure Monitor : tout le reste

![Sources Azure Monitor sur Azure Stack Hub : tout le reste](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.svg)

### <a name="resources---metrics-and-diagnostics-logs"></a>Ressources – Métriques et journaux de diagnostics

Les métriques et les journaux de diagnostics que vous pouvez collecter varient en fonction du type de ressource. Une liste des métriques que vous pouvez collecter pour chaque ressource sur Azure Stack Hub est disponible dans les métriques prises en charge. Pour plus d’informations, voir [Métriques prises en charge avec Azure Monitor sur Azure Stack Hub](azure-stack-metrics-supported.md).

### <a name="activity-log"></a>Journal d’activité

Le journal d’activité est identique à celui des ressources de calcul. 

### <a name="uses-for-monitoring-data"></a>Utilisations de l’analyse de données

**Stockage et archivage**  

Certaines données de surveillance sont déjà disponibles dans Azure Monitor et enregistrées pendant un laps de temps défini. 
 - Les métriques sont stockées pendant 90 jours. 
 - Les entrées de journal d’activité sont conservées pendant 90 jours. 
 - Les journaux de diagnostic ne sont pas stockés.
 - Archivez les données dans un compte de stockage pour les conserver à long terme.

**Requête**  

Vous pouvez utiliser l’API REST Azure Monitor, les commandes de l’Interface de ligne de commande interplateforme (CLI), les cmdlets PowerShell ou le kit de développement logiciel .NET pour accéder aux données présentes dans le système ou dans le stockage Azure. 

**Visualisation**

En affichant les données analysées sous forme de graphiques et de diagrammes, vous êtes en mesure d’identifier des tendances plus rapidement qu’en lisant simplement les données brutes vous-même. 

Il existe quelques méthodes de visualisation, qui sont les suivantes :
 - Utilisation du portail utilisateur et administrateur Azure Stack Hub.
 - Routage des données vers Microsoft Power BI.
 - Routage de données vers un outil de visualisation tiers par streaming en direct ou par lecture de l’outil à partir d’une archive située dans le stockage Azure.

## <a name="methods-of-accessing-azure-monitor-on-azure-stack-hub"></a>Méthodes d’accès à Azure Monitor sur Azure Stack Hub

Vous pouvez utiliser le suivi, le routage et la récupération des données à l’aide de l’une des méthodes suivantes. Toutes les méthodes ne sont pas disponibles pour toutes les actions ou tous les types de données. 

 - [Portail utilisateur Azure Stack Hub ](azure-stack-use-portal.md)
 - [PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [Interface de ligne de commande interplateforme (CLI)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [REST API](https://docs.microsoft.com/rest/api/monitor)
 - [Kit de développement logiciel (SDK) .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

> [!Important]  
> Si vous rencontrez une erreur de **ressource introuvable** lors de l'affichage du graphique de performances d'une machine virtuelle, vérifiez que vous avez enregistré Microsoft.insights sur l'abonnement associé à la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la consommation de données de surveillance sur Azure Stack Hub, voir [Consommer des données de surveillance à partir d’Azure Stack Hub](azure-stack-metrics-monitor.md).
