---
title: Changer de langue dans Azure Stack HCI
description: Cette rubrique fournit des conseils sur la façon de changer de langue dans le système d’exploitation Azure Stack HCI, Windows 10, Windows Admin Center et Microsoft Edge.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: 09245e4498e87896b95df993e23693ae1ed23eaf
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947331"
---
# <a name="change-languages-in-azure-stack-hci"></a>Changer de langue dans Azure Stack HCI

>S’applique à : Azure Stack HCI, version 20H2 ; Windows Server 2019

Vous pouvez changer votre langue par défaut dans un PC de gestion, Windows Admin Center, Microsoft Edge et le système d’exploitation Azure Stack HCI sur les serveurs que vous gérez. Windows Admin Center conserve la langue de votre choix, indépendamment des changements de langue apportés dans ces autres ressources.

Cet article explique comment changer de langue dans :
- Windows 10
- Windows Admin Center
- Microsoft Edge
- Server Core dans le système d’exploitation Azure Stack HCI

## <a name="change-the-language-in-the-management-pc"></a>Modifier la langue dans le PC de gestion
Si votre PC de gestion est sur Windows 10, vous pouvez installer un module linguistique à utiliser dans Windows 10, dans les applications dont vous vous servez et dans les sites web que vous visitez. Vous pouvez également changer la langue de votre clavier et définir la langue d’entrée dans un ordre de préférence linguistique pour les sites web et les applications.

Le changement de langue que vous effectuez dans Windows 10 ou dans la langue du clavier n’a pas d’incidence sur la langue d’affichage dans Windows Admin Center.

   >[!IMPORTANT]
   > Après avoir modifié votre langue dans Windows 10, nous vous recommandons de définir la langue par défaut de votre clavier avant de vous déconnecter, pour éviter d’éventuels problèmes d’entrée au clavier.

Pour plus d’informations, consultez [Gérer les paramètres de langue d’entrée et d’affichage dans Windows 10](https://support.microsoft.com/help/4496404/windows-10-manage-the-input-and-display-language).

## <a name="change-the-language-in-windows-admin-center"></a>Changer la langue dans Windows Admin Center
Vous pouvez changer la langue dans Windows Admin Center et le format régional en fonction de l’endroit où vous vous trouvez. Le changement de l’une de ces options dans Windows Admin Center n’a aucun effet sur le paramètre de langue du PC de gestion exécutant Windows 10.

Pour changer la langue dans Windows Admin Center :
1. Dans le volet **Toutes les connexions**, sélectionnez l’icône d’engrenage **Paramètres**, puis, sous **Paramètres**, sélectionnez **Langue / Région**.
1. Développez la liste déroulante **Langue** pour sélectionner votre langue préférée, puis développez la liste déroulante **Format régional** pour sélectionner une autre région, si nécessaire.
1. Sélectionnez **Enregistrer et recharger**.

    :::image type="content" source="media/languages/language-region.png" alt-text="Page Langue / Région dans Windows Admin Center":::

Pour en savoir plus, consultez [Paramètres de Windows Admin Center](/windows-server/manage/windows-admin-center/configure/settings).

## <a name="change-the-language-in-microsoft-edge"></a>Changer la langue dans Microsoft Edge
Vous pouvez ajouter des langues prises en charge dans Microsoft Edge et réorganiser vos préférences linguistiques dans le navigateur. Vous pouvez également ajouter une extension de traducteur de langue étrangère au navigateur pour obtenir des traductions.

Pour plus d’informations, consultez [Prise en charge linguistique dans Microsoft Edge](/deployedge/microsoft-edge-supported-languages).

## <a name="change-the-language-in-server-core"></a>Changer la langue dans Server Core
Si vous avez besoin de changer la langue dans la partie Server Core du système d’exploitation Azure Stack HCI, nous vous recommandons de le faire après avoir clusterisé vos serveurs. Vous pouvez ajouter des modules linguistiques pris en charge dans Server Core, puis remplacer la langue et la disposition du clavier par celles que vous souhaitez utiliser. Vous pouvez également utiliser une commande Windows PowerShell pour remplacer la langue actuelle et la méthode d’entrée au clavier.

Chaque module linguistique est installé dans le répertoire *%SystemRoot%\System32\\% Language-ID%* . Par exemple, *C:\Windows\System32\es-ES* correspond à l’emplacement du module linguistique espagnol. La taille de chaque module linguistique est d’environ 50 Mo. Si vous souhaitez installer la totalité des 38 modules linguistiques, la taille nécessaire de l’image que vous allez créer est d’environ 2 Go.

Pour plus d’informations, consultez [Langues disponibles pour Windows](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

Pour manuellement obtenir et ajouter des modules linguistiques au système d’exploitation :

1. Ajoutez un module linguistique à Server Core à l’aide de l’outil **DISM / Add-WindowsPackage**. La commande PowerShell `Add-WindowsPackage` est l’équivalent de l’exécutable DISM.

    Pour approfondir le sujet, consultez [Ajouter des langues aux images Windows](/windows-hardware/manufacture/desktop/add-language-packs-to-windows).

1. Nous vous conseillons d’ajouter des fonctionnalités de langue à la demande, afin d’activer des fonctionnalités supplémentaires pour la langue que vous avez ajoutée, comme dans l’exemple suivant :

    ```DOS
    dism /online /add-capability /capabilityname:Language.Basic~~~de-de~0.0.1.0
    ```

    Pour plus d’informations, consultez [Fonctionnalités de langue et de région à la demande](/windows-hardware/manufacture/desktop/features-on-demand-language-fod).

1. Vous pouvez utiliser l’applet de commande PowerShell **Set-WinUILanguageOverride** pour changer la langue de l’interface utilisateur Windows dans le système d’exploitation du compte d’utilisateur actif. Dans l’exemple suivant, `de-DE` spécifie l’allemand comme paramètre de langue en cours dans le système d’exploitation :

    ```PowerShell
    Set-WinUILanguageOverride de-DE
    ```

    Pour plus d’informations, consultez [Set-WinUILanguageOverride](/powershell/module/international/set-winuilanguageoverride?view=win10-ps).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations, consultez également :

- [Ajouter ou supprimer des serveurs pour un cluster Azure Stack HCI](./add-cluster.md)
