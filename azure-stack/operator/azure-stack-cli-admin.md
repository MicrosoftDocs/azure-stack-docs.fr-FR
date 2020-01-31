---
title: Activer Azure CLI pour les utilisateurs d’Azure Stack Hub
description: Découvrez comment activer l’interface de ligne de commande (CLI) multiplateforme pour gérer et déployer des ressources sur Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 07cc157290280dbfd435dbcda6939d62388ba7d9
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76879004"
---
# <a name="enable-azure-cli-for-azure-stack-hub-users"></a>Activer Azure CLI pour les utilisateurs d’Azure Stack Hub

Vous pouvez fournir le certificat racine de l’autorité de certification aux utilisateurs d’Azure Stack Hub afin qu’ils puissent utiliser Azure CLI sur leurs machines de développement. Vos utilisateurs ont besoin du certificat pour gérer des ressources à l'aide de l'interface CLI.

 - Le **certificat racine de l’autorité de certification Azure Stack Hub** est impératif si des utilisateurs utilisent l’interface CLI sur une station de travail située en dehors du Kit de développement Azure Stack (ASDK).  

 - Le **point de terminaison des alias de machines virtuelles** fournit un alias, comme « UbuntuLTS » ou « Win2012Datacenter », qui référence un éditeur, une offre, une référence SKU et une version d’image sous la forme d’un seul paramètre lors du déploiement de machines virtuelles.  

Les sections suivantes expliquent comment obtenir ces valeurs.

## <a name="export-the-azure-stack-hub-ca-root-certificate"></a>Exporter le certificat de l'autorité de certification racine Azure Stack Hub

Si vous utilisez un système intégré, vous n'avez pas besoin d'exporter le certificat racine de l'autorité de certification. Vous devez exporter le certificat racine de l’autorité de certification du Kit de développement Azure Stack (ASDK).

Pour exporter le certificat racine du kit ASDK au format PEM, connectez-vous et exécutez le script suivant :

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Write-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-vm-aliases-endpoint"></a>Configurer le point de terminaison des alias de machines virtuelles

Nous recommandons aux opérateurs Azure Stack Hub de configurer un point de terminaison accessible publiquement hébergeant un fichier d’alias de machines virtuelles. Le fichier d’alias de machines virtuelles est un fichier JSON qui fournit un nom commun pour une image. Vous utiliserez ce nom lorsque vous déploierez une machine virtuelle en tant que paramètre Azure CLI.  

Avant d'ajouter une entrée à un fichier d'alias, pensez à [télécharger les images à partir de la Place de marché Azure](azure-stack-download-azure-marketplace-item.md) ou à [publier votre propre image personnalisée](azure-stack-add-vm-image.md). Si vous publiez une image personnalisée, prenez note des informations concernant l’éditeur, l’offre, la référence SKU et la version que vous avez spécifiées lors de la publication. S’il s’agit d’une image provenant de la Place de marché, vous pouvez afficher les informations en utilisant la cmdlet `Get-AzureVMImage`.  

Un [exemple de fichier d’alias](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) contenant de nombreux alias d’images communs est disponible. Vous pouvez l’utiliser comme point de départ. Hébergez ce fichier dans un espace accessible à vos clients utilisant l’interface CLI. Une façon de le faire consiste à héberger le fichier dans un compte Stockage Blob et à partager l’URL avec vos utilisateurs :

1. Téléchargez l’[ exemple de fichier](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) à partir de GitHub.
2. Créez un compte de stockage dans Azure Stack Hub. Puis créez un conteneur d'objets blob. Définissez la stratégie d’accès sur « publique ».  
3. Chargez le fichier JSON dans le nouveau conteneur. Au terme de cette opération, vous pouvez afficher l'URL de l'objet blob. Sélectionnez le nom de l’objet blob, puis sélectionnez l’URL dans les propriétés de l’objet blob.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer des modèles avec l’interface de ligne de commande Azure](../user/azure-stack-deploy-template-command-line.md )
- [Se connecter avec PowerShell](azure-stack-powershell-install.md)
- [Gérer les autorisations utilisateur](azure-stack-manage-permissions.md)
