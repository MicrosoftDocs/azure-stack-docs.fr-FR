---
title: Résoudre les problèmes de rapport de validation de cluster
description: Résoudre les problèmes de rapport de validation de cluster et valider la configuration des paramètres QoS pour les clusters HCI Azure Stack
author: khdownie
ms.topic: troubleshooting
ms.date: 07/21/2020
ms.author: v-kedow
ms.reviewer: JasonGerend
ms.openlocfilehash: c4da92a6d88a3d2046ee6136f2481ac23e5bd476
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86867971"
---
# <a name="troubleshoot-cluster-validation-reporting"></a>Résoudre les problèmes de rapport de validation de cluster

> S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Cette rubrique vous aide à résoudre les problèmes de rapports de validation de cluster pour les paramètres QoS (qualité de service) de réseau et de stockage sur les serveurs d’un cluster Azure Stack HCI, et à vérifier que les règles importantes sont définies. Pour des performances et une connectivité optimales, le processus de validation de cluster vérifie que la configuration QoS de Data Center Bridging (DCB) est cohérente et, si elles sont définies, qu’elle contient des règles appropriées pour les classes de trafic du clustering de basculement et de SMB/SMB Direct.

## <a name="install-data-center-bridging"></a>Installer Data Center Bridging

Data Center Bridging doit être installé pour utiliser des applets de commande propres à QoS. Pour vérifier si la fonctionnalité Data Center Bridging est déjà installée sur un serveur, exécutez l’applet de commande suivante dans PowerShell :

```PowerShell
Get-WindowsFeature -Name Data-Center-Bridging -ComputerName Server1
```

Si elle n’est pas installée, installez Data Center Bridging en exécutant l’applet de commande suivante sur chaque serveur du cluster :

```PowerShell
Install-WindowsFeature –Name Data-Center-Bridging -ComputerName Server1
```

## <a name="run-a-cluster-validation-test"></a>Exécuter un test de validation du cluster

Utilisez la fonctionnalité Valider dans Windows Admin Center en sélectionnant **Outils > Serveurs > Inventaire > Valider le cluster**, ou exécutez la commande PowerShell suivante :

```PowerShell
Test-Cluster –Node Server1, Server2
```

Entre autres choses, le test vérifie que la configuration QoS de DCB est cohérente, et que tous les serveurs du cluster comptent le même nombre de classes de trafic et de règles QoS. Il vérifie également que tous les serveurs disposent de règles QoS définies pour les classes de trafic du clustering de basculement et de SMB/SMB Direct.

Vous pouvez afficher le rapport de validation dans Windows Admin Center, ou en accédant à un fichier journal dans le répertoire de travail en cours. Par exemple : C:\Users\<username>\AppData\Local\Temp\

En bas du rapport, s’affichent « Validate QoS Settings Configuration (Validation de la configuration des paramètres QoS) » avec un rapport correspondant pour chaque serveur du cluster.

Pour comprendre quelles classes de trafic sont déjà définies sur un serveur, utilisez l’applet de commande `Get-NetQosTrafficClass`.

Pour plus d’informations, consultez [Valider un cluster Azure Stack HCI](../deploy/validate.md).

## <a name="validate-networking-qos-rules"></a>Valider les règles QoS de réseau

Validez la cohérence des paramètres de l’état d’assentiment à DCB et de l’état du contrôle de flux prioritaire DCB entre les serveurs du cluster.

### <a name="dcb-willing-status"></a>État d’assentiment à DCB

Les cartes réseau qui prennent en charge le protocole DCBX (Data Center Bridging Capacity Exchange Protocol) peuvent accepter des configurations provenant d’un appareil distant. Pour activer cette fonctionnalité, le bit d’assentiment à DCB sur la carte réseau doit être défini sur true. Si le bit d’assentiment est défini sur false, l’appareil rejettera toutes les tentatives de configuration des appareils distants, et appliquera uniquement les configurations locales. Si vous utilisez des adaptateurs RoCE (RDMA over Converged Ethernet), la valeur du bit d’assentiment doit être false sur tous les serveurs.

Pour tous les serveurs d’un cluster Azure Stack HCI, le bit d’assentiment à DCB doit être défini de la même façon.

Utilisez l’applet de commande `Set-NetQosDcbxSetting` pour définir le bit d’assentiment à DCB sur true ou false, comme dans l’exemple suivant :

```PowerShell
Set-NetQosDcbxSetting –Willing $false
```

### <a name="dcb-flow-control-status"></a>État du contrôle de flux DCB

Le contrôle de flux basé sur les priorités est essentiel si le protocole de couche supérieure, tel que Fiber Channel, adopte un transport sous-jacent sans perte. Le contrôle de flux DCB peut être activé ou désactivé globalement, ou individuellement pour des cartes réseau. S’il est activé, il permet de créer des stratégies QoS qui hiérarchisent le trafic de certaines applications.

Pour que les stratégies de qualité de service fonctionnent sans interruption pendant le basculement, tous les serveurs d’un cluster Azure Stack HCI doivent avoir les mêmes paramètres d’état de contrôle de flux. Si vous utilisez des adaptateurs RoCE, le contrôle de flux prioritaire doit être activé sur tous les serveurs.

Utilisez l’applet de commande `Get-NetQosFlowControl` pour récupérer la configuration actuelle du contrôle de flux. Toutes les priorités sont désactivées par défaut.

Utilisez les applets de commande `Enable-NetQosFlowControl` et `Disable-NetQosFlowControl` avec le paramètre -priority pour activer ou désactiver le contrôle de flux prioritaire. Par exemple, la commande suivante active le contrôle de flux sur le trafic étiqueté avec la priorité 3 :

```PowerShell
Enable-NetQosFlowControl –Priority 3
```

## <a name="validate-storage-qos-rules"></a>Valider les règles QoS de stockage

Vérifiez que tous les nœuds sont dotés d’une règle QoS pour le clustering de basculement, et pour SMB ou SMB Direct. Dans le cas contraire, des problèmes de connectivité et de performances peuvent se produire.

### <a name="qos-rule-for-failover-clustering"></a>Règle QoS pour le clustering de basculement

Si **une** règle de qualité de service de stockage est définie dans un cluster, une règle QoS pour le clustering de basculement doit être présente, sinon des problèmes de connectivité peuvent s’ensuivre. Si vous voulez ajouter une nouvelle règle QoS pour le clustering de basculement, utilisez l’applet de commande `New-NetQosPolicy`, comme dans l’exemple suivant :

```PowerShell
New-NetQosPolicy "Cluster" -IPDstPort 3343 -Priority 6
```

### <a name="qos-rule-for-smb"></a>Règle QoS pour SMB

Si la totalité ou une partie des nœuds sont dotés de règles QoS définies, mais qu’ils ne disposent pas de règle QoS pour SMB, des problèmes de connectivité et de performances pour SMB peuvent en résulter. Si vous voulez ajouter une nouvelle règle QoS de réseau pour SMB, utilisez l’applet de commande `New-NetQosPolicy`, comme dans l’exemple suivant :

```PowerShell
New-NetQosPolicy -Name "SMB" -SMB -PriorityValue8021Action 3
```

### <a name="qos-rule-for-smb-direct"></a>Règle QoS pour SMB Direct

SMB Direct ignore la pile de mise en réseau plutôt que d’utiliser des méthodes RDMA pour transférer les données. Si la totalité ou une partie des nœuds sont dotés de règles QoS définies, mais qu’ils ne disposent pas de règle QoS pour SMB Direct, des problèmes de connectivité et de performances pour SMB Direct peuvent en résulter. Si vous voulez créer une stratégie QoS pour SMB Direct, exécutez les commandes suivantes :

```PowerShell
New-NetQosPolicy "SMB Direct" –NetDirectPort 445 –Priority 3
```

## <a name="next-steps"></a>Étapes suivantes

Pour consulter des informations connexes, reportez-vous également à :

- [Data Center Bridging](/windows-server/networking/technologies/dcb/dcb-top)
- [Gérer Data Center Bridging](/windows-server/networking/technologies/dcb/dcb-manage)
- [Configurations QoS courantes](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj735302(v=ws.11))