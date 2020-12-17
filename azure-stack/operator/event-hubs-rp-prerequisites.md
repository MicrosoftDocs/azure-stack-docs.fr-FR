---
title: Conditions préalables à l’installation d’Event Hubs sur Azure Stack Hub
description: Découvrez les prérequis nécessaires avant d’installer le fournisseur de ressources Event Hubs sur Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 2b3fcfb0325689de08e77a9fbf599d023ba26a4c
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97343511"
---
# <a name="prerequisites-for-installing-event-hubs-on-azure-stack-hub"></a>Prérequis pour l’installation d’Event Hubs sur Azure Stack Hub

Les prérequis suivants doivent être remplis avant de pouvoir installer Event Hubs sur Azure Stack Hub. **Vous pouvez avoir besoin d’un délai de plusieurs jours ou semaines** pour effectuer toutes les étapes.

> [!IMPORTANT]
> Ces prérequis supposent que vous avez déjà déployé au moins un système intégré Azure Stack Hub à 4 nœuds. Le fournisseur de ressources Event Hubs n’est pas pris en charge sur le kit de développement Azure Stack (ASDK).

> [!IMPORTANT]
> La version de build Azure Stack Hub 2002 ou une version ultérieure est exigée par Event Hubs. Notez que les builds Azure Stack Hub sont incrémentielles. Par exemple, si la version 1910 est installée, vous devez d’abord effectuer la mise à niveau vers [2002](./release-notes.md?view=azs-2002&preserve-view=true#2002-build-reference), puis vers 2005. Autrement dit, vous ne pouvez pas ignorer les builds intermédiaires.

## <a name="common-prerequisites"></a>Prérequis communs

[!INCLUDE [Common RP prerequisites](../includes/resource-provider-prerequisites.md)]

## <a name="event-hubs-prerequisites"></a>Prérequis Event Hubs

1. Procurez-vous des certificats SSL pour infrastructure à clé publique (PKI) pour Event Hubs. L'autre nom de l'objet (SAN) doit respecter le modèle de dénomination suivant : `CN=*.eventhub.<region>.<fqdn>`. Le nom de l’objet peut être spécifié, mais il n’est pas utilisé par Event Hubs lors de la gestion des certificats. Seul l'autre nom de l'objet est utilisé. Pour obtenir la liste complète des exigences détaillées, consultez [Exigences de certificat pour infrastructure à clé publique (PKI) Azure Stack Hub](azure-stack-pki-certs.md).  

   ![exemple de certificat](media/event-hubs-rp-prerequisites/certificate-example.png)

   > [!NOTE]
   > **Les fichiers PFX doivent être protégés par un mot de passe**. Le mot de passe sera demandé ultérieurement au cours de l’installation.

2. Veillez à passer en revue [Valider votre certificat](azure-stack-validate-pki-certs.md). L’article vous montre comment préparer et valider les certificats que vous utilisez pour le fournisseur de ressources Event Hubs. 

## <a name="next-steps"></a>Étapes suivantes

Ensuite, [installez le fournisseur de ressources Event Hubs](event-hubs-rp-install.md).
