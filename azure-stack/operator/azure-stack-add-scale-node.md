---
title: Ajouter des nœuds d’unité d’échelle dans Azure Stack Hub
description: Découvrez comment ajouter des nœuds d’unité d’échelle à des unités d’échelle dans Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: c264e0abc0fdc5a382b83a23158f860a56aea260
ms.sourcegitcommit: a3ae6dd8670f8fb24224880df7eee256ebbcc4ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772594"
---
# <a name="add-additional-scale-unit-nodes-in-azure-stack-hub"></a>Ajouter des nœuds d’unité d’échelle dans Azure Stack Hub

Les opérateurs Azure Stack Hub peuvent augmenter la capacité globale d’une unité d’échelle existante en ajoutant un calculateur physique. Le calculateur physique s’appelle aussi un nœud d’unité d’échelle. Chaque nouveau nœud d’unité d’échelle que vous ajoutez doit être homogène en termes de type de CPU, de mémoire, de numéro de disque et de taille avec les nœuds déjà présents dans l’unité d’échelle.

Pour ajouter un nœud d’unité d’échelle, vous devez intervenir dans Azure Stack Hub et exécuter les outils de votre fabricant de matériel (OEM). Les outils du fabricant OEM s’exécutent sur l’hôte du cycle de vie matériel (HLH) pour s’assurer que le nouveau calculateur physique correspond au même niveau de microprogramme que les nœuds existants.

Le diagramme de flux suivant montre le processus général pour ajouter un nœud d’unité d’échelle :

![Ajouter un flux d’unités d’échelle](media/azure-stack-add-scale-node/add-node-flow.svg)
<br> *Votre contrat de support indique si votre fournisseur OEM doit choisir l’emplacement physique du rack du serveur et mettre à jour le microprogramme.*

L’opération d’ajout de nouveau nœud peut prendre plusieurs heures ou plusieurs jours. Il n’y a aucun impact sur les charges de travail en cours d’exécution sur le système lors de l’ajout d’un nœud d’unité d’échelle supplémentaire.

> [!Note]  
> N’essayez aucune des opérations suivantes si une opération d’ajout de nœud d’unité d’échelle est déjà en cours :
>
>  - Mettre à jour Azure Stack Hub
>  - Effectuer une rotation des certificats
>  - Arrêter Azure Stack Hub
>  - Réparer un nœud d’unité d’échelle


## <a name="add-scale-unit-nodes"></a>Ajouter des nœuds d’unités d’échelle

Les étapes suivantes présentent de façon générale la procédure à suivre pour ajouter un nœud. N’effectuez pas ces étapes sans avoir consulté au préalable la documentation concernant les extensions de capacité fournie par le fabricant OEM.

1. Placez le nouveau serveur physique dans le rack et câblez-le de manière appropriée. 
2. Activez les ports du commutateur physique et ajustez les listes de contrôle d’accès (ACL) le cas échéant.
3. Configurez l’adresse IP appropriée dans le contrôleur de gestion de la carte de base (BMC) et appliquez tous les paramètres du BIOS conformément à la documentation fournie par le fabricant OEM.
4. Appliquez la ligne de base actuelle du microprogramme à tous les composants en utilisant les outils fournis par le fabricant du matériel fonctionnant sur l’HLH.
5. Exécutez l’opération d’ajout de nœud dans le portail administrateur Azure Stack Hub.
6. Vérifiez que l’opération d’ajout de nœud a réussi. Pour ce faire, vérifiez l’[**état** de l’unité d’échelle](#monitor-add-node-operations). 

## <a name="add-the-node"></a>Ajouter le nœud

Pour ajouter de nouveaux nœuds, vous pouvez vous servir du portail administrateur ou de PowerShell. L’opération Ajouter un nœud commence par ajouter le nouveau nœud d’unité d’échelle en tant que capacité de calcul disponible, puis augmente automatiquement la capacité de stockage. La capacité augmente automatiquement parce que Azure Stack Hub est un système hyperconvergé dans lequel le *calcul* et le *stockage* se mettent à l’échelle simultanément.

### <a name="use-the-administrator-portal"></a>Utiliser le portail d’administration

1. Connectez-vous au portail administrateur Azure Stack Hub en tant qu’opérateur Azure Stack Hub.
2. Accédez à **+ Créer une ressource** > **Capacité** > **Nœud d’unité d’échelle**.
   ![Nœud d’unité d’échelle](media/azure-stack-add-scale-node/select-node1.png)
3. Dans le volet **Ajouter un nœud**, sélectionnez la *région*, puis l’*unité d’échelle* à laquelle vous souhaitez ajouter le nœud. Spécifiez également l’*adresse BMC* du nœud d’unité d’échelle que vous ajoutez. Vous ne pouvez ajouter qu’un seul nœud à la fois.
   ![Ajouter les détails du nœud](media/azure-stack-add-scale-node/select-node2.png)
 

### <a name="use-powershell"></a>Utiliser PowerShell

Utilisez l’applet de commande **New-AzsScaleUnitNodeObject** pour ajouter un nœud.  

Avant d’utiliser l’un des exemples de scripts PowerShell suivants, remplacez les valeurs de *noms de nœuds* et d’*adresses IP* par des valeurs de votre environnement Azure Stack Hub.

  > [!Note]  
  > Lorsque vous nommez un nœud, vous devez spécifier un nom comportant moins de 15 caractères. Vous ne pouvez pas utiliser un nom qui contient un espace ou les caractères suivants : `\`, `/`, `:`, `*`, `?`, `"`, `<`, `>`, `|`, `\`, `~`, `!`, `@`, `#`, `$`, `%`, `^`, `&`, `(`, `)`, `{`, `}`, `_`.

**Ajouter un nœud :**
  ```powershell
  ## Add a single Node 
  $NewNode=New-AzsScaleUnitNodeObject -computername "<name_of_new_node>" -BMCIPv4Address "<BMCIP_address_of_new_node>" 
 
  Add-AzsScaleUnitNode -NodeList $NewNode -ScaleUnit "<name_of_scale_unit_cluster>" 
  ```  

## <a name="monitor-add-node-operations"></a>Surveiller les opérations Ajouter un nœud 
Utilisez le portail administrateur ou PowerShell pour connaître l’état de l’opération Ajouter un nœud. Les opérations Ajouter un nœud peuvent prendre de plusieurs heures à plusieurs jours.

### <a name="use-the-administrator-portal"></a>Utiliser le portail d’administration 
Pour superviser l’ajout d’un nouveau nœud, vous pouvez examiner l’unité d’échelle ou les objets de nœud d’unité d’échelle dans le portail administrateur. Pour ce faire, accédez à **Gestion des régions** > **Unités d’échelle**. Ensuite, sélectionnez l’unité d’échelle ou le nœud d’unité d’échelle que vous souhaitez examiner. 

### <a name="use-powershell"></a>Utiliser PowerShell
L’état de l’unité d’échelle et des nœuds de l’unité d’échelle peut être récupéré à l’aide de PowerShell comme suit :
  ```powershell
  #Retrieve Status for the Scale Unit
  Get-AzsScaleUnit|select name,state
 
  #Retrieve Status for each Scale Unit Node
  Get-AzsScaleUnitNode |Select Name, ScaleUnitNodeStatus
```

### <a name="status-for-the-add-node-operation"></a>État de l’opération Ajouter un nœud 
**Pour une unité d’échelle :**

|Statut               |Description  |
|---------------------|---------|
|Exécution en cours              |Tous les nœuds participent de façon active à l’unité d’échelle.|
|Arrêté              |Le nœud de l’unité d’échelle est désactivé ou inaccessible.|
|Extension            |Un ou plusieurs nœuds d’unité d’échelle sont actuellement ajoutés en tant que capacité de calcul.|
|Configuration du stockage  |La capacité de calcul a été augmentée et la configuration du stockage est en cours d’exécution.|
|Correction nécessaire |Une erreur nécessitant la réparation d’un ou de plusieurs nœuds de l’unité d’échelle a été détectée.|


**Pour un nœud de l’unité d’échelle :**

|Statut                |Description  |
|----------------------|---------|
|Exécution en cours               |Le nœud participe de façon active à l’unité d’échelle.|
|Arrêté               |Le nœud est indisponible.|
|Ajout                |Le nœud est ajouté de façon active à l’unité d’échelle.|
|Réparation             |Le nœud est réparé de façon active.|
|Maintenance           |Le nœud est suspendu et aucune charge de travail utilisateur active n’est en cours d’exécution. |
|Correction nécessaire  |Une erreur nécessitant la réparation du nœud a été détectée.|


## <a name="troubleshooting"></a>Dépannage
Voici les problèmes couramment rencontrés lors de l’ajout d’un nœud. 

**Scénario 1 :** L’opération d’ajout de nœud d’unité d’échelle échoue, mais un ou plusieurs nœuds sont répertoriés dans la liste avec l’état Arrêté.  
- Correction : Exécutez l’opération de réparation afin de réparer un ou plusieurs nœuds. Il n’est possible d’effectuer qu’une seule opération de réparation à la fois.

**Scénario 2 :** Un ou plusieurs nœuds d’unité d’échelle ont été ajoutés, mais l’extension de stockage a échoué. Dans ce scénario, l’objet du nœud de l’unité d’échelle rapporte un état de fonctionnement, mais la tâche de configuration du stockage n’est pas lancée.  
- Correction : Utilisez le point de terminaison privilégié pour examiner l’intégrité du stockage en exécutant la cmdlet PowerShell suivante :
  ```powershell
     Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
  ```
 
**Scénario 3 :** Vous recevez une alerte indiquant que la tâche d’échelle du stockage a échoué.  
- Correction : Dans ce cas, la tâche de configuration du stockage a échoué. Ce problème nécessite que vous contactiez le support technique.


## <a name="next-steps"></a>Étapes suivantes 
[Ajouter des adresses IP publiques](azure-stack-add-ips.md) 
