---
title: Attacher un GPU à une machine virtuelle Linux dans Azure Stack HCI
description: Guide pratique pour utiliser un GPU avec des charges de travail d’IA s’exécutant dans une machine virtuelle Ubuntu Linux sur Azure Stack HCI
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: c1f1ddbfb9f362261a8e55d32a0d8c28b7b64629
ms.sourcegitcommit: b65952127f39c263b162aad990e4d5b265570a7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80402860"
---
# <a name="attaching-a-gpu-to-an-ubuntu-linux-vm-on-azure-stack-hci"></a>Attachement d’un GPU à une machine virtuelle Ubuntu Linux sur Azure Stack HCI

> S’applique à : Windows Server 2019

Cette rubrique fournit des instructions pas à pas sur l’installation et la configuration d’un processeur graphique (GPU, graphics processing unit) NVIDIA avec Azure Stack HCI en utilisant la technologie DDA (Discrete Device Assignment) pour une machine virtuelle Ubuntu.
Ce document suppose que le cluster Azure Stack HCI est déployé et que les machines virtuelles sont installées.

## <a name="install-the-gpu-and-then-dismount-it-in-powershell"></a>Installer le GPU et le démonter dans PowerShell

1. Installez le ou les GPU NVIDIA physiquement sur les serveurs appropriés en suivant les instructions de l’OEM et les recommandations du BIOS.
2. Démarrez chaque serveur.
3. Connectez-vous à l’aide d’un compte disposant de privilèges administratifs sur les serveurs sur lesquels le GPU NVIDIA est installé.
4. Ouvrez le **Gestionnaire d’appareils** et accédez à la section *Autres appareils*. Vous devez voir dans la liste l’appareil « 3D Video Controller ».
5. Cliquez avec le bouton droit sur « 3D Video Controller » pour afficher la page **Propriétés**. Cliquez sur **Details**. Dans la liste déroulante sous **Propriété**, sélectionnez « Chemins d’accès des emplacements ».
6. Notez la valeur de la chaîne PCIRoot en surbrillance dans la capture d’écran ci-dessous. Cliquez avec le bouton droit sur **Valeur**, copiez la valeur et enregistrez-la.
    :::image type="content" source="media/attach-gpu-to-linux-vm/pciroot.png" alt-text="Capture d’écran - Chemins des emplacements":::
7. Ouvrez Windows PowerShell avec des privilèges élevés et exécutez la cmdlet `Dismount-VMHostAssignableDevice` pour démonter l’appareil GPU utilisant DDA en vue l’attribution à la machine virtuelle. Remplacez la valeur *LocationPath* par la valeur de votre appareil obtenue à l’étape 6.
    ```PowerShell
    Dismount-VMHostAssignableDevice -LocationPath "PCIROOT(16)#PCI(0000)#PCI(0000)" -force
    ```
8. Dans le **Gestionnaire d’appareils**, vérifiez que l’appareil apparaît comme démonté dans la liste des appareils système.
    :::image type="content" source="media/attach-gpu-to-linux-vm/dismounted.png" alt-text="Capture d’écran - Appareil démonté":::

## <a name="create-and-configure-an-ubuntu-virtual-machine"></a>Créer et configurer une machine virtuelle Ubuntu

1. Téléchargez le [fichier ISO de la version 18.04.02 du bureau Ubuntu](http://cdimage.ubuntu.com/lubuntu/releases/18.04.2/release/lubuntu-18.04.2-desktop-amd64.iso).
2. Ouvrez le **gestionnaire Hyper-V** sur le nœud du système sur lequel le GPU est installé.
   > [!NOTE]
   > [DDA ne prend pas en charge le basculement](/windows-server/virtualization/hyper-v/plan/plan-for-deploying-devices-using-discrete-device-assignment). Il s’agit d’une limitation des machines virtuelles avec DDA. Nous vous recommandons donc d’utiliser le **Gestionnaire Hyper-V** pour déployer la machine virtuelle sur le nœud au lieu du **Gestionnaire du cluster de basculement**. L’utilisation du **Gestionnaire du cluster de basculement** avec DDA échouera avec un message d’erreur indiquant que la machine virtuelle possède un appareil qui ne prend pas en charge la haute disponibilité.
3. À l’aide du fichier ISO Ubuntu téléchargé à l’étape 1, créez une machine virtuelle en utilisant l’**Assistant Nouvel ordinateur virtuel** dans le **Gestionnaire Hyper-V** pour créer une machine virtuelle Ubuntu Gen 1 avec 2 Go de mémoire et une carte réseau attachée.
4. Dans PowerShell, attribuez l’appareil GPU démonté à la machine virtuelle à l’aide des cmdlets ci-dessous, en remplaçant la valeur *LocationPath* par la valeur de votre appareil.
    ```PowerShell
    # Confirm that there are no DDA devices assigned to the VM
    Get-VMAssignableDevice -VMName Ubuntu

    # Assign the GPU to the VM
    Add-VMAssignableDevice -LocationPath "PCIROOT(16)#PCI(0000)#PCI(0000)" -VMName Ubuntu

    # Confirm that the GPU is assigned to the VM
    Get-VMAssignableDevice -VMName Ubuntu
    ```

    Quand elle réussit, l’attribution du GPU à la machine virtuelle affiche la sortie ci-dessous :  :::image type="content" source="media/attach-gpu-to-linux-vm/assign-gpu.png" alt-text="Capture d’écran - Attribution du GPU":::

    Configurez les autres valeurs en suivant la documentation du GPU disponible [ici](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda) :

   ```PowerShell
    # Enable Write-Combining on the CPU
    Set-VM -GuestControlledCacheTypes $true -VMName VMName

    # Configure the 32 bit MMIO space
    Set-VM -LowMemoryMappedIoSpace 3Gb -VMName VMName

    # Configure greater than 32 bit MMIO space
    Set-VM -HighMemoryMappedIoSpace 33280Mb -VMName VMName
   ```

   > [!NOTE]
   > La valeur 33280Mb doit suffire pour la plupart des GPU, mais doit être remplacée par une valeur supérieure à la mémoire du GPU.

5. À l’aide du Gestionnaire Hyper-V, connectez-vous à la machine virtuelle et démarrez l’installation du système d’exploitation Ubuntu. Choisissez les valeurs par défaut pour installer le système d’exploitation Ubuntu sur la machine virtuelle.

6. À l’issue de l’installation, utilisez le **Gestionnaire Hyper-V** pour arrêter la machine virtuelle et configurer l’**Action d’arrêt automatique** pour que la machine virtuelle arrête le système d’exploitation invité comme indiqué dans la capture d’écran ci-dessous :  :::image type="content" source="media/attach-gpu-to-linux-vm/guest-shutdown.png" alt-text="Capture d’écran - Arrêt du SE invité":::

7. Connectez-vous à Ubuntu et ouvrez le terminal pour installer SSH :

   ```shell
    $ sudo apt install openssh-server
   ```

8. Recherchez l’adresse TCP/IP pour l’installation d’Ubuntu à l’aide de la commande **ifconfig** et copiez l’adresse IP de l’interface **eth0**.

9. Utilisez un client SSH tel que [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/) pour vous connecter à la machine virtuelle Ubuntu afin de poursuivre la configuration.

10. Quand vous êtes connecté au client SSH, exécutez la commande **lspci** et vérifiez que le GPU NVIDIA est listé comme « 3D controller ».

    > [!IMPORTANT]
    > Si le GPU NVIDIA n’apparaît pas comme « 3D controller », ne poursuivez pas la procédure. Assurez-vous de suivre les étapes ci-dessus avant de continuer.

11. Dans la machine virtuelle, recherchez et ouvrez **Software & Updates**. Accédez à **Additional Drivers**, puis choisissez les pilotes GPU NVIDIA listés les plus récents. Terminez l’installation du pilote en cliquant sur le bouton **Apply Changes**.
    :::image type="content" source="media/attach-gpu-to-linux-vm/driver-install.png" alt-text="Capture d’écran - Installation du pilote":::

12. Redémarrez la machine virtuelle Ubuntu après l’installation du pilote. Après le démarrage de la machine virtuelle, connectez-vous par le biais du client SSH et exécutez la commande **nvidia-smi** pour vérifier que l’installation du pilote GPU NVIDIA s’est correctement déroulée. La sortie doit ressembler à la capture d’écran ci-dessous : :::image type="content" source="media/attach-gpu-to-linux-vm/nvidia-smi.png" alt-text="capture d’écran nvidia-smi":::

13. À l’aide du client SSH, configurez le référentiel et installez le moteur Docker CE :

    ```shell
    $ sudo apt-get update
    $ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
    ```
    Ajoutez la clé de GPG officielle de Docker :

    ```shell
    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

    Vérifiez que vous avez maintenant la clé avec l’empreinte digitale 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88 en recherchant les huit derniers caractères de l’empreinte digitale :

    ```shell
    $ sudo apt-key fingerprint 0EBFCD88
    ```

    La sortie doit ressembler à ceci :

    ```shell
    pub   rsa4096 2017-02-22 [SCEA]
    9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
    uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
    sub   rsa4096 2017-02-22 [S]
    ```

    Configurez le référentiel stable pour l’architecture Ubuntu AMD64 :

    ```shell
    $ sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
    ```

    Mettez à jour les packages et installez Docker CE :

    ```shell
    $ sudo apt-get update
    $ sudo apt-get install docker-ce docker-ce-cli containerd.io
    ```

    Vérifiez l’installation de Docker CE :

    ```shell
    $ sudo docker run hello-world
    ```

## <a name="configure-azure-iot-edge"></a>Configurer Azure IoT Edge

Pour vous préparer à effectuer cette configuration, consultez les questions fréquentes (FAQ) du dépôt GitHub [NVIDIA-Deepstream-Azure-IoT-Edge-on-a-NVIDIA-Jetson-Nano](https://github.com/Azure-Samples/NVIDIA-Deepstream-Azure-IoT-Edge-on-a-NVIDIA-Jetson-Nano), qui explique la nécessité d’installer Docker plutôt que Moby. Après vérification, effectuez les étapes ci-dessous.

### <a name="install-nvidia-docker"></a>Installer NVIDIA Docker

1. À partir du client SSH, ajoutez les référentiels de package :

    ```shell
    curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
    sudo apt-key add -
    distribution=$(. /etc/os-release;echo $ID$VERSION_ID)

    curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
    sudo tee /etc/apt/sources.list.d/nvidia-docker.list

    sudo apt-get update
    ```

2. Installez nvidia-docker2 et rechargez la configuration du démon Docker :

    ```shell
    sudo apt-get install -y nvidia-docker2
    sudo pkill -SIGHUP dockerd
    ```

3. Redémarrez la machine virtuelle :

    ```shell
    sudo /sbin/shutdown -r now
    ```

4. Au redémarrage, vérifiez que l’installation de NVIDIA Docker a réussi :

    ```shell
    sudo docker run --runtime=nvidia --rm nvidia/cuda:9.0-base nvidia-smi
    ```

    Si l’installation a réussi, la sortie ressemble à la capture d’écran ci-dessous :  :::image type="content" source="media/attach-gpu-to-linux-vm/docker.png" alt-text="Capture d’écran - Installation de Docker réussie":::

5. Installez Azure IoT Edge en suivant les instructions fournies ici et en ignorant l’installation du runtime :

    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list

    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    sudo apt-get update

    sudo apt-get install iotedge
    ```

    > [!NOTE]
    > Après avoir installé Azure IoT Edge, vérifiez que le fichier config.yaml est présent sur la machine virtuelle Ubuntu sous /etc/iotedge/config.yaml.

6. Créez une identité d’appareil IoT Edge dans le portail Azure en suivant les conseils fournis [ici](/azure/iot-edge/how-to-register-device#register-in-the-azure-portal). Ensuite, copiez la chaîne de connexion d’appareil pour l’appareil IoT Edge nouvellement créé.

7. À l’aide du client SSH, mettez à jour la chaîne de connexion d’appareil dans config.yaml sur la machine virtuelle Ubuntu :

    ```shell
    sudo nano /etc/iotedge/config.yaml
    ```

    Recherchez les configurations de provisionnement du fichier et supprimez les commentaires de la section « Manual provisioning configuration ». Mettez à jour la valeur de device_connection_string avec la chaîne de connexion à partir de votre appareil IoT Edge. Assurez-vous que les commentaires des autres sections de provisionnement sont supprimés. Assurez-vous que la ligne provisioning: n’est pas précédée d’un espace blanc et que les éléments imbriqués sont en retrait de deux espaces :

    :::image type="content" source="media/attach-gpu-to-linux-vm/manual-provisioning.png" alt-text="Capture d’écran - Section Manual Provisioning Configuration":::

    Pour coller le contenu du Presse-papiers dans Nano, cliquez sur le bouton droit en maintenant la touche Maj enfoncée ou appuyez sur Maj+Inser. Enregistrez le fichier et fermez-le (Ctrl + X, Y, Entrée).

8. À l’aide du client SSH, redémarrez le démon IoT Edge :

    ```shell
    sudo systemctl restart iotedge
    ```

    Vérifiez l’installation et vérifiez l’état du démon IoT Edge :

    ```shell
    systemctl status iotedge

    journalctl -u iotedge --no-pager --no-full
    ```

9. À l’aide du client SSH, créez la structure de répertoires suivante sur la machine virtuelle Ubuntu :

    ```shell
    cd /var
    sudo mkdir deepstream
    mkdir ./deepstream/custom_configs
    cd /var/deepstream
    sudo mkdir custom_streams
    sudo chmod -R 777 /var/deepstream
    cd ./custom_streams
    ```

10. Assurez-vous que votre répertoire de travail est /var/deepstream/custom_streams et téléchargez le [fichier de vidéos de démonstration](https://github.com/Azure-Samples/NVIDIA-Deepstream-Azure-IoT-Edge-on-a-NVIDIA-Jetson-Nano) en exécutant la commande suivante dans le client SSH :

    ```shell
    wget -O cars-streams.tar.gz --no-check-certificate https://onedrive.live.com/download?cid=0C0A4A69A0CDCB4C&resid=0C0A4A69A0CDCB4C%21588371&authkey=AAavgrxG95v9gu0
    ```

    Décompressez les fichiers vidéo :

    ```shell
    tar -xzvf cars-streams.tar.gz
    ```

    Le contenu du répertoire /var/deepstream/custom_streams doit être similaire à la capture d’écran ci-dessous :

    :::image type="content" source="media/attach-gpu-to-linux-vm/custom-streams.png" alt-text="Capture d’écran - Flux personnalisés":::

11. Créez un fichier nommé test5_config_file_src_infer_azure_iotedge_edited.txt dans le répertoire /var/deepstream/custom_configs. À l’aide d’un éditeur de texte, ouvrez le fichier et collez le code suivant, puis enregistrez et fermez le fichier.

    ```shell
    # Copyright (c) 2018 NVIDIA Corporation.  All rights reserved.
    #
    # NVIDIA Corporation and its licensors retain all intellectual property
    # and proprietary rights in and to this software, related documentation
    # and any modifications thereto.  Any use, reproduction, disclosure or
    # distribution of this software and related documentation without an express
    # license agreement from NVIDIA Corporation is strictly prohibited.
    
    [application]
    enable-perf-measurement=1
    perf-measurement-interval-sec=5
    #gie-kitti-output-dir=streamscl
    
    [tiled-display]
    enable=1
    rows=2
    columns=2
    width=1280
    height=720
    gpu-id=0
    #(0): nvbuf-mem-default - Default memory allocated, specific to particular platform
    #(1): nvbuf-mem-cuda-pinned - Allocate Pinned/Host cuda memory, applicable for Tesla
    #(2): nvbuf-mem-cuda-device - Allocate Device cuda memory, applicable for Tesla
    #(3): nvbuf-mem-cuda-unified - Allocate Unified cuda memory, applicable for Tesla
    #(4): nvbuf-mem-surface-array - Allocate Surface Array memory, applicable for Jetson
    nvbuf-memory-type=0
    
    [source0]
    enable=1
    #Type - 1=CameraV4L2 2=URI 3=MultiURI
    type=3
    uri=file://../../../../../samples/streams/sample_1080p_h264.mp4
    num-sources=2
    gpu-id=0
    nvbuf-memory-type=0
    
    [source1]
    enable=1
    #Type - 1=CameraV4L2 2=URI 3=MultiURI
    type=3
    uri=file://../../../../../samples/streams/sample_1080p_h264.mp4
    num-sources=2
    gpu-id=0
    nvbuf-memory-type=0
    
    [sink0]
    enable=0
    
    [sink3]
    enable=1
    #Type - 1=FakeSink 2=EglSink 3=File 4=RTSPStreaming
    type=4
    #1=h264 2=h265
    codec=1
    sync=0
    bitrate=4000000
    # set below properties in case of RTSPStreaming
    rtsp-port=8554
    udp-port=5400
    
    [sink1]
    enable=1
    #Type - 1=FakeSink 2=EglSink 3=File 4=UDPSink 5=nvoverlaysink 6=MsgConvBroker
    type=6
    msg-conv-config=../configs/dstest5_msgconv_sample_config.txt
    #(0): PAYLOAD_DEEPSTREAM - Deepstream schema payload
    #(1): PAYLOAD_DEEPSTREAM_MINIMAL - Deepstream schema payload minimal
    #(256): PAYLOAD_RESERVED - Reserved type
    #(257): PAYLOAD_CUSTOM   - Custom schema payload
    msg-conv-payload-type=1
    msg-broker-proto-lib=/opt/nvidia/deepstream/deepstream-4.0/lib/libnvds_azure_edge_proto.so
    topic=mytopic
    #Optional:
    #msg-broker-config=../../../../libs/azure_protocol_adaptor/module_client/cfg_azure.txt
    
    [sink2]
    enable=0
    type=3
    #1=mp4 2=mkv
    container=1
    #1=h264 2=h265 3=mpeg4
    ## only SW mpeg4 is supported right now.
    codec=3
    sync=1
    bitrate=2000000
    output-file=out.mp4
    source-id=0
    
    [osd]
    enable=1
    gpu-id=0
    border-width=1
    text-size=15
    text-color=1;1;1;1;
    text-bg-color=0.3;0.3;0.3;1
    font=Arial
    show-clock=0
    clock-x-offset=800
    clock-y-offset=820
    clock-text-size=12
    clock-color=1;0;0;0
    nvbuf-memory-type=0
    
    [streammux]
    gpu-id=0
    ##Boolean property to inform muxer that sources are live
    live-source=0
    batch-size=4
    ##time out in usec, to wait after the first buffer is available
    ##to push the batch even if the complete batch is not formed
    batched-push-timeout=40000
    ## Set muxer output width and height
    width=1920
    height=1080
    ##Enable to maintain aspect ratio wrt source, and allow black borders, works
    ##along with width, height properties
    enable-padding=0
    nvbuf-memory-type=0
    
    [primary-gie]
    enable=1
    gpu-id=0
    batch-size=4
    ## 0=FP32, 1=INT8, 2=FP16 mode
    bbox-border-color0=1;0;0;1
    bbox-border-color1=0;1;1;1
    bbox-border-color2=0;1;1;1
    bbox-border-color3=0;1;0;1
    nvbuf-memory-type=0
    interval=0
    gie-unique-id=1
    model-engine-file=../../../../../samples/models/Primary_Detector/resnet10.caffemodel_b4_int8.engine
    labelfile-path=../../../../../samples/models/Primary_Detector/labels.txt
    config-file=../../../../../samples/configs/deepstream-app/config_infer_primary.txt
    #infer-raw-output-dir=../../../../../samples/primary_detector_raw_output/
    
    [tracker]
    enable=1
    tracker-width=600
    tracker-height=300
    ll-lib-file=/opt/nvidia/deepstream/deepstream-4.0/lib/libnvds_mot_klt.so
    #ll-config-file required for DCF/IOU only
    #ll-config-file=tracker_config.yml
    #ll-config-file=iou_config.txt
    gpu-id=0
    #enable-batch-process applicable to DCF only
    enable-batch-process=0
    
    [tests]
    file-loop=1
    ```

12. Accédez au portail Azure. Sélectionnez **IoT Hub provisionné**, cliquez sur **Gestion automatique des appareils**, puis cliquez sur **IoT Edge** :

    :::image type="content" source="media/attach-gpu-to-linux-vm/iot-edge.png" alt-text="Capture d’écran - Gestion automatique des appareils":::

13. Dans le volet de droite, sélectionnez l’identité d’appareil dont la chaîne de connexion d’appareil a été utilisée plus haut. Cliquez sur Définir des modules :

    :::image type="content" source="media/attach-gpu-to-linux-vm/set-modules.png" alt-text="Capture d’écran - Définir des modules":::

14. Sous Modules IoT Edge, sélectionnez Module de la Place de marché :

    :::image type="content" source="media/attach-gpu-to-linux-vm/marketplace-module.png" alt-text="Capture d’écran - Module de la Place de marché":::

15. Recherchez NVIDIA et choisissez le SDK DeepStream comme indiqué ci-dessous :

    :::image type="content" source="media/attach-gpu-to-linux-vm/deepstream.png" alt-text="Capture d’écran - SDK DeepStream":::

16. Vérifiez que le module NvidiaDeepStreamSDK est listé sous Modules IoT Edge :

    :::image type="content" source="media/attach-gpu-to-linux-vm/edge-modules.png" alt-text="Capture d’écran - Modules IoT Edge":::

17. Cliquez sur le module « NVIDIADeepStreamSDK » et choisissez « Options de création de conteneur ». La configuration par défaut est illustrée ici :

    :::image type="content" source="media/attach-gpu-to-linux-vm/container-create-options.png" alt-text="Capture d’écran - Options de création de conteneur":::

    Remplacez la configuration ci-dessus par la configuration ci-dessous :

    ```shell
    {
      "ExposedPorts": {
        "8554/tcp": {}
      },
      "Entrypoint": [
        "/usr/bin/deepstream-test5-app",
        "-c",
        "test5_config_file_src_infer_azure_iotedge_edited.txt",
        "-p",
        "1",
        "-m",
        "1"
      ],
      "HostConfig": {
        "runtime": "nvidia",
        "Binds": [
          "/var/deepstream/custom_configs:/root/deepstream_sdk_v4.0.2_x86_64/sources/apps/sample_apps/deepstream-test5/custom_configs/",
          "/var/deepstream/custom_streams:/root/deepstream_sdk_v4.0.2_x86_64/sources/apps/sample_apps/deepstream-test5/custom_streams/"
        ],
        "PortBindings": {
          "8554/tcp": [
            {
              "HostPort": "8554"
            }
          ]
        }
      },
      "WorkingDir": "/root/deepstream_sdk_v4.0.2_x86_64/sources/apps/sample_apps/deepstream-test5/custom_configs/"
    }
    ```

18. Cliquez sur **Vérifier et créer**, puis, sur la page suivante, cliquez sur **Créer**. Vous devez maintenant voir les trois modules listés ci-dessous pour votre appareil IoT Edge dans le portail Azure :

    :::image type="content" source="media/attach-gpu-to-linux-vm/edge-hub-connections.png" alt-text="Capture d’écran - Modules et connexions du hub IoT Edge":::

19. Connectez-vous à la machine virtuelle Ubuntu à l’aide du client SSH et vérifiez que les modules corrects sont en cours d’exécution :

    ```shell
    sudo iotedge list
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify-modules-sudo.png" alt-text="Capture d’écran - iotedge list":::

    ```shell
    nvidia-smi
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify-modules-nvidia-smi.png" alt-text="Capture d’écran nvidia-smi":::

    > [!NOTE]
    > Le téléchargement du conteneur NvidiaDeepstream prend quelques minutes. Vous pouvez vérifier le téléchargement à l’aide de la commande « journalctl -u iotedge --no-pager --no-full » pour consulter les journaux du démon iotedge.

20. Vérifiez que le conteneur NvdiaDeepStreem est opérationnel. La sortie de la commande illustrée dans les captures d’écran ci-dessous indique que l’opération a réussi.

    ```shell
    sudo iotedge list
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify1.png" alt-text="Capture d’écran - iotedge list":::

    ```shell
    sudo iotedge logs -f NVIDIADeepStreamSDK
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify2.png" alt-text="Capture d’écran - iotedge list":::

    ```shell
    nvidia-smi
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify3.png" alt-text="Capture d’écran - iotedge list":::

21. Vérifiez l’adresse TCP/IP de votre machine virtuelle Ubuntu à l’aide de la commande **ifconfig** et recherchez l’adresse TCP/IP en regard de l’interface **eth0**.

22. Installez le lecteur VLC sur votre station de travail. Dans le lecteur VLC, cliquez sur **Media -> open network stream**, puis tapez l’adresse en respectant le format suivant :

    rtsp://ipaddress:8554/ds-test

    Où ipaddress correspond à l'adresse TCP/IP de votre machine virtuelle.

    :::image type="content" source="media/attach-gpu-to-linux-vm/vlc-player.png" alt-text="Capture d’écran - Lecteur VLC":::

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les GPU et la technologie DDA, consultez également :

- [Planifier le déploiement d’appareils à l’aide de la technologie Discrete Device Assignment](/windows-server/virtualization/hyper-v/plan/plan-for-deploying-devices-using-discrete-device-assignment)
- [Déployer des appareils graphiques à l’aide de la technologie Discrete Device Assignment](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)
