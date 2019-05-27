---
title: Déployer un fichier WAR Java sur une machine virtuelle dans Azure Stack | Microsoft Docs
description: Déployez un fichier WAR Java sur une machine virtuelle dans Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 1738d106a0688518f7a739d3fb02ec1b16c2b8b9
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838368"
---
# <a name="deploy-a-java-web-app-to-a-vm-in-azure-stack"></a>Déployer une application web Java sur une machine virtuelle dans Azure Stack

Vous pouvez créer une machine virtuelle pour héberger votre application web Python dans Azure Stack. Dans cet article, vous installez et configurez un serveur Apache Tomcat sur une machine virtuelle Linux dans Azure Stack. Vous chargez ensuite un fichier WAR Java sur le serveur. Un fichier WAR permet de distribuer une collection de fichiers d’archive Java (JAR), qui sont des fichiers compressés contenant des ressources Java comme les classes, le texte, les images, le code XML, le code HTML et les autres ressources nécessaires pour fournir une application web.

## <a name="create-a-vm"></a>Créer une machine virtuelle

1. Configurez votre machine virtuelle dans Azure Stack en suivant les instructions fournies dans [Déployer une machine virtuelle Linux pour héberger une application web dans Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. Dans le volet du réseau de machines virtuelles, vérifiez que les ports suivants sont accessibles :

    | Port | Protocole | Description |
    | --- | --- | --- |
    | 80 | HTTP | HTTP (Hypertext Transfer Protocol) est le protocole utilisé pour fournir des pages web à partir des serveurs. Les clients se connectent via HTTP avec une adresse IP ou un nom DNS. |
    | 443 | HTTPS | HTTPS (Hypertext Transfer Protocol Secure) est une version sécurisée du protocole HTTP qui nécessite un certificat de sécurité pour la transmission chiffrée des informations. |
    | 22 | SSH | SSH (Secure Shell) est un protocole réseau chiffré pour les communications sécurisées. Vous utilisez cette connexion avec un client SSH pour configurer la machine virtuelle et déployer l’application. |
    | 3389 | RDP | facultatif. Le protocole RDP (Remote Desktop Protocol) permet d’utiliser une connexion Bureau à distance avec une interface graphique utilisateur sur votre machine.   |
    | 8080 | Personnalisée | Port par défaut du service Apache Tomcat. Pour un serveur de production, vous routez le trafic par les ports 80 et 443. |

## <a name="install-java"></a>Installer Java

1. Connectez-vous à votre machine virtuelle en utilisant votre client SSH. Pour obtenir des instructions, consultez [Se connecter par SSH avec PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

2. À l’invite Bash sur votre machine virtuelle, exécutez la commande suivante :

    ```bash  
        sudo apt-get install default-jdk
    ```

3. Validez votre installation. Tout en restant connecté à votre machine virtuelle dans votre session SSH, exécutez la commande suivante :

    ```bash  
        java -version
    ```

## <a name="install-and-configure-tomcat"></a>Installer et configurer Tomcat

1. Connectez-vous à votre machine virtuelle en utilisant votre client SSH. Pour obtenir des instructions, consultez [Se connecter par SSH avec PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Créez un utilisateur Tomcat en effectuant les étapes suivantes :

    a. Créez un groupe Tomcat à l’aide de la commande suivante :

    ```bash  
        sudo groupadd tomcat
    ```
     
    b. Créez un utilisateur Tomcat. Ajoutez cet utilisateur au groupe Tomcat avec le répertoire de base */opt/tomcat*. Vous déployez Tomcat dans ce répertoire :

    ```bash  
        sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
    ```

1. Installez Tomcat en effectuant les étapes suivantes :

    a. Obtenez l’URL du fichier tar de la dernière version de Tomcat 8 sur la [page de téléchargement Tomcat 8](http://tomcat.apache.org/download-80.cgi).

    b. Utilisez cURL pour télécharger la dernière version en suivant le lien. Exécutez les commandes suivantes :

    ```bash  
        cd /tmp 
        curl -O <URL for the tar for the latest version of Tomcat 8>
    ```

    c. Installez Tomcat dans le répertoire */opt/tomcat*. Créez le dossier, puis ouvrez l’archive :

    ```bash  
        sudo mkdir /opt/tomcat
        sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
        sudo chown -R tomcat webapps/ work/ temp/ logs/
    ```

1. Mettez à jour les autorisations pour Tomcat en exécutant les commandes suivantes :

    ```bash  
        sudo chgrp -R tomcat /opt/tomcat
        sudo chmod -R g+r conf
        sudo chmod g+x conf
    ```

1. Créez un fichier de service *systemd* afin de pouvoir exécuter Tomcat en tant que service.

   a. Tomcat a besoin de savoir où vous avez installé Java. Ce chemin d’accès est la plupart du temps nommé *JAVA_HOME*. Trouvez l’emplacement en question en exécutant :

    ```bash  
        sudo update-java-alternatives -l
    ```

    Le résultat doit se présenter ainsi :

    ```Text  
        Output
        java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64
    ```

    Vous pouvez construire la valeur de la variable *JAVA_HOME* en ajoutant */jre* au chemin de la sortie. Pour l’exemple précédent, la valeur serait */usr/lib/jvm/java-1.8.0-openjdk-amd64/jre*.

    b. Utilisez la valeur obtenue de votre serveur pour créer le fichier de service systemd :

    ```bash  
        sudo nano /etc/systemd/system/tomcat.service
    ```

    c. Collez le contenu suivant dans votre fichier de service. Si nécessaire, remplacez la valeur de *JAVA_HOME* par la valeur trouvée sur votre système. Vous pouvez également avoir besoin de modifier les paramètres d’allocation de mémoire spécifiés dans CATALINA_OPTS :

    ```Text  
        [Unit]
        Description=Apache Tomcat Web Application Container
        After=network.target
        
        [Service]
        Type=forking
        
        Environment=JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre
        Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
        Environment=CATALINA_HOME=/opt/tomcat
        Environment=CATALINA_BASE=/opt/tomcat
        Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
        Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'
        
        ExecStart=/opt/tomcat/bin/startup.sh
        ExecStop=/opt/tomcat/bin/shutdown.sh
    
        User=tomcat
        Group=tomcat
        UMask=0007
        RestartSec=10
        Restart=always
        
        [Install]
        WantedBy=multi-user.target
    ```

    d. Enregistrez et fermez le fichier.

    e. Rechargez le démon systemd pour qu’il prenne connaissance de votre fichier de service :

    ```bash  
        sudo systemctl daemon-reload
    ```

    f. Démarrez le service Tomcat : 

    ```bash  
        sudo systemctl start tomcat
    ```

    g. Vérifiez qu’il a démarré sans erreur, en entrant :

    ```bash  
        sudo systemctl status tomcat
    ```

1. Vérifiez le serveur Tomcat. Tomcat utilise le port 8080 pour accepter les demandes classiques. Autorisez le trafic vers ce port en exécutant la commande suivante :

    ```bash  
        sudo ufw allow 8080
    ```

    Si vous n’avez pas ajouté les *règles de port d’entrée* de votre machine virtuelle Azure Stack, faites-le maintenant. Pour plus d’informations, voir [Créer une machine virtuelle](#create-a-vm).

1. Ouvrez un navigateur dans le même réseau que votre système Azure Stack, puis ouvrez votre serveur, *votremachine.local.cloudapp.azurestack.external:8080*.

    ![Apache Tomcat sur une machine virtuelle Azure Stack](media/azure-stack-dev-start-howto-vm-java/apache-tomcat.png)

    La page Apache Tomcat se charge sur votre serveur. Vous configurez maintenant le serveur de façon à pouvoir accéder à Server Status, à l’application Manager et à Host Manager.

1. Activez le fichier de service pour que Tomcat se lance automatiquement au redémarrage de votre serveur :

    ```bash  
        sudo systemctl enable tomcat
    ```

1. Pour avoir accès à l’interface de gestion web, configurez le serveur Tomcat. 

   a. Modifiez le fichier *tomcat-users.xml* et définissez un rôle et un utilisateur pour la connexion. Définissez l’utilisateur de sorte qu’il ait accès à `manager-gui` et à `admin-gui`.

    ```bash  
        sudo nano /opt/tomcat/conf/tomcat-users.xml
    ```

   b. Ajoutez les éléments suivants à la section `<tomcat-users>` :

    ```XML  
        <role rolename="tomcat"/>
        <user username="<username>" password="<password>" roles="tomcat,manager-gui,admin-gui"/>
    ```

    Votre fichier final peut, par exemple, se présenter ainsi :

    ```XML  
        <tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
        <role rolename="tomcat"/>
        <user username="tomcatuser" password="changemepassword" roles="tomcat,manager-gui,admin-gui"/>
        </tomcat-users>
    ```

    c. Enregistrez et fermez le fichier.

1. Tomcat restreint l’accès aux applications *Manager* et *Host Manager* aux connexions provenant du serveur. Comme vous installez Tomcat sur une machine virtuelle dans Azure Stack, vous devez enlever cette restriction. Changez les restrictions d’adresses IP sur ces applications, en modifiant les fichiers *context.xml* correspondants.

    a. Mettez à jour *context.xml* dans l’application Manager :

    ```bash  
        sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
    ```

    b. Décommentez la restriction d’adresse IP pour autoriser toutes les connexions, indépendamment de leur provenance, ou ajoutez l’adresse IP de la machine que vous utilisez pour vous connecter à Tomcat.

    ```XML  
    <Context antiResourceLocking="false" privileged="true" >
        <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
                allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
    </Context>
    ```

    c. Enregistrez et fermez le fichier.

    d. De même, mettez à jour le fichier *context.xml* de l’application Host Manager :

    ```bash  
        sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
    ```

    e. Enregistrez et fermez le fichier.

1. Pour mettre à jour le serveur avec les modifications, redémarrez le service Tomcat :

    ```bash  
        sudo systemctl restart tomcat
    ```

1. Ouvrez un navigateur dans le même réseau que votre système Azure Stack, puis ouvrez votre serveur, *votremachine.local.cloudapp.azurestack.external:8080*.

    a. Pour voir l’état du serveur Tomcat et vérifier que vous y avez accès, sélectionnez **Server Status**.

    b. Connectez-vous avec vos informations d’identification Tomcat.

    ![Apache Tomcat sur une machine virtuelle Azure Stack](media/azure-stack-dev-start-howto-vm-java/apache-tomcat-management-app.png)

## <a name="create-an-app"></a>Créer une application

Vous devez créer un fichier WAR pour effectuer un déploiement sur Tomcat. Si vous souhaitez uniquement vérifier votre environnement, vous trouverez un exemple de fichier WAR sur le [site Apache Tomcat](https://tomcat.apache.org/tomcat-6.0-doc/appdev/sample/).

Pour obtenir des conseils sur le développement d’applications Java dans Azure, voir [Créer et déployer des applications Java sur Azure](https://azure.microsoft.com/develop/java/).

## <a name="deploy-and-run-the-app"></a>Déployer et exécuter l’application

1. Connectez-vous à votre machine virtuelle en utilisant votre client SSH. Pour obtenir des instructions, consultez [Se connecter par SSH avec PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Pour mettre à jour le serveur avec votre package d’application, arrêtez le service Tomcat :

    ```bash  
        sudo systemctl stop tomcat
    ```

1. Pour pouvoir écrire dans le dossier webapps, ajoutez l’utilisateur FTP au groupe Tomcat. Cet utilisateur est celui que vous avez défini quand vous avez créé votre machine virtuelle dans Azure Stack.

    ```bash  
        sudo usermod -a -G tomcat <VM-user>
    ```

1. Pour effacer le dossier webapps, puis charger votre fichier WAR créé ou mis à jour, connectez-vous à votre machine virtuelle avec FileZilla. Pour obtenir des instructions, consultez [Se connecter par SFTP avec FileZilla](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-sftp-with-filezilla).

    a. Effacez le dossier *TOMCAT_HOME/webapps*.

    b. Ajoutez votre fichier WAR au dossier *TOMCAT_HOME/webapps* (par exemple, */opt/tomcat/webapps/* ).

1.  Tomcat développe et déploie automatiquement l’application. Vous pouvez la voir en utilisant le nom DNS que vous avez créé tout à l’heure. Par exemple : 

    ```HTTP  
       http://yourmachine.local.cloudapp.azurestack.external:8080/sample

## Next steps

- Learn more about how to [develop for Azure Stack](azure-stack-dev-start.md).
- Learn about [common deployments for Azure Stack as IaaS](azure-stack-dev-start-deploy-app.md).
- To learn the Java programming language and find additional resources for Java, see [Java.com](https://www.java.com).
