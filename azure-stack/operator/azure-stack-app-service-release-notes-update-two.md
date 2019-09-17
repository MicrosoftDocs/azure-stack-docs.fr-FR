---
title: Notes de publication d’App Service sur Azure Stack Update 2 | Microsoft Docs
description: Découvrez les améliorations, les correctifs et les problèmes connus concernant la version Update 2 d’App Service pour Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 05/18/2018
ms.openlocfilehash: c85cc176949c9f3c86cded80be14417add6c40da
ms.sourcegitcommit: 7d7a4c8c46613b6104caf23763bfd2275f6a826b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70808133"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>Notes de publication d’App Service sur Azure Stack Update 2

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Ces notes de publication décrivent les améliorations, les correctifs et les problèmes connus concernant Azure App Service sur Azure Stack Update 2. Les problèmes connus ont été répartis en trois sections : ceux qui concernent directement le déploiement, ceux qui concernent le processus de mise à jour et ceux qui concernent la build (après l’installation).

> [!IMPORTANT]
> Appliquez la mise à jour 1804 à votre système intégré Azure Stack ou déployez le dernier kit de développement Azure Stack (ASDK) avant de déployer Azure App Service 1.2.

## <a name="build-reference"></a>Référence de build

Le numéro de build d’App Service sur Azure Stack Update 2 est **72.0.13698.10**.

### <a name="prerequisites"></a>Prérequis

> [!IMPORTANT]
> Les nouveaux déploiements d’Azure App Service sur Azure Stack nécessitent désormais un [certificat avec caractères génériques à trois sujets](azure-stack-app-service-before-you-get-started.md#get-certificates) en raison des améliorations apportées à la gestion de l’authentification unique pour Kudu dans Azure App Service. Le nouveau sujet est : **\*.sso.appservice.\<région\>.\<nom_domaine\>.\<extension\>**

Avant de commencer le déploiement, consultez les [Prérequis pour le déploiement d’App Service sur Azure Stack](azure-stack-app-service-before-you-get-started.md).

### <a name="new-features-and-fixes"></a>Nouvelles fonctionnalités et correctifs

Azure App Service sur Azure Stack Update 2 inclut les améliorations et correctifs suivants :

- Mises à jour des **portails Locataire, Administration et Functions d’App Service, ainsi que des outils Kudu**. Cohérentes avec celles de la version du SDK du portail Azure Stack.

- Mises à jour du **runtime d’Azure Functions** à la **version 1.0.11612**.

- Mises à jour du service principal afin d’améliorer la fiabilité et l’envoi de messages d’erreur, ce qui facilite le diagnostic des problèmes courants.

- **Mises à jour des outils et frameworks d’applications suivants** :
  - Ajout de .NET Framework 4.7.1
  - Ajout des versions **Node.JS** :
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Ajout des versions **NPM** :
    - 5.6.0
  - Mise à jour des composants .NET Core pour qu’ils soient cohérents avec Azure App Service dans le cloud public.
  - Mise à jour de Kudu

- Activation de la fonctionnalité d’échange automatique des emplacements de déploiement - [Configuration de l’échange automatique](https://docs.microsoft.com/azure/app-service/deploy-staging-slots#configure-auto-swap).

- Activation de la fonctionnalité de test en production - [Présentation du test en production](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/).

- Activation d’Azure Functions Proxies - [Utilisation d’Azure Functions Proxies](https://docs.microsoft.com/azure/azure-functions/functions-proxies).

- Ajout de la prise en charge de l’expérience utilisateur dans l’extension d’administration d’App Service pour les opérations suivantes :
  - Rotation des secrets
  - Rotation des certificats
  - Rotation des informations d’identification système
  - Rotation des chaînes de connexion

### <a name="known-issues-post-installation"></a>Problèmes connus (après l’installation)

- Les Workers ne peuvent pas atteindre le serveur de fichiers si App Service est déployé dans un réseau virtuel existant et si le serveur de fichiers est uniquement disponible sur le réseau privé.

Si vous avez choisi de procéder au déploiement dans un réseau virtuel existant et avec une adresse IP interne pour vous connecter à votre serveur de fichiers, vous devez ajouter une règle de sécurité sortante, qui autorise le trafic SMB entre le sous-réseau Worker et le serveur de fichiers. Accédez au WorkersNsg dans le portail d’administration, puis ajoutez une règle de sécurité sortante comportant les propriétés suivantes :

* Source : Quelconque
* Plage de ports source : : *
* Destination : Adresses IP
* Plage d’adresses IP de destination : plage d’adresses IP de votre serveur de fichiers
* Plage de ports de destination : 445
* Protocole : TCP
* Action : AUTORISER
* Priorité : 700
* Nom : Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problèmes connus des administrateurs cloud utilisant Azure App Service sur Azure Stack

Reportez-vous à la documentation des [notes de publication d’Azure Stack 1804](azure-stack-update-1903.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation d’Azure App Service, consultez [Vue d’ensemble d’App Service sur Azure Stack](azure-stack-app-service-overview.md).
- Pour plus d’informations sur la préparation au déploiement d’App Service sur Azure Stack, consultez [Prérequis pour le déploiement d’App Service sur Azure Stack](azure-stack-app-service-before-you-get-started.md).
