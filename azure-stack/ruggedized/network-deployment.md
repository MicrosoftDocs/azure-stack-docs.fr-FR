---
title: Déploiement réseau Azure Stack pour Azure Stack Hub renforcé
description: Découvrez le déploiement réseau Azure Stack pour l’appareil Azure Stack Hub renforcé.
author: PatAltimore
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: patricka
ms.reviewer: shisab
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: df20e3f9ad04959bf2c85d8912644819cae09ba1
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867687"
---
# <a name="azure-stack-hub-ruggedized-network-deployment"></a>Déploiement réseau Azure Stack Hub renforcé

Cette rubrique couvre l’autorisation d’accès aux commutateurs ToR, aux affectations d’adresses IP, ainsi qu’à d’autres tâches de déploiement réseau. 

## <a name="plan-configuration-deployment"></a>Déploiement de la configuration du plan

Les sections suivantes couvrent les autorisations et les affectations d’adresses IP.

### <a name="physical-switch-access-control-list"></a>Liste de contrôle d’accès du commutateur physique

Pour protéger la solution Azure Stack, nous avons implémenté des listes de contrôle d’accès (ACL) sur les commutateurs ToR. Cette section décrit l’implémentation de cette sécurité. Le tableau ci-dessous décrit les sources et les destinations de chaque réseau à l’intérieur de la solution Azure Stack :

[![Diagramme des listes de contrôle d’accès sur les commutateurs ToR](media/network-deployment/acls.png)](media/network-deployment/acls.png#lightbox)

Le tableau ci-dessous met en corrélation les références des listes de contrôle d’accès avec les réseaux Azure Stack.

| Gestion d’interface BMC                                   | Adresses IP de la machine virtuelle de déploiement, de l’interface BMC, du serveur HLH, du serveur NTP et du serveur DNS autorisées en fonction du protocole et du port. |
|--------------------------------------------|----------------------------------------------------------------------------------------------------------------------------|
| Serveur HLH interne accessible (PDU)              | Le trafic est limité au commutateur BMC                                                                                           |
| Serveur HLH externe accessible (machine virtuelle de l’outil OEM)      | La liste de contrôle d’accès autorise l’accès au-delà du périphérique de bordure.                                                                             |
| Gestion du commutateur                                | Interfaces de gestion de commutateur dédiées.                                                                                    |
| Gestion de branche centrale                                 | Interfaces de gestion de branche centrale dédiées.                                                                                     |
| Azure Stack                                | Services et machines virtuelles, réseau restreint de l’infrastructure Azure Stack                                                           |
| Infrastructure                             |                                                                                                                            |
| Azure Stack                                | Point de terminaison protégé, serveur de la console de récupération d’urgence Azure Stack                                                          |
| Infrastructure                             |                                                                                                                            |
| Public (PEP/ERCS)                          |                                                                                                                            |
| Tor1,Tor2 RouterIP                         | Interface de bouclage du commutateur utilisé pour le peering BGP entre le SLB et le commutateur/routeur.                                   |
| Stockage                                    | Adresses IP privées non routées en dehors de la région                                                                              |
| Adresses IP virtuelles internes                              | Adresses IP privées non routées en dehors de la région                                                                              |
| Adresses IP virtuelles publiques                                | Espace d’adressage du réseau de l’abonné géré par le contrôleur de réseau.                                                            |
| Adresses IP virtuelles d’administration publiques                          | Petit sous-ensemble d’adresses dans le pool d’abonnés requises pour communiquer avec les adresses IP virtuelles internes et l’infrastructure Azure Stack     |
| Client/Internet                          | Réseau défini par le client. Du point de vue de Azure Stack 0.0.0.0 correspond au périphérique de bordure.                                |
| 0.0.0.0                                    |                                                                                                                            |
| **Deny**                                     | Le client peut mettre à jour ce champ pour permettre aux réseaux supplémentaires d’activer les fonctionnalités de gestion.             |
| **Autoriser**                                 | L’autorisation du trafic est activée, mais l’accès SSH est désactivé par défaut. Le client peut choisir d’activer le service SSH.         |
| **Aucun itinéraire**                                   | Les itinéraires ne sont pas propagés en dehors de l’environnement Azure Stack.                                                          |
| **LISTE DE CONTRÔLE D’ACCÈS MUX**                                    | Les listes de contrôle d’accès MUX Azure Stack sont utilisées.                                                                                        |
| **N/A**                                        | Ne fait pas partie d’une liste de contrôle d’accès VLAN.                                                                                                  |

### <a name="ip-address-assignments"></a>Affectations d'adresses IP

Dans la Feuille de travail de déploiement, vous êtes invité à fournir les adresses réseau suivantes pour prendre en charge le processus de déploiement Azure Stack. L’équipe de déploiement utilise l’outil Feuille de calcul de déploiement pour rompre les réseaux IP dans tous les plus petits réseaux requis par le système. Pour obtenir une description détaillée de chaque réseau, consultez la section relative à la conception et à l’infrastructure d’un réseau ci-dessus.

Dans cet exemple, nous allons renseigner l’onglet Paramètres réseau de la Feuille de calcul déploiement avec les valeurs suivantes :

-   Réseau BMC : 10.193.132.0 /27

-   Réseau de stockage, réseau privé et adresses IP virtuelles internes : 11.11.128.0 /24

-   Réseau d'infrastructure : 12.193.130.0 /24

-   Réseau d’adresses IP virtuelles (VIP) publiques : 13.200.132.0 /24

-   Réseau d’infrastructure du commutateur : 10.193.132.128 /26

Lorsque vous exécutez la fonction Générer de l’outil Feuille de calcul de déploiement, elle crée deux onglets sur la feuille de calcul. Le premier onglet est le Résumé du sous-réseau et montre comment les réseaux Supernet ont été fractionnés pour créer tous les réseaux requis par le système. Dans notre exemple ci-dessous, il n’existe qu’un sous-ensemble des colonnes figurant sous cet onglet. Le résultat réel contient plus de détails sur chaque réseau indiqué :

| **Rack** | **Type de sous-réseau** | **Nom**                                   | **Sous-réseau IPv4**   | **Adresses IPv4** |
|----------|-----------------|--------------------------------------------|-------------------|--------------------|
| Bordure   | Lien P2P        | P2P_Border/Border1_To_Rack1/TOR1           | 10.193.132.128/30 | 4                  |
| Bordure   | Lien P2P        | P2P_Border/Border1_To_Rack1/TOR2           | 10.193.132.132/30 | 4                  |
| Bordure   | Lien P2P        | P2P_Border/Border2_To_Rack1/TOR1           | 10.193.132.136/30 | 4                  |
| Bordure   | Lien P2P        | P2P_Border/Border2_To_Rack1/TOR2           | 10.193.132.140/30 | 4                  |
| Bordure   | Lien P2P        | P2P_Rack1/TOR1_To_Rack1/BMC                | 10.193.132.144/30 | 4                  |
| Bordure   | Lien P2P        | P2P_Rack1/TOR2_To_Rack1/BMC                | 10.193.132.148/30 | 4                  |
| Rack1    | Bouclage        | Loopback0_Rack1_TOR1                       | 10.193.132.152/32 | 1                  |
| Rack1    | Bouclage        | Loopback0_Rack1_TOR2                       | 10.193.132.153/32 | 1                  |
| Rack1    | Bouclage        | Loopback0_Rack1_BMC                        | 10.193.132.154/32 | 1                  |
| Rack1    | Lien P2P        | P2P_Rack1/TOR1-ibgp-1_To_Rack1/TOR2-ibgp-1 | 10.193.132.156/30 | 4                  |
| Rack1    | Lien P2P        | P2P_Rack1/TOR1-ibgp-2_To_Rack1/TOR2-ibgp-2 | 10.193.132.160/30 | 4                  |
| Rack1    | VLAN            | BMCMgmt                                    | 10.193.132.0/27   | 32                 |
| Rack1    | VLAN            | SwitchMgmt                                 | 10.193.132.168/29 | 8                  |
| Rack1    | VLAN            | CL01-RG01-SU01-Storage                     | 11.11.128.0/25    | 128                |
| Rack1    | VLAN            | CL01-RG01-SU01-Infra                       | 12.193.130.0/24   | 256                |
| Rack1    | Autre           | CL01-RG01-SU01-VIPS                        | 13.200.132.0/24   | 256                |
| Rack1    | Autre           | CL01-RG01-SU01-InternalVIPS                | 11.11.128.128/25  | 128                |

Le deuxième onglet est **Utilisation des adresses IP** et montre comment les adresses IP sont consommées :

#### <a name="bmc-network"></a>Réseau BMC

Le réseau Supernet pour le réseau BMC nécessite au minimum un réseau /26. La passerelle utilise la première adresse IP du réseau, suivie des périphériques BMC dans le rack. Plusieurs adresses sont attribuées à l’hôte de cycle de vie du matériel sur ce réseau qui peut être utilisé pour déployer, analyser et prendre en charge le rack. Ces adresses IP sont distribuées dans 3 groupes : DVM, InternalAccessible et ExternalAccessible.

- Rack : Rack1        
- Nom : BMCMgmt      

| **Affecté à**      | **Adresse IPv4** |
|----------------------|------------------|
| Réseau              | 10.193.132.0     |
| Passerelle              | 10.193.132.1     |
| HLH-BMC              | 10.193.132.2     |
| AzS-Node01           | 10.193.132.3     |
| AzS-Node02           | 10.193.132.4     |
| AzS-Node03           | 10.193.132.5     |
| AzS-Node04           | 10.193.132.6     |
| ExternalAccessible-1 | 10.193.132.19    |
| ExternalAccessible-2 | 10.193.132.20    |
| ExternalAccessible-3 | 10.193.132.21    |
| ExternalAccessible-4 | 10.193.132.22    |
| ExternalAccessible-5 | 10.193.132.23    |
| InternalAccessible-1 | 10.193.132.24    |
| InternalAccessible-2 | 10.193.132.25    |
| InternalAccessible-3 | 10.193.132.26    |
| InternalAccessible-4 | 10.193.132.27    |
| InternalAccessible-5 | 10.193.132.28    |
| CL01-RG01-SU01-DVM00 | 10.193.132.29    |
| HLH-OS               | 10.193.132.30    |
| Diffusion            | 10.193.132.31    |

#### <a name="storage-network"></a>Réseau de stockage

Le réseau de stockage est un réseau privé et n’est pas destiné à être acheminé au-delà du rack. C’est la première moitié du réseau Supernet du réseau privé et elle est utilisée par le commutateur distribué comme indiqué dans le tableau ci-dessous. La passerelle est la première adresse IP du sous-réseau. La deuxième moitié utilisée pour les adresses IP virtuelles internes est un pool privé d’adresses managé par Azure Stack SLB et n’est pas affichée sous l’onglet Utilisation des adresses IP. Ces réseaux prennent en charge Azure Stack, incluant des listes de contrôle d’accès sur les commutateurs ToR qui empêchent la publication et/ou l’accès à ces réseaux en dehors de la solution.

- Rack : Rack1               
- Nom : CL01-RG01-SU01-Storage 

| **Affecté à**              | **Adresse IPv4** |
|------------------------------|------------------|
| Réseau                      | 11.11.128.0      |
| Passerelle                      | 11.11.128.1      |
| TOR1                         | 11.11.128.2      |
| TOR2                         | 11.11.128.3      |
| Diffusion                    | 11.11.128.127    |

#### <a name="azure-stack-infrastructure-network"></a>Réseau d’infrastructure Azure Stack

Le réseau Supernet du réseau d’infrastructure requiert un réseau /24 et continue à être un /24 après l’exécution de l’outil Feuille de calcul de déploiement. La passerelle est la première adresse IP du sous-réseau.

- Rack : Rack1 
- Nom : CL01-RG01-SU01-Infra 

| **Affecté à**            | **Adresse IPv4** |
|----------------------------|------------------|
| Réseau                    | 12.193.130.0     |
| Passerelle                    | 12.193.130.1     |
| TOR1                       | 12.193.130.2     |
| TOR2                       | 12.193.130.3     |
| Diffusion                  | 12.193.130.255   |

#### <a name="switch-infrastructure-network"></a>Réseau d’infrastructure du commutateur

Le réseau d’infrastructure est divisé en plusieurs réseaux utilisés par l’infrastructure du commutateur physique. Cela diffère de l’infrastructure Azure Stack qui ne prend en charge que le logiciel Azure Stack. Le Réseau infra du commutateur ne prend en charge que l’infrastructure du commutateur physique. Les réseaux pris en charge par infra sont les suivants :

| **Nom**                                   | **Sous-réseau IPv4**   |
|--------------------------------------------|-------------------|
| P2P_Border/Border1_To_Rack1/TOR1           | 10.193.132.128/30 |
| P2P_Border/Border1_To_Rack1/TOR2           | 10.193.132.132/30 |
| P2P_Border/Border2_To_Rack1/TOR1           | 10.193.132.136/30 |
| P2P_Border/Border2_To_Rack1/TOR2           | 10.193.132.140/30 |
| P2P_Rack1/TOR1_To_Rack1/BMC                | 10.193.132.144/30 |
| P2P_Rack1/TOR2_To_Rack1/BMC                | 10.193.132.148/30 |
| Loopback0_Rack1_TOR1                       | 10.193.132.152/32 |
| Loopback0_Rack1_TOR2                       | 10.193.132.153/32 |
| Loopback0_Rack1_BMC                        | 10.193.132.154/32 |
| P2P_Rack1/TOR1-ibgp-1_To_Rack1/TOR2-ibgp-1 | 10.193.132.156/30 |
| P2P_Rack1/TOR1-ibgp-2_To_Rack1/TOR2-ibgp-2 | 10.193.132.160/30 |
| SwitchMgmt                                 | 10.193.132.168/29 |
|                                            |                   |

-   Point à point (P2P) : Ces réseaux autorisent la connectivité entre tous les commutateurs. La taille du sous-réseau est un réseau /30 pour chaque P2P. L’adresse IP la plus basse est toujours attribuée au périphérique en amont (Nord) de la pile.

-   Bouclage : Ces adresses sont des réseaux /32 attribués à chaque commutateur utilisé dans le rack. Aucun bouclage n’est attribué aux périphériques de bordure, car ils ne sont pas censés faire partie de la solution Azure Stack.

-   Gestion du commutateur ou gestion des commutateurs : Ce réseau /29 prend en charge les interfaces de gestion dédiées des commutateurs dans le rack. Les adresses IP sont attribuées comme suit : Cette table se trouve également dans l’onglet Utilisation des adresses IP de la feuille de calcul de déploiement :

- Rack : Rack1    
- Nom : SwitchMgmt 

| **Affecté à**  | **Adresse IPv4** |
|------------------|------------------|
| Réseau          | 10.193.132.168   |
| Passerelle          | 10.193.132.169   |
| TOR1             | 10.193.132.170   |
| TOR2             | 10.193.132.171   |
| Diffusion        | 10.193.132.175   |

## <a name="prepare-environment"></a>Préparer l’environnement

L’image de l’hôte du cycle de vie du matériel contient le conteneur Linux requis qui est utilisé pour générer la configuration du commutateur réseau physique.

La dernière boîte à outils de déploiement de partenaires inclut la dernière image conteneur.
L’image conteneur sur l’hôte de cycle de vie du matériel peut être remplacée lorsqu’il est nécessaire de générer une configuration de commutateur mise à jour.

Voici la procédure à suivre pour mettre à jour l’image conteneur :

1.  Télécharger l’image conteneur

2.  Remplacer l’image conteneur à l’emplacement suivant

## <a name="generate-configuration"></a>Générer la configuration

Ici, nous allons vous guider tout au long des étapes de génération des fichiers JSON et des fichiers de configuration du commutateur réseau :

1.  Ouvrir la Feuille de déploiement

2.  Remplir tous les champs obligatoires de tous les onglets

3.  Appelez la fonction « Générer » dans la Feuille de calcul de déploiement.  
    Deux onglets supplémentaires seront créés pour afficher les sous-réseaux d’adresses IP et les affectations générés.

4.  Consultez les données et, une fois confirmées, appelez la fonction « Exporter ».  
    Vous serez invité à fournir un dossier dans lequel les fichiers JSON seront enregistrés.

5.  Exécutez le conteneur à l’aide de Invoke-SwitchConfigGenerator.ps1. Ce script nécessite l’exécution d’une console PowerShell avec élévation de privilèges et des paramètres suivants.

    -   ContainerName : nom du conteneur qui générera les configurations du commutateur.

    -   ConfigurationData : chemin d’accès au fichier ConfigurationData.json exporté à partir de la feuille de calcul de déploiement.

    -   OutputDirectory : chemin d’accès au répertoire de sortie.

    -   Hors connexion : signale que le script s’exécute en mode hors connexion.

    ```powershell
    C:\\WINDOWS\\system32\> .\\Invoke-SwitchConfigGenerate.ps1 -ContainerName generalonrampacr.azurecr.io/master -ConfigurationData .\\ConfigurationData.json -OutputDirectory c:\\temp -Offline
    ```

Lorsque le script est terminé, il génère un fichier zip avec le préfixe utilisé dans la feuille de calcul. 

```console
C:\WINDOWS\system32> .\Invoke-SwitchConfigGenerate.ps1 -ContainerName generalonrampacr.azurecr.io/master -ConfigurationData .\ConfigurationData.json -OutputDirectory c:\temp -Offline                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         
Seconds : 2
Section : Validation
Step    : WindowsRequirement
Status  : True
Detail  : @{CurrentImage=10.0.18363.0}


Seconds : 2
Section : Validation
Step    : DockerService
Status  : True
Detail  : @{Status=Running}


Seconds : 9
Section : Validation
Step    : DockerSetup
Status  : True
Detail  : @{CPU=4; Memory=4139085824; OS=Docker Desktop; OSType=linux}


Seconds : 9
Section : Validation
Step    : DockerImage
Status  : True
Detail  : @{Container=generalonrampacr.azurecr.io/master:1.1910.78.1}


Seconds : 10
Section : Run
Step    : Container
Status  : True
Detail  : @{ID=2a20ba622ef9f58f9bcd069c3b9af7ec076bae36f12c5653f9469b988c01706c; ExternalPort=32768}


Seconds : 38
Section : Generate
Step    : Config
Status  : True
Detail  : @{OutputFile=c:\temp\N22R19.zip}


Seconds : 38
Section : Exit
Step    : StopContainer
Status  : True
Detail  : @{ID=2a20ba622ef9f58f9bcd069c3b9af7ec076bae36f12c5653f9469b988c01706c}
```

### <a name="custom-configuration"></a>Configuration personnalisée

Vous pouvez modifier quelques paramètres environnementaux de votre configuration de commutateur Azure Stack. Vous pouvez identifier les paramètres que vous pouvez modifier dans le modèle. Cet article explique chacun de ces paramètres personnalisables ainsi que l’impact possible des modifications sur l’infrastructure Azure Stack. Ces paramètres incluent la mise à jour du mot de passe, le serveur Syslog, l’analyse SNMP, l’authentification et la liste de contrôle d’accès.

Pendant le déploiement de la solution Azure Stack, le fabricant d’ordinateurs (OEM) crée et applique la configuration du commutateur pour Tor et pour BMC. Le fabricant d’ordinateurs (OEM) utilise l’outil d’automatisation Azure Stack pour vérifier que les configurations requises sont correctement définies sur ces appareils. La configuration est basée sur les informations contenues dans votre feuille de calcul de déploiement Azure Stack. 

>[!NOTE]
>Ne modifiez **pas** la configuration sans le consentement de l’équipe d’ingénierie OEM ou Microsoft Azure Stack. Une modification de la configuration des périphériques réseau peut avoir un impact significatif sur le fonctionnement ou la résolution de problèmes réseau dans votre instance Azure Stack. Pour plus d’informations sur ces fonctions de votre périphérique réseau ou sur la manière d’effectuer ces modifications, contactez votre fournisseur de matériel OEM ou le support Microsoft. Votre OEM dispose du fichier de configuration créé par l’outil d’automatisation sur la base de votre feuille de déploiement Azure Stack. 

Toutefois, certaines valeurs peuvent être ajoutées, supprimées ou modifiées dans la configuration des commutateurs réseau.

#### <a name="password-update"></a>Mise à jour du mot de passe

L’opérateur peut à tout moment mettre à jour le mot de passe d’un utilisateur sur les commutateurs réseau. Il n’est pas nécessaire de modifier les informations sur le système de Azure Stack ou d’utiliser les étapes pour Faire pivoter les secrets dans Azure Stack.

#### <a name="syslog-server"></a>Serveur syslog

Les opérateurs peuvent rediriger les journaux du commutateur vers un serveur Syslog dans leur centre de données.
Utilisez cette configuration pour vous assurer que les journaux d’un point spécifique dans le temps peuvent être utilisés pour résoudre des problèmes. Par défaut, les journaux sont stockés sur les commutateurs ; leur capacité de stockage de journaux est limitée. Consultez la section Mises à jour de la liste de contrôle d’accès pour avoir une vue d’ensemble de la configuration des autorisations pour l’accès à la gestion des commutateurs.

#### <a name="snmp-monitoring"></a>Analyse SNMP

L’opérateur peut configurer SNMP (simple Network Management Protocol) v2 ou v3 pour surveiller les périphériques réseau et envoyer des interruptions à une application de surveillance réseau dans le centre de données. Pour des raisons de sécurité, utilisez SNMPv3, qui est plus sécurisé que v2. Consultez votre fournisseur de matériel OEM pour obtenir les MIB et les configurations requises.
Consultez la section Mises à jour de la liste de contrôle d’accès pour avoir une vue d’ensemble de la configuration des autorisations pour l’accès à la gestion des commutateurs.

#### <a name="authentication"></a>Authentification

L’opérateur peut configurer RADIUS ou TACACS pour gérer l’authentification sur les périphériques réseau. Consultez votre fournisseur de matériel OEM pour obtenir les méthodes prise en charge et la configuration requise. Consultez la section Mises à jour de la liste de contrôle d’accès pour avoir une vue d’ensemble de la configuration des autorisations pour l’accès à la gestion des commutateurs.

#### <a name="access-control-list-updates"></a>Mises à jour de la liste de contrôle d'accès

L’opérateur peut modifier certaines listes de contrôle d’accès (ACL) pour autoriser l’accès à des interfaces de gestion des périphériques réseau et à l’hôte de cycle de vie du matériel (HLH) à partir d’une plage réseau de centre de données de confiance. L’opérateur peut choisir quel composant sera accessible et à partir d’où. Avec la liste de contrôle d’accès, l’opérateur peut autoriser ses machines virtuelles Jumpbox de gestion au sein d’une plage réseau spécifique à accéder à l’interface de gestion du commutateur, au système d’exploitation HLH et au BMC HLH.

Pour plus d’informations, consultez [Liste de contrôle d’accès du commutateur physique](#physical-switch-access-control-list).

#### <a name="tacacs-radius-and-syslog"></a>TACACS, RADIUS et Syslog

La solution Azure Stack n’est pas fournie avec une solution TACACS ou RADIUS pour le contrôle d’accès des appareils tels que les commutateurs et les routeurs, ni pour la solution Syslog pour capturer les journaux de commutateur, mais tous ces appareils prennent en charge ces services.
Pour faciliter l’intégration à un serveur TACACS, RADIUS et/ou Syslog existant dans votre environnement, nous fournissons un fichier supplémentaire avec la configuration du commutateur réseau qui permettra à l’ingénieur sur site de personnaliser le commutateur en fonction des besoins du client.

## <a name="next-steps"></a>Étapes suivantes

[Intégration réseau](network-integration.md)
