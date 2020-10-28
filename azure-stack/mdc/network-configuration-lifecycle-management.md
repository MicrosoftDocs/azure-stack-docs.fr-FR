---
title: Gestion de cycle de vie d’une configuration réseau MDC
description: En savoir plus sur la gestion de cycle de vie de la configuration réseau pour l’appareil MDC.
author: justinha
ms.author: justinha
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/02/2020
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: 78e4a98a606b100e42d4beb465874d4d0648c971
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183390"
---
# <a name="configuration-lifecycle-management"></a>Gestion de cycle de vie de la configuration

Cette rubrique traite de la gestion de cycle de vie de la configuration du réseau.

## <a name="update-configuration"></a>Mettre à jour la configuration


Méthodologie de mise à jour des configurations du commutateur dans le champ. Cela s’applique à toutes et à tous les types de mises à jour de la configuration. En raison de nombreuses variables inconnues en dehors de notre contrôle, comme les configurations manuelles appliquées par les clients ou par l’OEM, il s’agit d’un processus manuel en plusieurs étapes. À ce stade, il n’y a aucune garantie de durée de bon fonctionnement et les mises à jour peuvent être effectuées au cours d’une fenêtre de maintenance.

**\#1 -** Effectuez une sauvegarde des fichiers de configuration actuels pour les commutateurs edge1, edge2, tor1, tor2 et BMC. Copiez ces fichiers en dehors du commutateur.

**\#2 -** Notez le numéro de build de la boîte à outils de la configuration existante.
Toutes les configurations doivent être dans la bannière **motd** . Recherchez *« BUILDNUMBER »*

**\#3 -** Régénérez les configurations initiales à l’aide de la même version de kit d’outils que celle de l’étape \#2.

**\#4 -** Chargez les configurations de l’étape \#3 sur leurs commutateurs respectifs.
Il est alors nécessaire de *laver* la configuration générée par nos outils via un commutateur pour obtenir un planning de référence. Cela peut être effectué par l’OEM sur un matériel distinct, comme dans le labo de l’OEM ou sur site avec le client.

**\#5 -** Effectuez une sauvegarde du fichier config à partir de l’étape \#4 et copiez-la sur un emplacement distant

**\#6 -** À l’aide de l’outil Diff de votre choix, comparez la sauvegarde de la configuration actuelle de l’étape \#1 à la sauvegarde de la configuration du planning de référence à partir de l’étape \#5. Notez/copiez toutes les différences qui doivent être reportées à la configuration du commutateur mis à niveau par appareil.

**\#7 -** Exécutez la nouvelle boîte à outils qui génère les configurations du commutateur mis à jour.

**\#8 -** Fusionnez les différences à partir de l’étape 6 dans les nouvelles configurations du commutateur.

**\#9 -** Chargez les nouvelles configurations sur les commutateurs respectifs et exécutez les commandes post-validation fournies dans le répertoire de sortie à partir de nos outils.

**\#10 -** Enregistrez les configurations.

## <a name="ntp"></a>NTP

Vous pouvez utiliser le point de terminaison privilégié pour mettre à jour le serveur de temps dans Azure Stack. Utilisez un nom d’hôte qui se résout en deux adresses IP ou plus de serveur NTP.

Azure Stack utilise le protocole NTP (Network Time Protocol) pour se connecter aux serveurs de temps sur Internet. Les serveurs NTP fournissent une heure système précise. L’heure est utilisée sur les commutateurs réseau physiques d’Azure Stack, sur l’hôte de cycle de vie du matériel, sur le service d’infrastructure et sur les machines virtuelles. Si l’horloge n’est pas synchronisée, Azure Stack risque de rencontrer des problèmes graves avec le réseau et l’authentification. Des fichiers journaux, des documents et d’autres fichiers peuvent être créés avec des horodatages incorrects.

Au moins un serveur de temps (NTP) est requis pour qu’Azure Stack synchronise l’heure.
Quand vous déployez Azure Stack, vous fournissez l’adresse d’un serveur NTP. L’heure est un service d’infrastructure de centre de données critique. Si le service change, vous devez mettre à jour l’heure.

Azure Stack prend en charge la synchronisation de l’heure avec un seul serveur de temps (NTP). Vous ne pouvez pas fournir plusieurs serveurs de temps avec lesquels Azure Stack synchronise l’heure. Il est recommandé de configurer l’entrée DNS, résolue en plusieurs serveurs NTP. 


### <a name="update-ntp-post-deployment"></a>Mettre à jour le déploiement de la publication NTP

1.  Connectez-vous au point de terminaison privilégié (PEP). Vous n’avez pas besoin d’ouvrir un ticket de support pour déverrouiller le point de terminaison privilégié. 

2.  Exécutez la commande suivante pour examiner le serveur NTP configuré actuel :

    ```powershell
    Get-AzsTimeSource
    ```

3.  Exécutez la commande suivante pour mettre à jour Azure Stack et utiliser le nouveau serveur NTP afin de synchroniser immédiatement l’heure

    ```powershell
    Set-AzsTimeSource -Timeserver NEWTIMESERVER -resync
    ```

    >[!NOTE] 
    >Cette procédure ne met pas à jour le serveur de temps sur les commutateurs physiques. 


## <a name="dns"></a>DNS

Cette section explique comment mettre à jour les redirecteurs DNS pour résoudre les noms externes.

### <a name="update-the-dns-forwarder-in-azure-stack"></a>Mettre à jour le redirecteur DNS dans Azure Stack

Au moins un redirecteur DNS accessible est nécessaire pour que l’infrastructure Azure Stack résolve les noms externes. Un redirecteur DNS doit être fourni pour le déploiement d’Azure Stack. Cette entrée est utilisée pour les serveurs DNS internes d’Azure Stack en tant que redirecteur et permet de résoudre les noms externes pour des services tels que l’authentification, l’utilisation ou la gestion de la Place de marché.

DNS est un service d’infrastructure de centre de données critique qui peut changer, auquel cas Azure Stack doit être mis à jour.

Cet article décrit l’utilisation du point de terminaison privilégié (PEP) pour mettre à jour le redirecteur DNS dans Azure Stack. Il est recommandé d’utiliser deux adresses IP fiables de redirecteur DNS.

1.  Connectez-vous au point de terminaison privilégié. Notez qu’il n’est pas nécessaire de déverrouiller le point de terminaison privilégié en ouvrant un ticket de support.

2.  Exécutez la commande suivante pour passer en revue le redirecteur DNS configuré actuel. Une alternative consiste à utiliser les propriétés de région du portail d’administration :

    ```powershell
    Get-AzsDnsForwarder 
    ```

3.  Exécutez la commande suivante pour mettre à jour Azure Stack afin d’utiliser le nouveau redirecteur DNS :

    ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1", "IPAddress 2"
    ``` |

4.  Review the output of the command for any errors.
