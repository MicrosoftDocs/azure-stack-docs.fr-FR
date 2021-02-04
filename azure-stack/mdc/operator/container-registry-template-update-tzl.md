---
title: Mettre à jour le registre de conteneurs dans Azure Stack Hub - MDC
titleSuffix: Azure Stack
description: Découvrez comment mettre à jour le registre de conteneurs dans Azure Stack Hub pour des MDC (Modular Data Centers).
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
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 12/17/2019
ms.openlocfilehash: 8bc77528bec5fdb22e2ea83b45cc4eedc3b7984c
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99246378"
---
# <a name="update-the-container-registry-in-azure-stack-hub---modular-data-center-mdc"></a>Mettre à jour le registre de conteneurs dans Azure Stack Hub - Modular Data Center (MDC)

Les utilisateurs d’Azure Stack Hub peuvent mettre à jour leur déploiement de registre de conteneurs vers une référence SKU d’image de base AKS plus récente à l’aide des instructions ci-dessous. La machine virtuelle et le service du modèle de registre de conteneurs sont sans état, car toutes les images d’état et de conteneur sont stockées dans le stockage d’objets blob. Une mise à jour est aussi simple qu’un déploiement de modèle de registre de conteneurs avec une version plus récente du disque dur virtuel d’image de base AKS et un repointage DNS vers la nouvelle machine virtuelle. L’action de mise à jour de la valeur DNS des anciennes et nouvelles machines virtuelles de registre de conteneurs s’accompagne d’une petite fenêtre de connectivité de registre intermittente lors de la propagation des valeurs.

## <a name="prerequisites"></a>Prérequis

### <a name="operator"></a>Opérateur

- Syndiquez la dernière image de base AKS à partir de la Place de marché Azure Stack. L’image de base AKS est mise à jour une fois par mois.

  ![Image de base AKS](./media/container-registry-template-updating-tzl/aks-base-image.png)

### <a name="user"></a>Utilisateur

1.  Vérifiez la référence SKU de l’image de base AKS utilisée pour déployer le modèle de registre de conteneurs en accédant à l’enregistrement de déploiement dans le groupe de ressources, puis sélectionnez **Entrées**.

    ![Capture d’écran de la page « Entrées ».](./media/container-registry-template-updating-tzl/inputs.png)

2.  Déterminez si des références SKU plus récentes de l’image de base AKS sont disponibles à l’aide de la fonction **Get-VMImageSku**, `Import-Module .\pre-reqs.ps1` à partir des scripts de modèle de registre de conteneurs.

    ```powershell  
    PS C:\azurestack-galler-master\registry\Scripts> Get-VMImageSku -Location Shanghai
    
    Skus                  
    ----                  
    aks-ubuntu-1604-201909
    aks-ubuntu-1604-201910 
    ```

## <a name="parameters-required"></a>Paramètres requis

| Paramètre | Détails |
| --- | --- |
| Nom d’utilisateur | Fournissez le nom d'utilisateur pour la connexion à la machine virtuelle. |
| Clé publique SSH | Fournissez la clé publique SSH utilisée pour l'authentification auprès de la machine virtuelle à l'aide du protocole SSH. |
| Taille | Sélectionnez la taille de la machine virtuelle à déployer. |
| Adresse IP publique | Spécifiez le nom et le type d'adresse IP (dynamique/statique) pour cette machine virtuelle. |
| Étiquette de nom de domaine | Spécifiez le préfixe DNS de votre registre. Ce nom de domaine complet doit correspondre à la valeur CN du certificat PFX créé pour le registre. |
| Réplicas | Spécifiez le nombre de réplicas de conteneur à démarrer. |
| Référence d’image | Spécifiez la référence SKU d'image à utiliser pour le déploiement. Les références SKU disponibles pour l'image de base AKS sont énumérées par la cmdlet PowerShell **Get-VMImageSku**. |
| ID de client du principal du service | Spécifiez l’ID d’application du principal de service (SPN) tel qu’il est utilisé dans le déploiement précédent. |
| Mot de passe du principal du service / Confirmer le mot de passe | Spécifiez le secret de l’ID d’application SPN tel qu’il est utilisé dans le déploiement précédent. |
| ID de ressource du compte de stockage étendu existant | Spécifiez l’ID de ressource du compte de stockage tel qu’il est utilisé dans le déploiement précédent. |
| Conteneur d'objets blob back-end existant | Spécifiez le nom du conteneur d’objets blob tel qu’il est utilisé dans le déploiement précédent. |
| ID de ressource Key Vault du certificat PFX | Spécifiez l’ID de ressource Microsoft Azure Key Vault tel qu’il est utilisé dans le déploiement précédent. |
| URL de la clé secrète Key Vault du certificat PFX | Spécifiez l’URL du certificat telle qu’elle est utilisée dans le déploiement précédent. |
| Empreinte du certificat PFX | Spécifiez l’empreinte du certificat telel qu’elle est utilisée dans le déploiement précédent. |

## <a name="installation"></a>Installation

1.  Installez une nouvelle instance du modèle de registre de conteneurs dans un nouveau groupe de ressources.

    ![Installer un nouveau modèle de registre de conteneurs](./media/container-registry-template-updating-tzl/new-instance.png)

2.  Spécifiez la dernière version de la référence SKU du script `Get-VMImage` et utilisez un paramètre **dnsname** unique de l’installation initiale dans la configuration de la machine virtuelle. Utilisez le même principal de service et le même secret que pour l’installation initiale.

    ![Spécifier la référence SKU](./media/container-registry-template-updating-tzl/sku.png)

3.  Utilisez les mêmes paramètres de stockage et Key Vault que lors de l’installation initiale pour la configuration du stockage et de la Key Vault.

    ![Ajouter du stockage](./media/container-registry-template-updating-tzl/storage.png)

1.  Une fois le nouveau modèle de registre de conteneurs déployé, accédez au groupe de ressources initial, puis sélectionnez la ressource d’adresse IP publique.

    ![Sélectionner IP](./media/container-registry-template-updating-tzl/ip.png)

1.  Dans la ressource d’adresse IP publique, accédez à Configuration et modifiez l’étiquette du nom DNS afin de l’utiliser pour la ressource nouvellement déployée. Notez qu’après avoir modifié l’étiquette du nom DNS et sélectionné **Enregistrer**, les appels au registre de conteneurs commencent à échouer.

    ![Modifier l’étiquette DNS](./media/container-registry-template-updating-tzl/dns.png)
    
    ![Enregistrer la modification](./media/container-registry-template-updating-tzl/save.png)

2.  Accédez au nouveau groupe de ressources utilisé pour déployer la nouvelle instance du modèle de registre de conteneurs, sélectionnez la ressource IP publique, la configuration et mettez à jour l’étiquette du nom DNS vers le nom utilisé dans le déploiement d’origine, dans cet exemple `myreg`, puis sélectionnez **Enregistrer**.

    ![Sélectionner une configuration](./media/container-registry-template-updating-tzl/select-configuration.png)
    
    ![Enregistrer la configuration](./media/container-registry-template-updating-tzl/save-configuration.png)

3.  Les 30 minutes suivantes feront l’objet d’un accès intermittent au registre de conteneurs lors de la propagation de l’enregistrement DNS. Validez la connexion en vous connectant au registre Docker et en tirant (pull) /envoyant (push) une image.

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de la Place de Marché Azure Stack](../../operator/azure-stack-marketplace.md)
