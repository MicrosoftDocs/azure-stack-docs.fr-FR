---
title: Mises à jour de sécurité Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Découvrez la sécurité et les contrôles appliqués à Azure Stack Hub.
author: JustinHall
ms.topic: article
ms.date: 06/10/2019
ms.author: justinha
ms.reviewer: fiseraci
ms.lastreviewed: 04/07/2020
ms.openlocfilehash: 35b4fbd97032df00236a67dd5b776a2f3fada8ea
ms.sourcegitcommit: 5f4f0ee043ff994efaad44129ce49be43c64d5dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2020
ms.locfileid: "84819248"
---
# <a name="azure-stack-hub-infrastructure-security-controls"></a>Contrôles de sécurité de l’infrastructure Azure Stack Hub

L’utilisation de clouds hybrides doit essentiellement répondre à des considérations sur la sécurité et à des règles de conformité. Azure Stack Hub est conçu pour ces scénarios. Cet article explique les contrôles de sécurité en place pour Azure Stack Hub.

Deux couches de situation de sécurité coexistent au sein d’Azure Stack Hub. La première couche est l’infrastructure Azure Stack Hub qui inclut les composants matériels jusqu’à Azure Resource Manager. La première couche comprend les portails administrateur et utilisateur. La seconde couche se compose des charges de travail créées, déployées et gérées par les locataires. La seconde couche inclut des éléments tels que les machines virtuelles et les sites web de App Services.

## <a name="security-approach"></a>Approche de la sécurité

La sécurité d’Azure Stack Hub est conçue pour assurer la protection contre des menaces modernes et pour répondre aux exigences des principales normes de conformité. Ainsi, la sécurité de l’infrastructure Azure Stack Hub repose sur deux piliers :

- **Envisager les failles**  
    En partant de l’hypothèse que le système a déjà été enfreint, concentrez-vous sur *la détection des violations et sur la limitation de leur impact*, au lieu d’essayer uniquement d’empêcher les attaques.

- **Renforcement par défaut**  
    Étant donné que l’infrastructure s’exécute sur des composants matériels et logiciels bien définis, par défaut, Azure Stack Hub *active, configure et valide toutes les fonctionnalités de sécurité*.

Azure Stack Hub étant fourni en tant que système intégré, la sécurité de l’infrastructure Azure Stack Hub est définie par Microsoft. Tout comme dans Azure, il appartient aux locataires de définir la situation de sécurité de leurs charges de travail de locataire. Ce document fournit des connaissances fondamentales sur la sécurité de l’infrastructure Azure Stack Hub.

## <a name="data-at-rest-encryption"></a>Chiffrement des données au repos

Toutes les données concernant l’infrastructure et le locataire Azure Stack Hub sont chiffrées au repos à l’aide de BitLocker. Ce chiffrement protège contre la perte physique ou le vol de composants de stockage Azure Stack Hub. Pour plus d’informations, voir [Chiffrement des données au repos dans Azure Stack Hub](azure-stack-security-bitlocker.md).

## <a name="data-in-transit-encryption"></a>Chiffrement des données en transit

Les composants de l’infrastructure Azure Stack Hub communiquent à l’aide de canaux chiffrés avec le protocole TLS 1.2. L’infrastructure gère automatiquement les certificats de chiffrement.

Tous les points de terminaison externes de l’infrastructure, tels que les points de terminaison REST ou le portail Azure Stack Hub, prennent en charge le protocole TLS 1.2 pour sécuriser les communications. Des certificats de chiffrement, issus d’un tiers ou de l’autorité de certification de votre entreprise, doivent être fournis pour ces points de terminaison.

Même si vous pouvez utiliser des certificats auto-signés pour ces points de terminaison externes, Microsoft vous le déconseille fortement.
Pour plus d’informations sur la façon d’appliquer le protocole TLS 1.2 aux points de terminaison externes d’Azure Stack Hub, voir [Configurer les contrôles de sécurité d’Azure Stack Hub](azure-stack-security-configuration.md).

## <a name="secret-management"></a>Gestion des secrets

L’infrastructure Azure Stack Hub utilise une multitude de secrets, tels que des mots de passe et des certificats, pour fonctionner. La plupart des mots de passe associés aux comptes de service internes sont automatiquement permutés toutes les 24 heures, car ce sont des [comptes de service administrés de groupe (gMSA)](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview), un type de compte de domaine géré directement par le contrôleur de domaine interne.

L’infrastructure Azure Stack Hub utilise des clés RSA 4 096 bits pour tous ses certificats internes. Vous pouvez utiliser les mêmes certificats de longueur de clé pour les points de terminaison externes. Pour plus d’informations sur la rotation des secrets et des certificats, voir [Effectuer une rotation des secrets dans Azure Stack Hub](azure-stack-rotate-secrets.md).

## <a name="windows-defender-application-control"></a>Windows Defender Application Control

Azure Stack Hub se sert des dernières fonctionnalités de sécurité de Windows Server. L’une d’elles se nomme WDAC (Windows Defender Application Control, anciennement appelée Code Integrity). Elle fournit une liste verte des exécutables et garantit que seul du code autorisé peut s’exécuter dans l’infrastructure Azure Stack Hub.

Le code autorisé est signé par Microsoft ou le partenaire OEM. Le code autorisé signé est inclus dans la liste des logiciels autorisés, spécifiée dans une stratégie définie par Microsoft. En d’autres termes, seuls les logiciels approuvés pour s’exécuter dans l’infrastructure Azure Stack Hub peuvent être exécutés. Toute tentative d’exécution de code non autorisé est bloquée, et une alerte est générée. Azure Stack Hub applique à la fois l’intégrité du code du mode utilisateur (UMCI) et l’intégrité du code de l’hyperviseur (HVCI).

La stratégie WDAC empêche également les agents ou logiciels tiers de s’exécuter dans l’infrastructure Azure Stack Hub.
Pour plus d’informations sur WDAC, consultez [Windows Defender Application Control et protection de l’intégrité du code basée sur la virtualisation](https://docs.microsoft.com/windows/security/threat-protection/device-guard/introduction-to-device-guard-virtualization-based-security-and-windows-defender-application-control).

## <a name="credential-guard"></a>Credential Guard

Une autre fonctionnalité de sécurité de Windows Server dans Azure Stack Hub, à savoir Windows Defender Credential Guard, permet de protéger les informations d’identification de l’infrastructure Azure Stack Hub contre des attaques Pass-the-Hash et Pass-the-Ticket.

## <a name="antimalware"></a>Logiciel anti-programme malveillant

Tous les composants Azure Stack Hub (hôtes et machines virtuelles Hyper-V) sont protégés par l’antivirus Windows Defender.

Dans les scénarios connectés, les mises à jour du moteur et des définitions de l’antivirus sont appliquées plusieurs fois par jour. Dans des scénarios déconnectés, les mises à jour du logiciel anti-programme malveillant sont appliquées dans le cadre des mises à jour mensuelles d’Azure Stack Hub. Si une mise à jour plus fréquente des définitions Windows Defender est nécessaire dans les scénarios déconnectés, Azure Stack Hub prend également en charge l’importation des mises à jour Windows Defender. Pour plus d’informations, voir [Mettre à jour l’antivirus Windows Defender sur Azure Stack Hub](azure-stack-security-av.md).

## <a name="secure-boot"></a>Démarrage sécurisé

Azure Stack Hub applique le démarrage sécurisé sur tous les hôtes Hyper-V et toutes les machines virtuelles de l’infrastructure. 

## <a name="constrained-administration-model"></a>Modèle d’administration avec contraintes

Dans Azure Stack Hub, l’administration passe par trois points d’entrée ayant chacun une finalité spécifique :

- Le [portail administrateur](azure-stack-manage-portals.md) permet d’effectuer les opérations de gestion quotidiennes à l’aide d’une expérience de type « pointer-cliquer ».
- Azure Resource Manager expose toutes les opérations de gestion du portail administrateur par le biais d’une API REST, utilisée par PowerShell et Azure CLI.
- Pour certaines opérations de bas niveau (par exemple, des scénarios d’intégration ou de prise en charge de centre de données), Azure Stack Hub expose un point de terminaison PowerShell appelé [point de terminaison privilégié](azure-stack-privileged-endpoint.md). Ce point de terminaison expose uniquement un jeu d’applets de commande approuvé et fait l’objet d’un audit approfondi.

## <a name="network-controls"></a>Contrôles de réseau

L’infrastructure Azure Stack Hub intègre plusieurs couches de listes de contrôle d’accès (ACL) réseau. Les ACL empêchent tout accès non autorisé aux composants de l’infrastructure et limitent les communications de l’infrastructure aux chemins qui sont nécessaires à son fonctionnement.

Les ACL réseau sont appliquées dans trois couches :

- Couche 1 : Commutateurs TOR (Top-of-rack)
- Couche 2 : SDN (Software Defined Network)
- Couche 3 : Pare-feu de système d’exploitation d’hôte et de machine virtuelle

## <a name="regulatory-compliance"></a>Conformité aux normes

Azure Stack Hub a fait l’objet d’une évaluation de fonctionnalité formelle par un cabinet d’audit tiers indépendant. Par conséquent, une documentation concernant la façon dont l’infrastructure Azure Stack Hub satisfait aux contrôles applicables de diverses normes de conformité majeures est disponible. La documentation n’équivaut pas à une certification d’Azure Stack Hub, car les normes incluent plusieurs contrôles en lien avec le personnel et les processus. Au lieu de cela, les clients peuvent utiliser cette documentation pour lancer leurs processus de certification.

Les évaluations incluent les normes suivantes :

- La norme [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/) a trait au secteur des cartes de paiement.
- La [Matrice de contrôle cloud CSA](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview) est un mappage complet de plusieurs normes, dont FedRAMP Moderate, ISO27001, HIPAA, HITRUST, ITAR, NIST SP800-53.
- [FedRAMP High](https://www.fedramp.gov/fedramp-releases-high-baseline/) pour les clients du secteur public.

Vous trouverez la documentation relative à la conformité sur le [portail Microsoft Service Trust](https://aka.ms/azurestackcompliance). Les guides de conformité constituent une ressource protégée et nécessitent que vous vous connectiez avec vos informations d’identification du service cloud Azure.

## <a name="next-steps"></a>Étapes suivantes

- [Configurer les contrôles de sécurité d’Azure Stack Hub](azure-stack-security-configuration.md)
- [Apprendre à effectuer la rotation de vos secrets dans Azure Stack Hub](azure-stack-rotate-secrets.md)
- [Documents PCI-DSS et CSA-CCM pour Azure Stack Hub](https://aka.ms/azurestackcompliance)
