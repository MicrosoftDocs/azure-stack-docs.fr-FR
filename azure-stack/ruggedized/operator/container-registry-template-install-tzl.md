---
title: Ajouter un registre de conteneurs à Azure Stack Hub | Microsoft Docs
titleSuffix: Azure Stack
description: Apprenez à ajouter un registre de conteneurs sur la Place de marché Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/10/2020
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 12/17/2019
ms.openlocfilehash: 5d97f12e6bc933edf7b5b335ebd52a86a1a7f01d
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96940977"
---
# <a name="add-a-container-registry-to-azure-stack-hub"></a>Ajouter un registre de conteneurs à Azure Stack Hub

Vous pouvez ajouter un registre de conteneurs sur votre Place de marché Azure Stack Hub afin de permettre à vos utilisateurs de déployer et de gérer leur propre registre de conteneurs. Ce modèle de solution installe et configure le service open source Docker Container Registry dans le cadre d'un abonnement utilisateur exécuté sur l'image de base AKS Ubuntu 16.04-LTS. Le modèle prend en charge les déploiements connectés et déconnectés ainsi qu’Azure Active Directory (AAD) et les services de fédération Active Directory (AD FS) déployés sur Azure Stack Hub.

## <a name="get-the-marketplace-item"></a>Accéder à l'élément publié sur la Place de marché

L'élément **Modèle de registre de conteneurs** de la Place de marché se trouve dans le référentiel GitHub suivant : https://github.com/msazurestackworkloads/azurestack-gallery/releases/download/registry-v1.0.2/Microsoft.AzureStackContainerRegistry.1.0.2.azpkg. L'élément de la Place de marché est accessible à partir du portail avec certains abonnements.

Vous pouvez également ajouter l'élément (sideloading) à votre Place de marché à l'aide du fichier Microsoft.AzureStackDockerContainerRegistry.1.0.2.azpkg. Les scripts décrits dans cet article sont accessibles en téléchargeant le référentiel git sous forme de package (zip) à partir de https://github.com/msazurestackworkloads/azurestack-gallery/archive/master.zip, et en extrayant les fichiers. Le script se trouve dans le dossier `azurestack-gallery-master\registry\scripts`.

## <a name="prerequisites"></a>Prérequis

Vous devez disposer des éléments suivants avant d'ajouter l'élément Container Registry de la Place de marché sur Azure Stack Hub.

| Élément | Type | Détails |
| --- | --- | --- |
| Modules Azure Stack Hub PowerShell (Azs.Gallery.Admin) | Modules PowerShell | Uniquement requis en cas de sideloading de l'élément Modèle de registre de conteneurs de la galerie, les modules Azure Stack Hub PowerShell sont utilisés pour ajouter et supprimer des éléments de la galerie.<br>[Installer les modules Azure Stack PowerShell](../../operator/azure-stack-powershell-install.md) |
| Modèle de registre de conteneurs | Article de la Place de marché | Pour déployer le registre de conteneurs en tant qu'utilisateur Azure Stack Hub, l'élément Modèle de registre de conteneurs de la Place de marché doit être disponible dans votre abonnement, ou être ajouté manuellement (par sideloading) sur votre Place de marché Azure Stack Hub. En cas de sideloading, suivez les instructions pour transférer le package contenu dans le fichier `readme.md` vers le [référentiel GitHub](https://github.com/msazurestackworkloads/azurestack-gallery/releases/tag/registry-v1.0.1). |
| Image de base AKS Ubuntu 16.04-LTS, version minimale de septembre 2019 | Article de la Place de marché | Pour permettre aux utilisateurs de votre instance d'Azure Stack Hub de déployer le registre de conteneurs, vous devez mettre l'image de base AKS à leur disposition sur la Place de marché. Le Modèle de registre de conteneurs utilise l'image lorsqu'une machine virtuelle Ubuntu héberge les binaires du service Docker Container Registry. |
| Extension de script personnalisé Linux 2.0 | Article de la Place de marché | Pour permettre aux utilisateurs de votre instance d'Azure Stack Hub de déployer l'Extension de script personnalisé Linux, vous devez mettre l'image de base AKS à leur disposition sur la Place de marché. Le Modèle de registre de conteneurs utilise l'extension pour configurer le registre. |
| Certificat SSL | Certificat | Les utilisateurs qui déploient le Modèle de registre de conteneurs doivent fournir un certificat PFX utilisé lors de la configuration du chiffrement SSL pour le service de registre. Si vous utilisez le script, vous devez exécuter la session PowerShell à partir d'une invite avec élévation de privilèges. Celle-ci ne doit pas être exécutée sur le DVM ou sur le HLH.<br>Pour obtenir des conseils d'ordre général sur les exigences relatives aux certificats PKI Azure Stack Hub utilisant des certificats publics ou privés/d'entreprise, consultez [Exigences de certificat pour infrastructure à clé publique (PKI) Azure Stack Hub](../../operator/azure-stack-pki-certs.md).<br>Le nom de domaine complet du certificat doit suivre le modèle `<vmname>.<location>.cloudapp.<fqdn>`, sauf en cas d'utilisation d'une entrée de domaine/DNS personnalisée pour le point de terminaison. Le nom doit commencer par une lettre, contenir au moins deux lettres, n'utiliser que des minuscules et comporter au moins trois caractères. |
| Nom de principal du service (SPN) | Inscription d’application | Pour déployer et configurer le registre de conteneurs, une inscription d'application, également appelée Nom de principal de service (ou SPN), doit être créée. Ce SPN est utilisé lors de la configuration de la machine virtuelle et du registre pour accéder aux ressources de Microsoft Azure Key Vault et du compte de stockage créées avant le déploiement de l'élément de la Place de marché.<br>Dans AAD, le SPN doit être créé au sein du locataire auquel vous vous connectez sur le portail utilisateur d’Azure Stack Hub. Si vous utilisez AD FS, il sera créé dans le répertoire local.<br>Pour plus d’informations sur la création d’un SPN dans le cadre des méthodes d’authentification d’AAD et d’AD FS, consultez les [instructions d’aide suivantes](../../operator/azure-stack-create-service-principals.md).<br> **Important !** Vous devez enregistrer l'ID d'application et la clé secrète du SPN pour déployer les éventuelles mises à jour.<br> |
| Nom d'utilisateur et mot de passe du registre | Informations d’identification | Le service open source Docker Container Registry est déployé et configuré avec l'authentification de base. Pour accéder au registre à l'aide des commandes Docker afin d'envoyer et d'extraire des images, un nom d'utilisateur et un mot de passe sont requis. Le nom d'utilisateur et le mot de passe sont stockés en lieu sûr dans un magasin Key Vault.<br>**Important !** Vous devez enregistrer le nom d'utilisateur et le mot de passe du registre pour vous connecter à celui-ci et envoyer/extraire des images. |
| Clé publique/privée SSH | Informations d’identification | Afin de résoudre les problèmes de déploiement ou d'exécution de la machine virtuelle, une clé publique SSH doit être fournie pour le déploiement, et la clé privée correspondante doit être accessible. Il est recommandé d'utiliser le format openssh, ssh-keygen, pour générer la paire de clés privée/publique, car ce format est requis par les scripts de diagnostic afin de collecter les journaux.<br>**Important !** Vous devez avoir accès aux clés publiques et privées pour accéder à la machine virtuelle déployée à des fins de résolution des problèmes. |
| Accès aux portails administrateur et utilisateur et aux points de terminaison de gestion | Connectivité | Ce guide part du principe que vous déployez et configurez le registre à partir d'un système connecté au système Azure Stack Hub. |

Le script `Pre-reqs` crée les autres entrées nécessaires au déploiement de l'élément de la Place de marché.

## <a name="installation-steps"></a>Procédure d’installation :

L'installation du Modèle de registre de conteneurs nécessite la création de plusieurs ressources avant le déploiement.

1. Connectez-vous à Azure Stack Hub en tant qu'utilisateur à l'aide de PowerShell, puis sélectionnez un abonnement grâce à la cmdlet `Select-AzureRmSubscription –Subscription <subscription guid>`. Pour plus d'informations sur la connexion à Azure Stack Hub PowerShell en tant qu'utilisateur, consultez [Se connecter à Azure Stack en tant qu'utilisateur avec PowerShell](../../user/azure-stack-powershell-configure-user.md).

2. Exécutez `Import-Modules .\\pre-reqs.ps1` pour importer les modules dans le script `pre-reqs.ps1`. Le script crée un groupe de ressources, un compte de stockage, un conteneur d'objets blob et un magasin Key Vault, attribue des autorisations d'accès au SPN et copie les certificats, le nom d'utilisateur et le mot de passe du registre dans le magasin Key Vault.

3. Exécutez la cmdlet suivante à partir d'une invite avec élévation de privilèges en utilisant les valeurs de votre environnement pour les paramètres :

    ```powershell  
         Set-ContainerRegistryPrerequisites -Location Shanghai `
         -ServicePrincipalId <spn app id> `
         -ResourceGroupName newregreq1 `
         -StorageAccountName newregsa1 `
         -StorageAccountBlobContainer newregct1 `
         -KeyVaultName newregkv1 `
         -CertificateSecretName containersecret2 `
         -CertificateFilePath C:\crinstall\shanghairegcert.pfx `
         -CertificatePassword <cert password> `
         -RegistryUserName admin `
         -RegistryUserPassword <password> 
    ```

    | Paramètre | Détails |
    | --- | --- |
    | $Location | Il s'agit de ce que l'on appelle parfois le nom de la région. |
    | $ResourceGroupName | Spécifiez le nom du groupe de ressources pour lequel vous souhaitez créer le compte de stockage et le magasin Key Vault. Vous spécifierez un autre groupe de ressources lors du déploiement de l'élément de la Place de marché. |
    | $StorageAccountName | Spécifiez le nom du compte de stockage à créer pour le registre de conteneurs ; vous l'utiliserez lors du stockage des images qui ont été envoyées. |
    | $StorageAccountBlobContainer | Spécifiez le nom du conteneur d'objets blob à créer pour le stockage des images. |
    | $KeyVaultName | Spécifiez le nom du magasin Key Vault à créer pour stocker le certificat et la valeur du nom d'utilisateur et du mot de passe. |
    | $CertificateSecretName | Fournissez le nom de la clé secrète créée dans Key Vault pour stocker le certificat PFX. |
    | $CertificateFilePath | Fournissez le chemin d'accès au certificat PFX. |
    | $CertificatePassword | Fournissez le mot de passe du certificat PFX. |
    | $ServicePrincipalId | Fournissez l'AppID du SPN. |
    | $RegistryUserName | Fournissez le nom d'utilisateur pour l'accès au service de registre via l'autorisation de base. |
    | $RegistryUserPassword | Fournissez le mot de passe de l'utilisateur du registre. |

1. Une fois le script terminé, notez que la fin de celui-ci comprend les paramètres à utiliser pour le déploiement du modèle. Lors du copier-coller de ces valeurs, un espace peut être introduit si la valeur est renvoyée à la ligne.

    ```powershell  
    ----------------------------------------------------------------
    PFX KeyVaultResourceId       : /subscriptions/<subcription id>/resourceGroups/newr
    egreg1/providers/Microsoft.KeyVault/vaults/newregkv1
    PFX KeyVaultSecretUrl        : https://newregkv1.vault.shanghai.azurestack.corp.microsoft.com:443/secr
    ets/containersecret1/37cc2f7ea1c44ad7b930e2c237a14949
    PFX Certificate Thumbprint   : 64BD5F3BC41DCBC6495998900ED322D8110DE25E
    ----------------------------------------------------------------
    StorageAccountResourceId     : /subscriptions/<subcription id>/resourcegroups/newr
    egreg1/providers/Microsoft.Storage/storageAccounts/newregsa1
    Blob Container               : newregct1
    ----------------------------------------------------------------
    
    Skus : aks-ubuntu-1604-201909
    
    
    Skus : aks-ubuntu-1604-201910
    
    ---------------------------------------------------------------- 
    
    ```

1. Connectez-vous au portail utilisateur Azure Stack Hub.

2. Sélectionnez **Créer** > **Calculer** > **Modèle de registre de conteneurs**.

    ![Modèle de registre de conteneurs](./media/container-registry-template-install-tzl/image1.png)

3. Sélectionnez l'abonnement, le groupe de ressources et l'emplacement où vous souhaitez déployer le modèle de registre de conteneurs.

    ![Modèle de registre de conteneurs](./media/container-registry-template-install-tzl/image2.png)

4. Renseignez les détails de la configuration de la machine virtuelle. Par défaut, la référence SKU de l'image est **aks-ubuntu-1604-201909**, mais la sortie de la fonction `Set-ContainerRegistryPrerequisites` comprend la liste des références SKU qui peuvent être utilisées pour le déploiement. En présence de plusieurs références SKU, choisissez la plus récente pour le déploiement.

    ![Modèle de registre de conteneurs](./media/container-registry-template-install-tzl/image3.png)

    | Paramètre | Détails |
    | --- | --- |
    | Nom d’utilisateur | Fournissez le nom d'utilisateur pour la connexion à la machine virtuelle. |
    | Clé publique SSH | Fournissez la clé publique SSH utilisée pour l'authentification auprès de la machine virtuelle à l'aide du protocole SSH. |
    | Taille | Sélectionnez la taille de la machine virtuelle à déployer. |
    | Adresse IP publique | Spécifiez le nom et le type d'adresse IP (dynamique ou statique) pour cette machine virtuelle. Le nom de domaine n'est pas valide. Il ne peut contenir que des lettres minuscules, des chiffres et des traits d’union. Le premier caractère doit être une lettre. Le dernier caractère doit être une lettre ou un chiffre. La valeur doit comporter 3 à 63 caractères.  |
    | Étiquette de nom de domaine | Spécifiez le préfixe DNS de votre registre. Le nom de domaine complet doit correspondre à la valeur CN du certificat PFX créé pour le registre. |
    | Réplicas | Spécifiez le nombre de réplicas de conteneur à démarrer. |
    | Référence d’image | Spécifiez la référence SKU d'image à utiliser pour le déploiement. Les références SKU disponibles pour l'image de base AKS sont énumérées par le script `Set-ContainerRegistryPrerequisites`. |
    | ID client du SPN | Spécifiez l'ID d'application du SPN. |
    | Mot de passe / Confirmer le mot de passe du SPN | Spécifiez la clé secrète de l'ID d'application du SPN. |

1. Procédez à la configuration des services Stockage et Key Vault.

    ![Modèle de registre de conteneurs](./media/container-registry-template-install-tzl/image4.png)

    | Paramètre | Détails |
    | --- | --- |
    | ID de ressource du compte de stockage étendu existant | Spécifiez l'ID de ressource du compte de stockage renvoyé par le script `pre-reqs`. |
    | Conteneur d'objets blob back-end existant | Spécifiez le nom du conteneur d'objets blob, indiqué dans la sortie du script pre-reqs. |
    | ID de ressource Key Vault du certificat PFX | Spécifiez l'ID de ressource Key Vault renvoyé par le script `pre-reqs`. |
    | URL de la clé secrète Key Vault du certificat PFX | Spécifiez l'URL du certificat telle que renvoyée par le script pre-reqs. |
    | Empreinte du certificat PFX | Spécifiez l'empreinte numérique du certificat telle que renvoyée par le script pre-reqs. |

1. Une fois toutes les valeurs fournies et le déploiement du modèle de solution lancé, 10 à 15 minutes sont nécessaires à la machine virtuelle pour déployer et configurer le service de registre.

    ![Modèle de registre de conteneurs](./media/container-registry-template-install-tzl/image5.png)

2. Pour tester le registre, ouvrez une instance d'interface CLI Docker à partir d'un ordinateur ou d'une machine virtuelle qui a accès à l'URL du registre.

    > [!Note]
    >  Si vous avez utilisé un certificat auto-signé ou un certificat inconnu de la machine virtuelle que vous utilisez pour accéder au registre, vous devez installer ce certificat sur la machine virtuelle et redémarrer Docker.

## <a name="pushing-and-pulling-images-from-container-registry"></a>Envoyer et extraire des images à partir du registre de conteneurs

1. Connectez-vous à l'aide de `docker login –u \<username> -p \<password>`.
2. Extrayez une image à partir d'un registre connu.
3. Marquez l'image pour cibler le service Docker Container Registry nouvellement déployé.
4. Envoyez l'image vers le nouveau registre cible.

Par exemple :

```powershell  
PS C:\> docker pull mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
1.0: Pulling from azureiotedge-simulated-temperature-sensor
5d20c808ce19: Already exists
656de8e592c3: Already exists
1e1868d1f676: Already exists
f3fb1b0d620f: Pulling fs layer
26224c4fc11a: Pulling fs layer
c459a69d65b2: Pulling fs layer
c459a69d65b2: Verifying Checksum
c459a69d65b2: Download complete
f3fb1b0d620f: Download complete
f3fb1b0d620f: Pull complete
26224c4fc11a: Verifying Checksum
26224c4fc11a: Pull complete
c459a69d65b2: Pull complete
Digest: sha256:dd64ff0918459184574e840ee97aa9f1bacd40aa37c972984ea10f0ecd719d5f
Status: Downloaded newer image for mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

PS C:\> docker tag mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0    myreg.orlando.cloudapp.azurestack.corp.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

PS C:\> docker login -u admin -p admin myreg.orlando.cloudapp.azurestack.corp.microsoft.com
docker : WARNING! Using --password via the CLI is insecure. Use --password-stdin.
At line:1 char:1
+ docker login -u admin -p admin myreg.orlando.cloudapp.azurestack.corp ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (WARNING! Using ...password-stdin.:String) [], RemoteException
    + FullyQualifiedErrorId : NativeCommandError
 
Login Succeeded

PS C:\> docker push myreg.orlando.cloudapp.azurestack.corp.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
The push refers to repository [myreg.orlando.cloudapp.azurestack.corp.microsoft.com/azureiotedge-simulated-temperature-sensor]
d377c212e567: Preparing
0481685b758f: Preparing
15474c03a0b6: Preparing
8cdec5be5964: Preparing
79116d3fb0bf: Preparing
3fc64803ca2d: Preparing
3fc64803ca2d: Waiting
79116d3fb0bf: Mounted from azureiotedge-agent
8cdec5be5964: Mounted from azureiotedge-agent
15474c03a0b6: Pushed
d377c212e567: Pushed
3fc64803ca2d: Mounted from azureiotedge-agent
0481685b758f: Pushed
1.0: digest: sha256:f5fbc4a5c6806e12cafe1c363fea2b6cbd98a211b8153c5b19aca1386bfa6ecb size: 1576 

```

## <a name="known-issues"></a>Problèmes connus

La version du service Docker Container Registry déployée par ce modèle est la version 2.7. Un problème empêchant l'envoi et l'extraction des images de conteneurs Windows a été identifié sur cette version. Ce problème fait l'objet d'un suivi dans l'élément GitHub suivant : [https://github.com/docker/distribution-library-image/issues/89](https://github.com/docker/distribution-library-image/issues/89).

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de la Place de Marché Azure Stack](../../operator/azure-stack-marketplace.md)
