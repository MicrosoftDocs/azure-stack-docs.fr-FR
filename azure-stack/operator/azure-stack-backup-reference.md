---
title: Informations de référence sur le service Infrastructure Backup
description: Documentation de référence sur le service Infrastructure Backup dans Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 10/25/2019
ms.openlocfilehash: 0760e7d796c6e17c88089675fa6ff659eb684cc7
ms.sourcegitcommit: 721b82b3a1711f2825ec76ab6d75964b4f508631
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84301026"
---
# <a name="infrastructure-backup-service-reference"></a>Informations de référence sur le service Infrastructure Backup

## <a name="azure-backup-infrastructure"></a>Infrastructure de sauvegarde Azure

Azure Stack Hub se compose de plusieurs services comprenant le portail (Azure Resource Manager) et l’interface de gestion de l’infrastructure. L’interface de gestion de type application d’Azure Stack Hub vise à simplifier la tâche de l’opérateur de la solution.

Le service Infrastructure Backup est conçu pour absorber la complexité de la sauvegarde et de la restauration des données des services d’infrastructure, et permettre ainsi aux opérateurs de se concentrer sur la gestion de la solution et des contrats SLA des utilisateurs.

L’exportation des données de sauvegarde vers un partage externe est nécessaire pour éviter de stocker les sauvegardes sur le même système. L’obligation d’utiliser un partage externe permet à l’administrateur de choisir où stocker les données en fonction des stratégies de continuité d’activité et de reprise d’activité de l’entreprise.

### <a name="infrastructure-backup-service-components"></a>Composants du service Infrastructure Backup

Le service Infrastructure Backup comprend les composants suivants :

 - **Contrôleur Infrastructure Backup**  
 Le contrôleur Infrastructure Backup est instancié avec chaque cloud Azure Stack Hub et y réside.
 - **Fournisseur de ressources de sauvegarde**  
 Le fournisseur de ressources de sauvegarde se compose de l’interface utilisateur et des API exposant les fonctionnalités de sauvegarde de base pour l’infrastructure Azure Stack Hub.

#### <a name="infrastructure-backup-controller"></a>Infrastructure Backup Controller

Le contrôleur Infrastructure Backup est un service de Service Fabric qui est instancié pour un cloud Azure Stack Hub. Les ressources de sauvegarde sont créées à un niveau régional et capturent les données des services propres à une région provenant de AD, CA, Azure Resource Manager, CRP, SRP, NRP, Key Vault et RBAC.

### <a name="backup-resource-provider"></a>Fournisseur de ressources de sauvegarde

Le fournisseur de ressources de sauvegarde fournit une interface utilisateur dans le portail Azure Stack Hub, et donne accès à la configuration de base ainsi qu’à la liste des ressources de sauvegarde. Les opérateurs peuvent effectuer les actions suivantes dans l’interface utilisateur :

 - Activer la sauvegarde pour la première fois en fournissant l’emplacement de stockage externe, les informations d’identification et la clé de chiffrement
 - Consulter les ressources de sauvegarde déjà créées et l’état des ressources en cours de création
 - Changer l’emplacement de stockage où Backup Controller place les données de sauvegarde
 - Changer les informations d’identification utilisées par Backup Controller pour accéder à l’emplacement de stockage externe
 - Changer la clé de chiffrement utilisée par Backup Controller pour chiffrer les sauvegardes


## <a name="backup-controller-requirements"></a>Configuration requise pour Backup Controller

Cette section décrit les éléments importants de la configuration requise du service Infrastructure Backup. Nous vous recommandons de lire attentivement les informations suivantes avant d’activer la sauvegarde pour votre instance Azure Stack Hub, puis d’y revenir si nécessaire pendant le déploiement et les opérations suivantes.

La configuration requise inclut :

  - **Configuration logicielle exigée** : décrit les emplacements de stockage pris en charge et contient une aide pour le dimensionnement. 
  - **Configuration réseau exigée** : décrit les exigences en matière de réseau des différents emplacements de stockage.  

### <a name="software-requirements"></a>Configuration logicielle requise

#### <a name="supported-storage-locations"></a>Emplacements de stockage pris en charge

| Emplacement de stockage                                                                 | Détails                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Partage de fichiers SMB hébergé sur un dispositif de stockage dans l’environnement réseau approuvé | Partage SMB dans le centre de données dans lequel Azure Stack Hub est déployé ou dans un autre. Plusieurs instances Azure Stack Hub peuvent utiliser le même partage de fichiers. |
| Partage de fichiers SMB sur Azure                                                          | Non pris en charge actuellement.                                                                                                                                 |
| Stockage d’objets blob sur Azure                                                            | Non pris en charge actuellement.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Versions de SMB prises en charge

| SMB | Version |
|-----|---------|
| SMB | 3.x     |

#### <a name="smb-encryption"></a>Chiffrement SMB

**1907 et au-delà**

Le service Infrastructure Backup prend en charge le transfert des données de sauvegarde vers un emplacement de stockage externe avec le chiffrement SMB activé côté serveur. Si le serveur ne prend pas en charge le chiffrement SMB ou si la fonctionnalité n’est pas activée, le service Infrastructure Backup revient au transfert de données non chiffré. Les données de sauvegarde placées dans l’emplacement de stockage externe sont toujours chiffrées au repos et ne dépendent pas du chiffrement SMB.

#### <a name="storage-location-sizing"></a>Dimensionnement de l’emplacement de stockage

Il est recommandé de sauvegarder au moins deux fois par jour et de conserver sept jours de sauvegarde au maximum. Il s’agit du comportement par défaut lorsque vous activez les sauvegardes d’infrastructure sur Azure Stack Hub.

**1907 et au-delà**

***Système connecté au fournisseur d’identité Azure AD***

| Échelle de l’environnement | Taille prévue de la sauvegarde | Quantité totale d’espace nécessaire |
|-------------------|--------------------------|--------------------------------|
| 4-16 nœuds/ASDK   | 1 Go                     | 20 Go                          |

***Système connecté au fournisseur d’identité Azure AD via ADFS***

| Échelle de l’environnement | Taille prévue de la sauvegarde | Quantité totale d’espace nécessaire |
|-------------------|--------------------------|--------------------------------|
| 4-16 nœuds        | 20 Go                    | 280 Go                        |
| ASDK              | 10 Go                    | 140 Go                        |

**Avant 1907**

| Échelle de l’environnement | Taille prévue de la sauvegarde | Quantité totale d’espace nécessaire |
|-------------------|--------------------------|--------------------------------|
| 4-16 nœuds        | 20 Go                    | 280 Go                        |
| ASDK              | 10 Go                    | 140 Go                        |

### <a name="network-requirements"></a>Configuration requise pour le réseau

| Emplacement de stockage                                                                 | Détails                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Partage de fichiers SMB hébergé sur un dispositif de stockage dans l’environnement réseau approuvé | Le port 445 est obligatoire si l’instance Azure Stack Hub réside dans un environnement protégé par un pare-feu. Infrastructure Backup Controller se connecte au serveur de fichiers SMB sur le port 445. |
| Pour être utilisé, le nom de domaine complet du serveur de fichiers doit pouvoir être résolu à partir du PEP.             |                                                                                                                                                                                         |

#### <a name="firewall-rules"></a>Règles de pare-feu
Veillez à configurer des règles de pare-feu pour autoriser la connectivité entre les machines virtuelles ERCS dans l’emplacement de stockage externe. 

| Source | Cible | Protocole/Port |
|------------------|-----------------------|--------------------------------|
| ERCS VM 1        | Emplacement de stockage      | 445/SMB                        |
| ERCS VM 2        | Emplacement de stockage      | 445/SMB                        |
| ERCS VM 3        | Emplacement de stockage      | 445/SMB                        |

> [!Note]  
> Il n’est pas nécessaire d’ouvrir des ports d’entrée.

### <a name="encryption-requirements"></a>Exigences en matière de chiffrement

À partir de la version 1901, le service Infrastructure Backup utilise un certificat avec une clé publique (.CER) pour chiffrer les données de sauvegarde, et un certificat avec une clé privée (.PFX) pour déchiffrer les données de sauvegarde lors de la récupération cloud.

 - Le certificat est utilisé pour le transport des clés, et non pour établir une communication sécurisée authentifiée. Pour cette raison, le certificat peut être auto-signé. Azure Stack Hub n’ayant pas besoin de vérifier la racine ou l’approbation de ce certificat, aucun accès Internet externe n’est requis.

Le certificat auto-signé comprend deux parties, l’une avec la clé publique et l’autre avec la clé privée :

 - Chiffrer les données de sauvegarde : le certificat avec la clé publique (exporté vers un fichier .CER) est utilisé pour chiffrer les données de sauvegarde.
 - Déchiffrer les données de sauvegarde : le certificat avec la clé privée (exporté vers un fichier .PFX) est utilisé pour déchiffrer les données de sauvegarde.

Le certificat avec la clé publique (.CER) n’est pas géré par la rotation des secrets internes. Pour effectuer la rotation du certificat, vous devez créer un certificat auto-signé et mettre à jour les paramètres de sauvegarde avec le nouveau fichier (.CER). 
 
 - Toutes les sauvegardes existantes restent chiffrées à l’aide de la clé publique précédente. Les nouvelles sauvegardes utilisent la nouvelle clé publique.
 
Pour des raisons de sécurité, Azure Stack Hub ne conserve pas le certificat utilisé lors de la récupération du cloud avec la clé privée (.PFX). Ce fichier devra être fourni explicitement lors de la récupération du cloud.  

**Mode de compatibilité descendante** À partir de la version 1901, le prise en charge de clés de chiffrement est déconseillée et sera supprimée dans la version ultérieure. Si vous avez effectué une mise à jour de la version 1811 avec la sauvegarde déjà activée à l’aide d’une clé de chiffrement, Azure Stack Hub continue à utiliser la clé de chiffrement. Le mode de compatibilité descendante est pris en charge pour au moins trois versions. Un certificat sera ensuite requis.

 * La mise à jour de la clé de chiffrement au certificat est une opération unidirectionnelle.  
 * Toutes les sauvegardes existantes restent chiffrées avec la clé de chiffrement. Les nouvelles sauvegardes utiliseront la nouvelle clé publique. 

## <a name="infrastructure-backup-limits"></a>Limites d’Infrastructure Backup

Tenez compte de ces limites quand vous planifiez, déployez et utilisez vos instances Microsoft Azure Stack Hub. Le tableau suivant décrit ces limites.

### <a name="infrastructure-backup-limits"></a>Limites d’Infrastructure Backup

| Identificateur de la limite                                                 | Limite        | Commentaires                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Type de sauvegarde                                                      | Complète uniquement    | Infrastructure Backup Controller prend en charge seulement les sauvegardes complètes. Les sauvegardes incrémentielles ne sont pas prises en charge.                                          |
| Sauvegardes planifiées                                                | Planifiées et manuelles  | Le contrôleur de sauvegarde prend en charge les sauvegardes planifiées et à la demande.                                                                                 |
| Nombre maximal de tâches de sauvegarde simultanées                                   | 1            | Une seule tâche de sauvegarde active par instance de Backup Controller est prise en charge.                                                                  |
| Configuration du commutateur réseau                                     | Non compris | L’administrateur doit sauvegarder la configuration du commutateur réseau à l’aide des outils OEM. Reportez-vous à la documentation de chaque fournisseur OEM pour Azure Stack Hub. |
| Hardware Lifecycle Host (HLH)                                          | Non compris | L’administrateur doit sauvegarder le HLH à l’aide des outils OEM. Reportez-vous à la documentation de chaque fournisseur OEM pour Azure Stack Hub.      |
| Nombre maximal de partages de fichiers                                    | 1            | Vous ne pouvez utiliser qu’un seul partage de fichiers pour stocker les données de sauvegarde.                                                                                        |
| Sauvegarde des données du fournisseur de ressources App Services, Function, SQL, MySql | Non compris | Reportez-vous à l’aide publiée pour le déploiement et la gestion des fournisseurs de ressources à valeur ajoutée créés par Microsoft.                                                  |
| Fournisseurs de ressources de sauvegarde tiers                              | Non compris | Reportez-vous à l’aide publiée pour le déploiement et la gestion des fournisseurs de ressources à valeur ajoutée créés par des fournisseurs tiers.                                          |

## <a name="next-steps"></a>Étapes suivantes

 - Pour plus d’informations sur le service Infrastructure Backup, voir [Sauvegarde et récupération de données pour Azure Stack Hub avec le service Infrastructure Backup](azure-stack-backup-infrastructure-backup.md).
