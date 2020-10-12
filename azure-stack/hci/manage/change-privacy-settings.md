---
title: Changer les paramètres de confidentialité
description: Cette rubrique fournit des conseils sur la façon de changer les paramètres de confidentialité dans le système d’exploitation Azure Stack HCI ou Windows Server.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 10/05/2020
ms.openlocfilehash: 8055e02b702fb5e585b5570171c6dabb247b623f
ms.sourcegitcommit: 79be77eb2bebf314d956258fc9bc14b2014be190
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91745162"
---
# <a name="change-privacy-settings-on-individual-servers"></a>Changer les paramètres de confidentialité sur des serveurs individuels

>S’applique à : Azure Stack HCI version 20H2, Windows Server 2019, Windows Server 2016

Voici comment changer les paramètres de confidentialité sur un serveur exécutant le système d’exploitation Azure Stack HCI ou Windows Server. Pour gérer les paramètres de confidentialité avec la stratégie de groupe sur plusieurs serveurs à la fois ou dans l’ensemble de votre entreprise, consultez [Gérer des données de diagnostic d’entreprise](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#manage-enterprise-diagnostic-data).

## <a name="azure-stack-hci-or-server-core"></a>Azure Stack HCI ou Server Core
Si le serveur utilise le système d’exploitation Azure Stack HCI ou Windows Server avec l’option d’installation Server Core, effectuez les étapes suivantes :
1. Connectez-vous à un serveur dans le cluster Azure Stack HCI en utilisant le Bureau à distance, un contrôleur de gestion à distance (avec administration à distance ou BMC), un commutateur KVM ou en vous connectant localement à l’aide d’un clavier et d’un écran. 
1. Si vous vous connectez à un serveur qui exécute Azure Stack HCI, l’outil de configuration du serveur (Sconfig) s’ouvre automatiquement. Si vous vous connectez à un serveur exécutant Windows Server avec Server Core, à l’invite de commandes, entrez `Sconfig`.
1. À l’invite **Enter a number to select an option:** (Entrez un nombre pour sélectionner une option :), tapez **10**, puis appuyez sur Entrée.
1. À l’invite de confirmation, **Change Telemetry** (Changer les paramètres de télémétrie), sélectionnez **Yes** (Oui) pour afficher les options suivantes :

    Paramètres de télémétrie disponibles : **1 Security** (Sécurité), **2 Basic** (De base), **3 Enhanced** (Amélioré), **4 Full** (Complet)

    >[!NOTE]
    > Le paramètre par défaut pour Azure Stack HCI est **1 Security** (Sécurité).

1. À l’invite **Enter new telemetry setting:** (Entrer le nouveau paramètre de télémétrie :), tapez l’option de votre choix, puis appuyez sur Entrée.

## <a name="full-desktop"></a>Bureau complet
Si le serveur exécute Windows Server et qu’il dispose de l’option d’installation Bureau complet, effectuez les étapes suivantes :
1. Connectez-vous au tableau de bord Gestionnaire de serveur de Windows Server.

    Vous pouvez vous connecter localement à l’aide d’un clavier et d’un écran, ou bien avec un contrôleur de gestion à distance (avec administration à distance ou BMC) ou le Bureau à distance. 

1. Dans Gestionnaire de serveur, sous **Tableau de bord**, sélectionnez **Serveur local**.
1. Dans la page **Propriétés** du serveur, en regard de **Commentaires et diagnostics**, sélectionnez **Paramètres**.

    Dans la page **Paramètres**, les paramètres **Fréquence des commentaires** et **Données de diagnostic et d’utilisation** s’affichent. 
 
1. Développez le paramètre **Données de diagnostic et d’utilisation** pour sélectionner l’une des options suivantes :
    - **Données requises pour le diagnostic**
    - **Amélioré**
    - **Données de diagnostic facultatives**

    >[!NOTE]
    > Dans la page **Paramètres**, si l’avis **Certains paramètres sont gérés par votre organisation** s’affiche, le paramètre **Données de diagnostic et d’utilisation** peut ne pas être disponible.

## <a name="next-steps"></a>Étapes suivantes
Pour gérer les paramètres de confidentialité avec la stratégie de groupe sur plusieurs serveurs à la fois ou dans l’ensemble de votre entreprise, consultez :
-   [Gérer des données de diagnostic d’entreprise](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#manage-enterprise-diagnostic-data)
