---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 08/04/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: 8b8f26eefd70e54629a1505a01e1306cab2eda1d
ms.sourcegitcommit: 9a340b383dcf42c85bc6ec0d01ff3c9ae29dfe4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89609891"
---
Vous pouvez charger votre disque dur virtuel avec le portail. Ou, avec le conteneur que vous avez créé dans le portail, utilisez AzCopy.

### <a name="portal-to-generate-sas-url-and-upload-vhd"></a>Portail pour générer une URL SAS et charger un disque dur virtuel

1. Connectez-vous au portail utilisateur Azure Stack Hub.

2. Sélectionnez **Comptes de stockage**, ainsi qu’un compte de stockage existant ou créez un compte de stockage.

3. Sélectionnez **Objets blob** dans le panneau Compte de stockage de votre compte de stockage. Sélectionnez Conteneur pour créer un conteneur.

4. Tapez le nom de votre conteneur, puis sélectionnez **Objet blob (accès en lecture anonyme pour les objets blob uniquement)** .

5. Si vous envisagez d’utiliser AzCopy pour charger votre image plutôt que le portail, créez un jeton SAP. Sélectionnez **Signature d’accès partagé** dans le compte de stockage, puis sélectionnez **Générer la chaîne de connexion et SAP**. Copiez et prenez note de l’**URL SAS du service blob**. Vous allez utiliser cette URL lors de l’utilisation d’AzCopy pour charger votre disque dur virtuel.

6. Sélectionnez votre conteneur, puis **Charger**. Chargez votre disque dur virtuel.

### <a name="azcopy-vhd"></a>Disque dur virtuel AzCopy

Utilisez Explorateur Stockage Azure ou AzCopy pour réduire les risques de corruption de votre disque dur virtuel dans le processus de chargement et accélérer le chargement. Les étapes suivantes utilisent AzCopy sur un ordinateur Windows 10. AzCopy est un utilitaire de ligne de commande que vous pouvez utiliser pour copier des blobs ou des fichiers vers ou depuis un compte de stockage.

1. Si ce n’est pas déjà fait, installez AzCopy. Vous trouverez des instructions sur le téléchargement et la prise en main d’AzCopy dans l’article [Bien démarrer avec AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10). Prenez note de l’emplacement où vous stockez le fichier binaire. Vous pouvez [ajouter AzCopy à votre chemin](https://www.architectryan.com/2018/03/17/add-to-the-path-on-windows-10/) pour l’utiliser à partir de la ligne de commande PowerShell.

2. Ouvrez PowerShell pour utiliser AzCopy à partir de l’interpréteur de commandes.

3. Utilisez AzCopy pour charger votre disque dur virtuel dans votre conteneur dans le compte de stockage.

    ```powershell  
    set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09
    azcopy cp "/path/to/file.vhd" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS] --blob-type=PageBlob
    ```

> [!NOTE]  
> Chargez votre disque dur virtuel à l’aide d’une syntaxe similaire à celle du chargement d’un fichier unique dans un répertoire virtuel. Ajoutez `--blob-type=PageBlob` pour vous assurer que le disque dur virtuel est chargé en tant qu’**objet blob de pages**, au lieu d’**objet blob de blocs** par défaut.

Pour plus d’informations sur l’utilisation d’AzCopy et d’autres outils de stockage, consultez [Utiliser les outils de transfert de données dans le stockage Azure Stack Hub](/azure-stack/user/azure-stack-storage-transfer).