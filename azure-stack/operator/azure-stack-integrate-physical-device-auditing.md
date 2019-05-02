---
title: Audit d’appareil physique Azure Stack
description: Découvrez comment intégrer l’audit des accès aux appareils physiques dans Azure Stack
services: azure-stack
author: WenJason
manager: digimobile
ms.service: azure-stack
ms.topic: article
origin.date: 02/11/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.reviewer: thoroet
ms.lastreviewed: 02/11/2019
keywords: ''
ms.openlocfilehash: 7e39370879884dc8900671d174fc6e0708907d83
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307109"
---
# <a name="azure-stack-datacenter-integration---physical-device-auditing"></a>Intégration de centre de données Azure Stack - audit d’appareil physique

Tous les appareils physiques dans Azure Stack, tels que les contrôleurs de gestion de carte de base (BMC) et les commutateurs réseau, émettent des journaux d’audit. Vous pouvez intégrer les journaux d’audit dans votre solution d’audit globale. Étant donné que les appareils varient entre les différents fournisseurs de matériel OEM Azure Stack, contactez votre fournisseur pour obtenir la documentation sur l’intégration d’audit.
Les sections suivantes fournissent des informations générales sur l’audit d’appareil physique dans Azure Stack.  

## <a name="physical-device-access-auditing"></a>Audit des accès aux appareils physiques

Tous les appareils physiques dans Azure Stack prennent en charge l’utilisation de TACACS ou de RADIUS. La prise en charge inclut l’accès au contrôleur BMC (Baseboard Management Controller) et aux commutateurs réseau.

Les solutions Azure Stack ne sont pas fournies avec RADIUS ou TACACS intégrés. Les solutions ont cependant été validées pour prendre en charge l’utilisation de solutions RADIUS ou TACACS existantes disponibles sur le marché.

Pour RADIUS seulement, MSCHAPv2 a été validé. Il représente l’implémentation la plus sécurisée avec RADIUS.
Consultez votre fournisseur de matériel OEM pour activer TACAS ou RADIUS dans les appareils inclus avec votre solution Azure Stack.

## <a name="syslog-forwarding-for-network-devices"></a>Transfert Syslog pour les appareils réseau

Tous les appareils réseau physiques dans Azure Stack prennent en charge les messages Syslog. Les solutions Azure Stack ne sont pas fournies avec un serveur syslog. Les appareils ont cependant été validés pour prendre en charge l’envoi de messages à des solutions syslog existantes disponibles sur le marché.

L’adresse de destination syslog est un paramètre facultatif collecté pour le déploiement, mais il peut également être ajouté après le déploiement. Consultez votre fournisseur de matériel OEM pour configurer le transfert syslog sur vos appareils réseau.

## <a name="next-steps"></a>Étapes suivantes

[Stratégie de maintenance](azure-stack-servicing-policy.md)
