---
title: Résoudre les problèmes de validation en tant que service
titleSuffix: Azure Stack Hub
description: Résolvez les problèmes de validation en tant que service pour Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f9a3b4b3ac61447a3dca567ad9ebcb636c8f9bc3
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874623"
---
# <a name="troubleshoot-validation-as-a-service"></a>Résoudre les problèmes de validation en tant que service

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Voici les problèmes courants, sans lien avec les versions logicielles, et leurs solutions.

## <a name="local-agent"></a>Agent local

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>Le portail affiche l’agent local en mode débogage

Ce problème vient probablement du fait que l’agent ne peut pas envoyer de pulsations au service, car la connexion réseau est instable. Une pulsation est envoyée toutes les cinq minutes. Si le service ne reçoit pas de pulsation pendant 15 minutes, il considère que l’agent est inactif et plus aucun test n’est planifié pour ce service. Vérifiez le message d’erreur dans le fichier *Agenthost.log*, il se trouve dans le répertoire où l’agent a été démarré.

> [!Note]
> Tous les tests déjà en cours d’exécution sur l’agent continuent de s’exécuter, mais si la pulsation n’est pas restaurée avant la fin du test, l’agent ne peut pas mettre à jour l’état du test, ni charger les journaux. Le test apparaît toujours comme étant **en cours d’exécution** et il doit être annulé.

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>Le processus de l’agent sur la machine a été arrêté pendant l’exécution du test. À quoi s’attendre ?

Si le processus de l’agent s’arrête de manière inappropriée, le test qui s’exécutait sur celui-ci continuera de s’afficher comme étant **en cours d’exécution**. Un exemple d’arrêt inapproprié est lorsque l’ordinateur est redémarré et que le processus est tué (pour un arrêt normal, vous devez appuyer sur CTRL+C dans la fenêtre de l’agent). Si l’agent est redémarré, il met à jour l’état du test qui est **annulé**. Si l’agent n’est pas redémarré, le test s’affiche comme étant **en cours d’exécution**, et vous devez annuler manuellement le test.

> [!Note]
> Les tests au sein d’un workflow sont planifiés pour une exécution séquentielle. Les tests **en attente** ne sont pas exécutés tant que les tests affichant l’état **en cours d’exécution** dans ce même workflow ne sont pas terminés.

## <a name="vm-images"></a>Images de machine virtuelle

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>Une défaillance se produit lors du chargement d’une image de machine virtuelle dans le script `VaaSPreReq`
Reportez-vous à la section ci-dessous intitulée **Gérer les problèmes de lenteur de la connexion réseau**. Elle présenter les étapes manuelles permettant de charger les images de machine virtuelle sur une empreinte Azure Stack.

### <a name="handle-slow-network-connectivity"></a>Gérer les problèmes de lenteur de la connexion réseau

#### <a name="1-verify-that-the-environment-is-healthy"></a>1. Vérifier que l’environnement est sain

1. À partir du DVM/de la zone de saut, vérifiez que vous pouvez vous connecter au portail d’administration à l’aide des informations d’identification de l’administrateur.

2. Vérifiez qu’aucune alerte ou qu’aucun avertissement ne s’affiche.

3. Si l’environnement est sain, chargez manuellement les images de machine virtuelle nécessaires aux séries de tests VaaS, comme décrit dans les étapes de la section ci-dessous :

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="2-download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>2. Télécharger l’image PIR sur un partage local en cas de trafic réseau lent

1. Téléchargez AzCopy à partir de : [vaasexternaldependencies(AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip).

2. Extrayez AzCopy.zip et accédez au répertoire contenant `AzCopy.exe`.

3. Ouvrez Windows PowerShell à partir d’une invite de commandes avec élévation des privilèges. Exécutez les commandes suivantes :

```powershell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'OpenLogic-CentOS-69-20180105.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Debian8_latest.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare représente le chemin de partage ou le chemin local.

#### <a name="3-verifying-pir-image-file-hash-value"></a>3. Vérification de la valeur de hachage du fichier Image PIR

Vous pouvez utiliser l’applet de commande **Get-HashFile** pour obtenir la valeur de hachage des fichiers image du dépôt d’images publiques téléchargées pour vérifier l’intégrité des images.

| Nom de fichier | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL.vhd | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL.vhd | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL.vhd | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS.vhd | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619.1.vhd | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |
| OpenLogic-CentOS-69-20180105.vhd | C8B874FE042E33B488110D9311AF1A5C7DC3B08E6796610BF18FDD6728C7913C |
| Debian8_latest.vhd | 06F8C11531E195D0C90FC01DFF5DC396BB1DD73A54F8252291ED366CACD996C1 |

#### <a name="4-upload-vm-images-to-a-storage-account"></a>4. Charger les images de machine virtuelle sur un compte de stockage

1. Utilisez un compte de stockage existant ou créez un nouveau compte de stockage dans Azure.

2. Créez un conteneur sur lequel charger les images.

3. Utilisez l’outil Azcopy pour charger les images de machine virtuelle du [*LocalFileShare*] ci-dessus (où vous avez téléchargé les images de machine virtuelle) sur le conteneur que vous venez de créer.
    > [!IMPORTANT]
    > Remplacez « Niveau d’accès public » du conteur par « Blob (accès en lecture anonyme pour les objets blob uniquement) ».

#### <a name="5-upload-vm-images-to-azure-stack-environment"></a>5. Charger les images de machine virtuelle dans l’environnement Azure Stack

1. Connectez-vous au portail d’administration en tant qu’administrateur de service. L’URL du portail d’administration se trouve dans le fichier d’informations de tampon ou ECE. Pour obtenir des instructions, consultez la section [Paramètres d’environnement](azure-stack-vaas-parameters.md#environment-parameters).

2. Sélectionnez **Autres services** > **Fournisseurs de ressources** > **Compute** > **Images de VM**.

3. Sélectionnez le bouton **+Ajouter** situé en haut du panneau **Images de VM**.

4. Vérifiez ou modifiez les valeurs des champs suivants pour la première image de machine virtuelle :

    > [!IMPORTANT]
    > Toutes les valeurs par défaut ne sont pas correctes pour l’élément existant de la Place de marché.

    | Champ  | Valeur  |
    |---------|---------|
    | Serveur de publication | MicrosoftWindowsServer |
    | Offre | WindowsServer |
    | Type de système d'exploitation | Windows |
    | SKU | 2012-R2-Datacenter |
    | Version | 1.0.0 |
    | URI de l’objet blob du disque du système d’exploitation | https://<*Votre compte de stockage*>/<*nom du conteneur*>/WindowsServer2012R2DatacenterBYOL.vhd |


5. Cliquez sur le bouton **Créer**.

6. Répétez cette opération pour les images restantes de machine virtuelle.

Les propriétés de toutes les images de machine virtuelle nécessaires sont :

| Serveur de publication  | Offre  | Type de système d'exploitation | SKU | Version | URI de l’objet blob du disque du système d’exploitation |
|---------|---------|---------|---------|---------|---------|
| MicrosoftWindowsServer| WindowsServer | Windows | 2012-R2-Datacenter | 1.0.0 | https://[*Votre compte de stockage*]/[*nom du conteneur*]/WindowsServer2012R2DatacenterBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-centre-de-données | 1.0.0 | https://[*Votre compte de stockage*]/[*nom du conteneur*]/Server2016DatacenterFullBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016-Datacenter-Server-Core | 1.0.0 | https://[*Votre compte de stockage*]/[*nom du conteneur*]/Server2016DatacenterCoreBYOL.vhd |
| Canonical | UbuntuServer | Linux | 14.04.3-LTS | 1.0.0 | https://[*Votre compte de stockage*]/[*nom du conteneur*]/Ubuntu1404LTS.vhd |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://[*Votre compte de stockage*]/[*nom du conteneur*]/Ubuntu1604-20170619.1.vhd |
| OpenLogic | CentOS | Linux | 6.9 | 1.0.0 | https://[*Votre compte de stockage*]/[*nom du conteneur*]/OpenLogic-CentOS-69-20180105.vhd |
| Credativ | Debian | Linux | 8 | 1.0.0 | https://[*Votre compte de stockage*]/[*nom du conteneur*]/Debian8_latest.vhd |

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les changements des dernières versions, consultez les [Notes de publication de la validation en tant que service](azure-stack-vaas-release-notes.md).