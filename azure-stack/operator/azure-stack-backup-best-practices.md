---
title: Bonnes pratiques concernant le service Infrastructure Backup pour Azure Stack | Microsoft Docs
description: Adoptez ces bonnes pratiques pour déployer et gérer Azure Stack afin de réduire la perte de données en cas de défaillance catastrophique.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: 221FDE40-3EF8-4F54-A075-0C4D66EECE1A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 0b843018a90e0a07a1d1135099275288d4a4ce62
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974757"
---
# <a name="infrastructure-backup-service-best-practices"></a>Bonnes pratiques concernant le service Infrastructure Backup

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Adoptez ces bonnes pratiques pour déployer et gérer Azure Stack afin de réduire la perte de données en cas d’une panne catastrophique.

Examinez régulièrement les bonnes pratiques pour vérifier que votre installation reste conforme quand des modifications sont apportées au flux des opérations. Si vous rencontrez des problèmes lors de l’implémentation de ces bonnes pratiques, contactez le Support Microsoft pour obtenir de l’aide.

## <a name="configuration-best-practices"></a>Bonnes pratiques de configuration

### <a name="deployment"></a>Déploiement

Activer la sauvegarde de l’infrastructure après le déploiement de chaque cloud Azure Stack. À l’aide d’Azure Stack PowerShell, vous pouvez planifier des sauvegardes à partir de n’importe quel client/serveur qui a accès au point de terminaison de l’API de gestion d’opérateur.

### <a name="networking"></a>Mise en réseau

La chaîne UNC (convention d’affectation des noms) du chemin d’accès doit utiliser un nom de domaine complet (FQDN). L’adresse IP peut être utilisée si la résolution de noms n’est pas possible. Une chaîne UNC spécifie l’emplacement de ressources telles que des appareils ou des fichiers partagés.

### <a name="encryption"></a>Chiffrement

#### <a name="version-1901-and-newer"></a>Version 1901 ou ultérieure

Le certificat de chiffrement est utilisé pour chiffrer les données de sauvegarde exportées vers le stockage externe. Il peut s'agir d'un certificat auto-signé dans la mesure où il est uniquement utilisé pour transporter les clés. Pour plus d’informations sur la création d’un certificat, consultez New-SelfSignedCertificate.
  
La clé doit être stockée dans un emplacement sécurisé (par exemple, certificat global Azure Key Vault). Le format CER du certificat est utilisé pour chiffrer les données. Le format PFX doit être utilisé au cours du déploiement de récupération cloud d’Azure Stack pour déchiffrer les données de sauvegarde.

![Le certificat est stocké dans un emplacement sécurisé.](media/azure-stack-backup/azure-stack-backup-encryption-store-cert.png)

#### <a name="1811-and-older"></a>Version 1811 ou antérieure

La clé de chiffrement est utilisée pour chiffrer les données de sauvegarde exportées vers le stockage externe. La clé est générée dans le cadre de [l’activation de la sauvegarde pour Azure Stack avec PowerShell](azure-stack-backup-enable-backup-powershell.md).

La clé doit être stockée dans un emplacement sécurisé (par exemple, secret global Azure Key Vault). Cette clé doit être utilisée au cours du redéploiement d’Azure Stack.

![Stockez la clé dans un emplacement sécurisé.](media/azure-stack-backup/azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>Bonnes pratiques opérationnelles

### <a name="backups"></a>Sauvegardes

 - Les travaux de sauvegarde s’exécutent pendant l’exécution du système, les expériences de gestion et les applications utilisateur ne subissent donc aucun temps d’arrêt. Les travaux de sauvegarde prennent généralement entre 20 et 40 minutes pour une solution sous charge raisonnable.
 - Suivant les instructions fournies par le fabricant d’ordinateurs (OEM), les commutateurs réseau sauvegardés manuellement et l’hôte de cycle de vie du matériel (HLH) doivent être stockés sur le partage de sauvegarde dans lequel le contrôleur Infrastructure Backup conserve les données de sauvegarde du plan de contrôle. Envisagez de stocker les configurations HLH et de commutateur dans le dossier de la région. Si vous avez plusieurs instances Azure Stack dans la même région, envisagez d’utiliser un identificateur pour chaque configuration qui appartient à une unité d’échelle.

### <a name="folder-names"></a>Noms de dossier

 - L’infrastructure crée le dossier MASBACKUP automatiquement. Il s’agit d’un partage géré par Microsoft. Vous pouvez créer des partages au même niveau que MASBACKUP. Il est déconseillé de créer des dossiers ou des données de stockage dans MASBACKUP qu’Azure Stack n’a pas créé.
 -  Le nom de domaine complet de l’utilisateur et la région compris dans votre nom de dossier permettent de différencier les données de sauvegarde de plusieurs clouds. Le nom de domaine complet (FQDN) de votre déploiement Azure Stack et des points de terminaison est la combinaison du paramètre de la région et du paramètre du nom de domaine externe. Pour plus d’informations, consultez [Intégration au centre de données Azure Stack - DNS](azure-stack-integrate-dns.md).

Par exemple, le partage de sauvegarde est AzSBackups hébergé sur fileserver01.contoso.com. Dans ce partage de fichiers, il peut y avoir un dossier par déploiement Azure Stack qui utilise le nom de domaine externe et un sous-dossier qui utilise le nom de région.

FQDN : contoso.com  
Région : nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

Le dossier MASBackup se trouve à l’emplacement où Azure Stack stocke ses données de sauvegarde. N’utilisez pas ce dossier pour y stocker vos propres données. Les fabricants d’ordinateurs (OEM) ne devraient pas non plus utiliser ce dossier pour stocker des données de sauvegarde.

Les fabricants d’ordinateurs (OEM) sont encouragés à stocker les données de sauvegarde de leurs composants sous le dossier de la région. Chaque commutateur réseau, hôte de cycle de vie du matériel (HLH) et ainsi de suite, peuvent être stockés dans leur propre sous-dossier. Par exemple :

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>Surveillance

Les alertes suivantes sont prises en charge par le système :

| Alerte                                                   | Description                                                                                     | Correction                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| La sauvegarde a échoué, car le partage de fichiers se trouve hors capacité. | Le partage de fichiers est hors capacité et le contrôleur de sauvegarde ne peut pas exporter les fichiers de sauvegarde vers l’emplacement. | Augmentez la capacité de stockage et essayez à nouveau de sauvegarder. Supprimez des sauvegardes existantes (en commençant par les plus anciennes) pour libérer de l’espace.                    |
| La sauvegarde a échoué en raison de problèmes de connectivité.             | Le réseau entre Azure Stack et le partage de fichiers rencontre des problèmes.                          | Résolvez le problème de réseau, puis réessayez la sauvegarde.                                                                                            |
| La sauvegarde a échoué en raison d’une erreur dans le chemin.                | Le chemin du partage de fichier ne peut pas être résolu.                                                          | Mappez le partage à partir d’un autre ordinateur pour vérifier que le partage est accessible. Vous devrez peut-être mettre à jour le chemin s’il n’est plus valide.       |
| La sauvegarde a échoué en raison de problèmes d’authentification.               | Il peut y avoir un problème avec les informations d’identification ou un problème de réseau qui a un impact sur l’authentification.    | Mappez le partage à partir d’un autre ordinateur pour vérifier que le partage est accessible. Vous devrez peut-être mettre à jour les informations d’identification si elles ne sont plus valides. |
| La sauvegarde a échoué en raison d’une défaillance générale.                    | L’échec de la requête peut être dû à un problème occasionnel. Réessayez d’effectuer une sauvegarde.                    | Appelez le support technique.                                                                                                                               |

## <a name="next-steps"></a>Étapes suivantes

Passez en revue la documentation de référence pour le [service de sauvegarde de l’infrastructure](azure-stack-backup-reference.md).

Activez le [service de sauvegarde de l’infrastructure](azure-stack-backup-enable-backup-console.md).
