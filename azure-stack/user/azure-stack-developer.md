---
title: Développer des applications pour Azure Stack Hub
description: Considérations relatives au développement pour le prototypage d’applications sur Azure Stack Hub à l'aide des services Azure.
author: sethmanheim
ms.topic: article
ms.date: 06/12/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 9aea9074b490a9d6e99ad4da0967d88ed63db04a
ms.sourcegitcommit: dd140b3a2ac8e558eae9f5f422711d2ba560da16
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84744842"
---
# <a name="develop-for-azure-stack-hub"></a>Développer pour Azure Stack Hub

Vous pouvez commencer à développer des applications dès aujourd’hui, même si vous n’avez pas accès à un environnement Azure Stack Hub. Azure Stack Hub fournit des services Microsoft Azure qui s’exécutent dans votre centre de données, ce qui signifie que vous pouvez utiliser les mêmes outils et processus pour développer sur Azure Stack Hub.

## <a name="development-considerations"></a>Développement - Éléments à prendre en compte

Avec un peu de préparation et à l’aide des instructions fournies dans les rubriques suivantes, vous pouvez utiliser Azure pour émuler un environnement Azure Stack Hub.

* Dans Azure, vous pouvez créer des modèles Azure Resource Manager pouvant être déployés sur Azure Stack Hub. Pour obtenir des conseils sur le développement de modèles pour assurer la portabilité, consultez [Considérations relatives aux modèles](azure-stack-develop-templates.md).
* Il existe des différences de disponibilité de service et de gestion des versions de services entre Azure et Azure Stack Hub. Vous pouvez utiliser le [module de stratégie Azure Stack Hub](azure-stack-policy-module.md) pour restreindre les types de ressources et la disponibilité des services Azure à ce qui est disponible dans Azure Stack Hub. La restriction des services garantit que vos applications s’appuient sur les services accessibles à Azure Stack Hub.
* Les [modèles de démarrage rapide Azure Stack Hub](https://github.com/Azure/AzureStack-QuickStart-Templates) sont des exemples de scénarios courants qui montrent comment développer des modèles pouvant être déployés sur Azure et Azure Stack Hub.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le développement Azure Stack, consultez les articles suivants :

* [Meilleures pratiques relatives aux modèles Azure Resource Manager](azure-stack-develop-templates.md)
* [Modèles de démarrage rapide Azure Stack Hub disponibles sur GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates)
