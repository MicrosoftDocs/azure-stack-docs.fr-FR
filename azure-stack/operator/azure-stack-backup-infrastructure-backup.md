---
title: Récupérer des données dans Azure Stack Hub avec le service Infrastructure Backup
description: Découvrez comment sauvegarder et restaurer la configuration et les données du service dans Azure Stack Hub en utilisant le service Infrastructure Backup.
author: justinha
ms.topic: article
ms.date: 05/16/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 96d1062b7b6b4c30d1d635965b51de27c0b49269
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703279"
---
# <a name="recover-data-in-azure-stack-hub-with-the-infrastructure-backup-service"></a>Récupérer des données dans Azure Stack Hub avec le service Infrastructure Backup

Vous pouvez sauvegarder et restaurer la configuration et les données du service en utilisant le service Infrastructure Backup dans Azure Stack Hub. Chaque installation Azure Stack Hub contient une instance du service. Vous pouvez utiliser des sauvegardes créées par le service pour le redéploiement du cloud Azure Stack Hub visant à restaurer les données d’identité, de sécurité et d’Azure Resource Manager.

Activez la sauvegarde quand vous êtes prêt à passer votre cloud en production. Ne l’activez pas si vous prévoyez d’effectuer les tests et la validation pendant longtemps.

Avant d’activer votre service de sauvegarde, vérifiez que [la configuration requise est en place](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Le service Infrastructure Backup n’inclut pas les données utilisateur ni les applications. Pour plus d’informations sur la protection des applications basées sur une machine virtuelle IaaS, consultez [Protéger des machines virtuelles déployées sur Azure Stack Hub](../user/azure-stack-manage-vm-protect.md). Pour une parfaite compréhension de la protection des applications sur Azure Stack Hub, consultez le [livre blanc Azure Stack Hub : Considérations relatives à la continuité d’activité et la reprise d’activité](https://aka.ms/azurestackbcdrconsiderationswp).

## <a name="the-infrastructure-backup-service"></a>Le service Infrastructure Backup

Le service contient les fonctionnalités suivantes :

| Fonctionnalité                                            | Description                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Services d’infrastructure de sauvegarde                     | Coordonnez la sauvegarde sur une partie des services d’infrastructure d’Azure Stack Hub. Si un sinistre se produit, les données peuvent être restaurées lors d’un redéploiement. |
| Compression et chiffrement des données de sauvegarde exportées | Les données de sauvegarde sont compressées et chiffrées par le système avant d’être exportées vers l’emplacement de stockage externe fourni par l’administrateur.                |
| Supervision des travaux de sauvegarde                              | Le système vous notifie quand des travaux de sauvegarde échouent et vous indique comment résoudre le problème.                                                                                                |
| Expérience de gestion des sauvegardes                       | Backup RP prend en charge l’activation de la sauvegarde.                                                                                                                         |
| Récupération cloud                                     | Si une perte catastrophique de données se produit, les sauvegardes peuvent être utilisées pour restaurer les informations Azure Stack Hub principales lors d’un déploiement.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Vérifier la configuration requise pour le service Infrastructure Backup

- **Emplacement de stockage**  
  Vous avez besoin d’un partage de fichiers accessible depuis Azure Stack Hub et pouvant contenir sept sauvegardes. Chaque sauvegarde est d’environ 10 Go. Votre partage doit pouvoir stocker 140 Go de sauvegardes. Pour plus d’informations sur la sélection d’un emplacement de stockage pour le service Infrastructure Backup, consultez [Configuration requise pour le contrôleur de sauvegarde](azure-stack-backup-reference.md#backup-controller-requirements).
- **Informations d'identification**  
  Vous avez besoin d’un compte d’utilisateur de domaine et d’informations d’identification. Par exemple, vous pouvez utiliser vos informations d’identification d’administrateur Azure Stack Hub.
- **Certificat de chiffrement**  
  Les fichiers de sauvegarde sont chiffrés avec la clé publique dans le certificat. Pensez à stocker ce certificat à un emplacement sécurisé. 


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [activer la sauvegarde d’Azure Stack Hub à partir du portail administrateur](azure-stack-backup-enable-backup-console.md).

Découvrez comment [activer la sauvegarde d’Azure Stack Hub avec PowerShell](azure-stack-backup-enable-backup-powershell.md).

Découvrez comment [sauvegarder Azure Stack Hub](azure-stack-backup-back-up-azure-stack.md).

Découvrez comment [récupérer des données suite à une perte catastrophique](azure-stack-backup-recover-data.md).
