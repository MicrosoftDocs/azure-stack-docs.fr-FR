---
title: Meilleures pratiques concernant le service de sauvegarde de l’infrastructure pour Azure Stack | Microsoft Docs
description: Vous pouvez suivre l’ensemble des meilleures pratiques lorsque vous déployez et gérez Azure Stack dans votre centre de données afin de limiter la perte de données en cas de défaillance catastrophique.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 221FDE40-3EF8-4F54-A075-0C4D66EECE1A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: d2f62fd7a3a6253a077ad4cce2030b5165dc76cd
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618345"
---
# <a name="infrastructure-backup-service-best-practices"></a>Meilleures pratiques concernant le service de sauvegarde de l’infrastructure

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez suivre les meilleures pratiques lorsque vous déployez et gérez Azure Stack dans votre centre de données afin de limiter la perte de données dans le cas d’une défaillance catastrophique.

Examinez les meilleures pratiques à intervalles réguliers pour vérifier que votre installation est toujours conforme lorsque des modifications sont apportées au flux des opérations. Si vous rencontrez des problèmes lors de l’implémentation de ces meilleures pratiques, contactez le Support Microsoft pour obtenir de l’aide.

## <a name="configuration-best-practices"></a>Bonnes pratiques de configuration

### <a name="deployment"></a>Déploiement

Activer la sauvegarde de l’infrastructure après le déploiement de chaque cloud Azure Stack. À l’aide de Azure Stack PowerShell, vous pouvez planifier des sauvegardes à partir de n’importe quel client/serveur avec accès au point de terminaison de l’API de gestion d’opérateur.

### <a name="networking"></a>Mise en réseau

La chaîne UNC (convention d’affectation des noms) du chemin d’accès doit utiliser un nom de domaine complet (FQDN). L’adresse IP est possible si la résolution de noms est impossible. Une chaîne UNC spécifie l’emplacement de ressources telles que des appareils ou des fichiers partagés.

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

 - Les travaux de sauvegarde s’exécutent pendant l’exécution du système, les expériences de gestion et les applications utilisateur ne subissent donc aucun temps d’arrêt. Les travaux de sauvegarde prennent généralement entre 20 et 40 minutes pour une solution sous charge raisonnable.
 - Suivant les instructions fournies par le fabricant d’ordinateurs (OEM), sauvegardez manuellement les commutateurs réseau ; il est recommandé de stocker l’hôte de cycle de vie du matériel (HLH) sur le partage de sauvegarde dans lequel le contrôleur Infrastructure Backup conserve les données de sauvegarde du plan de contrôle. Envisagez de stocker les configurations HLH et de commutateur dans le dossier de la région. Si vous avez plusieurs instances Azure Stack dans la même région, envisagez d’utiliser un identificateur pour chaque configuration qui appartient à une unité d’échelle.

### <a name="folder-names"></a>Noms de dossier

 - L’infrastructure crée le dossier MASBACKUP automatiquement. Il s’agit d’un partage géré par Microsoft. Vous pouvez créer des partages au même niveau que MASBACKUP. Il est déconseillé de créer des dossiers ou des données de stockage dans MASBACKUP qu’Azure Stack n’a pas créé. 
 -  Le nom de domaine complet de l’utilisateur et la région compris dans votre nom de dossier permettent de différencier les données de sauvegarde de plusieurs clouds. Le nom de domaine complet (FQDN) de votre déploiement d’Azure Stack et des points de terminaison est la combinaison du paramètre de la région et du paramètre du nom de domaine externe. Pour plus d’informations, consultez [Intégration au centre de données Azure Stack - DNS](azure-stack-integrate-dns.md).

Par exemple, le partage de sauvegarde est AzSBackups hébergé sur fileserver01.contoso.com. Dans ce partage de fichiers, il peut y avoir un dossier par déploiement Azure Stack qui utilise le nom de domaine externe et un sous-dossier qui utilise le nom de région. 

FQDN : contoso.com  
Région : nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

Le dossier MASBackup se trouve à l’emplacement où Azure Stack stocke ses données de sauvegarde. Vous ne devriez pas utiliser ce dossier pour y stocker vos propres données. Les fabricants d’ordinateurs (OEM) ne devraient pas non plus utiliser ce dossier pour stocker des données de sauvegarde. 

Les fabricants d’ordinateurs (OEM) sont encouragés à stocker les données de sauvegarde de leurs composants sous le dossier de la région. Chaque commutateur réseau, hôte de cycle de vie du matériel (HLH) et ainsi de suite, peut être stocké dans son propre sous-dossier. Par exemple : 

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>Surveillance

Les alertes suivantes sont prises en charge par le système :

| Alerte                                                   | Description                                                                                     | Correction                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| La sauvegarde a échoué, car le partage de fichiers se trouve hors capacité | Le partage de fichiers est hors capacité et le contrôleur de sauvegarde ne peut pas exporter les fichiers de sauvegarde vers l’emplacement. | Augmentez la capacité de stockage et essayez à nouveau de sauvegarder. Supprimez des sauvegardes existantes (en commençant par les plus anciennes) pour libérer de l’espace.                    |
| La sauvegarde a échoué en raison de problèmes de connectivité.             | Le réseau entre Azure Stack et le partage de fichiers rencontre des problèmes.                          | Résolvez le problème de réseau, puis réessayez la sauvegarde.                                                                                            |
| La sauvegarde a échoué en raison d’une erreur dans le chemin d’accès                | Le chemin d’accès au partage de fichier ne peut pas être résolu                                                          | Mappez le partage à partir d’un autre ordinateur pour vérifier que le partage est accessible. Vous devrez peut-être mettre à jour le chemin d’accès, s’il n’est plus valide.       |
| La sauvegarde a échoué en raison de problèmes d’authentification               | Il peut y avoir un problème avec les informations d’identification ou un problème de réseau qui a un impact sur l’authentification.    | Mappez le partage à partir d’un autre ordinateur pour vérifier que le partage est accessible. Vous devrez peut-être mettre à jour les informations d’identification si elles ne sont plus valides. |
| La sauvegarde a échoué en raison d’une défaillance générale                    | L’échec de la requête peut être dû à un problème occasionnel. Essayez de sauvegarder à nouveau.                    | Appelez le support technique                                                                                                                               |

## <a name="next-steps"></a>Étapes suivantes

Passer en revue la documentation de référence pour le [service Infrastructure Backup](azure-stack-backup-reference.md)

Activer le [service Infrastructure Backup](azure-stack-backup-enable-backup-console.md)
