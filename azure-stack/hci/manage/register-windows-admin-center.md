---
title: Inscrire Windows Admin Center avec Azure
description: Comment inscrire votre passerelle Windows Admin Center auprès d’Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 01/25/2021
ms.openlocfilehash: c2067387d7b03252e7a2cc93aeddcb68dbd4bc83
ms.sourcegitcommit: 2ac64ac431411b673e655465939d3c95cc94c55d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811023"
---
# <a name="register-windows-admin-center-with-azure"></a>Inscrire Windows Admin Center avec Azure

> S’applique à Azure Stack HCI v20H2 ; Windows Server 2019

Pour utiliser les services Azure avec Windows Admin Center, vous devez d’abord installer Windows Admin Center sur un PC de gestion et procéder à une inscription à usage unique de votre passerelle Windows Admin Center. Il s’agit d’une condition préalable à l’inscription de votre cluster auprès d’Azure. Elle doit être effectuée sur le même PC de gestion que celui que vous prévoyez d’utiliser pour finaliser le processus d’[inscription du cluster](../deploy/register-with-azure.md).

## <a name="complete-the-gateway-registration-process-using-windows-admin-center"></a>Effectuer le processus d’inscription de passerelle à l’aide de Windows Admin Center

1. Lancez Windows Admin Center, puis cliquez sur l’icône d’engrenage **Paramètres** en haut à droite, ce qui vous permet d’accéder à la page de votre compte. Ensuite, dans le menu **Passerelle** sur la gauche, sélectionnez **Azure**, puis cliquez sur **Inscrire**.

   :::image type="content" source="media/register-wac/register-wac.png" alt-text="Sélectionnez Paramètres > Passerelle > Azure, puis cliquez sur S’inscrire" lightbox="media/register-wac/register-wac.png":::

2. Un code unique vous est fourni. Cliquez sur le bouton **Copier** à droite du code. Cliquez sur **Entrer le code** ; une autre fenêtre de navigateur s’ouvre dans laquelle vous pouvez coller le code affiché sur votre application ou votre appareil.

   :::image type="content" source="media/register-wac/enter-code.png" alt-text="Copiez le code unique, cliquez sur entrer le code, puis collez-le dans la boîte de dialogue" lightbox="media/register-wac/enter-code.png":::

3. Après avoir collé le code, vous êtes averti que vous êtes sur le point d’être connecté à Windows Admin Center sur un service ou un appareil distant. Entrez votre adresse e-mail ou numéro de téléphone. Si votre appareil est managé, vous êtes dirigé vers la page de connexion de votre organisation pour l’authentification. Suivez les instructions et entrez les informations d’identification appropriées.

   :::image type="content" source="media/register-wac/sign-in.png" alt-text="Connectez-vous à Windows Admin Center à l’aide de votre adresse e-mail ou numéro de téléphone" lightbox="media/register-wac/sign-in.png":::

   Vous devez normalement voir le message suivant : « Vous êtes connecté à l’application Windows Admin Center sur votre appareil ». Fermez la fenêtre du navigateur pour revenir à la page d’inscription d’origine.

4. Connectez-vous à Azure Active Directory en fournissant votre ID (de locataire) Azure Active Directory. Si vous avez suivi les étapes précédentes, le champ ID sera prérempli. Laissez l’option **Application Azure Active Directory** définie sur **Créer** si votre organisation ne vous a pas fourni d’ID existant. Si vous êtes déjà en possession d’un ID, cliquez sur **Utiliser l’existant** ; un champ vide s’affiche dans lequel vous indiquez l’ID fourni par votre administrateur. Après avoir entré votre ID, Windows Admin Center confirme qu’un compte est trouvé avec cet ID. Si vous disposez d’un ID existant mais que vous ne le connaissez pas, suivez [ces étapes](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) pour le récupérer.

   :::image type="content" source="media/register-wac/connect-to-aad.png" alt-text="Connectez-vous à Azure Active Directory en fournissant votre ID (de locataire) Azure Active Directory ou en en créant un nouveau" lightbox="media/register-wac/connect-to-aad.png":::

5. Cliquez sur le bouton **Se connecter** pour vous connecter à Azure. Une confirmation doit s’afficher vous informant que vous êtes maintenant connecté à Azure AD.

6. Accordez des autorisations dans Azure en accédant à **Autorisations d’application** dans le portail Azure. Sous **Donner son consentement**, sélectionnez **Accorder le consentement administrateur**.

7. Fermez la fenêtre et connectez-vous à Windows Admin Center avec votre compte Azure.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à :

- [Connecter Azure Stack HCI à Azure](../deploy/register-with-azure.md)
- [Utiliser Azure Stack HCI avec Windows Admin Center](../get-started.md)
- [Vous connecter aux services hybrides Azure](/windows-server/manage/windows-admin-center/azure/)