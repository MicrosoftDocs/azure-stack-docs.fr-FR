---
title: Résolution des problèmes AKS
description: Cet article fournit des informations sur la résolution des problèmes liés à Azure Kubernetes Service (AKS) sur Azure Stack HCI.
author: davannaw-msft
ms.topic: how-to
ms.date: 12/02/2020
ms.author: dawhite
ms.openlocfilehash: 53ee79628f63d4925cdf7c725d1c0ec4231b4ef3
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612452"
---
# <a name="troubleshooting-azure-kubernetes-service-on-azure-stack-hci"></a>Résolution des problèmes d’Azure Kubernetes Service sur Azure Stack HCI

Lorsque vous créez et gérez un cluster Kubernetes avec Azure Kubernetes Service sur Azure Stack HCI, vous risquez parfois de rencontrer des problèmes. Cet article fournit des consignes à suivre pour résoudre ces problèmes.

## <a name="troubleshooting-azure-stack-hci"></a>Résolution des problèmes d’Azure Stack HCI
Pour résoudre les problèmes de rapport de validation de cluster pour les paramètres QoS (qualité de service) de réseau et de stockage sur les serveurs d’un cluster Azure Stack HCI et vérifier que les règles importantes sont définies, consultez [Résoudre les problèmes de rapport de validation de cluster](../hci/manage/validate-qos.md).

Pour en savoir plus sur la résolution des problèmes liés à CredSSP, consultez [Résoudre les problèmes CredSSP](../hci/manage/troubleshoot-credssp.md).

## <a name="troubleshooting-windows-admin-center"></a>Résolution des problèmes de Windows Admin Center
Ce produit est en préversion publique, ce qui signifie qu’il est encore en développement. Il existe actuellement certains problèmes avec l’extension Azure Kubernetes Service de Windows Admin Center : 
* Actuellement, chaque serveur du cluster du système que vous utilisez pour configurer Azure Kubernetes Service sur Azure Stack HCI doit être un serveur approuvé. Ainsi, Windows Admin Center doit être en mesure d’exécuter des opérations CredSSP sur chaque serveur du cluster, et pas seulement sur un ou plusieurs d’entre eux. 
* Si vous obtenez une erreur stipulant `msft.sme.aks couldn't load`, et que l’erreur indique que le chargement de blocs a échoué, utilisez la version la plus récente de Microsoft Edge ou de Google Chrome, puis réessayez.
* Avant de démarrer l’Assistant de configuration d’un hôte Azure Kubernetes Service ou l’Assistant de création d’un cluster Kubernetes, vous devez vous connecter à Azure via Windows Admin Center. Vous devrez peut-être vous reconnecter au cours du workflow. Si vous rencontrez des difficultés lors de la connexion à Azure via Windows Admin Center, essayez de vous connecter à votre compte Azure à partir d’une autre source, comme le [portail Azure](https://portal.azure.com/). Si les problèmes continuent, consultez l’article [Problèmes connus de Windows Admin Center](/windows-server/manage/windows-admin-center/support/known-issues) avant de contacter le support technique.
* Dans l’itération actuelle du déploiement Azure Kubernetes Service sur Azure Stack HCI via Windows Admin Center, seul l’utilisateur qui a configuré l’hôte d’Azure Kubernetes Service peut créer des clusters Kubernetes sur le système. Pour contourner ce problème, copiez le dossier .wssd du profil de l’utilisateur qui a configuré l’hôte Azure Kubernetes Service dans le profil de l’utilisateur qui créera le nouveau cluster Kubernetes.
* Si vous recevez une erreur de configuration incorrecte dans l’un des Assistants, effectuez des opérations de nettoyage du cluster. Ces opérations peuvent impliquer la suppression du fichier C:\Program Files\AksHci\mocctl.exe.
* Pour que CredSSP fonctionne correctement dans l’Assistant de création de cluster, Windows Admin Center doit être installé et utilisé par le même compte. Si vous installez Windows Admin Center avec un compte et tentez de l’utiliser avec un autre, vous obtiendrez des erreurs.
* Pendant le déploiement du cluster, vous pouvez rencontrer un problème avec le transfert du fichier helm.zip. Ce problème cause souvent une erreur qui indique que le chemin du fichier helm.zip n’existe pas ou n’est pas valide. Pour résoudre ce problème, réessayez le déploiement.
* Si votre déploiement se bloque pendant une période prolongée, vous rencontrez peut-être des problèmes de connectivité ou liés à CredSSP. Suivez la procédure ci-dessous pour essayer de dépanner votre déploiement : 
    1.  Sur l’ordinateur exécutant Windows Admin Center, exécutez la commande suivante dans une fenêtre PowerShell : 
          ```PowerShell
          Enter-PSSession <servername>
          ```
    2.  Si cette commande réussit, vous pouvez vous connecter au serveur et il n’y a pas de problème de connectivité.
    
    3.  Si vous rencontrez des problèmes avec CredSSP, exécutez cette commande pour tester l’approbation entre l’ordinateur passerelle et l’ordinateur cible : 
          ```PowerShell
          Enter-PSSession –ComputerName <server> –Credential company\administrator –Authentication CredSSP
          ``` 
        Vous pouvez également exécuter la commande suivante pour tester l’approbation d’accès à la passerelle locale : 
          ```PowerShell
          Enter-PSSession -computer localhost -credential (Get-Credential)
          ``` 
* Si vous utilisez Azure Arc et que vous avez plusieurs ID de locataire, exécutez la commande suivante pour spécifier le locataire souhaité avant le déploiement. Dans le cas contraire, votre déploiement risque d’échouer.

   ```Azure CLI
   az login –tenant <tenant>
   ```
* Si vous venez de créer un nouveau compte Azure et que vous ne vous êtes pas connecté au compte sur votre ordinateur passerelle, vous pouvez rencontrer des problèmes lors de l’inscription de votre passerelle Windows Admin Center auprès d’Azure. Pour résoudre ce problème, connectez-vous à votre compte Azure dans un autre onglet ou une autre fenêtre de navigateur, puis inscrivez la passerelle Windows Admin Center auprès d’Azure.

### <a name="creating-windows-admin-center-logs"></a>Création de journaux Windows Admin Center
Lorsque vous signalez des problèmes avec Windows Admin Center, il est judicieux de joindre des journaux pour aider l’équipe de développement à diagnostiquer votre problème. Les erreurs dans Windows Admin Center se présentent généralement sous l’une des deux formes suivantes : 
- Événements affichés dans l’observateur d’événements sur l’ordinateur exécutant Windows Admin Center 
- Problèmes JavaScript qui apparaissent dans la console du navigateur 

Pour collecter les journaux pour Windows Admin Center, utilisez le script Get-SMEUILogs.ps1 fourni dans le package de préversion publique. 
 
Pour utiliser ce script, exécutez la commande suivante dans le dossier où votre script est stocké : 
 
```PowerShell
./Get-SMEUILogs.ps1 -ComputerNames [comp1, comp2, etc.] -Destination [comp3] -HoursAgo [48] -NoCredentialPrompt
```
 
La commande comprend les paramètres suivants :
 
* `-ComputerNames` : Liste des ordinateurs à partir desquels vous souhaitez collecter les journaux.
* `-Destination` : Ordinateur sur lequel vous voulez agréger les journaux.
* `-HoursAgo` : Heure de début de la collecte des journaux, exprimée en heures précédant l’exécution du script.
* `-NoCredentialPrompt` : Commutateur permettant de désactiver l’invite de saisie d’informations d’identification et permettant d’utiliser les informations d’identification par défaut dans votre environnement actuel.
 
Si vous avez des difficultés à exécuter ce script, vous pouvez exécuter la commande suivante pour afficher le texte d’aide : 
 
```PowerShell
GetHelp .\Get-SMEUILogs.ps1 -Examples
```

## <a name="troubleshooting-windows-worker-nodes"></a>Résolution des problèmes des nœuds Worker Windows 
Pour vous connecter à un nœud Worker Windows à l’aide de SSH, obtenez tout d’abord l’adresse IP du nœud en exécutant `kubectl get` et en capturant la valeur `EXTERNAL-IP`.

   > [!NOTE]
   > Vous devez passer l’emplacement approprié à votre clé privée SSH. L’exemple suivant utilise l’emplacement par défaut %systemdrive%\akshci\.ssh\akshci_rsa. Toutefois, vous devrez peut-être changer cet emplacement si vous avez demandé un autre chemin en spécifiant le paramètre `-sshPublicKey` pour `Set-AksHciConfig`.

Pour obtenir l’adresse IP du nœud Worker Windows :  

```
kubectl --kubeconfig=yourkubeconfig get nodes -o wide
```  

Utilisez `ssh Administrator@ip` pour vous connecter via SSH à un nœud Windows :  

```
ssh -i $env:SYSTEMDRIVE\AksHci\.ssh\akshci_rsa administrator@<IP Address of the Node>
```
  
Une fois connecté en SSH au nœud, vous pouvez exécuter `net user administrator *` pour mettre à jour votre mot de passe administrateur. 


## <a name="troubleshooting-linux-worker-nodes"></a>Résolution des problèmes des nœuds Worker Linux 
Pour vous connecter à un nœud Worker Linux à l’aide de SSH, obtenez tout d’abord l’adresse IP du nœud en exécutant `kubectl get`, puis capturez la valeur `EXTERNAL-IP`.


   > [!NOTE]
   > Vous devez passer l’emplacement approprié à votre clé privée SSH. L’exemple suivant utilise l’emplacement par défaut %systemdrive%\akshci\.ssh\akshci_rsa. Toutefois, vous devrez peut-être changer cet emplacement si vous avez demandé un autre chemin en spécifiant le paramètre `-sshPublicKey` pour `Set-AksHciConfig`.

Pour obtenir l’adresse IP du nœud Worker Linux :  

```
kubectl --kubeconfig=yourkubeconfig get nodes -o wide
```  

Utilisez `ssh clouduser@ip` pour vous connecter via SSH au nœud Linux : 

```
ssh -i $env:SYSTEMDRIVE\AksHci\.ssh\akshci_rsa clouduser@<IP Address of the Node>
```  

Une fois connecté en SSH au nœud, vous pouvez exécuter `net user administrator *` pour mettre à jour votre mot de passe administrateur. 

## <a name="troubleshooting-azure-arc-kubernetes"></a>Résolution de problèmes liés à Azure Arc Kubernetes
Pour découvrir comment dépanner certains scénarios courants liés à la connectivité, aux autorisations et aux agents Arc, consultez [Résolution des problèmes Kubernetes avec Azure Arc](/azure/azure-arc/kubernetes/troubleshooting).

## <a name="next-steps"></a>Étapes suivantes
Si vous continuez à rencontrer des problèmes quand vous utilisez Azure Kubernetes Service sur Azure Stack HCI, vous pouvez consigner les bogues via [GitHub](https://aka.ms/aks-hci-issues).
