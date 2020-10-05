---
title: Problèmes connus liés à Azure Kubernetes Service sur Azure Stack HCI
description: Problèmes connus liés à Azure Kubernetes Service sur Azure Stack HCI
author: abha
ms.topic: troubleshooting
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 21c511521837eff83d31784db3cf59bcfe25cb2f
ms.sourcegitcommit: 373e9e3e84eaa33331db9f78e52486fbb6beb907
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91592818"
---
# <a name="known-issues-for-azure-kubernetes-service-on-azure-stack-hci-public-preview"></a>Problèmes connus liés à Azure Kubernetes Service sur Azure Stack HCI - Préversion publique
Cet article décrit les problèmes connus avec la préversion publique d’Azure Kubernetes Service sur Azure Stack HCI.

## <a name="recovering-from-a-failed-aks-on-azure-stack-hci-deployment"></a>Récupération à partir d’un échec d’AKS sur un déploiement Azure Stack HCI
Si vous rencontrez des problèmes de déploiement ou si vous souhaitez réinitialiser votre déploiement, assurez-vous de fermer toutes les instances de Windows Admin Center connectées à Azure Container Service sur Azure Stack HCI avant d’exécuter Uninstall-AksHci à partir d’une fenêtre d’administration PowerShell.

## <a name="when-using-kubectl-to-delete-a-node-the-associated-vm-might-not-be-deleted"></a>Lorsque vous utilisez kubectl pour supprimer un nœud, la machine virtuelle associée peut ne pas être supprimée
Vous rencontrerez ce problème si vous procédez comme suit :
* Créer un cluster Kubernetes
* Mettre à l’échelle le cluster vers plus de deux nœuds
* Utiliser kubectl delete node <nom_nœud> pour supprimer un nœud 
* Exécuter kubectl get nodes. Le nœud supprimé n’est pas répertorié dans la sortie
* Ouvrir une fenêtre d’administration PowerShell
* Exécuter get-vm. Le nœud supprimé est toujours répertorié

Cela amène le système à ne pas reconnaître que le nœud est manquant et un nouveau nœud ne s’exécutera pas. Ceci sera résolu dans une version ultérieure

## <a name="time-synchronization-must-be-configured-across-all-physical-cluster-nodes-and-in-hyper-v"></a>La synchronisation de l’heure doit être configurée sur tous les nœuds de cluster physique et dans Hyper-V
Pour garantir le bon fonctionnement de l’authentification gMSA et d’AD, assurez-vous que les nœuds de cluster Azure Stack HCI sont configurés pour synchroniser leur heure avec un contrôleur de domaine ou une autre source de temps et que Hyper-V est configuré pour synchroniser l’heure sur les machines virtuelles.

## <a name="special-active-directory-permissions-are-needed-for-domain-joined-azure-stack-hci-nodes"></a>Des autorisations Active Directory spéciales sont nécessaires pour les nœuds Azure Stack HCI joints à un domaine 
Les utilisateurs qui déploient et configurent Azure Container Service sur Azure Stack HCI doivent disposer de l’autorisation « Contrôle total » pour créer des objets AD dans le conteneur Active Directory dans lequel les objets de serveur et de service sont créés. 

## <a name="get-akshcilogs-command-may-fail"></a>La commande Get-AksHciLogs peut échouer
Avec les clusters de grande taille, la commande Get-AksHciLogs peut lever une exception, échouer dans l’énumération des nœuds ou ne pas générer de fichier de sortie c:\wssd\wssdlogs.zip.
Cela est dû au fait que la commande PowerShell pour compresser un fichier `Compress-Archive` a une limite de taille de fichier de sortie de 2 Go. Ce problème sera résolu dans une version ultérieure.

## <a name="azure-kubernetes-service-powershell-deployment-doesnt-check-for-available-memory-before-creating-a-new-target-cluster"></a>Le déploiement PowerShell d’Azure Kubernetes Service ne vérifie pas la mémoire disponible avant de créer un nouveau cluster cible
Les commandes PowerShell Aks-Hci ne valident pas la mémoire disponible sur le serveur hôte avant de créer des nœuds Kubernetes. Cela peut entraîner un épuisement de la mémoire et au non démarrage des machines virtuelles. Cette défaillance n’est actuellement pas gérée correctement et le déploiement cessera de répondre sans message d’erreur explicite.
Si le déploiement cesse de répondre, ouvrez `Eventviewer` et recherchez les messages d’erreur associés à Hyper-V indiquant que la mémoire est insuffisante pour démarrer la machine virtuelle.
Ce problème sera résolu dans une version ultérieure

## <a name="azure-kubernetes-service-deployment-fails-on-an-azure-stack-hci-configured-with-static-ips-vlans-sdn-or-proxies"></a>Le déploiement d’Azure Kubernetes Service échoue sur un Azure Stack HCI configuré avec des adresses IP statiques, des VLAN, des SDN ou des proxies.
Lors du déploiement d’Azure Kubernetes Service sur un cluster Azure Stack HCI qui possède des adresses IP statiques, des VLAN, des SDN ou des proxies, le déploiement échoue lors de la création du cluster. Ce problème sera résolu dans une version ultérieure.

## <a name="ipv6-must-be-disabled-in-the-hosting-environment"></a>IPv6 doit être désactivé dans l’environnement d’hébergement
Si des adresses IPv4 et IPv6 sont liées à la carte réseau physique, le service `cloudagent` pour le clustering utilise l’adresse IPv6 pour la communication. Les autres composants de l’infrastructure de déploiement utilisent uniquement IPv4. Windows Admin Center ne pourra donc pas se connecter au cluster et signalera un échec de communication à distance lors de la tentative de connexion à l’ordinateur.
Solution de contournement : Désactivez IPv6 sur les cartes réseau physiques.
Ce problème sera résolu dans une version ultérieure

## <a name="moving-virtual-machines-between-azure-stack-hci-cluster-nodes-quickly-leads-to-vm-startup-failures"></a>Le déplacement de machines virtuelles entre des nœuds de cluster Azure Stack HCI entraîne rapidement des défaillances de démarrage de machine virtuelle
Lorsque vous utilisez l’outil d’administration de cluster pour déplacer une machine virtuelle d’un nœud (Nœud A) vers un autre nœud (Nœud B) dans le cluster Azure Stack HCI, la machine virtuelle peut ne pas démarrer sur le nouveau nœud. Lorsque vous replacez la machine virtuelle sur le nœud d’origine, elle ne démarre pas non plus.
Ce problème se produit parce que la logique de nettoyage de la première migration s’exécute de façon asynchrone. Par conséquent, la logique de « mise à jour de l’emplacement de la machine virtuelle » d’Azure Kubernetes Service trouve la machine virtuelle sur le Hyper-V d’origine sur le nœud A et la supprime au lieu d’annuler son inscription.
Solution de contournement : Vérifiez que la machine virtuelle a démarré correctement sur le nouveau nœud avant de la replacer sur le nœud d’origine.
Ce problème sera résolu dans une version ultérieure

## <a name="load-balancer-in-azure-kubernetes-service-requires-dhcp-reservation"></a>L’équilibreur de charge dans Azure Kubernetes Service requiert une réservation DHCP
La solution d’équilibrage de charge dans Azure Kubernetes Service sur Azure Stack HCI utilise DHCP pour attribuer des adresses IP aux points de terminaison de service. Si l’adresse IP du point de terminaison de service change en raison d’un redémarrage de service, le bail DHCP expire en raison d’un délai d’expiration réduit. Par conséquent, le service devient inaccessible car l’adresse IP dans la configuration de Kubernetes est différente de celle qui se trouve sur le point de terminaison. Cela peut entraîner l’indisponibilité du cluster Kubernetes.
Pour contourner ce problème, utilisez un pool d’adresses MAC pour les points de terminaison de service à charge équilibrée et réservez des adresses IP spécifiques pour chaque adresse MAC dans le pool.
Ce problème sera résolu dans une version ultérieure.

## <a name="cannot-deploy-azure-kubernetes-service-to-an-environment-that-has-separate-storage-and-compute-clusters"></a>Impossible de déployer Azure Kubernetes Service dans un environnement disposant de clusters de stockage et de calcul distincts
Windows Admin Center ne déploiera pas Azure Kubernetes Service dans un environnement avec des clusters de stockage et de calcul distincts, car il s’attend à ce que les ressources de calcul et de stockage soient fournies par le même cluster. Dans la plupart des cas, il ne trouve pas les CSV exposés par le cluster de calcul et refuse de poursuivre le déploiement.
Ce problème sera résolu dans une version ultérieure.

## <a name="windows-admin-center-only-supports-azure-kubernetes-service-for-azure-stack-hci-in-desktop-mode"></a>Windows Admin Center ne prend en charge Azure Kubernetes Service pour Azure Stack HCI qu’en mode bureau
En préversion, toutes les fonctionnalité d’Azure Kubernetes Service pour Azure Stack HCI sont prises en charge uniquement en mode bureau de Windows Admin Center. La passerelle Windows Admin Center doit être installée sur un PC Windows 10. Pour plus d’informations sur les options d’installation de Windows Admin Center, consultez le [la documentation de Windows Admin Center](https://docs.microsoft.com/windows-server/manage/windows-admin-center/plan/installation-options). Ces scénarios supplémentaires seront pris en charge dans une future version.

## <a name="azure-kubernetes-service-host-setup-fails-in-windows-admin-center-if-reboots-are-required"></a>Le programme d’installation de l’hôte Azure Kubernetes Service échoue dans Windows Admin Center si des redémarrages sont requis
L’Assistant Installation de l’hôte Azure Kubernetes Service échoue si un ou plusieurs serveurs que vous utilisez doivent être redémarrés pour installer des rôles comme PowerShell ou Hyper-V. La solution de contournement actuelle consiste à quitter l’Assistant et à réessayer sur le même système une fois que les serveurs sont de nouveau en ligne. Ce problème sera résolu dans une version ultérieure.

## <a name="azure-registration-step-in-azure-kubernetes-service-host-setup-asks-to-try-again"></a>L’étape d’inscription d’Azure dans le programme d’installation de l’hôte d’Azure Kubernetes Service vous demande de réessayer
Lorsque vous utilisez Windows Admin Center pour configurer l’hôte Azure Kubernetes Service, vous pouvez être invité à réessayer après avoir entré les informations requises sur la page d’inscription d’Azure. Vous devrez peut-être vous reconnecter à Azure sur la passerelle Windows Admin Center pour poursuivre cette étape. Ce problème sera résolu dans une version ultérieure.