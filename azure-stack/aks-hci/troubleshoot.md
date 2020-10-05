---
title: Dépannage
description: Guide de résolution des problèmes d’Azure Kubernetes Service sur Azure Stack HCI
author: davannaw-msft
ms.topic: how-to
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: e30d5ba784efc6453ce161bc2a87db7c728d3fce
ms.sourcegitcommit: 373e9e3e84eaa33331db9f78e52486fbb6beb907
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91592926"
---
# <a name="troubleshooting-azure-kubernetes-service-on-azure-stack-hci"></a>Résolution des problèmes d’Azure Kubernetes Service sur Azure Stack HCI

Lorsque vous créez ou gérez un cluster Kubernetes à l’aide d’Azure Kubernetes Service sur Azure Stack HCI, vous pouvez occasionnellement rencontrer des problèmes. Vous trouverez ci-dessous les instructions de résolution des problèmes pour vous aider à résoudre ces problèmes. 

## <a name="troubleshooting-azure-stack-hci"></a>Résolution des problèmes d’Azure Stack HCI
Pour résoudre les problèmes de rapports de validation de cluster pour les paramètres QoS (qualité de service) de réseau et de stockage sur les serveurs d’un cluster Azure Stack HCI et vérifier que les règles importantes sont définies, consultez [Résoudre les problèmes de rapport de validation de cluster](/azure-stack/hci/manage/validate-qos).

Pour résoudre les problèmes liés à CredSSP, consultez [Résoudre les problèmes résoudre les problèmes de CredSSP](/azure-stack/hci/manage/troubleshoot-credssp).

## <a name="troubleshooting-windows-admin-center"></a>Résolution des problèmes de Windows Admin Center
Ce produit est actuellement en préversion publique, ce qui signifie qu’il est toujours en cours de développement. Il existe actuellement plusieurs problèmes liés à l’extension Azure Kubernetes Service de Windows Admin Center : 
* Actuellement, chaque serveur du cluster du système que vous utilisez pour configurer Azure Kubernetes Service sur Azure Stack HCI doit être un serveur approuvé. Cela signifie que Windows Admin Center doit être en mesure d’effectuer des opérations CredSSP sur chaque serveur du cluster, et pas seulement sur un ou plusieurs d’entre eux. 
* Si vous rencontrez une erreur indiquant `msft.sme.aks couldn't load`, et que l’erreur indique que le chargement de blocs a échoué, utilisez la version la plus récente de Microsoft Edge ou de Google Chrome, puis réessayez.
* Avant de démarrer l’Assistant de configuration de l’hôte d’Azure Kubernetes Service ou l’Assistant de création d’un cluster Kubernetes, vous devez vous connecter à Azure via Windows Admin Center. Une nouvelle signature peut être nécessaire pendant le flux de travail. Si vous rencontrez des difficultés lors de la connexion à Azure via Windows Admin Center, essayez de vous connecter à votre compte Azure à partir d’une autre source, comme le [Portail Azure](https://portal.azure.com/). Si vous continuez à rencontrer des problèmes, consultez l’article [Problèmes connus de Windows Admin Center](/windows-server/manage/windows-admin-center/support/known-issues) avant de contacter le support.
* Dans l’itération actuelle du déploiement Azure Kubernetes Service sur Azure Stack HCI via Windows Admin Center, seul l’utilisateur qui a configuré l’hôte d’Azure Kubernetes Service peut créer des clusters Kubernetes sur le système. Pour contourner ce problème, copiez le dossier `.wssd` à partir du profil utilisateur qui configure l’hôte d’Azure Kubernetes Service sur le profil utilisateur qui lancera le nouveau cluster Kubernetes.
* Si vous recevez une erreur de configuration incorrecte dans l’un des Assistants, effectuez des opérations de nettoyage du cluster. Cela peut impliquer la suppression du fichier `C:\Program Files\AksHci\mocctl.exe`.
* Pour que CredSSP fonctionne correctement dans l’Assistant de création de cluster, Windows Admin Center doit être installé et utilisé par le même compte. L’installation avec un compte puis la tentative d’utilisation avec un autre compte entraînera des erreurs.
* Pendant le déploiement du cluster, un problème lié au transfert du fichier helm.zip peut se produire. Cela entraîne souvent une erreur indiquant que le chemin d’accès au fichier helm.zip n’existe pas ou n’est pas valide. Pour résoudre ce problème, revenez en arrière et recommencez le déploiement.
* Si votre déploiement se bloque pendant une période prolongée, il se peut que vous rencontriez des problèmes liés à CredSSP ou de connectivité. Essayez de suivre les étapes suivantes pour résoudre votre déploiement : 
    1.  Sur l’ordinateur exécutant WAC, exécutez la commande suivante dans une fenêtre PowerShell : 
    ```PowerShell
    Enter-PSSession <servername>
    ```
    2.  Si cette commande réussit, vous pouvez vous connecter au serveur et il n’y a pas de problème de connectivité.
    
    3.  Si vous rencontrez des problèmes liés à CredSSP, exécutez cette commande pour tester l’approbation entre l’ordinateur de la passerelle et l’ordinateur cible : 
    ```PowerShell
    Enter-PSSession –ComputerName <server> –Credential company\administrator –Authentication CredSSP
    ``` 
    Vous pouvez également exécuter la commande suivante pour tester l’approbation d’accès à la passerelle locale : 
    ```PowerShell
    Enter-PSSession -computer localhost -credential (Get-Credential)
    ``` 
* Si vous utilisez Azure Arc et que vous avez plusieurs ID de locataire, exécutez la commande suivante pour spécifier le locataire souhaité avant le déploiement. Si vous ne le faites pas, un échec de déploiement peut se produire.

```Azure CLI
az login –tenant <tenant>
```
* Si vous venez de créer un nouveau compte Azure et que vous ne vous êtes pas connecté au compte sur l’ordinateur de votre passerelle, vous pouvez rencontrer des problèmes d’inscription de votre passerelle WAC auprès d’Azure. Pour résoudre ce problème, connectez-vous à votre compte Azure dans un autre onglet ou une autre fenêtre de navigateur, puis inscrivez la passerelle WAC auprès d’Azure.

### <a name="creating-windows-admin-center-logs"></a>Création de journaux Windows Admin Center
Lorsque vous signalez des problèmes dans Windows Admin Center, il est utile de joindre les journaux pour que l’équipe de développement puisse diagnostiquer votre problème. Les erreurs dans Windows Admin Center se présentent généralement sous deux formes : des événements qui s’affichent dans la visionneuse d’événements sur l’ordinateur exécutant Windows Admin Center ou des problèmes JavaScript qui se produisent dans la console du navigateur. Pour collecter les journaux de Windows Admin Center, utilisez le script `Get-SMEUILogs.ps1` fourni dans le package de préversion publique. 
 
Pour utiliser le script, exécutez la commande suivante dans le répertoire de fichiers dans lequel votre script est stocké : 
 
```PowerShell
./Get-SMEUILogs.ps1 -ComputerNames [comp1, comp2, etc.] -Destination [comp3] -HoursAgo [48] -NoCredentialPrompt
```
 
La commande comprend les paramètres suivants :
 
* **-ComputerNames** : Liste des ordinateurs à partir desquels vous souhaitez collecter les journaux.
* **-Destination** : Ordinateur sur lequel vous souhaitez agréger les journaux.
* **-HoursAgo** : Ce paramètre définit le nombre d’heures à partir du runtime du script duquel vous souhaitez collecter les journaux.
* **-NoCredentialPrompt** : Il s’agit d’un commutateur qui permet de désactiver l’invite d’informations d’identification et d’utiliser les informations d’identification par défaut dans votre environnement actuel.
 
Si vous rencontrez des difficultés pour exécuter ce script, vous pouvez exécuter la commande suivante pour afficher le texte d’aide : 
 
```PowerShell
GetHelp .\Get-SMEUILogs.ps1 -Examples
```

## <a name="troubleshooting-windows-worker-nodes"></a>Résolution des problèmes des nœuds Worker Windows 
Pour vous connecter à un nœud Worker Windows, obtenez tout d’abord l’adresse IP de votre nœud en exécutant `kubectl get` et notez la valeur `EXTERNAL-IP` :

```PowerShell
kubectl get nodes -o wide
``` 
Utilisez SSH dans le nœud à l’aide de `ssh Administrator@ip`. Lorsque SSH est utilisé dans le nœud, vous pouvez exécuter `net user administrator *` pour mettre à jour votre mot de passe administrateur. 

## <a name="troubleshooting-linux-worker-nodes"></a>Résolution des problèmes des nœuds Worker Linux 
Pour vous connecter à un nœud Worker Linux, obtenez tout d’abord l’adresse IP de votre nœud en exécutant `kubectl get` et notez la valeur `EXTERNAL-IP` :

```PowerShell
kubectl get nodes -o wide
``` 
Utilisez SSH dans le nœud à l’aide de `ssh clouduser@ip`. 

## <a name="troubleshooting-azure-arc-for-kubernetes"></a>Résolution des problèmes d’Azure Arc pour Kubernetes
Pour résoudre certains scénarios courants liés à la connectivité, aux autorisations et aux agents Arc, consultez [Résolution des problèmes de Kubernetes avec Azure Arc](/azure/azure-arc/kubernetes/troubleshooting).

## <a name="next-steps"></a>Étapes suivantes
Si vous continuez à rencontrer des problèmes lors de l’utilisation d’Azure Kubernetes Service sur Azure Stack HCI, consignez les bogues via [GitHub](https://aka.ms/aks-hci-issues).  
