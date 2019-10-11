---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: c3a85e866acc615d2d33219232f7890b32df50f5
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71938760"
---
Dans les workflows de validation, la **planification** d’un test utilise des paramètres communs au niveau du workflow que vous avez spécifiés lors de la création de votre workflow (voir [Paramètres de flux de travail communs dans la validation en tant que service Azure Stack](../azure-stack-vaas-parameters.md)). Si l’une des valeurs de paramètres de test devient non valide, vous devez les redéfinir comme indiqué dans la section relative à la [modification des paramètres de workflow](../azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> La planification d’un test de validation sur une instance existante créera une nouvelle instance à la place de l’ancienne contenue dans le portail. Les journaux d’activité de l’ancienne instance sont conservés, mais ne sont pas accessibles à partir du portail.  
Dès lors qu’un test est concluant, l’action de **planification** est désactivée.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. Sélectionnez **Planifier** dans le menu contextuel pour ouvrir une invite de planification de l’instance de test.

1. Passez en revue les paramètres de test, puis sélectionnez **Envoyer** pour planifier l’exécution du test.