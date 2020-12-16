---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 12/9/2020
ms.reviewer: sijuman
ms.lastreviewed: 12/9/2020
ms.openlocfilehash: 003f6801209d5d5ab1c9c4bd1df0fa47578004ee
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96935144"
---
### <a name="error-thrown-when-installing-the-az-modules"></a>Erreur générée au moment de l’installation des modules Az

- Champ d’application : Ce problème concerne les versions 2002 et ultérieures
- Cause : Au moment de l’installation du module, une erreur est générée. Le message d’erreur indique : `Register-PacakgeSource : A parameter cannot be found that matches parameter name. 'PackageManagementProvider'.` Sinon, le message d’erreur peut inclure le texte suivant : `PackageManagement\Install-Package : Cannot convert value "2.0.1-preview" to type "System.Version". Error: "Input string was not in a correct format."`
- Correction : Dans la même session, exécutez l’applet de commande suivante :  
    `Install-Module PowershellGet -MinimumumVersion 2.3.0 -Force`  
Fermez votre session, puis démarrez une nouvelle session PowerShell avec élévation de privilèges.
- Occurrence : Courant