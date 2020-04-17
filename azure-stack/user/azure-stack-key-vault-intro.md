---
title: Introduction à Key Vault dans Azure Stack Hub
description: Découvrez comment Key Vault gère les clés et les secrets dans Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 01/24/2020
ms.author: sethm
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 09db1c2e8daa34b566512ca6daee2480a86527df
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "77704044"
---
# <a name="introduction-to-key-vault-in-azure-stack-hub"></a>Introduction à Key Vault dans Azure Stack Hub

## <a name="prerequisites"></a>Prérequis

* S’abonner à une offre qui inclut le service Azure Key Vault.  
* PowerShell est installé et [configuré pour une utilisation avec Azure Stack Hub](azure-stack-powershell-configure-user.md).

## <a name="key-vault-basics"></a>Principes fondamentaux de Key Vault

Key Vault dans Azure Stack Hub permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Key Vault vous permet de chiffrer des clés et des secrets, par exemple :

* Clés d’authentification
* Clés de compte de stockage
* Clés de chiffrement des données
* Fichiers .pfx
* Mots de passe

Key Vault rationalise le processus de gestion de clés et vous permet de garder le contrôle des clés qui accèdent à vos données et les chiffrent. Les développeurs peuvent créer des clés pour le développement et le test en quelques minutes, puis les migrer en toute transparence en clés de production. Les administrateurs de sécurité peuvent accorder (et annuler) les autorisations sur les clés, si nécessaire.

Toute personne disposant d’un abonnement Azure Stack Hub peut créer et utiliser des coffres de clés. Bien que Key Vault procure des avantages aux développeurs et aux administrateurs de sécurité, l’opérateur qui gère les autres services Azure Stack Hub pour une organisation peut également l’implémenter et le gérer. Par exemple, cet opérateur Azure Stack Hub peut se connecter avec un abonnement Azure Stack Hub et créer un coffre pour l’organisation où stocker les clés. Une fois ces opérations terminées, il peut :

* créer ou importer une clé ou un secret ;
* supprimer ou effacer une clé ou un secret ;
* autoriser des utilisateurs ou des apps à accéder au coffre de clés, afin de pouvoir gérer ou utiliser ses clés et ses secrets ;
* configurer l’utilisation de la clé (par exemple, signature ou chiffrement).

L’opérateur est ensuite à même de fournir aux développeurs des URI (Uniform Resource Identifier), qu’ils peuvent appeler à partir de leurs apps. Il peut également fournir aux administrateurs de sécurité les informations sur la journalisation relative à l’utilisation des clés.

Les développeurs peuvent également gérer les clés directement à l’aide d’API. Pour plus d’informations, consultez le [guide du développeur Key Vault](/azure/key-vault/key-vault-developers-guide).

## <a name="scenarios"></a>Scénarios

Les scénarios suivants expliquent comment Key Vault peut permettre de répondre aux besoins des développeurs et des administrateurs de sécurité.

### <a name="developer-for-an-azure-stack-hub-app"></a>Développeur d’une application Azure Stack Hub

**Problème :** Je souhaite écrire une application Azure Stack Hub qui utilise des clés pour la signature et le chiffrement. Cependant, ces clés doivent être externes à mon app, afin que la solution soit adaptée à une app répartie au niveau géographique.

**Instruction :** Les clés sont stockées dans un coffre et appelées par un URI, si nécessaire.

### <a name="developer-for-software-as-a-service-saas"></a>Développeur de logiciels SaaS (Software as a service)

**Problème :** Je ne veux pas prendre la responsabilité des clés et des secrets pour mes clients. Je veux que les clients détiennent et gèrent leurs clés, pour pouvoir me concentrer sur ce que je fais le mieux, c’est-à-dire fournir les principales fonctionnalités du logiciel.

**Instruction :** Les clients peuvent importer et gérer leurs propres clés dans Azure Stack Hub.

### <a name="chief-security-officer-cso"></a>Responsable de la sécurité

**Problème :** Je souhaite m’assurer que mon organisation contrôle le cycle de vie d’une clé et peut surveiller son utilisation.

**Instruction :** Key Vault a été conçu de manière à ce que Microsoft ne puisse pas afficher ni extraire vos clés. Lorsqu’une app doit effectuer des opérations de chiffrement en utilisant des clés de clients, Key Vault effectue cette opération à la place de l’app. L’application ne voit pas les clés de clients. Même si nous utilisons plusieurs services et ressources Azure Stack Hub, vous pouvez gérer les clés depuis un seul emplacement dans Azure Stack Hub. Le coffre fournit une interface unique, indépendamment du nombre de coffres dont vous disposez dans Azure Stack Hub, des régions qui sont prises en charge et des applications qui les utilisent.

## <a name="next-steps"></a>Étapes suivantes

* [Gérer Key Vault dans Azure Stack Hub par le biais du portail](azure-stack-key-vault-manage-portal.md)  
* [Gérer Key Vault dans Azure Stack Hub à l’aide de PowerShell](azure-stack-key-vault-manage-powershell.md)
