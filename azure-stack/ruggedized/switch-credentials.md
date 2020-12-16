---
title: Changer les informations d’identification
description: Explique comment mettre à jour les informations d’identification du commutateur pour une solution Azure Stack Hub renforcé
author: troettinger
ms.author: thoroet
ms.service: azure-stack
ms.topic: article
ms.date: 10/14/2020
ms.reviewer: justinha
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: a761544c8d514fd69364d917890d284ab06e9962
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941024"
---
# <a name="switch-credentials"></a>Changer les informations d’identification

Cette rubrique montre comment changer les informations d’identification relatives à l’administrateur (admin) et au protocole SNMP (Simple Network Management Protocol) sur tous les commutateurs. 

## <a name="prerequisites"></a>Prérequis

Avant d’exécuter les procédures :

- Utilisez le Bureau à distance pour vous connecter à l’hôte HLH.
- Recherchez PuTTY sur l’hôte HLH. Il est situé généralement à l’emplacement suivant : E:\Tools\Putty\putty.exe. Si PuTTY n’est pas disponible, téléchargez-le et copiez-le sur l’hôte HLH.
- Vérifiez que vous disposez des informations d’identification actuelles et de remplacement pour les commutateurs.

## <a name="update-credentials-for-the-admin-and-enable-accounts"></a>Mettre à jour les informations d’identification pour les comptes Admin et Enable 

Pour chaque commutateur de l’unité d’échelle (BMC, TOR1 et TOR2) :

1. Sur l’hôte HLH, à l’aide de PuTTY, connectez-vous au commutateur en utilisant les informations d’identification actuelles. 
1. Exécutez la commande suivante, en remplaçant \<new password\> par vos nouvelles informations d’identification Admin et Enable. 
   ```ini
   enable
   configuration terminal
   username admin pass
   word <new_password> privilege 15
   enable password <new_password>
   ```
1. Laissez la session active de connexion au commutateur ouverte.
1. Sur l’hôte HLH, à l’aide de PuTTY, connectez-vous au commutateur en utilisant les nouvelles informations d’identification.
1. Exécutez la commande suivante : Enable ne doit pas demander de mot de passe.

   ```ini
   enable
   write
   dir flash:
   ```

1. Vérifiez que la configuration de démarrage est à la date du jour.
1. Une fois la vérification effectuée, fermez cette session et la session d’origine.

## <a name="update-snmp-accounts"></a>Mettre à jour les comptes SNMP

Pour chaque commutateur de l’unité d’échelle (BMC, TOR1 et TOR2) :

1. Sur l’hôte HLH, à l’aide de PuTTY, connectez-vous au commutateur.
1. Exécutez la commande suivante pour connaître les utilisateurs et les groupes SNMP actuels ayant un accès en lecture/écriture :

   ```ini
   enable
   show run snmp | grep user
   ```

   L’exemple suivant montre le type de contenu retourné, notamment l’utilisateur, le nom de groupe et les codes de hachage de mot de passe :

   ```ini
   snmp-server user <user> <group> 3 encrypted auth sha <password_hash>
   ```

1. Exécutez la commande suivante en remplaçant \<user\> et \<group\> par les informations obtenues à l’étape précédente, puis en remplaçant \<password\> par votre nouveau mot de passe :

   ```ini
   configuration terminal
   snmp-server user <user> <group> 3 auth sha <password_new>
   end
   write
   exit
   ```

## <a name="next-steps"></a>Étapes suivantes

[Effectuer la rotation des secrets dans Azure Stack Hub](../operator/azure-stack-rotate-secrets.md)