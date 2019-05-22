---
title: Gestion des données et des journaux Azure Stack | Microsoft Docs
description: Découvrez comment Azure Stack collecte des informations.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2019
ms.author: PatAltimore
ms.reviewer: chengwei
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: e0b62c9f9519e1b77211327c4f9b48d4b53cf99a
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985870"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>Gestion des données client et des journaux Azure Stack 
*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*  

Dans la mesure où Microsoft est un processeur ou un sous-processeur de données personnelles dans le cadre d’Azure Stack, Microsoft s’engage envers tous les clients, à partir du 25 mai 2018, à appliquer les engagements (a) dans le « Traitement des données personnelles, RGPD » de la section « Conditions de protection des données » des[Conditions d’utilisation des services en ligne](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551078818&sdata=cpWsfZTBHpqEFr50DWQOryq342U8shgeFgMXVPQz5ug%3D&reserved=0) et (b) dans les conditions générales du Règlement Général sur la Protection des données de l’Union européenne de la pièce jointe 4 des [Conditions d’utilisation des services en ligne](https://nam06.safelinks.protection.outlook.com/?url=http%3A%2F%2Fwww.microsoftvolumelicensing.com%2FDocumentSearch.aspx%3FMode%3D3%26DocumentTypeId%3D31&data=02%7C01%7Ccomartin%40microsoft.com%7Ce2ce478261764c79c3f308d68df01136%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636852459551088813&sdata=bv1CBiaCnYmjiv6S0dFCbWEd4fNCkPBjBwgylNa%2FNt0%3D&reserved=0). 

Comme Azure Stack réside dans les centres de données client, Microsoft est le Responsable de fichier uniquement pour les données qui sont partagées avec Microsoft via les [Diagnostics](azure-stack-diagnostics.md), la [Télémétrie](azure-stack-telemetry.md) et la [Facturation](azure-stack-usage-reporting.md).  

## <a name="data-access-controls"></a>Contrôles d’accès aux données 
Les employés de Microsoft, qui doivent examiner une demande de support spécifique, auront un accès en lecture seule aux données chiffrées. Les employés de Microsoft ont également accès aux outils qui peuvent être utilisés pour supprimer les données si nécessaire. Tout accès aux données client est audité et journalisé.  

Contrôles d’accès aux données :
1.  Les données ne sont conservées que pendant 90 jours maximum après la clôture du dossier.
2.  Le client peut choisir de faire supprimer les données à tout moment durant cette période de 90 jours.
3.  Les employés de Microsoft ont accès aux données au cas par cas et uniquement si nécessaire pour aider à résoudre le problème de support. 
4.  Si Microsoft doit partager des données client avec des partenaires OEM, le consentement du client est obligatoire.  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>De quels contrôles les clients disposent-ils sur les demandes des personnes concernées ?
Comme mentionné précédemment, Microsoft prend en charge la suppression des données sur demande suite à une requête du client. Les clients peuvent demander à notre ingénieur du support de supprimer tous leurs journaux d’activité pour un dossier donné, au moment de leur choix, avant que les données ne soient effacées définitivement.  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>Est-ce que Microsoft informe les clients lorsque les données sont supprimées ?
Pour l’action de suppression automatique des données (90 jours après la clôture du dossier), nous ne contactons pas de manière proactive les clients pour les informer de la suppression. 

Pour l’action de suppression des données sur demande, l’ingénieur du support Microsoft a accès à l’outil qui lui permet d’initier la suppression des données sur demande, et il peut fournir une confirmation par téléphone au client une fois la suppression effectuée.

## <a name="diagnostic-data"></a>Données de diagnostic
Dans le cadre du processus de support, les opérateurs Azure Stack peuvent [partager les journaux de diagnostic](azure-stack-diagnostics.md) avec les équipes de support et d’ingénierie Azure Stack pour faciliter la résolution des problèmes.

Microsoft fournit un outil et un script aux clients pour collecter et charger les fichiers journaux de diagnostic demandés. Une fois collectés, les fichiers journaux sont transférés à Microsoft via une connexion HTTPS chiffrée. Comme le protocole HTTPS assure le chiffrement sur le réseau, aucun mot de passe n’est nécessaire pour le chiffrement en transit. Après leur réception, les fichiers journaux d’activité sont chiffrés et stockés jusqu’à ce qu’ils soient automatiquement supprimés 90 jours après la clôture de la demande de support.

## <a name="telemetry-data"></a>Données de télémétrie
La [télémétrie Azure Stack](azure-stack-telemetry.md) charge automatiquement les données du système sur Microsoft via Expériences des utilisateurs connectés. Les opérateurs Azure Stack peuvent personnaliser les fonctionnalités de télémétrie et les paramètres de confidentialité à tout moment.

Microsoft n’entend pas recueillir de données sensibles, telles que des numéros de cartes bancaires, des noms d’utilisateurs et des mots de passe, des adresses de messagerie ou tout autre information sensible. Toute information sensible reçue par inadvertance est supprimée. 

## <a name="billing-data"></a>Données de facturation
La [facturation Azure Stack](azure-stack-usage-reporting.md) tire parti du pipeline général de facturation et d’utilisation d’Azure, et ne s’aligne donc pas sur les conseils de conformité de Microsoft.

Les opérateurs Azure Stack peuvent configurer Azure Stack pour transmettre certaines informations sur l’utilisation à Azure à des fins de facturation. Cela est requis pour les clients Azure Stack à plusieurs nœuds qui choisissent une facturation du modèle de paiement à l’utilisation. Les rapports d’utilisation sont contrôlés indépendamment des données de télémétrie et ne sont pas requis pour les clients à plusieurs nœuds qui choisissent le modèle de capacité ou pour les utilisateurs du Kit de développement Azure Stack. Pour ces scénarios, les rapports d’utilisation peuvent être désactivés [à l’aide du script d’enregistrement](azure-stack-usage-reporting.md).


## <a name="next-steps"></a>Étapes suivantes 
[Apprenez-en davantage sur la sécurité dans Azure Stack](azure-stack-security-foundations.md) 
