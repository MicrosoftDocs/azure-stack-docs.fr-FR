---
title: Gérer des ressources réseau
titleSuffix: Azure Stack Hub
description: Découvrez comment gérer les ressources réseau, y compris le pool d’adresses MAC et la consommation d’adresses IP publiques dans une région.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: inhenkel
ms.reviewer: scottnap
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: b96485e74286cbbbd2ff20f2378dc7c452849b52
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487394"
---
# <a name="manage-network-resources-in-azure-stack-hub"></a>Gérer les ressources réseau dans Azure Stack Hub

## <a name="mac-address-pool"></a>Pool d’adresses MAC

Azure Stack Hub utilise un pool d’adresses MAC statiques pour générer et attribuer automatiquement une adresse MAC aux machines virtuelles. Ce pool d’adresses MAC est généré automatiquement pendant le déploiement et utilise la plage suivante :

- StartMacAddress : 00-1D-D8-B7-00-00
- EndMacAddress : 00-1D-D8-F4-FF-FF

> [!Note]  
> Ce pool d'adresses MAC est le même pour chaque système Azure Stack Hub et il n'est pas configurable.

Selon la façon dont les réseaux virtuels se connectent aux réseaux d’entreprise existants, vous pouvez vous attendre à obtenir des adresses MAC en double pour les machines virtuelles.

Pour plus d’informations sur l’utilisation du pool d’adresses MAC, utilisez l’applet de commande [Get-AzsMacAddressPool](/powershell/module/azs.fabric.admin/get-azsmacaddresspool) dans le module PowerShell d’administrateur Azure Stack Hub.

## <a name="view-public-ip-address-consumption-in-azure-stack-hub"></a>Afficher la consommation d'adresses IP publiques dans Azure Stack Hub

En tant qu’administrateur, vous pouvez afficher les éléments suivants :
 - Le nombre d’adresses IP publiques qui ont été attribuées à des locataires.
 - Le nombre d’adresses IP publiques encore disponibles.
 - Le pourcentage d’adresses IP publiques qui ont été attribuées à cet emplacement.

La vignette **Utilisation des pools d’adresses IP publiques** indique le nombre d’adresses IP publiques consommées sur les pools d’adresses IP publiques. Pour chaque adresse IP, la vignette affiche l’utilisation du locataire des instances de machine virtuelle IaaS, des services d’infrastructure fabric et des ressources d’adresses IP publiques créées explicitement par les locataires.

L'objectif de la vignette est de donner aux opérateurs Azure Stack Hub une idée du nombre d'adresses IP publiques utilisées à cet emplacement. Ce nombre permet aux administrateurs de déterminer si cette ressource est insuffisante.

L’élément de menu **Adresses IP publiques** sous **Ressources de locataire** répertorie uniquement les adresses IP publiques qui ont été *explicitement créées par les locataires*. L’élément de menu se trouve dans le volet **Fournisseurs de ressources** -> **Réseau**. Le nombre d’adresses IP publiques **Utilisées** dans la vignette **Utilisation des pools d’adresses IP publiques** est toujours différent (supérieur) par rapport au nombre de la vignette **Adresses IP publiques** sous **Ressources de locataire**.

### <a name="view-the-public-ip-address-usage-information"></a>Afficher les informations d’utilisation d’adresses IP publiques

Pour afficher le nombre total d’adresses IP publiques qui ont été consommées dans la région :

1. Sur le portail d'administration Azure Stack Hub, sélectionnez **Tous les services**. Ensuite, sous la catégorie **ADMINISTRATION**, sélectionnez **Réseau**.
1. Le volet **Réseau** affiche la vignette **Utilisation des pools d’adresses IP publiques** dans la section **Vue d’ensemble**.

    ![Volet Fournisseur de ressources réseau dans le portail d’administration Azure Stack Hub](media/azure-stack-viewing-public-ip-address-consumption/ip-address-consumption-01.png)

Le nombre**Utilisé** représente le nombre d’adresses IP publiques affectées à partir de pools d’adresses IP publiques. Le nombre sous **Libres** représente le nombre d’adresses IP publiques à partir de pools d’adresses IP publiques qui n’ont pas été affectées et sont encore disponibles. Le nombre sous **% utilisé** représente le nombre d’adresses utilisées ou affectées en pourcentage du nombre d’adresses IP publiques dans tous les pools d’adresses IP publiques de cet emplacement.

### <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Afficher les adresses IP publiques créées par des abonnements de locataire

Sélectionnez **Adresses IP publiques** sous **Ressources de locataire**. Passez en revue la liste d’adresses IP publiques explicitement créées par des abonnements de locataire dans une région donnée.

![Adresses IP publiques de locataire dans le portail d’administration Azure Stack Hub](media/azure-stack-viewing-public-ip-address-consumption/ip-address-consumption-02.png)

Notez que certaines adresses IP publiques qui ont été allouées dynamiquement apparaissent dans la liste. Toutefois, elles n’ont pas encore d’adresse associée. La ressource d’adresse a été créée dans le Fournisseur de ressources réseau, mais pas encore dans le contrôleur de réseau.

Le contrôleur de réseau n’affecte pas d’adresse à la ressource tant qu’elle n’est pas liée à une interface, une carte d’interface réseau, un équilibreur de charge ou une passerelle de réseau virtuel. Quand l’adresse IP publique est liée à une interface, le contrôleur de réseau lui alloue une adresse IP. L’adresse s’affiche dans le champ **Adresse**.

### <a name="view-the-public-ip-address-information-summary-table"></a>Afficher le tableau récapitulatif des informations d’adresses IP publiques

Quand une adresse IP publique est affectée, elle apparaît dans une liste ou dans l’autre selon les cas.

| **Cas d’affectation d’adresses IP publiques** | **Apparaît dans le récapitulatif d’utilisation** | **Apparaît dans la liste d’adresses IP publiques du locataire** |
| --- | --- | --- |
| Adresse IP publique dynamique non encore affectée à une carte réseau ou un équilibreur de charge (temporaire). |Non |Oui |
| Adresse IP publique dynamique affectée à une carte réseau ou un équilibreur de charge. |Oui |Oui |
| Adresse IP publique statique affectée à une carte réseau ou un équilibreur de charge du locataire. |Oui |Oui |
| Adresse IP publique statique affectée à un point de terminaison de service d’infrastructure fabric. |Oui |Non |
| Adresse IP publique implicitement créée pour des instances de machine virtuelle IaaS et utilisée pour les règles NAT de trafic sortant sur le réseau virtuel. Ces adresses IP sont créées en arrière-plan chaque fois qu’un locataire crée une instance de machine virtuelle pour que les machines virtuelles puissent envoyer des informations sur Internet. |Oui |Non |

## <a name="next-steps"></a>Étapes suivantes

[Gérer les comptes de stockage dans Azure Stack Hub](azure-stack-manage-storage-accounts.md)
