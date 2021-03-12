---
title: Configurer les paramètres du proxy pour AKS sur Azure Stack HCI
description: Découvrez comment planifier et configurer la prise en charge du proxy pour la connexion à Internet.
author: abha
ms.topic: how-to
ms.date: 01/27/2021
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 83a3a4928f5df1b6b4520cdc6ffd277e497425ff
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874422"
---
# <a name="configure-proxy-settings-on-aks-on-azure-stack-hci"></a>Configurer les paramètres du proxy dans AKS sur Azure Stack HCI

Si votre réseau nécessite l’utilisation d’un serveur proxy pour se connecter à Internet, ce document vous guide tout au long des étapes à suivre pour configurer la prise en charge du proxy dans AKS sur Azure Stack HCI en utilisant les modules PowerShell AksHci et WinInetProxy. 

Si vous ne souhaitez pas utiliser WinInetProxy, vous pouvez aussi utiliser **Propriétés Internet** (inetcpl.cpl) et cliquer sur l’onglet **Connexions**, puis sur **Paramètres réseau** pour configurer et vérifier vos paramètres de serveur proxy.


## <a name="before-you-begin"></a>Avant de commencer

Installez le module PowerShell [WinInetProxy](https://www.powershellgallery.com/packages/WinInetProxy/0.1.0) pour configurer un serveur proxy en exécutant la commande suivante dans une fenêtre d’administration PowerShell :

```Powershell
Install-Module -Name WinInetProxy -Repository PSGallery
```

Pour d’autres prérequis, consultez [Configuration exigée](./system-requirements.md). Pour télécharger le module PowerShell AksHci, consultez [configurer le module PowerShell AksHci](./setup-powershell.md).

## <a name="configure-a-proxy-server-without-authentication"></a>Configurer un serveur proxy sans authentification

Pour configurer un serveur proxy qui n’exige pas d’authentification, exécutez la commande suivante sur chaque nœud de cluster Azure Stack HCI :

```powershell
Set-WinInetProxy -ProxyServer http://proxy.contoso.com:3128 -ProxyBypass "local"
```

## <a name="configure-a-proxy-server-with-authentication"></a>Configurer un serveur proxy avec authentification

Pour configurer un serveur proxy qui exige une authentification, vous devez configurer les informations d’identification de l’hôte AKS sur Azure Stack à utiliser avec le proxy WinINet. Ces informations sont différentes selon le type d’authentification demandé par le proxy, qu’il s’agisse de l’authentification de base ou NTLM/Kerberos.

> [!NOTE]
> Si vous souhaitez utiliser un certificat pour vous connecter au serveur proxy, vous êtes alors responsable du provisionnement de ce certificat dans le magasin de certificats approprié sur vos hôtes afin de garantir son approbation.

### <a name="configure-a-proxy-server-with-ntlmkerberos-authentication"></a>Configurer un serveur proxy avec l’authentification NTLM/Kerberos

Ajoutez de nouvelles informations d’identification Windows au moyen de la commande ci-dessous. Entrez votre mot de passe sécurisé lorsque vous y êtes invité. Vous pouvez également utiliser le Gestionnaire d’informations d’identification Windows sous **Informations d’identification Windows** pour ajouter une nouvelle entrée dans Informations d’identification Windows. 

```powershell
cmdkey /generic:proxy.contoso.com /user:username /pass
```
Lorsque vous exécutez cette commande, il vous est demandé d’entrer le mot de passe.

Dans le profil PowerShell, ajoutez la commande suivante pour autoriser les agents AKS-HCI à utiliser les informations d’identification mises en cache :

```powershell
Set-WinInetProxy -ProxyServer http://proxy.contoso.com:3128 -ProxyBypass "local"
notepad $PROFILE
[System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials
```  

### <a name="configure-a-proxy-server-with-basic-authentication"></a>Configurer un serveur proxy avec l’authentification de base

Vous devez ajouter les informations d’identification de l’authentification de base au profil PowerShell pour que le module de téléchargement de l’agent puisse s’en servir. 

> [!NOTE]
> Les informations d’identification du profil PowerShell ne sont pas chiffrées et s’affichent en texte clair. Vérifiez que le profil PowerShell est protégé par des listes de contrôle d’accès (ACL), pour que seuls les administrateurs et le compte LocalSystem puissent les afficher.

Modifiez le profil PowerShell pour remplacer le nom d’utilisateur et le mot de passe par les informations d’authentification de base, comme indiqué ci-dessous :

```powershell
Set-WinInetProxy -ProxyServer http://proxy.contoso.com:3128 -ProxyBypass "local"
notepad $profile
[System.Net.WebRequest]::DefaultWebProxy.Credentials = new-object System.Net.NetworkCredential("username", "password")
```

## <a name="deploy-aks-on-azure-stack-hci-host-using-a-proxy-server"></a>Déployer l’hôte AKS sur Azure Stack HCI à l’aide d’un serveur proxy

Dès que votre serveur proxy est configuré, vous pouvez définir des configurations de proxy pour l’installation d’un hôte AKS au moyen de la commande `Set-AksHciConfig`. Il existe plusieurs procédures selon que le serveur proxy exige ou non une authentification.

Lorsque vous avez configuré votre déploiement en utilisant les options ci-dessous, vous pouvez [installer un hôte AKS sur Azure Stack HCI](./setup-powershell.md) et [créer des clusters AKS à l’aide de PowerShell](./create-kubernetes-cluster-powershell.md).

### <a name="configure-an-aks-host-for-a-proxy-server-with-basic-authentication"></a>Configurer un hôte AKS pour un serveur proxy avec l’authentification de base  

Si votre serveur proxy exige une authentification, exécutez les commandes suivantes pour récupérer les informations d’identification, puis définissez les détails de la configuration.

```powershell
$proxyCred = Get-Credential
Set-AksHciConfig -proxyServerHTTP "http://proxy.contoso.com:8888" -proxyServerHTTPS "http://proxy.contoso.com:8888" -proxyServerCredential $ProxyCred
```

## <a name="configure-an-aks-host-for-a-proxy-server-with-ntlmkerberos-authentication"></a>Configurer un hôte AKS pour un serveur proxy avec l’authentification NTLM/Kerberos

```powershell
$credential = Get-Credential # get the credential for the proxy server
Set-AksHciConfig -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerCredential $credential
```

### <a name="configure-an-aks-host-for-a-proxy-server-without-authentication"></a>Configurer un hôte AKS pour un serveur proxy sans authentification  

Si votre serveur proxy n’exige pas d’authentification, ouvrez PowerShell en tant qu’administrateur et exécutez la commande suivante :

```powershell
Set-AksHciConfig -proxyServerHTTP "http://proxy.contoso.com:8888" -proxyServerHTTPS "http://proxy.contoso.com:8888"
```

### <a name="configure-an-aks-host-for-a-proxy-server-with-a-trusted-certificate"></a>Configurer un hôte AKS pour un serveur proxy avec un certificat approuvé

Si votre serveur proxy exige que les clients proxy approuvent un certificat, spécifiez le fichier de certificat lorsque vous exécutez `Set-AksHciConfig`. Le format du fichier de certificat est *X. 509 codé en base 64*. Le certificat sera ainsi provisionné et approuvé sur l’ensemble de la pile :

```powershell
Set-AksHciConfig -proxyServerHTTP "http://proxy.contoso.com:8888" -proxyServerHTTPS "http://proxy.contoso.com:8888" -proxyServerCertFile "C:\proxycertificate.crt"
```

> [!NOTE]
> Les certificats proxy ne sont pas provisionnés ou approuvés sur les nœuds Worker de Windows Kubernetes pour le moment. La prise en charge des Workers Windows sera activée dans une version ultérieure.


## <a name="exclude-specific-hosts-or-domains-from-using-the-proxy-server"></a>Exclure des hôtes ou des domaines spécifiques de l’utilisation du serveur proxy

Dans la plupart des réseaux, vous devez exclure certains réseaux, hôtes ou domaines d’un accès possible via le serveur proxy. Pour cela, vous pouvez exempter les chaînes d’adresse à l’aide du paramètre `-proxyServerNoProxy` dans `Set-AksHciConfig`.

La valeur par défaut pour `proxyServerNoProxy` est `localhost,127.0.0.1,.svc,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16`.

Lorsque vous exécutez cette commande, les éléments suivants sont exclus :

- Le trafic localhost (localhost, 127.0.0.1)
- Le trafic du service Kubernetes interne (.svc) où _.svc_ représente un nom générique. Ce qui revient à dire que *.svc uniquement* est utilisé dans ce schéma.
- L’espace d’adressage du réseau privé (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16). Notez que l’espace d’adressage du réseau privé contient des réseaux importants, tels que le CIDR de service Kubernetes (10.96.0.0/12) et le CIDR de pod Kubernetes (10.244.0.0/16).

Bien que ces valeurs par défaut fonctionnent pour de nombreux réseaux, vous aurez peut-être besoin d’ajouter d’autres plages de sous-réseaux et/ou de noms à la liste d’exemptions. Par exemple, vous pouvez souhaiter exempter votre espace de noms d’entreprise (.contoso.com) d’un transit via le proxy. Il suffit pour cela de spécifier les valeurs dans la liste proxyServerNoProxy :

```powershell
Set-AksHciConfig -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerNoProxy "localhost,127.0.0.1,.svc,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16,.contoso.com"
```

## <a name="next-steps"></a>Étapes suivantes

- [Déployer une application Linux sur votre cluster Kubernetes](./deploy-linux-application.md).
- [Déployer une application Windows sur votre cluster Kubernetes](./deploy-windows-application.md).
