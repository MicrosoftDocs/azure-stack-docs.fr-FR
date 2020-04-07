---
title: Guide pratique pour supprimer Event Hubs sur Azure Stack Hub
description: Découvrez comment supprimer le fournisseur de ressources Event Hubs sur Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 2a1525f4b91998479d368714aa3a88df6ecfcef9
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80424595"
---
# <a name="how-to-remove-event-hubs-on-azure-stack-hub"></a>Guide pratique pour supprimer Event Hubs sur Azure Stack Hub

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

> [!WARNING]
> La désinstallation d’Event Hubs supprime (efface) le fournisseur de ressources ainsi que tous les clusters, espaces de noms et ressources Event Hubs créés par l’utilisateur. Elle supprime également les données d’événement qui leur sont associées.  
> Observez la plus grande prudence avant de décider de désinstaller Event Hubs. La désinstallation d’Event Hubs ne supprime **pas** les packages utilisés pour installer Event Hubs. Pour ce faire, consultez [Supprimer les packages Event Hubs](#delete-event-hubs-packages).

## <a name="uninstall-event-hubs"></a>Désinstaller Event Hubs

Cette séquence d’étapes permet de supprimer toutes les ressources Event Hubs, dont les clusters, les espaces de noms, les hubs d’événements et le fournisseur de ressources.

Pour supprimer Event Hubs et toutes les ressources associées créées par les utilisateurs, procédez comme suit :

1. Connectez-vous au portail d’administration Azure Stack Hub.
2. Sélectionnez **Gestion de la Place de marché** sur la gauche.
3. Sélectionnez **Fournisseurs de ressources**.
4. Sélectionnez **Event Hubs** dans la liste des fournisseurs de ressources. Vous pouvez filtrer la liste en entrant « Event Hubs » dans la zone de texte de recherche fournie.

   [![Supprimer Event Hubs 1](media/event-hubs-rp-remove/1-uninstall.png)](media/event-hubs-rp-remove/1-uninstall.png#lightbox)

5. Sélectionnez **Désinstaller** parmi les options disponibles en haut de la page.

   [![Supprimer Event Hubs 2](media/event-hubs-rp-remove/2-uninstall.png)](media/event-hubs-rp-remove/2-uninstall.png#lightbox)

6. Entrez le nom du fournisseur de ressources, puis sélectionnez **Désinstaller**. Cette action confirme votre souhait de désinstaller :
   - Le fournisseur de ressources Event Hubs
   - Tous les clusters, espaces de noms, hubs d’événements et données d’événement créés par l’utilisateur.

   [![Supprimer Event Hubs 3](media/event-hubs-rp-remove/3-uninstall.png)](media/event-hubs-rp-remove/3-uninstall.png#lightbox)

   [![Supprimer Event Hubs 4](media/event-hubs-rp-remove/4-uninstall.png)](media/event-hubs-rp-remove/4-uninstall.png#lightbox)

   > [!IMPORTANT]
   > Vous devez patienter au moins 10 minutes après la suppression d’Event Hubs avant de le réinstaller. Cela vient du fait que des activités de nettoyage peuvent toujours être en cours d’exécution et entrer en conflit avec toute nouvelle installation.

## <a name="delete-event-hubs-packages"></a>Supprimer les packages Event Hubs

Utilisez cette option si, après la désinstallation d’Event Hubs, vous souhaitez également supprimer tous les packages utilisés pour installer Event Hubs. 

## <a name="next-steps"></a>Étapes suivantes

Pour réinstaller, revenez à l’article [Installer le fournisseur de ressources Event Hubs](event-hubs-rp-install.md).