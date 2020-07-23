---
title: Gestion des journaux et des données client d’Azure Stack Hub
description: Découvrez comment Azure Stack Hub collecte les données et informations client.
author: JustinHall
ms.topic: article
ms.date: 02/24/2020
ms.author: justinha
ms.reviewer: chengwei
ms.lastreviewed: 02/24/2020
ms.openlocfilehash: 881df3896d832f3f25ae3d81eda2ec904d308796
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86488992"
---
# <a name="azure-stack-hub-log-and-customer-data-handling"></a>Gestion des journaux et des données client d’Azure Stack Hub 

Dans la mesure où Microsoft traite directement ou indirectement des données personnelles en rapport avec Azure Stack Hub, depuis le 25 mai 2018, Microsoft prend les engagements suivants envers tous les clients :

- Le « traitement des données personnelles ; RGPD » dans la section « Conditions de la protection des données » des [Conditions des services en ligne](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).
- Le Règlement général sur la protection des données (RGPD) de l’Union européenne de la pièce jointe 4 des [conditions des services en ligne](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Comme Azure Stack Hub réside dans des centres de données de clients, Microsoft est l’entité de contrôle uniquement pour les données partagées avec Microsoft via les [Diagnostics](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002), la [Télémétrie](azure-stack-telemetry.md) et la [Facturation](azure-stack-usage-reporting.md).  

## <a name="data-access-controls"></a>Contrôles d’accès aux données 
Les employés de Microsoft, qui doivent examiner une demande de support spécifique, auront un accès en lecture seule aux données chiffrées. Les employés de Microsoft ont également accès aux outils utilisés pour supprimer les données si nécessaire. Tout accès aux données client est audité et journalisé.  

Contrôles d’accès aux données :
- Les données sont conservées pendant un maximum de 90 jours après la clôture du dossier.
- Le client peut choisir de faire supprimer les données à tout moment durant cette période de 90 jours.
- Les employés de Microsoft ont accès aux données au cas par cas et uniquement si nécessaire pour aider à résoudre le problème de support.
- Si Microsoft doit partager des données client avec des partenaires OEM, le consentement du client est obligatoire.  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>De quels contrôles les clients disposent-ils sur les demandes des personnes concernées ?
Microsoft prend en charge la suppression des données à la demande suite à une requête du client. Les clients peuvent demander à tout moment qu’un ingénieur du support supprime tous leurs journaux d’un dossier donné, avant que les données ne soient effacées définitivement.  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>Est-ce que Microsoft informe les clients lorsque les données sont supprimées ?
Pour l’action de suppression automatique des données (90 jours après la clôture du dossier), nous ne contactons pas proactivement les clients pour les informer de la suppression.

Pour l’action de suppression de données à la demande, les ingénieurs du support technique de Microsoft ont accès à l’outil qui leur permet de supprimer des données à la demande. Ils peuvent fournir une confirmation par téléphone au client quand l’opération est terminée.

## <a name="diagnostic-data"></a>Données de diagnostic
Dans le cadre du processus de support, les opérateurs Azure Stack Hub peuvent [partager les journaux de diagnostic](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002) avec les équipes de support et d’ingénierie d’Azure Stack Hub pour faciliter la résolution des problèmes.

Microsoft fournit un outil et un script aux clients pour collecter et charger les fichiers journaux de diagnostic demandés. Une fois collectés, les fichiers journaux sont transférés à Microsoft via une connexion HTTPS chiffrée. Comme le protocole HTTPS assure le chiffrement sur le réseau, aucun mot de passe n’est nécessaire pour le chiffrement en transit. Après réception, les journaux sont chiffrés et stockés jusqu’à ce qu’ils soient automatiquement supprimés 90 jours après la clôture de la demande de support.

## <a name="telemetry-data"></a>Données de télémétrie
La [télémétrie d’Azure Stack Hub](azure-stack-telemetry.md) charge automatiquement les données du système sur Microsoft via le service Expériences des utilisateurs connectés. Les opérateurs Azure Stack Hub peuvent personnaliser les fonctionnalités de télémétrie et les paramètres de confidentialité à tout moment.

Microsoft n’a pas l’intention de recueillir des informations sensibles, comme des numéros de cartes bancaires, des noms d’utilisateur et des mots de passe, des adresses e-mail, etc. Toute information sensible reçue par inadvertance est supprimée.

## <a name="billing-data"></a>Données de facturation
La [facturation d’Azure Stack Hub](azure-stack-usage-reporting.md) tire parti du pipeline général de facturation et d’utilisation d’Azure, et ne s’aligne donc pas sur les conseils de conformité de Microsoft.

Les opérateurs Azure Stack Hub peuvent configurer l’infrastructure pour transmettre certaines informations sur l’utilisation à Azure à des fins de facturation. Cette configuration est nécessaire pour les clients de systèmes intégrés Azure Stack Hub qui choisissent le modèle de facturation avec paiement à l’utilisation. Les rapports d’utilisation sont contrôlés indépendamment de la télémétrie et ne sont pas nécessaires pour les clients des systèmes intégrés qui choisissent le modèle de capacité ou pour les utilisateurs ASDK. Pour ces scénarios, les rapports d’utilisation peuvent être désactivés [à l’aide du script d’enregistrement](azure-stack-usage-reporting.md).


## <a name="next-steps"></a>Étapes suivantes 
[En savoir plus sur la sécurité dans Azure Stack Hub](azure-stack-security-foundations.md) 
