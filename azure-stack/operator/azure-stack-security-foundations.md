---
title: Comprendre les contrôles de sécurité Azure Stack
description: En tant qu’administrateur de service, découvrez les contrôles de sécurité appliqués à Azure Stack.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/14/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: efa97c18a63954239475338c85f2145b8c6c6ac6
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308009"
---
# <a name="azure-stack-infrastructure-security-posture"></a>Situation de sécurité de l’infrastructure Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

L’utilisation de clouds hybrides doit essentiellement répondre à des considérations sur la sécurité et à des règles de conformité. Azure Stack est conçu pour ces scénarios. Cet article explique les contrôles de sécurité en place pour Azure Stack.

Deux couches de situation de sécurité coexistent au sein de Azure Stack. La première couche est l’infrastructure Azure Stack, qui inclut les composants matériels jusqu'à Azure Resource Manager. La première couche comprend l’administrateur et les portails de locataire. La seconde couche se compose des charges de travail créées, déployées et gérées par les locataires. La seconde couche inclut des éléments tels que les machines virtuelles et les sites web de App Services.

## <a name="security-approach"></a>Approche de la sécurité

La situation de sécurité pour Azure Stack est conçue pour assurer la protection face aux dernières menaces et répondre aux exigences des principaux standards de conformité. Ainsi, la situation de sécurité de l’infrastructure Azure Stack repose sur deux piliers :

 - **Envisager les failles**  
En partant de l’hypothèse que le système a déjà été enfreint, concentrez-vous sur *la détection des violations et sur la limitation de leur impact*, au lieu d’essayer uniquement d’empêcher les attaques. 
 - **Renforcement par défaut**  
Étant donné que l’infrastructure s’exécute sur des composants matériels et logiciels bien définis, Azure Stack *active, configure et valide toutes les fonctionnalités de sécurité* par défaut.

Étant donné qu’Azure Stack est fourni sous la forme d’un système intégré, la situation de sécurité de l’infrastructure Azure Stack est définie par Microsoft. Tout comme dans Azure, il appartient aux locataires de définir la situation de sécurité de leurs charges de travail de locataire. Ce document fournit des connaissances fondamentales sur la situation de sécurité de l’infrastructure Azure Stack.

## <a name="data-at-rest-encryption"></a>Chiffrement des données au repos
Toutes les données de l'infrastructure Azure Stack et des locataires sont chiffrées au repos à l'aide de BitLocker. Ce chiffrement protège contre la perte physique ou le vol de composants de stockage d’Azure Stack. Pour plus d’informations, consultez le [chiffrement des données au repos dans Azure Stack](azure-stack-security-bitlocker.md).

## <a name="data-in-transit-encryption"></a>Chiffrement des données en transit
Les composants de l’infrastructure Azure Stack communiquent à l’aide de canaux chiffrés avec TLS 1.2. L’infrastructure gère automatiquement les certificats de chiffrement. 

Tous les points de terminaison externes de l’infrastructure, tels que les points de terminaison REST ou le portail Azure Stack, prennent en charge TLS 1.2 pour sécuriser les communications. Des certificats de chiffrement, issus d’un tiers ou de l’autorité de certification de votre entreprise, doivent être fournis pour ces points de terminaison. 

Même si vous pouvez utiliser des certificats auto-signés pour ces points de terminaison externes, Microsoft vous le déconseille fortement. 

## <a name="secret-management"></a>Gestion des secrets
L’infrastructure Azure Stack utilise une multitude de secrets, tels que des mots de passe, pour fonctionner. La plupart d’entre eux sont souvent automatiquement permutés, car ce sont des comptes de service géré de groupe, qui permutent toutes les 24 heures.

Les autres secrets qui ne sont pas des comptes de service géré de groupe peuvent être permutés manuellement avec un script dans le point de terminaison privilégié.

## <a name="code-integrity"></a>Intégrité du code
Azure Stack se sert des fonctionnalités de sécurité de Windows Server 2016 les plus récentes. L’une d’elles est Windows Defender Device Guard, qui prend en charge l’approbation des applications et garantit que seul du code autorisé peut s’exécuter dans l’infrastructure Azure Stack. 

Le code autorisé est signé par Microsoft ou le partenaire OEM. Le code autorisé signé est inclus dans la liste des logiciels autorisés, spécifiée dans une stratégie définie par Microsoft. En d’autres termes, seuls les logiciels qui ont été approuvés pour s’exécuter dans l’infrastructure Azure Stack peuvent être exécutés. Toute tentative d’exécution d’un code non autorisé est bloquée et donne lieu à un audit.

De plus, la stratégie Device Guard empêche les agents tiers ou les logiciels de s’exécuter dans l’infrastructure Azure Stack.

## <a name="credential-guard"></a>Credential Guard
Une autre fonctionnalité de sécurité de Windows Server 2016 dans Azure Stack est Windows Defender Credential Guard, qui est utilisé pour protéger les informations d’identification de l’infrastructure Azure Stack contre les attaques pass-the-hash et pass-the-ticket.

## <a name="antimalware"></a>Logiciel anti-programme malveillant
Tous les composants Azure Stack (les hôtes Hyper-V et Machines Virtuelles) sont protégés par l’antivirus Windows Defender.

Dans les scénarios connectés, les mises à jour du moteur et des définitions de l’antivirus sont appliquées plusieurs fois par jour. Dans les scénarios déconnectés, les mises à jour du logiciel anti-programme malveillant sont appliquées dans le cadre des mises à jour mensuelles de Azure Stack. Pour plus d’informations, voir [Mettre à jour l’antivirus Windows Defender sur Azure Stack](azure-stack-security-av.md).

## <a name="constrained-administration-model"></a>Modèle d’administration avec contraintes
Dans Azure Stack, l'administration passe par trois points d'entrée, chacun ayant un objectif spécifique : 
1. Le [portail administrateur](azure-stack-manage-portals.md) permet d’effectuer les opérations de gestion quotidiennes de façon conviviale.
2. Azure Resource Manager expose toutes les opérations de gestion du portail administrateur par le biais d’une API REST, utilisée par PowerShell et Azure CLI. 
3. Pour les opérations de bas niveau spécifiques, par exemple les scénarios d’intégration ou de prise en charge de centre de données, Azure Stack expose un point de terminaison PowerShell appelé [Privileged Endpoint](azure-stack-privileged-endpoint.md) (Point de terminaison privilégié). Ce point de terminaison expose uniquement un jeu d’applets de commande approuvé et fait l’objet d’un audit approfondi.

## <a name="network-controls"></a>Contrôles de réseau
L’infrastructure Azure Stack est fournie avec plusieurs couches d’ACL réseau. Les ACL empêchent tout accès non autorisé aux composants de l’infrastructure et limitent les communications de l’infrastructure aux chemins qui sont nécessaires à son fonctionnement. 

Les ACL réseau sont appliquées dans trois couches :
1.  Commutateurs TOR (Top-of-rack)
2.  SDN (Software Defined Network)
3.  Pare-feu de système d’exploitation d’hôte et de machine virtuelle

## <a name="regulatory-compliance"></a>Conformité aux normes

Azure Stack a fait l’objet d’une évaluation formelle par un cabinet d’audit indépendant. Par conséquent, la documentation concernant la façon dont l’infrastructure Azure Stack satisfait aux contrôles applicables de diverses normes de conformité de premier plan est disponible. La documentation n’est pas une certification d’Azure Stack en raison des normes, notamment plusieurs contrôles en lien avec le personnel et les processus. Au lieu de cela, les clients peuvent utiliser cette documentation pour lancer leurs processus de certification.

Les évaluations incluent les normes suivantes :

- La norme [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/) a trait au secteur des cartes de paiement.
- La [Matrice de contrôle cloud CSA](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview) est un mappage complet de plusieurs normes, dont FedRAMP Moderate, ISO27001, HIPAA, HITRUST, ITAR, NIST SP800-53.
- [FedRAMP High](https://www.fedramp.gov/fedramp-releases-high-baseline/) pour les clients du secteur public.

Vous trouverez la documentation relative à la conformité sur le [portail Microsoft Service Trust](https://servicetrust.microsoft.com/ViewPage/Blueprint). Les guides de conformité constituent une ressource protégée et nécessitent que vous vous connectiez avec vos informations d’identification du service cloud Azure.

## <a name="next-steps"></a>Étapes suivantes

- [Apprendre à faire pivoter vos clés secrètes dans Azure Stack](azure-stack-rotate-secrets.md)
- [Norme PCI-DSS et documents CSA-CCM pour Azure Stack](https://servicetrust.microsoft.com/ViewPage/TrustDocuments)
- [Documents DoD et NIST pour Azure Stack](https://servicetrust.microsoft.com/ViewPage/Blueprint)
