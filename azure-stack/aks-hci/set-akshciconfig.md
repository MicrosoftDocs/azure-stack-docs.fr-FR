---
title: Set-AksHciConfig
author: jessicaguan
description: La commande PowerShell Set-AksHciConfig met à jour les paramètres de configuration de l’hôte Azure Kubernetes Service.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 80a6fc50bbfcaf028d6c810bbef7e2d6b4508cab
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874425"
---
# <a name="set-akshciconfig"></a>Set-AksHciConfig

## <a name="synopsis"></a>Synopsis
Définir ou mettre à jour les paramètres de configuration de l’hôte Azure Kubernetes Service.

## <a name="syntax"></a>Syntaxe

### <a name="set-configuration-for-host"></a>Définir la configuration de l’hôte
```powershell
Set-AksHciConfig [-imageDir <String>]
                 [-cloudConfigLocation <String>]
                 [-nodeConfigLocation <String>]
                 [-vnet <Virtual Network>]
                 [-controlPlaneVmSize <VmSize>]
                 [-sshPublicKey <String>]
                 [-macPoolStart <String>]
                 [-macPoolEnd <String>]       
                 [-proxyServerHTTP <String>]
                 [-proxyServerHTTPS <String>]
                 [-proxyServerNoProxy <String>]
                 [-proxyServerCertFile <String>]
                 [-proxyServerCredential <PSCredential>]
                 [-cloudServiceCidr <String>]
                 [-workingDir <String>]
                 [-version <String>]
                 [-nodeAgentPort <int>]
                 [-nodeAgentAuthorizerPort <int>]
                 [-clusterRoleName <String>]
                 [-cloudLocation <String>]
                 [-skipHostLimitChecks]
                 [-insecure]
                 [-skipUpdates]
                 [-forceDnsReplication]
                 [-enableDiagnosticData]   
```

## <a name="description"></a>Description
Définissez les paramètres de configuration de l’hôte Azure Kubernetes Service. Si vous effectuez un déploiement sur un cluster Azure Stack HCI comprenant entre deux et quatre nœuds, ou sur un cluster de basculement Windows Server 2019 Datacenter, vous devez spécifier les paramètres imageDir et cloudConfigLocation. Pour un seul nœud Windows Server 2019 Datacenter, tous les paramètres sont facultatifs et définis sur leurs valeurs par défaut. Toutefois, pour des performances optimales, nous vous recommandons de préférer un déploiement de cluster Azure Stack HCI comptant de 2 à 4 nœuds.

## <a name="examples"></a>Exemples

### <a name="to-deploy-on-a-2-4-node-cluster-with-dhcp-networking"></a>Pour effectuer un déploiement sur un cluster comprenant entre deux et quatre nœuds avec le réseau DHCP
```powershell
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config
```

### <a name="to-deploy-with-a-virtual-ip-pool"></a>Pour effectuer un déploiement avec un pool d’IP virtuelles
```powershell
PS C:\> New-AksHciNetworkSetting -name newNetwork -subnetCidr 192.168.2.0/32 -defaultGateway 192.168.2.1 -subnetVSwitch External -vipPoolStart 192.168.2.20 -vipPoolEnd 192.168.2.80   
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -networkSettings newNetwork
```

### <a name="to-deploy-with-a-proxy-server"></a>Pour effectuer un déploiement avec un serveur proxy
```powershell
PS C:\> $credential = Get-Credential
```

```powershell
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerNoProxy "localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16,10.231.110.0/24,10.68.237.0/24" -proxyServerCredential $credential
```

## <a name="parameters"></a>Paramètres

### <a name="-imagedir"></a>-imageDir
Chemin d’accès au répertoire où Azure Kubernetes Service sur Azure Stack HCI stocke ses images VHD. Valeur par défaut `%systemdrive%\AksHciImageStore` pour les déploiements à nœud unique. Pour les déploiements multinœud, ce paramètre est obligatoire. Le chemin d’accès doit pointer vers un chemin d’accès de stockage partagé, par exemple `C:\ClusterStorage\Volume2\ImageStore` ou un partage SMB tel que `\\FileShare\ImageStore`.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %systemdrive%\AksHciImageStore
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-cloudconfiglocation"></a>-cloudConfigLocation
Emplacement de stockage de la configuration de l’agent cloud. Valeur par défaut `%systemdrive%\wssdcloudagent` pour les déploiements à nœud unique. L’emplacement peut être le même que le chemin de -imageDir ci-dessus. Pour les déploiements multinœud, ce paramètre est obligatoire. Le chemin d’accès doit pointer vers un chemin d’accès de stockage partagé, par exemple `C:\ClusterStorage\Volume2\ImageStore` ou un partage SMB tel que `\\FileShare\ImageStore`. L’emplacement doit se trouver sur un partage à haut niveau de disponibilité afin que le stockage soit toujours accessible.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %systemdrive%\wssdcloudagent
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-nodeconfiglocation"></a>-nodeConfigLocation
Emplacement où les agents de nœud stockeront leur configuration. Chaque nœud possède un agent de nœud, ce qui signifie que sa configuration est locale. Cet emplacement doit être un chemin d’accès local. Pointe sur `%systemdrive%\programdata\wssdagent` par défaut pour tous les déploiements.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %systemdrive%\programdata\wssdagent
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vnet"></a>-vnet
Nom de l’objet AksHciNetworkSetting créé avec la commande New-AksHciNetworkSetting.
```yaml
Type: VirtualNetwork
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-controlplanevmsize"></a>-controlPlaneVmSize
Taille de la machine virtuelle à créer pour le plan de contrôle. La valeur par défaut est Standard_A4_V2. Pour obtenir la liste des tailles de machines virtuelles disponibles, exécutez `Get-AksHciVmSize`.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_A4_V2
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-sshpublickey"></a>-sshPublicKey
Chemin d’accès à un fichier de clé publique SSH. À l’aide de cette clé publique, vous serez en mesure de vous connecter aux machines virtuelles créées par le déploiement Azure Kubernetes Service sur Azure Stack HCI. Si vous disposez de votre propre clé publique SSH, vous devez indiquer son emplacement ici. Si aucune clé n’est fournie, nous allons en rechercher une dans `%systemdrive%\akshci\.ssh\akshci_rsa`.pub. Si le fichier n’existe pas, une paire de clés SSH est générée et utilisée dans l’emplacement ci-dessus.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-macpoolstart"></a>-macPoolStart
Cela permet de spécifier le début de l’adresse MAC du pool MAC que vous souhaitez utiliser pour la machine virtuelle hôte Azure Kubernetes Service. La syntaxe de l’adresse MAC exige que le bit le moins significatif du premier octet soit toujours égal à 0 et que le premier octet soit toujours un nombre pair (c.-à-d., 00, 02, 04, 06...). Une adresse MAC type peut se présenter comme suit : 02:1E:2B:78:00:00. Utilisez des pools d’adresses MAC pour les déploiements à long terme afin que les adresses MAC affectées soient cohérentes. Cela est utile si les machines virtuelles doivent posséder des adresses MAC spécifiques. La valeur par défaut est none.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-macpoolend"></a>-macPoolEnd
Cela permet de spécifier la fin de l’adresse MAC du pool MAC que vous souhaitez utiliser pour la machine virtuelle hôte Azure Kubernetes Service. La syntaxe de l’adresse MAC exige que le bit le moins significatif du premier octet soit toujours égal à 0 et que le premier octet soit toujours un nombre pair (c.-à-d., 00, 02, 04, 06...). Le premier octet de l’adresse passée en tant que -macPoolEnd doit être le même que le premier octet de l’adresse passée en tant que -macPoolStart. Utilisez des pools d’adresses MAC pour les déploiements à long terme afin que les adresses MAC affectées soient cohérentes. Cela est utile si les machines virtuelles doivent posséder des adresses MAC spécifiques. La valeur par défaut est none.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyserverhttp"></a>-proxyServerHTTP
Cela fournit un URI de serveur proxy qui doit être utilisé par tous les composants qui doivent atteindre des points de terminaison HTTP. Le format d’URI comprend le schéma d’URI, l’adresse du serveur et le port (autrement dit, https://server.com:8888) ). La valeur par défaut est none.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyserverhttps"></a>-proxyServerHTTPS
Cela fournit un URI de serveur proxy qui doit être utilisé par tous les composants qui doivent atteindre des points de terminaison HTTPS. Le format d’URI comprend le schéma d’URI, l’adresse du serveur et le port (autrement dit, https://server.com:8888) ). La valeur par défaut est none.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservernoproxy"></a>-proxyServerNoProxy
Il s’agit d’une chaîne d'adresses délimitée par des virgules qui seront exclues du proxy. La valeur par défaut est localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16. Sont exclus le trafic localhost (localhost, 127.0.0.1), le trafic de service Kubernetes interne (.svc), le CIDR de service Kubernetes (10.96.0.0/12) et le CIDR de POD Kubernetes (10.244.0.0/16) du serveur proxy. Vous pouvez utiliser ce paramètre pour ajouter des plages de sous-réseaux ou des exemptions de nom. **Les réglages de ce paramètre sont très importants car, s’ils ne sont pas configurés correctement, vous pouvez acheminer de façon inattendue le trafic de cluster Kubernetes interne vers votre proxy. Cela peut entraîner différentes défaillances dans la communication réseau.**

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservercertfile"></a>-proxyServerCertFile
Certificat utilisé pour l’authentification auprès du serveur proxy.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:
 
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservercredential"></a>-proxyServerCredential
Cela fournit le nom d’utilisateur et le mot de passe d’authentification auprès de vos serveurs proxy HTTP/HTTPS. Vous pouvez utiliser `Get-Credential` pour générer un objet PSCredential à transmettre à ce paramètre. La valeur par défaut est none.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-cloudservicecidr"></a>-cloudServiceCidr
Elle peut être utilisé pour fournir un préfixe IP/réseau statique à attribuer au service MOC CloudAgent. Cette valeur doit être fournie au format CIDR. (Exemple : 192.168.1.2/16). Vous pouvez spécifier cette valeur pour vous assurer que tout élément important sur le réseau est toujours accessible, car l’adresse IP ne change pas. La valeur par défaut est none.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-workingdir"></a>-workingDir
Il s’agit d’un répertoire de travail que le module doit utiliser pour stocker des fichiers de petite taille. Par défaut, pointe vers `%PROGRAMFILES%\AksHci` et ne doit pas être modifiée pour la plupart des déploiements. Nous vous déconseillons de modifier la valeur par défaut.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %PROGRAMFILES%\AksHci
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-version"></a>-version
Version d’Azure Kubernetes Service sur Azure Stack HCI que vous souhaitez déployer. La version la plus récente est utilisée par défaut. Nous vous déconseillons de modifier la valeur par défaut.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Latest version
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-nodeagentport"></a>-nodeAgentPort
Numéro de port TCP/IP sur lequel les agents de nœud doivent écouter le trafic. La valeur par défaut est 45000. Nous vous déconseillons de modifier la valeur par défaut.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 45000
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-nodeagentauthorizerport"></a>-nodeAgentAuthorizerPort
Numéro de port TCP/IP que les agents de nœud doit utiliser pour son port d’autorisation. La valeur par défaut est 45001. Nous vous déconseillons de modifier la valeur par défaut. 

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 45001
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-clusterrolename"></a>-clusterRoleName
Spécifie le nom à utiliser lors de la création de l’agent cloud en tant que service générique au sein du cluster. Par défaut, il s’agit d’un nom unique avec un préfixe ca et un suffixe GUID (par exemple : « ca-9e6eb299-bc0b-4f00-9fd7-942843820c26 »). Nous vous déconseillons de modifier la valeur par défaut.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: A unique name with a prefix of ca- and a guid suffix
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-cloudlocation"></a>-cloudLocation
Ce paramètre fournit un nom d’emplacement cloud personnalisé géré par Microsoft. Le nom par défaut est « MocLocation ». Nous vous déconseillons de modifier la valeur par défaut.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: MocLocation
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-skiphostlimitchecks"></a>-skipHostLimitChecks
Demande au script d’ignorer les vérifications qu’il effectue pour confirmer l’espace mémoire et disque est disponible avant de permettre au déploiement de continuer. Ce paramètre n'est pas recommandé.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-insecure"></a>-insecure
Déploie les composants Azure Kubernetes Service sur Azure Stack HCI, tels que l’agent cloud et le(s) agent(s) de nœud, en mode non sécurisé (aucune connexion TLS sécurisée).  Nous déconseillons d’utiliser le mode non sécurisé dans les environnements de production.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-skipupdates"></a>-skipUpdates
Utilisez cet indicateur si vous souhaitez ignorer les mises à jour disponibles. Ce paramètre n'est pas recommandé.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-forcednsreplication"></a>-forceDnsReplication
La réplication DNS peut prendre jusqu’à une heure sur certains systèmes. Le déploiement s’effectue alors lentement. Si vous avez rencontré ce problème, c’est que Install-AksHci est bloqué dans une boucle. Pour ignorer ce problème, essayez d’utiliser cet indicateur. L’indicateur -forceDnsReplication n’est pas un correctif garanti. En cas d’échec de la logique sous-jacente de l’indicateur, l’erreur est masquée et la commande s’exécute comme si l’indicateur n’avait pas été fourni.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-enablediagnosticdata"></a>-enableDiagnosticData`

Utilisez cet indicateur pour consentir à l’envoi des données de diagnostic nécessaires, à Microsoft. Cette action est utile si vous souhaitez ignorer l’invite de consentement que, sinon, `Set-AksHciConfig` affichera. `Install-AksHci` échoue si vous n’acceptez pas l’invite de consentement lorsqu’elle est présentée par `Set-AksHciConfig`.
