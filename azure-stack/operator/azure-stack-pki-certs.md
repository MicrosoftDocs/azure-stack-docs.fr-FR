---
title: Exigences de certificat d’infrastructure de clés publiques Azure Stack Hub | Microsoft Docs
description: Découvrez les exigences de déploiement de certificat d’infrastructure de clés publiques Azure Stack Hub pour les systèmes intégrés Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2019
ms.author: justinha
ms.reviewer: ppacent
ms.lastreviewed: 12/16/2019
ms.openlocfilehash: 124f59d043a940ebcd48131f1012551777a17a6d
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75810450"
---
# <a name="azure-stack-hub-public-key-infrastructure-certificate-requirements"></a>Exigences de certificat d’infrastructure de clés publiques Azure Stack Hub

Azure Stack Hub inclut un réseau d’infrastructure publique utilisant des adresses IP publiques accessibles en externe affectées à un petit ensemble de services Azure Stack Hub et, éventuellement, à des machines virtuelles de locataires. Des certificats d’infrastructure de clés publiques avec les noms de DNS appropriés pour ces points de terminaison d’infrastructure publique Azure Stack Hub sont requis pendant le déploiement Azure Stack Hub. Cet article fournit des informations sur :

- les certificats requis pour déployer Azure Stack Hub ;
- le processus d’obtention de certificats correspondants à ces spécifications ;
- la manière de préparer, valider et utiliser ces certificats pendant le déploiement.

> [!NOTE]
> Par défaut, Azure Stack Hub utilise également des certificats émis par une autorité de certification interne intégrée à Active Directory pour l’authentification entre les nœuds. Pour valider le certificat, toutes les machines d’infrastructure Azure Stack Hub approuvent le certificat racine de l’autorité de certification interne en ajoutant ce certificat à leur magasin de certificats local. Azure Stack Hub ne permet pas d’épingler les certificats ou de les ajouter à une liste verte. Le réseau SAN de chaque certificat de serveur est validé par rapport au nom de domaine complet de la cible. La totalité de la chaîne d’approbation est également validée, ainsi que la date d’expiration du certificat (authentification de serveur TLS standard sans épinglage du certificat).

## <a name="certificate-requirements"></a>Configuration requise des certificats
La liste suivante décrit les exigences de certificat nécessaires pour déployer Azure Stack Hub :

- Les certificats doivent être émis par une autorité de certification interne ou une autorité de certification publique. Si vous utilisez une autorité de certification publique, elle doit être incluse dans l’image du système d’exploitation de base dans le cadre du projet Microsoft Trusted Root Authority Program. La liste complète est disponible dans la rubrique [Programme de certification racine approuvé Microsoft : participants](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
- Votre infrastructure Azure Stack Hub doit avoir accès au réseau de l’emplacement de la liste de révocation de certificats de l’autorité de certification publiée dans le certificat. Cette CRL doit être un point de terminaison http.
- Lors de la rotation des certificats dans les builds antérieures à 1903, les certificats doivent être émis à partir de la même autorité de certification interne utilisée pour signer des certificats fournie au déploiement ou de toute autorité de certification publique ci-dessus. Pour la build 1903 et les versions ultérieures, les certificats peuvent être émis par toute entreprise ou autorité de certification publique.
- L’utilisation des certificats auto-signés n’est pas prise en charge.
- Pour le déploiement et la rotation, vous pouvez utiliser un certificat unique couvrant tous les espaces de noms dans les champs Nom de l’objet et Autre nom de l’objet du certificat, OU des certificats individuels pour chaque espace de noms ci-dessous dont ont besoin les services Azure Stack Hub que vous envisagez d’utiliser. Les deux approches requièrent l’utilisation de caractères génériques pour les points de terminaison où ils sont requis, comme **KeyVault** et **KeyVaultInternal**.
- Le chiffrement PFX du certificat doit être 3DES.
- L’algorithme de signature de certificat ne doit pas être SHA1.
- Le format du certificat doit être PFX, car les clés tant publique que privée sont requises pour l’installation d’Azure Stack Hub. La clé privée doit être définie pour l’attribut de clé Ordinateur local.
- Le chiffrement PFX doit être 3DES (chiffrement par défaut en cas d’exportation depuis un client Windows 10 ou un magasin de certificats Windows Server 2016).
- Les fichiers pfx de certificat doivent avoir une valeur « Signature numérique » et « KeyEncipherment » dans le champ « Utilisation de la clé ».
- Dans le champ « Utilisation avancée de la clé », les fichiers pfx de certificat doivent avoir les valeurs « Authentification du serveur (1.3.6.1.5.5.7.3.1) » et « Authentification du client (1.3.6.1.5.5.7.3.2) ».
- Le contenu des champs « Délivré à » et « Délivré par » du certificat ne peut pas être identique.
- Les mots de passe de tous les fichiers pfx de certificat doivent être identiques au moment du déploiement.
- Le mot de passe pour le fichier de certificats pfx doit être un mot de passe complexe. Notez ce mot de passe, car vous allez l’utiliser comme paramètre de déploiement. Le mot de passe doit répondre aux exigences suivantes en matière de complexité des mots de passe :
    - Longueur minimale de huit caractères.
    - Au moins trois des caractères suivants : majuscules, minuscules, chiffres de 0 à 9, caractères spéciaux, caractères alphabétiques autres que des majuscules ou des minuscules.
- Vérifiez que les noms de l’objet et les autres noms de l’objet dans l’extension des autres noms de l’objet (x509v3_config) correspondent. Le champ de l’autre nom de l’objet vous permet de spécifier des noms d’hôtes supplémentaires (sites web, adresses IP, noms communs) en vue de les protéger au moyen d’un seul certificat SSL.

> [!NOTE]  
> Les certificats auto-signés ne sont pas pris en charge.  
> Lors du déploiement d’Azure Stack Hub en mode déconnecté, il est recommandé d’utiliser des certificats émis par une autorité de certification d’entreprise. Cela est important, car les clients accédant à des points de terminaison Azure Stack Hub doivent être en mesure de contacter la liste de révocation des certificats.

> [!NOTE]  
> La présence d’autorités de certification intermédiaires dans la chaîne d’approbation d’un certificat *est* prise en charge.

## <a name="mandatory-certificates"></a>Certificats obligatoires
Le tableau de cette section décrit les certificats d’infrastructure de clés publiques de point de terminaison public Azure Stack Hub qui sont requis pour les déploiements d’Azure AD et d’AD FS Azure Stack Hub. Les exigences de certificat sont regroupées par zone, ainsi que les espaces de noms utilisés et les certificats requis pour chaque espace de noms. Le tableau décrit également le dossier dans lequel votre fournisseur de solutions copie les différents certificats par point de terminaison public.

Des certificats avec des noms de DNS appropriés pour chaque point de terminaison d’infrastructure publique Azure Stack Hub sont requis. Le nom DNS de chaque point de terminaison est exprimé au format : *&lt;prefix>.&lt;region>.&lt;fqdn>* .

Pour votre déploiement, les valeurs [region] et [externalfqdn] doivent correspondre à la région et aux noms de domaines externes que vous avez choisis pour votre système Azure Stack Hub. Par exemple, si le nom de la région était *Redmond* et le nom de domaine externe *contoso.com*, les noms DNS aurait le format *&lt;prefix>.redmond.contoso.com*. Les valeurs *&lt;prefix>* sont prédéfinies par Microsoft pour décrire le point de terminaison sécurisé par le certificat. De plus, les valeurs *&lt;prefix>* des points de terminaison d’infrastructure externe dépendent du service Azure Stack Hub qui utilise le point de terminaison spécifique.

Pour les environnements de production, nous recommandons de générer des certificats individuels pour chaque point de terminaison et de les copier dans le répertoire correspondant. Pour les environnements de développement, les certificats peuvent être fournis sous la forme d’un certificat unique avec caractères génériques couvrant tous les espaces de noms dans les champs Sujet et Autre nom de l’objet (SAN) copiés dans tous les répertoires. Un certificat unique couvrant tous les points de terminaison et services pose des problèmes de sécurité ; cette approche est donc destinée aux équipes de développement uniquement. N’oubliez pas que les deux options requièrent l’utilisation de certificats avec caractères génériques pour les points de terminaison tels que **acs** et le coffre de clés lorsqu’ils sont requis.

> [!Note]  
> Au cours du déploiement, vous devez copier les certificats dans le dossier de déploiement correspondant au fournisseur d’identité (Azure AD ou AD FS). Si vous utilisez un seul certificat pour tous les points de terminaison, vous devez copier ce fichier de certificat dans chaque dossier de déploiement, comme indiqué dans les tableaux ci-dessous. La structure des dossiers est prédéfinie dans la machine virtuelle de déploiement et se trouve sous : C:\CloudDeployment\Setup\Certificates.

| Dossier de déploiement | Objet et autres noms de l’objet (SAN) du certificat requis | Étendue (par région) | Espace de noms de sous-domaine |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Portail public | portal.&lt;region>.&lt;fqdn> | Portails | &lt;region>.&lt;fqdn> |
| Portail d’administration | adminportal.&lt;region>.&lt;fqdn> | Portails | &lt;region>.&lt;fqdn> |
| Azure Resource Manager Public | management.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| Azure Resource Manager Admin | adminmanagement.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>(Certificat SSL générique) | Stockage Blob | blob.&lt;region>.&lt;fqdn> |
| ACSTable | *.table.&lt;region>.&lt;fqdn><br>(Certificat SSL générique) | Stockage Table | table.&lt;region>.&lt;fqdn> |
| ACSQueue | *.queue.&lt;region>.&lt;fqdn><br>(Certificat SSL générique) | Stockage File d’attente | queue.&lt;region>.&lt;fqdn> |
| KeyVault | *.vault.&lt;region>.&lt;fqdn><br>(Certificat SSL générique) | Key Vault | vault.&lt;region>.&lt;fqdn> |
| KeyVaultInternal | *.adminvault.&lt;region>.&lt;fqdn><br>(Certificat SSL générique) |  Coffre de clés interne |  adminvault.&lt;region>.&lt;fqdn> |
| Hôte d’extension d’administration | *.adminhosting.\<region>.\<fqdn> (Certificats SSL génériques) | Hôte d’extension d’administration | adminhosting.\<region>.\<fqdn> |
| Hôte d’extension public | *.hosting.\<region>.\<fqdn> (Certificats SSL génériques) | Hôte d’extension public | hosting.\<region>.\<fqdn> |

Si vous déployez Azure Stack Hub à l’aide du mode de déploiement Azure AD, vous devez simplement demander les certificats répertoriés dans le tableau précédent. En revanche, si vous déployez Azure Stack Hub à l’aide du mode de déploiement AD FS, vous devez également demander les certificats décrits dans le tableau suivant :

|Dossier de déploiement|Objet et autres noms de l’objet (SAN) du certificat requis|Étendue (par région)|Espace de noms de sous-domaine|
|-----|-----|-----|-----|
|ADFS|adfs. *&lt;region>.&lt;fqdn>*<br>(Certificat SSL)|ADFS|*&lt;region>.&lt;fqdn>*|
|Graph|graph. *&lt;region>.&lt;fqdn>*<br>(Certificat SSL)|Graph|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> Tous les certificats répertoriés dans cette section doivent avoir le même mot de passe.

## <a name="optional-paas-certificates"></a>Certificats PaaS facultatifs
Si vous envisagez de déployer les services PaaS Azure Stack Hub supplémentaires (SQL, MySQL et App Service) après le déploiement et la configuration d’Azure Stack Hub, vous devez demander des certificats supplémentaires pour couvrir les points de terminaison des services PaaS.

> [!IMPORTANT]
> Les certificats que vous utilisez pour les fournisseurs de ressources App Service, SQL et MySQL doivent avoir la même autorité racine que ceux utilisés pour les points de terminaison Azure Stack Hub globaux.

Le tableau suivant décrit les points de terminaison et les certificats requis pour les adaptateurs SQL et MySQL et pour App Service. Vous n’avez pas besoin de copier ces certificats dans le dossier de déploiement Azure Stack Hub. À la place, vous fournissez ces certificats lorsque vous installez les fournisseurs de ressources supplémentaires.

|Étendue (par région)|Certificat|Objet et autres noms de l’objet (SAN) du certificat requis|Espace de noms de sous-domaine|
|-----|-----|-----|-----|
|SQL, MySQL|SQL et MySQL|&#42;.dbadapter. *&lt;region>.&lt;fqdn>*<br>(Certificat SSL générique)|dbadapter. *&lt;region>.&lt;fqdn>*|
|App Service|Certificat SSL par défaut de trafic web|&#42;.appservice. *&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice. *&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice. *&lt;region>.&lt;fqdn>*<br>(Certificat SSL générique à plusieurs domaines<sup>1</sup>)|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|App Service|API|api.appservice. *&lt;region>.&lt;fqdn>*<br>(Certificat SSL<sup>2</sup>)|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice. *&lt;region>.&lt;fqdn>*<br>(Certificat SSL<sup>2</sup>)|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|App Service|SSO|sso.appservice. *&lt;region>.&lt;fqdn>*<br>(Certificat SSL<sup>2</sup>)|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|

<sup>1</sup> Requiert un certificat avec plusieurs autres noms de l’objet génériques. Plusieurs SAN génériques sur un même certificat peuvent ne pas être pris en charge par toutes les autorités de certification publiques.

<sup>2</sup> Un certificat générique &#42;.appservice. *&lt;region>.&lt;fqdn>* ne peut pas être utilisé à la place de ces trois certificats (api.appservice. *&lt;region>.&lt;fqdn>* , ftp.appservice. *&lt;region>.&lt;fqdn>* et sso.appservice. *&lt;region>.&lt;fqdn>* . App Service requiert explicitement l’utilisation de certificats distincts pour ces points de terminaison.

## <a name="learn-more"></a>En savoir plus
Découvrez comment [Générer des certificats d’infrastructure de clés publiques pour le déploiement d’Azure Stack Hub](azure-stack-get-pki-certs.md).

## <a name="next-steps"></a>Étapes suivantes
[Intégrer l’identité AD FS avec votre centre de données Azure Stack Hub](azure-stack-integrate-identity.md).