---
title: Déployer le contrôleur de réseau à l’aide de Windows PowerShell
description: Découvrir comment déployer le contrôleur de réseau à l’aide de Windows PowerShell
author: v-dasis
ms.topic: how-to
ms.date: 08/17/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 6405b774cf27e8c48b9200d401174eaa5e292584
ms.sourcegitcommit: 65a115d1499b5fe16b6fe1c31cce43be21d05ef8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88818315"
---
# <a name="deploy-network-controller-using-windows-powershell"></a>Déployer le contrôleur de réseau à l’aide de Windows PowerShell

> S’applique à Azure Stack HCI, version 20H2 ; Windows Server 2019

Cette rubrique fournit des instructions sur l’utilisation de Windows PowerShell pour déployer un contrôleur de réseau sur une ou plusieurs machines virtuelles qui s’exécutent sur un cluster Azure Stack HCI. Le contrôleur de réseau est un composant de SDN (Software Defined Networking).

## <a name="using-windows-powershell"></a>Utilisation de Windows PowerShell

Vous pouvez exécuter PowerShell localement dans une session de Bureau à distance (RDP) sur un serveur hôte ou vous pouvez l’exécuter à distance à partir d’un ordinateur de gestion.

Quand vous exécutez PowerShell à partir d’un ordinateur de gestion, incluez le paramètre `-Name` ou `-Cluster` avec le nom du serveur ou du cluster que vous gérez. En outre, il peut être nécessaire de spécifier le nom de domaine complet (FQDN) quand vous utilisez le paramètre `-ComputerName` pour un serveur.

Vous aurez également besoin des applets de commande des outils d’administration de serveur distant (RSAT), et des modules PowerShell pour Hyper-V et le clustering de basculement. Si ceux-ci derniers ne sont pas déjà disponibles dans votre session PowerShell sur votre ordinateur de gestion, vous pouvez les ajouter en utilisant la commande suivante : `Add-WindowsFeature RSAT-Clustering-PowerShell`.

## <a name="install-the-network-controller-server-role"></a>Installer le rôle serveur du contrôleur de réseau

Utilisez cette procédure pour installer le rôle de serveur du contrôleur de réseau sur une machine virtuelle.

>[!IMPORTANT]
>Ne déployez pas le rôle serveur du contrôleur de réseau sur les hôtes physiques. Pour déployer le contrôleur de réseau, vous devez installer le rôle serveur du contrôleur de réseau sur une machine virtuelle Hyper-V installée sur un hôte Hyper-V. Une fois que vous avez installé le contrôleur de réseau sur des machines virtuelles sur trois hôtes Hyper-V différents, vous devez activer les hôtes Hyper-V pour la mise en réseau SDN (Software Defined Networking) en ajoutant les hôtes au contrôleur de réseau. En procédant ainsi, vous activez le Load Balancer logiciel SDN pour qu’il fonctionne.

Pour effectuer cette procédure, il est nécessaire d’appartenir au groupe **Administrateurs** ou à un groupe équivalent.  

>[!NOTE]
>Si vous souhaitez utiliser Gestionnaire de serveur au lieu de Windows PowerShell pour installer le contrôleur de réseau, consultez [Installer le rôle serveur du contrôleur de réseau à l’aide du Gestionnaire de serveur](https://technet.microsoft.com/library/mt403348.aspx)

Pour installer le contrôleur de réseau, saisissez les commandes suivantes :

```powershell
Install-WindowsFeature -Name NetworkController -IncludeManagementTools
```

L’installation du contrôleur de réseau requiert le redémarrage de l’ordinateur. Pour cela, tapez la commande suivante :

```powershell
Restart-Computer
```

## <a name="configure-the-network-controller-cluster"></a>Configurer le cluster du contrôleur de réseau

Le cluster du contrôleur de réseau offre une haute disponibilité et une évolutivité élevée à l’application du contrôleur de réseau, que vous pouvez configurer après avoir créé le cluster et qui est hébergé au-dessus du cluster.

>[!NOTE]
>Vous pouvez effectuer les procédures décrites dans les sections suivantes soit directement sur la machine virtuelle sur laquelle vous avez installé le contrôleur de réseau, soit à partir d’un ordinateur distant qui exécute Windows Admin Center. En outre, pour effectuer cette procédure, il est nécessaire d’appartenir au groupe **Administrateurs** ou à un groupe équivalent. Si l’ordinateur ou la machine virtuelle sur lequel vous avez installé le contrôleur de réseau est joint à un domaine, votre compte d’utilisateur doit être membre du groupe **Utilisateurs du domaine**.

Vous pouvez créer un cluster du contrôleur de réseau en créant un objet de nœud, puis en configurant le cluster.

### <a name="create-a-node-object"></a>Créer un objet de nœud

Vous devez créer un objet de nœud pour chaque machine virtuelle qui est membre du cluster du contrôleur de réseau.

Pour créer un objet de nœud, saisissez la commande suivante. Veillez à utiliser des valeurs pour chaque paramètre qui conviennent à votre déploiement.  

```powershell
New-NetworkControllerNodeObject -Name <string> -Server "ServerName" -FaultDomain "SiteName" -RestInterface "Name" [-NodeCertificate <X509Certificate2>]
```

La table suivante fournit des descriptions de chaque paramètre de la commande `New-NetworkControllerNodeObject`.

|Paramètre|Description|
|-------------|---------------|
|Nom|Le paramètre **Nom** spécifie le nom convivial du serveur que vous souhaitez ajouter au cluster|
|Serveur|Le paramètre **Serveur** spécifie le nom d’hôte, le nom de domaine complet (FQDN) ou l’adresse IP du serveur que vous souhaitez ajouter au cluster. Pour les ordinateurs joints au domaine, le nom de domaine complet est requis.|
|FaultDomain|Le paramètre **FaultDomain** spécifie le domaine d’erreur du serveur que vous ajoutez au cluster. Ce paramètre définit les serveurs qui peuvent rencontrer une défaillance en même temps que le serveur que vous ajoutez au cluster. Cette défaillance peut être due à des dépendances physiques partagées, telles que des sources d’alimentation et de mise en réseau. En général, les domaines d'erreur représentent les hiérarchies associées à ces dépendances partagées, avec davantage de nœuds susceptibles d'échouer ensemble depuis un point plus élevé dans l'arborescence du domaine d'erreur. Lors du runtime, le contrôleur de réseau étudie les domaines d'erreur dans le cluster et tente de répartir les services d'un contrôleur de réseau afin qu'ils soient tous dans des domaines d'erreur distincts. Ce processus permet de s'assurer, en cas d'échec d'un domaine d'erreur, que la disponibilité de ce service et son état ne sont pas compromis. Les domaines d’erreur sont spécifiés sous forme hiérarchique. Par exemple : « FD:/DC1/Rack1/Host1 », où DC1 est le nom du centre de données, Rack1 est le nom du rack et Host1 est le nom de l’hôte sur lequel le nœud est placé.|
|RestInterface|Le paramètre **RestInterface** spécifie le nom de l’interface sur le nœud où la communication REST (Representational State Transfer) est arrêtée. Cette interface du contrôleur de réseau reçoit les requêtes de l’API Northbound à partir de la couche de gestion du réseau.|
|NodeCertificate|Le paramètre **NodeCertificate** spécifie le certificat utilisé par le contrôleur de réseau pour l’authentification de l’ordinateur. Le certificat est requis si vous utilisez l’authentification basée sur les certificats pour la communication au sein du cluster. Le certificat est également utilisé pour le chiffrement du trafic entre les services du contrôleur de réseau. Le nom d’objet du certificat doit être le même que le nom DNS du nœud.|

### <a name="configure-the-cluster"></a>Configurer le cluster

Pour configurer le cluster, saisissez la commande suivante. Veillez à utiliser des valeurs pour chaque paramètre qui conviennent à votre déploiement.

```powershell
Install-NetworkControllerCluster -Node "NetworkControllerNodeName" -ClusterAuthentication "ClusterAuthenticationType" [-ManagementSecurityGroup <string>][-DiagnosticLogLocation <string>][-LogLocationCredential <PSCredential>] [-CredentialEncryptionCertificate <X509Certificate2>][-Credential <PSCredential>][-CertificateThumbprint <String>] [-UseSSL][-ComputerName <string>][-LogSizeLimitInMBs<UInt32>] [-LogTimeLimitInDays<UInt32>]
```

La table suivante fournit des descriptions de chaque paramètre de la commande `Install-NetworkControllerCluster`.
  
|Paramètre|Description|
|-------------|---------------|
|ClusterAuthentication|Le paramètre **ClusterAuthentication** spécifie le type d’authentification utilisé pour sécuriser la communication entre les nœuds et est également utilisé pour le chiffrement du trafic entre les services du contrôleur de réseau. Les valeurs prises en charge sont **Kerberos**, **x509** et **Aucun**. L’authentification Kerberos utilise des comptes de domaine et peut uniquement être utilisée si les nœuds du contrôleur de réseau sont joints à un domaine. Si vous spécifiez l’authentification basée sur x509, vous devez fournir un certificat dans l’objet NetworkControllerNode. En outre, vous devez configurer manuellement le certificat avant d’exécuter cette commande.|
|ManagementSecurityGroup|Le paramètre **ManagementSecurityGroup** spécifie le nom du groupe de sécurité qui contient les utilisateurs autorisés à exécuter les cmdlets de gestion à partir d’un ordinateur distant. Cela s’applique uniquement si ClusterAuthentication est Kerberos. Vous devez spécifier un groupe de sécurité de domaine et non un groupe de sécurité sur l’ordinateur local.|
|Nœud|Le paramètre **Nœud** spécifie la liste des nœuds de contrôleur de réseau que vous avez créés à l’aide de la commande **New-NetworkControllerNodeObject**.|
|DiagnosticLogLocation|Le paramètre **DiagnosticLogLocation** spécifie l’emplacement du partage dans lequel les journaux de diagnostic sont régulièrement chargés. Si vous ne spécifiez pas de valeur pour ce paramètre, les journaux sont stockés localement sur chaque nœud. Les journaux sont stockés localement dans le dossier %systemdrive%\Windows\tracing\SDNDiagnostics. Les journaux de cluster sont stockés localement dans le dossier %systemdrive%\ProgramData\Microsoft\Service Fabric\log\Traces.|
|LogLocationCredential|Le paramètre **LogLocationCredential** spécifie les informations d’identification requises pour accéder à l’emplacement de partage où les journaux sont stockés.|
|CredentialEncryptionCertificate|Le paramètre **CredentialEncryptionCertificate** spécifie le certificat utilisé par le contrôleur de réseau pour chiffrer les informations d’identification utilisées pour accéder aux fichiers binaires du contrôleur de réseau et au paramètre **LogLocationCredential**, le cas échéant. Le certificat doit être approvisionné sur tous les nœuds du contrôleur de réseau avant d’exécuter cette commande et le même certificat doit être inscrit sur tous les nœuds du cluster. L’utilisation de ce paramètre pour protéger les fichiers binaires et les journaux du contrôleur de réseau est recommandée dans les environnements de production. Sans ce paramètre, les informations d’identification sont stockées en texte clair et peuvent être utilisées de façon incorrecte par tout utilisateur non autorisé.|
|Informations d'identification|Ce paramètre est obligatoire uniquement si vous exécutez cette commande à partir d’un ordinateur distant. Le paramètre **Informations d’identification** spécifie un compte d’utilisateur qui a l’autorisation d’exécuter cette commande sur l’ordinateur cible.|
|CertificateThumbprint|Ce paramètre est obligatoire uniquement si vous exécutez cette commande à partir d’un ordinateur distant. Le paramètre **CertificateThumbprint** spécifie le certificat de clé publique numérique (x509) d’un compte d’utilisateur qui a l’autorisation d’exécuter cette commande sur l’ordinateur cible.|
|UseSSL|Ce paramètre est obligatoire uniquement si vous exécutez cette commande à partir d’un ordinateur distant. Le paramètre **UseSSL** spécifie que le protocole SSL (Secure Sockets Layer) utilisé pour établir une connexion à l'ordinateur distant. Par défaut, SSL n'est pas utilisé.|
|ComputerName|Le paramètre **ComputerName** spécifie le nœud du contrôleur de réseau sur lequel cette commande est exécutée. Si vous ne spécifiez pas de valeur pour ce paramètre, l’ordinateur local est utilisé par défaut.|
|LogSizeLimitInMBs|Ce paramètre spécifie la taille maximale du journal, en Mo, que le contrôleur de réseau peut stocker. Les journaux sont stockés de manière circulaire. Si DiagnosticLogLocation est fourni, la valeur par défaut de ce paramètre est 40 Go. Si DiagnosticLogLocation n’est pas fourni, les journaux sont stockés sur les nœuds du contrôleur de réseau et la valeur par défaut de ce paramètre est 15 Go.|
|LogTimeLimitInDays|Ce paramètre spécifie la limite de durée, en jours, pendant laquelle les journaux sont stockés. Les journaux sont stockés de manière circulaire. La valeur par défaut de ce paramètre est 3 jours.|

## <a name="configure-the-network-controller-application"></a>Configurer l’application du contrôleur de réseau

Pour configurer l’application du contrôleur de réseau, saisissez la commande suivante. Veillez à utiliser des valeurs pour chaque paramètre qui conviennent à votre déploiement.

```powershell
Install-NetworkController -Node <NetworkControllerNode[]> -ClientAuthentication <ClientAuthentication>  [-ClientCertificateThumbprint <string[]>]  [-ClientSecurityGroup <string>] -ServerCertificate <X509Certificate2> [-RESTIPAddress <String>] [-RESTName <String>] [-Credential <PSCredential>][-CertificateThumbprint <String> ] [-UseSSL]
```

La table suivante fournit des descriptions de chaque paramètre de la commande `Install-NetworkController`.

|Paramètre|Description|
|-------------|---------------|
|ClientAuthentication|Le paramètre **ClientAuthentication** spécifie le type d’authentification utilisé pour sécuriser la communication entre REST et le contrôleur de réseau. Les valeurs prises en charge sont **Kerberos**, **x509** et **Aucun**. L’authentification Kerberos utilise des comptes de domaine et peut uniquement être utilisée si les nœuds du contrôleur de réseau sont joints à un domaine. Si vous spécifiez l’authentification basée sur x509, vous devez fournir un certificat dans l’objet NetworkControllerNode. En outre, vous devez configurer manuellement le certificat avant d’exécuter cette commande.|
|Nœud|Le paramètre **Nœud** spécifie la liste des nœuds de contrôleur de réseau que vous avez créés à l’aide de la commande **New-NetworkControllerNodeObject**.|
|ClientCertificateThumbprint|Ce paramètre est requis uniquement lorsque vous utilisez l’authentification basée sur les certificats pour les clients du contrôleur de réseau. Le paramètre **ClientCertificateThumbprint** spécifie l’empreinte numérique du certificat qui est inscrit aux clients sur la couche Northbound.|
|ServerCertificate|Le paramètre **ServerCertificate** spécifie le certificat utilisé par le contrôleur de réseau pour prouver son identité aux clients. Le certificat de serveur doit inclure l’objectif d’authentification du serveur dans les extensions d’utilisation améliorée de la clé et doit être délivré au contrôleur de réseau par une autorité de certification approuvée par les clients.|
|RESTIPAddress|Vous n’avez pas besoin de spécifier une valeur pour **RESTIPAddress** avec un déploiement à un seul nœud du contrôleur de réseau. Pour les déploiements à plusieurs nœuds, le paramètre **RESTIPAddress** spécifie l’adresse IP du point de terminaison REST en notation CIDR. Par exemple, 192.168.1.10/24. La valeur du nom d’objet de **ServerCertificate** doit résoudre la valeur du paramètre **RESTIPAddress**. Ce paramètre doit être spécifié pour tous les déploiements de contrôleur de réseau à plusieurs nœuds lorsque tous les nœuds se trouvent sur le même sous-réseau. Si les nœuds se trouvent sur des sous-réseaux différents, vous devez utiliser le paramètre **Restname** au lieu d’utiliser **RESTIPAddress**.|
|RestName|Vous n’avez pas besoin de spécifier une valeur pour **RESTName** avec un déploiement à un seul nœud du contrôleur de réseau. La seule fois où vous devez spécifier une valeur pour **Restname** est lorsque les déploiements à plusieurs nœuds ont des nœuds qui se trouvent sur des sous-réseaux différents. Pour les déploiements à plusieurs nœuds, le paramètre **Restname** spécifie le nom de domaine complet du cluster du contrôleur de réseau.|
|ClientSecurityGroup|Le paramètre **ClientSecurityGroup** spécifie le nom du groupe de sécurité Active Directory dont les membres sont des clients du contrôleur de réseau. Ce paramètre est obligatoire uniquement si vous utilisez l’authentification Kerberos pour **ClientAuthentication**. Le groupe de sécurité doit contenir les comptes à partir desquels les API REST sont accessibles et vous devez créer le groupe de sécurité et ajouter des membres avant d’exécuter cette commande.|
|Informations d'identification|Ce paramètre est obligatoire uniquement si vous exécutez cette commande à partir d’un ordinateur distant. Le paramètre **Informations d’identification** spécifie un compte d’utilisateur qui a l’autorisation d’exécuter cette commande sur l’ordinateur cible.|
|CertificateThumbprint|Ce paramètre est obligatoire uniquement si vous exécutez cette commande à partir d’un ordinateur distant. Le paramètre **CertificateThumbprint** spécifie le certificat de clé publique numérique (x509) d’un compte d’utilisateur qui a l’autorisation d’exécuter cette commande sur l’ordinateur cible.|
|UseSSL|Ce paramètre est obligatoire uniquement si vous exécutez cette commande à partir d’un ordinateur distant. Le paramètre **UseSSL** spécifie que le protocole SSL (Secure Sockets Layer) utilisé pour établir une connexion à l'ordinateur distant. Par défaut, SSL n'est pas utilisé.|

Une fois la configuration de l’application de contrôleur de réseau terminée, le déploiement du contrôleur de réseau est terminé.

## <a name="network-controller-deployment-validation"></a>Validation du déploiement du contrôleur de réseau

Pour valider votre déploiement du contrôleur de réseau, vous pouvez ajouter des informations d’identification au contrôleur de réseau, puis récupérer les informations d’identification.

Si vous utilisez Kerberos comme type `ClientAuthentication`, l’appartenance au groupe **ClientSecurityGroup** que vous avez créé est requise pour effectuer cette procédure.

1. Sur un ordinateur client, si vous utilisez Kerberos comme type `ClientAuthentication`, connectez-vous à l’aide d’un compte d’utilisateur membre de votre groupe **ClientSecurityGroup**.

1. Dans PowerShell, tapez les commandes suivantes. Veillez à utiliser des valeurs pour chaque paramètre qui conviennent à votre déploiement.

    ```powershell
    $cred=New-Object Microsoft.Windows.Networkcontroller.credentialproperties
    $cred.type="usernamepassword"
    $cred.username="admin"
    $cred.value="abcd"

    New-NetworkControllerCredential -ConnectionUri "https://networkcontroller"-Properties $cred -ResourceId "cred1"
    ```

1. Pour récupérer les informations d’identification que vous avez ajoutées au contrôleur de réseau, saisissez la commande suivante. Veillez à utiliser des valeurs pour chaque paramètre qui conviennent à votre déploiement.

    ```powershell
    Get-NetworkControllerCredential -ConnectionUri https://networkcontroller -ResourceId cred1  
    ```

1. Consultez la sortie de la commande qui doit ressembler à l’exemple de sortie suivant.

    ```powershell
    Tags                   :
    ResourceRef     : /credentials/cred1
    CreatedTime    : 1/1/0001 12:00:00 AM
    InstanceId        : e16ffe62-a701-4d31-915e-7234d4bc5a18
    Etag                  : W/"1ec59631-607f-4d3e-ac78-94b0822f3a9d"
    ResourceMetadata :
    ResourceId       : cred1
    Properties       : Microsoft.Windows.NetworkController.CredentialProperties
    ```

    > [!NOTE]
    > Quand vous exécutez la commande `Get-NetworkControllerCredential`, vous pouvez attribuer la sortie de la commande à une variable à l’aide de l’opérateur point pour répertorier les propriétés des informations d’identification. Par exemple : `$cred.Properties`.

## <a name="additional-powershell-commands-for-network-controller"></a>Commandes PowerShell supplémentaires pour le contrôleur de réseau

Après avoir déployé le contrôleur de réseau, vous pouvez utiliser des commandes PowerShell pour gérer et modifier votre déploiement. Voici quelques-unes des modifications que vous pouvez apporter à votre déploiement.

- Modifier les paramètres du nœud, du cluster et de l’application du contrôleur de réseau

- Supprimer le cluster et l’application du contrôleur de réseau

- Gérer des nœuds de cluster du contrôleur de réseau, notamment ajouter, supprimer, activer et désactiver des nœuds.

La table suivante fournit la syntaxe des commandes PowerShell que vous pouvez utiliser pour accomplir ces tâches.

|Tâche|Commande|Syntaxe|
|--------|-------|----------|
|Modifier les paramètres de cluster du contrôleur de réseau|Set-NetworkControllerCluster|`Set-NetworkControllerCluster [-ManagementSecurityGroup <string>][-Credential <PSCredential>] [-computerName <string>][-CertificateThumbprint <String> ] [-UseSSL]`
|Modifier les paramètres d’application du contrôleur de réseau|Set-NetworkController|`Set-NetworkController [-ClientAuthentication <ClientAuthentication>] [-Credential <PSCredential>] [-ClientCertificateThumbprint <string[]>] [-ClientSecurityGroup <string>] [-ServerCertificate <X509Certificate2>] [-RestIPAddress <String>] [-ComputerName <String>][-CertificateThumbprint <String> ] [-UseSSL]`
|Modifier les paramètres de nœud du contrôleur de réseau|Set-NetworkControllerNode|`Set-NetworkControllerNode -Name <string> > [-RestInterface <string>] [-NodeCertificate <X509Certificate2>] [-Credential <PSCredential>] [-ComputerName <string>][-CertificateThumbprint <String> ] [-UseSSL]`
|Modifier les paramètres de diagnostic du contrôleur de réseau|Set-NetworkControllerDiagnostic|`Set-NetworkControllerDiagnostic [-LogScope <string>] [-DiagnosticLogLocation <string>] [-LogLocationCredential <PSCredential>] [-UseLocalLogLocation] >] [-LogLevel <loglevel>][-LogSizeLimitInMBs <uint32>] [-LogTimeLimitInDays <uint32>] [-Credential <PSCredential>] [-ComputerName <string>][-CertificateThumbprint <String> ] [-UseSSL]`
|Supprimer l’application du contrôleur de réseau|Uninstall-NetworkController|`Uninstall-NetworkController [-Credential <PSCredential>][-ComputerName <string>] [-CertificateThumbprint <String> ] [-UseSSL]`
|Supprimer le cluster du contrôleur de réseau|Uninstall-NetworkControllerCluster|`Uninstall-NetworkControllerCluster [-Credential <PSCredential>][-ComputerName <string>][-CertificateThumbprint <String> ] [-UseSSL]`
|Ajouter un nœud au cluster du contrôleur de réseau|Add-NetworkControllerNode|`Add-NetworkControllerNode -FaultDomain <String> -Name <String> -RestInterface <String> -Server <String> [-CertificateThumbprint <String> ] [-ComputerName <String> ] [-Credential <PSCredential> ] [-Force] [-NodeCertificate <X509Certificate2> ] [-PassThru] [-UseSsl]`
|Désactiver le nœud de cluster du contrôleur de réseau|Disable-NetworkControllerNode|`Disable-NetworkControllerNode -Name <String> [-CertificateThumbprint <String> ] [-ComputerName <String> ] [-Credential <PSCredential> ] [-PassThru] [-UseSsl]`
|Activer le nœud de cluster du contrôleur de réseau|Enable-NetworkControllerNode|`Enable-NetworkControllerNode -Name <String> [-CertificateThumbprint <String> ] [-ComputerName <String> ] [-Credential <PSCredential> ] [-PassThru] [-UseSsl]`
|Supprimer un nœud de contrôleur de réseau d’un cluster|Remove-NetworkControllerNode|`Remove-NetworkControllerNode [-CertificateThumbprint <String> ] [-ComputerName <String> ] [-Credential <PSCredential> ] [-Force] [-Name <String> ] [-PassThru] [-UseSsl]`

Pour plus d’informations, consultez la documentation de référence de Windows PowerShell pour le contrôleur de réseau sur [NetworkController](/powershell/module/networkcontroller/?view=win10-ps).

## <a name="sample-network-controller-configuration-script"></a>Exemple de script de configuration du contrôleur de réseau

L’exemple de script de configuration suivant montre comment créer un cluster du contrôleur de réseau à plusieurs nœuds et installer l’application du contrôleur de réseau. En outre, la variable `$cert` sélectionne un certificat dans le magasin de certificats de l’ordinateur local qui correspond à la chaîne du nom d’objet « networkController.contoso.com ».

```powershell
$a = New-NetworkControllerNodeObject -Name "Node1" -Server "NCNode1.contoso.com" -FaultDomain "fd:/rack1/host1" -RestInterface Internal
$b = New-NetworkControllerNodeObject -Name "Node2" -Server "NCNode2.contoso.com" -FaultDomain "fd:/rack1/host2" -RestInterface Internal
$c = New-NetworkControllerNodeObject -Name "Node3" -Server "NCNode3.contoso.com" -FaultDomain "fd:/rack1/host3" -RestInterface Internal

$cert= get-item Cert:\LocalMachine\My | get-ChildItem | where {$_.Subject -imatch "networkController.contoso.com" }

Install-NetworkControllerCluster -Node @($a,$b,$c)  -ClusterAuthentication Kerberos -DiagnosticLogLocation \\share\Diagnostics - ManagementSecurityGroup Contoso\NCManagementAdmins -CredentialEncryptionCertificate $cert  
Install-NetworkController -Node @($a,$b,$c) -ClientAuthentication Kerberos -ClientSecurityGroup Contoso\NCRESTClients -ServerCertificate $cert -RestIpAddress 10.0.0.1/24
```

## <a name="next-steps"></a>Étapes suivantes

Si vous n’utilisez pas Kerberos avec votre déploiement de contrôleur de réseau, vous devez déployer des certificats. Pour plus d’informations, consultez [Étapes de post-déploiement pour le contrôleur de réseau](https://docs.microsoft.com/windows-server/networking/sdn/technologies/network-controller/post-deploy-steps-nc).
