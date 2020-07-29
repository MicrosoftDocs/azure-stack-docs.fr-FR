---
title: Inscrire Windows Admin Center avec Azure
description: Comment inscrire Windows Admin Center auprès d’Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: ee6794c0798bc388bc3d9313665eb0b7917cf8eb
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86867972"
---
# <a name="register-windows-admin-center-with-azure"></a>Inscrire Windows Admin Center avec Azure

> S’applique à Azure Stack HCI v20H2 ; Windows Server 2019

Pour utiliser les services Azure avec Windows Admin Center, vous devez d’abord installer Windows Admin Center sur un PC de gestion et procéder à une inscription à usage unique.

## <a name="complete-the-registration-process-in-windows-admin-center"></a>Effectuer le processus d’inscription dans Windows Admin Center

1. Lancez Windows Admin Center, puis cliquez sur l’icône d’engrenage **Paramètres** en haut à droite, ce qui vous permet d’accéder à la page de votre compte. Ensuite, dans le menu **Passerelle** sur la gauche, sélectionnez **Azure**, puis cliquez sur **Inscrire**.
1. Un code unique vous est fourni. Cliquez sur le bouton **Copier** à droite du code.
1. Cliquez sur **Entrer le code** ; une autre fenêtre de navigateur s’ouvre dans laquelle vous pouvez coller le code affiché sur votre application ou votre appareil.
1. Après avoir collé le code, vous êtes averti que vous êtes sur le point d’être connecté à Windows Admin Center sur un service ou un appareil distant. 
1. Entrez votre adresse e-mail ou numéro de téléphone. Si votre appareil est managé, vous êtes dirigé vers la page de connexion de votre organisation pour l’authentification. Suivez les instructions et entrez les informations d’identification appropriées.
1. Vous devez normalement voir le message suivant : « Vous êtes connecté à l’application Windows Admin Center sur votre appareil ». Fermez la fenêtre du navigateur pour revenir à la page d’inscription d’origine.
1. Connectez-vous à Azure Active Directory en fournissant votre ID (de locataire) Azure Active Directory. Si vous avez suivi les étapes précédentes, le champ ID sera prérempli. Laissez l’option **Application Azure Active Directory** définie sur **Créer** si votre organisation ne vous a pas fourni d’ID existant. Si vous êtes déjà en possession d’un ID, cliquez sur **Utiliser l’existant** ; un champ vide s’affiche dans lequel vous indiquez l’ID fourni par votre administrateur. Après avoir entré votre ID, Windows Admin Center confirme qu’un compte est trouvé avec cet ID. Si vous disposez d’un ID existant mais que vous ne le connaissez pas, suivez [ces étapes](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) pour le récupérer.
1. Cliquez sur le bouton **Se connecter** pour vous connecter à Azure. Une confirmation doit s’afficher vous informant que vous êtes maintenant connecté à Azure AD.
1. Accordez des autorisations dans Azure en accédant à **Autorisations d’application** dans le portail Azure. Sous **Donner son consentement**, sélectionnez **Accorder le consentement administrateur**.
1. Fermez la fenêtre et connectez-vous à Windows Admin Center avec votre compte Azure.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à :

- [Utiliser Azure Stack HCI avec Windows Admin Center](../get-started.md)
- [Vous connecter aux services hybrides Azure](/windows-server/manage/windows-admin-center/azure/)