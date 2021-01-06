---
title: Résoudre les problèmes CredSSP
description: Découvrez comment résoudre des problèmes de CredSSP
author: v-dasis
ms.topic: how-to
ms.date: 12/14/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 472f0cf6103ac1559a9f8a0f757d66bc545f9a5d
ms.sourcegitcommit: f4a1a7e9d0b64ca84105d48170a23e1f473e976c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97743537"
---
# <a name="troubleshoot-credssp"></a>Résoudre les problèmes CredSSP

> S’applique à Azure Stack HCI, version v20H2

Certaines opérations Azure Stack HCI utilisent Windows Remote Management (WinRM), qui n’autorise pas la délégation des informations d’identification par défaut. Pour autoriser la délégation, l’ordinateur doit avoir le protocole CredSSP (Credential Security Support Provider) temporairement activé. CredSSP est un fournisseur de support de sécurité qui permet au client de déléguer des informations d’identification à un serveur pour une authentification à distance.

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

- Quand vous exécutez Windows Admin Center sur un serveur, assurez-vous que le compte d’utilisateur est membre du groupe d’administrateurs de passerelles.

- Nous vous recommandons d’exécuter Windows Admin Center sur un ordinateur qui est membre du même domaine que les serveurs gérés.

- Pour pouvoir activer ou désactiver CredSSP sur un serveur, assurez-vous que vous appartenez au groupe d’administrateurs de passerelles sur cet ordinateur. Pour plus d’informations, consultez les deux premières sections dans [Configurer le contrôle d’accès utilisateur et les autorisations](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions).

- Le redémarrage du service WinRM sur les serveurs du cluster peut vous inviter à rétablir la connexion WinRM entre chaque serveur de cluster et Windows Admin Center.

    Pour ce faire, vous pouvez accéder à chaque serveur de cluster et, dans le menu **Outils** de Windows Admin Center, sélectionnez **Services**, **WinRM** et **Redémarrer**, puis dans l’invite **Redémarrer le service**, sélectionnez **Oui**.

## <a name="manual-troubleshooting"></a>Dépannage manuel

Si vous recevez le message d’erreur WinRM suivant, essayez d’effectuer les étapes de vérification manuelle décrites dans cette section pour résoudre l’erreur. Exemple de message d’erreur :

`Connecting to remote <sever name> failed with the following error message: The WinRM client cannot process the request. A computer policy does not allow the delegation of the user credentials to the target computer because the computer is not trusted. The identity of the target computer can be verified if you configure the WSMAN service to use a valid certificate.`

Les étapes de vérification manuelle de cette section nécessitent la configuration des ordinateurs suivants :
- L’ordinateur exécutant Windows Admin Center
- Le serveur sur lequel vous avez reçu le message d’erreur

Pour résoudre l’erreur, essayez les étapes des solutions suivantes selon votre cas :

**Solution 1 :**
1. Redémarrez l’ordinateur qui exécute Windows Admin Center, ainsi que le serveur.
1. Réessayez d’exécuter l’Assistant Création d’un cluster.

    Pour des détails sur l’exécution de l’Assistant, consultez [Créer un cluster Azure Stack HCI en utilisant Windows Admin Center](../deploy/create-cluster.md).

**Solution 2 :**
1. Sur l’ordinateur exécutant Windows Admin Center, ouvrez Windows PowerShell en tant qu’administrateur et exécutez les commandes suivantes :

    ```powershell
    Disable-WsmanCredSSP -Role Client  
    ```

    ```powershell  
    Enable-WsmanCredSSP -Role Client -DelagateComputer <Server FQDN Name>
    ```

1. Utilisez la fonctionnalité RDP pour vous connecter au serveur, puis exécutez les commandes PowerShell suivantes :

    ```powershell  
    Disable-WsmanCredSSP -Role Server  
    ```

    ```powershell  
    Enable-WsmanCredSSP -Role Server  
    ```
    
1. Réessayez d’exécuter l’Assistant Création d’un cluster.

    Pour des détails sur l’exécution de l’Assistant, consultez [Créer un cluster Azure Stack HCI en utilisant Windows Admin Center](../deploy/create-cluster.md).

**Solution 3 :**
1. Sur l’ordinateur exécutant Windows Admin Center, exécutez la commande PowerShell suivante pour vérifier le nom du principal de service (SPN) :

    ```powershell
    setspn -Q WSMAN/<Windows Admin Center Computer Name>  
    ```
    
    Le résultat doit normalement retourner la sortie suivante :

    `WSMAN/<Windows Admin Center Computer Name>`

    `WSMAN/<Windows Admin Center Computer FQDN Name>`

1. Si ce n’est pas le cas, exécutez les commandes PowerShell suivantes pour inscrire le SPN :

    ```powershell
    setspn -S WSMAN/<Windows Admin Center Computer Name> <Windows Admin Center Computer Name>  
    ```

    ```powershell
    setspn -S WSMAN/<Windows Admin Center Computer Name> <Windows Admin Center Computer FQDN Name>  
    ```

1. Utilisez la fonctionnalité RDP pour vous connecter au serveur, puis exécutez la commande PowerShell suivante pour vérifier le SPN :

    ```powershell
    setspn -Q WSMAN/<Server Name>  
    ```

    Le résultat doit normalement retourner la sortie suivante :

    `WSMAN/<Server Name>`

    `WSMAN/<Server FQDN Name>`

1. Si ce n’est pas le cas, exécutez les commandes PowerShell suivantes pour inscrire le SPN :

    ```powershell
    setspn -S WSMAN/<Server Name> <Server Name>  
    ```

    ```powershell
    setspn -S WSMAN/<Server Name> <Server FQDN Name>  
    ```

1. Réessayez d’exécuter l’Assistant Création d’un cluster.

    Pour des détails sur l’exécution de l’Assistant, consultez [Créer un cluster Azure Stack HCI en utilisant Windows Admin Center](../deploy/create-cluster.md).


**Solution 4 :**

Si les étapes des solutions précédentes ont échoué ou n’ont pas abouti, cela peut indiquer un conflit d’enregistrement dans Active Directory. Vous pouvez utiliser un autre nom d’ordinateur pour réinitialiser l’enregistrement en tant que nouvel enregistrement dans Active Directory.

Pour réinitialiser l’enregistrement dans Active Directory, réinstallez le système d’exploitation Azure Stack HCI en utilisant un nouveau nom d’ordinateur.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur CredSSP, consultez [Fournisseur de support sécurité des informations d’identification](/windows/win32/secauthn/credential-security-support-provider).