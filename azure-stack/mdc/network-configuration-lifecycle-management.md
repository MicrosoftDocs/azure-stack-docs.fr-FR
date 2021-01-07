---
title: Gestion de cycle de vie d’une configuration réseau MDC
description: Découvrez la gestion de cycle de vie de la configuration réseau pour Azure Modular Data Center.
author: PatAltimore
ms.author: patricka
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/02/2020
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: 5453738374f9377645a6ba594dda08b8668931fe
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872328"
---
# <a name="configuration-lifecycle-management"></a>Gestion de cycle de vie de la configuration

Cet article couvre la gestion de cycle de vie de la configuration réseau pour Azure Modular Data Center.

## <a name="update-configuration"></a>Mettre à jour la configuration

Cette méthodologie de mise à jour des configurations de commutateur sur le tas s’applique à tous les types de mises à jour de configuration, quels qu’ils soient. En raison des nombreuses variables inconnues en dehors de notre contrôle, comme les configurations manuelles appliquées par les clients ou par l’OEM, il s’agit d’un processus manuel en plusieurs étapes. À ce stade, il n’existe aucune garantie de durée de bon fonctionnement. Les mises à jour doivent être effectuées pendant une fenêtre de maintenance.

1. Effectuez une sauvegarde des fichiers de configuration actuels pour les commutateurs edge1, edge2, tor1, tor2 et BMC. Copiez ces fichiers en dehors du commutateur.

1. Notez le numéro de build de la boîte à outils de la configuration existante. Toutes les configurations doivent avoir ce numéro dans la bannière **motd**. Recherchez **BUILDNUMBER**.

1. Regénérez les configurations initiales à l’aide de la même version de boîte à outils que celle de l’étape 2.

1. Chargez les configurations de l’étape 3 sur leurs commutateurs respectifs. Le but de cette action est de *laver* la configuration générée par nos outils par le biais d’un commutateur pour obtenir une base de référence. Cette action peut être effectuée par l’OEM sur du matériel distinct, comme dans le labo de l’OEM, ou sur site avec le client.

1. Effectuez une sauvegarde du fichier de configuration de l’étape 4. Copiez-la à un emplacement distant.

1. À l’aide de l’outil Diff de votre choix, comparez la sauvegarde de la configuration actuelle (étape 1) à la sauvegarde de la configuration de la base de référence (étape 5). Notez ou copiez toutes les différences qui doivent être reportées dans la configuration du commutateur mis à niveau par appareil.

1. Exécutez la nouvelle boîte à outils qui génère les configurations de commutateur mis à jour.

1. Fusionnez les différences identifiées à l’étape 6 dans les nouvelles configurations de commutateur.

1. Chargez les nouvelles configurations sur les commutateurs respectifs. Exécutez les commandes post-validation fournies dans le répertoire de sortie à partir de nos outils.

1. Enregistrez les configurations.

## <a name="ntp"></a>NTP

Vous pouvez utiliser le point de terminaison privilégié pour mettre à jour le serveur de temps dans Azure Stack. Utilisez un nom d’hôte qui se résout en deux adresses IP ou plus de serveur NTP (Network Time Protocol).

Azure Stack utilise le protocole NTP pour se connecter aux serveurs de temps sur Internet. Les serveurs NTP fournissent une heure système précise. L’heure est utilisée sur les commutateurs réseau physiques d’Azure Stack, sur l’hôte de cycle de vie du matériel, sur le service d’infrastructure et sur les machines virtuelles. Si l’horloge n’est pas synchronisée, Azure Stack risque de rencontrer des problèmes graves avec le réseau et l’authentification. Des fichiers journaux, des documents et d’autres fichiers peuvent être créés avec des horodatages incorrects.

Au moins un serveur de temps (NTP) est requis pour qu’Azure Stack synchronise l’heure. Quand vous déployez Azure Stack, vous fournissez l’adresse d’un serveur NTP. L’heure est un service d’infrastructure de centre de données critique. Si le service change, vous devez mettre à jour l’heure.

Azure Stack prend en charge la synchronisation de l’heure avec un seul serveur de temps (NTP). Vous ne pouvez pas fournir plusieurs serveurs NTP avec lesquels Azure Stack synchronise l’heure. Nous vous recommandons de configurer l’entrée DNS (Domain Name System) qui se résout en plusieurs serveurs NTP.

### <a name="update-ntp-post-deployment"></a>Mettre à jour le déploiement de la publication NTP

1. Connectez-vous au point de terminaison privilégié. Vous n’avez pas besoin d’ouvrir un ticket de support pour le déverrouiller.

1. Exécutez la commande suivante pour examiner le serveur NTP actuellement configuré.

    ```powershell
    Get-AzsTimeSource
    ```

1. Exécutez la commande suivante pour mettre à jour Azure Stack et utiliser le nouveau serveur NTP afin de synchroniser immédiatement l’heure.

    ```powershell
    Set-AzsTimeSource -Timeserver NEWTIMESERVER -resync
    ```

>[!NOTE]
>Cette procédure ne met pas à jour le serveur de temps sur les commutateurs physiques.

## <a name="dns"></a>DNS

Cette section explique comment mettre à jour les redirecteurs DNS pour résoudre les noms externes.

### <a name="update-the-dns-forwarder-in-azure-stack"></a>Mettre à jour le redirecteur DNS dans Azure Stack

Au moins un redirecteur DNS accessible est nécessaire pour que l’infrastructure Azure Stack résolve les noms externes. Un redirecteur DNS doit être fourni pour le déploiement d’Azure Stack. Cette entrée est utilisée pour les serveurs DNS internes d’Azure Stack en tant que redirecteur et permet de résoudre les noms externes pour des services tels que l’authentification, l’utilisation ou la gestion de la Place de marché.

DNS est un service d’infrastructure de centre de données critique susceptible d'évoluer. En cas de modification, Azure Stack doit être mis à jour.

Cet article décrit l’utilisation du point de terminaison privilégié (PEP) pour mettre à jour le redirecteur DNS dans Azure Stack. Nous recommandons d’utiliser deux adresses IP fiables de redirecteur DNS.

1. Connectez-vous au point de terminaison privilégié. Il n’est pas nécessaire de déverrouiller le point de terminaison privilégié en ouvrant un ticket de support.

1. Exécutez la commande suivante pour passer en revue le redirecteur DNS configuré actuel. Une alternative consiste à utiliser les propriétés de région du portail d’administration.

    ```powershell
    Get-AzsDnsForwarder 
    ```

1. Exécutez la commande suivante pour mettre à jour Azure Stack afin d’utiliser le nouveau redirecteur DNS.

    ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1", "IPAddress 2"
    ```

1. Recherchez les erreurs éventuelles dans la sortie de la commande.
