---
title: Mettre à jour l’antivirus Windows Defender
titleSuffix: Azure Stack Hub
description: Découvrez comment mettre à jour l’antivirus Windows Defender sur Azure Stack Hub
author: justinha
ms.topic: article
ms.date: 12/04/2019
ms.author: justinha
ms.reviewer: fiseraci
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 31ef55554646065a5d61cc21abf4e72b7fcb4eda
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86488091"
---
# <a name="update-windows-defender-antivirus-on-azure-stack-hub"></a>Mettre à jour l’antivirus Windows Defender sur Azure Stack Hub

[L’antivirus Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) est une solution anti-programme malveillant qui sécurise votre infrastructure et la protège contre les virus. Chaque composant de l’infrastructure Azure Stack Hub (hôtes Hyper-V et machines virtuelles) est protégé par l’antivirus Windows Defender. Pour garantir une protection constante, les définitions, le moteur et la plateforme de l’antivirus Windows Defender doivent être régulièrement mis à jour. Le mode d’application des mises à jour dépend de votre configuration.

## <a name="connected-scenario"></a>Scénario connecté

Le [fournisseur de ressources de mise à jour](azure-stack-updates.md#the-update-resource-provider) Azure Stack Hub télécharge les mises à jour du moteur et les définitions du logiciel anti-programme malveillant plusieurs fois par jour. Chaque composant de l’infrastructure Azure Stack Hub récupère ensuite la mise à jour du fournisseur de ressources de mise à jour et l’applique automatiquement.

Pour les déploiements Azure Stack Hub qui sont connectés à l’Internet public, appliquez la [mise à jour mensuelle Azure Stack Hub](azure-stack-apply-updates.md). La mise à jour mensuelle Azure Stack Hub inclut les mises à jour de la plateforme de l’antivirus Windows Defender publiées au cours du mois.

## <a name="disconnected-scenario"></a>Scénario déconnecté

Pour les déploiements Azure Stack Hub qui ne sont pas connectés à l’Internet public (par exemple les centres de données protégés par un « air gap »), à partir de la version 1910, les clients peuvent appliquer les définitions de logiciel anti-programme malveillant et les mises à jour du moteur au moment de leur publication. 

Pour appliquer les mises à jour à votre solution Azure Stack Hub, commencez par les télécharger à partir du site Microsoft (liens ci-dessous), puis importez-les dans un conteneur d’objets blob de stockage sous votre compte *updateadminaccount*. Une tâche planifiée analyse le conteneur d’objets blob toutes les 30 minutes et, si de nouvelles définitions Defender et mises à jour du moteur sont détectées, celles-ci sont appliquées à l’infrastructure Azure Stack Hub. 

Pour les déploiements déconnectés qui n’ont pas été effectués dans la version 1910 ou ultérieure, ou qui n’ont pas la possibilité de télécharger des définitions Defender et des mises à jour du moteur quotidiennement, la mise à jour mensuelle Azure Stack Hub comprend les définitions de l’antivirus Windows Defender, ainsi que les mises à jour du moteur et de la plateforme du mois en cours. 


### <a name="set-up-windows-defender-for-manual-updates"></a>Configurer des mises à jour manuelles pour Windows Defender 

Avec la version 1910, deux nouvelles applets de commande ont été ajoutées au point de terminaison privilégié pour configurer la mise à jour manuelle de Windows Defender dans Azure Stack Hub. 

```powershell 
### cmdlet to configure the storage blob container for the Defender updates 
Set-AzsDefenderManualUpdate [-Container <string>] [-Remove]  
### cmdlet to retrieve the configuration of the Defender manual update settings 
Get-AzsDefenderManualUpdate  
``` 

La procédure suivante montre comment configurer la mise à jour manuelle de Windows Defender. 

1. Connectez-vous au point de terminaison privilégié et exécutez l’applet de commande suivante pour spécifier le nom du conteneur d’objets blob de stockage dans lequel les mises à jour de Defender seront téléchargées. 

   > [!NOTE] 
   > Le processus de mise à jour manuel décrit ci-dessous ne fonctionne que dans les environnements déconnectés où l’accès à « go.microsoft.com » n’est pas autorisé. Toute tentative d’exécution de l’applet de commande Set-AzsDefenderManualUpdate dans un environnement connecté va générer une erreur. 

   ```powershell 
   ### Configure the storage blob container for the Defender updates 
   Set-AzsDefenderManualUpdate -Container <yourContainerName>
   ``` 

2. Téléchargez les deux packages de mise à jour Windows Defender et enregistrez-les à un emplacement accessible à partir de votre portail d’administration Azure Stack Hub.  

   * mpam-fe.exe sur [https://go.microsoft.com/fwlink/?LinkId=121721&arch=x64](https://go.microsoft.com/fwlink/?LinkId=121721&arch=x64) 
   * nis_full.exe sur [https://go.microsoft.com/fwlink/?LinkId=197094](https://go.microsoft.com/fwlink/?LinkId=197094) 

   > [!NOTE] 
   > Vous devez télécharger ces deux fichiers **chaque fois** que vous souhaitez mettre à jour les signatures de Defender. 

3. Dans le portail d’administration, sélectionnez **Tous les services**. Ensuite, dans la catégorie **DONNÉES ET STOCKAGE**, sélectionnez **Comptes de stockage**. (Ou, dans la zone de filtre, commencez à taper **comptes de stockage** et sélectionnez l’option correspondante.) 

   ![Azure Stack Hub Defender - Tous les services](./media/azure-stack-security-av/image1.png)  

4. Dans la zone de filtre, tapez **mise à jour**, puis sélectionnez le compte de stockage **updateadminaccount**. 

5. Dans les détails du compte de stockage, sous **Services**, sélectionnez **Objets Blob**. 

   ![Azure Stack Hub Defender - Blob](./media/azure-stack-security-av/image2.png) 

6. Sous **Service blob**, sélectionnez **+ Conteneur** pour créer un conteneur. Entrez le nom qui a été spécifié avec Set-AzsDefenderManualUpdate (dans cet exemple *defenderupdates*), puis sélectionnez **OK**. 

   ![Azure Stack Hub Defender - Conteneur](./media/azure-stack-security-av/image3.png) 

7. Une fois le conteneur créé, cliquez sur le nom du conteneur, puis cliquez sur **Charger** pour charger les fichiers de package dans le conteneur. 

   ![Azure Stack Hub Defender - Charger](./media/azure-stack-security-av/image4.png) 

8. Sous **Charger l’objet blob**, cliquez sur l’icône de dossier, accédez aux fichiers *mpam-fe.exe* de mise à jour Windows Defender, puis cliquez sur **Ouvrir** dans la fenêtre de l’explorateur de fichiers. 

9. Sous **Charger l’objet blob**, cliquez sur **Charger**. 

   ![Azure Stack Hub Defender - Charger l’objet blob1](./media/azure-stack-security-av/image5.png) 

1. Répétez les étapes 8 et 9 pour le fichier *nis_full.exe*. 

   ![Azure Stack Hub Defender - Charger l’objet blob2](./media/azure-stack-security-av/image6.png)

Une tâche planifiée analyse le conteneur d’objets blob toutes les 30 minutes et applique tous les nouveaux packages Windows Defender.  

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur la sécurité dans Azure Stack Hub](azure-stack-security-foundations.md)
