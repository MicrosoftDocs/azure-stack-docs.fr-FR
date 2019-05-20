---
title: Guide pratique pour utiliser une clé publique SSH avec Azure Stack | Microsoft Docs
description: Guide pratique pour utiliser une clé publique SSH
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 0f4133052022963e1ed0457dd479a00bcc5bb749
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490065"
---
# <a name="how-to-use-an-ssh-public-key"></a>Guide pratique pour utiliser une clé publique SSH

Il est parfois nécessaire de créer une paire clé privée/clé publique SSH pour utiliser une connexion SSH ouverte à partir d’un ordinateur de développement et de la machine virtuelle du serveur qui héberge l’application web dans Azure Stack. Cet article explique étape par étape comment obtenir les clés et les utiliser pour se connecter au serveur. Vous pouvez utiliser un client SSH pour obtenir une invite Bash sur le serveur Linux ou un client SFTP pour déplacer des fichiers de et vers le serveur.

## <a name="create-an-ssh-public-key-on-windows"></a>Créer une clé publique SSH sur Windows

Cette section utilise le générateur de clés PuTTY pour créer une paire clé privée/clé publique SSH qui servira à créer une connexion sécurisée à des ordinateurs Linux dans Azure Stack. PuTTY est une implémentation gratuite de SSH et Telnet pour les plateformes Windows et Unix, avec un émulateur de terminal `xterm`.

1. [Téléchargez et installez PuTTY pour votre ordinateur.](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

1. Ouvrez le générateur de clés PuTTY.

1. Définissez les **Paramètres** sur **RSA**.

1. Définissez le nombre de bits dans une clé générée sur `2048`.  

    ![Utiliser PuTTY pour générer une clé publique SSH](media/azure-stack-dev-start-howto-ssh-public-key/001-putty-key-gen-start.png)

1. Sélectionnez **Générer**. Dans la zone **Clé**, déplacez le curseur sur la zone vide de manière aléatoire.

1. Ajoutez une **Phrase secrète de clé** et confirmez-la dans la zone **Confirmer**. Prenez note de votre phrase secrète.
    ![Utiliser PuTTY pour générer une clé publique SSH](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-key-gen-result.png)

1. Sélectionnez **Enregistrer la clé publique** et enregistrez-la dans un emplacement accessible.

1. Sélectionnez **Enregistrer la clé privée** et enregistrez-la dans un emplacement accessible. N’oubliez pas qu’elle est associée à la clé publique.

Votre clé publique est stockée dans le fichier texte que vous avez enregistré. Si vous l’ouvrez, il devrait se présenter ainsi :

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

Pour utiliser la clé publique lorsqu’elle est demandée par une application, copiez et collez tout le contenu de la zone de texte comme valeur.

<!-- 
## Create an SSH public key on Linux

ToDo: I need to write this section.

-->
## <a name="connect-with-ssh-using-putty"></a>Se connecter par SSH avec PuTTY

Si vous avez installé PuTTY, vous disposez à la fois du générateur de clé et d’un client SSH. Ouvrez le client SSH et PuTTY, et configurez vos valeurs de connexion et votre clé SSH. Si vous êtes sur le même réseau que votre système Azure Stack, connectez-vous à votre machine virtuelle.

Avant de vous connecter, il vous faut :
- PuTTY ;
- l’adresse IP et le nom d’utilisateur de l’ordinateur Linux de votre système Azure Stack qui utilise une clé SSH publique comme type d’authentification ;
- le port 22 ouvert pour la machine ;
- la clé publique SSH utilisée lors de la création de la machine ;
- l’ordinateur client équipé de PuTTY, sur le même réseau que votre système Azure Stack.

### <a name="connect-via-ssh-with-putty"></a>Se connecter par SSH avec PuTTy

1. Ouvrez PuTTY.

    ![Utiliser PuTTY pour se connecter](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-connect.png)

2. Ajoutez le nom d’utilisateur et l’adresse IP publique de l’ordinateur (par exemple, `username@192.XXX.XXX.XX` comme **Nom d’hôte**). 
3. Vérifiez que **Port** est défini sur `22` et **Type de connexion** sur `SSH`.
4. Développez **SSH** > **Auth** dans l’arborescence **Catégorie**.

    ![Clé privée SSH](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-set-private-key.png)

5. Sélectionnez **Parcourir** pour trouver votre fichier de clé privée (nomfichier.ppk) issu de votre paire clé publique-clé privée.
6. Sélectionnez Session dans l’arborescence de Catégorie.

    ![Clé publique SSH avec clé privée](media/azure-stack-dev-start-howto-ssh-public-key/003-puTTY-save-session.png)

7. Tapez un nom pour la session sous **Sessions enregistrées** et sélectionnez **Enregistrer**.
8. Sélectionnez le nom de la session, puis **Charger**.
9. Sélectionnez **Ouvrir**. La session SSH s’ouvre.

## <a name="connect-with-sftp-with-filezilla"></a>Se connecter par SFTP avec FileZilla

Vous pouvez utiliser Filezilla comme client FTP prenant en charge SFTP pour déplacer des fichiers de et vers votre ordinateur Linux. FileZilla s’exécute sur Windows 10, Linux et macOS. Le client FileZilla prend en charge FTP, mais également FTP sur TLS (FTPS) et SFTP. Il s’agit d’un logiciel open source distribué gratuitement sous les conditions de la licence publique générale GNU.

### <a name="set-your-connection"></a>Définir la connexion

1. [Téléchargez et installez FileZilla](https://filezilla-project.org/download.php).
1. Ouvrez FileZilla.
1. Sélectionnez **Fichier** > **Site Manager**.

    ![Clé publique SSH avec clé privée](media/azure-stack-dev-start-howto-ssh-public-key/005-filezilla-file-manager.png)

1. Sélectionnez **SFTP (SSH File Transfer Protocol)** comme **Protocole**.
1. Ajoutez l’adresse IP publique de votre machine dans la zone **Hôte**.
1. Sélectionnez **Normal** comme **Type de connexion**.
1. Ajoutez votre nom d’utilisateur et votre mot de passe.
1. Sélectionnez **OK**.
1. Sélectionnez **Modifier** > **Paramètres**.

    ![Clé publique SSH avec clé privée](media/azure-stack-dev-start-howto-ssh-public-key/006-filezilla-add-private-key.png)

1. Développez **Connexion** dans l’arborescence **Sélectionner la page**. Sélectionnez **SFTP**.
1. Sélectionnez **Ajouter un fichier clé** et ajoutez votre fichier de clé privée, par exemple nomfichier.ppk.
1. Sélectionnez **OK**.

### <a name="open-your-connection"></a>Ouvrir la connexion

1. Ouvrez FileZilla.
1. Sélectionnez **Fichier** > **Site Manager**.
1. Sélectionnez le nom de votre site, puis **Se connecter**.

## <a name="next-steps"></a>Étapes suivantes

Apprenez à [Développer dans Azure Stack](azure-stack-dev-start.md).