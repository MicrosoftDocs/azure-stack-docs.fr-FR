---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 12/09/2019
ms.reviewer: bryanla
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: e1e2c3c6c3d1149b2cd034c2f5e4abfdf26e712f
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80424607"
---
Si vous avez déjà installé un fournisseur de ressources à partir de la Place de marché Azure Stack Hub, vous avez probablement rempli les prérequis courants et pouvez ignorer cette section. Sinon, veillez à respecter les prérequis suivants : 

1. [Inscrivez votre instance Azure Stack Hub auprès d’Azure](../operator/azure-stack-registration.md) si ce n’est déjà fait. Cette étape est nécessaire, car vous allez vous connecter à la Place de marché et y télécharger des éléments à partir d’Azure.

2. Si vous ne connaissez pas la fonctionnalité **Gestion de la Place de marché** du portail administrateur Azure Stack Hub, consultez [Télécharger des éléments de la Place de marché Azure et les publier sur Azure Stack Hub](../operator/azure-stack-download-azure-marketplace-item.md). Cet article vous guide tout au long du processus de téléchargement des éléments à partir d’Azure vers la Place de marché Azure Stack Hub. Il couvre les deux scénarios, connecté et déconnecté. Si votre instance Azure Stack Hub est déconnectée ou partiellement connectée, d’autres prérequis doivent être respectés en vue de l’installation.

3. Mettez à jour votre répertoire de base Azure Active Directory (Azure AD). À partir de la build 1910, la nouvelle application de fournisseur de ressources de déploiement (DRP, Deployment Resource Provider) doit être utilisée pour inscrire votre locataire de répertoire de base. Cette application permet à DRP de créer et d’inscrire correctement des fournisseurs de ressources. Si cette étape n’est pas effectuée, l’installation du fournisseur de ressources échoue. 

   - Après avoir correctement mis à jour votre instance Azure Stack Hub vers la build 1910 (ou supérieure), suivez les [instructions relatives au clonage/téléchargement du dépôt d’outils Azure Stack Hub](../operator/azure-stack-powershell-download.md). 
   - Ensuite, suivez les instructions relatives à la [mise à jour du répertoire de base Azure Stack Hub Azure AD (après l’installation de mises à jour ou de nouveaux fournisseurs de ressources)](https://github.com/Azure/AzureStack-Tools/tree/master/Identity#updating-the-azure-stack-aad-home-directory-after-installing-updates-or-new-resource-providers). 