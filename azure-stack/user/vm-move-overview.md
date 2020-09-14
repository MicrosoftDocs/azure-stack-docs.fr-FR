---
title: Déplacer une machine virtuelle vers Azure Stack Hub
description: Découvrez les différentes façons dont vous pouvez déplacer une machine virtuelle vers Azure Stack Hub.
author: mattbriggs
ms.topic: conceptual
ms.date: 9/8/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: 932fed2c6097fbbf41606f365b0c5a424bf1381b
ms.sourcegitcommit: 9a340b383dcf42c85bc6ec0d01ff3c9ae29dfe4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89609890"
---
# <a name="move-a-vm-to-azure-stack-hub-overview"></a>Présentation de la procédure permettant de déplacer une machine virtuelle vers Azure Stack Hub

Vous pouvez déplacer des machines virtuelles de votre environnement vers Azure Stack Hub. Vous devez prendre en compte certaines limitations lorsque vous envisagez de déplacer vos charges de travail. Cet article répertorie les conditions requises pour le disque dur virtuel dans Azure Stack Hub. Azure Stack Hub nécessite un disque dur virtuel de génération 1. Votre machine virtuelle doit être généralisée ou spécialisée. Utilisez des machines virtuelles généralisées comme base pour les machines virtuelles créées dans Azure Stack. Une machine virtuelle spécialisée contient des comptes d’utilisateur. Pour migrer, préparer et télécharger le disque dur virtuel, vérifiez que celui-ci respecte la configuration requise, chargez l’image dans un compte de stockage dans Azure Stack Hub, puis créez la machine virtuelle dans votre cloud. Si votre tâche de migration est plus complexe, consultez le livre blanc sur la *migration vers Azure Stack Hub*.

Les images personnalisées se présentent sous deux formes : **généralisées** et **spécialisées**.

- **Image généralisée**  
  Une image de disque généralisée est une image qui a été préparée avec **Sysprep** pour supprimer toutes les informations uniques (par exemple les comptes d’utilisateur), ce qui lui permet d’être réutilisée pour créer plusieurs machines virtuelles. Les disques durs virtuels généralisés sont un outil idéal lors de la création d’images que l’opérateur cloud Azure Stack Hub envisage d’utiliser en tant qu’éléments de la place de marché. Les images proposées via le portail d’administration ou les points de terminaison d’administrateur sont des **images de plateforme**.

- **Image spécialisée**  
  Une image de disque spécialisée est une copie d’un VHD (disque dur virtuel) d’une machine virtuelle existante qui contient les comptes d’utilisateur, applications et autres données d’état de votre machine virtuelle d’origine. Il s’agit généralement du format dans lequel les machines virtuelles sont migrées vers Azure Stack Hub. Les disques durs virtuels spécialisés conviennent parfaitement quand vous devez migrer des machines virtuelles locales vers Azure Stack Hub.

Lors du déplacement d’une image dans Azure Stack Hub, réfléchissez à la façon dont vous souhaitez que l’image soit utilisée.

- **Charge de travail personnelle**  
    Vous disposez d’un ordinateur dans votre environnement local ou dans Azure international que vous utilisez pour le développement ou des tâches spécifiques et vous souhaitez tirer parti de l’hébergement dans un cloud privé avec Azure Stack Hub. Vous souhaitez conserver les données et les comptes d’utilisateur sur l’ordinateur. Vous souhaitez déplacer cet ordinateur dans Azure Stack Hub en tant qu’image spécialisée.

- **Image de référence**  
    Vous voulez partager une configuration de machine virtuelle commune et un ensemble d’applications au sein de votre groupe de travail. Vous n’avez pas besoin de partager l’image avec des utilisateurs en dehors de votre domaine Azure Stack Hub (locataire de répertoire). Dans ce cas, vous devez généraliser l’image en supprimant les données et les comptes d’utilisateur. Vous pouvez ensuite partager cette image avec d’autres utilisateurs de votre locataire.

- **Offre de la Place de marché Azure Stack Hub**  
    Votre opérateur cloud peut utiliser une image généralisée comme base d’une offre de la place de marché. Une fois que vous avez préparé votre image, votre opérateur cloud peut utiliser l’image personnalisée pour créer une offre de la Place de marché pour votre instance Azure Stack Hub. Les utilisateurs peuvent créer leur propre machine virtuelle à partir de l’image, comme pour toute autre offre de la Place de marché. Pour créer cette offre, vous devez collaborer avec votre opérateur cloud.

## <a name="verify-vhd-requirements"></a>Vérifier la configuration requise du disque dur virtuel

> [!IMPORTANT]  
> Lors de la préparation de votre disque dur virtuel, vous devez respecter les prérequis suivants. Sinon, vous ne pourrez pas utiliser votre disque dur virtuel dans Azure Stack Hub.
> Avant de charger l’image, tenez compte des éléments suivants :
> - Azure Stack Hub prend en charge uniquement les images provenant de machines virtuelles de génération 1.
> - Le disque dur virtuel est de type fixe. Azure Stack Hub ne prend pas en charge les disques durs virtuels dynamiques.
> - Le disque dur virtuel a une taille virtuelle minimale d’au moins 20 Mo.
> - Le disque dur virtuel est aligné, c’est-à-dire que la taille virtuelle doit être un multiple de 1 Mo.
> - Longueur du blob de disque dur virtuel = taille virtuelle + longueur de pied de page du disque dur virtuel (512). Un petit pied de page à la fin de l’objet blob décrit les propriétés du disque dur virtuel. 

Pour consulter les étapes permettant de réparer votre disque dur virtuel, consultez [Vérifier votre disque dur virtuel](vm-move-from-azure.md#verify-your-vhd)

## <a name="methods-of-moving-a-vm"></a>Méthodes de déplacement d’une machine virtuelle

Vous pouvez déplacer manuellement votre machine virtuelle dans Azure Stack Hub avec les scénarios suivants :

| Scénario | Instructions |
| --- | --- |
| De la version internationale d’Azure vers Azure Stack Hub | Préparez votre disque dur virtuel dans la version internationale d’Azure, puis chargez-le sur Azure Stack Hub. Pour plus d’informations, consultez [Déplacer une machine virtuelle d’Azure vers Azure Stack Hub](vm-move-from-azure.md). |
| Local et généralisé vers Azure Stack Hub | Préparez votre disque dur virtuel et généralisez un disque dur virtuel localement dans Hyper-V, puis chargez-le sur Azure Stack Hub. Pour plus d’informations, consultez [Déplacer une machine virtuelle généralisée d’un emplacement local vers Azure Stack Hub](vm-move-generalized.md). |
| Local et spécialisé vers Azure Stack Hub | Préparez votre disque dur virtuel spécialisé localement dans Hyper-V, puis chargez-le sur Azure Stack Hub. Pour plus d’informations, consultez [Déplacer une machine virtuelle spécialisée d’un emplacement local vers Azure Stack Hub](vm-move-specialized.md). |

## <a name="migrate-to-azure-stack-hub"></a>Migrer vers Azure Stack Hub

Vous trouverez des informations, des listes de vérification et les meilleures pratiques de planification et de migration de vos charges de travail en bloc vers Azure Stack Hub dans un guide écrit par les experts AzureCAT d’Azure international. Le guide se concentre sur la migration des applications existantes qui s’exécutent sur des serveurs physiques ou sur des plateformes de virtualisation existantes. En déplaçant ces charges de travail vers l’environnement IaaS Azure Stack Hub, les équipes peuvent bénéficier d’opérations plus fluides, de déploiements en libre-service, de configurations matérielles standardisées et d’une cohérence au sein d’Azure.

[Procurez-vous le livre blanc](https://azure.microsoft.com/resources/migrate-to-azure-stack-hub-patterns-and-practices-checklists/).

Vous pouvez également trouver des conseils sur la migration dans le Cloud Adoption Framework. Pour plus d’informations, consultez [Planifier votre migration Azure Stack Hub](/azure/cloud-adoption-framework/scenarios/azure-stack/plan). 

## <a name="next-steps"></a>Étapes suivantes

[Présentation des machines virtuelles Azure Stack Hub](azure-stack-compute-overview.md)

[Ajouter une image de machine virtuelle personnalisée à Azure Stack Hub](../operator/azure-stack-add-vm-image.md)