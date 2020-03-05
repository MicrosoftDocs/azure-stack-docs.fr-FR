---
title: Rendre les groupes identiques de machines virtuelles disponibles dans Azure Stack Hub
description: Découvrez comment un opérateur cloud peut ajouter des groupes de machines virtuelles identiques à la Place de marché Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2019
ms.openlocfilehash: f0b23df25ef2e9fffe9c5f43ee649ecfe5f74092
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77695289"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack-hub"></a>Rendre les groupes identiques de machines virtuelles disponibles dans Azure Stack Hub

Les groupes de machines virtuelles identiques constituent une ressource de calcul Azure Stack Hub. Vous pouvez utiliser les groupes identiques pour déployer et gérer des groupes de machines virtuelles identiques. Étant donné que toutes les machines virtuelles sont configurées de la même façon, il n’est pas nécessaire de préprovisionner les machines virtuelles des groupes identiques. Il est plus simple de créer des services à grande échelle qui ciblent le Big Compute, le Big Data et les charges de travail conteneurisées.

Cet article explique comment rendre des groupes identiques disponibles dans la Place de marché Azure Stack Hub. Lorsque vous aurez suivi cette procédure, vos utilisateurs pourront ajouter des groupes de machines virtuelles identiques à leurs abonnements.

Les groupes de machines virtuelles identiques sur Azure Stack Hub ont la même fonction que sur Azure. Pour plus d’informations, consultez les vidéos suivantes :

* [Mark Russinovich parle des groupes identiques Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Virtual machine scale sets with Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Sur Azure Stack Hub, les groupes de machines virtuelles identiques ne prennent pas en charge la mise à l’échelle automatique. Vous pouvez ajouter d’autres instances à un groupe identique en utilisant les modèles Resource Manager, l’interface de ligne de commande ou PowerShell.

## <a name="prerequisites"></a>Prérequis

* **Place de marché Azure Stack Hub :** Inscrivez Azure Stack Hub auprès d’Azure international pour rendre les éléments disponibles dans la Place de marché Azure Stack Hub. Suivez les instructions fournies dans [Inscrire Azure Stack Hub auprès d’Azure](azure-stack-registration.md).
* **Image du système d’exploitation :** Pour pouvoir créer un groupe de machines virtuelles identiques, vous devez au préalable télécharger les images de machine virtuelle à utiliser dans ce groupe à partir de la [Place de marché Azure Stack Hub](azure-stack-download-azure-marketplace-item.md). Pour qu’un utilisateur puisse créer un groupe identique, les images doivent déjà être présentes.

## <a name="use-the-azure-stack-hub-portal"></a>Utiliser le portail Azure Stack Hub

>[!IMPORTANT]  
> Les informations contenues dans cette section s’appliquent quand vous utilisez Azure Stack Hub versions 1808 ou ultérieures.

1. Connectez-vous au portail Azure Stack Hub. Ensuite, accédez à **Tous les services**, **Groupes de machines virtuelles identiques**, puis, sous **COMPUTE**, sélectionnez les **groupes de machines virtuelles identiques**.
   ![Sélectionner des groupes de machines virtuelles identiques](media/azure-stack-compute-add-scalesets/all-services.png)

2. Sélectionnez ***Créer des groupes de machines virtuelles identiques***.
   ![Créer un groupe de machines virtuelles identiques](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. Renseignez les champs vides en choisissant dans les menus déroulants les options appropriées pour **Image du disque du système d’exploitation**, **Abonnement** et **Taille d’Instance**. Sélectionnez **Oui** pour **Utiliser des disques managés**. Cliquez sur **Créer**.
    ![Configurer et créer des groupes de machines virtuelles identiques](media/azure-stack-compute-add-scalesets/create.png)

4. Pour voir votre nouveau groupe de machines virtuelles identiques, accédez à **Toutes les ressources**, recherchez le nom du groupe de machines virtuelles identiques, puis sélectionnez son nom dans les résultats de la recherche.
   ![Afficher le groupe de machines virtuelles identiques](media/azure-stack-compute-add-scalesets/search.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Mettre à jour des images dans un groupe de machines virtuelles identiques

Après avoir créé un groupe de machines virtuelles identiques, les utilisateurs peuvent mettre à jour des images dans un groupe identique sans devoir recréer aucun groupe identique. Le processus de mise à jour d’une image varie selon les scénarios suivants :

1. Le modèle de déploiement du groupe de machines virtuelles identiques **spécifie dernière** pour **version** :  

   Lorsque la `version` est définie sur **dernière** dans la section `imageReference` du modèle pour un groupe identique, les opérations de scale-up sur le groupe identique utilisent la dernière version disponible de l’image pour les instances de groupe identique. Une fois une montée en puissance terminée, vous pouvez supprimer les instances de groupes de machines virtuelles identiques les plus anciennes. Les valeurs de `publisher`, `offer` et `sku` restent inchangées.

   L’exemple JSON ci-après spécifie `latest` :  

    ```json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

2. Le modèle de déploiement du groupe de machines virtuelles identiques **ne spécifie pas de dernière** pour **version** et spécifie un numéro de version à la place :  

    Si l’opérateur Azure Stack télécharge une image d’une version plus récente (et supprime la version antérieure), le scale-up du groupe identique n’est pas possible. Ceci est normal, car la version de l’image spécifiée dans le modèle du groupe identique doit être disponible.  

Pour plus d’informations, consultez [disques et images du système d’exploitation](../user/azure-stack-compute-overview.md#operating-system-disks-and-images).  

## <a name="scale-a-virtual-machine-scale-set"></a>Mettre à l’échelle un groupe de machines virtuelles identiques

Vous pouvez augmenter ou réduire la taille d’un groupe identique de machines virtuelles.

1. Dans le portail, sélectionnez votre groupe identique, puis choisissez **Mise à l’échelle**.

2. Utilisez la barre de défilement pour définir la nouvelle échelle pour ce groupe identique de machines virtuelles, puis cliquez sur **Enregistrer**.

     ![Mettre à l’échelle le groupe de machines virtuelles identiques](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="next-steps"></a>Étapes suivantes

* [Télécharger des éléments de la Place de marché à partir d’Azure vers Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)
