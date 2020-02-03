---
title: Gérer les ressources réseau dans Azure Stack Hub
description: Les administrateurs peuvent gérer les ressources réseau, y compris le pool d'adresses MAC et la consommation d'adresses IP publiques dans une région
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: 949c00edfbe7e5daff8c1cd17c20681c3a7d9265
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76880211"
---
# <a name="manage-network-resources"></a>Gérer des ressources réseau

## <a name="mac-address-pool"></a>Pool d’adresses MAC

Azure Stack Hub utilise un pool d'adresses MAC statiques pour générer et attribuer automatiquement une adresse MAC aux machines virtuelles.
Ce pool d’adresses MAC est généré automatiquement pendant le déploiement et utilise la plage suivante :

- StartMacAddress : 00-1D-D8-B7-00-00
- EndMacAddress : 00-1D-D8-F4-FF-FF

> [!Note]  
> Ce pool d'adresses MAC est le même pour chaque système Azure Stack Hub et il n'est pas configurable.

Selon la façon dont les réseaux virtuels se connectent aux réseaux d’entreprise existants, vous pouvez vous attendre à obtenir des adresses MAC en double pour les machines virtuelles.

Pour plus d'informations sur l'utilisation du pool d'adresses MAC, utilisez la cmdlet [Get-AzsMacAddressPool](https://docs.microsoft.com/powershell/module/azs.fabric.admin/get-azsmacaddresspool) dans le module PowerShell Administrateur d'Azure Stack Hub.

## <a name="view-public-ip-address-consumption-in-azure-stack-hub"></a>Afficher la consommation d'adresses IP publiques dans Azure Stack Hub

En tant qu’administrateur, vous pouvez afficher les éléments suivants :
 - Le nombre d’adresses IP publiques qui ont été attribuées à des locataires.
 - Le nombre d’adresses IP publiques encore disponibles.
 - Le pourcentage d’adresses IP publiques qui ont été attribuées à cet emplacement.

La vignette **Utilisation des pools d’adresses IP publiques** indique le nombre d’adresses IP publiques consommées sur les pools d’adresses IP publiques. Pour chaque adresse IP, la vignette affiche l’utilisation du locataire des instances de machine virtuelle IaaS, des services d’infrastructure fabric et des ressources d’adresses IP publiques créées explicitement par les locataires.

L'objectif de la vignette est de donner aux opérateurs Azure Stack Hub une idée du nombre d'adresses IP publiques utilisées à cet emplacement. Ce nombre permet aux administrateurs de déterminer si cette ressource est insuffisante.

L’élément de menu **Adresses IP publiques** sous **Ressources de locataire** répertorie uniquement les adresses IP publiques qui ont été *explicitement créées par les locataires*. L’élément de menu se trouve sur le volet **Fournisseurs de ressources**, **Réseau**. Le nombre d’adresses IP publiques **Utilisées** dans la vignette **Utilisation des pools d’adresses IP publiques** est toujours différent (supérieur) par rapport au nombre de la vignette **Adresses IP publiques** sous **Ressources de locataire**.

### <a name="view-the-public-ip-address-usage-information"></a>Afficher les informations d’utilisation d’adresses IP publiques

Pour afficher le nombre total d’adresses IP publiques qui ont été consommées dans la région :

1. Sur le portail d'administration Azure Stack Hub, sélectionnez **Tous les services**. Ensuite, sous la catégorie **ADMINISTRATION**, sélectionnez **Réseau**.
1. Le volet **Réseau** affiche la vignette **Utilisation des pools d’adresses IP publiques** dans la section **Vue d’ensemble**.

    ![Volet Fournisseur de ressources réseau](media/azure-stack-viewing-public-ip-address-consumption/ip-address-consumption-01.png)

Le nombre**Utilisé** représente le nombre d’adresses IP publiques affectées à partir de pools d’adresses IP publiques. Le nombre sous **Libres** représente le nombre d’adresses IP publiques de pools d’adresses IP publiques qui n’ont pas été affectées et sont encore disponibles. Le nombre sous **% utilisé** représente le nombre d’adresses utilisées ou affectées en pourcentage du nombre d’adresses IP publiques dans tous les pools d’adresses IP publiques de cet emplacement.

### <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Afficher les adresses IP publiques créées par des abonnements de locataire

Sélectionnez **Adresses IP publiques** sous **Ressources de locataire**. Passez en revue la liste d’adresses IP publiques explicitement créées par des abonnements de locataire dans une région donnée.

![Adresses IP publiques de locataire](media/azure-stack-viewing-public-ip-address-consumption/ip-address-consumption-02.png)

Notez que certaines adresses IP publiques qui ont été allouées dynamiquement apparaissent dans la liste. Toutefois, elles n’ont pas encore d’adresse associée. La ressource d’adresse a été créée dans le Fournisseur de ressources réseau, mais pas encore dans le contrôleur de réseau.

Le contrôleur de réseau n’affecte pas d’adresse à la ressource tant qu’elle n’est pas liée à une interface, une carte d’interface réseau, un équilibreur de charge ou une passerelle de réseau virtuel. Quand l’adresse IP publique est liée à une interface, le contrôleur de réseau lui alloue une adresse IP. L’adresse s’affiche dans le champ **Adresse**.

### <a name="view-the-public-ip-address-information-summary-table"></a>Afficher le tableau récapitulatif des informations d’adresses IP publiques

Quand une adresse IP publique est affectée, elle apparaît dans une liste ou dans l’autre selon les cas.

| **Cas d’affectation d’adresses IP publiques** | **Apparaît dans le récapitulatif d’utilisation** | **Apparaît dans la liste d’adresses IP publiques du locataire** |
| --- | --- | --- |
| Adresse IP publique dynamique non encore affectée à une carte réseau ou un équilibreur de charge (temporaire) |Non |Oui |
| Adresse IP publique dynamique affectée à une carte réseau ou un équilibreur de charge. |Oui |Oui |
| Adresse IP publique statique affectée à une carte réseau ou un équilibreur de charge du locataire. |Oui |Oui |
| Adresse IP publique statique affectée à un point de terminaison de service d’infrastructure fabric. |Oui |Non |
| Adresse IP publique implicitement créée pour des instances de machine virtuelle IaaS et utilisée pour les règles NAT de trafic sortant sur le réseau virtuel. Ces adresses IP sont créées en arrière-plan chaque fois qu’un locataire crée une instance de machine virtuelle pour que les machines virtuelles puissent envoyer des informations sur Internet. |Oui |Non |

## <a name="next-steps"></a>Étapes suivantes

[Gérer les comptes de stockage dans Azure Stack Hub](azure-stack-manage-storage-accounts.md)
