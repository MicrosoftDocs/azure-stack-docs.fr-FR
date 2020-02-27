---
title: Feuille de déploiement pour les systèmes intégrés Azure Stack Hub
description: Découvrez comment installer et utiliser l’outil de feuille de calcul de déploiement pour déployer Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 04/19/2019
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 04/19/2019
ms.openlocfilehash: a008aab83970941f6c81d14f36d6f1502369fe46
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77510025"
---
# <a name="deployment-worksheet-for-azure-stack-hub-integrated-systems"></a>Feuille de déploiement pour les systèmes intégrés Azure Stack Hub

La feuille de calcul de déploiement Azure Stack Hub est une application Windows Forms qui rassemble toutes les informations de déploiement nécessaires et les décisions au même endroit. Vous pouvez remplir la feuille de calcul de déploiement pendant le processus de planification et l’examiner avant le début du déploiement.

Les informations requises par la feuille de calcul contiennent des informations sur la mise en réseau, la sécurité et l’identité. Cela nécessite des décisions importantes qui peuvent nécessiter des connaissances dans de nombreux domaines différents. Par conséquent, nous vous recommandons de consulter les équipes possédant des compétences dans ces domaines pour qu’elles vous aident à remplir la feuille de calcul.

Lors du remplissage de la feuille de calcul, vous devrez peut-être apporter des modifications de configuration pré-déploiement à votre environnement réseau. Ceci peut inclure la réservation d’espaces d’adressage IP pour la solution Azure Stack Hub ainsi que la configuration de routeurs, commutateurs et pare-feu afin de préparer la connectivité à la nouvelle solution Azure Stack Hub.

> [!NOTE]
> Pour plus d’informations sur le remplissage de la feuille de calcul de déploiement, consultez [cet article dans la documentation Azure Stack Hub](azure-stack-datacenter-integration.md).

[![Feuille de calcul de déploiement](media/azure-stack-deployment-worksheet/depworksheet.png "Feuille de calcul de déploiement")](media/azure-stack-deployment-worksheet/depworksheet.png)

## <a name="installing-the-windows-powershell-module"></a>Installation du module Windows PowerShell

Pour chaque version de la feuille de calcul de déploiement, vous devez effectuer une installation unique d’un module Powershell pour chaque ordinateur sur lequel vous souhaitez utiliser la feuille de calcul de déploiement.

> [!NOTE]  
> L’ordinateur doit être connecté à Internet pour que cette méthode fonctionne.

1. Ouvrez une invite PowerShell avec élévation de privilèges.

2. Dans la fenêtre PowerShell, installez le module à partir de la [galerie PowerShell](https://www.powershellgallery.com/packages/Azs.Deployment.Worksheet/) :

   ```PowerShell
   Install-Module -Name Azs.Deployment.Worksheet -Repository PSGallery
   ```

Si vous recevez un message concernant une installation à partir d’un référentiel non approuvé, appuyez sur **Y (O)** pour continuer l’installation.

## <a name="use-the-deployment-worksheet-tool"></a>Utiliser l’outil de feuille de calcul de déploiement

Pour lancer et utiliser la feuille de calcul de déploiement sur un ordinateur sur lequel vous avez installé le module PowerShell de feuille de calcul de déploiement, procédez comme suit :

1. Démarrez Windows PowerShell (n’utilisez pas PowerShell ISE, car des résultats inattendus peuvent se produire). Il n’est pas nécessaire d’exécuter PowerShell en tant qu’administrateur.

2. Importez le module PowerShell **AzS.Deployment.Worksheet** :

   ```PowerShell
   Import-Module AzS.Deployment.Worksheet
   ```

3. Une fois que le module est importé, lancez la feuille de calcul de déploiement :

   ```PowerShell
   Start-DeploymentWorksheet
   ```

La feuille de calcul de déploiement se compose d’onglets distincts pour la collecte des paramètres d’environnement, tels que **Customer Settings (Paramètres client)** , **Network Settings (Paramètres réseau)** et **Scale Unit # (Nombre d’unités d’échelle)** . Vous devez fournir toutes les valeurs (à l’exception de celles qui sont marquées comme **facultatives**) sous tous les onglets avant que des configurations de fichiers de données puissent être générées. Une fois que toutes les valeurs requises ont été entrées dans l’outil, vous pouvez utiliser le menu **Action** pour **importer**, **exporter** et **générer**. Les fichiers JSON suivants sont requis pour le déploiement :

**Importer** : Vous permet d’importer un fichier de données de configuration Azure Stack Hub (ConfigurationData.json) généré par cet outil, ou un de ceux créés par les versions précédentes de la feuille de calcul de déploiement. Le fait d’effectuer une importation réinitialise les formulaires et supprime tous les paramètres entrés précédemment ou toutes les données déjà générées.

**Exporter** : Valide les données entrées actuellement dans les formulaires, génère les sous-réseaux IP et les affectations, puis enregistre le contenu en tant que fichiers de configuration au format JSON. Vous pouvez ensuite utiliser ces fichiers pour générer la configuration du réseau et installer Azure Stack Hub.

**Générer** : Valide les données entrées actuellement et génère la carte réseau sans exporter les fichiers JSON de déploiement. Deux nouveaux onglets sont créés si la fonction **Générer** réussit : **Subnet Summary (Résumé de sous-réseau)** et **IP Assignments (Affectations d’adresses IP)** . Vous pouvez analyser les données de ces onglets pour vérifier que les affectations de réseau sont telles que prévues.

**Effacer tout** : Efface toutes les données entrées actuellement dans les formulaires et réinitialise les valeurs par défaut.

**Save or Open your work in-progress (Enregistrer ou ouvrir votre travail en cours)**  : Vous pouvez enregistrer et ouvrir des données partiellement entrées sur lesquelles vous travaillez, à l’aide des menus **Fichier -> Enregistrer** et **Fichier -> Ouvrir**. Cela diffère des fonctions **Importer** et **Exporter**, car elles nécessitent l’entrée et la validation de toutes les données. Ouvrir/Enregistrer ne valide pas les données et ne nécessite pas que tous les champs soient remplis pour enregistrer votre travail en cours.

**Logging and Warning messages (Journalisation et messages d’avertissement)**  : Pendant l’utilisation du formulaire, des messages d’avertissements non critique peuvent s’afficher dans la fenêtre PowerShell. Les erreurs critiques sont affichées sous forme de messages contextuels. La journalisation détaillée facultative, notamment un journal écrit sur disque, peut être activée pour aider à résoudre les problèmes.

Pour démarrer l’outil avec la journalisation détaillée :

   ```PowerShell
   Start-DeploymentWorksheet -EnableLogging
   ```

Le journal enregistré est disponible dans le répertoire **Temp** de l’utilisateur actuel, par exemple : **C:\Users\me\AppData\Local\Temp\Microsoft_AzureStack\DeploymentWorksheet_Log.txt**.

## <a name="next-steps"></a>Étapes suivantes

* [Modèles de connexion pour le déploiement de Microsoft Azure Stack Hub](azure-stack-connection-models.md)
