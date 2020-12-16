---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 12/9/2020
ms.reviewer: sijuman
ms.lastreviewed: 12/9/2020
ms.openlocfilehash: e456f17c75b129a3f45c64b9b55e75d21ecb2973
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96935143"
---
### <a name="when-installing-az-module-falsely-throws-admin-rights-required-error"></a>Au moment de l’installation du module Az, une erreur relative aux droits d’administrateur est générée à tort

- Champ d’application : Ce problème concerne les versions 2002 et ultérieures
- Cause : Quand le module est installé à partir d’une invite de commandes avec élévation de privilèges, une erreur est générée. L’erreur indique `Administrator rights required`.
- Correction : Fermez votre session, puis démarrez une nouvelle session PowerShell avec élévation de privilèges. Vérifiez qu’il n’existe aucun module Az.Accounts chargé dans la session.
- Occurrence : Courant