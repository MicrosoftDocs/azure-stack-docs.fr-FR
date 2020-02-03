---
title: Ajouter des images Linux dans la Place de marché Azure Stack Hub
description: Découvrez comment ajouter des images Linux dans la Place de marché Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 01/23/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: b5e15568b2943d34dd5456f924db59cfcf48cb7f
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890253"
---
# <a name="add-linux-images-to-the-azure-stack-hub-marketplace"></a>Ajouter des images Linux dans la Place de marché Azure Stack Hub

Vous pouvez déployer des machines virtuelles Linux sur Azure Stack Hub en ajoutant une image Linux dans la Place de marché Azure Stack Hub. Le moyen le plus simple d’ajouter une image Linux à Azure Stack Hub est via la gestion de la Place de marché. Ces images ont été préparées et testées pour leur compatibilité avec Azure Stack Hub.

## <a name="marketplace-management"></a>Gestion de la Place de marché

Pour ajouter des images Linux à partir de la Place de marché Azure, consultez [Télécharger des éléments de Place de marché depuis Azure vers Azure Stack Hub](azure-stack-download-azure-marketplace-item.md). Sélectionnez les images Linux que vous souhaitez proposer aux utilisateurs sur votre système Azure Stack Hub.

Il existe des mises à jour fréquentes pour ces images ; aussi reportez-vous régulièrement à la gestion de la Place de marché pour les maintenir à jour.

## <a name="prepare-your-own-image"></a>Préparer votre propre image

Dans la mesure du possible, téléchargez les images disponibles via la gestion de la Place de marché. Ces images ont été préparées et testées pour Azure Stack Hub.

### <a name="azure-linux-agent"></a>Agent Linux Azure

L’Agent Azure Linux (généralement appelé **WALinuxAgent** ou **walinuxagent**) est nécessaire ; certaines versions de l’agent ne fonctionnent pas sur Azure Stack Hub. Les versions entre 2.2.21 et 2.2.34 (incluses) ne sont pas prises en charge dans Azure Stack Hub. Pour utiliser les dernières versions de l’agent qui sont postérieures à 2.2.35, appliquez le correctif logiciel 1901/1902, ou mettez à jour votre environnement Azure Stack Hub avec la version 1903 (ou supérieure). Notez que [cloud-init](https://cloud-init.io/) est pris en charge dans les versions Azure Stack Hub au-delà de la build 1910.

| Build Azure Stack Hub | Build Agent Linux Azure |
| ------------- | ------------- |
| 1.1901.0.99 ou versions antérieures | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 ou versions plus récentes |
| 1.1902.2.73  | 2.2.35 ou versions plus récentes |
| 1.1903.0.35  | 2.2.35 ou versions plus récentes |
| Versions après la build 1903 | 2.2.35 ou versions plus récentes |
| Non pris en charge | 2.2.21-2.2.34 |
| Versions après la build 1910 | Toutes les versions de l’agent Azure WALA|

Vous pouvez préparer votre propre image Linux en appliquant les instructions suivantes :

* [Distributions CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES et openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Serveur Ubuntu](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="cloud-init"></a>Cloud-Init

[cloud-init](https://cloud-init.io/) est pris en charge dans les versions Azure Stack Hub au-delà de la build 1910. Pour utiliser cloud-init afin de personnaliser votre machine virtuelle Linux, vous pouvez utiliser les instructions PowerShell suivantes.

### <a name="step-1-create-a-cloud-inittxt-file-with-your-cloud-config"></a>Étape 1 : Créer un fichier cloud-init.txt avec votre code cloud-config

Créez un fichier nommé cloud-init.txt et collez-y la configuration de cloud suivante :

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
  ```
  
### <a name="step-2-reference-the-cloud-inittxt-during-the-linux-vm-deployment"></a>Étape 2 : Référencer cloud-init.txt pendant le déploiement de la machine virtuelle Linux

Chargez le fichier dans un compte de stockage Azure, un compte de stockage Azure Stack Hub ou un dépôt GitHub accessible par votre machine virtuelle Linux Azure Stack Hub.
Actuellement, l’utilisation de cloud-init pour le déploiement de machines virtuelles est prise en charge uniquement sur REST, PowerShell et l’interface de ligne de commande, et elle n’a pas d’interface utilisateur de portail associée dans Azure Stack Hub.

Vous pouvez suivre [ces instructions](../user/azure-stack-quick-create-vm-linux-powershell.md) pour créer la machine virtuelle Linux à l’aide de PowerShell, mais veillez à référencer cloud-init.txt dans l’indicateur `-CustomData` :

```powershell
$VirtualMachine =Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred -CustomData "#include https://cloudinitstrg.blob.core.windows.net/strg/cloud-init.txt"
```

## <a name="add-your-image-to-marketplace"></a>Ajouter votre image dans la Place de marché

Suivez [Ajoutez l’image à la Place de Marché](azure-stack-add-vm-image.md). Vérifiez que le paramètre `OSType` a la valeur `Linux`.

Une fois que vous avez ajouté l’image dans la Place de marché, un élément de Place de marché est créé et les utilisateurs peuvent déployer une machine virtuelle Linux.

## <a name="next-steps"></a>Étapes suivantes

* [Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)
* [Vue d’ensemble de la Place de marché Azure Stack Hub](azure-stack-marketplace.md)
