---
title: Télémétrie Azure Stack | Microsoft Docs
description: ’Décrit comment configurer les paramètres de télémétrie à l’aide de PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: mabrigg
ms.reviewer: comartin
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: edd242251cac89029eab7b45f06bb08f4db8a6a3
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618727"
---
# <a name="azure-stack-telemetry"></a>Télémétrie Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

La télémétrie Azure Stack charge automatiquement les données du système sur Microsoft via Expériences des utilisateurs connectés. Les équipes Microsoft utilisent les données de télémétrie d’Azure Stack pour améliorer les expériences des clients. Ces données sont également utilisées pour la sécurité, l’intégrité, la qualité et l’analyse des performances.

Si vous êtes opérateur Azure Stack, les données de télémétrie peuvent vous fournir des informations précieuses sur les déploiements de l’entreprise et vous permettre de faire entendre votre voix pour améliorer les futures versions d’Azure Stack.

> [!NOTE]
> Vous pouvez également configurer Azure Stack pour transmettre certaines informations sur l’utilisation à Azure à des fins de facturation. Cela est requis pour les clients Azure Stack à plusieurs nœuds qui choisissent une facturation du paiement à l’utilisation. Les rapports d’utilisation sont contrôlés indépendamment des données de télémétrie et ne sont pas requis pour les clients à plusieurs nœuds qui choisissent le modèle de capacité ou pour les utilisateurs du Kit de développement Azure Stack. Pour ces scénarios, les rapports d’utilisation peuvent être désactivés [à l’aide du script d’enregistrement](azure-stack-usage-reporting.md).

Les données de télémétrie Azure Stack reposent sur le composant Expériences des utilisateurs connectés et télémétrie de Windows Server 2016, qui utilise la technologie de journalisation des traces [Suivi d’événements pour Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) pour collecter et stocker des événements et des données. Les composants Azure Stack utilisent la même technologie pour publier les événements et les données recueillies à l’aide des API de suivi et de journalisation des événements du système d’exploitation publiques. Ces fournisseurs sont des exemples de composants Azure Stack : Ressource réseau, Ressource de stockage, Supervision de la ressource et Mettre à jour la ressource. Le composant Expériences des utilisateurs connectés et télémétrie chiffre les données à l’aide du protocole SSL et utilise l’épinglage de certificat pour transmettre les données via HTTPS vers le service de gestion des données Microsoft.

> [!IMPORTANT]
> Pour prendre en charge le flux des données de télémétrie, le port 443 (HTTPS) doit être ouvert sur votre réseau. Le composant Expériences des utilisateurs connectés et télémétrie se connecte au service de gestion des données de Microsoft à l’adresse https://v10.vortex-win.data.microsoft.com. Le composant Expériences des utilisateurs connectés et télémétrie se connecte également à l’adresse https://settings-win.data.microsoft.com pour télécharger les informations de configuration.

## <a name="privacy-considerations"></a>Considérations relatives à la confidentialité

Le service ETW réachemine les données de télémétrie vers le stockage cloud protégé. Le principe des privilèges minimum détermine l’accès aux données de télémétrie. Seul le personnel Microsoft dont les besoins métiers sont valides sont autorisés à accéder aux données de télémétrie. Microsoft ne partage pas les données personnelles des clients avec des tiers, excepté à la discrétion du client ou à des fins limitées décrites dans la [Déclaration de confidentialité Microsoft](https://privacy.microsoft.com/PrivacyStatement). Les rapports d’entreprise partagés avec les fabricants OEM et les partenaires incluent des données anonymes et agrégées. Les décisions relatives au partage des données sont effectuées par une équipe Microsoft interne composée de parties prenantes des domaines de la confidentialité, des questions juridiques et de la gestion des données.

Microsoft croit en la réduction des informations et la met en pratique. Nous nous efforçons de recueillir uniquement les informations nécessaires et nous les stockons seulement pour la durée pendant laquelle elles sont nécessaires pour fournir un service ou une analyse. La plupart des informations concernant le fonctionnement du système Azure Stack et des services Azure sont supprimées dans les six mois. Les données résumées ou agrégées seront conservées plus longtemps.

Nous comprenons que la confidentialité et la sécurité des informations des clients sont importantes.  Microsoft a adopté une approche sérieuse et complète de la confidentialité du client et de la protection de ses données dans Azure Stack. Les administrateurs informatiques peuvent personnaliser les fonctionnalités et les paramètres de confidentialité à tout moment. Notre engagement en faveur de la transparence et de confiance est clair :

- Nous informons les clients des types de données que nous recueillons.
- Les clients des entreprises ont la possibilité de personnaliser leurs paramètres de confidentialité.
- La confidentialité et la sécurité des clients sont notre priorité.
- Nous sommes transparents concernant l’utilisation des données de télémétrie.
- Nous les utilisons pour améliorer l’expérience des clients.

Microsoft n’entend pas recueillir de données sensibles, telles que des numéros de cartes bancaires, des noms d’utilisateurs et des mots de passe, des adresses de messagerie ou tout autre information sensible. Toute information sensible reçue par inadvertance est supprimée.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Exemples d’utilisation des données de télémétrie par Microsoft

La télémétrie joue un rôle important dans l’identification et la résolution rapides de problèmes de fiabilité critiques au sein des déploiements et des configurations des clients. Les informations issues des données de télémétrie nous permettent d’identifier rapidement les problèmes liés aux services ou aux configurations matérielles. La capacité de Microsoft à obtenir ces données de la part des clients et à améliorer l’écosystème nous permet d’augmenter le niveau de qualité de nos solutions Azure Stack intégrées.

Les données de télémétrie permettent également à Microsoft de mieux comprendre comment les clients déploient des composants et utilisent des fonctionnalités et des services pour atteindre leurs objectifs commerciaux. Ces données nous aident à hiérarchiser nos investissements d’ingénierie dans des domaines qui peuvent impacter directement les expériences et les charges de travail des clients.

Parmi les exemples figurent l’utilisation des conteneurs, le stockage et les configurations réseau associés aux rôles Azure Stack. Nous utilisons également les informations pour améliorer les solutions de gestion et de surveillance Azure Stack et les rendre plus intelligentes. Grâce à ces améliorations, les clients peuvent diagnostiquer les problèmes plus facilement et de faire des économies en faisant moins appel à Microsoft.

## <a name="manage-telemetry-collection"></a>Gérer la collecte de données de télémétrie

Nous vous déconseillons de désactiver la télémétrie dans votre organisation. Toutefois, que cela peut être utile dans certains scénarios.

Vous pouvez parfois configurer le niveau de données de télémétrie transmises à Microsoft à l’aide des paramètres de registres avant le déploiement d’Azure Stack ou à l’aide des points de terminaison de télémétrie après le déploiement d’Azure Stack.

### <a name="telemetry-levels-and-data-collection"></a>Collecte de données et de niveaux de télémétrie

Avant de modifier les paramètres de télémétrie, vous devez comprendre les niveaux de télémétrie et les données recueillies à chaque niveau.

Les paramètres de télémétrie sont regroupés en quatre niveaux (0-3) qui se cumulent et qui sont classés comme suit :

**0 (Sécurité)**</br>
Données de sécurité uniquement. Informations nécessaires pour sécuriser le système d’exploitation. Cela inclut des données sur les paramètres du composant Expériences de l’utilisateur connecté et télémétrie, et sur Windows Defender. Aucune donnée de télémétrie spécifique d’Azure Stack n’est émise à ce niveau.

**1 (De base)**</br>
Données de sécurité, données de base relatives à l’intégrité et données concernant la qualité. Informations de base relatives à l’appareil, y compris, les données concernant la qualité, la compatibilité des applications, l’utilisation des applications et les données issues du niveau **Sécurité**. La définition du niveau de télémétrie sur De base a pour effet d’activer la télémétrie Azure Stack. Les données recueillies à ce niveau comprennent :

- Des *informations de base relatives à l’appareil* qui permettent de comprendre les types et les configurations des instances natives et virtualisées de Windows Server 2016 au sein de l’écosystème, notamment :

  - les attributs de la machine, comme le fabricant OEM et le modèle ;
  - les attributs du réseau, tels que le nombre et la vitesse des adaptateurs réseau ;
  - les attributs du processeur et de la mémoire, tels que le nombre de cœurs et la taille de la mémoire installée ;
  - les attributs du stockage, tels que le nombre de disques, leur type et leur taille.

- Une *fonction de télémétrie*, y compris le pourcentage d’événements chargés et supprimés, et la dernière heure de chargement de données.
- Des *informations relatives à la qualité* qui permettent à Microsoft de comprendre les performances d’Azure Stack. Par exemple, le nombre d’alertes critiques sur une configuration matérielle spécifique.
- Des *données de compatibilité* qui aident à identifier les fournisseurs de ressources installés sur un système et une machine virtuelle. Cela permet d’identifier les éventuels problèmes de compatibilité.

**2 (Amélioré)**</br>
Informations supplémentaires, y compris : utilisation du système d’exploitation et des services Azure Stack, fonctionnement de ces services, données de fiabilité avancées et données issues des niveaux **De base** et **Sécurité**.

> [!NOTE]
> Il s’agit du paramètre de télémétrie par défaut.

**3 (Complet)**</br>
Toutes les données nécessaires pour identifier et vous aider à résoudre les problèmes, ainsi que les données issues des niveaux **Sécurité**, **De base**, et **Avancé**.

> [!IMPORTANT]
> Ces niveaux de télémétrie s’appliquent uniquement aux composants Microsoft Azure Stack. Les composants logiciels et services non Microsoft s’exécutant dans l’hôte de cycle de vie du matériel issus de partenaires fabricants de matériel Azure Stack peuvent communiquer avec leurs services cloud en dehors de ces niveaux de télémétrie. Vous devez collaborer avec votre fournisseur de solutions matérielles Azure Stack afin de comprendre leur stratégie de télémétrie, et comment s’y abonner ou annuler votre abonnement.

La désactivation de la télémétrie Windows et Azure Stack désactive également les données de télémétrie SQL. Pour plus d’informations sur les conséquences des paramètres de télémétrie de Windows Server, consultez le [Livre blanc sur la télémétrie Windows](https://aka.ms/winservtelemetry).

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK : définir le niveau de télémétrie au sein du registre Windows

Vous pouvez utiliser l’Éditeur du Registre Windows pour définir manuellement le niveau de télémétrie sur l’ordinateur hôte physique avant de déployer Azure Stack. Si une stratégie de gestion, telle qu’une stratégie de groupe existe déjà, celle-ci remplace le paramètre de registre.

Avant de déployer Azure Stack sur l’hôte du Kit de développement, démarrez la machine sur CloudBuilder.vhdx et exécutez le script suivant dans une fenêtre PowerShell avec des privilèges élevés :

```powershell
### Get current AllowTelemetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK et multinœuds : activer ou désactiver la télémétrie après le déploiement

Pour activer ou désactiver la télémétrie après le déploiement, vous devez avoir accès au point de terminaison privilégié (PEP) exposé sur les machines virtuelles ERCS.

1. Pour activer : `Set-Telemetry -Enable`
2. Pour désactiver : `Set-Telemetry -Disable`

Détails de PARAMETER :
> .PARAMETER Enable - Active le chargement des données de télémétrie</br>
> .PARAMETER Disable - Désactive le chargement des données de télémétrie  

**Script pour activer la télémétrie :**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Enable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

**Script pour désactiver la télémétrie :**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Disable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

## <a name="next-steps"></a>Étapes suivantes

[Inscrire Azure Stack auprès d’Azure](azure-stack-registration.md)