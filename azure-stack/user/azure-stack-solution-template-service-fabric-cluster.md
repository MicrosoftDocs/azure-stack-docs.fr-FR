---
title: Déployer un cluster Service Fabric sécurisé dans Azure Stack Hub
description: Découvrez comment déployer un cluster Service Fabric sécurisé dans Azure Stack Hub
author: mattbriggs
ms.topic: tutorial
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: shnatara
ms.lastreviewed: 09/25/2019
ms.openlocfilehash: 4ccbdfe93f8ed960002c251e0d18e24f29a9b229
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84111861"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack-hub"></a>Déployer un cluster Service Fabric dans Azure Stack Hub

Pour déployer un cluster Service Fabric sécurisé dans Azure Stack Hub, utilisez l’élément **Cluster Service Fabric** de la Place de marché Azure. 

Pour plus d’informations sur l’utilisation de Service Fabric, consultez les articles [Vue d’ensemble d’Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) et [Scénarios de sécurité d’un cluster Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) dans la documentation Azure.

Le cluster Service Fabric dans Azure Stack Hub n’utilise pas le fournisseur de ressources Microsoft.ServiceFabric. Au lieu de cela, dans Azure Stack Hub, le cluster Service Fabric est un groupe de machines virtuelles identiques avec des logiciels préinstallés à l’aide de [DSC (Desired State Configuration)](https://docs.microsoft.com/powershell/scripting/dsc/overview/overview).

## <a name="prerequisites"></a>Prérequis

Le déploiement du cluster Service Fabric requiert les éléments suivants :
1. **Certificat de cluster**  
   Il s’agit du certificat de serveur X.509 que vous ajoutez au coffre de clés quand vous déployez Service Fabric. 
   - Le **CN** (nom commun) figurant sur ce certificat doit correspondre au nom de domaine complet (FQDN) du cluster Service Fabric que vous créez. 
   - Le certificat doit présenter le format PFX, car la procédure requiert à la fois les clés publiques et privées. 
     Consultez les [exigences](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) relatives à la création de ce certificat côté serveur.

     > [!NOTE]  
     > Vous pouvez utiliser un certificat auto-signé à la place du certificat de serveur X.509 à des fins de test. Les certificats auto-signés ne doivent pas nécessairement correspondre au FQDN du cluster.

1. **Certificat client d’administration**  
   Il s’agit du certificat, éventuellement auto-signé, que le client utilise pour s’authentifier auprès du cluster Service Fabric. Consultez les [exigences](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) relatives à la création de ce certificat client.

1. **Les éléments suivants doivent être disponibles dans la Place de marché Azure Stack Hub :**
    - **Windows Server 2016** : le modèle utilise l’image Windows Server 2016 pour créer le cluster.  
    - **Extension de script personnalisé** - Extension de machine virtuelle de Microsoft.  
    - **PowerShell Desired Stage Configuration** : extension de machine virtuelle de Microsoft.


## <a name="add-a-secret-to-key-vault"></a>Ajouter un secret au coffre de clés
Pour déployer un cluster Service Fabric, vous devez spécifier l’*identificateur de secret* du coffre de clés ou l’URL appropriés pour le cluster Service Fabric. Le modèle Azure Resource Manager utilise un coffre de clés comme entrée. Le modèle récupère ensuite le certificat du cluster lors de l’installation du cluster Service Fabric.

> [!IMPORTANT]  
> Vous devez utiliser PowerShell pour ajouter un secret au coffre de clés en vue d’une utilisation avec Service Fabric. N’utilisez pas le portail.  

Utilisez le script ci-après pour créer le coffre de clés et lui ajouter le *certificat de cluster*. (Consultez les [prérequis](#prerequisites).) Avant d’exécuter le script, consultez l’exemple de script et mettez à jour les paramètres indiqués pour les faire correspondre à votre environnement. Ce script génère également les valeurs que vous devez fournir au modèle Azure Resource Manager. 

> [!TIP]  
> La réussite de l’exécution du script requiert l’existence préalable d’une offre publique incluant les services relatifs aux ressources de calcul, de réseau, de stockage et de coffre de clés. 

  ```powershell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


Pour plus d’informations, consultez l’article [Gérer Key Vault dans Azure Stack Hub avec PowerShell](azure-stack-key-vault-manage-powershell.md).

## <a name="deploy-the-marketplace-item"></a>Déployer l’élément Place de marché

1. Dans le portail utilisateur, accédez à **+ Créer une ressource** > **Compute** > **Cluster Service Fabric**. 

   ![Sélection du cluster Service Fabric](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

2. Pour chaque page, telle que *De base*, remplissez le formulaire de déploiement. Si vous avez des doutes concernant une valeur, utilisez les valeurs par défaut.

    Pour les déploiements sur une instance Azure Stack Hub déconnectée ou pour déployer une autre version de Service Fabric, téléchargez le package de déploiement Service Fabric et son package d’exécution correspondant et hébergez-les sur un objet blob Azure Stack Hub. Fournissez ces valeurs dans les champs **URL du package de déploiement Service Fabric** et **URL du package d’exécution Service Fabric**.
    > [!NOTE]  
    > Il existe des problèmes de compatibilité entre la dernière version de Service Fabric et son kit SDK correspondant. Tant que ce problème n’est pas résolu, fournissez les paramètres suivants à l’URL du package de déploiement et à l’URL du package d’exécution. Sinon, vos déploiements échoueront.
    > - URL du package de déploiement Service Fabric : <https://download.microsoft.com/download/8/3/6/836E3E99-A300-4714-8278-96BC3E8B5528/6.5.641.9590/Microsoft.Azure.ServiceFabric.WindowsServer.6.5.641.9590.zip>
    > - URL du package d’exécution Service Fabric : <https://download.microsoft.com/download/B/0/B/B0BCCAC5-65AA-4BE3-AB13-D5FF5890F4B5/6.5.641.9590/MicrosoftAzureServiceFabric.6.5.641.9590.cab>
    >
    > Pour les déploiements déconnectés, téléchargez ces packages à partir de l’emplacement spécifié et hébergez-les localement sur un objet blob Azure Stack Hub.

   ![Concepts de base](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

    
3. Sur la page *Paramètres réseau*, vous pouvez spécifier les ports spécifiques à ouvrir pour vos applications :

   ![Paramètres réseau](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

4. Dans la page *Sécurité*, ajoutez les valeurs que vous avez obtenues lors des étapes de [création du coffre de clés Azure Key Vault](#add-a-secret-to-key-vault) et du chargement du secret.

   Pour le champ *Admin Client Certificate Thumbprints* (Empreinte du certificat client d’administration), entrez l’empreinte du *certificat client d’administration*. (Consultez les [prérequis](#prerequisites).)
   
   - Coffre de clés source :  spécifiez la chaîne `keyVault id` complète indiquée dans les résultats du script. 
   - Cluster Certificate URL (URL du certificat de cluster) : spécifiez l’URL complète de l’entrée `Secret Id` indiquée dans les résultats du script. 
   - Cluster Certificate thumbprint (Empreinte numérique du certificat de cluster) : spécifiez la valeur *Cluster Certificate Thumbprint* obtenue dans les résultats du script.
   - URL du certificat de serveur : Si vous souhaitez utiliser un autre certificat que le certificat de cluster, chargez le certificat dans un coffre de clés et fournissez l’URL complète du secret. 
   - Empreinte numérique du certificat de serveur : Spécifier l’empreinte numérique du certificat de serveur
   - Admin Client Certificate Thumbprints (Empreinte numérique du certificat client d’administration) : spécifiez la valeur de l’*empreinte numérique du certificat client d’administration* que vous avez créée dans les prérequis. 

   ![Sortie du script](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Sécurité](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

5. Terminez l’Assistant, puis sélectionnez **Créer** pour déployer le cluster Service Fabric.



## <a name="access-the-service-fabric-cluster"></a>Accéder au cluster Service Fabric
Vous pouvez accéder au cluster Service Fabric en utilisant l’outil Service Fabric Explorer ou PowerShell Service Fabric.


### <a name="use-service-fabric-explorer"></a>Utiliser Service Fabric Explorer
1.  Assurez-vous que le navigateur a accès à votre certificat client d’administration et qu’il peut s’authentifier auprès de votre cluster Service Fabric.  

    a. Ouvrez Internet Explorer, puis accédez à **Options Internet** > **Contenu** > **Certificats**.
  
    b. Sur la page Certificats, sélectionnez **Importer** pour démarrer *l’Assistant Importation de certificat*, puis cliquez sur **Suivant**. Sur la page *Fichier à importer*, cliquez sur **Parcourir**, puis sélectionnez le **Certificat client d’administration** que vous avez fourni au modèle Azure Resource Manager.
        
       > [!NOTE]  
       > Ce certificat ne correspond pas au certificat de cluster qui a été précédemment ajouté au coffre de clés.  

    c. Vérifiez que l’option « Échange d’informations personnelles » est sélectionnée dans le menu déroulant d’extensions de la fenêtre Explorateur de fichiers.  

       ![Échange d’informations personnelles](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. Sur la page *Magasin de certificats*, sélectionnez **Personnel**, puis terminez l’Assistant.  
       ![Magasin de certificats](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
1. Pour rechercher le FQDN de votre cluster Service Fabric :  

    a. Accédez au groupe de ressources associé à votre cluster Service Fabric, puis recherchez la ressource *Adresse IP publique*. Sélectionnez l’objet associé à l’adresse IP publique pour ouvrir le panneau *Adresse IP publique*.  

      ![Adresse IP publique](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. Dans le panneau Adresse IP publique, le FQDN s’affiche sous la forme *Nom DNS*.  

      ![Nom DNS](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

1. Pour rechercher l’URL de Service Fabric Explorer, ainsi que le point de terminaison de connexion du client, consultez les résultats de la solution Template deployment.

1. Dans votre navigateur, accédez à <https://*FQDN*:19080>. Remplacez *FQDN* par le FQDN de votre cluster Service Fabric trouvé à l’étape 2.   
   Si vous avez utilisé un certificat auto-signé, vous recevrez un avertissement signalant que la connexion n’est pas sécurisée. Pour accéder au site web, sélectionnez **Informations supplémentaires**, puis **Atteindre la page web**. 

1. Pour vous authentifier auprès du site, vous devez sélectionner un certificat à utiliser. Sélectionnez **Autres choix**, sélectionnez le certificat approprié, puis cliquez sur **OK** pour vous connecter à Service Fabric Explorer. 

   ![Authenticate](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



### <a name="use-service-fabric-powershell"></a>Utiliser PowerShell Service Fabric

1. Installez le *Kit de développement logiciel (SDK) Microsoft Azure Service Fabric* en suivant les instructions de la procédure [Préparer votre environnement de développement sur Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) dans la documentation Azure Service Fabric.  

1. Une fois l’installation terminée, configurez les variables d’environnement système pour vous assurer que les cmdlets Service Fabric sont accessibles à partir de PowerShell.  
    
    a. Accédez à **Panneau de configuration** > **Système et sécurité** > **Système**, puis sélectionnez **Paramètres système avancés**.  
    
      ![Panneau de configuration](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. Dans l’onglet **Avancé** de *Propriétés système*, sélectionnez **Variables d’environnement**.  

    c. Dans la zone *Variables système*, modifiez **Path** et assurez-vous que l’emplacement **C:\\Program Files\\Microsoft Service Fabric\\bin\\Fabric\\Fabric.Code** figure au début de la liste des variables d’environnement.  

      ![Liste de variables d’environnement](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

1. Après avoir modifié l’ordre des variables d’environnement, redémarrez PowerShell, puis exécutez le script PowerShell ci-après pour accéder au cluster Service Fabric :

   ```powershell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ```
   
   > [!NOTE]  
   > Le nom du cluster dans le script n’est pas précédé de *https://* . Le port 19000 est requis.

## <a name="next-steps"></a>Étapes suivantes

[Déployer Kubernetes sur Azure Stack Hub](azure-stack-solution-template-kubernetes-deploy.md)
