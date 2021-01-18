---
title: Déployer une virtualisation d’entreprise fiable sur Azure Stack HCI
description: Cette rubrique fournit des conseils sur la planification, la configuration et le déploiement d’une infrastructure hautement sécurisée qui utilise la virtualisation d’entreprise fiable sur le système d’exploitation Azure Stack HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/07/2021
ms.openlocfilehash: 37a29bcfae28f1b1f01cf8e459df016a3b5c4daf
ms.sourcegitcommit: 330d04d39e0cf3e8965e2ccbc181c968cb71d9ad
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052667"
---
# <a name="deploy-trusted-enterprise-virtualization-on-azure-stack-hci"></a>Déployer une virtualisation d’entreprise fiable sur Azure Stack HCI

>S’applique à : Azure Stack HCI, version 20H2

Cette rubrique fournit des conseils sur la planification, la configuration et le déploiement d’une infrastructure hautement sécurisée qui utilise la virtualisation d’entreprise fiable sur le système d’exploitation Azure Stack HCI. Tirez parti de votre investissement Azure Stack HCI pour exécuter des charges de travail sécurisées sur du matériel qui utilise [la sécurité basée sur la virtualisation (VBS)](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-vbs) et des services cloud hybrides via Windows Admin Center et le portail Azure.

## <a name="overview"></a>Vue d’ensemble
VBS est un composant clé des [investissements en sécurité dans Azure Stack HCI](/windows-server/get-started-19/whats-new-19#security) pour protéger les hôtes et les machines virtuelles contre les menaces de sécurité. Par exemple, le [Guide d’implémentation technique de la sécurité (STIG)](https://nvd.nist.gov/ncp/checklist/914), qui est publié en tant qu’outil pour améliorer la sécurité des systèmes d’information du ministère de la défense (DoD), indique que VBS et HVCI ([Hypervisor-Protected Code Integrity, Intégrité du code protégée par hyperviseur](https://docs.microsoft.com/windows-hardware/drivers/bringup/device-guard-and-credential-guard)) sont des exigences de sécurité générales. Il est impératif d’utiliser le matériel hôte activé pour VBS et HVCI afin de protéger les charges de travail sur les machines virtuelles, car un hôte compromis ne peut pas garantir la protection de la machine virtuelle.

VBS utilise des fonctionnalités de virtualisation matérielle pour créer et isoler une région sécurisée de mémoire du système d’exploitation. Vous pouvez utiliser le [mode sécurisé virtuel (VSM)](https://docs.microsoft.com/virtualization/hyper-v-on-windows/tlfs/vsm) dans Windows pour héberger un certain nombre de solutions de sécurité afin d’améliorer la protection contre les vulnérabilités du système d’exploitation et les attaques malveillantes.

VBS utilise l’hyperviseur Windows pour créer et gérer les limites de sécurité dans les logiciels du système d’exploitation, appliquer des restrictions pour protéger les ressources système vitales et protéger les ressources de sécurité, telles que les informations d’identification des utilisateurs authentifiés. Avec VBS, même si un programme malveillant a accès au noyau du système d’exploitation, vous pouvez considérablement limiter et contenir les failles possibles, car l’hyperviseur empêche les programmes malveillants d’exécuter du code ou d’accéder aux secrets de la plateforme.

L’hyperviseur, le niveau de logiciel système le plus privilégié, définit et applique des autorisations de page sur l’ensemble de la mémoire système. Dans VSM, les pages ne peuvent s’exécuter qu’après avoir réussi les contrôles d’intégrité du code. Même si une vulnérabilité, telle qu’un dépassement de capacité de la mémoire tampon, peut permettre à un programme malveillant d’essayer de modifier la mémoire, les pages de codes ne peuvent pas être modifiées et la mémoire modifiée ne peut pas être exécutée. VBS et HVCI renforcent considérablement l’application de la stratégie d’intégrité du code. Tous les fichiers binaires et pilotes en mode noyau sont vérifiés avant de pouvoir démarrer, et les pilotes ou fichiers système non signés ne peuvent pas être chargés dans la mémoire système.

## <a name="deploy-trusted-enterprise-virtualization"></a>Déployer une virtualisation d’entreprise approuvée
Cette section décrit de manière générale comment acquérir du matériel pour déployer une infrastructure hautement sécurisée qui utilise la virtualisation d’entreprise fiable sur Azure Stack HCI et Windows Admin Center pour la gestion.

### <a name="step-1-acquire-hardware-for-trusted-enterprise-virtualization-on-azure-stack-hci"></a>Étape 1 : Acquérir du matériel pour la virtualisation d’entreprise fiable sur Azure Stack HCI
Tout d’abord, vous devez vous procurer du matériel. Le moyen le plus simple est de localiser votre partenaire de matériel Microsoft préféré dans le [catalogue Azure Stack HCI](https://hcicatalog.azurewebsites.net) et d’acheter un système intégré sur lequel le système d’exploitation Azure Stack HCI est préinstallé. Dans le catalogue, vous pouvez appliquer un filtre de façon à afficher le matériel fournisseur qui est optimisé pour ce type de charge de travail.

Dans le cas contraire, vous devez déployer le système d’exploitation Azure Stack HCI sur votre propre matériel. Pour plus d’informations sur les options de déploiement Azure Stack HCI et sur l’installation de Windows Admin Center, consultez [Déployer le système d’exploitation Azure Stack HCI](./operating-system.md).

Ensuite, utilisez Windows Admin Center pour [créer un cluster Azure Stack HCI](./create-cluster.md).

Tous les matériels provenant de partenaires pour Azure Stack HCI sont certifiés avec la qualification supplémentaire d’assurance qualité du matériel. Le processus de qualification teste toutes les fonctionnalités [VBS](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-vbs) requises. Toutefois, VBS et HVCI ne sont pas automatiquement activés dans Azure Stack HCI. Pour plus d’informations sur la qualification supplémentaire de l’assurance matérielle, consultez la section « Assurance qualité du matériel » sous **Systèmes** dans le [catalogue Windows Server](https://www.windowsservercatalog.com/content.aspx?ctf=AQinfo-systems.htm#:~:text=Hardware%20Assurance%20Windows%20Server%20systems%20that%20are%20awarded,of%20Windows%20Server%2C%20starting%20with%20Windows%20Server%202016).

   >[!WARNING]
   > HVCI peut être incompatible avec les périphériques matériels non listés dans le catalogue Azure Stack HCI. Pour l’infrastructure de virtualisation d’entreprise fiable, nous vous recommandons vivement d’utiliser un matériel Azure Stack HCI validé provenant de nos partenaires.

### <a name="step-2-enable-hvci"></a>Étape 2 : Activer HVCI
Activez HVCI sur les machines virtuelles et le matériel de votre serveur. Pour plus d’informations, consultez [Activer la protection basée sur la virtualisation de l’intégrité du code](https://docs.microsoft.com/windows/security/threat-protection/device-guard/enable-virtualization-based-protection-of-code-integrity).

### <a name="step-3-set-up-azure-security-center-in-windows-admin-center"></a>Étape 3 : Configurer Azure Security Center dans Windows Admin Center
Dans Windows Admin Center, configurez [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-introduction) pour ajouter la protection contre les menaces et évaluer rapidement la position de sécurité de vos charges de travail.

Pour plus d’informations, consultez [Protéger les ressources Windows Admin Center avec Security Center](https://docs.microsoft.com/azure/security-center/windows-admin-center-integration).

Pour bien démarrer avec Security Center :
- Vous devrez vous abonner à Microsoft Azure. Si vous n’avez pas d’abonnement, vous pouvez vous inscrire à un [essai gratuit](https://azure.microsoft.com/free).
- Le niveau tarifaire gratuit de Security Center est activé sur tous vos abonnements Azure en cours après votre première consultation du tableau de bord Azure Security Center dans le portail Azure. Il peut aussi être activé par programmation par le biais d’une API.
Pour tirer parti des fonctionnalités avancées de gestion de la sécurité et de détection des menaces, vous devez activer Azure Defender. Vous pouvez utiliser Azure Defender gratuitement pendant 30 jours. Pour plus d’informations, consultez la [tarification de Security Center](https://azure.microsoft.com/pricing/details/security-center).
- Si vous êtes prêt à activer Azure Defender, le [Démarrage rapide : Configuration d’Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started) vous accompagne dans la procédure étape par étape.

Vous pouvez également utiliser Windows Admin Center pour configurer d’autres services hybrides Azure, tels que Sauvegarde, File Sync, Site Recovery, VPN de point à site, Update Management et Azure Monitor.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la virtualisation d’entreprise fiable, consultez :
- [Hyper-V sur Windows Server](/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)
