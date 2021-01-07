---
title: Intégrer l’audit des appareils physiques à votre centre de données Azure Stack Hub
description: Découvrez comment intégrer l’audit des accès aux appareils physiques à votre centre de données Azure Stack Hub.
author: PatAltimore
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 2bfef311e8a57187aef9e8af2c4f110f8dd54ba2
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870968"
---
# <a name="integrate-physical-device-auditing-with-your-azure-stack-hub-datacenter"></a>Intégrer l’audit des appareils physiques à votre centre de données Azure Stack Hub

Tous les appareils physiques dans Azure Stack Hub, tels que les contrôleurs de gestion de carte de base (BMC) et les commutateurs réseau, émettent des journaux d’audit. Vous pouvez intégrer les journaux d’audit dans votre solution d’audit globale. Étant donné que les appareils varient entre les différents fournisseurs de matériel OEM Azure Stack Hub, contactez votre fournisseur pour obtenir la documentation sur l’intégration d’audit. Les sections suivantes fournissent des informations générales sur l’audit d’appareil physique dans Azure Stack Hub.  

## <a name="physical-device-access-auditing"></a>Audit des accès aux appareils physiques

Tous les appareils physiques dans Azure Stack Hub prennent en charge l’utilisation de TACACS ou de RADIUS. La prise en charge inclut l’accès au contrôleur BMC (Baseboard Management Controller) et aux commutateurs réseau.

Les solutions Azure Stack Hub ne sont pas fournies avec RADIUS ou TACACS intégrés. Les solutions ont cependant été validées pour prendre en charge l’utilisation de solutions RADIUS ou TACACS existantes disponibles sur le marché.

Pour RADIUS seulement, MSCHAPv2 a été validé. Il représente l’implémentation la plus sécurisée avec RADIUS. Consultez votre fournisseur de matériel OEM pour activer TACAS ou RADIUS dans les appareils inclus avec votre solution Azure Stack Hub.

## <a name="syslog-forwarding-for-network-devices"></a>Transfert Syslog pour les appareils réseau

Tous les appareils réseau physiques dans Azure Stack Hub prennent en charge les messages Syslog. Les solutions Azure Stack Hub ne sont pas fournies avec un serveur Syslog. Les appareils ont cependant été validés pour prendre en charge l’envoi de messages à des solutions syslog existantes disponibles sur le marché.

L’adresse de destination syslog est un paramètre facultatif collecté pour le déploiement, mais il peut également être ajouté après le déploiement. Consultez votre fournisseur de matériel OEM pour configurer le transfert syslog sur vos appareils réseau.

## <a name="next-steps"></a>Étapes suivantes

[Stratégie de maintenance](azure-stack-servicing-policy.md)
