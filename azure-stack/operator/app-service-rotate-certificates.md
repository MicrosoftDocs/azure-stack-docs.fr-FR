---
title: Effectuer la rotation des secrets et des certificats App Service sur Azure Stack Hub
description: Découvrez comment effectuer la rotation des secrets et des certificats utilisés par Azure App Service sur Azure Stack Hub.
author: BryanLa
ms.topic: article
ms.date: 01/10/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/10/2020
ms.openlocfilehash: f1a42f5b04ea83d9ff9130fb63ba6833cd7d2914
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76876539"
---
# <a name="rotate-app-service-on-azure-stack-hub-secrets-and-certificates"></a>Effectuer la rotation des secrets et des certificats App Service sur Azure Stack Hub

Ces instructions s’appliquent uniquement à Azure App Service sur Azure Stack Hub.  La rotation des secrets Azure App Service sur Azure Stack Hub n’est pas incluse dans la procédure de rotation des secrets centralisée pour Azure Stack Hub.  Les opérateurs peuvent surveiller la validité des secrets au sein du système, la date de leur dernière mise à jour, ainsi que le temps restant avant leur expiration.

> [!Important]
> Les opérateurs ne reçoivent pas d’alertes en cas d'expiration des secrets dans le tableau de bord Azure Stack Hub, car Azure App Service sur Azure Stack Hub n’est pas intégré au service d’alerte Azure Stack Hub.  Les opérateurs doivent régulièrement surveiller leurs secrets à l’aide de l'expérience d'administrateur Azure App Service sur Azure Stack Hub dans le portail d'administration Azure Stack Hub.

Ce document décrit la procédure permettant d'effectuer la rotation des secrets suivants :

* Clés de chiffrement utilisées dans Azure App Service sur Azure Stack Hub ;
* Informations d’identification de connexion de base de données utilisées par Azure App Service sur Azure Stack Hub pour interagir avec les bases de données d’hébergement et de contrôle ;
* Certificats utilisés par Azure App Service sur Azure Stack Hub pour sécuriser les points de terminaison ;
* Informations d’identification système pour les rôles d'infrastructure Azure App Service sur Azure Stack Hub.

## <a name="rotate-encryption-keys"></a>Effectuer la rotation des clés de chiffrement

Pour effectuer la rotation des clés de chiffrement utilisées dans Azure App Service sur Azure Stack Hub, procédez comme suit :

1. Accédez à l’expérience d’administration App Service dans le portail d'administration Azure Stack Hub.

1. Accédez à l’option de menu **Secrets**.

1. Cliquez sur le bouton **Rotation** de la section Clés de chiffrement.

1. Cliquez sur **OK** pour démarrer la procédure de rotation.

1. La rotation des clés de chiffrement intervient et toutes les instances de rôle sont mises à jour. Les opérateurs peuvent surveiller l’état de la procédure à l’aide du bouton **État**.

## <a name="rotate-connection-strings"></a>Effectuer la rotation des chaînes de connexion

Pour mettre à jour les informations d’identification de chaîne de connexion des bases de données d'hébergement et de contrôle App Service, procédez comme suit :

1. Accédez à l’expérience d’administration App Service dans le portail d'administration Azure Stack Hub.

1. Accédez à l’option de menu **Secrets**.

1. Cliquez sur le bouton **Rotation** de la section Chaînes de connexion.

1. Indiquez le **Nom d'utilisateur de l'administrateur système SQL** et le **Mot de passe**, puis cliquez sur **OK** pour démarrer la procédure de rotation. 

1. La rotation des informations d’identification intervient dans les instances de rôle Azure App Service. Les opérateurs peuvent surveiller l’état de la procédure à l’aide du bouton **État**.

## <a name="rotate-certificates"></a>Effectuer une rotation des certificats

Pour effectuer la rotation des certificats utilisés dans Azure App Service sur Azure Stack Hub, procédez comme suit :

1. Accédez à l’expérience d’administration App Service dans le portail d'administration Azure Stack Hub.

1. Accédez à l’option de menu **Secrets**.

1. Cliquez sur le bouton **Rotation** de la section Certificats.

1. Indiquez le **fichier de certificat** et le **mot de passe** associé aux certificats dont vous souhaitez effectuer la rotation, puis cliquez sur **OK**.

1. La rotation des certificats intervient en fonction des besoins dans les instances de rôle Azure App Service sur Azure Stack Hub.  Les opérateurs peuvent surveiller l’état de la procédure à l’aide du bouton **État**.

## <a name="rotate-system-credentials"></a>Effectuer la rotation des informations d'identification système

Pour effectuer la rotation des informations d’identification système utilisées dans Azure App Service sur Azure Stack Hub, procédez comme suit :

1. Accédez à l’expérience d’administration App Service dans le portail d'administration Azure Stack Hub.

1. Accédez à l’option de menu **Secrets**.

1. Cliquez sur le bouton **Rotation** de la section Informations d’identification système.

1. Sélectionnez l'**Étendue** des informations d’identification système dont vous effectuez la rotation.  Les opérateurs peuvent choisir de faire pivoter les informations d’identification système pour tous les rôles ou pour des rôles individuels.

1. Indiquez le **Nom d'utilisateur administrateur local**, le nouveau **Mot de passe**, confirmez ce **Mot de passe**, puis cliquez sur **OK**.

1. La rotation des informations d’identification intervient en fonction des besoins dans l'instance de rôle Azure App Service sur Azure Stack Hub correspondante.  Les opérateurs peuvent surveiller l’état de la procédure à l’aide du bouton **État**.



