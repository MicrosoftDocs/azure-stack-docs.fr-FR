---
title: Télécharger et extraire la solution
description: Découvrez comment télécharger et extraire le Kit de développement Azure Stack (ASDK).
author: justinha
ms.topic: article
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 08/10/2019
ms.openlocfilehash: f582f6061ebc2bbce7a3605ed7795b9ab5562738
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77695901"
---
# <a name="download-and-extract-the-asdk"></a>Télécharger et extraire la solution
Après avoir vérifié que l’ordinateur hôte du Kit de développement répond bien aux exigences de base pour l’installation du Kit de développement Azure Stack (ASDK), vous devez télécharger puis extraire le package de déploiement du Kit de développement Azure Stack afin d’obtenir le fichier Cloudbuilder.vhdx.

## <a name="download-the-asdk"></a>Télécharger l’ASDK
1. Avant de commencer le téléchargement, vérifiez que votre ordinateur répond aux prérequis suivants :

   - Votre ordinateur doit avoir au moins 60 Go d’espace disque disponible sur quatre disques durs logiques identiques et distincts en plus du disque de système d’exploitation.
   - [.NET Framework 4.6 (ou ultérieur)](https://dotnet.microsoft.com/download/dotnet-framework-runtime/net46) doit être installé.

2. [Accédez à la page de démarrage](https://azure.microsoft.com/overview/azure-stack/try/?v=try) sur laquelle vous pouvez télécharger le kit ASDK, renseignez vos informations, puis cliquez sur **Envoyer**.
3. Téléchargez et exécutez le script de vérification des conditions requises du [vérificateur de déploiement pour ASDK](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409). Ce script autonome passe en revue les vérifications des conditions requises réalisées par la configuration pour ASDK. Il vous permet de vous assurer que vous respectez les exigences matérielles et logicielles avant de télécharger le package plus volumineux d’ASDK.
4. Sous **Télécharger le logiciel**, cliquez sur **Kit de développement Azure Stack**.

   > [!NOTE]
   > La taille du téléchargement du kit ASDK (AzureStackDevelopmentKit.exe) est d’environ 10 Go.

## <a name="extract-the-asdk"></a>Extraire le kit ASDK
1. Une fois le téléchargement terminé, cliquez sur **Exécuter** pour lancer l’auto-extracteur ASDK (AzureStackDevelopmentKit.exe).
2. Lisez et acceptez le contrat de licence de la page **Contrat de licence** de l’Assistant de l’auto-extracteur, plus cliquez sur **Suivant**.
3. Passez en revue la déclaration de confidentialité de la page **Avis important** de l’Assistant de l’auto-extracteur, puis cliquez sur **Suivant**.
4. Sélectionnez l’emplacement où les fichiers de configuration Azure Stack doivent être extraits sur la page **Sélectionner l’emplacement de destination** de l’Assistant de l’auto-extracteur, puis cliquez sur **Suivant**. L’emplacement par défaut est *dossier actuel*\Kit de développement Azure Stack. 
5. Passez en revue le résumé de l’emplacement de la page **Ready to Extract** (Prêt pour l’extraction) de l’Assistant de l’auto-extracteur, puis cliquez sur **Extraire** pour extraire les fichiers CloudBuilder.vhdx (environ 28 Go) et ThirdPartyLicenses.rtf. Ce processus prend un certain temps.
6. Copiez ou déplacez le fichier CloudBuilder.vhdx à la racine du lecteur C:\ (`C:\CloudBuilder.vhdx`) sur l’ordinateur hôte ASDK.

> [!NOTE]
> Après avoir extrait les fichiers, vous pouvez supprimer les fichiers .EXE et .BIN pour récupérer de l’espace sur le disque dur. Vous pouvez aussi sauvegarder ces fichiers pour ne pas avoir à les retélécharger en cas de redéploiement du kit ASDK.


## <a name="next-steps"></a>Étapes suivantes
[Préparer l’ordinateur hôte de l’ASDK](asdk-prepare-host.md)