---
title: Meilleures pratiques de Validation Azure Stack | Microsoft Docs
description: Cet article décrit les meilleures pratiques concernant l’utilisation de la Validation en tant que Service.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7e866187c81b488cc90f7ddaa443db4868ba4d9
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68417745"
---
# <a name="create-an-oem-package"></a>Créer un package OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Le Package d’Extension OEM Azure Stack est le mécanisme par lequel le contenu spécifique OEM est ajouté à l’infrastructure Azure Stack, pour une utilisation lors du déploiement ainsi que pour les processus opérationnels tels que la mise à jour, l’expansion et le remplacement de champ.

## <a name="creating-the-package"></a>Création du package

Une fois créé et validé, le package d’extension OEM peut être utilisé dans VaaS.  Avant de continuer, assurez-vous que vous avez suivi les étapes de [création d’un package OEM](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true). Le package est ensuite envoyé à Microsoft, ainsi que les résultats des tests VaaS pour la connexion au flux de travail de Validation du Package. Les étapes suivantes décrivent comment regrouper les fichiers générés dans un seul fichier zip que VaaS peut consommer.

1. Utilisez le contenu suivant pour le package :
    - Un fichier zip contenant le contenu du package
    - Un fichier manifest nommé `oemMetadata.xml`, dont le contenu doit être identique au fichier metadata.xml à la racine du contenu du package.

2. Sélectionnez les fichiers de contenu et créez un fichier zip à partir des contenus :

    ![Contenu du fichier zip](media/vaas-create-oem-package-1.png) ![Compresser les contenus de l’élément](media/vaas-create-oem-package-2.png)

3. Renommez le fichier obtenu de façon suffisamment descriptive pour que vous l’identifiez.

## <a name="verifying-the-contents"></a>Vérification des contenus

Pour valider la structure de votre fichier zip, inspectez-le et vérifiez qu’il ne contient aucun sous-dossier. Le domaine de premier niveau dispose des contenus compressés. Vous trouverez ci-dessous une structure de package valide.
> [!IMPORTANT]
> La compression du dossier parent plutôt des contenus entraîne l’échec de la signature du package.

![Contenus du package zippé correctement](media/vaas-create-oem-package-3.png)

Le fichier zip peut désormais être chargé vers VaaS et signé par Microsoft lors du workflow de Validation du Package.

## <a name="next-steps"></a>Étapes suivantes

- [Valider un package OEM](azure-stack-vaas-validate-oem-package.md)
