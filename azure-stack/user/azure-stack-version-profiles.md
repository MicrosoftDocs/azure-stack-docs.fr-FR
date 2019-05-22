---
title: Gérer des profils de version des API dans Azure Stack | Microsoft Docs
description: En savoir plus sur les profils de version des API dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 1557403536764f8aa88a271d183436a3d35954ae
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782473"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Gérer les profils de version des API dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Les profils d’API précisent le fournisseur de ressources Azure ainsi que la version d’API des points de terminaison Azure REST. Vous pouvez créer des clients personnalisés dans différents langages à l’aide des profils d’API. Chaque client utilise un profil d’API pour contacter le fournisseur de ressources et la version d’API appropriés pour Azure Stack.

Vous pouvez créer une application qui fonctionne avec les fournisseurs de ressources Azure sans savoir exactement quelle version des API de fournisseur de ressources est compatible avec Azure Stack. Il vous suffit d’aligner votre application sur un profil. Le kit de développement logiciel (SDK) rétablit la version d’API appropriée.

Cette rubrique vous aide à :

 - comprendre les profils d’API pour Azure Stack,
 - comprendre l’utilisation des profils d’API pour développer vos solutions,
 - obtenir des conseils spécifiques au code.

## <a name="summary-of-api-profiles"></a>Résumé des profils d’API

- Les profils d’API représentent un ensemble de fournisseurs de ressources Azure et leurs versions d’API.
- Les profils d’API ont été créés pour que vous puissiez générer des modèles utilisables sur plusieurs clouds Azure. Les profils fournissent une interface compatible et stable.
- La mise en production des profils intervient quatre fois par an.
- Trois conventions d’appellation des profils sont utilisées :
    - **le plus récent**  
        Contient les versions d’API les plus récentes publiées dans Azure global.
    - **yyyy-mm-dd-hybrid**  
    Version publiée deux fois par an, elle se concentre sur la cohérence et la stabilité d’un cloud à l’autre. Ce profil vise à assurer une compatibilité optimale avec Azure Stack.
    - **yyyy-mm-dd-profile** Équilibre entre une stabilité optimale et les fonctionnalités les plus récentes.

## <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Profils d’API Azure et compatibilité avec Azure Stack

Les profils d’API Azure les plus récents ne sont pas compatibles avec Azure Stack. Vous pouvez utiliser les conventions d’affectation de noms suivantes pour identifier les profils à utiliser pour vos solutions Azure Stack.

**La plus récente**  
Ce profil comprend les versions d’API les plus à jour proposées dans Azure global. Ces versions ne sont pas compatibles avec Azure Stack. Le profil **La plus récente** présente le plus grand nombre de changements importants. Ce profil ne vise pas la stabilité et la compatibilité avec les autres clouds. Si vous essayez d’utiliser les versions d’API les plus à jour, **La plus récente** est le profil que vous devez utiliser.

**Yyyy-mm-dd-hybrid**  
Ce profil est publié deux fois par an, en mars et en septembre. Il garantit une stabilité et une compatibilité optimales avec les autres clouds. Le profil **Yyyy-mm-dd-hybrid** est conçu pour cibler les environnements Azure global et Azure Stack. Les versions d’API Azure répertoriées dans ce profil sont les mêmes que celles disponibles dans Azure Stack. Utilisez ce profil si vous souhaitez développer des solutions cloud hybrides.

**yyyy-mm-dd-profile**  
Ce profil pour Azure global est publié deux fois par an, en juin et en décembre. Il ne fonctionne pas avec Azure Stack et présente généralement de nombreux changements cassants. Même s’il offre un équilibre entre une stabilité optimale et les fonctionnalités les plus récentes, il n’est pas identique au profil **La plus récente**. La différence entre les deux est que le profil **La plus récente** propose toujours les toutes dernières versions des API, quelle que soit la date de publication des API. Par exemple, si une nouvelle version de l’API Compute est créée demain, cette version est aussitôt proposée dans le profil **La plus récente**, mais pas dans le profil **yyyy-mm-dd-profile**, car ce profil existe déjà. Le profil **yyyy-mm-dd-profile** fournit les dernières versions mises à jour ayant été publiées avant juin ou avant décembre.

## <a name="azure-resource-manager-api-profiles"></a>Profils d’API Azure Resource Manager

Azure Stack n’utilise pas les versions d’API les plus récentes disponibles dans Azure global. Quand vous créez une solution, vous devez rechercher la version d’API pour chaque fournisseur de ressources Azure qui est compatible avec Azure Stack.

Plutôt que d’effectuer des recherches sur chaque fournisseur de ressources et sur chacune des versions spécifiques prises en charge par Azure Stack, vous pouvez utiliser un profil d’API. Le profil spécifie un ensemble de fournisseurs de ressources et de versions d’API. Le SDK, ou un outil intégré au SDK, vous oriente vers le paramètre `api-version` cible indiquée dans le profil. Avec les profils d’API, vous pouvez spécifier une version de profil qui s’applique à tout un modèle. Puis, au moment de l’exécution, Azure Resource Manager sélectionne la version appropriée de la ressource.

Les profils d’API fonctionnent avec des outils qui utilisent Azure Resource Manager, par exemple PowerShell, Azure CLI, le code fourni dans le SDK et Microsoft Visual Studio. Les outils et les SDK peuvent utiliser des profils pour lire la version des modules et des bibliothèques à inclure au moment de créer une application.

Par exemple, si vous utilisez PowerShell pour créer un compte de stockage à l’aide du fournisseur de ressources **Microsoft.Storage**, qui prend en charge le paramètre **api-version** 2016-03-30, et une machine virtuelle utilisant le fournisseur de ressources **Microsoft.Compute** avec le paramètre **api-version** 2015-12-01, vous devez rechercher quel module PowerShell prend en charge 2016-03-30 pour Storage et quel module prend en charge 2015-02-01 pour Compute, puis les installer. Pour vous éviter de procéder ainsi, vous pouvez utiliser un profil. Utilisez l’applet de commande `Install-Profile <profilename>` pour que PowerShell charge la bonne version des modules.

De même, lorsque vous utilisez le SDK Python pour générer une application basée sur Python, vous pouvez spécifier le profil. Le SDK charge les modules correspondant aux fournisseurs de ressources que vous avez spécifiés dans votre script.

En tant que développeur, vous pouvez ainsi vous concentrer sur l’écriture de votre solution. Au lieu de rechercher les versions d’API, le fournisseur de ressources et le cloud qui fonctionnent ensemble, vous pouvez utiliser un profil pour avoir l’assurance que votre code fonctionne dans tous les clouds qui prennent en charge ce profil.

## <a name="api-profile-code-samples"></a>Exemples de code de profil d’API

Il existe des exemples de code pour vous aider à intégrer votre solution et votre langage par défaut à Azure Stack à l’aide des profils. Actuellement, vous pouvez trouver des conseils et des exemples pour les langages suivants :

- **.NET** Vous pouvez utiliser le profil d’API .NET pour obtenir la version la plus récente et la plus stable de chaque type de ressource dans un package de fournisseur de ressources. Pour plus d’informations, consultez l’article [Utiliser des profils de version d’API avec .NET dans Azure Stack](azure-stack-version-profiles-net.md).
- **PowerShell**  
Le module **AzureRM.Bootstrapper**, disponible via PowerShell Gallery, fournit les cmdlets PowerShell nécessaires pour utiliser des profils de version d’API. Pour plus d’informations, consultez [Utiliser des profils de version d’API pour PowerShell](azure-stack-version-profiles-powershell.md).
- **Interface de ligne de commande Azure**  
Vous pouvez mettre à jour la configuration de votre environnement pour utiliser le profil de version d’API spécifique à Azure Stack. Pour plus d’informations, consultez [Utiliser les profils de version d’API pour Azure CLI](azure-stack-version-profiles-azurecli2.md).
- **Go**  
Dans le SDK Go, un profil est une combinaison de différents types de ressources dans différentes versions de différents services. Les profils sont disponibles sous les profils/chemin avec leur version au format **YYYY-MM-DD**. Pour plus d’informations, consultez [Utiliser des profils de version d’API pour Go](azure-stack-version-profiles-go.md).
- **Ruby**  
Le Kit de développement logiciel (SDK) Ruby pour Azure Stack Resource Manager fournit des outils pour vous aider à créer et gérer votre infrastructure. Les fournisseurs de ressources du SDK incluent le calcul, les réseaux virtuels et le stockage avec le langage Ruby. Pour plus d’informations, consultez [Utiliser des profils de version d’API avec Ruby](azure-stack-version-profiles-ruby.md)
- **Python**  
Le Kit de développement logiciel (SDK) Python prend en charge les profils de version d’API pour cibler différentes plateformes de cloud telles qu’Azure Stack et Azure globale. Vous pouvez utiliser des profils d’API visant à créer des solutions pour un cloud hybride. Pour plus d’informations, consultez [Utiliser des profils de version d’API avec Python](azure-stack-version-profiles-python.md)

## <a name="next-steps"></a>Étapes suivantes

* [Installer PowerShell pour Azure Stack](../operator/azure-stack-powershell-install.md)
* [Configurez l’environnement PowerShell de l’utilisateur Azure Stack.](azure-stack-powershell-configure-user.md)
* [Passer en revue les détails sur les versions d’API du fournisseur de ressources prises en charge par les profils](azure-stack-profiles-azure-resource-manager-versions.md).
