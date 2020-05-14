---
title: Effectuer la rotation des secrets et des certificats App Service sur Azure Stack Hub
description: Découvrez comment effectuer la rotation des secrets et des certificats utilisés par Azure App Service sur Azure Stack Hub.
author: apwestgarth
ms.topic: article
ms.date: 04/09/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/09/2020
ms.openlocfilehash: 3a39329ba8c99e919b2466705553e11b49424c45
ms.sourcegitcommit: ddcd083430ca905653d412dc2f7b813218d79509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374726"
---
# <a name="rotate-app-service-on-azure-stack-hub-secrets-and-certificates"></a>Effectuer la rotation des secrets et des certificats App Service sur Azure Stack Hub

Ces instructions s’appliquent uniquement à Azure App Service sur Azure Stack Hub. La rotation des secrets Azure App Service sur Azure Stack Hub n’est pas incluse dans la procédure de rotation des secrets centralisée pour Azure Stack Hub. Les opérateurs peuvent superviser la validité des secrets au sein du système, la date de leur dernière mise à jour ainsi que le temps restant avant leur expiration.

> [!Important]
> Les opérateurs ne reçoivent pas d’alertes en cas d’expiration des secrets dans le tableau de bord Azure Stack Hub, car Azure App Service sur Azure Stack Hub n’est pas intégré au service d’alerte Azure Stack Hub. Les opérateurs doivent régulièrement superviser leurs secrets à l’aide de l’expérience d’administrateur Azure App Service sur Azure Stack Hub dans le portail d’administration Azure Stack Hub.

Ce document décrit la procédure permettant d'effectuer la rotation des secrets suivants :

* Clés de chiffrement utilisées dans Azure App Service sur Azure Stack Hub
* Informations d’identification de connexion de base de données utilisées par Azure App Service sur Azure Stack Hub pour interagir avec les bases de données d’hébergement et de contrôle
* Certificats utilisés par Azure App Service sur Azure Stack Hub pour sécuriser les points de terminaison et la rotation des certificats d’application d’identité dans Azure Active Directory (Azure AD) ou les services de fédération Active Directory (AD FS)
* Informations d’identification système pour les rôles d'infrastructure Azure App Service sur Azure Stack Hub.

## <a name="rotate-encryption-keys"></a>Effectuer la rotation des clés de chiffrement

Pour effectuer la rotation des clés de chiffrement utilisées dans Azure App Service sur Azure Stack Hub, procédez comme suit :

1. Accédez à l’expérience d’administration App Service dans le portail d’administration Azure Stack Hub.

1. Accédez à l’option de menu **Secrets**.

1. Sélectionnez le bouton **Rotation** de la section Clés de chiffrement.

1. Sélectionnez **OK** pour démarrer la procédure de rotation.

1. La rotation des clés de chiffrement intervient et toutes les instances de rôle sont mises à jour. Les opérateurs peuvent vérifier l’état de la procédure à l’aide du bouton **État**.

## <a name="rotate-connection-strings"></a>Effectuer la rotation des chaînes de connexion

Pour mettre à jour les informations d’identification de chaîne de connexion des bases de données d’hébergement et de contrôle App Service, procédez comme suit :

1. Accédez à l’expérience d’administration App Service dans le portail d’administration Azure Stack Hub.

1. Accédez à l’option de menu **Secrets**.

1. Sélectionnez le bouton **Rotation** de la section Chaînes de connexion.

1. Indiquez le **Nom d’utilisateur de l’administrateur système SQL** et le **Mot de passe**, puis sélectionnez **OK** pour démarrer la procédure de rotation.

1. La rotation des informations d’identification intervient dans les instances de rôle Azure App Service. Les opérateurs peuvent vérifier l’état de la procédure à l’aide du bouton **État**.

## <a name="rotate-certificates"></a>Effectuer une rotation des certificats

Pour effectuer la rotation des certificats utilisés dans Azure App Service sur Azure Stack Hub, procédez comme suit :

1. Accédez à l’expérience d’administration App Service dans le portail d’administration Azure Stack Hub.

1. Accédez à l’option de menu **Secrets**.

1. Sélectionnez le bouton **Rotation** de la section Certificats.

1. Indiquez le **fichier de certificat** et le **mot de passe** associé aux certificats dont vous souhaitez effectuer la rotation, puis sélectionnez **OK**.

1. La rotation des certificats intervient en fonction des besoins dans les instances de rôle Azure App Service sur Azure Stack Hub. Les opérateurs peuvent vérifier l’état de la procédure à l’aide du bouton **État**.

Lors de la rotation du certificat d’application d’identité, l’application correspondante dans Azure AD ou AD FS doit également être mise à jour avec le nouveau certificat.

> [!IMPORTANT]
> L’impossibilité de mettre à jour l’application d’identité avec le nouveau certificat, après la rotation, arrête l’expérience du portail de l’utilisateur pour Azure Functions, empêche les utilisateurs de se servir des outils de développement KUDU et empêche les administrateurs de gérer les groupes identiques de niveau worker à partir de l’expérience d’administration App Service.

### <a name="rotate-credential-for-the-azure-ad-identity-application"></a>Effectuer la rotation des informations d’identification pour l’application d’identité Azure AD

L’application d’identité est créée par l’opérateur avant le déploiement d’Azure App Service sur Azure Stack Hub. Si l’ID d’application est inconnu, procédez comme suit pour le découvrir :

1. Accédez au **portail administrateur Azure Stack Hub**.

1. Accédez à **Abonnements** et sélectionnez **Abonnement Fournisseur par défaut**.

1. Sélectionnez **Contrôle d’accès (IAM)** et l’application **App Service**.

1. Notez la valeur **ID de l’application**, qui correspond à l’ID de l’application d’identité qui doit être mise à jour dans Azure AD.

Pour effectuer la rotation du certificat pour l’application dans Azure AD, procédez comme suit :

1. Accédez au **portail Azure** et connectez-vous à l’aide de l’administrateur général utilisé pour déployer Azure Stack Hub.

1. Accédez à **Azure Active Directory**, puis à **Inscriptions d’applications**.

1. Recherchez l’**ID d’application**, puis spécifiez l’ID de l’application d’identité.

1. Sélectionnez l’application, puis accédez à **Certificats et secrets**.

1. Sélectionnez **Charger le certificat** et chargez le nouveau certificat pour l’application d’identité avec l’un des types de fichiers suivants : .cer, .pem, .crt.

1. Confirmez que l’**empreinte numérique** correspond à celle listée dans l’expérience d’administration App Service dans le portail d’administration Azure Stack Hub.

1. Supprimez l’ancien certificat.

### <a name="rotate-certificate-for-ad-fs-identity-application"></a>Effectuer la rotation du certificat pour l’application d’identité AD FS

L’application d’identité est créée par l’opérateur avant le déploiement d’Azure App Service sur Azure Stack Hub. Si l’ID d’objet de l’application est inconnu, procédez comme suit pour le découvrir :

1. Accédez au **portail administrateur Azure Stack Hub**.

1. Accédez à **Abonnements** et sélectionnez **Abonnement Fournisseur par défaut**.

1. Sélectionnez **Contrôle d’accès (IAM)** et l’application **AzureStack-AppService-<guid>** .

1. Notez la valeur **ID de l’objet**, qui correspond à l’ID du principal de service qui doit être mis à jour dans AD FS.

Pour effectuer la rotation du certificat pour l’application dans AD FS, vous devez avoir accès au point de terminaison privilégié (PEP). Ensuite, vous mettez à jour les informations d’identification du certificat à l’aide de PowerShell, en substituant vos propres valeurs aux espaces réservés suivants :

| Espace réservé | Description |  Exemple |
| ----------- | ----------- | ------- |
| `<PepVM>` | Nom de la machine virtuelle de point de terminaison privilégié sur votre instance d’Azure Stack Hub. | « AzS-ERCS01 » |
| `<CertificateFileLocation>` | Emplacement de votre certificat X509 sur le disque. | « d:\certs\sso.cer » |
| `<ApplicationObjectId>` | Identificateur affecté à l’application d’identité. | « S-1-5-21-401916501-2345862468-1451220656-1451 » |

1. Ouvrez une session Windows PowerShell avec élévation de privilèges et exécutez le script suivant :

    ```powershell
    # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint
    $Creds = Get-Credential

    # Create a new Certificate object from the identity application certificate exported as .cer file
    $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("<CertificateFileLocation>")

    # Create a new PSSession to the PrivelegedEndpoint VM
    $Session = New-PSSession -ComputerName "<PepVm>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # Use the privileged endpoint to update the certificate thumbprint, used by the service principal associated with the App Service identity application
    $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<ApplicationObjectId>" -ClientCertificates $using:Cert}
    $Session | Remove-PSSession

    # Output the updated service principal details
    $SpObject

    ```

1. Une fois l’exécution du script terminée, les informations mises à jour sur l’inscription de l’application s’affichent, incluant la valeur de l’empreinte numérique du certificat.

    ```shell
    ApplicationIdentifier : S-1-5-21-401916501-2345862468-1451220656-1451
    ClientId              : 
    Thumbprint            : FDAA679BF9EDDD0CBB581F978457A37BFD73CA3B
    ApplicationName       : Azurestack-AppService-d93601c2-1ec0-4cac-8d1c-8ccde63ef308
    ClientSecret          : 
    PSComputerName        : AzS-ERCS01
    RunspaceId            : cb471c79-a0d3-40ec-90ba-89087d104510
    ```

## <a name="rotate-system-credentials"></a>Effectuer la rotation des informations d'identification système

Pour effectuer la rotation des informations d’identification système utilisées dans Azure App Service sur Azure Stack Hub, procédez comme suit :

1. Accédez à l’expérience d’administration App Service dans le portail d’administration Azure Stack Hub.

1. Accédez à l’option de menu **Secrets**.

1. Sélectionnez le bouton **Rotation** de la section Informations d’identification système.

1. Sélectionnez l’**Étendue** des informations d’identification système dont vous effectuez la rotation. Les opérateurs peuvent choisir de faire pivoter les informations d’identification système pour tous les rôles ou pour des rôles individuels.

1. Spécifiez un nouveau **Nom d’utilisateur administrateur local** et un nouveau **Mot de passe**. Ensuite, confirmez le **Mot de passe**, puis sélectionnez **OK**.

1. La rotation des informations d’identification intervient en fonction des besoins dans l’instance de rôle Azure App Service sur Azure Stack Hub correspondante. Les opérateurs peuvent vérifier l’état de la procédure à l’aide du bouton **État**.
