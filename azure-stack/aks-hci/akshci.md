---
title: Module PowerShell AksHci
description: Découvrir comment utiliser les commandes du module AksHci pour gérer AKS sur Azure Stack HCI
author: jessicaguan
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 511b3ad0dcf791550f40136a73f47114e9784bbe
ms.sourcegitcommit: 2c6418ee465e67edd417961b1f5211b2e09dbd5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102116748"
---
# <a name="akshci"></a>AksHci 

Commandes permettant d’interagir avec Azure Kubernetes Service sur Azure Stack HCI.

## <a name="akshci-cmdlets"></a>Applets de commande AksHci

|         |            |
| ------- | ---------- |
| [get-akshcicluster](get-akshcicluster.md) | Lister les clusters déployés, y compris l’hôte Azure Kubernetes Service. |
| [get-akshciclusterupgrades](get-akshciclusterupgrades.md) | Obtenir les mises à niveau disponibles d’un cluster Azure Kubernetes Service. |
| [get-akshciconfig](get-akshciconfig.md) | Lister les paramètres de la configuration actuelle de l’hôte Azure Kubernetes Service. |
| [get-akshcicredential](get-akshcicredential.md) | Accéder à vos clusters à l’aide de kubectl. |
| [get-akshcieventlog](get-akshcieventlog.md) | Obtenir tous les journaux des événements du module PowerShell AKS HCI. |
| [get-akshcikubernetesversion](get-akshcikubernetesversion.md) | Lister la version disponible pour la création du cluster Kubernetes managé. |
| [get-akshcilogs](get-akshcilogs.md) | Créer un dossier compressé avec les journaux de tous vos pods. |
| [get-akshciupdates](get-akshciupdates.md) | Lister les mises à jour disponibles pour Azure Kubernetes Service sur Azure Stack HCI. |
| [get-akshciversion](get-akshciversion.md) | Obtenir la version actuelle d’Azure Kubernetes Service sur Azure Stack HCI. |
| [get-akshcivmsize](get-akshcivmsize.md) | Lister les tailles de machine virtuelle prises en charge. |
| [initialize-akshcinode](initialize-akshcinode.md) | Exécuter des contrôles sur chaque nœud physique pour vérifier que toutes les conditions exigées sont satisfaites avant d’installer Azure Kubernetes Service sur Azure Stack HCI. |
| [install-akshci](install-akshci.md) | Installer l’hôte ainsi que les agents et services Azure Kubernetes Service sur Azure Stack HCI. |
| [install-akshciadauth](install-akshciadauth.md) | Installer l’authentification Active Directory. |
| [install-akshciarconboarding](install-akshciarconboarding.md) | Télécharger et installer kubectl, l’outil en ligne de commande de Kubernetes. |
| [new-akshcicluster](new-akshcicluster.md) | Créer un cluster Kubernetes managé. |
| [new-akshcinetworksetting](new-akshcinetworksetting.md) | Créer un objet pour un nouveau réseau virtuel. |
| [remove-akshcicluster](remove-akshcicluster.md) | Supprimer un cluster Kubernetes managé. |
| [restart-akshci](restart-akshci.md) | Redémarrer Azure Kubernetes Service sur Azure Stack HCI et supprimer tous les clusters Kubernetes déployés. |
| [set-akshciclusternodecount](set-akshciclusternodecount.md) | Mettre à l’échelle le nombre de nœuds de plan de contrôle ou de nœuds Worker dans un cluster. |
| [set-akshciconfig](set-akshciconfig.md) | Définir ou mettre à jour les paramètres de configuration de l’hôte Azure Kubernetes Service. |
| [uninstall-akshci](uninstall-akshci.md) | Supprimer Azure Kubernetes Service sur Azure Stack HCI. |
| [uninstall-akshciadauth](uninstall-akshciadauth.md) | Supprimer l’authentification Active Directory. |
| [update-akshci](update-akshci.md) | Mettre à jour l’hôte Azure Kubernetes Service avec la dernière version de Kubernetes. |
| [update-akshcicluster](update-akshcicluster.md) | Mettre à jour un cluster Kubernetes managé vers une version plus récente de Kubernetes ou du système d’exploitation. |

