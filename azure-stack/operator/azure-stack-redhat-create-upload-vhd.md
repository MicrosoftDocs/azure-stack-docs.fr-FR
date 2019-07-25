---
title: Création et téléchargement d’un disque dur virtuel Red Hat Enterprise Linux pour une utilisation dans Azure Stack | Microsoft Docs
description: Apprenez à créer et à télécharger un disque dur virtuel (VHD) Azure contenant un système d'exploitation Red Hat Linux.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: jeffgo
ms.lastreviewed: 08/15/2018
ms.openlocfilehash: 52ca03b01e762f980cbabcbe63d718bfd4c2c152
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418186"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure-stack"></a>Préparation d'une machine virtuelle Red Hat pour Azure Stack

Dans cet article, vous allez apprendre à préparer une machine virtuelle Red Hat Enterprise Linux (RHEL) à utiliser dans Azure Stack. Cet article couvre les versions de RHEL 7.1+. Les hyperviseurs de préparation abordés dans cet article sont Hyper-V, KVM (Machine virtuelle basée sur le noyau) et VMware.

Pour plus d’informations sur la prise en charge Red Hat Enterprise Linux, reportez-vous à [Red Hat and Azure Stack: Forum aux questions](https://access.redhat.com/articles/3413531).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Préparer une machine virtuelle Red Hat à partir du Gestionnaire Hyper-V

Cette section repose sur l’hypothèse que vous disposez déjà d’un fichier ISO provenant du site web de Red Hat et que vous avez installé l’image RHEL sur un disque dur virtuel (VHD). Pour plus d’informations sur l’utilisation du Gestionnaire Hyper-V pour installer une image de système d’exploitation, consultez l’article [Install Hyper-V and Configure a Virtual Machine](https://technet.microsoft.com/library/hh846766.aspx) (Installer Hyper-V et configurer une machine virtuelle).

### <a name="rhel-installation-notes"></a>Notes d’installation de RHEL

* Azure Stack ne prend pas en charge le format VHDX. Azure prend uniquement en charge les VHD fixes. Vous pouvez utiliser Hyper-V Manager pour convertir le disque au format VHD, ou l’applet de commande Convert-VHD. Si vous utilisez VirtualBox, sélectionnez **Taille fixe** par opposition à l’option de valeur par défaut allouée dynamiquement lorsque vous créez le disque.
* Azure Stack ne prend en charge que les machines virtuelles de la génération 1. Vous pouvez convertir une machine virtuelle de génération 1 du format VHDX au format VHD et d'une taille à expansion dynamique en taille fixe. Vous ne pouvez pas modifier la génération d’une machine virtuelle. Pour plus d’informations, consultez [Dois-je créer une machine virtuelle de génération 1 ou 2 dans Hyper-V ?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* La taille maximale autorisée pour le disque dur virtuel s’élève à 1 023 Go.
* Lorsque vous installez le système d’exploitation Linux, nous vous recommandons d’utiliser les partitions standard plutôt que le Gestionnaire de volumes logiques (LVM), qui constitue souvent le choix par défaut pour de nombreuses installations. Cette pratique évite les conflits de noms LVM avec des machines virtuelles clonées, notamment si vous avez besoin d’attacher un disque de système d’exploitation à une autre machine virtuelle identique à des fins de résolution des problèmes.
* La prise en charge du noyau pour le montage de systèmes de fichiers UDF (Universal Disk Format) est requise. Au premier démarrage, le média au format UDF attaché à l’invité transmet la configuration d’approvisionnement à la machine virtuelle Linux. L’agent Linux Azure doit monter le système de fichiers UDF pour lire sa configuration et approvisionner la machine virtuelle.
* Ne configurez pas de partition swap sur le système d’exploitation ou le disque. L'agent Linux est configurable pour créer un fichier d'échange sur le disque de ressources temporaire. Les étapes suivantes fournissent de plus amples informations à ce sujet.
* Tous les VHD sur Azure doivent avoir une taille virtuelle alignée sur 1 Mo. Avant de convertir un disque brut en VHD, vous devez vous assurer que la taille du disque brut est un multiple de 1 Mo. Vous trouverez de plus amples informations dans les étapes suivantes.
* Azure Stack ne prend pas en charge cloud-init. Votre machine virtuelle doit être configurée avec une version prise en charge de l’agent Linux Microsoft Azure (WALA).

### <a name="prepare-an-rhel-7-virtual-machine-from-hyper-v-manager"></a>Préparer une machine virtuelle RHEL 7 à partir du Gestionnaire Hyper-V

1. Dans le Gestionnaire Hyper-V, sélectionnez la machine virtuelle.

1. Cliquez sur **Connecter** pour ouvrir une fenêtre de console de la machine virtuelle.

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

1. Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour effectuer cette modification, ouvrez le fichier `/etc/default/grub` dans un éditeur de texte et modifiez le paramètre `GRUB_CMDLINE_LINUX`. Par exemple :

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Cette opération garantit que tous les messages de la console sont envoyés vers le premier port série, ce qui peut faciliter la tâche du support Azure en matière de résolution des problèmes. Cette configuration désactive également les nouvelles conventions d’affectation de noms RHEL 7 pour les cartes réseau.

   Le démarrage graphique et transparent n’est pas utile dans un environnement cloud où nous voulons que tous les journaux d’activité soient envoyés au port série. Vous pouvez laisser l’option `crashkernel` configurée le cas échéant. Notez que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle de 128 Mo ou plus, ce qui peut être problématique sur les machines virtuelles de petite taille. Nous vous recommandons de supprimer les paramètres suivants :

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. Une fois que vous avez fini de modifier `/etc/default/grub`, exécutez la commande suivante pour régénérer la configuration grub :

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Arrêtez et désinstallez cloud-init :

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu, ce qui est généralement le réglage par défaut. Modifiez `/etc/ssh/sshd_config` pour y inclure la ligne suivante :

    ```sh
    ClientAliveInterval 180
    ```

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

    L’agent Linux Azure peut configurer automatiquement un espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après l’approvisionnement de cette dernière sur Azure. Le disque de ressources local est un disque temporaire qui peut être vidé lorsque la machine virtuelle est déprovisionnée. Après avoir installé l’agent Linux Azure lors de l’étape précédente, modifiez en conséquence les paramètres suivants dans le fichier `/etc/waagent.conf` :

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

1. Si vous utilisez un système qui a été déployé à l’aide d’une autorité de certification d’entreprise, la machine virtuelle RHEL n’approuve pas le certificat racine Azure Stack. Vous devez placer ce dernier dans le magasin racine approuvé. Consultez l’article [Adding trusted root certificates to the server](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html) (Ajout de certificats racines approuvés au serveur).

1. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Cliquez sur **Action** > **Arrêter** dans le Gestionnaire Hyper-V.

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

1. Créez une machine virtuelle dans KVM à partir de l’image qcow2. Définissez le type de disque sur **qcow2**, puis définissez le modèle d’appareil de l’interface réseau virtuelle sur **virtio**. Démarrez ensuite la machine virtuelle, puis connectez-vous en tant que racine.

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

   Cette commande permet également d’assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d’Azure. Cette commande désactive également les nouvelles conventions d’affectation de noms RHEL 7 pour les cartes réseau.

   Le démarrage graphique et transparent n’est pas utile dans un environnement cloud dans lequel tous les journaux d’activité sont envoyés au port série. Vous pouvez laisser l’option `crashkernel` configurée le cas échéant. Ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle de 128 Mo ou davantage, ce qui peut être problématique sur les machines virtuelles de petite taille. Nous vous recommandons de supprimer les paramètres suivants :

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. Une fois que vous avez fini de modifier `/etc/default/grub`, exécutez la commande suivante pour régénérer la configuration grub :

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

1. Arrêtez et désinstallez cloud-init :

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

1. Lorsque vous créez un disque dur virtuel personnalisé pour Azure Stack, n’oubliez pas qu’une version WALinuxAgent comprise entre 2.2.20 et 2.2.35 (ces deux versions exclues) ne fonctionne pas sur les environnements Azure Stack. Vous pouvez utiliser les versions 2.2.20/2.2.35 pour préparer votre image. Pour utiliser les versions postérieures à 2.2.35 en vue de préparer votre image personnalisée, mettez à jour votre système Azure Stack avec la version 1903, ou appliquez le correctif logiciel 1901/1902. 

     Suivez ces instructions pour télécharger WALinuxAgent :
    
   a.   Téléchargez setuptools.
    ```bash
    wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
    tar xzf setuptools-7.0.tar.gz
    cd setuptools-7.0
    ```
   b. Il s’agit d’un exemple où nous téléchargeons la version « 2.2.20 » depuis le dépôt GitHub. Téléchargez et décompressez la version 2.2.20 de l’agent à partir de GitHub. 
    ```bash
    wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
    unzip v2.2.20.zip
    cd WALinuxAgent-2.2.20
    ```
    c. Installez setup.py.
    ```bash
    sudo python setup.py install
    ```
    d. Redémarrez waagent.
    ```bash
    sudo systemctl restart waagent
    ```
    e. Vérifiez si la version de l’agent correspond à celle que vous avez téléchargée. Pour cet exemple, la version devrait être 2.2.20.
    
    ```bash
    waagent -version
    ```

1. Ne créez pas d’espace d’échange sur le disque du système d’exploitation.

    L’agent Linux Azure peut configurer automatiquement un espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après l’approvisionnement de cette dernière sur Azure. Le disque de ressources local est un disque temporaire qui peut être vidé lorsque la machine virtuelle est déprovisionnée. Après avoir installé l’agent Linux Azure lors de l’étape précédente, modifiez en conséquence les paramètres suivants dans le fichier `/etc/waagent.conf` :

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

1. Si vous utilisez un système qui a été déployé à l’aide d’une autorité de certification d’entreprise, la machine virtuelle RHEL n’approuve pas le certificat racine Azure Stack. Vous devez placer ce dernier dans le magasin racine approuvé. Consultez l’article [Adding trusted root certificates to the server](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html) (Ajout de certificats racines approuvés au serveur).

1. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Arrêtez la machine virtuelle dans KVM.

1. Convertissez l’image qcow2 au format VHD.

    > [!NOTE]
    > Il existe un bogue connu dans la version 2.2.1 de qemu-img, qui entraîne un formatage incorrect de disque dur virtuel. Ce problème a été résolu dans QEMU 2.6. Il est recommandé d’utiliser qemu-img 2.2.0 ou une version antérieure, ou d’effectuer une mise à jour à la version 2.6 ou à une version ultérieure. Référence : https://bugs.launchpad.net/qemu/+bug/1490611.

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

    Ou, avec la version qemu **2.6 +** inclut l’option `force_size` :

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Préparer une machine virtuelle Red Hat à partir de VMware

Cette section suppose que vous avez déjà installé une machine virtuelle RHEL dans VMWare. Pour plus d’informations sur l’installation d’un système d’exploitation dans VMWare, voir le document [VMware Guest Operating System Installation Guide](https://partnerweb.vmware.com/GOSIG/home.html)(Guide d’installation de système d’exploitation invité VMWare).

* Lorsque vous installez le système d’exploitation Linux, nous vous recommandons d’utiliser les partitions standard plutôt que LVM, ce qui constitue souvent le choix par défaut pour de nombreuses installations. Cette pratique évite les conflits de noms LVM avec des machines virtuelles clonées, notamment si vous avez besoin d’attacher un disque de système d’exploitation à une autre machine virtuelle à des fins de résolution des problèmes. Vous pouvez utiliser les techniques LVM ou RAID sur les disques de données si vous le souhaitez.
* Ne configurez pas de partition swap sur le système d’exploitation ou le disque. Vous pouvez configurer l’agent Linux pour la création d’un fichier d’échange sur le disque de ressources temporaire. Les étapes qui suivent fournissent plus d’informations à ce sujet.
* Lorsque vous créez le disque dur virtuel, sélectionnez **Stocker le disque virtuel en un seul fichier**.

### <a name="prepare-an-rhel-7-virtual-machine-from-vmware"></a>Préparer une machine virtuelle RHEL 7 à partir de VMWare

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

1. Modifiez la ligne de démarrage du noyau dans votre configuration grub pour y inclure les paramètres de noyau supplémentaires pour Azure. Pour effectuer cette modification, ouvrez le fichier `/etc/default/grub` dans un éditeur de texte et modifiez le paramètre `GRUB_CMDLINE_LINUX`. Par exemple :

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

    Cette configuration permet également d’assurer que tous les messages de la console sont envoyés vers le premier port série, ce qui peut simplifier les problèmes de débogage pour la prise en charge d’Azure. Cela désactive également les nouvelles conventions d’affectation de noms RHEL 7 pour les cartes réseau. De plus, nous vous recommandons de supprimer les paramètres suivants :

    ```sh
    rhgb quiet crashkernel=auto
    ```

    Le démarrage graphique et transparent n’est pas utile dans un environnement cloud où nous voulons que tous les journaux d’activité soient envoyés au port série. Vous pouvez laisser l’option `crashkernel` configurée le cas échéant. Notez que ce paramètre réduit la quantité de mémoire disponible dans la machine virtuelle de 128 Mo ou plus, ce qui peut être problématique sur les machines virtuelles de petite taille.

1. Une fois que vous avez fini de modifier `/etc/default/grub`, exécutez la commande suivante pour régénérer la configuration grub :

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

1. Arrêtez et désinstallez cloud-init :

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Vérifiez que le serveur SSH est installé et configuré pour démarrer au moment prévu. Ce paramètre est généralement la valeur par défaut. Modifiez `/etc/ssh/sshd_config` pour y inclure la ligne suivante :

    ```sh
    ClientAliveInterval 180
    ```

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

    L’agent Linux Azure peut configurer automatiquement un espace d’échange à l’aide du disque de ressources local attaché à la machine virtuelle après l’approvisionnement de cette dernière sur Azure. Notez que le disque de ressources local est un disque temporaire et qu’il peut être vidé lors de l’annulation de l’approvisionnement de la machine virtuelle. Après avoir installé l’agent Linux Azure lors de l’étape précédente, modifiez en conséquence les paramètres suivants dans le fichier `/etc/waagent.conf` :

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

1. Si vous utilisez un système qui a été déployé à l’aide d’une autorité de certification d’entreprise, la machine virtuelle RHEL n’approuve pas le certificat racine Azure Stack. Vous devez placer ce dernier dans le magasin racine approuvé. Consultez l’article [Adding trusted root certificates to the server](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html) (Ajout de certificats racines approuvés au serveur).

1. Exécutez les commandes suivantes pour annuler le déploiement de la machine virtuelle et préparer son déploiement sur Azure :

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Arrêtez l’ordinateur virtuel et convertir le fichier VMDK au format VHD.

    > [!NOTE]
    > Il existe un bogue connu dans la version 2.2.1 de qemu-img, qui entraîne un formatage incorrect de disque dur virtuel. Ce problème a été résolu dans QEMU 2.6. Il est recommandé d’utiliser qemu-img 2.2.0 ou une version antérieure, ou d’effectuer une mise à jour à la version 2.6 ou à une version ultérieure. Référence : <https://bugs.launchpad.net/qemu/+bug/1490611>.

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

    Ou, avec la version qemu **2.6 +** inclut l’option `force_size` :

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Préparer une machine virtuelle Red Hat à partir d’un fichier ISO grâce à l’utilisation automatique d’un fichier Kickstart

1. Créez un fichier qui inclut le contenu suivant et enregistrez-le. Pour plus d’informations sur l’installation de Kickstart, voir le document [Kickstart Installation Guide](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html)(Guide d’installation Kickstart).

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

1. Dans le Gestionnaire Hyper-V, créez une machine virtuelle. Sur la page **Connecter un disque dur virtuel**, sélectionnez **Attacher un disque dur virtuel ultérieurement**, puis exécutez l’Assistant Nouvelle machine virtuelle.

1. Ouvrez les paramètres de la machine virtuelle :

    a. Attachez un nouveau disque dur virtuel à la machine virtuelle. Veillez à sélectionner **Format VHD** et **Taille fixe**.

    b. Attachez l’ISO d’installation au lecteur de DVD.

    c. Configurez le BIOS de manière à exécuter le démarrage à partir d’un CD.

1. Démarrez la machine virtuelle. Lorsque le guide d’installation s’affiche, appuyez sur la touche **Tab** pour configurer les options de démarrage.

1. Entrez `inst.ks=<the location of the kickstart file>` à la fin des options de démarrage, puis appuyez sur **Entrée**.

1. Attendez que l'installation se termine. À la fin de l’installation, la machine virtuelle est automatiquement arrêtée. Votre disque dur virtuel Linux est alors prêt pour le téléchargement dans Azure.

## <a name="known-issues"></a>Problèmes connus

### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Impossible d’inclure le pilote Hyper-V dans le disque virtuel initial lors de l’utilisation d’un hyperviseur non-Hyper-V

Dans certains cas, les programmes d’installation de Linux n’incluent pas les pilotes pour Hyper-V dans le disque virtuel initial (initrd ou initramfs), sauf si Linux détecte que ce dernier s’exécute dans un environnement Hyper-V.

Lorsque vous utilisez un système de virtualisation différent (c’est-à-dire Oracle VM VirtualBox, Xen Project, etc.) pour préparer votre image Linux, vous pouvez avoir besoin de recréer initrd afin de vous assurer que les modules noyau hv_vmbus et hv_storvsc figurent au moins parmi les modules disponibles sur le disque virtuel initial. Ceci est un problème connu, touchant au moins les systèmes basés sur la distribution Red Hat en amont.

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

Vous êtes maintenant prêt à utiliser votre disque dur virtuel Red Hat Enterprise Linux pour créer des machines virtuelles dans Azure Stack. Si vous importez le fichier VHD pour la première fois sur Azure Stack, consultez [Créer et publier un élément de la Place de marché](azure-stack-create-and-publish-marketplace-item.md).

Pour plus d’informations sur les hyperviseurs certifiés pour l’exécution de Red Hat Enterprise Linux, consultez le [site web de Red Hat](https://access.redhat.com/certified-hypervisors).
