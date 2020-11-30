---
title: Guide d’utilisation de l’outil de restauration automatique Azure Site Recovery
description: Découvrez comment utiliser l’outil de restauration automatique Azure Site Recovery pour protéger les machines virtuelles.
author: sethmanheim
ms.author: sethm
ms.date: 11/19/2020
ms.topic: how-to
ms.reviewer: rtiberiu
ms.lastreviewed: 11/19/2020
ms.openlocfilehash: 0cb3bccab11d337a8a8804578233edb95ac02dc6
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517223"
---
# <a name="azure-site-recovery-failback-tool"></a>Outil de restauration automatique Azure Site Recovery

Dans un environnement connecté, vous pouvez utiliser Azure Site Recovery pour protéger les machines virtuelles qui s’exécutent sur Azure Stack Hub. [Cet article](/azure/site-recovery/azure-stack-site-recovery) explique comment configurer l’environnement et comment Site Recovery contribue à la stratégie globale de récupération d’urgence et de continuité des activités pour ces charges de travail.

En cas de panne, l’opérateur Azure Stack Hub passe par la procédure de *basculement* une fois qu’Azure Stack Hub est de nouveau opérationnel, il passe par un processus de *restauration automatique*. La procédure de basculement est décrite dans [cet article sur la récupération de site](/azure/site-recovery/azure-stack-site-recovery), mais le processus de restauration automatique implique quelques étapes manuelles :

1. Arrêtez d’exécuter la machine virtuelle dans Azure.
2. Téléchargez les disques durs virtuels (VHD).
3. Chargez les VHD sur Azure Stack Hub.
4. Recréez les machines virtuelles.
5. Enfin, démarrez cette machine virtuelle s’exécutant sur Azure Stack Hub. 

Étant donné que ce processus peut être source d’erreurs et fastidieux, nous avons créé des scripts pour accélérer et automatiser ce processus.

> [!Note]  
> L’outil Azure Site Recovery nécessite les modules Az d’Azure Azure Hub. Si vous exécutez les modules AzureRM d’Azure Stack Hub, vous devez mettre à niveau votre station de travail ou utiliser l’outil de restauration automatique Azure Site Recovery dans un environnement isolé avec les modules Az. Pour plus d’informations, consultez [Installer le module PowerShell Az pour Azure Stack Hub](powershell-install-az-module.md).

## <a name="failback-procedure"></a>Procédure de restauration automatique

Le processus de restauration automatique contient trois parties principales :

- **Copy-AzSiteRecoveryVmVHD :**
  - Arrête la machine virtuelles Azure.
  - Prépare l’exportation du disque.
  - Copie le disque via AzCopy ou StorageBlobCopy.
  - Charge le disque sur un compte de stockage Azure Stack Hub.

- Une fois le disque copié, deux scénarios sont traités via **Prepare-AzSiteRecoveryVMFailBack** :
  - Azure Stack Hub d’origine a été récupéré. La machine virtuelle d’origine existe toujours et vous devez uniquement modifier ses VHD.
  - En cas de sinistre, si les machines virtuelles d’origine sont perdues, vous devez recréer la machine virtuelle entière.

  Cette procédure couvre les deux scénarios en créant le modèle et le fichier de paramètres requis.

- Le déploiement réel du modèle Azure Resource Manager à l’aide du fichier de paramètres et le déploiement/la création de la machine virtuelle sur Azure Stack Hub.

### <a name="prerequisites"></a>Prérequis

Les conditions préalables suivantes sont requises pour effectuer la procédure de restauration automatique :

- Copiez [l’outil de restauration automatique Azure Site Recovery](https://aka.ms/azshasr).

- Importez le module FailbackTool.psm1 dans PowerShell.

- Suivez la procédure décrite dans [cet article pour installer le module Az for Azure Stack Hub](powershell-install-az-module.md).

- (facultatif) [Télécharger AzCopy version 10](/azure/storage/common/storage-use-azcopy-v10).

  - La copie de l’objet blob à l’aide de **AzCopy** est plus rapide, mais nécessite un espace disque local supplémentaire pour stocker temporairement le fichier blob.
  - Si **AzCopy** n’est pas utilisé, la copie du disque dur virtuel s’effectue à l’aide de **AzStorageBlobCopy**. Cela signifie qu’aucun stockage local n’est requis, mais le processus prend plus de temps.

- Accès aux ressources sur le portail Microsoft Azure et accès pour créer ces ressources sur Azure Stack Hub.

## <a name="step-1-copy-blob-from-azure-to-azure-stack-hub"></a>Étape 1 : Copier un objet blob à partir d’Azure vers Azure Stack Hub

Appelez l’applet de commande PowerShell **Copy-AzSiteRecoveryVmVHD** pour arrêter la machine virtuelle Azure, téléchargez les VHD à partir d’Azure et chargez-les sur Azure Stack Hub. Par exemple :

```powershell
$uris = Copy-AzSiteRecoveryVmVHD `
        -SourceVM $vmOnAzure `
        -TargetStorageAccountName "targetaccountName" `
        -TargetStorageEndpoint "redmond.ext-v.masd.stbtest.microsoft.com" `
        -TargetStorageAccountKey $accountKey `
        -AzCopyPath "C:\azcopy_v10\azcopy.exe" `
        -VhdLocalFolder "C:\tempfolder"
```

Tenez compte des points suivants :

- Cet exemple utilise `$uris`pour contenir la `SourceDiskVhdUris` valeur utilisée à l’étape 2.

- Le paramètre `-SourceVM` est un objet de machine virtuelle récupéré par `Get-AzVM`.
  - Il s’agit de la machine virtuelle protégée d’Azure Stack Hub qui a été basculée sur Azure.
  - Peu importe si la machine virtuelle est en cours d’exécution, car le script l’arrête. Toutefois, il est recommandé de l’arrêter explicitement et d’arrêter les services à l’intérieur de la machine virtuelle en conséquence.

- Vous pouvez fournir une clé de compte (à l’aide de `TargetStorageAccountKey`) ou le jeton SAS (à l’aide de `TargetStorageAccountSasToken`) du compte de stockage côté Azure Stack Hub. Le jeton SAS doit être créé au niveau du compte de stockage, avec au moins les autorisations suivantes :

   :::image type="content" source="media/site-recovery-failback/sasperms.png" alt-text="Autorisations du jeton SAS":::

- Vous pouvez fournir le point de terminaison de stockage, qui comprend la région et le nom de domaine complet (FQDN). par exemple, `regionname.azurestack.microsoft.com`, ou le nom d’environnement d’Azure Stack Hub, tel que `AzureStackTenant`. Si le nom de l’environnement est utilisé, il doit être indiqué à l’aide de **Get-AzEnvironment**.

- Vous pouvez choisir d’utiliser **AzCopy** ou **AzStorageBlobCopy** pour copier le disque dur virtuel à partir d’Azure vers Azure Stack Hub. **AzCopy** est plus rapide, mais il doit d’abord télécharger les fichiers VHD dans un dossier local :
  - Pour utiliser **AzCopy**, indiquez les paramètres `-AzCopyPath` et `-VhdLocalFolder` (le chemin d’accès où les disques durs virtuels seront copiés).
  - Si l’espace local est insuffisant, vous pouvez choisir de copier le disque dur virtuel directement, sans **AzCopy**, en omettant les paramètres `-AzCopyPath` et `-VhdLocalFolder`. Par défaut, cette commande utilise **AzStorageBlobCopy** pour copier directement sur le compte de stockage Azure Stack Hub.

## <a name="step-2-generate-resource-manager-templates"></a>Étape 2 : Générer des modèles Resource Manager

Une fois le disque copié, utilisez l’applet de commande **Prepare-AzSiteRecoveryVMFailBack** pour créer les `$templateFile` et `$parameterFile` requis pour déployer la machine virtuelle sur Azure Stack Hub :

```powershell
$templateFile, $parameterFile = Prepare-AzSiteRecoveryVMFailBack `
                                -SourceContextName "PublicAzure" `
                                -SourceVM $vmOnAzure `
                                -SourceDiskVhdUris $uris `
                                -TargetResourceLocation "redmond" `
                                -ArmTemplateDestinationPath "C:\ARMtemplates" `
                                -TargetVM $vmOnHub `
                                -TargetContextName "AzureStack"

```

Tenez compte des points suivants :

- Cet exemple utilise `-SourceDiskVhdUris` comme valeur de retour de l’étape 1 (à l’aide de `$uris`).

- La nouvelle cmdlet prend en charge deux scénarios :
  - En spécifiant `-TargetVM`, vous supposez que la machine virtuelle est active côté Azure Stack Hub et vous souhaitez remplacer ses disques par les derniers copiés à partir d’Azure.
  - Le script génère un modèle Resource Manager pour déployer cette machine virtuelle, puis supprime la machine virtuelle existante d’Azure Stack Hub.
  
  > [!NOTE]
  > La suppression de la machine virtuelle Azure Stack Hub elle-même ne supprime pas les autres objets (comme VNET, groupe de ressources, groupes de sécurité réseau (NSG)). Elle supprime uniquement la ressource de machine virtuelle, puis le modèle est déployé avec le paramètre `-incremental`.

  - Si vous ne fournissez pas le paramètre `-TargetVM`, le script suppose que la machine virtuelle n’existe plus côté Azure Stack Hub, de sorte que le script crée un modèle Resource Manager pour déployer une machine virtuelle entièrement nouvelle.

- Les fichiers de modèle Resource Manager générés sont placés sous `-ArmTemplateDestinationPath` et le chemin d’accès complet du fichier de modèle ou du fichier de paramètres est renvoyé.

- Si le paramètre `-TargetVM` est fourni, l’applet de commande supprime la machine virtuelle. vous pouvez donc passer aux étapes suivantes.

## <a name="step-3-deploy-the-resource-manager-template"></a>Étape 3 : Déployer le modèle Resource Manager

À ce stade, le disque dur virtuel est chargé sur Azure Stack Hub et le modèle Resource Manager et les fichiers de paramètres respectifs sont créés. Il vous reste à déployer la machine virtuelle sur Azure Stack Hub.

Dans certains scénarios, vous souhaiterez peut-être modifier ce modèle et ajouter, supprimer ou modifier certains noms ou ressources. Cela est autorisé, car vous pouvez modifier et ajuster le modèle en fonction des besoins.

Lorsque vous êtes prêt, et après avoir confirmé que les ressources du modèle Resource Manager sont conformes, vous pouvez appeler l’applet de commande **New-AzResourceGroupDeployment** pour déployer les ressources. Par exemple :

```powershell
New-AzResourceGroupDeployment `
  -Name "Failback" `
  -ResourceGroupName "failbackrg" `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile `
  -Mode Incremental
```

Tenez compte des points suivants :

- Le paramètre `-ResourceGroupName` doit être un groupe de ressources existant.
- Les paramètres `-TemplateFile` et `-TemplateParameterFile` proviennent des valeurs renvoyées à l’étape 2.

## <a name="next-steps"></a>Étapes suivantes

- [Fonctionnalités des machines virtuelles Azure Stack Hub](../user/azure-stack-vm-considerations.md)
- [Ajouter et supprimer une image de machine virtuelle personnalisée dans Azure Stack Hub](azure-stack-add-vm-image.md)
- [Créer une machine virtuelle Windows à l'aide de PowerShell dans Azure Stack Hub](../user/azure-stack-quick-create-vm-windows-powershell.md)