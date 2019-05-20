---
title: Déployer un fichier WAR Java sur une machine virtuelle dans Azure Stack | Microsoft Docs
description: Déployez un fichier WAR Java sur une machine virtuelle dans Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: e788be6315078fccee020fefe6ad79a20485c382
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482385"
---
# <a name="how-to-deploy-a-java-web-app-to-a-vm-in-azure-stack"></a>Guide pratique pour déployer une application web Java sur une machine virtuelle dans Azure Stack

Vous pouvez créer une machine virtuelle pour héberger votre application web Python dans Azure Stack. Cet article explique étape par étape comment configurer le serveur, le paramétrer de façon à héberger une application web Python, puis déployer l’application.

Java est un langage de programmation informatique à usage général, simultané, orienté objet et classe et conçu pour avoir aussi peu de dépendances d’implémentation que possible. Il suit le slogan WORA (« Write once, run anywhere »), ce qui signifie que le code Java compilé peut s’exécuter sans recompilation sur toutes les plateformes compatibles Java. Pour apprendre le langage de programmation Java et trouver des ressources supplémentaires sur Java, voir [Java.com](https://www.java.com).

Cet article explique comment installer et configurer un serveur Apache Tomcat sur une machine virtuelle Linux dans Azure Stack, puis charger un fichier Web application Archive (WAR) Java dans le serveur. Un fichier WAR permet de distribuer un ensemble de fichiers JAR, de JavaServer Pages, de servlets Java, de classes Java, de fichiers XML, de bibliothèques de balises, de pages web statiques (HTML et fichiers associés) et d’autres ressources, le tout constituant une application web.

Apache Tomcat, souvent appelé serveur Tomcat, est un conteneur open source de servlets Java développé par l’Apache Software Foundation. Tomcat implémente plusieurs spécifications Java EE, comme Java Servlet, JavaServer Pages, Java EL et WebSocket, et offre un environnement de serveur web HTTP « Java exclusivement » permettant d’exécuter du code Java.

## <a name="create-a-vm"></a>Créer une machine virtuelle

1. Configurez votre machine virtuelle dans Azure Stack. Suivez les étapes de [Déployer une machine virtuelle Linux pour héberger une application web dans Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. Dans le panneau du réseau machines virtuelles, vérifiez que les ports suivants sont accessibles :

    | Port | Protocole | Description |
    | --- | --- | --- |
    | 80 | HTTP | Le protocole HTTP (Hypertext Transfer Protocol) est un protocole d’application pour les systèmes d’information hypermédias, collaboratifs et distribués. Les clients se connectent à l’application web avec l’adresse IP publique ou le nom DNS de la machine virtuelle. |
    | 443 | HTTPS | Le protocole HTTPS (Hypertext Transfer Protocol Secure) est une extension du protocole HTTP (Hypertext Transfer Protocol). Il sert à sécuriser la communication sur un réseau d’ordinateurs. Les clients se connectent à l’application web avec l’adresse IP publique ou le nom DNS de la machine virtuelle. |
    | 22 | SSH | Le protocole SSH (Secure Shell) est un protocole réseau de chiffrement qui permet d’exploiter en toute sécurité des services réseau sur un réseau non sécurisé. Nous utiliserons cette connexion avec un client SSH pour configurer la machine virtuelle et déployer l’application. |
    | 3389 | RDP | facultatif. Le protocole RDP (Remote Desktop Protocol) permet d’utiliser une connexion Bureau à distance avec une interface graphique utilisateur.   |
    | 8080 | Personnalisée | Le port par défaut du service Apache Tomcat est 8080. Pour un serveur de production, le trafic est acheminé par les ports 80 et 443. |

## <a name="install-java"></a>Installer Java

1. Connectez-vous à votre machine virtuelle en utilisant votre client SSH. Pour obtenir des instructions, voir [Se connecter par SSH avec PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
2. Dans l’invite Bash de votre machine virtuelle, tapez les commandes suivantes :

    ```bash  
        sudo apt-get install default-jdk
    ```

3. Validez votre installation. Tout en conservant la connexion à votre machine virtuelle dans votre session SSH, tapez les commandes suivantes :

    ```bash  
        java -version
    ```

## <a name="install-and-configure-tomcat"></a>Installer et configurer Tomcat

1. Connectez-vous à votre machine virtuelle en utilisant votre client SSH. Pour obtenir des instructions, voir [Se connecter par SSH avec PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).

2. Créez un utilisateur Tomcat.
    - Tout d’abord, créez un groupe Tomcat :
        ```bash  
            sudo groupadd tomcat
        ```
     
    - Ensuite, créez un utilisateur Tomcat et faites-en un membre du groupe Tomcat avec le répertoire de base `/opt/tomcat`, où sera installé Tomcat, et le shell `/bin/false` (afin que personne ne puisse ouvrir de session dans le compte) :
        ```bash  
            sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
        ```

3. Installez Tomcat.
    - Tout d’abord, récupérez l’URL du fichier tar de la dernière version de Tomcat 8 sur la page de téléchargement correspondante : [http://tomcat.apache.org/download-80.cgi](http://tomcat.apache.org/download-80.cgi).
    - Ensuite, utilisez cURL pour télécharger la dernière version en suivant le lien.

        ```bash  
            cd /tmp 
            curl -O <URL for the tar for the latest version of Tomcat 8>
        ```

    - Troisièmement, installez Tomcat dans le répertoire `/opt/tomcat`. Créez le répertoire, puis extrayez l’archive avec les commandes suivantes :

        ```bash  
            sudo mkdir /opt/tomcat
            sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
            sudo chown -R tomcat webapps/ work/ temp/ logs/
        ```

4. Mettez à jour les autorisations de Tomcat.

    ```bash  
        sudo chgrp -R tomcat /opt/tomcat
        sudo chmod -R g+r conf
        sudo chmod g+x conf
    ```

5. Créez un fichier de service `systemd` pour pouvoir exécuter Tomcat en tant que service.

    - Tomcat doit savoir où Java est installé. Ce chemin d’accès est la plupart du temps nommé **JAVA_HOME**. Trouvez l’emplacement en question en exécutant :

        ```bash  
            sudo update-java-alternatives -l
        ```

        Le résultat devrait se présenter ainsi :

        ```Text  
            Output
            java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64
        ```

        La valeur de la variable **JAVA_HOME** se construit en ajoutant `/jre` au chemin de la sortie (par exemple, dans l’exemple ci-dessus, `/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre`).

    - Utilisez la valeur provenant de votre serveur pour créer le fichier de service systemd.

        ```bash  
            sudo nano /etc/systemd/system/tomcat.service
        ```

    - Collez le contenu suivant dans votre fichier de service. Remplacez si nécessaire la valeur de **JAVA_HOME** par la valeur trouvée sur votre système. Il est également recommandé de modifier les paramètres d’allocation de mémoire spécifiés dans CATALINA_OPTS :

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

    - Enregistrez et fermez le fichier.
    - Rechargez le démon systemd pour qu’il prenne connaissance de votre fichier de service :

        ```bash  
            sudo systemctl daemon-reload
        ```

    - Lancez le service Tomcat : 

        ```bash  
            sudo systemctl start tomcat
        ```

    - Vérifiez qu’il a démarré sans erreur en tapant :

        ```bash  
            ssudo systemctl status tomcat
        ```

6. Vérifiez le serveur Tomcat. Tomcat utilise le port 8080 pour accepter les demandes classiques. Autorisez le trafic vers ce port en exécutant la commande suivante :

    ```bash  
        sudo ufw allow 8080
    ```

    Si vous n’avez pas ajouté les **règles de port d’entrée** de votre machine virtuelle Azure Stack, faites-le maintenant. Pour plus d’informations, voir [Créer une machine virtuelle](#create-a-vm).

7. Ouvrez un navigateur dans le réseau de votre système Azure Stack et ouvrez votre serveur : `yourmachine.local.cloudapp.azurestack.external:8080`.

    ![Apache Tomcat sur une machine virtuelle Azure Stack](media/azure-stack-dev-start-howto-vm-java/apache-tomcat.png)

    La page Apache Tomcat se charge sur votre serveur. Nous allons maintenant configurer le serveur de façon à pouvoir accéder à Server Status, à l’application Manager et à Host Manager.

8. Activez le fichier de service pour que Tomcat se lance automatiquement au redémarrage de votre serveur :

    ```bash  
        sudo systemctl enable tomcat
    ```

9. Configurez le serveur Tomcat de façon à pouvoir accéder à l’interface de gestion web. Modifiez le fichier `tomcat-users.xml` et définissez un rôle et un utilisateur pour pouvoir vous connecter. Définissez l’utilisateur de sorte qu’il ait accès à `manager-gui` et à `admin-gui`.

    ```bash  
        sudo nano /opt/tomcat/conf/tomcat-users.xml
    ```

    - Ajoutez les éléments suivants dans la section `<tomcat-users>` :

    ```XML  
        <role rolename="tomcat"/>
        <user username="<username>" password="<password>" roles="tomcat,manager-gui,admin-gui"/>
    ```

    - Votre fichier final peut par exemple se présenter ainsi :

    ```XML  
        <tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
         <role rolename="tomcat"/>
        <user username="tomcatuser" password="changemepassword" roles="tomcat,manager-gui,admin-gui"/>
        </tomcat-users>
    ```

    - Enregistrez et fermez le fichier.


10. Tomcat restreint l’accès aux applications **Manager** et **Host Manager** aux connexions provenant du serveur. Comme vous installez Tomcat sur une machine virtuelle dans Azure Stack, vous devez supprimer cette restriction. Changez les restrictions d’adresses IP sur ces applications, en modifiant les fichiers `context.xml` correspondants.

    - Mettez à jour le fichier `context.xml` de l’application Manager :

        ```bash  
            sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
        ```

    - Supprimez les marques de commentaire de la restriction d’adresse IP pour autoriser les connexions indépendamment de leur provenance, ou ajoutez l’adresse IP de l’ordinateur que vous utilisez pour vous connecter à Tomcat.

        ```XML  
        <Context antiResourceLocking="false" privileged="true" >
          <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
                 allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
        </Context>
        ```

    - Enregistrez et fermez le fichier.

    - De même, mettez à jour le fichier `context.xml` de l’application Host Manager :

        ```bash  
            sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
        ```

    - Enregistrez et fermez le fichier.

11. Redémarrez le service Tomcat pour mettre à jour le serveur avec les modifications :

    ```bash  
        sudo systemctl restart tomcat
    ```

12. Ouvrez un navigateur dans le réseau de votre système Azure Stack et ouvrez votre serveur : `yourmachine.local.cloudapp.azurestack.external:8080`.

    - Sélectionnez Server Status pour contrôler l’état du serveur Tomcat et vérifier que vous y avez accès.
    - Connectez-vous avec vos informations d’identification Tomcat.

![Apache Tomcat sur une machine virtuelle Azure Stack](media/azure-stack-dev-start-howto-vm-java/apache-tomcat-management-app.png)

## <a name="create-an-app"></a>Créer une application

Il est nécessaire de créer un fichier WAR pour effectuer un déploiement sur Tomcat. Si vous souhaitez simplement vérifier votre environnement, vous trouverez un exemple de fichier WAR sur le site Apache Tomcat : [exemple d’application](https://tomcat.apache.org/tomcat-6.0-doc/appdev/sample/).

Pour obtenir des conseils sur le développement d’applications Java dans Azure, voir [Créer et déployer des applications Java sur Azure](https://azure.microsoft.com/develop/java/).

## <a name="deploy-and-run-the-app"></a>Déployer et exécuter l’application

1. Connectez-vous à votre machine virtuelle en utilisant votre client SSH. Pour obtenir des instructions, voir [Se connecter par SSH avec PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
1. Arrêtez le service Tomcat pour mettre à jour le serveur avec votre package d’application :

    ```bash  
        sudo systemctl stop tomcat
    ```

2.  Ajoutez votre utilisateur FTP au groupe Tomcat pour pouvoir écrire dans le dossier webapps. Il s’agit de l’utilisateur que vous avez défini lors de la création de votre machine virtuelle dans Azure Stack.

    ```bash  
        sudo usermod -a -G tomcat <VM-user>
    ```

3. Connectez-vous à votre machine virtuelle avec FileZilla pour effacer le dossier webapps, puis charger votre fichier WAR créé ou mis à jour. Pour obtenir des instructions sur FileZilla, voir [Se connecter par SFTP avec FileZilla](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-sftp-with-filezilla).
    - Effacez `TOMCAT_HOME/webapps`.
    - Ajoutez votre fichier WAR à ` TOMCAT_HOME/webapps`, par exemple `/opt/tomcat/webapps/`.

4.  Tomcat développe et déploie automatiquement l’application. Vous pouvez la voir avec le nom DNS que vous avez créé tout à l’heure. Par exemple : 

    ```HTTP  
       http://yourmachine.local.cloudapp.azurestack.external:8080/sample

## Next steps

- Learn more about how to [Develop for Azure Stack](azure-stack-dev-start.md)
- Learn about [common deployments for Azure Stack as IaaS](azure-stack-dev-start-deploy-app.md).