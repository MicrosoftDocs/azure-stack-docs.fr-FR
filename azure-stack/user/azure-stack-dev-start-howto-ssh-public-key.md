---
title: Guide pratique pour utiliser une clé publique SSH avec Azure Stack Hub
description: Guide pratique pour utiliser une clé publique SSH
author: mattbriggs
ms.topic: overview
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 5f65b0ff096fbf84b140406b7cb98fdbdf5b0b11
ms.sourcegitcommit: 4138a2a15f78e7db38b3a29acc963a71937146fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "79295628"
---
# <a name="use-an-ssh-public-key"></a>Utiliser une clé publique SSH

Pour utiliser une connexion SSH ouverte entre la machine de développement et la machine virtuelle serveur dans votre instance Azure Stack Hub qui héberge votre application web, il est parfois nécessaire de créer une paire clé privée-clé publique SSH (Secure Shell). 

Dans cet article, vous créez des clés et les utilisez ensuite pour vous connecter au serveur. Vous pouvez utiliser un client SSH pour obtenir une invite Bash sur le serveur Linux ou utiliser un client SFTP (Secure FTP) pour déplacer des fichiers de et vers le serveur.

## <a name="create-an-ssh-public-key-on-windows"></a>Créer une clé publique SSH sur Windows

Dans cette section, vous utilisez le générateur de clé PuTTY pour créer une paire clé privée-clé publique SSH qui servira à créer une connexion sécurisée aux machines Linux dans votre instance Azure Stack Hub. PuTTY est un émulateur de terminal gratuit avec lequel vous pouvez vous connecter à un serveur via SSH et Telnet.

1. [Téléchargez et installez PuTTY pour votre ordinateur.](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

1. Ouvrez le générateur de clé PuTTY.

    ![Générateur de clé PuTTY avec une zone Key (Clé) vide](media/azure-stack-dev-start-howto-ssh-public-key/001-putty-key-gen-start.png)

1. Sous **Parameters**, sélectionnez **RSA**.

1. Dans la zone **Number of bits in a generated key** (Nombre de bits dans une clé générée), entrez **2048**.  

1. Sélectionnez **Générer**.

1. Dans la zone **Key**, générez quelques caractères aléatoires en déplaçant le curseur sur la zone vide.

    ![Générateur de clé PuTTY avec une zone Key remplie](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-key-gen-result.png)

1. Entrez une phrase secrète de clé dans **Key passphrase** et confirmez-la dans la zone **Confirm passphrase** (Confirmer la phrase secrète). Notez votre phrase secrète pour une utilisation ultérieure.

1. Sélectionnez **Save public key** (Enregistrer la clé publique) et enregistrez-la dans un emplacement accessible.

1. Sélectionnez **Save private key** (Enregistrer la clé privée) et enregistrez-la dans un emplacement accessible. N’oubliez pas qu’elle est associée à la clé publique.

Votre clé publique est stockée dans le fichier texte que vous avez enregistré. Le texte ressemble à ceci :

```text  
---- BEGIN SSH2 PUBLIC KEY ----
Comment: "rsa-key-20190330"
THISISANEXAMPLEDONOTUSE AAAAB3NzaC1yc2EAAAABJQAAAQEAthW2CinpqhXq
9uSa8/lSH7tLelMXnFljSrJIcpxp3MlHlYVbjHHoKfpvQek8DwKdOUcFIEzuStfT
Z8eUI1s5ZXkACudML68qQT8R0cmcFBGNY20K9ZMz/kZkCEbN80DJ+UnWgjdXKLvD
Dwl9aQwNc7W/WCuZtWPazee95PzAShPefGZ87Jp0OCxKaGYZ7UXMrCethwfVumvU
aj+aPsSThXncgVQUhSf/1IoRtnGOiZoktVvt0TIlhxDrHKHU/aZueaFXYqpxDLIs
BvpmONCSR3YnyUtgWV27N6zC7U1OBdmv7TN6M7g01uOYQKI/GQ==
---- END SSH2 PUBLIC KEY ----
```

Quand une application demande la clé, vous copiez et collez tout le contenu du fichier texte.

## <a name="connect-with-ssh-by-using-putty"></a>Se connecter par SSH avec PuTTY

Quand vous installez PuTTY, vous avez à la fois le générateur de clé PuTTY et un client SSH. Dans cette section, vous ouvrez le client SSH et PuTTY, et vous configurez vos valeurs de connexion et votre clé SSH. Si vous êtes sur le même réseau que votre instance Azure Stack Hub, vous vous connectez à votre machine virtuelle.

Avant de vous connecter, il vous faut :
- PuTTY ;
- L’adresse IP et le nom d’utilisateur de la machine Linux dans votre instance Azure Stack Hub qui utilise une clé SSH publique comme type d’authentification.
- Le port 22 ouvert pour la machine.
- La clé publique SSH utilisée au moment de la création de la machine.
- La machine cliente qui exécute PuTTY, sur le même réseau que votre instance Azure Stack Hub.

1. Ouvrez PuTTY.

    ![Volet de configuration de PuTTY](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-connect.png)

2. Dans la zone **Host Name (or IP address)** [Nom d’hôte (ou adresse IP)], entrez le nom d’utilisateur et l’adresse IP publique de la machine (par exemple, **username@192.XXX.XXX.XX** ). 
3. Vérifiez que le **Port** est **22** et que le type de connexion dans **Connection type** est **SSH**.
4. Dans l’arborescence **Category**, développez **SSH** et **Auth**.

    ![Volet de configuration de PuTTY - Clé privée SSH](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-set-private-key.png)

5. À côté de la zone **Private key file for authentication** (Fichier de clé privée pour l’authentification), sélectionnez **Browse** (Parcourir), puis recherchez le fichier de clé privée ( *\<nomfichier>.ppk*) correspondant à votre paire de clés privée-publique.
6. Dans l’arborescence **Category**, sélectionnez **Session**.

    ![Volet de configuration de PuTTY - Zone « Saved Sessions »](media/azure-stack-dev-start-howto-ssh-public-key/003-puTTY-save-session.png)

7. Sous **Saved Sessions** (Sessions enregistrées), entrez un nom pour la session, puis sélectionnez **Save** (Enregistrer).
8. Dans la liste **Saved Sessions** (Sessions enregistrées), sélectionnez le nom de votre session, puis sélectionnez **Load** (Charger).
9. Sélectionnez **Ouvrir**. La session SSH s’ouvre.

## <a name="connect-with-sftp-with-filezilla"></a>Se connecter par SFTP avec FileZilla

Pour déplacer des fichiers de et vers votre machine Linux, vous pouvez utiliser Filezilla, qui est un client FTP prenant en charge SFTP (Secure FTP). FileZilla s’exécute sur Windows 10, Linux et macOS. Le client FileZilla prend en charge FTP, mais également FTPS (FTP sur TLS) et SFTP. Il s’agit d’un logiciel open source qui est distribué gratuitement sous les conditions de la licence publique générale GNU.

### <a name="set-your-connection"></a>Définir la connexion

1. [Téléchargez et installez FileZilla](https://filezilla-project.org/download.php).
1. Ouvrez FileZilla.
1. Sélectionnez **Fichier** > **Site Manager**.

    ![Volet Gestionnaire de Sites de FileZilla](media/azure-stack-dev-start-howto-ssh-public-key/005-filezilla-file-manager.png)

1. Dans la liste déroulante **Protocole**, sélectionnez **SFTP - SSH File Transfer Protocol**.
1. Dans la zone **Hôte**, entrez l’adresse IP publique de votre machine.
1. Dans la zone **Type d’authentification**, sélectionnez **Normale**.
1. Entrez votre nom d'utilisateur et votre mot de passe.
1. Sélectionnez **OK**.
1. Sélectionnez **Modifier** > **Paramètres**.

    ![Volet Paramètres de FileZilla](media/azure-stack-dev-start-howto-ssh-public-key/006-filezilla-add-private-key.png)

1. Dans l’arborescence **Sélectionner une page**, développez **Connexion**, puis sélectionnez **SFTP**.
1. Sélectionnez **Ajouter un fichier de clé**, puis entrez votre fichier de clé privée (par exemple, *\<nomfichier>.ppk*).
1. Sélectionnez **OK**.

### <a name="open-your-connection"></a>Ouvrir la connexion

1. Ouvrez FileZilla.
1. Sélectionnez **Fichier** > **Site Manager**.
1. Sélectionnez le nom de votre site, puis sélectionnez **Connexion**.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [configurer un environnement de développement dans Azure Stack Hub](azure-stack-dev-start.md).
