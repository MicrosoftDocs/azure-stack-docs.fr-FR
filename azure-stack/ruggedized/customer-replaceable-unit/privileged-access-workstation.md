---
title: Station de travail à accès privilégié et accès aux points de terminaison privilégiés
description: Apprenez-en davantage sur l’accès à la station de travail à accès privilégié et aux points de terminaison privilégiés.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 96a4e944b6b86c8b5db314141fd3473a8512d518
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256198"
---
# <a name="privileged-access-workstation-and-privileged-endpoint-access"></a>Station de travail à accès privilégié et accès aux points de terminaison privilégiés

## <a name="overview"></a>Vue d’ensemble

Pour cette procédure, vous devez vous connecter à la station de travail à accès privilégié. Le client devra vous donner les moyens de vous connecter à la station de travail à accès privilégié à l’aide du Bureau à distance.

## <a name="configuring-the-winrm"></a>Configuration de WinRM

Pour autoriser les connexions au point de terminaison privilégié à partir de la station de travail à accès privilégié, vérifiez que les adresses IP de point de terminaison privilégié, telles que définies dans le portail administrateur Azure Stack Hub, sont définies en tant qu’hôte approuvé sur la station de travail à accès privilégié. Les instructions pour obtenir ces adresses IP à partir du portail d’administration figurent dans Vérification de l’accès et de l’intégrité des nœuds d’unité d’échelle en page 16.

Pour afficher ou modifier les hôtes approuvés WinRM, lancez une session PowerShell avec élévation de privilèges :

-   Affichez les hôtes approuvés.

Pour afficher les hôtes approuvés actuels, dans PowerShell, exécutez :

-   Modifiez les hôtes approuvés.

Si les adresses IP ERCS (Emergency Recovery Console Server) ne sont pas présentes, exécutez la commande suivante pour définir une nouvelle valeur pour les hôtes approuvés, en remplaçant *\<ERCS01_IP\*, *\<ERCS02_IP\* et *\<ERCS03_IP\* par les trois adresses IP de point de terminaison privilégié définies dans le portail administrateur Azure Stack Hub :

## <a name="connect-to-the-privileged-endpoint"></a>Connectez-vous au point de terminaison privilégié.

Sur la station de travail à accès privilégié, ouvrez une session PowerShell avec élévation de privilèges et exécutez les deux commandes suivantes. Remplacez *\<ERCS_IP\* par l’adresse IP de l’une des instances de point de terminaison privilégié, comme indiqué plus haut dans cette procédure. À l’invite, entrez les informations d’identification de point de terminaison privilégié fournies par le client.

## <a name="close-the-privileged-endpoint"></a>Fermer le point de terminaison privilégié

Pour fermer la session de point de terminaison privilégié, exécutez la commande suivante :

## <a name="further-reading"></a>Lectures supplémentaires

Pour plus d’informations sur la connexion et l’utilisation du point de terminaison privilégié, consultez [Utiliser le point de terminaison privilégié dans Azure Stack](../../operator/azure-stack-privileged-endpoint.md)
[Hub](../../operator/azure-stack-privileged-endpoint.md).