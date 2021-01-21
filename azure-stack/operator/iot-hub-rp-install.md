---
title: Guide pratique pour installer IoT Hub sur Azure Stack Hub
description: Apprenez à installer le fournisseur de ressources IoT Hub sur Azure Stack Hub.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 47d06bc7363a9ce8d4de8971bf26c38bfdb16bcc
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256113"
---
# <a name="how-to-install-iot-hub-on-azure-stack-hub"></a>Guide pratique pour installer IoT Hub sur Azure Stack Hub

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

Cet article explique comment télécharger et installer le fournisseur de ressources IoT Hub afin de pouvoir l'offrir aux clients dans le cadre de l'abonnement. Le processus d'installation d'IoT Hub prend environ 2 heures.

## <a name="download-iot-hub"></a>Télécharger IoT Hub

<!-- ### Connected Scenario -->
::: zone pivot="state-connected"
Si votre instance d'Azure Stack Hub a accès à la Place de marché Azure, suivez les étapes décrites dans cette section pour télécharger et installer IoT Hub et ses dépendances. 

Pour télécharger IoT Hub dans le cadre d'un déploiement connecté, procédez comme suit :

1. Connectez-vous au portail administrateur d'Azure Stack Hub. 
2. Sélectionnez **Gestion de la Place de marché** sur la gauche, puis sélectionnez **Fournisseurs de ressources** et cliquez sur **+ Ajouter depuis Azure**.

    [![Recherche du fournisseur de ressources sur la Place de marché](media/iot-hub-rp-install/marketplace-rp-add-from-azure.png)](media/iot-hub-rp-install/marketplace-rp-add-from-azure.png#lightbox)

3. Si nécessaire, appliquez le filtre « IoT Hub », puis sélectionnez le package **IoT Hub**.

    [![Fournisseur de ressources IoT Hub sur la Place de marché](../operator/media/iot-hub-rp-install/download1.png)](../operator/media/iot-hub-rp-install/download1.png#lightbox)

4. Sur la page du package **IoT Hub**, sélectionnez **Télécharger**.

    [![Détails du package IoT Hub](../operator/media/iot-hub-rp-install/download2.png)](../operator/media/iot-hub-rp-install/download2.png#lightbox)

5. Attendez la fin du téléchargement du package. L'état **Téléchargement** apparaîtra ; cette étape peut prendre une dizaine de minutes.

    [![État du téléchargement du package IoT Hub](../operator/media/iot-hub-rp-install/download3.png)](../operator/media/iot-hub-rp-install/download3.png#lightbox)

6. Une fois le package téléchargé, l'état passe à **Non installé** sur la page **Gestion de la Place de marché**.

    [![Package IoT Hub téléchargé non installé](../operator/media/iot-hub-rp-install/download4.png)](../operator/media/iot-hub-rp-install/download4.png#lightbox)
::: zone-end

<!-- ### Disconnected or partially connected scenario -->
::: zone pivot="state-disconnected"
Pour télécharger IoT Hub dans le cadre d'un déploiement déconnecté ou partiellement connecté, vous devez commencer par télécharger les packages sur votre ordinateur local. Une fois ceux-ci téléchargés, importez-les dans votre instance d'Azure Stack Hub.

1. Si ce n’est pas déjà fait, suivez les instructions de [Télécharger des éléments de la Place de marché - Scénario déconnecté ou partiellement connecté](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected). Ici, vous téléchargez et exécutez l'outil de syndication de la Place de marché, qui vous permet de télécharger les packages IoT Hub.
2. Lorsque la fenêtre « Éléments de la Place de marché Azure » de l’outil de syndication s'ouvre, recherchez et sélectionnez « IoT Hub » pour télécharger les packages requis sur votre ordinateur local.
3. Une fois le téléchargement terminé, vous importez les packages sur votre instance Azure Stack Hub et les publiez sur la Place de marché.
::: zone-end

## <a name="install-iot-hub"></a>Installer IoT Hub

À partir de la page **Gestion de la Place de marché**, procédez comme suit pour installer le package IoT Hub :

1. Cliquez sur la ligne **IoT Hub**, puis sur **Démarrer l'installation**.

    [![Fournisseur de ressources IoT Hub en attente d'installation](../operator/media/iot-hub-rp-install/install1.png)](../operator/media/iot-hub-rp-install/install1.png#lightbox)

2. Cliquez sur **Composants requis pour l'installation**.

    [![Composants requis pour l'installation du fournisseur de ressources IoT Hub](../operator/media/iot-hub-rp-install/install2.png)](../operator/media/iot-hub-rp-install/install2.png#lightbox)

3. Pour suivre l'état de l'installation, consultez le panneau de notifications. Cette étape prendra environ 10 minutes.

    [![Installation des composants requis pour l'installation du fournisseur de ressources IoT Hub](../operator/media/iot-hub-rp-install/install3.png)](../operator/media/iot-hub-rp-install/install3.png#lightbox)

4. Attendez la fin de l'installation des composants requis. Cette étape prend généralement 5 à 10 minutes selon l'environnement.

    [![Fournisseur de ressources IoT Hub - Préparer les secrets](../operator/media/iot-hub-rp-install/install4.png)](../operator/media/iot-hub-rp-install/install4.png#lightbox)

5. Cliquez sur **Ajouter des certificats** sous **Préparer les secrets**. Fournissez le certificat pfx créé avec les composants requis.

    [![Fournisseur de ressources IoT Hub - Préparer les secrets - Charger le certificat](../operator/media/iot-hub-rp-install/install5.png)](../operator/media/iot-hub-rp-install/install5.png#lightbox)

6. Recherchez et fournissez le certificat pfx qui a été créé, ainsi que le mot de passe (l'entrée du script)

    [![Fournisseur de ressources IoT Hub - Préparer les secrets - Choisir un certificat](../operator/media/iot-hub-rp-install/install6.png)](../operator/media/iot-hub-rp-install/install6.png#lightbox)

    [![Fournisseur de ressources IoT Hub - Préparer les secrets - Fournir un mot de passe](../operator/media/iot-hub-rp-install/install61.png)](../operator/media/iot-hub-rp-install/install61.png#lightbox)

7. Cliquez sur **Installer** sous **Installer le fournisseur de ressources**.

    [![Fournisseur de ressources IoT Hub - Préparer les secrets - Terminé](../operator/media/iot-hub-rp-install/install7.png)](../operator/media/iot-hub-rp-install/install7.png#lightbox)

8. Une fois l'installation lancée, l'état du déploiement est disponible sur la Place de marché ou dans le panneau de notifications.

    [![Fournisseur de ressources IoT Hub - Installation en cours](../operator/media/iot-hub-rp-install/install8.png)](../operator/media/iot-hub-rp-install/install8.png#lightbox)

9. L'installation peut prendre 90 à 120 minutes. Patientez jusqu’à la fin de l’installation.

    [![Fournisseur de ressources IoT Hub - Installation terminée](../operator/media/iot-hub-rp-install/install91.png)](../operator/media/iot-hub-rp-install/install91.png#lightbox)

    [![Fournisseur de ressources de la Place de marché - Fournisseurs de ressources installés](../operator/media/iot-hub-rp-install/install92.png)](../operator/media/iot-hub-rp-install/install92.png#lightbox)

Le fournisseur de ressources IoT Hub a bien été installé. Pour démarrer, procédez comme suit :

1. Si nécessaire, suivez les instructions pour [créer un plan, des offres et un abonnement](./service-plan-offer-subscription-overview.md).

2. Si vous disposez déjà d'un abonnement, mettez à jour l'offre/le plan associé pour y inclure le service **Microsoft.Devices**. Accédez à **Plan** -> **Choisir le plan à mettre à jour** -> **Ajouter un service et un quota**.

3. Ajoutez **Service Microsoft.Devices**, puis cliquez sur **Enregistrer**.

    [![Ajouter le service IoT Hub au plan](../operator/media/iot-hub-rp-install/pd2.png)](../operator/media/iot-hub-rp-install/pd2.png#lightbox)

4. Vous avez terminé. Des instances d'IoT Hub peuvent maintenant être créées.

## <a name="using-iot-hub"></a>Utilisation d’un hub IoT

Pour en savoir plus sur l'utilisation d'IoT Hub, consultez la [documentation d'Azure IoT Hub](/azure/iot-hub).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la gestion d'IoT Hub sur Azure Stack Hub, consultez [Guide pratique pour gérer IoT Hub sur Azure Stack Hub](iot-hub-rp-manage.md).