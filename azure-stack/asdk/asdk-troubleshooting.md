---
title: Résoudre les problèmes de ASDK | Microsoft Docs
description: Découvrez comment résoudre les problèmes avec le kit de développement Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: ab43d94c2e65032e5e525ec000e38cacb01b2980
ms.sourcegitcommit: 1bae55e754d7be75e03af7a4db3ec43fd7ff3e9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71319104"
---
# <a name="troubleshoot-the-asdk"></a>Résoudre les problèmes de ASDK
Cet article fournit des informations de dépannage courantes pour le Kit de développement Azure Stack (ASDK). Pour obtenir de l’aide sur les systèmes intégrés Azure Stack, consultez [Résolution des problèmes de Microsoft Azure Stack](../operator/azure-stack-troubleshooting.md). 

ASDK étant un environnement d’évaluation, les services de support technique Microsoft ne fournissent pas de support. Si vous rencontrez un problème qui n’est pas documenté, vous pouvez obtenir de l’aide auprès d’experts sur le [Forum MSDN Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 


## <a name="deployment"></a>Déploiement
### <a name="deployment-failure"></a>Échec du déploiement
Si vous rencontrez un problème lors de l’installation, vous pouvez relancer le déploiement à partir de l’étape qui n’a pas abouti en utilisant l’option de réexécution du script de déploiement. Par exemple :

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>À la fin du déploiement, la session PowerShell est toujours ouverte et ne présente aucune sortie
Ce comportement est probablement tout simplement le résultat du comportement par défaut d’une fenêtre de commande PowerShell, lorsqu’elle a été sélectionnée. Le déploiement du kit ASDK s’est déroulé correctement, mais le script a été interrompu au moment de la sélection de la fenêtre. Vous pouvez vérifier que l’installation est terminée en recherchant le mot « select » dans la barre de titre de la fenêtre de commande. Appuyez sur la touche Échap pour la désélectionner ; le message d’achèvement devrait alors s’afficher.

## <a name="virtual-machines"></a>Machines virtuelles
### <a name="default-image-and-gallery-item"></a>Élément de la galerie et image par défaut
Vous devez ajouter un élément de la galerie et une image Windows Server avant de pouvoir déployer des machines virtuelles dans Azure Stack.

### <a name="after-restarting-my-azure-stack-host-some-vms-dont-automatically-start"></a>Après le redémarrage de l’hôte Azure Stack, certaines machines virtuelles ne démarrent pas automatiquement.
Vous remarquerez peut-être que les services Azure Stack ne sont pas immédiatement disponibles après le redémarrage de votre hôte. Ceci est dû au fait que la vérification de la cohérence des points de récupération et des [machines virtuelles d’infrastructure](asdk-architecture.md#virtual-machine-roles) Azure Stack demande un certain temps. Toutefois, ils finiront par démarrer automatiquement.

Vous remarquerez peut-être aussi que les machines virtuelles clientes ne démarrent pas automatiquement après le redémarrage de l’hôte ASDK. Vous pouvez les mettre en ligne en quelques étapes manuelles :

1.  Sur l’hôte ASDK, démarrez **Gestionnaire du cluster de basculement** dans le menu Démarrer.
2.  Sélectionnez le cluster **S-Cluster.azurestack.local**.
3.  Sélectionnez **Rôles**.
4.  Les machines virtuelles clientes apparaissent avec l’état *enregistré*. Lorsque toutes les machines virtuelles d’infrastructure sont en cours d’exécution, cliquez avec le bouton droit sur les machines virtuelles clientes et sélectionnez **Démarrer** pour reprendre la machine virtuelle.

### <a name="ive-deleted-some-vms-but-still-see-the-vhd-files-on-disk"></a>J’ai supprimé des machines virtuelles, mais je vois toujours les fichiers de VHD sur le disque. 
Ce comportement est normal :

* La suppression d’une machine virtuelle n’entraîne pas celle des VHD. Les disques sont des ressources distinctes dans le groupe de ressources.
* Lorsqu’un compte de stockage est supprimé, la suppression est visible immédiatement sur Azure Resource Manager, mais les disques qu’il contient éventuellement restent conservés dans le stockage jusqu’à l’exécution du nettoyage de la mémoire.

Si vous voyez des VHD « orphelins », il est important de savoir s’ils font partie du dossier d’un compte de stockage supprimé. Si le compte de stockage n’a pas été supprimé, il est normal que les VHD soient toujours présents.

Pour en savoir plus sur la configuration du seuil de rétention et de la récupération à la demande, consultez la page [Gérer les comptes de stockage](../operator/azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Stockage
### <a name="storage-reclamation"></a>Récupération du stockage
Il peut s’écouler jusqu’à 14 heures avant que la capacité récupérée ne s’affiche dans le portail. La récupération d’espace dépend de différents facteurs, notamment le pourcentage d’utilisation des fichiers conteneurs internes dans le magasin d’objets blob de blocs. Par conséquent, selon la quantité de données supprimées, il n’y a pas de garantie quant à la quantité d’espace récupérable lors de l’exécution du récupérateur de mémoire.

## <a name="next-steps"></a>Étapes suivantes
[Consulter le forum de support Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)
