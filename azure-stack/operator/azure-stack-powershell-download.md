---
title: Télécharger les outils Azure Stack Hub à partir de GitHub
description: Découvrez comment télécharger les outils nécessaires pour utiliser Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 4/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 4/22/2020
ms.openlocfilehash: 9faa562f9b8ff339016473f1b3c0df0512c90988
ms.sourcegitcommit: 7b8e067cb449e67ca9c2935580684d78840ad495
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106922"
---
# <a name="download-azure-stack-hub-tools-from-github"></a>Télécharger les outils Azure Stack Hub à partir de GitHub

**AzureStack-Tools** est un [référentiel GitHub](https://github.com/Azure/AzureStack-Tools) qui héberge des modules PowerShell permettant de gérer et déployer des ressources sur Azure Stack Hub. Si vous prévoyez d’établir la connectivité VPN, vous pouvez télécharger ces modules PowerShell avec le Kit de développement Azure Stack (ASDK) ou sur un client externe Windows. 

## <a name="get-tools-for-azure-stack-hub-azurerm-module"></a>Obtenir les outils Azure Stack Hub pour le module AzureRM

Pour obtenir ces outils, clonez le dépôt GitHub à partir de la branche `master` ou téléchargez le dossier **AzureStack-Tools** en exécutant le script suivant :

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```
Pour plus d’informations sur l’utilisation du module AzureRM pour Azure Stack Hub, consultez [Installer le module PowerShell AzureRM pour Azure Stack Hub](azure-stack-powershell-install.md).

## <a name="get-tools-for-azure-stack-hub-az-preview-module"></a>Obtenir les outils Azure Stack Hub pour le module Az (préversion)

Pour obtenir ces outils, clonez le dépôt GitHub à partir de la branche `az` ou téléchargez le dossier **AzureStack-Tools** en exécutant le script suivant :

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/az.zip `
  -OutFile az.zip

# Expand the downloaded files.
expand-archive az.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
 cd AzureStack-Tools-az

```

Pour plus d’informations sur l’utilisation du module Az pour Azure Stack Hub, consultez [Installer le module PowerShell Az (préversion) pour Azure Stack Hub](powershell-install-az-module.md).

## <a name="functionality-provided-by-the-modules"></a>Fonctionnalités fournies par les modules

Le référentiel **AzureStack-Tools** contient des modules PowerShell qui prennent en charge les fonctionnalités suivantes pour Azure Stack Hub :  

| Fonctionnalités | Description | Qui peut utiliser ce module ? |
| --- | --- | --- |
| [Fonctionnalités de cloud](../user/azure-stack-validate-templates.md) | Utilisez ce module pour obtenir les fonctionnalités cloud d’un cloud. Par exemple, vous pouvez obtenir les fonctionnalités de cloud telles que la version d’API et les ressources Azure Resource Manager. Vous pouvez également obtenir les extensions de machine virtuelle pour Azure Stack Hub et les clouds Azure. | Les opérateurs et les utilisateurs de cloud |
| [Stratégie Resource Manager pour Azure Stack Hub](../user/azure-stack-policy-module.md) | Utilisez ce module pour configurer un abonnement Azure ou un groupe de ressources Azure avec la même gestion des versions et la même disponibilité de service qu’Azure Stack Hub. | Les opérateurs et les utilisateurs de cloud |
| [Inscription auprès d’Azure](azure-stack-registration.md ) | Utilisez ce module pour inscrire votre instance du Kit de développement Azure Stack auprès d’Azure. Une fois l’inscription effectuée, vous pouvez télécharger les éléments de la Place de Marché Azure et les utiliser dans Azure Stack Hub. | Les opérateurs de cloud |
| [Déploiement d'Azure Stack Hub](../asdk/asdk-install.md) | Utilisez ce module pour préparer l’ordinateur hôte Azure Stack Hub au déploiement et au redéploiement à l’aide de l’image de disque dur virtuel (VHD) Azure Stack Hub. | Les opérateurs de cloud|
| [Connexion à Azure Stack Hub](azure-stack-powershell-install.md) | Ce module permet de configurer la connectivité VPN avec Azure Stack Hub. | Les opérateurs et les utilisateurs de cloud |
| [Validateur de modèle](../user/azure-stack-validate-templates.md) | Utilisez ce module pour vérifier si un modèle existant ou nouveau peut être déployé sur Azure Stack Hub. | Les opérateurs et les utilisateurs de cloud|

## <a name="next-steps"></a>Étapes suivantes

- [Bien démarrer avec PowerShell sur Azure Stack Hub](../user/azure-stack-powershell-overview.md).
- [Configurez l’environnement PowerShell de l’utilisateur Azure Stack Hub.](../user/azure-stack-powershell-configure-user.md)
- [Se connecter au Kit de développement Azure Stack par le biais d’un réseau privé virtuel](../asdk/asdk-connect.md).
