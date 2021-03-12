---
title: Chiffrer les secrets etcd dans AKS sur Azure Stack HCI
description: Apprendre à chiffrer les secrets etcd dans AKS sur Azure Stack HCI
author: aabhathipsay
ms.topic: how-to
ms.date: 02/02/2021
ms.author: aabha
ms.reviewer: ''
ms.openlocfilehash: 82d40c2f97171196b90171e3dda850990bf2fbab
ms.sourcegitcommit: 2c6418ee465e67edd417961b1f5211b2e09dbd5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102116714"
---
# <a name="encrypt-etcd-secrets-on-aks-on-azure-stack-hci-clusters"></a>Chiffrer les secrets etcd dans les clusters AKS sur Azure Stack HCI

Un secret dans Kubernetes est un objet qui contient une petite quantité de données sensibles, telles que des mots de passe et des clés SSH. Dans le serveur d’API Kubernetes, les secrets sont stockés dans _etcd_, un magasin de valeurs de clés à haut niveau de disponibilité qui est utilisé comme magasin de stockage Kubernetes pour toutes les données de cluster. Lorsque vous activez le chiffrement des secrets etcd, vous pouvez [chiffrer les données secrètes au repos](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/) et automatiser également la gestion ainsi que la rotation des clés de chiffrement. 

> [!NOTE]
> Dans cette préversion, le chiffrement des secrets etcd est disponible uniquement pour les nouveaux clusters de charge de travail. 

## <a name="enable-encryption-of-etcd-secrets"></a>Activer le chiffrement des secrets etcd

Utilisez le paramètre `-enableSecretsEncryption` de la commande [New-AksHciCluster](./new-akshcicluster.md) pour activer le chiffrement des secrets etcd et automatiser la rotation des clés de chiffrement, comme indiqué ci-dessous : 

```powershell
New-AksHciCluster -name mynewcluster -enableSecretsEncryption
```

Dès lors que vous avez déployé un nouveau cluster avec le paramètre `-enableSecretsEncryption`, vous ne pouvez pas désactiver cette fonctionnalité.

## <a name="monitor-and-troubleshoot"></a>Surveiller et dépanner

Pour simplifier le déploiement d’applications sur des clusters Kubernetes, consultez la [documentation et les scripts](https://github.com/microsoft/AKS-HCI-Apps) disponibles.

- Si vous souhaitez configurer la journalisation à l’aide d’Elasticsearch, de Fluent Bit et de Kibana, suivez les étapes pour [installer les outils et configurer la journalisation](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Logging)
- Si vous souhaitez utiliser l’outil de supervision Prometheus, suivez les étapes pour [installer Prometheus dans un cluster Kubernetes](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring#certs-and-keys-monitoring)

> [!NOTE]
> Vous pouvez trouver les journaux sur le nœud de plan de contrôle sous `/var/log/pods`.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Chiffrement des données secrètes au repos](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data)
- [Utilisation d’un fournisseur KMS pour le chiffrement des données](https://kubernetes.io/docs/tasks/administer-cluster/kms-provider/)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide pratique, vous avez appris à activer le chiffrement des secrets etcd pour les nouveaux clusters de charge de travail. À présent, vous pouvez :
- [Déployer une application Linux sur un cluster Kubernetes](./deploy-linux-application.md).
- [Déployer une application Windows Server sur un cluster Kubernetes](./deploy-windows-application.md).
