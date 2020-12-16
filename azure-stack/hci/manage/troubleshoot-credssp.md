---
title: Résoudre les problèmes CredSSP
description: Découvrez comment résoudre des problèmes de CredSSP
author: v-dasis
ms.topic: how-to
ms.date: 12/10/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: f90e03c275c4ca7a28a9d8392351bf55d0adb2c0
ms.sourcegitcommit: 97ecba06aeabf2f30de240ac283b9bb2d49d62f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97010835"
---
# <a name="troubleshoot-credssp"></a>Résoudre les problèmes CredSSP

> S’applique à Azure Stack HCI, version v20H2

Certaines opérations Azure Stack HCI utilisent Windows Remote Management (WinRM), qui n’autorise pas la délégation des informations d’identification par défaut. Pour autoriser la délégation, l’ordinateur doit avoir le protocole CredSSP (Credential Security Support Provider) temporairement activé. CredSSP est un fournisseur de support de sécurité qui permet à un client de déléguer des informations d’identification à un serveur cible pour une authentification à distance. 

L’activation de CredSSP est une posture de sécurité détériorée et, dans la plupart des cas, elle doit être désactivée une fois la tâche ou l’opération terminée.

Certaines tâches qui requièrent l’activation de CredSSP sont les suivantes :

- workflow Assistant Création d’un cluster
- requêtes ou mises à jour Active Directory
- requêtes ou mises à jour SQL Server
- Emplacement de comptes ou d’ordinateurs sur un autre domaine ou dans un environnement non joint à un domaine

## <a name="troubleshooting-tips"></a>Conseils de dépannage

Si vous rencontrez des problèmes avec CredSSP, les conseils de résolution des problèmes suivants peuvent vous aider :

- Pour utiliser l’Assistant Création d’un cluster quand vous exécutez Windows Admin Center sur un serveur plutôt que sur un ordinateur local, vous devez être membre du groupe d’administrateurs de passerelles sur le serveur Windows Admin Center. Pour plus d’informations, consultez [Options d’accès utilisateur avec Windows Admin Center](/windows-server/manage/windows-admin-center/plan/user-access-options).

- Lors de l’exécution de l’Assistant Création d’un cluster, CredSSP peut signaler un problème si une approbation Active Directory n’est pas établie ou si elle est interrompue. Cela se produit lorsque les serveurs basés sur un groupe de travail sont utilisés pour la création du cluster. Dans ce cas, essayez de redémarrer manuellement chaque serveur du cluster.

- Quand vous exécutez Windows Admin Center sur un serveur (mode service), assurez-vous que le compte d’utilisateur est membre du groupe d’administrateurs de passerelles.

- Nous vous recommandons d’exécuter Windows Admin Center sur un ordinateur qui est membre du même domaine que les serveurs gérés.

- Pour pouvoir activer ou désactiver CredSSP sur un serveur, assurez-vous que vous appartenez au groupe d’administrateurs de passerelles sur cet ordinateur. Pour plus d’informations, consultez les deux premières sections dans [Configurer le contrôle d’accès utilisateur et les autorisations](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions).

- Le redémarrage du service Windows Remote Management (WinRM) sur les serveurs du cluster peut vous inviter à rétablir la connexion WinRM entre chaque serveur de cluster et Windows Admin Center.

    Pour ce faire, vous pouvez accéder à chaque serveur de cluster et, dans le menu **Outils** de Windows Admin Center, sélectionnez **Services**, **WinRM** et **Redémarrer**, puis dans l’invite **Redémarrer le service**, sélectionnez **Oui**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur CredSSP, consultez [Fournisseur de support sécurité des informations d’identification](/windows/win32/secauthn/credential-security-support-provider).