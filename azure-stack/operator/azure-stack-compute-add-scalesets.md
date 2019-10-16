---
title: Mettre les groupes de machines virtuelles identiques à disposition dans Azure Stack | Microsoft Docs
description: Découvrez comment un opérateur cloud peut ajouter des groupes de machines virtuelles identiques à la Place de marché Azure Stack.
services: azure-stack
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 10/04/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: ed73441f1a8d3c1c722ce35d5deda9ab7387283b
ms.sourcegitcommit: f91979c1613ea1aa0e223c818fc208d902b81299
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974090"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Mettre les groupes de machines virtuelles identiques à disposition dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*
  
Les groupes de machines virtuelles identiques constituent une ressource de calcul Azure Stack. Vous pouvez les utiliser pour déployer et gérer un groupe de machines virtuelles identiques. Étant donné que toutes les machines virtuelles sont configurées de la même façon, il n’est pas nécessaire de préprovisionner les machines virtuelles des groupes identiques. Il est plus simple de créer des services à grande échelle qui ciblent le Big Compute, le Big Data et les charges de travail conteneurisées.

Cet article explique le processus de mise à disposition des groupes identiques dans la Place de marché Azure Stack. Lorsque vous aurez suivi cette procédure, vos utilisateurs pourront ajouter des groupes de machines virtuelles identiques à leurs abonnements.

Les groupes de machines virtuelles identiques sur Azure Stack suivent le même principe que sur Azure. Pour plus d’informations, consultez les vidéos suivantes :

* [Mark Russinovich parle des groupes identiques Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Virtual machine scale sets with Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Sur Azure Stack, les groupes de machines virtuelles identiques ne sont pas compatibles avec la mise à l’échelle automatique. Vous pouvez ajouter d’autres instances à un groupe identique en utilisant les modèles Resource Manager, l’interface de ligne de commande ou PowerShell.

## <a name="prerequisites"></a>Prérequis

* **Place de marché Azure Stack :** Inscrivez Azure Stack auprès d’Azure pour un déploiement à l’échelle mondiale afin d’activer la disponibilité des éléments dans la Place de marché Azure Stack. Suivez les instructions fournies dans [Inscrire Azure Stack avec Azure](azure-stack-registration.md).
* **Image du système d’exploitation :** Avant de pouvoir créer un groupe de machines virtuelles identiques, vous devez télécharger les images de machine virtuelle à utiliser dans ce groupe à partir de la [Place de marché Azure Stack](azure-stack-download-azure-marketplace-item.md). Pour qu’un utilisateur puisse créer un groupe identique, les images doivent déjà être présentes.

## <a name="use-the-azure-stack-portal"></a>Utiliser le portail Azure Stack

>[!IMPORTANT]  
> Les informations contenues dans cette section s’appliquent quand vous utilisez Azure Stack 1808 ou ultérieur. Si vous disposez de la version 1807 (ou antérieure), consultez [Ajouter le groupe de machines virtuelles identiques (avant la version 1808)](#add-the-virtual-machine-scale-set-prior-to-version-1808).

1. Connectez-vous au portail Azure Stack. Ensuite, accédez à **Tous les services**, **Groupes de machines virtuelles identiques**, puis, sous **COMPUTE**, sélectionnez les **groupes de machines virtuelles identiques**.
   ![Sélectionner des groupes de machines virtuelles identiques](media/azure-stack-compute-add-scalesets/all-services.png)

2. Sélectionnez ***Créer des groupes de machines virtuelles identiques***.
   ![Créer un groupe de machines virtuelles identiques](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. Renseignez les champs vides en choisissant dans les menus déroulants les options appropriées pour **Image du disque du système d’exploitation**, **Abonnement** et **Taille d’Instance**. Sélectionnez **Oui** pour **Utiliser des disques managés**. Cliquez sur **Créer**.
    ![Configurer et créer des groupes de machines virtuelles identiques](media/azure-stack-compute-add-scalesets/create.png)

4. Pour voir votre nouveau groupe de machines virtuelles identiques, accédez à **Toutes les ressources**, recherchez le nom du groupe de machines virtuelles identiques, puis sélectionnez son nom dans les résultats de la recherche.
   ![Afficher le groupe de machines virtuelles identiques](media/azure-stack-compute-add-scalesets/search.png)

## <a name="add-the-virtual-machine-scale-set-prior-to-version-1808"></a>Ajouter le groupe identique de machines virtuelles (avant la version 1808)

>[!IMPORTANT]  
> Les informations contenues dans cette section s’appliquent lorsque vous utilisez une version d’Azure Stack antérieure à la version 1808. Si vous utilisez la version 1808 ou une version ultérieure, voir [Utiliser le portail Azure Stack](#use-the-azure-stack-portal).

1. Ouvrez Azure Stack Marketplace et connectez-vous à Azure. Sélectionnez **Gestion de la Place de marché**, puis cliquez sur **+ Ajouter à partir d’Azure**.

    ![Gestion de la Place de marché Azure Stack](media/azure-stack-compute-add-scalesets/image01.png)

2. Ajoutez et téléchargez l’article de Place de marché de groupe identique de machines virtuelles.

    ![Élément de la Place de marché - Groupe de machines virtuelles identiques](media/azure-stack-compute-add-scalesets/image02.png)

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

   Avant que la montée en puissance puisse utiliser une nouvelle image, vous devez télécharger cette nouvelle image :  

   * Si l’image de la Place de marché Azure Stack est plus récente que celle du groupe identique, téléchargez la nouvelle image pour remplacer l’ancienne. Une fois l’image remplacée, un utilisateur peut continuer à monter en puissance.

   * Si la version de l’image de la Place de marché Azure Stack est la même que celle de l’image du groupe identique, supprimez l’image qui est utilisée dans le groupe identique, puis téléchargez la nouvelle. Entre la suppression de l’image d’origine et le téléchargement de la nouvelle image, vous ne pouvez pas effectuer de scale-up.

   Ce processus est requis pour resyndiquer les images qui utilisent le format de fichier partiellement alloué, introduit avec la version 1803.

2. Le modèle de déploiement du groupe de machines virtuelles identiques **ne spécifie pas de dernière** pour **version** et spécifie un numéro de version à la place :  

    Si vous téléchargez une image avec une version plus récente (ce qui modifie la version disponible), le groupe identique ne peut pas monter en puissance. Ceci est normal, car la version de l’image spécifiée dans le modèle du groupe identique doit être disponible.  

Pour plus d’informations, consultez [disques et images du système d’exploitation](../user/azure-stack-compute-overview.md#operating-system-disks-and-images).  

## <a name="scale-a-virtual-machine-scale-set"></a>Mettre à l’échelle un groupe de machines virtuelles identiques

Vous pouvez augmenter ou réduire la taille d’un groupe identique de machines virtuelles.

1. Dans le portail, sélectionnez votre groupe identique, puis choisissez **Mise à l’échelle**.

2. Utilisez la barre de défilement pour définir la nouvelle échelle pour ce groupe identique de machines virtuelles, puis cliquez sur **Enregistrer**.

     ![Mettre à l’échelle le groupe de machines virtuelles identiques](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="remove-a-virtual-machine-scale-set"></a>Supprimer un groupe de machines virtuelles identiques

Pour supprimer un élément de la galerie du groupe de machines virtuelles identiques, exécutez la commande PowerShell suivante :

```powershell  
Remove-AzsGalleryItem
```

> [!NOTE]
> L’élément de la galerie n’est pas forcément supprimé immédiatement de la Place de marché. Pour que l’élément cesse de s’afficher dans la Place de marché, vous devrez peut-être actualiser le portail Azure Stack plusieurs fois.

## <a name="next-steps"></a>Étapes suivantes

* [Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack](azure-stack-download-azure-marketplace-item.md)
