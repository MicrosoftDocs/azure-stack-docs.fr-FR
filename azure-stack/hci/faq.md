---
title: FAQ sur Azure Stack HCI
description: FAQ sur Azure Stack HCI.
ms.topic: conceptual
author: JohnCobb1
ms.author: v-johcob
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/24/2020
ms.openlocfilehash: f4c6b9585f41388281c6618fabd21932f6d48c38
ms.sourcegitcommit: afdae61022037b5dba8345cb264049897e0aca8f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97051596"
---
# <a name="azure-stack-hci-faq"></a>FAQ sur Azure Stack HCI
Le Forum aux questions (FAQ) consacré à Azure Stack HCI comprend une section Connectivité Azure Stack HCI et une section FAQ d'ordre général.

## <a name="azure-stack-hci-connectivity"></a>Connectivité Azure Stack HCI
Azure Stack HCI est une pile d'infrastructure hyperconvergée locale fournie en tant que service hybride Azure. Vous installez le logiciel Azure Stack HCI sur des serveurs physiques que vous contrôlez dans vos locaux. Puis vous vous connectez à Azure pour bénéficier des fonctionnalités cloud de surveillance, de prise en charge et de facturation, ainsi que des fonctionnalités optionnelles de gestion et de sécurité. Cette section FAQ explique comment Azure Stack HCI utilise le cloud en répondant aux questions les plus fréquemment posées sur la connectivité en termes d'exigences et de comportement.

### <a name="your-data-stays-on-premises"></a>Vos données restent locales

**Mes données stockées sur Azure Stack HCI sont-elles envoyées vers le cloud ?**

Non. Les noms, les métadonnées, la configuration et le contenu de vos machines virtuelles locales ne sont jamais envoyés vers le cloud, sauf si vous activez expressément à cette fin des services supplémentaires comme Sauvegarde Azure ou Azure Site Recovery. Ou sauf si vous inscrivez ces machines virtuelles individuellement dans des services de gestion cloud comme Azure Arc.

### <a name="edge-local-management-and-control"></a>Gestion et contrôle de périphérie au niveau local

**Le plan de contrôle d'Azure Stack HCI passe-t-il par le cloud ?**

Non. Vous pouvez utiliser des outils de périphérie locaux, tels que Windows Admin Center, PowerShell ou System Center, pour gérer directement l'infrastructure hôte et les machines virtuelles, même si votre connexion réseau au cloud est interrompue ou très limitée. Les opérations quotidiennes courantes, telles que le déplacement d'une machine virtuelle entre des hôtes, le remplacement d'un disque défaillant ou la configuration d'adresses IP, ne reposent pas sur le cloud. Cependant, la connectivité au cloud est nécessaire pour obtenir des mises à jour logicielles, modifier votre inscription Azure ou utiliser des fonctionnalités qui dépendent directement des services cloud pour la sauvegarde, la surveillance, etc.

**Existe-t-il des exigences particulières en termes de bande passante ou de latence entre Azure Stack HCI et le cloud ?**

Non. Les connexions à bande passante limitée telles que les lignes T1 rurales ou les connexions satellite/cellulaires conviennent à la synchronisation d'Azure Stack HCI. La connectivité minimale requise n'est que de quelques kilo-octets par jour. D'autres services peuvent nécessiter davantage de bande passante, notamment pour répliquer ou sauvegarder des machines virtuelles entières, télécharger des mises à jour logicielles volumineuses ou charger des journaux détaillés à des fins d'analyse et de surveillance dans le cloud.

### <a name="designed-for-intermittent-and-limited-connectivity"></a>Conçu pour une connectivité intermittente et limitée

**Azure Stack HCI nécessite-t-il une connectivité continue au cloud ?**

Non. Azure Stack HCI est conçu pour gérer les périodes de connectivité limitée ou inexistante.

**Que se passe-t-il si ma connexion réseau au cloud est momentanément interrompue ?**

En cas d'interruption de la connexion, l'infrastructure hôte et les machines virtuelles continuent de fonctionner normalement, et vous pouvez utiliser des outils de périphérie locaux pour la gestion. En revanche, vous ne pouvez pas utiliser les fonctionnalités qui reposent directement sur les services cloud. Les informations contenues sur le portail Azure deviennent également obsolètes jusqu'à ce qu'Azure Stack HCI soit à nouveau en mesure de se synchroniser.

**Combien de temps Azure Stack HCI peut-il fonctionner avec une connexion interrompue ?**

Au minimum, Azure Stack HCI doit se synchroniser avec Azure une fois tous les 30 jours consécutifs.

**Que se passe-t-il en cas de dépassement de la limite de 30 jours ?**

Si Azure Stack HCI ne s'est pas synchronisé avec Azure depuis plus de 30 jours consécutifs, l'état de connexion du cluster affiche **Hors stratégie** sur le portail Azure et dans d'autres outils, et le cluster passe en mode d'utilisation avec fonctionnalités réduites. Dans ce mode, l'infrastructure hôte reste active et toutes les machines virtuelles existantes continuent de fonctionner normalement. Toutefois, il est impossible de créer de nouvelles machines virtuelles tant qu'Azure Stack HCI n'est pas en mesure de se resynchroniser. Il existe une raison technique interne à cela : la licence générée dans le cloud du cluster a expiré et doit être renouvelée par le biais d'une synchronisation avec Azure.

### <a name="understanding-sync"></a>Présentation de la synchronisation

**Quel contenu Azure Stack HCI synchronise-t-il avec le cloud ?**

Cela dépend des fonctionnalités que vous utilisez. Au minimum, Azure Stack HCI synchronise les informations de base du cluster à afficher sur le portail Azure (comme la liste des nœuds en cluster, le modèle de matériel et la version du logiciel), les informations de facturation qui récapitulent les jours de base accumulés depuis la dernière synchronisation, ainsi que les informations de diagnostic minimales requises pour permettre à Microsoft de garder votre instance d'Azure Stack HCI sécurisée, à jour et en bon état de fonctionnement. La taille totale est limitée à quelques kilo-octets. Si vous activez des services supplémentaires, ils peuvent charger d'autres éléments : par exemple, Azure Log Analytics peut charger des journaux d'activité et des compteurs de performances à des fins de surveillance.

**À quelle fréquence Azure Stack HCI se synchronise-t-il avec le cloud ?**

Cela dépend des fonctionnalités que vous utilisez. Au minimum, Azure Stack HCI essaie de se synchroniser toutes les 12 heures. Si la synchronisation échoue, le contenu est conservé localement et envoyé lors de la prochaine synchronisation réussie. En outre, vous pouvez à tout moment effectuer une synchronisation manuelle à l'aide de la cmdlet PowerShell `Sync-AzureStackHCI` ou à partir de Windows Admin Center. Si vous activez d'autres services, les chargements peuvent être plus fréquents ; par exemple, Azure Log Analytics peut être chargé toutes les 5 minutes à des fins de surveillance.

### <a name="data-residency"></a>Résidence des données

**Où vont les informations synchronisées ?**

Azure Stack HCI se synchronise avec Azure et stocke les données dans un centre de données sécurisé et géré par Microsoft. Pour plus d’informations, consultez [Collecte de données d’Azure Stack HCI](concepts/data-collection.md) et [Résidence des données dans Azure](https://azure.microsoft.com/global-infrastructure/data-residency/).

### <a name="disconnected-or-air-gapped"></a>Déconnecté

**Puis-je utiliser Azure Stack HCI sans jamais me connecter à Azure ?**

Non. Azure Stack HCI doit se synchroniser avec Azure une fois tous les 30 jours consécutifs.

**Puis-je transférer des données hors connexion entre une instance déconnectée d'Azure Stack HCI et Azure ?**

Non. En l'absence de connectivité réseau, il n'existe actuellement aucun mécanisme d'inscription et de synchronisation entre les sites locaux et Azure. Par exemple, vous ne pouvez pas transporter des certificats ou des données de facturation via un périphérique de stockage amovible. Si nous recevons suffisamment de demandes de la part de nos clients, nous sommes prêts à explorer une telle fonctionnalité à l'avenir. Faites-le nous savoir sur le [Forum de commentaires d'Azure Stack HCI ](https://feedback.azure.com/forums/929833-azure-stack-hci).

## <a name="azure-stack-hci-general-faqs"></a>FAQ d'ordre général sur Azure Stack HCI

**Quelle relation existe-t-il entre Azure Stack HCI et Windows Server ?**

Windows Server est la base de presque tous les produits Azure, et toutes les fonctionnalités que vous appréciez continuent d'être publiées et prises en charge dans Windows Server. L’offre initiale d’Azure Stack HCI était basée sur Windows Server 2019 et utilisait le modèle de licence Windows Server traditionnel. Aujourd’hui, Azure Stack HCI a son propre système d’exploitation et son propre modèle de licence basé sur un abonnement. Azure Stack HCI est recommandé pour déployer HCI localement, à l’aide du matériel validé par Microsoft et fourni par nos partenaires.

**Est-ce que je peux effectuer une mise à niveau depuis Windows Server 2019 vers Azure Stack HCI ?**

Il n’existe aucune mise à niveau sur place de Windows Server vers Azure Stack HCI pour l’instant. Restez en contact pour obtenir des conseils de migration spécifiques pour les clients qui exécutent des clusters hyperconvergés basés sur Windows Server 2019 et 2016.

**Quels services Azure puis-je connecter à Azure Stack HCI ?**

Pour obtenir une liste mise à jour des services Azure auxquels vous pouvez connecter Azure Stack HCI, consultez [Connexion de Windows Server aux services Azure hybrides](/windows-server/manage/windows-admin-center/azure/index).

**Quel est le point commun entre les solutions Azure Stack Hub et Azure Stack HCI ?**

Azure Stack HCI offre les mêmes technologies de calcul, de stockage et de réseau à définition logicielle basées sur la technologie Hyper-V qu’Azure Stack Hub. Les deux offres répondent à des critères de test et de validation rigoureux pour garantir la fiabilité et la compatibilité avec la plateforme matérielle sous-jacente.

**En quoi sont-ils différents ?**

Avec Azure Stack Hub, vous exécutez des services cloud localement. Vous pouvez utiliser des services Azure IaaS et PaaS localement pour créer et exécuter de façon cohérente des applications cloud en tout lieu, gérées localement avec le portail Azure.

Avec Azure Stack HCI, vous exécutez localement des charges de travail virtualisées, gérées avec Windows Admin Center et des outils Windows Server connus. Vous pouvez également vous connecter à des scénarios hybrides Azure comme la récupération de site cloud, la supervision, entre autres.

**Puis-je mettre à niveau Azure Stack HCI vers Azure Stack Hub ?**

Non, mais les clients peuvent migrer leurs charges de travail d’Azure Stack HCI vers Azure Stack Hub ou Azure.

**Comment identifier un serveur Azure Stack HCI ?**

Windows Admin Center répertorie le système d’exploitation dans la liste Toutes les connexions et à d’autres endroits, ou vous pouvez utiliser la commande PowerShell suivante pour rechercher le nom et la version du système d’exploitation.

```PowerShell
Get-ComputerInfo -Property 'osName', 'osDisplayVersion'
```

Voici un exemple de sortie :

```
OsName                    OSDisplayVersion
------                    ----------------
Microsoft Azure Stack HCI 20H2
```
