---
title: Démarrage rapide en vue de configurer un hôte Azure Kubernetes Service sur Azure Stack HCI à l’aide de Windows PowerShell
description: Découvrez comment configurer un hôte Azure Kubernetes Service sur Azure Stack HCI à l’aide de Windows PowerShell
author: jessicaguan
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: jeguan
ms.openlocfilehash: 4211ec50ef0ea24ffb55f14791101c5d266ede2e
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612554"
---
# <a name="quickstart-set-up-an-azure-kubernetes-service-host-on-azure-stack-hci-using-powershell"></a>Démarrage rapide : Configurer un hôte Azure Kubernetes Service sur Azure Stack HCI à l’aide de PowerShell

> S’applique à : Azure Stack HCI, Windows Server 2019 Datacenter

Dans ce guide de démarrage rapide, vous allez apprendre à configurer un hôte Azure Kubernetes Service à l’aide de PowerShell. Si vous préférez utiliser Windows Admin Center, consultez [Configurer avec Windows Admin Center](setup.md).

## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous disposez des éléments suivants :
 - Clusters Azure Stack HCI comportant entre deux et quatre nœuds
 - Cluster de basculement Windows Server 2019 Datacenter
 - Windows Server 2019 Datacenter avec un nœud unique 
 
Avant de commencer, assurez-vous que toutes les conditions préalables sont remplies dans la page [Configuration système requise](.\system-requirements.md). 
**Nous vous recommandons de disposer d’un cluster Azure Stack HCI doté de 2 à 4 nœuds.** Si vous ne disposez pas de ces ressources, suivez les instructions de la [page d’inscription Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/).    

   > [!IMPORTANT]
   > Lorsque vous supprimez Azure Kubernetes Service sur Azure Stack HCI, suivez scrupuleusement les instructions de [Supprimer Azure Kubernetes Service sur Azure Stack HCI](#remove-azure-kubernetes-service-on-azure-stack-hci). 

## <a name="step-1-download-and-install-the-akshci-powershell-module"></a>Étape 1 : Téléchargez et installez le module PowerShell AksHci.

Téléchargez `AKS-HCI-Public-Preview-Dec-2020` à partir de la [page d’inscription Azure Kubernetes Service sur Azure Stack HCI](https://aka.ms/AKS-HCI-Evaluate). Le fichier zip `AksHci.Powershell.zip` contient le module PowerShell.

Si vous avez déjà installé Azure Kubernetes Service sur Azure Stack HCI avec PowerShell ou Windows Admin Center, il existe deux flux d’installation pour le nouveau module PowerShell :
 - Effectuez une nouvelle installation du module PowerShell, en commençant avec un système propre pour que les charges de travail précédemment déployées soient supprimées. Pour effectuer une nouvelle installation, rendez-vous à l’étape 1.1.
 - Mettez à niveau le module PowerShell si vous souhaitez conserver votre système et vos charges de travail en place. Pour mettre à niveau le module PowerShell, rendez-vous à l’étape 1.2.

### <a name="step-11-clean-install-of-the-akshci-powershell-module"></a>Étape 1.1 : Nouvelle installation du module PowerShell AksHci

Exécutez la commande suivante avant de continuer.
   ```powershell
   Uninstall-AksHci
   ```

**Fermez toutes les fenêtres PowerShell.** Supprimez les répertoires existants pour AksHci, AksHci.UI, MOC et MSK8sDownloadAgent qui se trouvent dans le chemin d’accès `%systemdrive%\program files\windowspowershell\modules`. Une fois les répertoires existants supprimés, vous pouvez extraire le contenu du nouveau fichier zip. Veillez à extraire le fichier zip à l’emplacement approprié (`%systemdrive%\program files\windowspowershell\modules`). Exécutez ensuite les commande suivantes.

   ```powershell
   Import-Module AksHci
   ```

Fermez toutes les fenêtres PowerShell, puis rouvrez une session d’administration et rendez-vous à l’étape 1.3 - Valider le module PowerShell mis à niveau.

### <a name="step-12-upgrade-the-akshci-powershell-module"></a>Étape 1.2 : Mettre à niveau le module PowerShell AksHci

**Fermez toutes les fenêtres PowerShell.** Supprimez les répertoires existants pour AksHci, AksHci.UI, MOC et MSK8sDownloadAgent qui se trouvent dans le chemin d’accès `%systemdrive%\program files\windowspowershell\modules`. Une fois ces répertoires supprimés, vous pouvez extraire le contenu du nouveau fichier zip. Veillez à extraire le fichier zip à l’emplacement approprié (`%systemdrive%\program files\windowspowershell\modules`). Exécutez ensuite les commande suivantes.

   ```powershell
   Import-Module AksHci
   ```
  
Après avoir exécuté les commandes ci-dessus, fermez toutes les fenêtres PowerShell, puis rouvrez une session d’administration pour valider la mise à niveau du module PowerShell comme indiqué ci-dessous, puis exécutez la commande `Update-AksHci` comme indiqué plus loin dans le document.

## <a name="step-13-validate-upgraded-powershell-module"></a>Étape 1.3 : Valider le module PowerShell mis à niveau

**Fermez toutes les fenêtres PowerShell** et rouvrez une nouvelle session d’administration pour vérifier si vous disposez de la dernière version du module PowerShell.  

   ```powershell
   Get-Command -Module AksHci
   ```
 
**Output:**
```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Initialize-AksHciNode                              0.2.12     AksHci
Function        Get-AksHciCluster                                  0.2.12     AksHci
Function        Get-AksHciConfig                                   0.2.12     AksHci
Function        Get-AksHciCredential                               0.2.12     AksHci
Function        Get-AksHciKubernetesVersion                        0.2.12     AksHci
Function        Get-AksHciLogs                                     0.2.12     AksHci
Function        Get-AksHciUpdates                                  0.2.12     AksHci
Function        Get-AksHciVersion                                  0.2.12     AksHci
Function        Get-AksHciVmSize                                   0.2.12     AksHci
Function        Install-AksHci                                     0.2.12     AksHci
Function        Install-AksHciAdAuth                               0.2.12     AksHci
Function        Install-AksHciArcOnboarding                        0.2.12     AksHci
Function        New-AksHciCluster                                  0.2.12     AksHci
Function        Remove-AksHciCluster                               0.2.12     AksHci
Function        Restart-AksHci                                     0.2.12     AksHci
Function        Set-AksHciClusterNodeCount                         0.2.12     AksHci
Function        Set-AksHciConfig                                   0.2.12     AksHci
Function        Uninstall-AksHci                                   0.2.12     AksHci
Function        Uninstall-AksHciAdAuth                             0.2.12     AksHci
Function        Uninstall-AksHciArcOnboarding                      0.2.12     AksHci
Function        Update-AksHci                                      0.2.12     AksHci
Function        Update-AksHciCluster                               0.2.12     AksHci
```

## <a name="step-2-prepare-your-machines-for-deployment"></a>Étape 2 : Préparer votre ou vos machines en vue du déploiement

Exécutez des vérifications sur chaque nœud physique pour vérifier si toutes les conditions requises sont satisfaites pour installer Azure Kubernetes Service sur Azure Stack HCI.

Ouvrez PowerShell en tant qu’administrateur et exécutez la commande suivante.

   ```powershell
   Initialize-AksHciNode
   ```

Une fois les vérifications terminées, le texte « Terminé » s’affiche en vert.

## <a name="step-3-configure-your-deployment"></a>Étape 3 : Configurer votre déploiement

Définissez les paramètres de configuration de l’hôte Azure Kubernetes Service. **Si vous effectuez un déploiement sur un cluster Azure Stack HCI comprenant entre deux et quatre nœuds ou un cluster de basculement Windows Server 2019 Datacenter, vous devez spécifier les paramètres `imageDir` et `cloudConfigLocation`.** Pour un seul nœud Windows Server 2019 Datacenter, tous les paramètres sont facultatifs et définis sur leurs valeurs par défaut. Toutefois, pour des performances optimales, **nous vous recommandons de préférer un déploiement de cluster Azure Stack HCI comptant de 2 à 4 nœuds.**

Configurez votre déploiement avec la commande suivante.

   ```powershell
   Set-AksHciConfig [-imageDir <String>]
                    [-cloudConfigLocation <String>]
                    [-nodeConfigLocation <String>]
                    [-vnetName <String>]
                    [-controlPlaneVmSize <VmSize>]
                    [-loadBalancerVmSize <VmSize>]
                    [-sshPublicKey <String>]
                    [-vipPoolStartIp <String>]
                    [-vipPoolEndIp <String>]
                    [-macPoolStart <String>]
                    [-macPoolEnd <String>]
                    [-vlanID <int>]
                    [-kvaLoadBalancerType {unstacked_haproxy, stacked_kube_vip}]
                    [-kvaControlPlaneEndpoint <String>]
                    [-proxyServerHTTP <String>]
                    [-proxyServerHTTP <String>]
                    [-proxyServerNoProxy <String>]
                    [-proxyServerCredential <PSCredential>]
                    [-cloudServiceCidr <String>]
                    [-workingDir <String>]
                    [-version <String>]
                    [-vnetType <String>]
                    [-nodeAgentPort <int>]
                    [-nodeAgentAuthorizerPort <int>]
                    [-clusterRoleName <String>]
                    [-cloudLocation <String>]
                    [-skipHostLimitChecks]
                    [-insecure]
                    [-skipUpdates]
                    [-forceDnsReplication]

   ```

### <a name="example"></a>Exemple

Pour effectuer un déploiement sur un cluster comprenant entre deux et quatre nœuds avec gestion réseau DHCP :

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config
   ```

Pour effectuer un déploiement avec un pool d’adresses IP virtuelles :

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vipPoolStartIp 10.0.0.20 -vipPoolEndIp 10.0.0.80
   ```

Pour effectuer un déploiement avec un équilibreur de charge `stacked_kube_vip` :

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -kvaLoadBalancerType stacked_kube_vip -kvaControlPlaneEndpoint 10.0.1.10
   ```

Pour effectuer un déploiement avec un serveur proxy :

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerNoProxy "localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16,10.231.110.0/24,10.68.237.0/24" -proxyServerCredential $credential
   ```  

### <a name="optional-parameters"></a>Paramètres facultatifs

`-imageDir`

Chemin d’accès au répertoire où Azure Kubernetes Service sur Azure Stack HCI stocke ses images VHD. Valeur par défaut `%systemdrive%\AksHciImageStore` pour les déploiements à nœud unique. *Pour les déploiements multinœud, ce paramètre est obligatoire*. Le chemin d’accès doit pointer vers un chemin d’accès de stockage partagé, par exemple `C:\ClusterStorage\Volume2\ImageStore` ou un partage SMB tel que `\\FileShare\ImageStore`.

`-cloudConfigLocation`

Emplacement de stockage de la configuration de l’agent cloud. Valeur par défaut `%systemdrive%\wssdcloudagent` pour les déploiements à nœud unique. L’emplacement peut être le même que le chemin d’accès de `-imageDir` ci-dessus. Pour les *déploiements multinœud, ce paramètre est obligatoire*. Le chemin d’accès doit pointer vers un chemin d’accès de stockage partagé, par exemple `C:\ClusterStorage\Volume2\ImageStore` ou un partage SMB tel que `\\FileShare\ImageStore`. L’emplacement doit se trouver sur un partage à haut niveau de disponibilité afin que le stockage soit toujours accessible.

`-nodeConfigLocation`

Emplacement où les agents de nœud stockeront leur configuration. Chaque nœud possède un agent de nœud, ce qui signifie que sa configuration est locale. Cet emplacement doit être un chemin d’accès local. Pointe sur `%systemdrive%\programdata\wssdagent` par défaut pour tous les déploiements.

`-vnetName`

Nom du commutateur virtuel auquel connecter les ordinateurs virtuels. Si vous disposez déjà d’un commutateur externe sur l’hôte, vous devez transmettre le nom du commutateur ici. Le commutateur sera créé s’il n’existe pas.La valeur par défaut est le nom « External ».  

`-controlPlaneVmSize`

Taille de la machine virtuelle à créer pour le plan de contrôle. Pour obtenir la liste des tailles de machines virtuelles disponibles, exécutez `Get-AksHciVmSize`.

`-loadBalancerVmSize`

Taille de la machine virtuelle à créer pour les machines virtuelles Load Balancer. Pour obtenir la liste des tailles de machines virtuelles disponibles, exécutez `Get-AksHciVmSize`.

`-sshPublicKey`

Chemin d’accès à un fichier de clé publique SSH. À l’aide de cette clé publique, vous serez en mesure de vous connecter aux machines virtuelles créées par le déploiement Azure Kubernetes Service sur Azure Stack HCI. Si vous disposez de votre propre clé publique SSH, vous devez indiquer son emplacement ici. Si aucune clé n’est fournie, nous allons en rechercher une dans `%systemdrive%\akshci\.ssh\akshci_rsa.pub`. Si le fichier n’existe pas, une paire de clés SSH est générée et utilisée dans l’emplacement ci-dessus.

`-vipPoolStartIp`

Lorsque vous utilisez des pools d’adresses IP virtuelles pour votre déploiement, ce paramètre spécifie le démarrage réseau du pool. Vous devez utiliser des pools VIP pour les déploiements de longue durée afin de garantir qu’un pool d’adresses IP reste cohérent. Cela est utile lorsque vos charges de travail doivent toujours être accessibles. La valeur par défaut est none.

`-vipPoolEndIp`

Lorsque vous utilisez des pools d’adresses IP virtuelles pour votre déploiement, ce paramètre spécifie la fin réseau du pool. Vous devez utiliser des pools VIP pour les déploiements de longue durée afin de garantir qu’un pool d’adresses IP reste cohérent. Cela est utile lorsque vos charges de travail doivent toujours être accessibles. La valeur par défaut est none.

`-macPoolStart` 

Cela permet de spécifier le début de l’adresse MAC du pool MAC que vous souhaitez utiliser pour la machine virtuelle hôte Azure Kubernetes Service. La syntaxe de l’adresse MAC exige que le bit le moins significatif du premier octet soit toujours égal à 0 et que le premier octet soit toujours un nombre pair (c.-à-d., 00, 02, 04, 06...). Une adresse MAC type peut se présenter comme suit : 02:1E:2B:78:00:00. Utilisez des pools d’adresses MAC pour les déploiements à long terme afin que les adresses MAC affectées soient cohérentes. Cela est utile si les machines virtuelles doivent posséder des adresses MAC spécifiques. La valeur par défaut est none.

`-macPoolEnd`

Cela permet de spécifier la fin de l’adresse MAC du pool MAC que vous souhaitez utiliser pour la machine virtuelle hôte Azure Kubernetes Service. La syntaxe de l’adresse MAC exige que le bit le moins significatif du premier octet soit toujours égal à 0 et que le premier octet soit toujours un nombre pair (c.-à-d., 00, 02, 04, 06...). Le premier octet de l’adresse passé en tant que `-macPoolEnd` doit être le même que le premier octet de l’adresse passé en tant que `-macPoolStart`. Utilisez des pools d’adresses MAC pour les déploiements à long terme afin que les adresses MAC affectées soient cohérentes. Cela est utile si les machines virtuelles doivent posséder des adresses MAC spécifiques. La valeur par défaut est none.

`-vlandID`

Elle peut être utilisée pour spécifier un ID de réseau local virtuel. L’hôte Azure Kubernetes Service et les cartes réseau des machines virtuelles du cluster Kubernetes seront marqués avec l’ID de réseau local virtuel fourni. Cette valeur doit être utilisée s’il existe un ID de réseau local virtuel spécifique qui doit être balisé pour obtenir la bonne connectivité. La valeur par défaut est none.

`-kvaLoadBalancerType`

Accepte `unstacked_haproxy` ou `stacked_kube_vip` en argument. `unstacked_haproxy` est l’emplacement par défaut où une machine virtuelle d’équilibrage de charge distincte est déployée avec HAProxy en tant que point de terminaison de serveur d’API de l’hôte Azure Kubernetes Service. `stacked_kube_vip` est une solution d’équilibrage de charge, [Kubevip](https://kube-vip.io/)pour l’hôte Azure Kubernetes Service. Elle vous permet de spécifier une adresse IP statique dans l’hôte en tant qu’adresse IP flottante sur les nœuds du plan de contrôle pour que le serveur d’API reste hautement disponible via l’adresse IP. Si vous choisissez cette option, vous devez spécifier l’adresse IP statique dans le paramètre `kvaControlPlaneEndpoint` ; aucune machine virtuelle d’équilibrage de charge distincte n’est alors déployée.

`stacked_kube_vip` nécessite une adresse IP et est plus respectueux des ressources en économisant la mémoire, l’UC et le temps de déploiement. Si vous ne disposez pas d’adresse IP comme adresse IP flottante, vous devez utiliser `unstacked_haproxy`. Cette dernière option nécessite une machine virtuelle d’équilibrage de charge. 

`-kvaControlPlaneEndpoint`

Spécifie l’adresse IP statique à utiliser comme adresse du serveur d’API hôte Azure Kubernetes Service lorsque le paramètre `kvaLoadBalancerType` est défini sur `stacked_kube_vip`. Si `stacked_kube_vip` est utilisé, ce paramètre doit être spécifié.

`-proxyServerHTTP`

Cela fournit un URI de serveur proxy qui doit être utilisé par tous les composants qui doivent atteindre des points de terminaison HTTP. Le format d’URI comprend le schéma d’URI, l’adresse du serveur et le port (autrement dit, https://server.com:8888) ). La valeur par défaut est none.

`-proxyServerHTTPS`

Cela fournit un URI de serveur proxy qui doit être utilisé par tous les composants qui doivent atteindre des points de terminaison HTTPS. Le format d’URI comprend le schéma d’URI, l’adresse du serveur et le port (autrement dit, https://server.com:8888) ). La valeur par défaut est none.

`-proxyServerNoProxy`

Il s’agit d’une chaîne d'adresses délimitée par des virgules qui seront exclues du proxy. La valeur par défaut est `localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16`. Cela exclut le trafic localhost (localhost, 127.0.0.1), le trafic du service Kubernetes interne (.svc), le service Kubernetes CIDR (10.96.0.0/12) et le routage CIDR (10.244.0.0/16) du serveur proxy. Vous pouvez utiliser ce paramètre pour ajouter des plages de sous-réseaux ou des exemptions de nom. **Les réglages de ce paramètre sont très importants car, s’ils ne sont pas configurés correctement, vous pouvez acheminer de façon inattendue le trafic de cluster Kubernetes interne vers votre proxy. Cela peut entraîner différentes défaillances dans la communication réseau.**


`-proxyServerCredential`

Cela fournit le nom d’utilisateur et le mot de passe d’authentification auprès de vos serveurs proxy HTTP/HTTPS. Vous pouvez utiliser `Get-Credential` pour générer un objet `PSCredential` à transmettre à ce paramètre. La valeur par défaut est none.

`-cloudServiceCidr`

Elle peut être utilisé pour fournir un préfixe IP/réseau statique à attribuer au service MOC CloudAgent. Cette valeur doit être fournie au format CIDR. (Exemple : 192.168.1.2/16). Vous pouvez spécifier cette valeur pour vous assurer que tout élément important sur le réseau est toujours accessible, car l’adresse IP ne change pas. La valeur par défaut est none.

`-workingDir`

Il s’agit d’un répertoire de travail que le module doit utiliser pour stocker des fichiers de petite taille. Par défaut, pointe vers `%PROGRAMFILES%\AksHci` et ne doit pas être modifiée pour la plupart des déploiements. Nous vous déconseillons de modifier la valeur par défaut. 

`-version`

Version d’Azure Kubernetes Service sur Azure Stack HCI que vous souhaitez déployer. La version la plus récente est utilisée par défaut. Nous vous déconseillons de modifier la valeur par défaut.

`-vnetType`

Type de commutateur virtuel à connecter ou à créer. Par défaut, il s’agit du type de commutateur « External ». Nous vous déconseillons de modifier la valeur par défaut.

`-nodeAgentPort`

Numéro de port TCP/IP sur lequel les agents de nœud doivent écouter le trafic. La valeur par défaut est 45000. Nous vous déconseillons de modifier la valeur par défaut. 

`-nodeAgentAuthorizerPort`

Numéro de port TCP/IP que les agents de nœud doit utiliser pour son port d’autorisation. La valeur par défaut est 45001. Nous vous déconseillons de modifier la valeur par défaut.  

`-clusterRoleName`

Spécifie le nom à utiliser lors de la création de l’agent cloud en tant que service générique au sein du cluster. Par défaut, il s’agit d’un nom unique avec un préfixe ca et un suffixe GUID (par exemple : « ca-9e6eb299-bc0b-4f00-9fd7-942843820c26 »). Nous vous déconseillons de modifier la valeur par défaut.

`-cloudLocation` 

Ce paramètre fournit un nom d’emplacement cloud personnalisé géré par Microsoft. Le nom par défaut est « MocLocation ». Nous vous déconseillons de modifier la valeur par défaut.

`-skipHostLimitChecks`

Demande au script d’ignorer les vérifications qu’il effectue pour confirmer l’espace mémoire et disque est disponible avant de permettre au déploiement de continuer. Ce paramètre n'est pas recommandé.

`-insecure`

Déploie les composants Azure Kubernetes Service sur Azure Stack HCI, tels que l’agent cloud et le(s) agent(s) de nœud, en mode non sécurisé (aucune connexion TLS sécurisée).  Nous déconseillons d’utiliser le mode non sécurisé dans les environnements de production.

`-skipUpdates`

Utilisez cet indicateur si vous souhaitez ignorer les mises à jour disponibles. Ce paramètre n'est pas recommandé.

`-forceDnsReplication`

La réplication DNS peut prendre jusqu’à une heure sur certains systèmes. Le déploiement s’effectue alors lentement. Si vous avez rencontré ce problème, c’est que Install-AksHci est bloqué dans une boucle. Pour ignorer ce problème, essayez d’utiliser cet indicateur. L’indicateur `-forceDnsReplication` n’est pas un correctif garanti. En cas d’échec de la logique sous-jacente de l’indicateur, l’erreur est masquée et la commande s’exécute comme si l’indicateur n’avait pas été fourni. 

### <a name="reset-the-azure-kubernetes-service-on-azure-stack-hci-configuration"></a>Réinitialiser la configuration d’Azure Kubernetes Service sur Azure Stack HCI

Pour réinitialiser la configuration d’Azure Kubernetes Service sur Azure Stack HCI, exécutez les commandes suivantes. L’exécution de cette commande seule permet de rétablir les valeurs par défaut de la configuration.

```powershell
Set-AksHciConfig
```

## <a name="step-4-start-a-new-deployment"></a>Étape 4 : Démarrer un nouveau déploiement

Après avoir configuré votre déploiement, vous devez le démarrer. Cette opération installe les agents/services Azure Kubernetes Service sur Azure Stack HCI et l’hôte Azure Kubernetes Service.

Pour commencer le déploiement, exécutez la commande suivante.

```powershell
Install-AksHci
```

### <a name="verify-your-deployed-azure-kubernetes-service-host"></a>Vérifier votre hôte Azure Kubernetes Service déployé

Pour vous assurer que votre hôte Azure Kubernetes Service a été déployé, exécutez la commande suivante. Vous pouvez également obtenir la liste des clusters Kubernetes à l’aide de la même commande après les avoir déployés.

```powershell
Get-AksHciCluster
```

**Output:**
```

Name            : clustergroup-management
Version         : v1.18.8
Control Planes  : 1
Linux Workers   : 0
Windows Workers : 0
Phase           : provisioned
Ready           : True
```

## <a name="step-5-access-your-clusters-using-kubectl"></a>Étape 5 : Accéder à vos clusters à l’aide de kubectl

Pour accéder à votre hôte Azure Kubernetes Service ou à votre cluster Kubernetes à l’aide de kubectl, exécutez la commande suivante. Cette opération utilise le fichier kubeconfig du cluster spécifié comme fichier kubeconfig par défaut pour kubectl.

```powershell
Get-AksHciCredential -clusterName <String>
                     [-outputLocation <String>]
```

### <a name="example"></a>Exemple

```powershell
Get-AksHciCredential -clusterName clustergroup-management
```

### <a name="required-parameters"></a>Paramètres obligatoires

`clusterName`

Nom du cluster.

### <a name="optional-parameters"></a>Paramètres facultatifs

`outputLocation`

Emplacement auquel vous voulez que le fichier kubeconfig soit téléchargé. La valeur par défaut est `%USERPROFILE%\.kube`.

## <a name="get-logs"></a>Obtenir des journaux d’activité

Pour récupérer les journaux de vos pods, exécutez la commande suivante. Cette commande crée un dossier compressé de sortie appelé `akshcilogs` dans le chemin d’accès `C:\wssd\akshcilogs`.

```powershell
Get-AksHciLogs
```

## <a name="update-to-the-latest-version-of-azure-kubernetes-service-on-azure-stack-hci"></a>Mettre à jour vers la dernière version d’Azure Kubernetes Service sur Azure Stack HCI

Pour mettre à jour vers la dernière version d’Azure Kubernetes Service sur Azure Stack HCI, exécutez la commande suivante. La commande de mise à jour ne fonctionne que si vous avez installé la version sortie en octobre. Elle ne fonctionne pas pour les versions antérieures à la version d’octobre. Cette commande de mise à jour met à jour l’hôte Azure Kubernetes Service et la plateforme cloud Microsoft locale. Pour cette préversion, la version de Kubernetes et la version du système d’exploitation hôte AKS restent les mêmes. Cette commande ne met pas à niveau les clusters de charge de travail existants. Les nouveaux clusters de charge de travail créés après la mise à jour de l’hôte AKS diffèrent des clusters de charge de travail existants en ce qui concerne la version du système d’exploitation du nœud Windows et la version de Kubernetes.

   ```powershell
   Update-AksHci
   ```
   
Nous vous recommandons de mettre à jour les clusters de charge de travail immédiatement après la mise à jour du cluster de gestion pour empêcher l’exécution de versions de système d’exploitation Windows Server non prises en charge dans vos clusters Kubernetes avec des nœuds Windows. Pour mettre à jour votre cluster de charge de travail, consultez la section relative à la [mise à jour de votre cluster de charge de travail](create-kubernetes-cluster-powershell.md).

## <a name="restart-azure-kubernetes-service-on-azure-stack-hci"></a>Redémarrer Azure Kubernetes Service sur Azure Stack HCI

L’opération de redémarrage d’Azure Kubernetes Service sur Azure Stack HCI supprime tous vos clusters Kubernetes, le cas échéant, et l’hôte Azure Kubernetes Service. Elle désinstalle également les agents et services Azure Kubernetes Service sur Azure Stack HCI des nœuds. Elle repasse ensuite par les étapes du processus d’installation d’origine jusqu’à ce que l’hôte soit recréé. Azure Kubernetes Service sur Azure Stack HCI que vous avez configuré via `Set-AksHciConfig` et les images VHDX téléchargées sont conservés.

Pour redémarrer Azure Kubernetes Service sur Azure Stack HCI avec les mêmes paramètres de configuration, exécutez la commande suivante.

```powershell
Restart-AksHci
```

## <a name="reset-configuration-settings-and-reinstall-azure-kubernetes-service-on-azure-stack-hci"></a>Réinitialiser les paramètres de configuration et réinstaller Azure Kubernetes Service sur Azure Stack HCI

Pour réinstaller Azure Kubernetes Service sur Azure Stack HCI avec des paramètres de configuration différents, exécutez la commande suivante.

```powershell
Uninstall-AksHci
```

Après avoir exécuté la commande ci-dessus, vous pouvez modifier les paramètres de configuration à l’aide de la commande suivante. Les paramètres restent les mêmes que ceux décrits à l’étape 3. Si vous exécutez cette commande sans paramètres, les valeurs par défaut des paramètres sont rétablies.

```powershell
Set-AksHciConfig
```

Après avoir modifié la configuration selon les paramètres de votre choix, exécutez la commande suivante pour réinstaller Azure Stack Kubernetes sur Azure Stack HCI.

```powershell
Install-AksHci
```

## <a name="remove-azure-kubernetes-service-on-azure-stack-hci"></a>Supprimer Azure Kubernetes Service sur Azure Stack HCI

Pour supprimer Azure Kubernetes Service sur Azure Stack HCI, exécutez la commande suivante. **Si vous utilisez PowerShell pour désinstaller un déploiement Windows Admin Center, vous devez exécuter la commande avec l’indicateur `-Force`.**

```powershell
Uninstall-AksHci
```

Après la commande ci-dessus, vous pouvez exécuter la commande `Install-AksHci` pour installer l’hôte Azure Container Service avec la même configuration que précédemment. Si vous souhaitez modifier la configuration, exécutez `Set-AksHciConfig` avec les modifications que vous souhaitez apporter avant d’exécuter la commande d’installation.

Si vous ne souhaitez pas conserver l’ancienne configuration, exécutez la commande suivante.

```powershell
Uninstall-AksHci -Force
```

Si les commandes PowerShell sont exécutées sur un cluster où Windows Admin Center a été précédemment utilisé pour le déploiement, le module PowerShell vérifie l’existence du fichier de configuration de Windows Admin Center. Windows Admin Center place son fichier de configuration sur tous les nœuds. **Si vous utilisez la commande de désinstallation et revenez à Windows Admin Center, exécutez la commande de désinstallation ci-dessus avec l’indicateur `-Force`. Si ce n’est pas le cas, PowerShell et Windows Admin Center ne seront pas synchronisés.**

## <a name="next-steps"></a>Étapes suivantes

- [Créer un cluster Kubernetes pour vos applications](create-kubernetes-cluster-powershell.md)
