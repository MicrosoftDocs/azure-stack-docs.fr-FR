---
title: Considérations en matière de sécurité pour Azure Stack HCI
description: Cette rubrique fournit des conseils sur les considérations en matière de sécurité pour le système d’exploitation Azure Stack HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 90a7904a9f102688364b2776d81eee5d7d6e559a
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86946974"
---
# <a name="azure-stack-hci-security-considerations"></a>Considérations en matière de sécurité pour Azure Stack HCI

>S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Cette rubrique contient des considérations en matière de sécurité et des recommandations relatives au système d’exploitation Azure Stack HCI :
- La première partie couvre les outils de sécurité et les technologies de base permettant de durcir le système d’exploitation et de protéger les données et les identités afin de créer une solide fondation pour votre organisation.
- La deuxième partie couvre les ressources disponibles par le biais d’Azure Security Center.
- La troisième partie couvre des considérations en matière de sécurité avancée visant à durcir encore davantage la posture de sécurité de votre organisation dans ces domaines.

## <a name="why-are-security-considerations-important"></a>Pourquoi les considérations en matière de sécurité sont-elles importantes ?
La sécurité affecte tous les membres de votre organisation, des cadres de Direction jusqu’aux travailleurs de l’information. Une sécurité inadéquate est un réel risque pour les organisations, car une violation de la sécurité risque de perturber l’activité de l’entreprise, voire de provoquer son arrêt complet. Plus tôt vous pouvez détecter une attaque potentielle, plus rapidement vous pouvez réduire le risque de compromettre la sécurité.

Après avoir étudié les points faibles d’un environnement afin de les exploiter, une personne malveillante peut généralement, dans les 24 à 48 heures, escalader les privilèges pour prendre le contrôle des systèmes sur le réseau. Des mesures de sécurité adéquates durcissent les systèmes de l’environnement afin de prolonger le temps nécessaire à une personne malveillante pour prendre le contrôle (des semaines, voire des mois, plutôt que quelques heures) en bloquant les mouvements de l’attaquant. L’implémentation des recommandations de sécurité fournies dans cette rubrique permet à votre organisation de se positionner afin de détecter les attaques et d’y répondre le plus rapidement possible.

## <a name="part-1-build-a-secure-foundation"></a>Première partie : Créer une solide fondation
Les sections suivantes recommandent des outils et des technologies de sécurité permettant de créer une solide fondation pour les serveurs qui exécutent le système d’exploitation Azure Stack HCI dans votre environnement.

### <a name="harden-the-environment"></a>Durcir l’environnement
Cette section explique comment protéger les services et les machines virtuelles qui s’exécutent sur le système d’exploitation :
- Le **matériel certifié Azure Stack HCI** fournit des paramètres de démarrage sécurisé, UEFI et TPM cohérents prêts à l’emploi. La combinaison de la sécurité basée sur la virtualisation et du matériel certifié contribue à protéger les charges de travail sensibles à la sécurité. Vous pouvez également connecter cette infrastructure approuvée à Azure Security Center pour activer l’analytique comportementale et la création de rapports, afin de tenir compte des charges de travail et des menaces à évolution rapide.

    - Le *démarrage sécurisé* est une norme de sécurité développée par l’industrie du PC pour aider à garantir qu’un appareil démarre en utilisant uniquement les logiciels approuvés par le fabricant OEM (Original Equipment Manufacturer). Pour plus d’informations, consultez [Démarrage sécurisé](/windows-hardware/design/device-experiences/oem-secure-boot).
    - L’interface *UEFI (United Extensible Firmware Interface)* contrôle le processus de démarrage du serveur, puis transfère le contrôle à Windows ou à un autre système d’exploitation. Pour plus d’informations, consultez les [exigences relatives au microprogramme UEFI](/windows-hardware/design/device-experiences/oem-uefi).
    - La technologie de *Module de plateforme sécurisée (TPM)* fournit des fonctions matérielles liées à la sécurité. Une puce TPM est un processeur à chiffrement sécurisé qui génère, stocke et limite l’utilisation des clés de chiffrement. Pour plus d’informations, consultez [Vue d’ensemble de la technologie de module de plateforme sécurisée](/windows/security/information-protection/tpm/trusted-platform-module-overview).

    Pour en savoir plus sur les fournisseurs de matériel certifié Azure Stack HCI, consultez le site web [Solutions Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/).

- **Device Guard** et **Credential Guard**. Device Guard offre une protection contre les programmes malveillants sans signature connue, le code non signé et les logiciels malveillants qui accèdent au noyau pour capturer des informations sensibles ou endommager le système. Windows Defender Credential Guard utilise la sécurité basée sur la virtualisation pour isoler les secrets afin que seuls les logiciels système privilégiés puissent y accéder.

    Pour plus d’informations, consultez [Gérer Windows Defender Credential Guard](/windows/security/identity-protection/credential-guard/credential-guard-manage) et téléchargez l’[outil de préparation du matériel de Device Guard et Credential Guard](https://www.microsoft.com/en-us/download/details.aspx?id=53337).

- Les mises à jour de **Windows** et du **microprogramme** sont essentielles sur les clusters, les serveurs (y compris les machines virtuelles invitées) et les PC pour garantir la protection du système d’exploitation et du matériel système contre les attaquants. Vous pouvez utiliser l’outil **Mises à jour** de Windows Admin Center pour appliquer des mises à jour à des systèmes individuels. Si votre fournisseur de matériel inclut la prise en charge de Windows Admin Center pour obtenir les mises à jour des pilotes, des microprogrammes et des solutions, vous pouvez obtenir ces mises à jour en même temps que les mises à jour de Windows. Sinon, vous pouvez les obtenir directement auprès de votre fournisseur.

    Pour plus d’informations, consultez [Mettre à jour le cluster](../manage/update-cluster.md).

    Pour gérer les mises à jour sur plusieurs clusters et serveurs à la fois, abonnez-vous au service facultatif Azure Update Management, qui est intégré à Windows Admin Center. Pour plus d’informations, consultez [Azure Update Management using Windows Admin Center](https://www.thomasmaurer.ch/2018/11/azure-update-management-windows-admin-center).

### <a name="protect-data"></a>Protéger les données
Cette section explique comment utiliser Windows Admin Center pour protéger les données et les charges de travail sur le système d’exploitation :

- **BitLocker pour les espaces de stockage** protège les données au repos. Vous pouvez utiliser BitLocker pour chiffrer le contenu des volumes de données des espaces de stockage sur le système d’exploitation. L’utilisation de BitLocker pour protéger les données peut aider les organisations à maintenir la conformité aux normes gouvernementales, régionales et sectorielles telles que FIPS 140-2 et HIPAA.

    Pour accéder à BitLocker dans Windows Admin Center

    1. Connectez-vous à un cluster Espaces de stockage direct puis, dans le volet **Outils**, sélectionnez **Volumes**.
    1. Dans la page **Volumes**, sélectionnez **Inventaire** puis, sous **Fonctionnalités facultatives**, activez le bouton bascule **Chiffrement (BitLocker)** .
    
        :::image type="content" source="./media/security/bitlocker-toggle-switch.png" alt-text="Bouton bascule pour activer BitLocker":::
    
    1. Dans la fenêtre contextuelle **Chiffrement (BitLocker)** , sélectionnez **Démarrer** puis, dans la page **Activer le chiffrement**, indiquez vos informations d’identification pour terminer le flux de travail.

   >[!NOTE]
   > Si le message contextuel **Installer tout d’abord BitLocker** s’affiche, suivez les instructions pour installer la fonctionnalité sur chaque serveur du cluster, puis redémarrez vos serveurs.

- Le chiffrement **SMB** pour la mise en réseau Windows protège les données en transit. Le protocole *SMB (Server Message Block)* est un protocole de partage de fichiers réseau qui permet à des applications installées sur un ordinateur d’accéder en lecture et en écriture à des fichiers et de solliciter des services auprès de programmes serveur sur un réseau informatique.

    Pour activer le chiffrement SMB, consultez [Améliorations en matière de sécurité SMB](/windows-server/storage/file-server/smb-security).

- L’**Antivirus Windows Defender** dans Windows Admin Center protège le système d’exploitation sur les clients et les serveurs contre les virus, les logiciels malveillants, les logiciels espions et autres menaces. Pour plus d’informations, consultez [Antivirus Microsoft Defender sur Windows Server 2016 et 2019](/windows/security/threat-protection/microsoft-defender-antivirus/microsoft-defender-antivirus-on-windows-server-2016).

### <a name="protect-identities"></a>Protéger les identités
Cette section explique comment utiliser Windows Admin Center pour protéger les identités privilégiées :

- Le **contrôle d’accès** peut améliorer la sécurité de votre environnement de gestion. Si vous utilisez un serveur Windows Admin Center (plutôt qu’un PC Windows 10), vous pouvez contrôler deux niveaux d’accès à Windows Admin Center proprement dit : les utilisateurs de la passerelle et les administrateurs de la passerelle. Les options du fournisseur d’identité d’administrateur de passerelle sont les suivantes :
    - Groupes d’ordinateurs locaux ou Active Directory pour appliquer l’authentification par carte à puce
    - Azure Active Directory pour appliquer l’accès conditionnel et l’authentification multifacteur
 
    Pour plus d’informations, consultez [Options d’accès utilisateur avec Windows Admin Center](/windows-server/manage/windows-admin-center/plan/user-access-options) et [Configurer les autorisations et le contrôle d’accès utilisateur](/windows-server/manage/windows-admin-center/configure/user-access-control).

- Le **trafic du navigateur** vers Windows Admin Center utilise le protocole HTTPS. Le trafic de Windows Admin Center vers les serveurs managés utilise PowerShell standard et WMI (Windows Management Instrumentation) sur WinRM (Windows Remote Management). Windows Admin Center prend en charge la Solution de mot de passe d’administrateur local (LAPS), la délégation contrainte basée sur les ressources, le contrôle d’accès à la passerelle à l’aide d’Active Directory (AD) ou de Microsoft Azure Active Directory (Azure AD), ainsi que le contrôle d’accès en fonction du rôle (RBAC) pour la gestion des serveurs cibles.

    Windows Admin Center prend en charge Microsoft Edge (Windows 10, version 1709 ou ultérieure), Google Chrome et Microsoft Edge Insider sur Windows 10. Vous pouvez installer Windows Admin Center sur un PC Windows 10 ou un serveur Windows.

    Si vous installez Windows Admin Center sur un serveur, il s’exécute en tant que passerelle, sans interface utilisateur sur le serveur hôte. Dans ce scénario, les administrateurs peuvent se connecter au serveur via une session HTTPS, sécurisée par un certificat de sécurité auto-signé sur l’hôte. Toutefois, il est préférable d’utiliser un certificat SSL approprié délivré par une autorité de certification approuvée pour le processus d’authentification, car les navigateurs pris en charge traitent une connexion auto-signée comme étant non sécurisée, même s’il s’agit d’une connexion à une adresse IP locale sur un VPN approuvé.

    Pour en savoir plus sur les options d’installation pour votre organisation, consultez [Quel type d’installation vous convient ?](/windows-server/manage/windows-admin-center/plan/installation-options).

- **CredSSP** est un fournisseur d’authentification que Windows Admin Center utilise dans certains cas pour transmettre les informations d’identification aux ordinateurs au-delà du serveur spécifique dont vous ciblez la gestion. Windows Admin Center exige actuellement CredSSP pour :
    - Créer un cluster.
    - Accéder à l’outil **Mises à jour** pour utiliser la fonctionnalité Clustering de basculement ou Mise à jour adaptée aux clusters.
    - Gérer le stockage SMB désagrégé sur des machines virtuelles.

    Pour plus d’informations, consultez [Windows Admin Center utilise-t-il CredSSP ?](/windows-server/manage/windows-admin-center/understand/faq#does-windows-admin-center-use-credssp)

- Le **contrôle d’accès en fonction du rôle (RBAC)** dans Windows Admin Center permet aux utilisateurs de disposer d’un accès limité aux serveurs qu’ils doivent gérer, au lieu d’en faire des administrateurs locaux complets. Pour utiliser RBAC dans Windows Admin Center, vous configurez chaque serveur managé avec un point de terminaison JEA (Just Enough Administration) PowerShell.

    Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle](/windows-server/manage/windows-admin-center/plan/user-access-options#role-based-access-control) et [Just Enough Administration](/powershell/scripting/learn/remoting/jea/overview?view=powershell-7).

- Les **outils de sécurité** dans Windows Admin Center que vous pouvez utiliser pour gérer et protéger les identités incluent Active Directory, Certificats, Pare-feu, Utilisateurs et groupes locaux, et bien plus encore.

    Pour plus d’informations, consultez [Gérer des serveurs à l’aide de Windows Admin Center](/windows-server/manage/windows-admin-center/use/manage-servers).

## <a name="part-2-use-azure-security-center"></a>Deuxième partie : Utiliser Azure Security Center
*Azure Security Center* est un système de gestion de la sécurité de l’infrastructure unifié qui renforce la posture de sécurité de vos centres de données et fournit une protection avancée contre les menaces pour vos charges de travail hybrides dans le cloud et localement. Security Center fournit des outils permettant d’évaluer l’état de sécurité de votre réseau, de protéger les charges de travail, de déclencher des alertes de sécurité et de suivre des recommandations spécifiques afin de parer les attaques et de gérer les menaces futures. Security Center effectue tous ces services à grande vitesse dans le Cloud sans aucune surcharge de déploiement grâce à la protection et au provisionnement automatique avec les services Azure.

Security Center protège les machines virtuelles des serveurs Windows et Linux en installant l’agent Log Analytics sur ces ressources. Azure met en corrélation les événements collectés par les agents dans des recommandations (tâches de durcissement) que vous effectuez pour sécuriser vos charges de travail. Les tâches de durcissement basées sur les bonnes pratiques en matière de sécurité incluent la gestion et l’application de stratégies de sécurité. Vous pouvez ensuite effectuer le suivi des résultats et gérer la conformité et la gouvernance dans le temps via la supervision Security Center tout en réduisant la surface d’attaque sur l’ensemble de vos ressources.

La gestion des personnes autorisées à accéder à vos ressources et abonnements Azure constitue une partie importante de votre stratégie de gouvernance Azure. Le contrôle d’accès en fonction du rôle (RBAC) Azure est la principale méthode de gestion de l’accès dans Azure. Pour en savoir plus, consultez [Gérer l’accès à votre environnement Azure avec des contrôles d’accès en fonction du rôle](/azure/cloud-adoption-framework/ready/azure-setup-guide/manage-access).

L’utilisation de Security Center par le biais de Windows Admin Center nécessite un abonnement Azure. Pour bien démarrer, consultez [Intégrer Azure Security Center à Windows Admin Center](/azure/security-center/windows-admin-center-integration).

Après l’inscription, accédez à Security Center dans Windows Admin Center : Dans la page **Toutes les connexions**, sélectionnez un serveur ou une machine virtuelle, sous **Outils**, sélectionnez **Azure Security Center**, puis sélectionnez **Se connecter à Azure**.

Pour plus d’informations, consultez [Qu’est-ce que le Centre de sécurité Azure ?](/azure/security-center/security-center-intro)

## <a name="part-3-add-advanced-security"></a>Troisième partie : Ajouter une sécurité avancée
Les sections suivantes recommandent des outils de sécurité avancée et des technologies permettant de durcir les serveurs exécutant le système d’exploitation Azure Stack HCI dans votre environnement.

### <a name="harden-the-environment"></a>Durcir l’environnement
- **Les bases de référence de sécurité Microsoft** sont basées sur des recommandations de sécurité de Microsoft obtenues par le biais d’un partenariat avec des organisations commerciales et des agences du gouvernement des États-Unis telles que le Ministère de la défense. Les bases de référence de sécurité incluent des paramètres de sécurité recommandés pour le Pare-feu Windows, Windows Defender et bien d’autres.

    Les bases de référence de sécurité sont fournies en tant que sauvegardes d’objets de stratégie de groupe (GPO) que vous pouvez importer dans Active Directory Domain Services (AD DS), puis déployer sur des serveurs joints à un domaine pour durcir l’environnement. Vous pouvez également utiliser des outils de script locaux pour configurer des serveurs autonomes (non joints à un domaine) avec des bases de référence de sécurité. Pour commencer à utiliser les bases de référence de sécurité, téléchargez [Microsoft Security Compliance Toolkit 1.0](https://www.microsoft.com/download/details.aspx?id=55319).

    Pour plus d’informations, consultez [Bases de référence de sécurité Microsoft](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines).

### <a name="protect-data"></a>Protection des données
- Le **durcissement de l’environnement Hyper-V** nécessite le durcissement de Windows Server exécuté sur une machine virtuelle, comme vous le feriez pour durcir le système d’exploitation qui s’exécute sur un serveur physique. Les environnements virtuels ayant généralement plusieurs machines virtuelles qui partagent le même hôte physique, il est impératif de protéger à la fois l’hôte physique et les machines virtuelles qui s’y exécutent. Une personne malveillante qui compromet un hôte peut affecter plusieurs machines virtuelles avec un impact plus important sur les charges de travail et les services. Cette section décrit les méthodes suivantes que vous pouvez appliquer pour renforcer Windows Server dans un environnement Hyper-V :

    - **Une infrastructure protégée et des VM dotées d’une protection maximale** durcissent la sécurité des machines virtuelles exécutées dans des environnements Hyper-V en empêchant les attaquants de modifier les fichiers de machine virtuelle. Une *infrastructure protégée* est constituée d’un service Guardian hôte (SGH) qui est généralement un cluster de trois nœuds, d’un ou plusieurs hôtes protégés, et d’un ensemble de VM dotées d’une protection maximale. Le service d’attestation évalue la validité des demandes des hôtes, tandis que le service de protection de clé détermine s’il faut libérer les clés que les hôtes service Guardian peuvent utiliser pour démarrer la VM dotée d’une protection maximale.

        Pour plus d’informations, consultez [Vue d’ensemble de la structure protégée et des machines virtuelles dotées d’une protection maximale](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms).
     
     - Le module **vTPM (Virtual Trusted Platform Module)** dans Windows Server prend en charge le module de plateforme sécurisée pour les machines virtuelles, qui vous permet d’utiliser des technologies de sécurité avancée telles que BitLocker dans des machines virtuelles. Vous pouvez activer la prise en charge du module de plateforme sécurisée sur n’importe quelle machine virtuelle Hyper-V de Génération 2 à l’aide du Gestionnaire Hyper-V ou de l’applet de commande Windows PowerShell `Enable-VMTPM`.
     
        Pour plus d’informations, consultez [Enable-VMTPM](/powershell/module/hyper-v/enable-vmtpm?view=win10-ps).
     
     - **SDN (Software Defined Networking)** dans Azure Stack HCI et Windows Server centralise la configuration et la gestion des périphériques réseau physiques et virtuels, tels que les routeurs, les commutateurs et les passerelles, dans votre centre de données. Les éléments de réseau virtuel, tels que le commutateur virtuel Hyper-V, la virtualisation de réseau Hyper-V et la passerelle RAS, sont conçus pour être des éléments intégraux de votre infrastructure SDN.

        Pour plus d’informations, consultez [Mise en réseau SDN (Software Defined Networking)](/windows-server/networking/sdn/).

### <a name="protect-identities"></a>Protéger les identités
- La **Solution de mot de passe d’administrateur local (LAPS)** est un mécanisme léger pour les systèmes joints à un domaine Active Directory qui affecte périodiquement une nouvelle valeur aléatoire et unique comme mot de passe du compte d’administrateur local de chaque ordinateur. Les mots de passe sont stockés dans un attribut confidentiel sécurisé sur l’objet ordinateur correspondant dans Active Directory, où seuls les utilisateurs spécifiquement autorisés peuvent les récupérer. LAPS utilise les comptes locaux pour la gestion des ordinateurs distants d’une manière qui offre des avantages par rapport à l’utilisation des comptes de domaine. Pour plus d’informations, consultez [Remote Use of Local Accounts: LAPS Changes Everything](/archive/blogs/secguide/remote-use-of-local-accounts-laps-changes-everything) (Utilisation à distance de comptes locaux : LAPS change tout).

    Pour commencer à utiliser LAPS, téléchargez [Local Administrator Password Solution (LAPS)](https://www.microsoft.com/download/details.aspx?id=46899).

- **Microsoft Advanced Threat Analytics (ATA)** est un produit local que vous pouvez utiliser pour détecter les tentatives de compromission des identités privilégiées par des attaquants. ATA analyse le trafic réseau à la recherche des protocoles d’authentification, d’autorisation et de collecte d’informations, tels que Kerberos et DNS. ATA utilise les données recueillies pour créer des profils de comportement des utilisateurs et d’autres entités sur le réseau, afin de détecter les anomalies et les modèles d’attaque connus.
    
    Pour plus d’informations, consultez [Qu’est-ce qu’Advanced Threat Analytics ?](/advanced-threat-analytics/what-is-ata).

- **Windows Defender Remote Credential Guard** protège les informations d’identification sur une connexion Bureau à distance en redirigeant les requêtes Kerberos vers l’appareil qui demande la connexion. Il fournit également l’authentification unique pour les sessions Bureau à distance. Au cours d’une session Bureau à distance, si l’appareil cible est compromis, vos informations d’identification ne sont pas exposées car les informations d’identification et leurs dérivés ne sont jamais transmis sur le réseau à l’appareil cible.

    Pour plus d’informations, consultez [Gérer Windows Defender Credential Guard](/windows/security/identity-protection/credential-guard/credential-guard-manage).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la sécurité et la conformité réglementaire, consultez également :
- [Sécurité et assurance](/windows-server/security/security-and-assurance)
- [Meilleures pratiques de sécurité pour les solutions Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/)
