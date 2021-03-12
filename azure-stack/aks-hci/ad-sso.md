---
title: Utiliser l’authentification unique Active Directory dans AKS pour Azure Stack HCI
description: Utiliser l’authentification Active Directory pour se connecter de manière sécurisée au serveur d’API avec des informations d’identification d’authentification unique
author: v-susbo
ms.topic: how-to
ms.date: 02/12/2021
ms.author: v-susbo
ms.reviewer: ''
ms.openlocfilehash: fd4b8982ecaa9de1b3b63dd97cb460772e25971d
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "101874383"
---
# <a name="use-active-directory-single-sign-in-credentials-for-aks-on-azure-stack-hci"></a>Utiliser des informations d’identification d’authentification unique Active Directory pour AKS sur Azure Stack HCI

> S’applique à : AKS sur Azure Stack HCI, AKS Runtime sur Windows Server 2019 Datacenter

Sans l’authentification Active Directory, les utilisateurs doivent s’appuyer sur un fichier _kubeconfig_ basé sur un certificat lors de la connexion au serveur d’API via la commande `kubectl`. Le fichier _kubeconfig_ contient des secrets, tels que des clés privées et des certificats qui doivent être distribués avec soin. Cela peut constituer un risque de sécurité significatif.

Au lieu d’utiliser un fichier _kubeconfig_ basé sur un certificat, vous pouvez utiliser des informations d’identification d’authentification unique Active Directory (AD) comme méthode sécurisée pour vous connecter au serveur d’API. L’intégration d’AD à Azure Kubernetes Service sur Azure Stack HCI permet à un utilisateur d’un ordinateur Windows joint à un domaine de se connecter au serveur d’API (via `kubectl`) à l’aide de ses informations d’identification d’authentification unique. Cela évite de devoir gérer et distribuer des fichiers _kubeconfig_ basés sur des certificats qui contiennent des clés privées.

L’intégration AD utilise _kubeconfig_ AD, qui est différent des fichiers _kubeconfig_ basés sur des certificats et ne contient pas de secrets. Le fichier _kubeconfig_ basé sur un certificat peut toutefois être utilisé à des fins de sauvegarde, telles que la résolution des problèmes, en cas de problème de connexion avec les informations d’identification Active Directory.

L’intégration AD offre un autre avantage en matière de sécurité : les utilisateurs et les groupes sont stockés sous forme d’[identificateurs de sécurité (SID)](https://docs.microsoft.com/troubleshoot/windows-server/identity/security-identifiers-in-windows). Contrairement aux noms de groupe, les SID sont immuables et uniques, et ne présentent donc aucun conflit de nom.

> [!Note] 
> Pour cette préversion, nous assurons une connectivité d’authentification unique AD uniquement pour les clusters de charge de travail. 

Ce document vous guide tout au long des étapes suivantes pour configurer Active Directory comme fournisseur d’identité et activer l’authentification unique via `kubectl` :

- Créez le compte AD pour le serveur d’API, puis créez le fichier [keytab](https://web.mit.edu/kerberos/krb5-devel/doc/basic/keytab_def.html) associé au compte. Pour créer le compte AD et générer le fichier keytab, consultez [Créer une authentification Active Directory à l’aide du fichier keytab](#create-ad-auth-using-the-keytab-file).
- Utilisez le fichier [keytab](https://web.mit.edu/kerberos/krb5-devel/doc/basic/keytab_def.html) pour installer l’authentification AD sur le cluster Kubernetes. Dans le cadre de cette étape, une configuration de contrôle d’accès en fonction du rôle (RBAC) par défaut est automatiquement créée.
- Convertissez les noms de groupe en SID et inversement lors de la création ou de la modification des configurations RBAC. Pour obtenir des instructions, consultez [Créer et mettre à jour la liaison de rôle de groupe AD](#create-and-update-the-ad-group-role-binding).
 
## <a name="before-you-begin"></a>Avant de commencer

Avant de commencer le processus de configuration des informations d’identification d’authentification unique Active Directory, vous devez vérifier que les éléments suivants sont disponibles :

 - Le module **PowerShell Aks-Hci** le plus récent est installé. Si ce n’est pas le cas, consultez [Télécharger et installer le module PowerShell AksHci](./setup-powershell.md#step-1-download-and-install-the-akshci-powershell-module). 
 - L’hôte AKS est installé et configuré. Si ce n’est pas le cas, suivez la procédure [Configurer votre déploiement](./setup-powershell.md#step-4-configure-your-deployment).  
 - Assurez-vous que le fichier keytab peut être utilisé. Si ce n’est pas le cas, consultez [Créer le compte AD du serveur d’API et le fichier keytab](#create-the-api-server-ad-account-and-the-keytab-file). 
 - Le fichier keytab est généré pour un nom de principal du service (SPN) spécifique et ce SPN doit correspondre au compte AD du serveur d’API pour le cluster de charge de travail. Vous devez également vous assurer que le même SPN est utilisé tout au long du processus d’authentification Active Directory. Le fichier keytab doit être nommé _current.keytab_.
 - Pour chaque cluster de charge de travail AKS sur Azure Stack HCI, vérifiez qu’un compte AD du serveur d’API est disponible.
 - L’ordinateur client doit être un ordinateur Windows joint à un domaine.

## <a name="create-ad-auth-using-the-keytab-file"></a>Créer une authentification Active Directory à l’aide du fichier keytab

### <a name="step-1-create-the-workload-cluster-and-enable-ad-authentication"></a>Étape 1 : Créer le cluster de charge de travail et activer l’authentification AD

Avant d’installer l’authentification AD, vous devez d’abord créer un cluster de charge de travail AKS sur Azure Stack HCI et activer le module complémentaire d’authentification Active Directory sur le cluster. Si vous n’activez pas l’authentification Active Directory lors de la création du cluster, vous ne pourrez pas l’activer ultérieurement.

Ouvrez PowerShell en tant qu’administrateur et exécutez la commande suivante à l’aide du paramètre **-enableADAuth** de la commande `New-AksHciCluster` :

```powershell
New-AksHciCluster -name mynewcluster1 -enableADAuth
```

Pour chaque cluster de charge de travail, vérifiez qu’un compte AD du serveur d’API est disponible.

Pour plus d’informations sur la création du cluster de charge de travail, consultez [Créer des clusters Kubernetes avec Windows PowerShell](./create-kubernetes-cluster-powershell.md).

### <a name="step-2-install-ad-authentication"></a>Étape 2 : Installer l’authentification AD

Avant de pouvoir installer l’authentification Active Directory, vérifiez que le cluster de charge de travail est installé et que l’authentification AD est activée sur le cluster. Pour installer l’authentification AD, utilisez l’une des options suivantes.

#### <a name="option-1"></a>Option 1 :

Pour un cluster Azure Stack HCI joint à un domaine, ouvrez PowerShell en tant qu’administrateur et exécutez la commande suivante :

```powershell
Install-AksHciAdAuth -name mynewcluster1 -keytab .\current.keytab -SPN k8s/apiserver@CONTOSO.COM -adminUser contoso\bob
```  

#### <a name="option-2"></a>Option 2 :

Si l’hôte de cluster n’est pas joint à un domaine, utilisez le nom d’utilisateur administrateur ou le nom de groupe au format SID, comme indiqué dans l’exemple ci-dessous :
 
```powershell
Install-AksHciAdAuth -name mynewcluster1 -keytab .\current.keytab -SPN k8
```  

Pour rechercher le SID du compte d’utilisateur, consultez [Déterminer l’identificateur de sécurité de l’utilisateur ou du groupe](#determine-the-user-or-group-security-identifier). 

Avant de passer aux étapes suivantes, vous devez noter les points suivants :

- Assurez-vous que le fichier keytab est nommé _current.keytab_.
- Remplacez le SPN qui correspond à votre environnement.
- **-adminUser** crée une liaison de rôle correspondante pour le groupe AD spécifié avec des privilèges d’administrateur de cluster. Remplacez _contoso\bob_ par le groupe ou l’utilisateur AD qui correspond à votre environnement.

### <a name="step-3-test-the-ad-webhook-and-keytab-file"></a>Étape 3 : Tester le Webhook AD et le fichier keytab

Vous devez vérifier que le Webhook AD est en cours d’exécution sur le serveur d’API et que le fichier keytab est stocké en tant que secret Kubernetes. Pour obtenir le fichier _kubeconfig_ basé sur un certificat pour le cluster de charge de travail, procédez comme suit :

1. Procurez-vous un fichier _kubeconfig_ basé sur un certificat. Vous allez utiliser le fichier _kubeconfig_ pour vous connecter au cluster en tant qu’hôte local à l’aide de la commande suivante :

   ```powershell
   Get-AksHciCredential -name mynewcluster1
   ```  

2. Exécutez `kubectl` sur le serveur auquel vous vous êtes connecté (avec le fichier _kubeconfig_ basé sur un certificat que vous avez créé précédemment), puis vérifiez le déploiement du Webhook AD pour vous assurer qu’il est au format _ad-auth-webhook-xxxx_.  

    ```bash
    kubectl get pods -n=kube-system
    ```

3. Exécutez `kubectl` pour vérifier que le fichier keytab est déployé en tant que secret et qu’il est listé comme secret Kubernetes : 

   ```bash
   kubectl get secrets -n=kube-system
   ```

### <a name="step-4-create-the-ad-kubeconfig-file"></a>Étape 4 : Créer le fichier kubeconfig AD

Une fois que le Webhook AD et le fichier keytab sont correctement déployés, créez le fichier _kubeconfig_ AD.
Une fois le fichier créé, copiez le fichier _kubeconfig_ AD sur l’ordinateur client et utilisez-le pour vous authentifier auprès du serveur d’API. Contrairement au fichier _kubeconfig_ basé sur un certificat, _kubeconfig_ AD n’est pas un secret et peut être copié en texte brut en toute sécurité.

Ouvrez PowerShell en tant qu’administrateur et exécutez la commande suivante :  

   ```powershell
   Get-AksHciCredential -name mynewcluster1 -outputLocation .\AdKubeconfig -adAuth
   ```

### <a name="step-5-copy-kubeconfig-and-other-files-to-the-client-machine"></a>Étape 5 : Copier kubeconfig et d’autres fichiers sur l’ordinateur client

Vous devez copier les trois fichiers listés ci-dessous à partir du cluster Azure Stack HCI sur votre ordinateur client :

- Copiez le fichier _kubeconfig_ AD créé à l’étape précédente vers $env:USERPROFILE\.kube\config.

- Créez le chemin du dossier `c:\adsso` et copiez les fichiers suivants à partir du cluster Azure Stack HCI sur votre ordinateur client.
  - Kubectl.exe sous `$env:ProgramFiles\AksHci` vers c:\adsso
  - Kubectl-adsso.exe sous `$env:ProgramFiles\AksHci` vers c:\adsso

### <a name="step-6-connect-to-the-api-server-from-the-client-machine"></a>Étape 6 : Se connecter au serveur d’API à partir de l’ordinateur client

Une fois que vous avez effectué les étapes précédentes, connectez-vous à votre ordinateur client Windows joint à un domaine à l’aide de vos informations d’identification d’authentification unique. Ouvrez PowerShell, puis essayez d’accéder au serveur d’API avec `kubectl`. Si vous êtes invité à vous authentifier, cela signifie que vous avez correctement configuré l’authentification unique AD.

## <a name="create-and-update-the-ad-group-role-binding"></a>Créer et mettre à jour la liaison de rôle de groupe AD

Comme indiqué à l’étape 2, une liaison de rôle par défaut avec des privilèges d’administrateur de cluster est créée pour l’utilisateur et/ou le groupe qui a été fourni lors de l’installation. La liaison de rôle dans Kubernetes définit les stratégies d’accès pour les groupes AD. Cette étape explique comment utiliser RBAC pour créer des liaisons de rôle de groupe AD dans Kubernetes et modifier des liaisons de rôle existantes. Par exemple, l’administrateur de cluster peut accorder des privilèges supplémentaires aux utilisateurs à l’aide de groupes AD (ce qui rend le processus plus efficace). Pour en savoir plus sur le contrôle d’accès en fonction du rôle, consultez [Utilisation de l’autorisation RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/).

Lors de la création ou de la modification d’autres entrées RBAC du groupe AD, le nom de l’objet doit être précédé du préfixe « `microsoft:activedirectory:CONTOSO\\group name` ». Notez que les noms doivent contenir un nom de domaine et un préfixe encadrés par des guillemets doubles.

Voici deux exemples :

**Exemple 1**

```bash
apiVersion: rbac.authorization.k8s.io/v1 
 kind: ClusterRoleBinding 
 metadata: 
   name: ad-user-cluster-admin 
 roleRef: 
   apiGroup: rbac.authorization.k8s.io 
   kind: ClusterRole 
   name: cluster-admin 
 subjects: 
 - apiGroup: rbac.authorization.k8s.io 
   kind: User 
   name: "microsoft:activedirectory:CONTOSO\Bob" 
```

**Exemple 2**

L’exemple ci-dessous montre comment créer un rôle personnalisé et une liaison de rôle pour un [espace de noms](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) avec un groupe AD. Dans l’exemple, « SREGroup » est un groupe préexistant dans Active Directory Contoso. Lorsque des utilisateurs sont ajoutés au groupe AD, ils reçoivent immédiatement des privilèges.

```bash
kind: Role 
 apiVersion: rbac.authorization.k8s.io/v1 
 metadata: 
   name: sre-user-full-access 
   namespace: sre 
 rules: 
 - apiGroups: ["", "extensions", "apps"] 
   resources: ["*"] 
   verbs: ["*"] 
 - apiGroups: ["batch"] 
   resources: 
   - jobs 
   - cronjobs 
   verbs: ["*"] 
 apiVersion: rbac.authorization.k8s.io/v1 
 kind: RoleBinding 
 namespace: sre 
 metadata: 
   name: ad-user-cluster-admin 
 roleRef: 
   apiGroup: rbac.authorization.k8s.io 
   kind: Role 
   name: sre-user-full-access 
 subjects: 
 - apiGroup: rbac.authorization.k8s.io 
   kind: User 
   name: "microsoft:activedirectory:CONTOSO\SREGroup" 
```

Avant d’appliquer le fichier yaml, les **noms de groupe et d’utilisateur** doivent toujours être convertis en SID à l’aide de la commande :

```bash
kubectl-adsso nametosid <rbac.yml>
```  

De même, pour mettre à jour un RBAC existant, vous pouvez convertir le SID en un nom de groupe convivial avant d’apporter des modifications. Pour convertir le SID, utilisez la commande : 

```bash
kubectl-adsso sidtoname <rbac.yml>
```

## <a name="change-the-ad-account-password-associated-with-the-api-server-account"></a>Modifier le mot de passe du compte AD associé au compte du serveur d’API

Lorsque le mot de passe du compte du serveur d’API est modifié, vous devez désinstaller le module complémentaire d’authentification Active Directory et le réinstaller à l’aide des fichiers keytab actuel et précédent mis à jour. 

Chaque fois que vous modifiez le mot de passe, vous devez renommer le keytab actuel (_current.keytab_) en _previous.keytab_, puis veiller à nommer le nouveau mot de passe _current.keytab_.

> [!Important] 
> Les fichiers doivent être nommés _current.keytab_ et _previous.keytab_, respectivement. Les liaisons de rôle existantes ne sont pas affectées par cette opération.

### <a name="uninstall-and-reinstall-ad-authentication"></a>Désinstaller et réinstaller l’authentification AD

Vous pouvez réinstaller l’authentification unique AD quand le compte du serveur d’API est modifié, lorsque le mot de passe est mis à jour ou pour résoudre un problème.

Pour désinstaller l’authentification Active Directory, ouvrez PowerShell en tant qu’administrateur et exécutez la commande suivante :  

```powershell
Uninstall-AksHciAdAuth -name mynewcluster1
```

Pour réinstaller l’authentification Active Directory, ouvrez PowerShell en tant qu’administrateur et exécutez la commande suivante :

```powershell
Install-AksHciAdAuth -name mynewcluster1 -keytab <.\current.keytab> -previousKeytab <.\previous.keytab> -SPN <service/principal@CONTOSO.COM> -adminUser CONTOSO\Bob
```

> [!Note] 
> Le paramètre `-previousKeytab` n’est requis que lorsque vous modifiez le mot de passe. Cela permet d’éviter les temps d’arrêt si les clients ont des tickets mis en cache.

## <a name="create-the-api-server-ad-account-and-the-keytab-file"></a>Créer le compte AD du serveur d’API et le fichier keytab

Deux étapes constituent ce processus. Tout d’abord, créez un compte/utilisateur AD pour le serveur d’API avec le nom de principal du service (SPN) et, ensuite, créez un fichier keytab pour le compte AD.

### <a name="step-1-create-a-new-ad-account-or-user-for-the-api-server"></a>Étape 1 : Créer un compte ou un utilisateur AD pour le serveur d’API

Utilisez la commande PowerShell [New-ADUser](https://docs.microsoft.com/powershell/module/addsadministration/new-aduser?view=win10-ps&preserve-view=true) pour créer un compte/utilisateur AD avec le SPN. Voici un exemple : 

```powershell 
New-ADUser -Name apiserver -ServicePrincipalNames k8s/apiserver -AccountPassword (ConvertTo-SecureString "password" -AsPlainText -Force) -KerberosEncryptionType AES128 -Enabled 1
```

### <a name="step-2-create-the-keytab-file-for-the-ad-account"></a>Étape 2 : Créer le fichier keytab pour le compte AD

Pour créer le fichier keytab, utilisez la commande Windows [ktpass](https://docs.microsoft.com/windows-server/administration/windows-commands/ktpass). 

Voici un exemple d’utilisation de ktpass :

```bash
ktpass /out current.keytab /princ k8s/apiserver@BCONTOSO.COM /mapuser contoso\apiserver_acct /crypto all /pass p@$$w0rd /ptype KRB5_NT_PRINCIPAL
```

> [!NOTE]
> Si vous voyez une erreur du type **DsCrackNames a retourné 0x2 dans l’entrée de nom**, vérifiez que le paramètre pour `/mapuser` est sous la forme `mapuser DOMAIN\user` où DOMAIN est la sortie d’écho `%userdomain%`.

  
## <a name="determine-the-user-or-group-security-identifier"></a>Déterminer l’identificateur de sécurité de l’utilisateur ou du groupe

Utilisez l’une des deux options suivantes pour rechercher le SID de votre compte ou d’autres comptes.

Pour rechercher le SID associé à votre compte, à partir d’une invite de commandes de votre répertoire de base, tapez ce qui suit :

```bash
whoami /user
``` 

Pour rechercher le SID associé à un autre compte, ouvrez PowerShell en tant qu’administrateur et exécutez la commande suivante :

```powershell
(New-Object System.Security.Principal.NTAccount(<CONTOSO\Bob>)).Translate([System.Security.Principal.SecurityIdentifier]).value
```

## <a name="next-steps"></a>Étapes suivantes 

Dans ce guide pratique, vous avez appris à configurer l’authentification AD pour vous connecter de manière sécurisée au serveur d’API avec des informations d’identification d’authentification unique. À présent, vous pouvez :

- [Déployer des applications Linux sur un cluster Kubernetes](./deploy-linux-application.md).
- [Déployer une application Windows Server sur un cluster Kubernetes](./deploy-windows-application.md).
