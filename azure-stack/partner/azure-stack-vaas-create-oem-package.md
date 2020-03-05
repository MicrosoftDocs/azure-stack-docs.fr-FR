---
title: Créer un package d’extension OEM
titleSuffix: Azure Stack Hub
description: Découvrez comment créer un package d’extension OEM dans Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 64ecf37e0fff2e9380a2c13b6fe65a41ec84de08
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704707"
---
# <a name="create-an-oem-package"></a>Créer un package OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Le package d’extension OEM Azure Stack Hub est le mécanisme par lequel le contenu propre à l’OEM est ajouté à l’infrastructure Azure Stack Hub. Le contenu est utilisé dans les processus de déploiement et les processus opérationnels tels que la mise à jour, l’expansion et le remplacement de champs.

## <a name="creating-the-package"></a>Création du package

Une fois créé et validé, le package d’extension OEM peut être utilisé dans VaaS. Avant de continuer, veillez à suivre les étapes de [création d’un package OEM](https://microsoft.sharepoint.com/:w:/r/teams/cloudsolutions/Sacramento/_layouts/15/Doc.aspx?sourcedoc=%7BD7406069-7661-419C-B3B1-B6A727AB3972%7D&file=Azure%20Stack%20OEM%20Extension%20Package.docx&action=default&mobileredirect=true). Le package est ensuite envoyé à Microsoft, ainsi que les résultats des tests VaaS pour la connexion au workflow de validation du package. Les étapes suivantes décrivent comment regrouper les fichiers générés dans un seul fichier zip que VaaS peut consommer.

1. Utilisez le contenu suivant pour le package :
    - Un fichier zip contenant le contenu du package
    - Un fichier manifeste nommé `oemMetadata.xml`, dont le contenu doit être identique au fichier `metadata.xml` situé à la racine du contenu du package.

2. Sélectionnez les fichiers de contenu et créez un fichier zip :

    ![Contenu du fichier zip lors de la création d’un package d’extension OEM](media/vaas-create-oem-package-1.png) ![Compresser le contenu des éléments lors de la création d’un package d’extension OEM](media/vaas-create-oem-package-2.png)

3. Renommez le fichier obtenu de façon explicite afin de l’identifier plus facilement.

## <a name="verifying-the-contents"></a>Vérification des contenus

Pour valider la structure de votre fichier zip, inspectez-le et vérifiez qu’il ne contient aucun sous-dossier. Le domaine de premier niveau dispose des contenus compressés. Vous trouverez ci-dessous une structure de package valide :

> [!IMPORTANT]
> La compression du dossier parent plutôt des contenus entraîne l’échec de la signature du package.

![Contenu du package correctement compressé lors de la création d’un package d’extension OEM](media/vaas-create-oem-package-3.png)

Le fichier zip peut maintenant être chargé dans VaaS et signé par Microsoft lors du workflow de validation du package.

## <a name="next-steps"></a>Étapes suivantes

- [Valider un package OEM](azure-stack-vaas-validate-oem-package.md)
