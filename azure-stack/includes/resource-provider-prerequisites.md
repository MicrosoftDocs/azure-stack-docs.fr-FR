---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 12/09/2019
ms.reviewer: bryanla
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 2a980becc3bf749987759f90a9ab84050d033a04
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92297858"
---
Si vous avez déjà installé un fournisseur de ressources, vous avez probablement rempli les prérequis suivants et pouvez ignorer cette section. Sinon, faites ce qui suit avant de continuer : 

1. [Inscrivez votre instance Azure Stack Hub auprès d’Azure](../operator/azure-stack-registration.md) si ce n’est déjà fait. Cette étape est nécessaire, car vous allez vous connecter à la Place de marché et y télécharger des éléments à partir d’Azure.

2. Si vous ne connaissez pas la fonctionnalité **Gestion de la Place de marché** du portail administrateur Azure Stack Hub, consultez [Télécharger des éléments de la Place de marché Azure et les publier sur Azure Stack Hub](../operator/azure-stack-download-azure-marketplace-item.md). Cet article vous guide tout au long du processus de téléchargement des éléments à partir d’Azure vers la Place de marché Azure Stack Hub. Il couvre les deux scénarios, connecté et déconnecté. Si votre instance Azure Stack Hub est déconnectée ou partiellement connectée, d’autres prérequis doivent être respectés en vue de l’installation.

3. Mettez à jour votre répertoire de base Azure Active Directory (Azure AD). À partir de la build 1910, une nouvelle application doit être inscrite dans votre abonné de répertoire de base. Cette application permet à Azure Stack Hub de créer et d’inscrire avec succès des fournisseurs de ressources plus récents (comme Event Hubs, IoT Hub, etc.) avec votre abonné Azure AD. Il s’agit d’une action ponctuelle qui doit être effectuée après la mise à niveau vers la build 1910 ou une build plus récente. Si cette étape n’est pas effectuée, les installations du fournisseur de ressources échouent. 

   - Après avoir correctement mis à jour votre instance Azure Stack Hub vers la build 1910 (ou supérieure), suivez les [instructions relatives au clonage/téléchargement du dépôt d’outils Azure Stack Hub](../operator/azure-stack-powershell-download.md). 
   - Ensuite, suivez les instructions relatives à la [mise à jour du répertoire de base Azure Stack Hub Azure AD (après l’installation de mises à jour ou de nouveaux fournisseurs de ressources)](https://github.com/Azure/AzureStack-Tools/tree/master/Identity#updating-the-azure-stack-aad-home-directory-after-installing-updates-or-new-resource-providers). 