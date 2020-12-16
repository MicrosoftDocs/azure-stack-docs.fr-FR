---
title: Préparer des nœuds Windows pour la prise en charge de compte de service administré de groupe
description: Découvrez comment configurer des comptes de service administrés de groupe pour des conteneurs sur des nœuds Windows
author: v-susbo
ms.topic: how-to
ms.date: 11/30/2020
ms.author: v-susbo
ms.openlocfilehash: 754ebc1a365efb7efa0e96eef438ae2347a069ab
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612571"
---
# <a name="prepare-windows-nodes-for-group-managed-service-account-support"></a>Préparer des nœuds Windows pour la prise en charge de compte de service administré de groupe

> S’applique à : AKS sur Azure Stack HCI, AKS Runtime sur Windows Server 2019 Datacenter

Les comptes de service administrés de groupe correspondent à un type spécifique de compte Active Directory, qui offre les fonctionnalités suivantes :
- Gestion automatique des mots de passe
- Gestion simplifié du SPN (nom de principal du service)
- Délégation éventuelle de la gestion à d’autres administrateurs sur plusieurs serveurs 

Pour configurer des comptes de service administrés de groupe (gMSA) pour les pods et les conteneurs qui s’exécuteront sur vos nœuds Windows, vous devez tout d’abord joindre vos nœuds Windows à un domaine Active Directory.

Pour permettre la prise en charge des comptes de service administrés de groupe, le nom de votre cluster Kubernetes doit comporter moins de 4 caractères. Cela est dû au fait que la longueur maximale prise en charge pour un nom de serveur joint à un domaine est de 15 caractères. La convention de nommage de cluster Kubernetes AKS sur Azure Stack HCI pour un nœud Worker ajoute quelques caractères prédéfinis à un nom de nœud.

## <a name="join-the-worker-nodes-to-a-domain"></a>Joindre les nœuds Worker à un domaine

Pour joindre vos nœuds Worker Windows à un domaine, connectez-vous à un nœud Worker Windows en exécutant `kubectl get` et en notant la valeur `EXTERNAL-IP`.

```
kubectl get nodes -o wide
```  

Vous pouvez ensuite utiliser SSH dans le nœud à l’aide de `ssh Administrator@ip`. Pour plus d’informations sur la connexion à l’aide de SSH, consultez [Résolution des problèmes de nœuds Worker Windows à l’aide de SSH](troubleshoot.md#troubleshooting-windows-worker-nodes).

Une fois que vous avez réussi à vous connecter à votre nœud Worker Windows via SSH, exécutez la commande suivante pour joindre le nœud à un domaine. Vous devez ensuite redémarrer le nœud Worker Windows. 

```
netdom.exe join %computername% /domain:DomainName /UserD:DomainName\UserName /PasswordD:Password
```  

Une fois que tous les nœuds Worker Windows ont été joints à un domaine, suivez les étapes détaillées dans la [configuration de gMSA](https://kubernetes.io/docs/tasks/configure-pod-container/configure-gmsa/). Ces étapes vous aideront à appliquer les Webhooks et définitions de ressources personnalisés gMSA Kubernetes à votre cluster Kubernetes.

Pour plus d’informations sur les conteneurs Windows avec gMSA, consultez [Conteneurs Windows et gMSA](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts). 

## <a name="next-steps"></a>Étapes suivantes

* [Utilisez Azure Monitor pour surveiller votre cluster et votre application](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters).
* [Utiliser un volume persistant sur un cluster Kubernetes](persistent-volume.md)
