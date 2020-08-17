---
title: Résoudre les problèmes CredSSP
description: Découvrez comment résoudre des problèmes de CredSSP
author: v-dasis
ms.topic: how-to
ms.date: 08/06/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 24e9483aa9658809adc9be7fbfa4a2cb13daab84
ms.sourcegitcommit: 952d26ad08fcc28ad3ad83e27644e61497623a44
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87899925"
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

- Lors de l’exécution de l’Assistant Création d’un cluster, CredSSP peut signaler un problème si une approbation Active Directory n’est pas établie ou si elle est interrompue. Cela se produit lorsque les serveurs basés sur un groupe de travail sont utilisés pour la création du cluster. Dans ce cas, essayez de redémarrer manuellement chaque serveur du cluster.

- Quand vous exécutez Windows Admin Center sur un serveur (mode service), assurez-vous que le compte d’utilisateur est membre du groupe d’administrateurs de passerelles.

- Pour pouvoir activer ou désactiver CredSSP sur un serveur, assurez-vous que vous appartenez au groupe d’administrateurs de passerelles sur cet ordinateur. Pour plus d’informations, consultez les deux premières sections dans [Configurer le contrôle d’accès utilisateur et les autorisations](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur CredSSP, consultez [Fournisseur de support sécurité des informations d’identification](/windows/win32/secauthn/credential-security-support-provider).