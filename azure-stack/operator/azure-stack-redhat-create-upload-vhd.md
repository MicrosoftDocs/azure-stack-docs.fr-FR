---
title: Préparation d’une machine virtuelle Red Hat pour Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Apprenez à créer et à télécharger un disque dur virtuel (VHD) Azure contenant un système d'exploitation Red Hat Linux.
author: sethmanheim
ms.topic: article
ms.date: 12/11/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 12/11/2019
ms.openlocfilehash: d3df1040faec4c14b08f358f49fe9a6e9404fdc0
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698060"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure-stack-hub"></a>Préparation d’une machine virtuelle Red Hat pour Azure Stack Hub

Dans cet article, vous allez apprendre à préparer une machine virtuelle RHEL (Red Hat Enterprise Linux) à utiliser dans Azure Stack Hub. Cet article couvre les versions de RHEL 7.1+. Les hyperviseurs de préparation abordés dans cet article sont Hyper-V, KVM (Machine virtuelle basée sur le noyau) et VMware.

Pour plus d’informations sur la prise en charge Red Hat Enterprise Linux, consultez [Red Hat and Azure Stack Hub: Forum aux questions](https://access.redhat.com/articles/3413531).

## <a name="prepare-a-red-hat-based-vm-from-hyper-v-manager"></a>Préparer une machine virtuelle Red Hat à partir du Gestionnaire Hyper-V

Cette section part de l’hypothèse que vous avez déjà un fichier ISO provenant du site web Red Hat, et que vous avez installé l’image RHEL sur un VHD (disque dur virtuel). Pour plus d’informations sur l’utilisation du Gestionnaire Hyper-V afin d’installer une image de système d’exploitation, consultez [Installer le rôle Hyper-V et configurer une machine virtuelle](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="rhel-installation-notes"></a>Notes d’installation de RHEL

* Azure Stack Hub ne prend pas en charge le format VHDX. Azure prend uniquement en charge les VHD fixes. Vous pouvez utiliser Hyper-V Manager pour convertir le disque au format VHD, ou l’applet de commande Convert-VHD. Si vous utilisez VirtualBox, sélectionnez **Taille fixe** par opposition à l’option de valeur par défaut allouée dynamiquement lorsque vous créez le disque.
* Azure Stack Hub prend uniquement en charge les machines virtuelles de génération 1. Vous pouvez convertir une machine virtuelle de génération 1 du format de fichier VHDX au format de fichier VHD, et son disque de taille dynamique en disque de taille fixe. Vous ne pouvez pas changer la génération d’une machine virtuelle. Pour plus d’informations, consultez [Dois-je créer une machine virtuelle de génération 1 ou 2 dans Hyper-V ?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* La taille maximale autorisée pour le disque dur virtuel s’élève à 1 023 Go.
* Lorsque vous installez le système d’exploitation Linux, nous vous recommandons d’utiliser les partitions standard plutôt que le Gestionnaire de volumes logiques (LVM), qui constitue souvent le choix par défaut pour de nombreuses installations. Cette pratique évite les conflits de noms LVM avec les machines virtuelles clonées, notamment si vous devez attacher un disque de système d’exploitation à une autre machine virtuelle identique pour résoudre des problèmes.
* La prise en charge du noyau pour le montage de systèmes de fichiers UDF (Universal Disk Format) est requise. Au premier démarrage, le média au format UDF attaché à l’invité passe la configuration de provisionnement à la machine virtuelle Linux. Azure Linux Agent doit monter le système de fichiers UDF pour lire sa configuration et provisionner la machine virtuelle.
* Ne configurez pas de partition d’échange sur le disque du système d’exploitation. L'agent Linux est configurable pour créer un fichier d'échange sur le disque de ressources temporaire. Les étapes suivantes fournissent de plus amples informations à ce sujet.
* Tous les VHD sur Azure doivent avoir une taille virtuelle alignée sur 1 Mo. Avant de convertir un disque brut en VHD, vous devez vous assurer que la taille du disque brut est un multiple de 1 Mo. Vous trouverez de plus amples informations dans les étapes suivantes.
* Azure Stack Hub prend en charge cloud-init. [Cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) est une méthode largement utilisée pour personnaliser une machine virtuelle Linux lors de son premier démarrage. Vous pouvez utiliser cloud-init pour installer des packages et écrire des fichiers, ou encore pour configurer des utilisateurs ou des paramètres de sécurité. cloud-init étant appelé pendant le processus de démarrage initial, aucune autre étape ni aucun agent ne sont nécessaires pour appliquer votre configuration. Pour obtenir des instructions sur l’ajout de cloud-init à votre image, consultez [Préparer une image de machine virtuelle Azure Linux existante pour une utilisation avec cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/cloudinit-prepare-custom-image).

### <a name="prepare-an-rhel-7-vm-from-hyper-v-manager"></a>Préparer une machine virtuelle RHEL 7 à partir du Gestionnaire Hyper-V

1. Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

1. Sélectionnez **Connecter** afin d’ouvrir une fenêtre de console pour la machine virtuelle.

1. Créez ou modifiez le fichier `/etc/sysconfig/network`, puis ajoutez le texte suivant :

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Créez ou modifiez le fichier `/etc/sysconfig/network-scripts/ifcfg-eth0`, puis ajoutez le texte suivant selon vos besoins :

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Assurez-vous que le service réseau se lance au démarrage en exécutant la commande suivante :

    ```bash
    sudo systemctl enable network
    ```

1. Inscrivez votre abonnement Red Hat pour installer des packages à partir du référentiel RHEL en exécutant la commande suivante :

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour effectuer cette modification, ouvrez `/etc/default/grub` dans un éditeur de texte, puis modifiez le paramètre `GRUB_CMDLINE_LINUX`. Par exemple :

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Cette modification permet de garantir que tous les messages de la console sont envoyés au premier port série, ce qui facilite la tâche au support Azure pour la résolution des problèmes de débogage. Cette configuration désactive également les nouvelles conventions d’affectation de noms RHEL 7 pour les cartes réseau.

   Le démarrage graphique et sans assistance n’est pas utile dans un environnement cloud où nous souhaitons que tous les journaux soient envoyés au port série. Vous pouvez laisser l’option `crashkernel` configurée le cas échéant. Ce paramètre réduit la quantité de mémoire disponible sur la machine virtuelle de 128 Mo ou plus, ce qui peut poser un problème pour les machines virtuelles plus petites. Nous vous recommandons de supprimer les paramètres suivants :

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. Une fois que vous avez fini de modifier `/etc/default/grub`, exécutez la commande suivante pour regénérer la configuration de grub :

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. [Facultatif après la version 1910] Arrêtez et désinstallez cloud-init :

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu, ce qui est généralement le réglage par défaut. Modifiez `/etc/ssh/sshd_config` pour y inclure la ligne suivante :

    ```sh
    ClientAliveInterval 180
    ```

1. Durant la création d’un VHD (disque dur virtuel) personnalisé pour Azure Stack Hub, gardez à l’esprit que la version de WALinuxAgent comprise entre la 2.2.20 et la 2.2.35 (ces deux versions étant exclues) ne fonctionne pas dans les environnements Azure Stack Hub avant la version 1910. Vous pouvez utiliser les versions 2.2.20/2.2.35 pour préparer votre image. Pour utiliser les versions postérieures à la version 2.2.35 et préparer votre image personnalisée, mettez à jour votre infrastructure Azure Stack Hub vers la version 1903 (ou ultérieure), ou appliquez le correctif logiciel 1901/1902.
    
    [Avant la version 1910] Suivez ces instructions pour télécharger une version compatible de WALinuxAgent :
    
    1. Téléchargez setuptools.
        
        ```bash
        wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
        tar xzf setuptools-7.0.tar.gz
        cd setuptools-7.0
        ```
    
    1. Téléchargez et décompressez la version 2.2.20 de l’agent à partir de GitHub.

        ```bash
        wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
        unzip v2.2.20.zip
        cd WALinuxAgent-2.2.20
        ```

    1. Installez setup.py.

        ```bash
        sudo python setup.py install
        ```

    1. Redémarrez waagent.
    
        ```bash
        sudo systemctl restart waagent
        ```

    1. Vérifiez si la version de l’agent correspond à celle que vous avez téléchargée. Pour cet exemple, la version devrait être 2.2.20.

        ```bash
        waagent -version
        ```

    [Après la version 1910] Suivez ces instructions pour télécharger une version compatible de WALinuxAgent :
    
    1. Le package WALinuxAgent, `WALinuxAgent-<version>`, a fait l’objet d’une transmission de type push vers le référentiel Red Hat « extras ». Activez le référentiel extras en exécutant la commande suivante :

        ```bash
        subscription-manager repos --enable=rhel-7-server-extras-rpms
        ```

    1. Installez l'agent linux Azure en exécutant la commande suivante :

        ```bash
        sudo yum install WALinuxAgent
        sudo systemctl enable waagent.service
        ```
    

1. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’agent Linux Azure peut configurer automatiquement l’espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après le provisionnement de celle-ci sur Azure. Le disque de ressources local est un disque temporaire qui peut être vidé quand la machine virtuelle est déprovisionnée. Après avoir installé l’agent Linux Azure lors de l’étape précédente, modifiez en conséquence les paramètres suivants dans le fichier `/etc/waagent.conf` :

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Si vous souhaitez annuler l'inscription de l'abonnement, exécutez la commande suivante :

    ```bash
    sudo subscription-manager unregister
    ```

1. Si vous utilisez un système déployé à l’aide d’une autorité de certification d’entreprise, la machine virtuelle RHEL n’approuve pas le certificat racine Azure Stack Hub. Vous devez placer ce dernier dans le magasin racine approuvé. Pour plus d’informations, consultez les instructions relatives à l’[ajout de certificats racine approuvés au serveur](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Exécutez les commandes suivantes pour déprovisionner la machine virtuelle et préparer son provisionnement sur Azure :

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Sélectionnez **Action** > **Arrêter** dans le Gestionnaire Hyper-V.

1. Convertissez le VHD en un VHD de taille fixe à l’aide de la fonctionnalité « Modifier le disque » du Gestionnaire Hyper-V ou de la commande PowerShell Convert-VHD. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Préparer une machine virtuelle Red Hat à partir de KVM

1. Téléchargez l'image KVM de RHEL 7 depuis le site web Red Hat. Cette procédure utilise RHEL 7 comme exemple.

1. Définissez un mot de passe racine.

    Générez un mot de passe chiffré et copiez la sortie de la commande :

    ```bash
    openssl passwd -1 changeme
    ```

   Définissez un mot de passe racine avec guestfish :

    ```sh
    guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Modifiez le second champ de l'utilisateur racine en remplaçant « !! » » par le mot de passe chiffré.

1. Créez une machine virtuelle dans KVM à partir de l’image qcow2. Définissez le type de disque sur **qcow2**, puis définissez le modèle d’appareil de l’interface réseau virtuelle sur **virtio**. Démarrez ensuite cette machine virtuelle, puis connectez-vous en tant qu’utilisateur root.

1. Créez ou modifiez le fichier `/etc/sysconfig/network`, puis ajoutez le texte suivant :

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Créez ou modifiez le fichier `/etc/sysconfig/network-scripts/ifcfg-eth0`, puis ajoutez le texte suivant :

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Assurez-vous que le service réseau se lance au démarrage en exécutant la commande suivante :

    ```bash
    sudo systemctl enable network
    ```

1. Inscrivez votre abonnement Red Hat pour installer des packages à partir du référentiel RHEL en exécutant la commande suivante :

    ```bash
    subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour effectuer cette configuration, ouvrez le fichier `/etc/default/grub` dans un éditeur de texte et modifiez le paramètre `GRUB_CMDLINE_LINUX`. Par exemple :

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Cette commande permet également d’assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d’Azure. Cette commande désactive également les nouvelles conventions d’affectation de noms RHEL 7 pour les cartes réseau.

   Le démarrage graphique et sans assistance n’est pas utile dans un environnement cloud où tous les journaux sont envoyés au port série. Vous pouvez laisser l’option `crashkernel` configurée le cas échéant. Ce paramètre réduit la quantité de mémoire disponible sur la machine virtuelle de 128 Mo ou plus, ce qui peut poser un problème pour les machines virtuelles plus petites. Nous vous recommandons de supprimer les paramètres suivants :

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. Une fois que vous avez fini de modifier `/etc/default/grub`, exécutez la commande suivante pour regénérer la configuration de grub :

    ```bash
    grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Ajoutez des modules de Hyper-V dans initramfs.

    Modifiez `/etc/dracut.conf` en y ajoutant le contenu suivant :

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Régénérez initramfs :

    ```bash
    dracut -f -v
    ```

1. [Facultatif après la version 1910] Arrêtez et désinstallez cloud-init :

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu :

    ```bash
    systemctl enable sshd
    ```

    Modifiez /etc/ssh/sshd_config pour y inclure les lignes suivantes :

    ```sh
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. Durant la création d’un VHD (disque dur virtuel) personnalisé pour Azure Stack Hub, gardez à l’esprit que la version de WALinuxAgent comprise entre la 2.2.20 et la 2.2.35 (ces deux versions étant exclues) ne fonctionne pas dans les environnements Azure Stack Hub avant la version 1910. Vous pouvez utiliser les versions 2.2.20/2.2.35 pour préparer votre image. Pour utiliser les versions postérieures à la version 2.2.35 et préparer votre image personnalisée, mettez à jour votre infrastructure Azure Stack Hub vers la version 1903 (ou ultérieure), ou appliquez le correctif logiciel 1901/1902.

    [Avant la version 1910] Suivez ces instructions pour télécharger une version compatible de WALinuxAgent :

    1. Téléchargez setuptools.
        
        ```bash
        wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
        tar xzf setuptools-7.0.tar.gz
        cd setuptools-7.0
        ```
        
    1. Téléchargez et décompressez la version 2.2.20 de l’agent à partir de GitHub.
        
        ```bash
        wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
        unzip v2.2.20.zip
        cd WALinuxAgent-2.2.20
        ```
        
    1. Installez setup.py.
        
        ```bash
        sudo python setup.py install
        ```
        
    1. Redémarrez waagent.
        
        ```bash
        sudo systemctl restart waagent
        ```
        
    1. Vérifiez si la version de l’agent correspond à celle que vous avez téléchargée. Pour cet exemple, la version devrait être 2.2.20.
        
        ```bash
        waagent -version
        ```
        
    [Après la version 1910] Suivez ces instructions pour télécharger une version compatible de WALinuxAgent :
    
    1. Le package WALinuxAgent, `WALinuxAgent-<version>`, a fait l’objet d’une transmission de type push vers le référentiel Red Hat « extras ». Activez le référentiel extras en exécutant la commande suivante :

        ```bash
        subscription-manager repos --enable=rhel-7-server-extras-rpms
        ```

        1.Installez l’agent linux Azure en exécutant la commande suivante :

            ```bash
            sudo yum install WALinuxAgent
            sudo systemctl enable waagent.service
            ```

1. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’agent Linux Azure peut configurer automatiquement l’espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après le provisionnement de celle-ci sur Azure. Le disque de ressources local est un disque temporaire qui peut être vidé quand la machine virtuelle est déprovisionnée. Après avoir installé l’agent Linux Azure lors de l’étape précédente, modifiez en conséquence les paramètres suivants dans le fichier `/etc/waagent.conf` :

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Annulez l'inscription de l'abonnement (le cas échéant) en exécutant la commande suivante :

    ```bash
    subscription-manager unregister
    ```

1. Si vous utilisez un système déployé à l’aide d’une autorité de certification d’entreprise, la machine virtuelle RHEL n’approuve pas le certificat racine Azure Stack Hub. Vous devez placer ce dernier dans le magasin racine approuvé. Pour plus d’informations, consultez les instructions relatives à l’[ajout de certificats racine approuvés au serveur](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Exécutez les commandes suivantes pour déprovisionner la machine virtuelle et préparer son provisionnement sur Azure :

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Arrêtez la machine virtuelle dans KVM.

1. Convertissez l’image qcow2 au format VHD.

    > [!NOTE]
    > Il existe un bogue connu dans les versions de qemu-img >= 2.2.1, qui entraîne un formatage incorrect du VHD. Ce problème a été résolu dans QEMU 2.6. Il est recommandé d’utiliser qemu-img version 2.2.0 ou antérieure, ou d’effectuer une mise à jour vers la version 2.6 ou supérieure. Référence : https://bugs.launchpad.net/qemu/+bug/1490611.

    Convertissez tout d'abord l'image au format RAW :

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Assurez-vous que la taille de l’image RAW est alignée sur 1 Mo. Dans le cas contraire, arrondissez la taille pour l’aligner sur 1 Mo :

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Convertissez le disque brut en VHD à taille fixe :

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Sinon, avec qemu version **2.6+** , incluez l’option `force_size` :

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-vm-from-vmware"></a>Préparer une machine virtuelle Red Hat à partir de VMware

Cette section part de l’hypothèse que vous avez déjà installé une machine virtuelle RHEL dans VMware. Pour plus d’informations sur l’installation d’un système d’exploitation dans VMWare, voir le document [VMware Guest Operating System Installation Guide](https://aka.ms/aa6z600)(Guide d’installation de système d’exploitation invité VMWare).

* Lorsque vous installez le système d’exploitation Linux, nous vous recommandons d’utiliser les partitions standard plutôt que LVM, ce qui constitue souvent le choix par défaut pour de nombreuses installations. Cette méthode évite les conflits de noms LVM avec les machines virtuelles clonées, notamment si vous devez attacher un disque de système d’exploitation à une autre machine virtuelle pour résoudre des problèmes. Vous pouvez utiliser les techniques LVM ou RAID sur les disques de données si vous le souhaitez.
* Ne configurez pas de partition d’échange sur le disque du système d’exploitation. Vous pouvez configurer l’agent Linux pour la création d’un fichier d’échange sur le disque de ressources temporaire. Vous trouverez plus d’informations sur cette configuration dans les étapes qui suivent.
* Lorsque vous créez le disque dur virtuel, sélectionnez **Stocker le disque virtuel en un seul fichier**.

### <a name="prepare-an-rhel-7-vm-from-vmware"></a>Préparer une machine virtuelle RHEL 7 à partir de VMware

1. Créez ou modifiez le fichier `/etc/sysconfig/network`, puis ajoutez le texte suivant :

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Créez ou modifiez le fichier `/etc/sysconfig/network-scripts/ifcfg-eth0`, puis ajoutez le texte suivant :

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Assurez-vous que le service réseau commencera aux heures de démarrage en exécutant la commande suivante :

    ```bash
    sudo chkconfig network on
    ```

1. Inscrivez votre abonnement Red Hat pour installer des packages à partir du référentiel RHEL en exécutant la commande suivante :

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour effectuer cette modification, ouvrez `/etc/default/grub` dans un éditeur de texte, puis modifiez le paramètre `GRUB_CMDLINE_LINUX`. Par exemple :

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

    Cette configuration permet également d’assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d’Azure. Cela désactive également les nouvelles conventions d’affectation de noms RHEL 7 pour les cartes réseau. Nous vous recommandons de supprimer les paramètres suivants :

    ```sh
    rhgb quiet crashkernel=auto
    ```

    Le démarrage graphique et sans assistance n’est pas utile dans un environnement cloud où nous souhaitons que tous les journaux soient envoyés au port série. Vous pouvez laisser l’option `crashkernel` configurée le cas échéant. Ce paramètre réduit la quantité de mémoire disponible sur la machine virtuelle de 128 Mo ou plus, ce qui peut poser un problème pour les machines virtuelles plus petites.

1. Une fois que vous avez fini de modifier `/etc/default/grub`, exécutez la commande suivante pour regénérer la configuration de grub :

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Ajoutez des modules de Hyper-V dans initramfs.

    Modifiez `/etc/dracut.conf`, ajoutez le contenu :

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Régénérez initramfs :

    ```bash
    dracut -f -v
    ```

1. [Facultatif après la version 1910] Arrêtez et désinstallez cloud-init :

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. Ce paramètre est généralement la valeur par défaut. Modifiez `/etc/ssh/sshd_config` pour y inclure la ligne suivante :

    ```sh
    ClientAliveInterval 180
    ```

1. Durant la création d’un VHD (disque dur virtuel) personnalisé pour Azure Stack Hub, gardez à l’esprit que la version de WALinuxAgent comprise entre la 2.2.20 et la 2.2.35 (ces deux versions étant exclues) ne fonctionne pas dans les environnements Azure Stack Hub avant la version 1910. Vous pouvez utiliser les versions 2.2.20/2.2.35 pour préparer votre image. Pour utiliser les versions postérieures à la version 2.2.35 et préparer votre image personnalisée, mettez à jour votre infrastructure Azure Stack Hub vers la version 1903 (ou ultérieure), ou appliquez le correctif logiciel 1901/1902.

    [Avant la version 1910] Suivez ces instructions pour télécharger une version compatible de WALinuxAgent :

    1. Téléchargez setuptools.
    
        ```bash
        wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
        tar xzf setuptools-7.0.tar.gz
        cd setuptools-7.0
        ```
        
    1. Téléchargez et décompressez la version 2.2.20 de l’agent à partir de GitHub.
        
        ```bash
        wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
        unzip v2.2.20.zip
        cd WALinuxAgent-2.2.20
        ```
        
    1. Installez setup.py.
        
        ```bash
        sudo python setup.py install
        ```
        
    1. Redémarrez waagent.
        
        ```bash
        sudo systemctl restart waagent
        ```
        
    1. Vérifiez si la version de l’agent correspond à celle que vous avez téléchargée. Pour cet exemple, la version devrait être 2.2.20.
        
        ```bash
        waagent -version
        ```
        
    [Après la version 1910] Suivez ces instructions pour télécharger une version compatible de WALinuxAgent :
    
    1. Le package WALinuxAgent, `WALinuxAgent-<version>`, a fait l’objet d’une transmission de type push vers le référentiel Red Hat « extras ». Activez le référentiel extras en exécutant la commande suivante :

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

    1.Installez l’agent linux Azure en exécutant la commande suivante :
        
        ```bash
        sudo yum install WALinuxAgent
        sudo systemctl enable waagent.service
        ```
        
1. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’agent Linux Azure peut configurer automatiquement l’espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après le provisionnement de celle-ci sur Azure. Notez que le disque de ressources local est un disque temporaire qui peut être vidé quand la machine virtuelle est déprovisionnée. Après avoir installé l’agent Linux Azure lors de l’étape précédente, modifiez en conséquence les paramètres suivants dans le fichier `/etc/waagent.conf` :

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    NOTE: set this to whatever you need it to be.
    ```

1. Si vous souhaitez annuler l'inscription de l'abonnement, exécutez la commande suivante :

    ```bash
    sudo subscription-manager unregister
    ```

1. Si vous utilisez un système déployé à l’aide d’une autorité de certification d’entreprise, la machine virtuelle RHEL n’approuve pas le certificat racine Azure Stack Hub. Vous devez placer ce dernier dans le magasin racine approuvé. Pour plus d’informations, consultez les instructions relatives à l’[ajout de certificats racine approuvés au serveur](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Exécutez les commandes suivantes pour déprovisionner la machine virtuelle et préparer son provisionnement sur Azure :

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Arrêtez la machine virtuelle et convertissez le fichier VMDK au format VHD.

    > [!NOTE]
    > Il existe un bogue connu dans les versions de qemu-img >= 2.2.1, qui entraîne un formatage incorrect du VHD. Ce problème a été résolu dans QEMU 2.6. Il est recommandé d’utiliser qemu-img version 2.2.0 ou antérieure, ou d’effectuer une mise à jour vers la version 2.6 ou supérieure. Référence : <https://bugs.launchpad.net/qemu/+bug/1490611>.

    Convertissez tout d'abord l'image au format RAW :

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Assurez-vous que la taille de l’image RAW est alignée sur 1 Mo. Dans le cas contraire, arrondissez la taille pour l’aligner sur 1 Mo :

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Convertissez le disque brut en VHD à taille fixe :

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Sinon, avec qemu version **2.6+** , incluez l’option `force_size` :

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-vm-from-an-iso-by-using-a-kickstart-file-automatically"></a>Préparer une machine virtuelle Red Hat à partir d’une image ISO via l’utilisation automatique d’un fichier kickstart

1. Créez un fichier qui inclut le contenu suivant et enregistrez-le. L’arrêt et la désinstallation de cloud-init sont facultatifs (cloud-init est pris en charge sur Azure Stack Hub après la version 1910). Installez l’agent à partir du dépôt redhat uniquement après la version 1910. Avant la version 1910, utilisez le dépôt Azure comme dans la section précédente. Pour plus d’informations sur l’installation de Kickstart, voir le document [Kickstart Installation Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html)(Guide d’installation Kickstart).

    ```sh
    Kickstart for provisioning a RHEL 7 Azure VM

    System authorization information
    auth --enableshadow --passalgo=sha512

    Use graphical install
    text

    Do not run the Setup Agent on first boot
    firstboot --disable

    Keyboard layouts
    keyboard --vckeymap=us --xlayouts='us'

    System language
    lang en_US.UTF-8

    Network information
    network  --bootproto=dhcp

    Root password
    rootpw --plaintext "to_be_disabled"

    System services
    services --enabled="sshd,waagent,NetworkManager"

    System timezone
    timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

    Partition clearing information
    clearpart --all --initlabel

    Clear the MBR
    zerombr

    Disk partitioning information
    part /boot --fstype="xfs" --size=500
    part / --fstyp="xfs" --size=1 --grow --asprimary

    System bootloader configuration
    bootloader --location=mbr

    Firewall configuration
    firewall --disabled

    Enable SELinux
    selinux --enforcing

    Don't configure X
    skipx

    Power down the machine after install
    poweroff

    %packages
    @base
    @console-internet
    chrony
    sudo
    parted
    -dracut-config-rescue

    %end

    %post --log=/var/log/anaconda/post-install.log

    #!/bin/bash

    Register Red Hat Subscription
    subscription-manager register --username=XXX --password=XXX --auto-attach --force

    Install latest repo update
    yum update -y

    Stop and Uninstall cloud-init
    systemctl stop cloud-init
    yum remove cloud-init
    
    Enable extras repo
    subscription-manager repos --enable=rhel-7-server-extras-rpms

    Install WALinuxAgent
    yum install -y WALinuxAgent

    Unregister Red Hat subscription
    subscription-manager unregister

    Enable waaagent at boot-up
    systemctl enable waagent

    Disable the root account
    usermod root -p '!!'

    Configure swap in WALinuxAgent
    sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
    sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

    Set the cmdline
    sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

    Enable SSH keepalive
    sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

    Build the grub cfg
    grub2-mkconfig -o /boot/grub2/grub.cfg

    Configure network
    cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    EOF

    Deprovision and prepare for Azure
    waagent -force -deprovision

    %end
    ```

1. Placez le fichier Kickstart à un emplacement auquel système d’installation peut accéder.

1. Créez une machine virtuelle dans le Gestionnaire Hyper-V. Sur la page **Connecter un disque dur virtuel**, sélectionnez **Attacher un disque dur virtuel ultérieurement**, puis exécutez l’Assistant Nouvelle machine virtuelle.

1. Ouvrez les paramètres de la machine virtuelle :

    a. Attachez un nouveau disque dur virtuel à la machine virtuelle. Veillez à sélectionner **Format VHD** et **Taille fixe**.

    b. Attachez l’ISO d’installation au lecteur de DVD.

    c. Configurez le BIOS de manière à exécuter le démarrage à partir d’un CD.

1. Démarrez la machine virtuelle. Lorsque le guide d’installation s’affiche, appuyez sur la touche **Tab** pour configurer les options de démarrage.

1. Entrez `inst.ks=<the location of the kickstart file>` à la fin des options de démarrage, puis appuyez sur **Entrée**.

1. Attendez que l'installation se termine. Une fois l’opération effectuée, la machine virtuelle s’arrête automatiquement. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

## <a name="known-issues"></a>Problèmes connus

### <a name="the-hyper-v-driver-couldnt-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Impossible d’inclure le pilote Hyper-V dans le disque virtuel initial en cas d’utilisation d’un hyperviseur non Hyper-V

Dans certains cas, les programmes d’installation Linux n’incluent pas les pilotes pour Hyper-V dans le disque virtuel initial (initrd ou initramfs), sauf si Linux détecte qu’il s’exécute dans un environnement Hyper-V.

Quand vous utilisez un autre système de virtualisation (par exemple Oracle VM VirtualBox, Xen Project, etc.) pour préparer votre image Linux, vous pouvez être amené à regénérer initrd afin de vérifier que les modules de noyau hv_vmbus et hv_storvsc sont bien disponibles sur le disque virtuel initial. Ceci est un problème connu, touchant au moins les systèmes basés sur la distribution Red Hat en amont.

Pour résoudre ce problème, ajoutez des modules Hyper-V dans initramfs, puis régénérez ce dernier :

Modifiez `/etc/dracut.conf` et ajoutez le contenu suivant :

```sh
add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
```

Régénérez initramfs :

```bash
dracut -f -v
```

Pour plus d’informations, consultez l’article décrivant la procédure de [régénération d’initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Étapes suivantes

Vous êtes désormais prêt à utiliser votre disque dur virtuel Red Hat Enterprise Linux pour créer des machines virtuelles dans Azure Stack Hub. Si vous importez le fichier VHD pour la première fois sur Azure Stack Hub, consultez [Créer et publier un élément de la Place de marché](azure-stack-create-and-publish-marketplace-item.md).

Pour plus d’informations sur les hyperviseurs certifiés pour l’exécution de Red Hat Enterprise Linux, consultez le [site web de Red Hat](https://access.redhat.com/certified-hypervisors).
