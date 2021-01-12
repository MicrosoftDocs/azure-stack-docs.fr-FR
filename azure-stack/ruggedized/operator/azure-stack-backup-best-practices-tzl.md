---
title: Meilleures pratiques concernant le service de sauvegarde de l’infrastructure pour Azure Stack | Microsoft Docs
description: Adoptez ces bonnes pratiques pour déployer et gérer Azure Stack afin de réduire la perte de données en cas d’une panne catastrophique.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/11/2019
ms.openlocfilehash: d54381c185b51b880d452ebf303581c068072f9a
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97909835"
---
# <a name="infrastructure-backup-service-best-practices---ruggedized"></a>Meilleures pratiques concernant le service Infrastructure Backup – Renforcé

*S’applique à : Modular Data Center, Azure Stack Hub renforcé*

Examinez régulièrement ces meilleures pratiques pour vérifier que votre installation est toujours conforme lorsque des modifications sont apportées au flux des opérations. Si vous rencontrez des problèmes lors de l'implémentation de ces meilleures pratiques, contactez le Support Microsoft pour obtenir de l'aide.

## <a name="configuration-best-practices"></a>Bonnes pratiques de configuration

La sauvegarde de l'infrastructure est activée par défaut lors du déploiement d'un nouveau système, et celle-ci est stockée en interne. À l'aide du portail Azure Stack ou de PowerShell, vous pouvez fournir un emplacement de stockage externe afin d'exporter les sauvegardes vers un emplacement secondaire.

### <a name="networking"></a>Mise en réseau

La chaîne UNC (convention d’affectation des noms) du chemin d’accès doit utiliser un nom de domaine complet (FQDN). L'adresse IP peut être utilisée si la résolution de noms s'avère impossible. Une chaîne UNC spécifie l’emplacement de ressources telles que des appareils ou des fichiers partagés.

### <a name="encryption"></a>Chiffrement

Le certificat de chiffrement est utilisé pour chiffrer les données de sauvegarde exportées vers le stockage externe. Il peut s'agir d'un certificat auto-signé dans la mesure où il est uniquement utilisé pour transporter les clés. Pour plus d’informations sur la création d’un certificat, consultez New-SelfSignedCertificate.

Le certificat doit être stocké dans un emplacement sécurisé. Le format CER du certificat permet uniquement de chiffrer les données, et non d'établir la communication.

## <a name="operational-best-practices"></a>Bonnes pratiques opérationnelles

### <a name="backups"></a>Sauvegardes

- Les travaux de sauvegarde s'exécutent pendant l'exécution du système ; les expériences de gestion et les applications utilisateur ne subissent donc aucun temps d'arrêt. Les travaux de sauvegarde prennent généralement 20 à 40 minutes pour une solution sous charge raisonnable.

- Des instructions supplémentaires sont fournies pour sauvegarder manuellement les commutateurs réseau et l'hôte de cycle de vie du matériel (HLH).

### <a name="folder-names"></a>Noms de dossier

- L’infrastructure crée le dossier MASBACKUP automatiquement. Il s’agit d’un partage géré par Microsoft. Vous pouvez créer des partages au même niveau que MASBACKUP. Dans MASBACKUP, il est déconseillé de créer des dossiers ou des données de stockage qu'Azure Stack n'a pas créé.

- Le nom de domaine complet de l’utilisateur et la région compris dans votre nom de dossier permettent de différencier les données de sauvegarde de plusieurs clouds. Le nom de domaine complet (FQDN) de votre déploiement Azure Stack et des points de terminaison est la combinaison du paramètre de la région et du paramètre du nom de domaine externe. Pour plus d’informations, consultez [Intégration au centre de données Azure Stack - DNS](../../operator/azure-stack-integrate-dns.md).

Par exemple, le partage de sauvegarde est AzSBackups hébergé sur fileserver01.contoso.com. Dans ce partage de fichiers, il peut y avoir un dossier par déploiement Azure Stack qui utilise le nom de domaine externe et un sous-dossier qui utilise le nom de région.

- FQDN : contoso.com
- Région : nyc

```shell
\\fileserver01.contoso.com\AzSBackups
\\fileserver01.contoso.com\AzSBackups\contoso.com
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup
```

Le dossier `MASBackup` se trouve à l'emplacement où Azure Stack stocke ses données de sauvegarde. N’utilisez pas ce dossier pour y stocker vos propres données. Les fabricants d'ordinateurs (OEM) ne doivent pas non plus utiliser ce dossier pour stocker des données de sauvegarde.

Les fabricants d’ordinateurs (OEM) sont encouragés à stocker les données de sauvegarde de leurs composants sous le dossier de la région. Chaque commutateur réseau, hôte de cycle de vie du matériel (HLH) et ainsi de suite, peuvent être stockés dans leur propre sous-dossier. Par exemple :

```shell
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration
```

### <a name="monitoring"></a>Surveillance

Les alertes suivantes sont prises en charge par le système :

| Alerte                                                   | Description                                                                                     | Correction                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| La sauvegarde a échoué, car le partage de fichiers se trouve hors capacité. | Le partage de fichiers est hors capacité et le contrôleur de sauvegarde ne peut pas exporter les fichiers de sauvegarde vers l’emplacement. | Augmentez la capacité de stockage et essayez à nouveau de sauvegarder. Supprimez des sauvegardes existantes (en commençant par les plus anciennes) pour libérer de l’espace.                    |
| La sauvegarde a échoué en raison de problèmes de connectivité.             | Le réseau entre Azure Stack et le partage de fichiers rencontre des problèmes.                          | Résolvez le problème de réseau, puis réessayez la sauvegarde.                                                                                            |
| La sauvegarde a échoué en raison d’une erreur dans le chemin.                | Le chemin du partage de fichier ne peut pas être résolu.                                                          | Mappez le partage à partir d’un autre ordinateur pour vérifier que le partage est accessible. Vous devrez peut-être mettre à jour le chemin s’il n’est plus valide.       |
| La sauvegarde a échoué en raison de problèmes d’authentification.               | Il peut y avoir un problème avec les informations d’identification ou un problème de réseau qui a un impact sur l’authentification.    | Mappez le partage à partir d’un autre ordinateur pour vérifier que le partage est accessible. Vous devrez peut-être mettre à jour les informations d’identification si elles ne sont plus valides. |
| La sauvegarde a échoué en raison d’une défaillance générale.                    | L’échec de la requête peut être dû à un problème occasionnel. Réessayez d’effectuer une sauvegarde.                    | Appelez le support technique.                                                                                                                               |

### <a name="infrastructure-backup-service-components"></a>Composants du service Infrastructure Backup

Le service Infrastructure Backup comprend les composants suivants :

- **Contrôleur Infrastructure Backup** : le contrôleur Infrastructure Backup est instancié avec chaque cloud Azure Stack et y réside.

- **Fournisseur de ressources de sauvegarde** : le fournisseur de ressources de sauvegarde se compose de l'interface utilisateur et des API qui comprennent les fonctionnalités de sauvegarde de base pour l'infrastructure Azure Stack.

### <a name="infrastructure-backup-controller"></a>Infrastructure Backup Controller

Infrastructure Backup Controller est un service de Service Fabric qui est instancié pour un cloud Azure Stack. Les ressources de sauvegarde sont créées au niveau régional et capturent les données de service propres à la région d'AD, CA, Azure Resource Manager, CRP, SRP, NRP, Key Vault et RBAC.

### <a name="backup-resource-provider"></a>Fournisseur de ressources de sauvegarde

Le fournisseur de ressources de sauvegarde fournit une interface utilisateur dans le portail Azure Stack, et donne accès à la configuration de base ainsi qu’à la liste des ressources de sauvegarde. Les opérateurs peuvent effectuer les actions suivantes dans l’interface utilisateur :

- Activer la sauvegarde pour la première fois en fournissant l’emplacement de stockage externe, les informations d’identification et la clé de chiffrement
- Consulter les ressources de sauvegarde déjà créées et l’état des ressources en cours de création
- Changer l’emplacement de stockage où Backup Controller place les données de sauvegarde
- Changer les informations d’identification utilisées par Backup Controller pour accéder à l’emplacement de stockage externe
- Changer le certificat de chiffrement utilisé par Backup Controller pour chiffrer les sauvegardes

## <a name="backup-controller-requirements"></a>Configuration requise pour Backup Controller

Cette section décrit la configuration requise pour le service Infrastructure Backup. Nous vous recommandons d'examiner attentivement les informations suivantes avant d'activer la sauvegarde de votre instance d'Azure Stack, puis de vous y référer si nécessaire pendant le déploiement et les opérations ultérieures.

La configuration requise inclut :

- **Configuration logicielle requise** : décrit les emplacements de stockage pris en charge et contient une aide pour le dimensionnement.
- **Configuration réseau requise** : décrit la configuration réseau requise pour différents emplacements de stockage.

### <a name="software-requirements"></a>Configuration logicielle requise

#### <a name="supported-storage-locations"></a>Emplacements de stockage pris en charge

| Emplacement de stockage                                                                  | Détails                                                                                                                                                  |
|-----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Partage de fichiers SMB hébergé sur un dispositif de stockage dans l’environnement réseau approuvé | Partage SMB dans le même centre de données que celui où Azure Stack est déployé ou dans un autre centre de données. Plusieurs instances Azure Stack peuvent utiliser le même partage de fichiers. |
| Partage de fichiers SMB sur Azure                                                          | Non pris en charge actuellement.                                                                                                                                 |
| Stockage d’objets blob sur Azure                                                            | Non pris en charge actuellement.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Versions de SMB prises en charge

|SMB  |Version  |
|---------|---------|
|SMB     |   3.x      |

#### <a name="smb-encryption"></a>Chiffrement SMB

Le service Infrastructure Backup prend en charge le transfert des données de sauvegarde vers un emplacement de stockage externe avec le chiffrement SMB activé côté serveur. Si le serveur ne prend pas en charge le chiffrement SMB ou si la fonctionnalité n'est pas activée, le service Infrastructure Backup revient au transfert de données non chiffré. Les données de sauvegarde placées sur l'emplacement de stockage externe sont toujours chiffrées au repos et ne dépendent pas du chiffrement SMB.

#### <a name="storage-location-sizing"></a>Dimensionnement de l’emplacement de stockage

Nous vous recommandons d'effectuer des sauvegardes au moins deux fois par jour, et de conserver au maximum sept jours de sauvegardes. Il s’agit du comportement par défaut lorsque vous activez les sauvegardes d’infrastructure sur Azure Stack.


|Échelle de l’environnement  |Taille prévue de la sauvegarde  |Quantité totale d’espace nécessaire  |
|---------|---------|---------|
|4-16 nœuds     |  20 Go       |  280 Go       |
<!-- TZLASDKFIX 
|ASDK     |   10 GB      |   140 GB     |
-->
### <a name="network-requirements"></a>Configuration requise pour le réseau

|Emplacement de stockage  |Détails  |
|---------|---------|
|Partage de fichiers SMB hébergé sur un dispositif de stockage dans l’environnement réseau approuvé     |  Le port 445 est obligatoire si l’instance Azure Stack se trouve dans un environnement de pare-feu. Infrastructure Backup Controller se connecte au serveur de fichiers SMB sur le port 445.       |
| Pour être utilisé, le nom de domaine complet du serveur de fichiers doit pouvoir être résolu à partir du PEP.     |         |

> [!NOTE]
> Il n’est pas nécessaire d’ouvrir des ports d’entrée.

### <a name="encryption-requirements"></a>Exigences en matière de chiffrement

Le service Infrastructure Backup utilise un certificat doté d'une clé publique (.CER) pour chiffrer les données de sauvegarde. Le certificat est utilisé pour le transport des clés, pas pour établir une communication sécurisée authentifiée. Pour cette raison, le certificat peut être auto-signé. Azure Stack n'ayant pas besoin vérifier la racine ou la confiance pour ce certificat, aucun accès externe à Internet n'est requis.

Le certificat auto-signé comprend deux parties, l’une avec la clé publique et l’autre avec la clé privée :

- Chiffrer les données de sauvegarde : le certificat avec la clé publique (exporté vers un fichier .CER) est utilisé pour chiffrer les données de sauvegarde.
- Déchiffrer les données de sauvegarde : le certificat avec la clé privée (exporté vers un fichier .PFX) est utilisé pour déchiffrer les données de sauvegarde.

Le certificat avec la clé publique (. CER) n’est pas géré par la rotation des secrets interne. Pour procéder à une rotation du certificat, vous devez créer un certificat auto-signé et mettre à jour les paramètres de sauvegarde avec le nouveau fichier (.CER).

Toutes les sauvegardes existantes restent chiffrées à l’aide de la clé publique précédente. Les nouvelles sauvegardes utilisent la nouvelle clé publique.

Pour des raisons de sécurité, le certificat utilisé lors de la récupération cloud avec la clé privée (. PFX) n'est pas conservé par Azure Stack.

## <a name="infrastructure-backup-limits"></a>Limites d’Infrastructure Backup

Tenez compte de ces limites quand vous planifiez, déployez et utilisez vos instances Microsoft Azure Stack. Le tableau suivant décrit ces limites.

|Identificateur de la limite  |Limite  |Commentaires  |
|---------|---------|---------|
|Type de sauvegarde     | Complète uniquement        | Infrastructure Backup Controller prend en charge seulement les sauvegardes complètes. Les sauvegardes incrémentielles ne sont pas prises en charge.        |
|Sauvegardes planifiées     | Planifiées et manuelles        | Le contrôleur de sauvegarde prend en charge les sauvegardes planifiées et à la demande.        |
|Nombre maximal de tâches de sauvegarde simultanées      | 1        | Une seule tâche de sauvegarde active par instance de Backup Controller est prise en charge.        |
|Configuration du commutateur réseau     | Non compris         | L’administrateur doit sauvegarder la configuration du commutateur réseau à l’aide des outils OEM. Reportez-vous à la documentation pour Azure Stack de chaque fournisseur OEM.        |
|Hardware Lifecycle Host (HLH)     | Non compris         | L’administrateur doit sauvegarder le HLH à l’aide des outils OEM. Reportez-vous à la documentation pour Azure Stack de chaque fournisseur OEM.        |
|Nombre maximal de partages de fichiers     | 1        | Vous ne pouvez utiliser qu’un seul partage de fichiers pour stocker les données de sauvegarde.        |
|Fournisseurs de ressources de sauvegarde à valeur ajoutée     | Dans l'étendue        | La sauvegarde de l'infrastructure comprend la sauvegarde des fournisseurs de ressources Event Hubs, IoT Hub et Data Box Edge.        |

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [service Infrastructure Backup](../../operator/azure-stack-backup-reference.md).
