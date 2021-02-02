---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 11/20/2020
ms.reviewer: bryanla
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: 6d4bb587ad28fb26adf60689989d0e1858519cfe
ms.sourcegitcommit: 0765de47f4a73e09192d34739e40c750b6e7abaf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920814"
---
### <a name="create-a-device-root-ca-certificate"></a>Créer un certificat d’autorité de certification racine d’appareil

Créez le certificat de l’autorité de certification racine de l’appareil et les fichiers de clé requis pour votre appareil : 

1. Revenez à l’invite de commandes Bash dans votre session PuTTy.
2. Accédez au répertoire des données où vous avez placé les scripts de génération de certificat à la section précédente.
3. Exécutez la commande suivante :

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

4. Le certificat de l’autorité de certification racine de l’appareil est stocké dans le fichier suivant : `<DATA-DIR>/certs/azure-iot-test-only.root.ca.cert.pem`.

### <a name="create-the-iot-edge-device-ca-certificate"></a>Créer le certificat de l’autorité de certification de l’appareil IoT Edge

Les appareils IoT Edge destinés à la production ont besoin d’un certificat d’autorité de certification d’appareil référencé à partir du fichier config.yaml. Le certificat d’autorité de certification de l’appareil est chargé de créer des certificats pour les modules en cours d’exécution sur l’appareil. Il est également nécessaire pour les scénarios de passerelle, car c'est grâce au certificat de l'autorité de certification de l'appareil que l'appareil IoT Edge vérifie son identité auprès des appareils en aval.

Pour créer le certificat de l’autorité de certification de l’appareil IoT Edge :

1. Revenez à l’invite de commandes Bash dans votre session PuTTy.
2. Exécutez le script suivant, qui crée plusieurs fichiers de clé et de certificat d’autorité de certification d’appareil : 

   ```bash
   # Navigate to data directory
   cd <DATA-DIR>
   
   # Generate IoT Edge device CA certificate 
   ./certGen.sh create_edge_device_ca_certificate <DEVICE-CA-CERT-NAME>
   ```

3.  Les fichiers de paire de clés et de certificat suivants sont référencés plus loin dans le fichier config.yaml :

    `<DATA-DIR>/certs/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>-full-chain.cert.pem`  
    `<DATA-DIR>/private/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>.key.pem`


