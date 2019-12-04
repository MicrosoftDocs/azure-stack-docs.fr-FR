---
title: Modifier des paramètres spécifiques dans votre configuration de commutateur Azure Stack | Microsoft Docs
description: Découvrez ce que vous pouvez personnaliser dans votre configuration de commutateur Azure Stack. Une fois que le fabricant d’ordinateurs (OEM) a créé la configuration, ne la modifiez pas sans le consentement de l’équipe d’ingénierie OEM ou Microsoft Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: Femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 99d52dc7165fdd37a614c1396d7e5c1f0165e235
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74299129"
---
#  <a name="modify-specific-settings-on-your-azure-stack-switch-configuration"></a>Modifier des paramètres spécifiques dans votre configuration de commutateur Azure Stack

Vous pouvez modifier quelques paramètres environnementaux de votre configuration de commutateur Azure Stack. Vous pouvez identifier les paramètres pouvant être modifiés dans le modèle créé par votre fabricant d’ordinateurs (OEM). Cet article explique chacun de ces paramètres personnalisables ainsi que l’impact possible des modifications sur l’infrastructure Azure Stack. Ces paramètres incluent la mise à jour du mot de passe, le serveur Syslog, l’analyse SNMP, l’authentification et la liste de contrôle d’accès. 

Pendant le déploiement de la solution Azure Stack, le fabricant d’ordinateurs (OEM) crée et applique la configuration du commutateur pour Tor et pour BMC. Le fabricant d’ordinateurs (OEM) utilise l’outil d’automatisation Azure Stack pour vérifier que les configurations requises sont correctement définies sur ces appareils. La configuration est basée sur les informations contenues dans votre [feuille de calcul de déploiement](azure-stack-deployment-worksheet.md) Azure Stack. Une fois que l’OEM a créé la configuration, ne modifiez **pas** la configuration sans le consentement de l’équipe d’ingénierie OEM ou Microsoft Azure Stack. Une modification de la configuration des périphériques réseau peut avoir un impact significatif sur le fonctionnement ou la résolution de problèmes réseau dans votre instance Azure Stack.

Toutefois, certaines valeurs peuvent être ajoutées, supprimées ou modifiées dans la configuration des commutateurs réseau.

>[!Warning]  
> Ne modifiez **pas** la configuration sans le consentement de l’équipe d’ingénierie OEM ou Microsoft Azure Stack. Une modification de la configuration des périphériques réseau peut avoir un impact significatif sur le fonctionnement ou la résolution de problèmes réseau dans votre instance Azure Stack.
>
> Pour plus d’informations sur ces fonctions de votre périphérique réseau ou sur la manière d’effectuer ces modifications, contactez votre fournisseur de matériel OEM ou le support Microsoft. Votre OEM dispose du fichier de configuration créé par l’outil d’automatisation sur la base de votre feuille de déploiement Azure Stack. 

## <a name="password-update"></a>Mise à jour du mot de passe

L’opérateur peut à tout moment mettre à jour le mot de passe d’un utilisateur sur les commutateurs réseau. Il n’est pas nécessaire de modifier les informations sur le système de Azure Stack ou d’utiliser les étapes pour [Faire pivoter les secrets dans Azure Stack](azure-stack-rotate-secrets.md).

## <a name="syslog-server"></a>Serveur syslog

Les opérateurs peuvent rediriger les journaux du commutateur vers un serveur Syslog dans leur centre de données. Utilisez cette configuration pour vous assurer que les journaux d’un point spécifique dans le temps peuvent être utilisés pour résoudre des problèmes. Par défaut, les journaux sont stockés sur les commutateurs ; leur capacité de stockage de journaux est limitée. Consultez la section [Mises à jour de la liste de contrôle d’accès](#access-control-list-updates) pour avoir une vue d’ensemble de la configuration des autorisations pour l’accès à la gestion des commutateurs.

## <a name="snmp-monitoring"></a>Analyse SNMP

L’opérateur peut configurer SNMP (simple Network Management Protocol) v2 ou v3 pour surveiller les périphériques réseau et envoyer des interruptions à une application de surveillance réseau dans le centre de données. Pour des raisons de sécurité, utilisez SNMPv3, qui est plus sécurisé que v2. Consultez votre fournisseur de matériel OEM pour obtenir les MIB et les configurations requises. Consultez la section [Mises à jour de la liste de contrôle d’accès](#access-control-list-updates) pour avoir une vue d’ensemble de la configuration des autorisations pour l’accès à la gestion des commutateurs.

## <a name="authentication"></a>Authentication

L’opérateur peut configurer RADIUS ou TACACS pour gérer l’authentification sur les périphériques réseau. Consultez votre fournisseur de matériel OEM pour obtenir les méthodes prise en charge et la configuration requise.  Consultez la section [Mises à jour de la liste de contrôle d’accès](#access-control-list-updates) pour avoir une vue d’ensemble de la configuration des autorisations pour l’accès à la gestion des commutateurs.

## <a name="access-control-list-updates"></a>Mises à jour de la liste de contrôle d'accès

> [!NOTE]
> Depuis la version 1910, la feuille de calcul de déploiement a un nouveau champ pour les **réseaux autorisés**, qui remplace les étapes manuelles requises pour autoriser l’accès à des interfaces de gestion des périphériques réseau et à l’hôte HLH (hôte du cycle de vie du matériel) à partir d’une plage réseau de centre de données de confiance. Pour plus d’informations sur cette nouvelle fonctionnalité, consultez [Planification de l’intégration réseau pour Azure Stack](azure-stack-network.md#permitted-networks).

L’opérateur peut modifier certaines listes de contrôle d’accès (ACL) pour autoriser l’accès à des interfaces de gestion des périphériques réseau et à l’hôte de cycle de vie du matériel (HLH) à partir d’une plage réseau de centre de données de confiance. Avec la liste de contrôle d’accès, l’opérateur peut autoriser ses machines virtuelles Jumpbox de gestion dans une plage réseau spécifique à accéder à l’interface de gestion du commutateur, au système d’exploitation HLH et au contrôleur BMC HLH.

## <a name="next-steps"></a>Étapes suivantes

[Intégration au centre de données Azure Stack - DNS](azure-stack-integrate-dns.md)
