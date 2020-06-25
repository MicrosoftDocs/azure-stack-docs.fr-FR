---
title: Superviser Azure Stack HCI avec Azure Monitor
description: Supervisez des serveurs et configurez des alertes avec Azure Monitor à partir de Windows Admin Center.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 04/03/2020
ms.openlocfilehash: 43bcc5be8fd96e33d16cfdebd87e0d965c8eff41
ms.sourcegitcommit: 76af742a42e807c400474a337e29d088ede8a60d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85196968"
---
# <a name="monitor-azure-stack-hci-with-azure-monitor"></a>Superviser Azure Stack HCI avec Azure Monitor

> S’applique à : Windows Server 2019

[Azure Monitor](/azure/azure-monitor/overview) collecte les données de télémétrie provenant de diverses ressources, notamment des serveurs Windows et des machines virtuelles, il les analyse et il agit dessus, à la fois localement et dans le cloud. Même si Azure Monitor extrait des données à partir de machines virtuelles Azure et d’autres ressources Azure, cet article porte essentiellement sur la façon dont Azure Monitor fonctionne avec les serveurs locaux et les machines virtuelles s’exécutant sur Azure Stack HCI, en particulier avec Windows Admin Center.

## <a name="how-does-azure-monitor-work"></a>Comment Azure Monitor fonctionne-t-il ?
:::image type="content" source="media/monitor/azure-monitor-diagram.png" alt-text="diagramme illustrant le fonctionnement d’Azure Monitor" border="false":::
Les données générées à partir de serveurs Windows locaux sont collectées dans un espace de travail Log Analytics dans Azure Monitor. Dans un espace de travail, vous pouvez activer diverses solutions de supervision (ensembles de logique qui fournissent des insights pour un scénario particulier). Par exemple, Azure Update Management, Azure Security Center et Azure Monitor pour machines virtuelles sont tous des solutions de supervision qui peuvent être activées dans un espace de travail.

Quand vous activez une solution de supervision dans un espace de travail Log Analytics, tous les serveurs qui envoient des rapports à cet espace de travail commencent à collecter des données pertinentes pour cette solution, afin qu’elle puisse générer des insights pour tous les serveurs de l’espace de travail.

Pour collecter des données de télémétrie sur un serveur local et les transmettre à l’espace de travail Log Analytics, Azure Monitor exige l’installation de Microsoft Monitoring Agent (MMA). Certaines solutions de supervision nécessitent également un agent secondaire. Par exemple, Azure Monitor pour machines virtuelles dépend également d’un agent ServiceMap pour les fonctionnalités supplémentaires fournies par cette solution.

Certaines solutions, comme Azure Update Management, dépendent également d’Azure Automation, qui vous permet de gérer de manière centralisée les ressources dans les environnements Azure et non Azure. Par exemple, Azure Update Management utilise Azure Automation pour planifier et orchestrer l’installation des mises à jour sur les machines de votre environnement, de manière centralisée, à partir du portail Azure.

## <a name="what-data-does-azure-monitor-collect"></a>Quelles sont les données collectées par Azure Monitor ?

Toutes les données collectées par Azure Monitor font partie d’un des deux types fondamentaux : métriques et journaux.

1. Les [métriques](/azure/azure-monitor/platform/data-platform#metrics) sont des valeurs numériques décrivant un aspect d’un système à un moment précis dans le temps. Elles sont légères et capables de prendre en charge des scénarios en quasi-temps réel. Les données collectées par Azure Monitor s’affichent directement dans la page **Vue d’ensemble** du portail Azure.

    :::image type="content" source="media/monitor/metrics.png" alt-text="image de l’ingestion de métriques dans Metrics Explorer" border="false":::

2. Les [journaux d’activité](/azure/azure-monitor/platform/data-platform#logs) contiennent différents types de données organisées en enregistrements, avec différents jeux de propriétés pour chaque type. Les données de télémétrie, comme les événements et les traces, sont stockées sous forme de journaux d’activité en plus des données de performances, afin qu’elles puissent être combinées pour analyse. Les données de journal collectées par Azure Monitor peuvent être analysées à l’aide de [requêtes](/azure/azure-monitor/log-query/log-query-overview) qui permettent de récupérer, consolider et analyser rapidement les données collectées. Vous pouvez créer et tester des requêtes à l’aide de [Log Analytics](/azure/azure-monitor/log-query/portals) dans le Portail Azure, avant d’analyser directement les données à l’aide de ces outils ou d’enregistrer les requêtes pour les utiliser pour les [visualisations](/azure/azure-monitor/visualizations) ou les [règles d’alerte](/azure/azure-monitor/platform/alerts-overview).

    :::image type="content" source="media/monitor/logs.png" alt-text="image de l’ingestion des journaux dans Log Analytics" border="false":::

## <a name="how-does-windows-admin-center-enable-you-to-use-azure-monitor"></a>Comment Windows Admin Center vous permet-il d’utiliser Azure Monitor ?

Dans Windows Admin Center, vous pouvez activer trois solutions de supervision :

- [Azure Monitor pour clusters](#onboard-your-cluster-using-windows-admin-center)
- [Azure Update Management](/windows-server/manage/windows-admin-center/azure/azure-update-management) (dans l’outil **Mises à jour**)
- Azure Monitor pour machines virtuelles (dans Paramètres du serveur), également appelée « Insights sur les machines virtuelles »

Vous pouvez commencer à utiliser Azure Monitor à partir de n’importe lequel de ces outils. Si vous n’avez jamais utilisé Azure Monitor auparavant, Windows Admin Center provisionne automatiquement un espace de travail Log Analytics (et un compte Azure Automation, si nécessaire), puis installe et configure MMA sur le serveur cible. Il installe ensuite la solution correspondante dans l’espace de travail.

Par exemple, si vous accédez d’abord à l’outil **Mises à jour** pour configurer Azure Update Management, Windows Admin Center effectue les opérations suivantes :

1. Installer l’agent MMA sur la machine.
2. Créer l’espace de travail Log Analytics et le compte Azure Automation (car un compte Azure Automation est nécessaire dans ce cas-là).
3. Installer la solution Update Management dans l’espace de travail nouvellement créé.

Si vous voulez ajouter une autre solution de supervision à partir de Windows Admin Center Windows sur le même serveur, Windows Admin Center installe simplement cette solution dans l’espace de travail existant auquel ce serveur est connecté. Windows Admin Center installe également tous les autres agents nécessaires.

Si vous vous connectez à un autre serveur, mais que vous avez déjà configuré un espace de travail Log Analytics (par le biais de Windows Admin Center ou manuellement dans le portail Azure), vous pouvez également installer MMA sur le serveur et le connecter à un espace de travail existant. Quand vous connectez un serveur à un espace de travail, il démarre automatiquement la collecte de données et l’envoi de rapports aux solutions installées dans cet espace de travail.

## <a name="azure-monitor-for-virtual-machines-aka-virtual-machine-insights"></a>Azure Monitor pour machines virtuelles (solution également appelée « Insights sur les machines virtuelles »)

Quand vous configurez Azure Monitor pour machines virtuelles dans **Paramètres du serveur**, Windows Admin Center active la solution Azure Monitor pour machines virtuelles, également appelée « Insights sur les machines virtuelles ». Cette solution vous permet de superviser l’intégrité et les événements du serveur, de créer des alertes par e-mail, d’obtenir une vue centralisée des performances du serveur dans l’ensemble de votre environnement et de visualiser des applications, des systèmes et des services connectés à un serveur donné.

> [!NOTE]
> Malgré son nom, la solution Insights sur les machines virtuelles fonctionne pour les serveurs physiques ainsi que pour les machines virtuelles.

Avec une allocation, par Azure Monitor, de 5 Go gratuits de données/mois/client, vous pouvez facilement essayer cette solution pour un ou deux serveurs sans vous soucier de la facturation. Poursuivez la lecture de ce document pour voir d’autres avantages de l’intégration de serveurs à Azure Monitor, comme l’obtention d’une vue centralisée des performances des systèmes sur les serveurs de votre environnement.

## <a name="onboard-your-cluster-using-windows-admin-center"></a>Intégrer votre cluster à l’aide de Windows Admin Center

La façon la plus simple d’intégrer votre cluster à Azure Monitor consiste à utiliser le workflow automatisé de Windows Admin Center qui configure le service de contrôle d’intégrité et Log Analytics, puis installe l’agent MMA.

:::image type="content" source="media/monitor/onboarding.gif" alt-text="image de l’intégration d’un cluster à Azure Monitor":::

Dans la page Vue d’ensemble d’une connexion au serveur, cliquez sur le nouveau bouton **Gérer les alertes** ou accédez à **Paramètres du serveur > Supervision et alertes**. Dans cette page, intégrez votre serveur à Azure Monitor en cliquant sur **Configurer**, puis en renseignant le volet de configuration. Admin Center se charge de provisionner l’espace de travail Azure Log Analytics, d’installer l’agent nécessaire et de configurer la solution Insights sur les machines virtuelles. Une fois que vous avez terminé, votre serveur envoie les données du compteur de performances à Azure Monitor, ce qui vous permet de voir et de créer des alertes par e-mail basées sur ce serveur, à partir du portail Azure.

## <a name="onboard-your-cluster-manually-using-powershell"></a>Intégrer votre cluster manuellement à l’aide de PowerShell

Si vous préférez intégrer votre cluster manuellement, effectuez les étapes ci-dessous.

### <a name="configure-health-service"></a>Configurer le service de contrôle d’intégrité

La première chose à faire est de configurer votre cluster. Comme vous le savez peut-être, le [service de contrôle d’intégrité](/windows-server/failover-clustering/health-service-overview) améliore la supervision quotidienne et l’expérience opérationnelle pour les clusters exécutant des espaces de stockage direct.

Comme nous l’avons vu ci-dessus, Azure Monitor collecte les journaux à partir de chaque nœud sur lequel il s’exécute dans votre cluster. Par conséquent, nous devons configurer le service de contrôle d’intégrité pour écrire dans un canal d’événements, qui se présente comme suit :

```
Event Channel: Microsoft-Windows-Health/Operational
Event ID: 8465
```

Pour configurer le service de contrôle d’intégrité, vous exécutez :

```PowerShell
get-storagesubsystem clus* | Set-StorageHealthSetting -Name "Platform.ETW.MasTypes" -Value "Microsoft.Health.EntityType.Subsystem,Microsoft.Health.EntityType.Server,Microsoft.Health.EntityType.PhysicalDisk,Microsoft.Health.EntityType.StoragePool,Microsoft.Health.EntityType.Volume,Microsoft.Health.EntityType.Cluster"
```

L’exécution de l’applet de commande ci-dessus indique au paramètre d’intégrité de commencer à écrire des événements dans le canal d’événements *Microsoft-Windows-Health/Operational*.

### <a name="configure-log-analytics"></a>Configurer Log Analytics

Maintenant que vous avez configuré la journalisation appropriée sur votre cluster, l’étape suivante consiste à configurer correctement Log Analytics.

Pour donner une vue d’ensemble, [Azure Log Analytics](/azure/azure-monitor/platform/agent-windows) peut collecter des données directement à partir de machines Windows physiques ou virtuelles de votre centre de données ou d’un autre environnement cloud, puis de les enregistrer dans un dépôt unique pour ensuite procéder à une analyse et à une mise en corrélation détaillées.

Pour comprendre la configuration prise en charge, consultez les pages [Prise en charge des systèmes d’exploitation Windows](/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) et [Configuration du pare-feu réseau](/azure/azure-monitor/platform/log-analytics-agent#network-firewall-requirements).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

#### <a name="login-in-to-azure-portal"></a>Se connecter au portail Azure

Connectez-vous au portail Azure à l’adresse [https://portal.azure.com](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

#### <a name="create-a-workspace"></a>Créer un espace de travail

Pour plus d’informations sur les étapes indiquées ci-dessous, consultez la [documentation Azure Monitor](/azure/azure-monitor/learn/quick-collect-windows-computer).

1. Dans le portail Azure, cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.

    :::image type="content" source="media/monitor/azure-portal-01.png" alt-text="Azure portal":::

2. Cliquez sur **Créer**, puis sélectionnez des options pour les éléments suivants :

   * Attribuez un nom au nouvel **Espace de travail Log Analytics**, tel que *DefaultLAWorkspace*.
   * Dans la liste déroulante **Abonnement**, sélectionnez un abonnement à lier si la valeur par défaut sélectionnée n’est pas appropriée.
   * Pour **Groupe de ressources**, sélectionnez un groupe de ressources existant qui contient une ou plusieurs machines virtuelles Azure.

    :::image type="content" source="media/monitor/create-loganalytics-workspace-02.png" alt-text="Créer le panneau de ressources Log Analytics":::

3. Après avoir entré les informations requises dans le volet **Espace de travail Log Analytics**, cliquez sur **OK**.

Pendant que les informations sont vérifiées et l’espace de travail créé, vous pouvez suivre la progression sous **Notifications** dans le menu.

#### <a name="obtain-workspace-id-and-key"></a>Obtenir l’ID et la clé d’espace de travail
Avant d’installer l’agent MMA pour Windows, vous devez disposer de l’ID et de la clé de votre espace de travail Log Analytics.  L’Assistant Installation a besoin de ces informations pour configurer correctement l’agent et s’assurer qu’il peut communiquer avec Log Analytics.

1. Dans le portail Azure, cliquez sur **Tous les services** en haut à gauche. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.
2. Dans votre liste d’espaces de travail Log Analytics, sélectionnez *DefaultLAWorkspace* créé précédemment.
3. Sélectionnez **Paramètres avancés**.
    :::image type="content" source="media/monitor/log-analytics-advanced-settings-01.png" alt-text="Paramètres avancés de Log Analytics":::
4. Sélectionnez **Sources connectées**, puis **Serveurs Windows**.
5. Des valeurs figurent à droite d’**ID de l’espace de travail** et de **Clé primaire**. Enregistrez-les tous les deux temporairement : copiez-collez ces deux valeurs dans votre éditeur pour l’instant.

### <a name="installing-the-mma-on-windows"></a>Installation de l’agent MMA sur Windows
Les étapes suivantes installent et configurent Microsoft Monitoring Agent.

> [!IMPORTANT]
> Veillez à installer cet agent sur chaque serveur de votre cluster et indiquez que vous voulez que l’agent s’exécute au démarrage de Windows.

1. Dans la page **Serveurs Windows**, sélectionnez l’option **Télécharger l’agent Windows** correspondant à la version appropriée selon l’architecture du processeur du système d’exploitation Windows.
2. Exécutez le programme d’installation pour installer l’agent sur votre ordinateur.
3. Sur la page d’**accueil**, cliquez sur **Suivant**.
4. Dans la page **Termes du contrat de licence**, lisez les conditions de licence, puis cliquez sur **J’accepte**.
5. Dans la page **Dossier de destination**, modifiez ou conservez le dossier d’installation par défaut, puis cliquez sur **Suivant**.
6. Sur la page **Options d’installation de l’agent**, choisissez de connecter l’agent à Azure Log Analytics, puis cliquez sur **Suivant**.
7. Dans la page **Azure Log Analytics**, collez les valeurs **ID de l’espace de travail** et **Clé de l’espace de travail (clé primaire)** que vous avez copiées précédemment. Si l’ordinateur a besoin de communiquer via un serveur proxy avec le service Log Analytics, cliquez sur **Avancé**, puis indiquez l’URL et le numéro de port du serveur proxy. Si votre serveur proxy requiert une authentification, tapez le nom d’utilisateur et un mot de passe pour vous authentifier auprès du serveur proxy, puis cliquez sur **Suivant**.
8. Cliquez sur **Suivant** après avoir fourni les paramètres de configuration nécessaires.
    :::image type="content" source="media/monitor/log-analytics-mma-setup-laworkspace.png" alt-text="Coller l’ID et la clé primaire de l’espace de travail":::
9. Dans la page **Prêt pour l’installation**, passez en revue vos choix, puis cliquez sur **Installer**.
10. Dans la page **Configuration effectuée**, cliquez sur **Terminer**.

Lorsque vous avez terminé, **Microsoft Monitoring Agent** apparaît dans le **Panneau de configuration**. Vous pouvez examiner votre configuration et vérifier que l’agent est connecté à Log Analytics. Une fois connecté, sous l'onglet **Azure Log Analytics**, l’agent affiche un message indiquant : **Microsoft Monitoring Agent s'est correctement connecté au service Microsoft Log Analytics.**

:::image type="content" source="media/monitor/log-analytics-mma-laworkspace-status.png" alt-text="État de la connexion MMA à Log Analytics":::

Pour comprendre la configuration prise en charge, consultez les pages [Prise en charge des systèmes d’exploitation Windows](/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) et [Configuration du pare-feu réseau](/azure/azure-monitor/platform/log-analytics-agent#network-firewall-requirements).

## <a name="setting-up-alerts-using-windows-admin-center"></a>Configuration d’alertes à l’aide de Windows Admin Center

Une fois que vous avez attaché votre serveur à Azure Monitor, vous pouvez utiliser les liens hypertexte intelligents figurant dans la page **Paramètres > Supervision et alertes** pour accéder au portail Azure. Dans Windows Admin Center, vous pouvez facilement configurer des alertes par défaut qui s’appliquent à tous les serveurs de votre espace de travail Log Analytics. Windows Admin Center permet de collecter automatiquement des compteurs de performances. Vous pouvez donc [créer une alerte](/azure/azure-monitor/platform/alerts-log) en personnalisant l’une des nombreuses requêtes prédéfinies, ou écrire votre propre requête.

:::image type="content" source="media/monitor/setup1.gif" alt-text="Capture d’écran Configurer des alertes":::

Il s’agit des alertes et de leurs conditions par défaut que vous pouvez choisir :

| Nom de l’alerte                | Condition par défaut                                  |
|---------------------------|----------------------------------------------------|
| Utilisation du processeur           | Plus de 85 % pendant 10 minutes                            |
| Utilisation de la capacité de disque | Plus de 85 % pendant 10 minutes                            |
| Utilisation de la mémoire        | Mémoire disponible inférieure à 100 Mo pendant 10 minutes   |
| Heartbeat                 | Moins de 2 battements pendant 5 minutes                   |
| Erreur système critique     | Toute alerte critique dans le journal des événements du système de cluster |
| Alerte du service de contrôle d’intégrité      | Toute erreur du service de contrôle d’intégrité sur le cluster            |

Une fois que vous avez configuré les alertes dans Windows Admin Center, vous pouvez voir les alertes dans votre espace de travail Log Analytics dans Azure.

:::image type="content" source="media/monitor/setup2.gif" alt-text="Capture d’écran Afficher les alertes":::

### <a name="collecting-event-and-performance-data"></a>Collecte de données d’événements et de performances

Log Analytics est capable de collecter les événements des journaux des événements Windows ainsi que des compteurs de performances que vous spécifiez dans l’optique de procéder à une analyse à long terme et de générer des rapports afin de réagir dès qu’une condition particulière est détectée. Pour configurer la collecte d’événements à partir du journal des événements Windows, ainsi que plusieurs compteurs de performances courants avec lesquels commencer, procédez comme suit.

1. Dans le portail Azure, cliquez sur **Plus de services** dans l’angle inférieur gauche. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.
2. Sélectionnez **Paramètres avancés**.
    :::image type="content" source="media/monitor/log-analytics-advanced-settings-01.png" alt-text="Paramètres avancés de Log Analytics":::
3. Sélectionnez **Données**, puis **Journaux des événements Windows**.
4. Ici, ajoutez le canal d’événements du service de contrôle d’intégrité en tapant le nom ci-dessous, puis cliquez sur le signe plus ( **+** ).
   ```
   Event Channel: Microsoft-Windows-Health/Operational
   ```
5. Dans le tableau, vérifiez les niveaux de gravité **Erreur** et **Avertissement**.
6. Cliquez sur **Enregistrer** en haut de la page pour enregistrer la configuration.
7. Sélectionnez **Compteurs de performances Windows** pour activer la collecte des compteurs de performances sur un ordinateur Windows.
8. Quand vous procédez à la configuration initiale des compteurs de performances Windows pour un nouvel espace de travail Log Analytics, la possibilité vous est offerte de créer rapidement plusieurs compteurs courants. Ils s’affichent avec une case à cocher en regard.
    :::image type="content" source="media/monitor/windows-perfcounters-default.png" alt-text="Compteurs de performances Windows par défaut sélectionnés":::
    Cliquez sur **Ajouter les compteurs de performances sélectionnés**.  Ils sont ajoutés et prédéfinis avec un intervalle d’échantillonnage de collecte de dix secondes.
9. Cliquez sur **Enregistrer** en haut de la page pour enregistrer la configuration.

## <a name="create-queries-and-alerts-based-on-log-data"></a>Créer des requêtes et des alertes en fonction des données de journal

Si vous avez déjà effectué cette opération, votre cluster doit envoyer vos journaux et compteurs de performances à Log Analytics. La prochaine étape consiste à créer des règles d’alerte qui exécutent automatiquement des recherches dans les journaux à intervalles réguliers. Si les résultats de la recherche dans les journaux correspondent à des critères spécifiques, une alerte est déclenchée pour vous envoyer une notification par e-mail ou SMS. Examinons cela maintenant.

### <a name="create-a-query"></a>Créer une requête

Commencez par ouvrir le portail **Recherche dans les journaux**.

1. Dans le portail Azure, cliquez sur **Tous les services**. Dans la liste des ressources, tapez **Moniteur**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Moniteur**.
2. Dans le menu de navigation **Superviser**, sélectionnez **Log Analytics**, puis sélectionnez un espace de travail.

Le moyen le plus rapide de récupérer des données à utiliser consiste à faire appel à une requête simple qui retourne tous les enregistrements d’une table. Tapez les requêtes suivantes dans la zone de recherche, puis cliquez sur le bouton de recherche.

```
Event
```

Les données sont retournées dans la vue Liste par défaut, et vous pouvez voir le nombre total d’enregistrements retournés.

:::image type="content" source="media/monitor/log-analytics-portal-eventlist-01.png" alt-text="Capture d’écran d’une requête simple":::

Sur le côté gauche de l’écran se trouve le volet de filtre, qui vous permet d’ajouter un filtrage à la requête sans la modifier directement.  Plusieurs propriétés d’enregistrement s’affichent pour ce type d’enregistrement, et vous pouvez sélectionner une ou plusieurs valeurs de propriété pour affiner vos résultats de recherche.

Cochez la case en regard de **Erreur** sous **EVENTLEVELNAME** ou tapez ce qui suit pour limiter les résultats aux événements d’erreur.

```
Event | where (EventLevelName == "Error")
```

:::image type="content" source="media/monitor/log-analytics-portal-eventlist-02.png" alt-text="Capture d’écran d’un filtre":::

Une fois que vous avez effectué les requêtes appropriées pour les événements qui vous intéressent, enregistrez-les pour la prochaine étape.

### <a name="create-alerts"></a>Créez des alertes
À présent, examinons un exemple de création d’une alerte.

1. Dans le portail Azure, cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Log Analytics**.
2. Dans le volet gauche, sélectionnez **Alertes** puis cliquez sur **Nouvelle règle d’alerte** en haut de la page pour créer une nouvelle alerte.
    :::image type="content" source="media/monitor/alert-rule-02.png" alt-text="Capture d’écran de création d’une règle d’alerte":::
3. Pour la première étape, sous la section **Créer une alerte**, vous allez sélectionner votre espace de travail Log Analytics comme ressource, car il s’agit d’un signal d’alerte basé sur des journaux.  Filtrez les résultats en choisissant l’**Abonnement** spécifique dans la liste déroulante si vous en avez plusieurs. Cette liste contient l’espace de travail Log Analytics créé précédemment.  Filtrez le **Type de ressource** en sélectionnant **Log Analytics** dans la liste déroulante.  Pour finir, sélectionnez la **ressource** **DefaultLAWorkspace** et cliquez sur **Terminé**.
    :::image type="content" source="media/monitor/alert-rule-03.png" alt-text="Capture d’écran de l’étape 1 de création d’une règle d’alerte":::
4. Dans la section **Critères d’alerte**, cliquez sur **Ajouter des critères** pour sélectionner votre requête enregistrée, puis spécifiez la logique que suit la règle d’alerte.
5. Configurez l’alerte avec les informations suivantes : a. Dans la liste déroulante **Basé sur**, sélectionnez **Mesure des métriques**.  Une mesure des métriques créera une alerte pour chaque objet de la requête dont la valeur dépasse notre seuil spécifié.
   b. Pour la **Condition**, sélectionnez **Supérieur à**, puis spécifiez un seuil.
   c. Définissez ensuite quand déclencher l’alerte. Par exemple, vous pouvez sélectionner **Violations consécutives**, puis, dans la liste déroulante, sélectionnez **Supérieur à** avec la valeur 3.
   d. Dans la section Évaluées sur la base de, définissez la valeur **Période** sur **30** minutes et la **Fréquence** sur 5. La règle s’exécute toutes les cinq minutes et renvoie les enregistrements qui ont été créés dans les trente minutes précédant l’heure actuelle.  Paramétrer la période de temps sur une durée plus longue compense la latence potentielle des données et garantit que la requête retourne des données pour éviter un faux positif où l’alerte ne se déclenche jamais.
6. Cliquez sur **Terminé** pour terminer la règle d’alerte.
    :::image type="content" source="media/monitor/alert-signal-logic-02.png" alt-text="Capture d’écran de configuration d’un signal d’alerte":::
7. Passons maintenant à la deuxième étape. Donnez un nom à votre alerte dans le champ **Nom de la règle d’alerte** ; par exemple, **Alerte pour tous les événements d’erreur**.  Spécifiez une **Description** détaillant les spécificités de l’alerte, puis sélectionnez **Critique (gravité 0)** pour la valeur de **Gravité** parmi les options fournies.
8. Pour activer immédiatement la règle d’alerte lors de la création, acceptez la valeur par défaut pour l’option **Activer la règle lors de sa création**.
9. Pour la troisième et dernière étape, vous spécifiez un **Groupe d’actions**, ce qui garantit que les mêmes actions soient entreprises chaque fois qu’une alerte est déclenchée et peuvent être utilisées pour chaque règle que vous définissez. Configurez un nouveau groupe d’actions avec les informations suivantes : a. Sélectionnez **Nouveau groupe d’actions** et le volet **Ajouter un groupe action** s’affiche.
   b. Pour le **Nom du groupe d’actions**, spécifiez un nom tel que **Opérations informatiques - Notifier** et un **Nom court** comme **itops-n**.
   c. Vérifiez que les valeurs par défaut de l’**Abonnement** et du **Groupe de ressources** sont correctes. Si ce n’est pas le cas, sélectionnez la valeur correcte dans la liste déroulante.
   d. Dans la section Actions, spécifiez un nom pour l’action, tel que **Envoyer un message électronique**, et pour le **Type d’action**, sélectionnez **E-mail/SMS/Push/Voix** dans la liste déroulante. Le volet Propriétés **E-mail/SMS/Push/Voix** s’ouvre sur la droite afin de fournir des informations supplémentaires.
   e. Dans le volet **E-mail/SMS/Push/Voix**, sélectionnez et configurez votre préférence. Par exemple, autorisez les **e-mails** et fournissez une adresse e-mail SMTP valide à laquelle envoyer le message.
   f. Cliquez sur **OK** pour enregistrer vos modifications.<br><br>

    :::image type="content" source="media/monitor/action-group-properties-01.png" alt-text="Capture d’écran de création d’un groupe d’actions":::

10. Cliquez sur **OK** pour créer le groupe d’actions.
11. Cliquez sur **Créer une règle d’alerte** pour terminer la règle d’alerte. Son exécution démarre immédiatement.
    :::image type="content" source="media/monitor/alert-rule-01.png" alt-text="Capture d’écran de la finalisation de la création d’une règle d’alerte":::

### <a name="example-alert"></a>Exemple d’alerte

Pour référence, voici à quoi ressemble un exemple d’alerte dans Azure.

:::image type="content" source="media/monitor/alert.gif" alt-text="Capture d’écran d’alerte Azure":::

Voici un exemple de l’e-mail qui vous sera envoyé par Azure Monitor :

:::image type="content" source="media/monitor/warning.png" alt-text="Capture d’écran d’un exemple d’e-mail d’alerte":::

## <a name="create-custom-kusto-queries-in-log-analytics"></a>Créer des requêtes Kusto personnalisées dans Log Analytics

Vous pouvez également [écrire des requêtes de journal personnalisées](/azure/azure-monitor/log-query/get-started-queries) dans Azure Monitor à l’aide du langage de requête Kusto pour collecter des données à partir d’une ou de plusieurs machines virtuelles.

## <a name="get-a-consolidated-view-across-multiple-servers"></a>Obtenir une vue centralisée de plusieurs serveurs

Si vous intégrez plusieurs serveurs à un seul espace de travail Log Analytics dans Azure Monitor, vous pouvez obtenir une vue centralisée de tous ces serveurs à partir de la solution [Insights sur les machines virtuelles](/azure/azure-monitor/insights/vminsights-overview) dans Azure Monitor. (Notez que seuls les onglets Performances et Mappages de la solution Insights sur les machines virtuelles pour Azure Monitor fonctionnent avec les serveurs locaux : l’onglet Intégrité ne fonctionne qu’avec les machines virtuelles Azure.) Pour voir cela dans le portail Azure, accédez à **Azure Monitor > Machines virtuelles** (sous Insights), puis accédez aux onglets **Performances** ou **Mappages**.

## <a name="visualize-connected-services"></a>Visualiser les services connectés

Quand Windows Admin Center intègre un serveur à la solution Insights sur les machines virtuelles dans Azure Monitor, il active également une fonctionnalité appelée [Service Map](/azure/azure-monitor/insights/service-map). Cette fonctionnalité détecte automatiquement les composants d’application et mappe la communication entre les services afin que vous puissiez facilement visualiser les connexions entre les serveurs avec une grande précision à partir du portail Azure. Pour la trouver, accédez au portail Azure, sélectionnez **Azure Monitor > Machines virtuelles** (sous Insights), puis accédez à l’onglet **Mappages**.

> [!NOTE]
> Les visualisations de la solution Insights sur les machines virtuelles pour Azure Monitor sont actuellement proposées dans six régions publiques.  Pour obtenir les informations les plus récentes, consultez la [documentation Azure Monitor pour machines virtuelles](/azure/azure-monitor/insights/vminsights-onboard#log-analytics). Vous devez déployer l’espace de travail Log Analytics dans l’une des régions prises en charge pour bénéficier des autres avantages offerts par la solution Insights sur les machines virtuelles décrite ci-dessus.

## <a name="disabling-monitoring"></a>Désactivation de la supervision

Pour déconnecter complètement votre serveur de l’espace de travail Log Analytics, désinstallez l’agent MMA. Cela signifie que ce serveur n’enverra plus de données à l’espace de travail, et que toutes les solutions installées dans cet espace de travail ne collecteront plus et ne traiteront plus de données à partir de ce serveur. Toutefois, cela n’affecte pas l’espace de travail en soi. Toutes les ressources qui envoient de rapports à cet espace de travail continueront à le faire. Pour désinstaller l’agent MMA dans Windows Admin Center (WAC), accédez à **Applications et fonctionnalités**, recherchez **Microsoft Monitoring Agent**, puis cliquez sur **Désinstaller**.

Si vous voulez désactiver une solution spécifique dans un espace de travail, vous devez [supprimer la solution de supervision du portail Azure](/azure/azure-monitor/insights/solutions#remove-a-management-solution). La suppression d’une solution de supervision signifie que les insights créés par cette solution ne seront plus générés pour _aucun_ des serveurs envoyant des rapports à cet espace de travail. Par exemple, si vous désinstallez la solution Azure Monitor pour machines virtuelles, vous ne voyez plus d’insights sur les performances des machines virtuelles ou des serveurs en provenance de l’une des machines connectées à votre espace de travail.

## <a name="next-steps"></a>Étapes suivantes

Pour connaître les rubriques connexes, consultez également :

- [En savoir plus sur l’intégration d’Azure à Windows Admin Center](/windows-server/manage/windows-admin-center/azure/)
- [Utiliser Azure Monitor afin d’envoyer des e-mails pour les erreurs du service de contrôle d’intégrité](/windows-server/storage/storage-spaces/configure-azure-monitor)
