---
title: Préparer des certificats PKI Azure Stack Hub pour un déploiement ou une rotation
titleSuffix: Azure Stack Hub
description: Découvrez comment préparer des certificats PKI pour un déploiement de systèmes intégrés Azure Stack Hub ou la rotation de secrets dans un environnement Azure Stack Hub existant.
author: ihenkel
ms.topic: article
ms.date: 09/16/2019
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 09/16/2019
ms.openlocfilehash: 3111d59a685425210bd8e63fbafcd4bb68c4a3a2
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76881594"
---
# <a name="prepare-azure-stack-hub-pki-certificates-for-deployment-or-rotation"></a>Préparer des certificats PKI Azure Stack Hub pour un déploiement ou une rotation

Les fichiers de certificat [obtenus auprès de l’autorité de certification de votre choix](azure-stack-get-pki-certs.md) doivent être importés et exportés avec des propriétés correspondant aux exigences de certificat d’Azure Stack Hub.

## <a name="prepare-certificates-for-deployment"></a>Préparer les certificats pour le déploiement

Procédez comme suit pour préparer et valider les certificats PKI Azure Stack Hub à utiliser pour déployer un nouvel environnement Azure Stack Hub ou opérer la rotation de secrets dans un environnement Azure Stack Hub.

### <a name="import-the-certificate"></a>Importer le certificat

1. Copiez les versions du certificat d’origine [obtenues auprès de l’autorité de certification de votre choix](azure-stack-get-pki-certs.md) dans un répertoire de l’ordinateur hôte de déploiement. 
   > [!WARNING]
   > Ne copiez pas les fichiers importés, exportés ou modifiés d’une façon ou d’une autre, à partir des fichiers fournis directement par l’autorité de certification.

1. Cliquez avec le bouton droit sur le certificat, puis sélectionnez **Installer le certificat** ou **Installer PFX**, selon la façon dont le certificat a été remis par votre autorité de certification.

1. Dans **l’Assistant Importation de certificat**, sélectionnez **Ordinateur local** en tant qu’emplacement d’importation. Sélectionnez **Suivant**. Sur l’écran suivant, sélectionnez à nouveau Suivant.

    ![Emplacement d’importation du certificat sur la machine locale](./media/prepare-pki-certs/1.png)

1. Sélectionnez **Placer tous les certificats dans le magasin suivant**, puis sélectionnez **Approbation de l’entreprise** comme emplacement. Sélectionnez **OK** pour fermer la boîte de dialogue de sélection du magasin de certificats, puis sélectionnez **Suivant**.

   ![Configurer le magasin de certificats pour l’importation de certificats](./media/prepare-pki-certs/3.png)

   a. Si vous importez un fichier PFX, une boîte de dialogue supplémentaire s’affiche. Sur la page **Protection de la clé privée**, entrez le mot de passe correspondant à vos fichiers de certificat, puis activez l’option **Marquer cette clé comme exportable. Cela vous permet de sauvegarder ou transporter vos clés ultérieurement**. Sélectionnez **Suivant**.

   ![Marquer la clé comme exportable](./media/prepare-pki-certs/2.png)

1. Sélectionnez **Terminer** pour effectuer l’importation.

> [!NOTE]
> Après importation d’un certificat pour Azure Stack Hub, la clé privée du certificat est stockée sous la forme d’un fichier PKCS 12 (PFX) sur le stockage en cluster.

### <a name="export-the-certificate"></a>Exportation du certificat

Ouvrez la console MMC du Gestionnaire de certificats et connectez-vous au magasin de certificats de l’ordinateur Local.

1. Ouvrez la console MMC (Microsoft Management Console). Pour ouvrir la console dans Windows 10, cliquez avec le bouton droit sur le **menu Démarrer**, sélectionnez **Exécuter**, tapez **mmc**, puis appuyez sur Entrée.

2. Sélectionnez **Fichier** > **Ajouter/supprimer un composant logiciel enfichable**, sélectionnez **Certificats**, puis **Ajouter**.

    ![Ajouter le composant logiciel enfichable Certificats dans la console MMC](./media/prepare-pki-certs/mmc-2.png)

3. Sélectionnez **Compte d’ordinateur**, puis **Suivant**. Sélectionnez **Ordinateur local**, puis **Terminer**. Sélectionnez **OK** pour fermer la page Ajouter/Supprimer un composant logiciel enfichable.

    ![Sélectionner un compte pour l’ajout du composant logiciel enfichable Certificats dans la console MMC](./media/prepare-pki-certs/mmc-3.png)

4. Accédez à **Certificats** > **Confiance de l’entreprise** > **Emplacement des certificats**. Vérifiez que votre certificat apparaît sur la droite.

5. Dans la barre des tâches de la console Gestionnaire de certificats, sélectionnez **Actions** > **Toutes les tâches** > **Exporter**. Sélectionnez **Suivant**.

   > [!NOTE]
   > Selon le nombre de certificats Azure Stack Hub que vous avez, il se peut que vous deviez effectuer ce processus plusieurs fois.

6. Sélectionnez **Oui, exporter la clé privée**, puis cliquez sur **Suivant**.

7. Dans la section Format du fichier d’exportation :
    
   - Sélectionnez **Inclure tous les certificats dans le certificat si possible**.  
   - Sélectionnez **Exporter toutes les propriétés étendues**.  
   - Sélectionnez **Activer la confidentialité de certificat**.  
   - Cliquez sur **Suivant**.  
    
     ![Assistant Exportation du certificat avec options sélectionnées](./media/prepare-pki-certs/azure-stack-save-cert.png)

8. Sélectionnez **Mot de passe** et indiquez un mot de passe pour les certificats. Créez un mot de passe qui répond aux exigences suivantes de complexité des mots de passe :

    * Longueur minimale de huit caractères.
    * Au moins trois des éléments suivants : lettres majuscules, lettres minuscules, chiffres de 0 à 9, caractères spéciaux, caractères alphabétiques autres que des majuscules ou des minuscules.

    Notez ce mot de passe. Vous allez l’utiliser en tant que paramètre de déploiement.

9. Sélectionnez **Suivant**.

10. Choisissez un nom de fichier et l’emplacement du fichier PFX à exporter. Sélectionnez **Suivant**.

11. Sélectionnez **Terminer**.

## <a name="next-steps"></a>Étapes suivantes

[Validate PKI certificates](azure-stack-validate-pki-certs.md) (Valider des certificats d’infrastructure à clé publique)