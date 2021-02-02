---
title: Connecter un appareil IoT Edge à IoT Hub sur Azure Stack Hub
description: Apprenez à connecter un appareil IoT Edge à IoT Hub sur Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/20/2020
ms.reviewer: dmolokanov
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: 768952db6d93930f710dd80ea10e23ae6d8297a5
ms.sourcegitcommit: 0765de47f4a73e09192d34739e40c750b6e7abaf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920816"
---
# <a name="connect-an-iot-edge-device-to-iot-hub-on-azure-stack-hub"></a>Connecter un appareil IoT Edge à IoT Hub sur Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

Cet article explique la procédure de connexion d’un appareil IoT Edge virtuel à IoT Hub exécuté sur Azure Stack Hub. Vous pouvez utiliser le même processus général pour connecter un appareil physique à votre hub IoT.

## <a name="prerequisites"></a>Prérequis

Vous devez remplir les conditions préalables suivantes avant de poursuivre :

- Vous devez disposer d’un compte qui peut accéder au [portail de l’utilisateur Azure Stack Hub](../user/azure-stack-use-portal.md), avec au moins des [autorisations « contributeur »](../user/azure-stack-manage-permissions.md).

- Demandez à votre administrateur d’[installer IoT Hub sur le fournisseur de ressources Azure Stack Hub](../operator/iot-hub-rp-overview.md). La procédure d’installation couvre également la création d’une offre qui comprend le service IoT Hub. 

- Une fois qu’une offre est disponible, votre administrateur peut créer ou mettre à jour votre abonnement Azure Stack Hub pour inclure IoT Hub. Vous pouvez également [vous abonner à la nouvelle offre et créer votre propre abonnement](azure-stack-subscribe-services.md).

- Il est utile de comprendre les bases du chiffrement à clé publique (PKI), en particulier, la façon dont une autorité de certification (CA) et des certificats X509 sont utilisés pour créer une chaîne de confiance. Cette approbation permet aux nœuds de réseau de s’authentifier et de communiquer en toute sécurité entre eux, par exemple votre service IoT Hub et l’appareil IoT Edge. 

## <a name="overview"></a>Vue d’ensemble

Voici un résumé des étapes à effectuer pour connecter un appareil IoT Edge à votre IoT Hub sur Azure Stack Hub :

1. Créez IoT Hub et les ressources de machine virtuelle Linux sur votre instance Azure Stack Hub. La machine virtuelle Linux sert d’appareil IoT Edge virtuel. 
2. Configurez les certificats requis pour l’appareil IoT Edge. Le nombre d’étapes requises dépend du type d’autorité de certification qui a émis votre certificat d’autorité de certification racine IoT Hub.
3. Configurez le logiciel et les services requis pour l’appareil IoT Edge.
4. Testez l’appareil IoT Edge pour vous assurer qu’il fonctionne correctement et qu’il communique avec votre IoT Hub.

Avant d’exécuter le script dans chaque section, veillez à mettre à jour les espaces réservés du script pour qu’ils correspondent à la configuration de votre environnement. Prenez note des valeurs que vous utilisez, car vous en aurez besoin dans les sections suivantes :

| Espace réservé | Exemple | Description |
|-------------|---------|-------------|
| `<DEVICE-CA-CERT-NAME>` | `edged1cert`| Nom que vous donnez au certificat de l’autorité de certification de l’appareil IoT Edge. |
| `<IOT-HUB-HOSTNAME>`| `test-hub-1.mgmtiothub.region.mydomain.com`| Nom d’hôte de votre hub IoT. |
| `<IOT-HUB-ROOT-CA>`| `root.cer`| Nom de fichier que vous attribuez à l’autorité de certification racine IoT Hub exportée (si vous utilisez une autorité de certification privée). |
| `<DATA-DIR>`| `/home/edgeadmin/edged1`| Chemin d’accès complet au répertoire de données que vous créez sur la machine virtuelle Linux. |
| `<USER-ACCOUNT>`| `edgeadmin`| Compte que vous utilisez pour vous connecter à la machine virtuelle Linux. |

## <a name="create-azure-stack-hub-resources"></a>Créer des ressources Azure Stack Hub

Créez tout d’abord les ressources Azure Stack Hub nécessaires, notamment un hub IoT et une machine virtuelle Linux. Utilisez le [portail de l’utilisateur Azure Stack Hub](../user/azure-stack-use-portal.md) pour créer ces ressources.

### <a name="create-an-iot-hub-resource"></a>Créer une ressource IoT Hub

1. Connectez-vous au [portail de l’utilisateur Azure Stack Hub](../user/azure-stack-use-portal.md) à partir d’un ordinateur qui a accès à votre instance Azure Stack Hub.
2. Si vous n’en avez pas encore créé, suivez les instructions de la section [Créer un hub IoT](/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub) de **Créer un hub IoT à l’aide du portail Azure** pour créer une ressource IoT Hub. 

   > [!IMPORTANT]
   > Veillez à utiliser le [portail de l’utilisateur Azure Stack Hub](../user/azure-stack-use-portal.md) lorsque vous suivez la procédure décrite dans l’article, et NON le portail Azure. Notez également que certaines captures d’écran et instructions peuvent être légèrement différentes, car toutes les fonctionnalités Azure ne sont pas disponibles sur Azure Stack Hub. 

### <a name="deploy-and-configure-a-linux-vm"></a>Déployer et configurer une machine virtuelle Linux 

Dans cette section, vous allez déployer une nouvelle machine virtuelle Linux, qui servira d’appareil IoT Edge virtuel :

1. En vous aidant du guide de démarrage rapide [Créer une machine virtuelle serveur Linux sur le portail Azure Stack Hub](azure-stack-quick-linux-portal.md), installez une machine virtuelle Linux sur votre instance Azure Stack Hub. Veillez à activer le port **SSH (22)** dans la propriété **Sélectionner des ports d’entrée publics**, lorsque vous accédez à la page **Paramètres**. 

   > [!NOTE]
   > Vous ne devez finaliser que les 5 premières sections, jusqu’à **Se connecter à la machine virtuelle**, car vous n’aurez pas besoin du serveur web NGINX. 

2. Une fois que vous avez créé la machine virtuelle et que vous vous y êtes connecté à l’aide de PuTTY, créez un sous-répertoire de données à partir de votre répertoire de base du compte d’utilisateur, par exemple :

   ```bash
   mkdir edged1
   ```

3. Configurez l’outil de génération de certificats en utilisant l’une des méthodes suivantes. Téléchargez les fichiers à partir du référentiel GitHub IoT Edge, requis ultérieurement pour la génération de certificats d’appareil :

   **Utilisez le script cURL :**
   ```bash
   # Navigate to the edge device data directory
   cd <DATA-DIR>
   
   # Download certGen.sh script file, and openssl_root_ca.cnf config file
   curl -Lo certGen.sh https://raw.githubusercontent.com/Azure/iotedge/master/tools/CACertificates/certGen.sh
   curl -Lo openssl_root_ca.cnf https://raw.githubusercontent.com/Azure/iotedge/master/tools/CACertificates/openssl_root_ca.cnf
   ```
   **Utilisez Git pour cloner le référentiel :**
   
   ```bash
   # Navigate to the home directory
   cd /home/<USER-ACCOUNT>
   
   # Clone the iotedge repo, which contains the certGen.sh script file and openssl_root_ca.cnf config file
   git clone https://github.com/Azure/iotedge.git
   
   # Navigate to the edge device data directory
   cd <DATA-DIR>
   
   # Copy certGen.sh and openssl_root_ca.cnf into your working directory
   cp /home/<USER-ACCOUNT>/iotedge/tools/CACertificates/*.cnf .
   cp /home/<USER-ACCOUNT>/iotedge/tools/CACertificates/certGen.sh .
   ```

## <a name="configure-iot-edge-device-certificates"></a>Configurer des certificats d’appareil IoT Edge

Dans cette section, vous allez effectuer la configuration du certificat de machine virtuelle requise par l’appareil IoT Edge virtuel. 

Votre service IoT Hub et l’appareil IoT Edge sont sécurisés avec des certificats X509. Tous deux doivent utiliser un certificat d’autorité de certification racine émis par la même autorité de certification. Sélectionnez l’onglet approprié ci-dessous pour terminer la configuration du certificat, en fonction du type d’autorité de certification racine utilisé par votre hub IoT.

# <a name="public-ca"></a>[Autorité de certification publique](#tab/public-ca)

[!INCLUDE [common-cert-config](../includes/iot-hub-connect-an-iot-edge-device-cert-common.md)]

# <a name="private-ca"></a>[Autorité de certification privée](#tab/private-ca)

> [!IMPORTANT]
> Si vous utilisez une autorité de certification privée, des étapes supplémentaires sont requises pour transférer le certificat d’autorité de certification racine IoT Hub vers l’appareil IoT Edge. N’effectuez cette procédure ***que** _ si vous utilisez votre propre autorité de certification privée pour la génération de certificats. Si votre certificat d’autorité de certification racine IoT Hub a été émis par une autorité de certification publique, sélectionnez l’onglet précédent _ *Autorité de certification publique**. 

### <a name="export-the-root-ca-certificate-from-your-iot-hub"></a>Exporter le certificat d’autorité de certification racine à partir de votre hub IoT

Avec une machine ayant accès à votre Azure Stack Hub, exportez le certificat d’autorité de certification racine IoT Hub au format PEM. L’exemple suivant montre comment exporter le certificat à l’aide d’un navigateur [Microsoft Edge](https://www.microsoft.com/edge) ou [Google Chrome](https://www.google.com/chrome/index.html) : 

   1. Sur la page **Vue d’ensemble** de votre hub IoT, utilisez le bouton **Copier** à droite de la propriété **Hostname** pour copier le nom d’hôte IoT Hub dans le Presse-papiers :  

      [![Nom d’hôte IoT Hub](media\iot-hub-connect-an-iot-edge-device\copy-iot-hub-host-name.png)](media\iot-hub-connect-an-iot-edge-device\copy-iot-hub-host-name.png#lightbox)
   1. Ouvrez un nouvel onglet dans un navigateur Microsoft Edge ou Google Chrome, entrez `https://`, collez le nom d’hôte IoT Hub copié à l’étape précédente, puis appuyez sur **Entrée**. 

   1. Une fois le message d’erreur retourné, sélectionnez l’icône de cadenas à gauche de la barre d’adresse, puis sélectionnez **Certificat**.
      [![connexion sécurisée du certificat](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-connection.png)](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-connection.png#lightbox)

   1. Lorsque le certificat SSL/TLS s’affiche, sélectionnez l’onglet **Chemin d’accès de certification**. Sélectionnez ensuite le certificat supérieur dans le chemin d’accès, puis sélectionnez le bouton **Afficher le certificat**.  
      [![connexion sécurisée du certificat pour le certificat SSL](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl.png)](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl.png#lightbox) 

   1. Lorsque le certificat d’autorité de certification racine s’affiche, sélectionnez l’onglet **Détails**, puis le bouton **Copier dans le fichier...** .
      [![connexion sécurisée du certificat pour le certificat d’autorité de certification](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl-root-ca.png)](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl-root-ca.png#lightbox) 

   1. Lorsque l’**Assistant Exportation de certificat** s’affiche, exportez le certificat vers un fichier au **format X.509 encodé en base 64**, par exemple **root.cer**. Vous utilisez ce fichier dans la section suivante, vous pouvez donc l’exporter vers un emplacement accessible à partir de la machine virtuelle Linux, ou pouvant être copié vers celle-ci.

   1. Une fois l’exportation terminée, vous pouvez fermer l’onglet du navigateur.

### <a name="install-the-iot-hub-root-ca-certificate-on-the-iot-edge-device"></a>Installer le certificat d’autorité de certification racine sur l’appareil IoT Edge

À présent, installez le certificat d’autorité de certification racine IoT Hub que vous avez exporté dans la section précédente dans le magasin approuvé de l’appareil IoT Edge. 

1. Transférez le fichier d’autorité de certification racine IoT Hub que vous avez exporté dans la section précédente vers l’appareil IoT Edge. Étant donné que nous utilisons une machine virtuelle Linux comme appareil IoT Edge, vous devez le copier sur la machine virtuelle Linux. Selon votre environnement, envisagez d’utiliser PuTTY avec la commande PSCP ou le programme WinSCP pour copier le fichier sur la machine virtuelle Linux.
2. Exécutez le script suivant à partir de votre session PuTTY de machine virtuelle Linux, où vous avez stocké le fichier d’autorité de certification racine IoT Hub. Le script vérifie que la connexion TLS a réussi et installe l’autorité de certification racine dans le magasin approuvé de l’appareil IoT Edge :

   ```bash
   # Verify connection failed first
   # Expected response: Verify return code: 2 (unable to get issuer certificate)
   openssl s_client -connect <IOT-HUB-HOSTNAME>:443
   
   # Verify connection succeeded when root CA provided
   # Expected response: Verify return code: 0 (ok)
   openssl s_client -connect <IOT-HUB-HOSTNAME>:443 -CAfile <IOT-HUB-ROOT-CA>
   
   # Install root CA in the trusted store on Edge device
   sudo cp <IOT-HUB-ROOT-CA> /usr/local/share/ca-certificates/
   sudo update-ca-certificates
   
   # Verify connection succeeded even when no root CA provided
   # Expected response: Verify return code: 0 (ok)
   openssl s_client -connect <IOT-HUB-HOSTNAME>:443
   ```

[!INCLUDE [common-cert-config](../includes/iot-hub-connect-an-iot-edge-device-cert-common.md)]

### <a name="append-the-iot-hub-root-ca-to-the-device-root-ca"></a>Ajouter l’autorité de certification racine IoT Hub à l’autorité de certification racine de l’appareil

Ajoutez maintenant l’autorité de certification racine IoT Hub que vous avez exportée et copiée sur l’appareil IoT Edge, sur l’autorité de certification racine de l’appareil que vous avez générée précédemment :

```bash
# Navigate to home directory
cd /home/<USER-ACCOUNT>

# Append IoT Hub root CA to the device root CA
cat <IOT-HUB-ROOT-CA> >> certs/azure-iot-test-only.root.ca.cert.pem
```
-----

## <a name="configure-iot-edge-device-software-and-services"></a>Configurer le logiciel et les services de l’appareil IoT Edge

Dans cette section, vous allez effectuer la configuration de la machine virtuelle et d’IoT Hub requise par l’appareil IoT Edge virtuel.

### <a name="install-iot-edge-and-container-runtimes-on-the-vm"></a>Installer IoT Edge et des runtimes de conteneur sur la machine virtuelle

1. Revenez à la session PuTTY de la machine virtuelle et exécutez le script suivant pour inscrire la clé Microsoft et le flux du référentiel logiciel :

   ```bash
   # Navigate to the home directory
   cd /home/<USER-ACCOUNT>

   # Install the repository configuration. 
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   
   # Copy the generated list.
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   
   # Install Microsoft GPG public key.
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

2. Installer le runtime de conteneur :

   ```bash
   sudo apt-get update
   sudo apt-get install moby-engine moby-cli
   ```

3. Installer le démon de sécurité Azure IoT Edge :

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

> [!NOTE]
> Vous pouvez ignorer l’avis **IMPORTANT : Mettez à jour le fichier de configuration** pour l’instant, car vous allez le mettre à jour dans une prochaine section.

### <a name="create-an-iot-edge-device-in-iot-hub"></a>Créer un appareil IoT Edge dans IoT Hub

1. Revenez au [portail de l’utilisateur Azure Stack Hub](../user/azure-stack-use-portal.md) et accédez au service **IoT Hub**.

2. Sélectionnez votre ressource IoT Hub, sélectionnez la page **IoT Edge**, puis **ajoutez un appareil IoT Edge**.

   [![Ressource IoT Hub](media\iot-hub-connect-an-iot-edge-device\add-an-iot-edge-device.png)](media\iot-hub-connect-an-iot-edge-device\add-an-iot-edge-device.png#lightbox)

3. Sur la page **Créer un appareil**, entrez l’**ID d’appareil**, par exemple « edged1 ».
4. Lorsque vous avez terminé, sélectionnez **Enregistrer**.

   [![IoT Edge - Créer un appareil](media\iot-hub-connect-an-iot-edge-device\create-iot-edge-device.png)](media\iot-hub-connect-an-iot-edge-device\create-iot-edge-device.png#lightbox)

5. Lorsque le portail revient à la page **IoT Edge**, sélectionnez votre nouvel appareil dans la liste des appareils.

   [![IoT Edge - Afficher les appareils](media\iot-hub-connect-an-iot-edge-device\view-iot-edge-devices.png)](media\iot-hub-connect-an-iot-edge-device\view-iot-edge-devices.png#lightbox)

6. Sur la page des détails de l’appareil, utilisez le bouton **Copier** à droite de **Chaîne de connexion principale** pour copier la chaîne dans le Presse-papiers. Vous allez utiliser la chaîne de connexion dans la section suivante.

   [![IoT Edge - Détails de l’appareil](media\iot-hub-connect-an-iot-edge-device\iot-edge-device-details.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-device-details.png#lightbox)

### <a name="configure-the-virtual-iot-edge-device-on-the-vm"></a>Configurer l’appareil IoT Edge virtuel sur la machine virtuelle

1. Revenez à la session PuTTY de la machine virtuelle. À l’aide de Bash, ouvrez le fichier de configuration dans Nano sur l’appareil IoT Edge virtuel :

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

2. Localisez la propriété `provisioning` sous le commentaire **# Configuration manuelle de l’approvisionnement à l’aide d’une chaîne de connexion**, dans la section **Mode d’approvisionnement et paramètres**. Mettez à jour la chaîne de connexion de l’appareil IoT Edge en remplaçant l’espace réservé `<ADD DEVICE CONNECTION STRING HERE>` par la chaîne de connexion que vous avez copiée dans le Presse-papiers à la section précédente :

   > [!NOTE]
   > Pour coller le contenu du Presse-papiers dans Nano, appuyez sur la touche **Maj** et maintenez-la enfoncée, puis cliquez sur le bouton droit de la souris. Vous pouvez également appuyer sur les touches **Maj** et **Insertion** simultanément. Si l’opération de collage déplace votre curseur à l’extrême droite de la chaîne de connexion, appuyez sur la touche **Début** pour revenir à l’extrême gauche.

   ```yaml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

3. Recherchez et supprimez les commentaires de la propriété `certificates` dans la section **Paramètres du certificat**. Supprimez les commentaires et remplacez les URI de fichier par les chemins d’accès aux trois certificats que vous avez créés précédemment, par exemple :

   ```yaml
   certificates:
     device_ca_cert: "<DATA-DIR>/certs/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>-full-chain.cert.pem"
     device_ca_pk: "<DATA-DIR>/private/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>.key.pem"
     trusted_ca_certs: "<DATA-DIR>/certs/azure-iot-test-only.root.ca.cert.pem"
   ```

4. Une fois l’opération terminée, les propriétés `provisioning` et `certificates` doivent ressembler aux réponses suivantes :

   [![Éditeur Nano - Propriété d’approvisionnement](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-connection-string.png)](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-connection-string.png#lightbox)

   [![Éditeur Nano - Propriété des certificats](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-certificates.png)](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-certificates.png#lightbox)


5. Enregistrez et fermez le fichier à l’aide de **Ctrl** + **X**, **Y**, puis **Entrée**.

6. Après être revenu à Bash, redémarrez le démon :
   
   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="verify-a-successful-installation"></a>Vérifier la réussite de l’installation

1. Vérifiez l’état du démon IoT Edge :

   ```bash
   systemctl status iotedge
   ```

2. Vous devriez voir que le service IoT Edge est en cours d’exécution et « actif », avec une réponse similaire à ce qui suit. Le cas échéant, vous pouvez passer directement à l’étape 4.

   [![Service IoT Edge s’exécutant correctement](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-running.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-running.png#lightbox)

3. En cas d’échec du service IoT Edge :
   - Vous allez voir une réponse similaire indiquant « Échec », comme dans l’exemple suivant : [![Échec du service IoT Edge](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-failed.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-failed.png#lightbox)   
   - Pour résoudre les problèmes, vous pouvez effectuer les opérations suivantes :
     - Examinez les journaux du démon :

         ```bash
         journalctl -u iotedge --no-pager --no-full
         ```
     - Lancez l’outil de résolution des problèmes pour vérifier les erreurs de configuration et de mise en réseau les plus courantes. Dans cet exemple, un fichier `/etc/iotedge/config.yaml` incorrect a été détecté :

         ```bash
         sudo iotedge check
         ```

         [![Vérification du service IoT Edge](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-check.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-check.png#lightbox)

         > [!NOTE]
         > Le module système `$edgeHub` est facultatif et n’est pas déployé sur l’appareil tant que vous n’avez pas déployé votre premier module IoT Edge. Par conséquent, `iotedge check` peut renvoyer une erreur indiquant que `$edgeHub` ne peut pas être lié à des ports lors d’une vérification de la connectivité de l’hôte. Cette erreur peut être ignorée si `$edgeHub` n’est pas obligatoire dans votre déploiement.           

   - Si vous constatez que le format du fichier .YML est incorrect comme dans l’exemple ci-dessus, il vous suffit simplement d’effectuer les opérations suivantes :
      - Répétez les étapes décrites dans [Configurer l’appareil IoT Edge virtuel sur la machine virtuelle](#configure-the-virtual-iot-edge-device-on-the-vm) pour corriger fichier .YML.
      - Répétez les étapes décrites dans [Vérifier la réussite de l’installation](#verify-a-successful-installation).

4. Une fois que vous avez déterminé que le service IoT Edge a bien démarré, listez les modules en cours d’exécution. Vous devriez voir le service `edgeAgent` présentant l’état `running` :

   ```bash
   sudo iotedge list
   ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne les utilisez plus, revenez au [portail de l’utilisateur Azure Stack Hub](../user/azure-stack-use-portal.md), et supprimez les ressources de machine virtuelle et IoT Hub que vous avez créées précédemment.

## <a name="next-steps"></a>Étapes suivantes

Les ressources supplémentaires sont les suivantes :

- [Installer le runtime Azure IoT Edge sur des systèmes Linux Debian](/azure/iot-edge/how-to-install-iot-edge-linux)
- [Configurer un appareil IoT Edge en tant que passerelle transparente](/azure/iot-edge/how-to-create-transparent-gateway)
- [Créer des certificats de démonstration pour tester les fonctionnalités de l’appareil IoT Edge](/azure/iot-edge/how-to-create-test-certificates)