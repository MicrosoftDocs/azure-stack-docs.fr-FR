---
title: Prérequis pour l'installation d'IoT Hub sur Azure Stack Hub
description: Découvrez les prérequis nécessaires avant d'installer le fournisseur de ressources IoT Hub sur Azure Stack Hub.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: f194ef78a31722a05742b14b312ea4aad58d3ed3
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050277"
---
# <a name="prerequisites-for-installing-iot-hub-on-azure-stack-hub"></a>Prérequis pour l'installation d'IoT Hub sur Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

Les prérequis suivants doivent être remplis avant de pouvoir installer IoT Hub sur Azure Stack Hub. **Vous pouvez avoir besoin d’un délai de plusieurs jours ou semaines** pour effectuer toutes les étapes.

> [!IMPORTANT]
> Ces prérequis supposent que vous avez déjà déployé au moins un système intégré Azure Stack Hub à 4 nœuds, **numéro de build 1.2005.6.53** ou ultérieur. Le fournisseur de ressources IoT Hub n'est pas pris en charge sur le kit de développement Azure Stack Hub (ASDK).

## <a name="common-prerequisites"></a>Prérequis communs

[!INCLUDE [Common RP prerequisites](../includes/resource-provider-prerequisites.md)]

## <a name="dependency-prerequisites"></a>Prérequis en matière de dépendance

1. Téléchargez et [installez Event Hubs](event-hubs-rp-install.md) à partir de la Place de marché. Event Hubs doit être déployé AVANT de commencer à déployer IoT Hub.
2. Pour accélérer le téléchargement et l'installation d'IoT Hub, téléchargez les éléments dépendants suivants à partir de la Place de marché avant de télécharger le package IoT Hub. Sinon, le processus de déploiement d'IoT Hub essaiera de télécharger les packages dépendants :
    * Extension de script personnalisé
    * PowerShell Desired State Configuration
    * Licence gratuite : SQL Server 2016 SP2 Express sur Windows Server 2016
    * Extension IaaS SQL
    * Module complémentaire Azure Stack - Fournisseur de ressources - Windows Server
3. Une fois Event Hubs installé, attendez au moins 10 minutes avant de poursuivre le déploiement d'IoT Hub.

## <a name="certificate-requirements"></a>Conditions de certificat

1. Procurez-vous un certificat TLS/SSL d'infrastructure à clé publique (PKI) pour Event Hubs. L'autre nom de l'objet (SAN) doit respecter le modèle de dénomination suivant : `CN=*.mgmtiothub.<region>.<fqdn>`.

   Le nom de l'objet peut être spécifié, mais il n'est pas utilisé par IoT Hub lors de la gestion des certificats. Seul l'autre nom de l'objet est utilisé. Pour obtenir la liste complète des exigences détaillées, consultez [Exigences de certificat pour infrastructure à clé publique (PKI) Azure Stack Hub](azure-stack-pki-certs.md).

   ![Exemple de certificat IoT Hub](media\iot-hub-rp-prerequisites\certificate.png)

2. Veillez à passer en revue [Valider votre certificat](azure-stack-validate-pki-certs.md). L'article explique comment préparer et valider les certificats utilisés pour le fournisseur de ressources IoT Hub. 

## <a name="dns-configuration-requirements"></a>Configuration DNS requise
 
Pour qu'IoT Hub fonctionne correctement sur le réseau sous Azure Stack Hub, l'administrateur réseau doit configurer DNS. Recherchez le paramètre de transfert conditionnel DNS dans l'outil de gestion DNS, et ajoutez une règle de transfert conditionnel afin d'autoriser le trafic pour : `<region>.cloudapp.<externaldomainname>`. Par exemple, `ussouth.cloudapp.contoso.com`.

## <a name="next-steps"></a>Étapes suivantes

Installer le fournisseur de ressources IoT Hub sur une [instance connectée d'Azure Stack](iot-hub-rp-install.md).
