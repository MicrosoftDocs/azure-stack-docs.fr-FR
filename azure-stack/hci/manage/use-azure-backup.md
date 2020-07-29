---
title: Utiliser la sauvegarde Azure pour sauvegarder des serveurs Windows
description: Cet article fournit des conseils sur l’utilisation de la sauvegarde Azure via Windows Admin Center pour sauvegarder des serveurs Windows.
author: thomasmaurer
ms.author: thmaure
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: a4668e4d78a2ea3fb9f94913863d3471313034e5
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947501"
---
# <a name="use-azure-backup-to-back-up-windows-servers"></a>Utiliser la sauvegarde Azure pour sauvegarder des serveurs Windows

>S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Windows Admin Center simplifie la sauvegarde des serveurs Windows sur Microsoft Azure. Le processus vous protège également contre les suppressions accidentelles ou malveillantes, l’endommagement et même les ransomware. Afin d’automatiser la configuration, vous connectez Windows Admin Center à Azure pour utiliser le service Sauvegarde Azure.

Cet article explique comment configurer la sauvegarde Azure, et créer une stratégie de sauvegarde pour les volumes de serveur et l’état du système Windows depuis Windows Admin Center. Ce guide est consacré à la sauvegarde sur Azure des charges de travail exécutées dans des machines virtuelles dotées du système d’exploitation Azure Stack HCI.

Pour en savoir plus sur l’intégration d’Azure à Windows Admin Center, consultez [Connexion de Windows Server aux services hybrides Azure](/windows-server/manage/windows-admin-center/azure/).

## <a name="how-azure-backup-works-with-windows-admin-center"></a>Fonctionnement de la sauvegarde Azure avec Windows Admin Center
La **sauvegarde Azure** est un service que vous pouvez utiliser pour sauvegarder, protéger et restaurer vos données dans Azure. La sauvegarde Azure remplace vos solutions de sauvegarde locale ou hors site existantes par une solution cloud à la fois fiable, sécurisée et économique.

Pour plus d’informations, consultez [Qu’est-ce que le service Sauvegarde Azure ?](/azure/backup/backup-overview)

Azure Backup propose plusieurs composants que vous pouvez télécharger et déployer sur l’ordinateur ou sur le serveur approprié, ou dans le cloud. Vous déployez un composant (ou un agent) en fonction de ce que vous souhaitez protéger. Tous les composants de la sauvegarde Azure peuvent sauvegarder des données dans un coffre Recovery Services d’Azure, que vous protégiez vos données localement ou dans Azure.

L’intégration de la sauvegarde Azure à Windows Admin Center est idéale pour sauvegarder les volumes et l’état du système Windows de serveurs virtuels ou de serveurs Windows locaux. Le processus complet sauvegarde les serveurs de fichiers, les contrôleurs de domaine et les serveurs web IIS.

Vous accédez à la sauvegarde Azure dans Windows Admin Center via l’outil **Sauvegarde**. Les fonctionnalités de configuration, gestion et supervision de l’outil **Sauvegarde** vous permettent de commencer rapidement à sauvegarder des serveurs, à effectuer des opérations de sauvegarde et de restauration, et à surveiller l’intégrité globale de la sauvegarde des serveurs Windows.

## <a name="prerequisites"></a>Prérequis
Les prérequis suivants sont nécessaires pour utiliser la sauvegarde Azure :
- Compte Azure avec au moins un abonnement actif
- Accès Internet à Azure pour les serveurs Windows cibles
- Connexion de la passerelle Windows Admin Center à Azure

    Pour en savoir plus, consultez [Configuration de l’intégration à Azure](/windows-server/manage/windows-admin-center/azure/azure-integration).

## <a name="getting-started-with-azure-backup"></a>Bien démarrer avec la sauvegarde Azure
Lorsque vous sélectionnez pour la première fois l’outil **Sauvegarde** dans Windows Admin Center en vue d’établir une connexion serveur vers Azure, la page **Bienvenue dans la sauvegarde Azure** s’affiche. Sélectionnez **Configurer la sauvegarde Azure** pour démarrer l’Assistant Configuration de la sauvegarde Azure. Les sections suivantes décrivent les étapes de l’Assistant.

Si la sauvegarde Azure est déjà configurée pour une connexion serveur, la sélection de l’outil **Sauvegarde** ouvre le **Tableau de bord de sauvegarde**. Pour plus d’informations sur les opérations et les tâches que vous pouvez effectuer à partir du tableau de bord, consultez la section [Gestion et supervision](#management-and-monitoring).

### <a name="step-1-log-on-to-the-microsoft-azure-portal"></a>Étape 1 : Se connecter au portail Microsoft Azure
Connectez-vous à votre compte Azure.

> [!NOTE]
> Si vous avez déjà connecté la passerelle Windows Admin Center à Azure, vous devez normalement vous connecter automatiquement au portail. Sélectionnez **Se déconnecter** pour, ensuite, vous connecter en tant qu’autre utilisateur.

### <a name="step-2-set-up-azure-backup"></a>Étape 2 : Configurer la sauvegarde Azure
Sélectionnez les paramètres suivants pour la sauvegarde Azure :
- **ID d’abonnement :** il s’agit de l’abonnement Azure que vous souhaitez utiliser pour sauvegarder Windows Server sur Azure. Toutes les ressources Azure, comme le groupe de ressources Azure et le coffre Recovery Services, sont créées et associées à l’abonnement sélectionné.
- **Coffre :** le coffre Recovery Services représente l’emplacement où les sauvegardes de vos serveurs seront stockées. Vous pouvez utiliser un coffre existant, sinon Windows Admin Center en créera un.  
- **Groupe de ressources :** le groupe de ressources Azure est un conteneur servant à une collection de ressources. Le coffre Recovery Services est créé ou contenu dans le groupe de ressources spécifié. Vous pouvez utiliser un groupe de ressources existant, sinon Windows Admin Center en créera un.
- **Emplacement :** il s’agit de la région Azure dans laquelle le coffre Recovery Services sera créé. Nous vous conseillons de sélectionner la région Azure la plus proche du serveur Windows que vous sauvegardez.

### <a name="step-3-select-backup-items-and-schedule"></a>Étape 3 : Sélectionner la planification et les éléments de sauvegarde
1. Sélectionnez les éléments que vous voulez sauvegarder de votre serveur. Windows Admin Center vous permet de choisir une combinaison de volumes avec l’état du système Windows, et fournit une taille estimée des données que vous avez sélectionnées pour la sauvegarde.

    > [!NOTE]
    > La première sauvegarde est une sauvegarde complète de toutes les données sélectionnées. Les sauvegardes suivantes sont incrémentielles, elles transfèrent uniquement les modifications apportées aux données qui sont survenues depuis la sauvegarde précédente.

1. Sélectionnez l’une des **Planifications de sauvegarde** prédéfinies pour les volumes et l’état du système Windows.

### <a name="step-4-enter-an-encryption-passphrase"></a>Étape 4 : Indiquer une phrase secrète de chiffrement
1. Entrez une **Phrase secrète de chiffrement** composée au minimum de 16 caractères. Avec la sauvegarde Azure, vos données de sauvegarde sont sécurisées à l’aide d’une phrase secrète de chiffrement que vous créez et gérez. La phrase secrète de chiffrement est demandée pour récupérer des données à partir de la sauvegarde Azure.

    > [!NOTE]
    > Stockez la phrase secrète dans un emplacement hors site sécurisé, tel qu’un autre serveur ou [Azure Key Vault](/azure/key-vault/quick-create-portal). Microsoft ne stocke pas la phrase secrète, et ne peut pas récupérer ni réinitialiser la phrase secrète en cas de perte ou d’oubli.

1. Passez en revue tous les paramètres de la page de l’Assistant, puis sélectionnez **Appliquer**.

Windows Admin Center effectue ensuite les opérations suivantes :
1. S’il n’en existe pas déjà un, il crée un groupe de ressources Azure.
1. Il crée un coffre Azure Recovery Services, comme indiqué.
1. Il installe et enregistre l’Agent Microsoft Azure Recovery Services dans le coffre.
1. Il crée la planification de la sauvegarde et de la rétention en fonction des options sélectionnées qui sont associées au serveur Windows.

## <a name="management-and-monitoring"></a>Gestion et supervision
Une fois la sauvegarde Azure correctement configurée, le **Tableau de bord de sauvegarde** s’affiche lorsque vous ouvrez l’outil de **Sauvegarde** pour une connexion au serveur existant. Vous pouvez effectuer les tâches suivantes depuis le tableau de bord :
- **Accéder au coffre dans Azure :** sous l’onglet **Vue d’ensemble**, sélectionnez **Coffre Recovery Services** pour accéder au coffre dans lequel vous pouvez effectuer de nombreuses opérations de gestion. Pour en savoir davantage, consultez [Superviser et gérer les coffres Recovery Services](/azure/backup/backup-azure-manage-windows-server).
- **Effectuer une sauvegarde ad hoc :** sélectionnez **Sauvegarder maintenant** pour réaliser une sauvegarde ad hoc. 
- **Superviser des travaux et configurer des notifications d’alerte :** accédez à l’onglet **Travaux** pour superviser les travaux en cours ou passés, et [configurer des notifications d’alerte](/azure/backup/backup-azure-manage-windows-server#configuring-notifications-for-alerts) afin de recevoir des e-mails en cas d’échec de travaux et d’autres alertes de sauvegarde.
- **Afficher des points de récupération et récupérer des données :** sélectionnez l’onglet **Points de récupération** pour afficher des points de récupération, et pour sélectionner **Récupérer des données** afin de suivre les étapes de récupération de vos données à partir d’Azure.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations, consultez également :
- [Protéger les machines virtuelles Azure Stack HCI à l’aide d’Azure Site Recovery](./azure-site-recovery.md)
