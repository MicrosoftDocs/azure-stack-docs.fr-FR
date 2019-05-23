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
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 78ca05eb15570022e6eb25a7023d91c5ff8b817f
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617335"
---
# <a name="azure-stack-telemetry"></a>Télémétrie Azure Stack

Les données de télémétrie ou du système Azure Stack sont automatiquement chargées sur Microsoft via Expériences des utilisateurs connectés. Les données recueillies à partir des données de télémétrie Azure sont utilisées par les équipes Microsoft principalement pour améliorer nos expériences client et à des fins de sécurité, d’intégrité, de qualité et de performance.

En tant qu’opérateur Azure Stack, les données de télémétrie peuvent fournir des informations précieuses sur les déploiements de l’entreprise et vous permettre de faire entendre votre voix pour améliorer les futures versions d’Azure Stack.

> [!NOTE]
> Azure Stack peut également être configuré pour transmettre certaines informations sur l’utilisation à Azure à des fins de facturation. Cela est requis pour les clients Azure Stack à plusieurs nœuds qui choisissent une facturation du paiement à l’utilisation. Les rapports d’utilisation sont contrôlés indépendamment des données de télémétrie et ne sont pas requis pour les clients à plusieurs nœuds qui choisissent le modèle de capacité ou pour les utilisateurs du Kit de développement Azure Stack. Pour ces scénarios, les rapports d’utilisation peuvent être désactivés [à l’aide du script d’enregistrement](../operator/azure-stack-usage-reporting.md).

Les données de télémétrie Azure Stack reposent sur le composant Expériences des utilisateurs connectés et télémétrie de Windows Server 2016, qui utilise la technologie de journalisation des traces [suivi d’événements pour Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) pour collecter et stocker des événements et des données de télémétrie. Les composants Azure Stack utilisent la même technologie de journalisation pour publier les événements et les données recueillies à l’aide des API de suivi et de journalisation des événements du système d’exploitation publiques. Parmi les composants Azure Stack figurent le fournisseur de ressources réseau, le fournisseur de ressources de stockage, le fournisseur de ressources de surveillance et le fournisseur de ressources de mise à jour. Le composant Expériences des utilisateurs connectés et télémétrie chiffre les données à l’aide du protocole SSL et utilise l’épinglage de certificat pour transmettre les données de télémétrie via HTTPS vers le service de gestion des données Microsoft.

> [!NOTE]
> Pour prendre en charge le flux des données de télémétrie, le port 443 (HTTPS) doit être ouvert sur votre réseau. Le composant Expériences des utilisateurs connectés et télémétrie se connecte au service de gestion des données de Microsoft à l’adresse https://v10.vortex-win.data.microsoft.com. Le composant Expériences des utilisateurs connectés et télémétrie se connecte également à l’adresse https://settings-win.data.microsoft.com pour télécharger les informations de configuration.

## <a name="privacy-considerations"></a>Considérations relatives à la confidentialité
Le service ETW réachemine les données de télémétrie vers le stockage cloud protégé. Le principe des tout derniers privilégiés guides l’accès aux données de télémétrie. Seul le personnel Microsoft dont les besoins métiers sont valides sont autorisés à accéder aux données de télémétrie. Microsoft ne partage pas les données personnelles de ses clients avec des tiers, excepté à la discrétion du client à des fins limitées décrites dans la [Déclaration de confidentialité d’Azure Stack](https://privacy.microsoft.com/PrivacyStatement). Nous partageons les rapports d’entreprise, qui incluent des informations de télémétrie anonymes et agrégées avec les fabricants OEM et les partenaires. Les décisions relatives au partage des données sont effectuées par une équipe Microsoft interne composée de parties prenantes des domaines de la confidentialité, des questions juridiques et de la gestion des données.

Microsoft croit en la réduction des informations et la met en pratique. Nous nous efforçons de recueillir uniquement les informations dont nous avons besoin, et nous les stockons seulement pour la durée pendant laquelle elles sont nécessaires pour fournir un service ou une analyse. La plupart des informations concernant le fonctionnement du système Azure Stack et des services Azure sont supprimées dans les six mois. Les données résumées ou agrégées sont conservées plus longtemps.

Nous comprenons l’importance de la confidentialité et de la sécurité des informations de nos clients. Nous avons adopté une approche sérieuse et complète de la confidentialité du client et de la protection de ses données avec Azure Stack. Les administrateurs informatiques peuvent personnaliser les fonctionnalités et les paramètres de confidentialité à tout moment. Notre engagement en faveur de la transparence et de confiance est clair :
- Nous informons les clients des types de données que nous recueillons.
- Les clients des entreprises ont le pouvoir de personnaliser leurs propres paramètres de confidentialité.
- La confidentialité et la sécurité des clients sont notre priorité.
- Nous sommes transparents concernant l’utilisation des données de télémétrie.
- Nous les utilisons pour améliorer l’expérience des clients.

Microsoft n’entend pas recueillir d’informations sensibles, telles que des numéros de cartes bancaires, des noms d’utilisateurs et des mots de passe, des adresses de messagerie ou tout autre information sensible. Toute information sensible reçue par inadvertance est supprimée.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Exemples d’utilisation des données de télémétrie par Microsoft
La télémétrie joue un rôle important dans l’identification et la résolution rapides de problèmes de fiabilité critiques au sein des déploiements et des configurations des clients. Les informations issues des données de télémétrie que nous collectons nous permettent d’identifier rapidement les problèmes liés aux services ou aux configurations matérielles. La capacité de Microsoft à obtenir ces données de la part des clients et à améliorer l’écosystème nous permet d’augmenter le niveau de qualité de nos solutions Azure Stack intégrées.

Les données de télémétrie permettent également à Microsoft de mieux comprendre comment les clients déploient des composants et utilisent des fonctionnalités et des services pour atteindre leurs objectifs commerciaux. Le fait d’obtenir des insights à partir de ces données nous aide à hiérarchiser nos investissements d’ingénierie dans des domaines qui peuvent impacter directement les expériences et les charges de travail de nos clients.

Parmi les exemples figurent l’utilisation des conteneurs, le stockage et les configurations réseau associés aux rôles Azure Stack. Nous utilisons également les informations pour améliorer certaines de nos solutions de gestion et de surveillance et les rendre plus intelligentes. Cela permet aux clients de diagnostiquer les problèmes de qualité et de faire des économies en faisant moins appel à Microsoft.

## <a name="manage-telemetry-collection"></a>Gérer la collecte de données de télémétrie
Nous vous recommandons de ne pas désactiver la télémétrie au sein de votre organisation dans la mesure où cette dernière génère des données permettant d’améliorer la fonctionnalité et la stabilité des produits. Nous reconnaissons, toutefois, que cela peut être utile dans certains scénarios.

Dans certaines instances, vous pouvez configurer le niveau de données de télémétrie transmises à Microsoft à l’aide du prédéploiement de paramètres de registre ou du postdéploiement de points de terminaison de télémétrie.

### <a name="set-telemetry-level-in-the-windows-registry"></a>Définir le niveau de télémétrie dans le Registre Windows
L’Éditeur du Registre Windows sert à définir manuellement le niveau de télémétrie sur l’ordinateur hôte physique avant de déployer Azure Stack. Si une stratégie de gestion, telle qu’une stratégie de groupe existe déjà, celle-ci remplace le paramètre de registre.

Avant de déployer Azure Stack sur l’hôte du Kit de développement, démarrez la machine sur CloudBuilder.vhdx et exécutez le script suivant dans une fenêtre PowerShell avec des privilèges élevés :

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

Les niveaux de télémétrie sont cumulés et répartis dans quatre niveaux (0 à 3) :

**0 (Sécurité)**. Données de sécurité uniquement. Informations nécessaires pour permettre la sécurisation du système d’exploitation, y compris des données relatives aux paramètres du composant Expériences des utilisateurs connectés et télémétrie, et Windows Defender. Aucune donnée de télémétrie spécifique d’Azure Stack n’est émise à ce niveau.

**1 (De base)**. Données de sécurité, données de base relatives à l’intégrité et données concernant la qualité. Informations de base relatives à l’appareil, y compris, les données concernant la qualité, la compatibilité des applications, l’utilisation des applications et les données issues du niveau de sécurité. La définition du niveau de télémétrie sur De base a pour effet d’activer la télémétrie Azure Stack. Les données recueillies à ce niveau comprennent :

- Des **informations de base relatives à l’appareil** qui aident à comprendre les types et les configurations des instances natives et virtualisées de Windows Server 2016 au sein de l’écosystème, y compris :
  - Les attributs de la machine, comme le fabricant OEM, le modèle.
  - Les attributs du réseau, tels que le nombre et la vitesse des adaptateurs réseau.
  - Les attributs du processeur et de la mémoire, tels que le nombre de cœurs, la taille de la mémoire.
  - Les attributs du stockage, tels que le nombre de disques, le type et la taille.
- Une **fonction de télémétrie**, y compris le pourcentage d’événements chargés, supprimés et la dernière heure de chargement.
- Des **informations relatives à la qualité** qui permettent à Microsoft de comprendre les performances d’Azure Stack. Par exemple, le nombre d’alertes critiques sur une configuration matérielle spécifique.
- **Données de compatibilité** qui permet d’identifier les fournisseurs de ressources installés sur un système ou une machine virtuelle, ainsi que d’identifier les problèmes de compatibilité potentiels.

**2 (Amélioré)**. Informations supplémentaires, y compris : comment le système d’exploitation et les autres services Azure Stack sont utilisés, comment ils fonctionnent, données de fiabilité avancées et données issues des niveaux de base et de sécurité.

**3 (Complet)**. Toutes les données nécessaires pour identifier et vous aider à résoudre les problèmes, ainsi que les données issues des niveaux **Sécurité**, **De base**, et **Avancé**.

> [!NOTE]
> La valeur du niveau de télémétrie par défaut est 2 (avancé).

La désactivation de la télémétrie Windows et Azure Stack désactive la télémétrie SQL. Pour plus d’informations sur les implications des paramètres de télémétrie Windows Server, reportez-vous au [Livre blanc sur la télémétrie Windows](https://aka.ms/winservtelemetry).

> [!IMPORTANT]
> Ces niveaux de télémétrie s’appliquent uniquement aux composants Microsoft Azure Stack. Les composants logiciels et services non Microsoft s’exécutant dans l’hôte de cycle de vie du matériel issus de partenaires fabricants de matériel Azure Stack peuvent communiquer avec leurs services cloud en dehors de ces niveaux de télémétrie. Vous devez collaborer avec votre fournisseur de solutions matérielles Azure Stack afin de comprendre leur stratégie de télémétrie, et comment s’y abonner ou annuler votre abonnement.

### <a name="enable-or-disable-telemetry-after-deployment"></a>Activer ou désactiver la télémétrie après le déploiement

Pour activer ou désactiver la télémétrie après le déploiement, vous devez avoir accès au point de terminaison privilégié (PEP) exposé sur les machines virtuelles ERCS.
1.  Pour activer : `Set-Telemetry -Enable`
2.  Pour désactiver : `Set-Telemetry -Disable`

Informations relatives à PARAMETER :
> .PARAMETER Enable - Active le chargement des données de télémétrie
> 
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
[Démarrer et arrêter l’ASDK](asdk-start-stop.md)
