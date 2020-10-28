---
title: Azure Stack - Contrôles d’intégrité dans le stockage étendu pour le stockage Blob Modular Data Center
description: Cet article fournit une aide relative à l’exécution de contrôles d’intégrité dans le stockage étendu pour le stockage Blob Modular Data Center.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2020
ms.author: justinha
ms.reviewer: karlt
ms.lastreviewed: 10/01/2020
ms.openlocfilehash: 9d8e7915cb5c1ca9d0a073c750f67a5688157e74
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183389"
---
# <a name="extended-storage-health-checks"></a>Contrôles d’intégrité du stockage étendu

Cet article fournit des conseils pour contrôler l’intégrité du matériel de stockage étendu du centre de données et le déploiement d’Azure Stack Hub.

Avant de commencer, consultez et suivez les étapes décrites dans [Mise à jour du microprogramme pour le stockage étendu](extended-storage-firmware-updates.md).

La dernière étape avant l’expédition du système consiste à l’arrêter. Suivez les étapes du Guide de démarrage rapide.

## <a name="extended-storage-health"></a>Intégrité du stockage étendu

Cette section fournit des conseils sur le contrôle d’intégrité du matériel de stockage étendu.
Vérifiez les événements et avertissez l’administrateur s’il y a des problèmes à résoudre. 


Pour contrôler l’intégrité du pool de stockage du cluster, exécutez la commande suivante :
```console
isi storagepool health
```

Par exemple, si le pool est sain, les résultats de la commande ressemblent à ceci :
```console
All pools are healthy.
Unprovisioned drives: none
```

En cas de défaillance ou de lecteurs manquants, les résultats de la commande ressemblent à ceci :

```console
SmartPools Health
Name                  Health  Type Prot   Members          Down          Smartfailed
--------------------- ------- ---- ------ ---------------- ------------- -------------
a2000_200tb_800gb-    -M---
ssd-sed_16gb
 a2000_200tb_800gb-   -M---   HDD  3x     3,11,14,19,23,30 Nodes:        Nodes:
ssd-sed_16gb:24                           ,38,41,46:bay6,1 Drives:       Drives:
                                          0-11,16,19,
                                          25:bay6,10-11,16

OK = Ok, U = Too few nodes, M = Missing drives,
D = Some nodes or drives are down, S = Some nodes or drives are smartfailed,
R = Some nodes or drives need repair
Unprovisioned drives: none
```

Pour résoudre tout problème, contactez le Support Microsoft.

La commande suivante contrôle l’intégrité globale du cluster, dans un affichage simplifié :
```console
isi status
```

Si tout est correct, un **OK** s’affiche en vert, sinon des avertissements jaunes ou des erreurs rouges apparaissent soit sur la ligne **Intégrité du cluster** , soit sur une ou plusieurs des lignes ID du nœud de cluster dans le tableau suivant.

Si vous avez besoin d’informations supplémentaires sur l’intégrité du cluster, une commande plus détaillée peut être exécutée, dans un affichage développé :
```console
isi status -a
```

Pour résoudre tout problème, contactez le Support Microsoft.

## <a name="azure-stack-hub-health"></a>Intégrité d’Azure Stack Hub

Cette section fournit une aide relative au contrôle d’intégrité du déploiement Azure Stack Hub.

Pour obtenir une vue d’ensemble du déploiement Azure Stack intégré au système de stockage étendu, exécutez le script suivant, qui est un wrapper pour les éléments suivants :
- Connexion à une machine virtuelle ERCS à l’aide des informations d’identification fournies
- Exécution de la commande de débogage Test-AzureStack (qui génère des informations d’intégrité détaillées directement sous la forme d’une sortie à l’écran)

Les composants requis pour ce script sont les suivants :
- Ficher de script .\Invoke-ExtendedStorageConfiguration.ps1, trouvé dans le dossier C:\OEMSoftware\ExtendedStorage\ de l’Hôte du cycle de vie matériel
- Variable d’informations d’identification $AzScred, qui doit être remplie avec les informations d’identification *DOMAIN* \cloudadmin. Remplacez *DOMAIN* par le nom de domaine réel, par exemple CONTOSO.


```powershell
$AzScred = Get-Credential -Credential 'DOMAIN\cloudadmin'
.\Invoke-ExtendedStorageConfiguration.ps1 -TestAzureStack -AzureStackCred $AzScred
```

Consultez le résultat et vérifiez l’état d’intégrité global du déploiement Azure Stack, ainsi que les sections **NAS** spécifiques des résultats de l’état d’intégrité spécifique de l’intégration du stockage étendu avec Azure Stack.

Pour plus d’informations sur le diagnostics Azure Stack, consultez [Valider l’état du système Azure Stack Hub](../operator/azure-stack-diagnostic-test.md).

## <a name="technical-support"></a>Support technique

Pour résoudre tout problème, contactez le Support Microsoft.

## <a name="next-steps"></a>Étapes suivantes

- [Mise à jour du microprogramme](extended-storage-firmware-updates.md)
