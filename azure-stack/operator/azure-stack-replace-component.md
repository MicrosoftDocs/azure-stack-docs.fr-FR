---
title: Remplacer un composant matériel sur un nœud d’unité d’échelle Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Découvrez comment remplacer un composant matériel sur un système intégré Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 07/18/2019
ms.openlocfilehash: 2755daf33f1bd2bd4bc02858821ae0727df71042
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90572594"
---
# <a name="replace-a-hardware-component-on-an-azure-stack-hub-scale-unit-node"></a>Remplacer un composant matériel sur un nœud d’unité d’échelle Azure Stack Hub

Cet article décrit le processus général de remplacement des composants matériels qui ne sont pas échangeables à chaud. Les étapes de remplacement réelles varient en fonction du revendeur de votre matériel OEM. Pour obtenir des instructions détaillées spécifiques pour votre système intégré Azure Stack Hub, voir la documentation FRU (Field Replaceable Unit) de votre fournisseur.

> [!CAUTION]  
> Le nivellement du microprogramme est essentiel pour la réussite de l’opération décrite dans cet article. Le fait d'ignorer cette étape peut entraîner une instabilité du système, une baisse des performances, ainsi que des threads de sécurité, ou empêcher la fonctionnalité d'automatisation d'Azure Stack Hub de déployer le système d'exploitation. Lors du remplacement du matériel, consultez toujours la documentation de votre partenaire de matériel pour vous assurer que le microprogramme appliqué correspond à la version OEM affichée dans le [portail administrateur Azure Stack Hub](azure-stack-updates.md).

| Partenaire matériel | Région | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Tous | [Guide des opérations Cisco Integrated System pour Microsoft Azure Stack Hub](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[Notes de publication de Cisco Integrated System pour Microsoft Azure Stack Hub](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | Tous | [Cloud pour Microsoft Azure Stack Hub 14G (compte et connexion obligatoires)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud pour Microsoft Azure Stack Hub 13G (compte et connexion obligatoires)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPON | [Support technique de service managé Fujitsu (compte et connexion obligatoires)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA | [Support des produits et systèmes informatiques Fujitsu](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  | EU | [MySupport Fujitsu (compte et connexion obligatoires)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | Tous | [HPE ProLiant pour Microsoft Azure Stack Hub](http://www.hpe.com/info/MASupdates) |
| Lenovo | Tous | [Meilleures recettes ThinkAgile SXM](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [Package OEM/microprogramme](https://aka.ms/AA6z600)<br>[Documentation Terra Azure Stack Hub (y compris FRU)](https://aka.ms/aa6zktc)

Les composants non échangeables à chaud incluent les éléments suivants :

- UC*
- Mémoire*
- Contrôleur de gestion de la carte mère/carte de base (BMC)/carte vidéo
- Contrôleur de disque/adaptateur ou carte de bus hôte (HBA)/fond de panier
- Carte réseau (NIC)
- Disque de système d’exploitation*
- Lecteurs de données (ne prenant pas en charge l’échange à chaud, tels que les cartes complémentaires PCI-e)*

*Ces composants peuvent éventuellement supporter un échange à chaud, mais cela dépend de la manière dont le fournisseur les implémente. Pour obtenir des instructions détaillées, consultez la documentation FRU de votre fabricant OEM.

L’organigramme suivant illustre le processus FRU général de remplacement d’un composant matériel non échangeable à chaud.

![Organigramme montrant le flux de remplacement de composant](media/azure-stack-replace-component/replacecomponentflow.PNG)

* Cette action n’est peut-être pas requise. Elle dépend de l’état du matériel.

** Votre fournisseur de matériel OEM peut ou non échanger le composant et mettre à jour le microprogramme. Cela dépend de votre contrat de support technique.

## <a name="review-alert-information"></a>Examiner les informations sur l’alerte

Le système de contrôle d’intégrité et de surveillance d’Azure Stack Hub effectue le suivi de l’intégrité des cartes réseau et des lecteurs de données contrôlés par les espaces de stockage direct. Il n’effectue pas le suivi des autres composants matériels. Pour tous les autres composants matériels, des alertes sont générées dans la solution de supervision du matériel spécifique du fournisseur, qui s’exécute sur l’hôte de cycle de vie du matériel.  

## <a name="component-replacement-process"></a>Processus de remplacement de composant

Les étapes suivantes fournissent une vue d’ensemble du processus de remplacement de composant. Ne suivez pas ces étapes sans vous référer à la documentation FRU fournie par le fabricant OEM.

1. Utilisez l’action Arrêter pour arrêter normalement le nœud d’unité d’échelle. Cette action n’est peut-être pas requise. Elle dépend de l’état du matériel.

2. Dans le cas peu probable où l’action d’arrêt ne fonctionnerait pas, utilisez l’action [Vider](azure-stack-node-actions.md#drain) pour placer le nœud d’unité d’échelle en mode maintenance. Cette action n’est peut-être pas requise. Elle dépend de l’état du matériel.

   > [!NOTE]  
   > Dans tous les cas, un seul nœud peut être désactivé et mis hors tension en même temps sans endommager les espaces de stockage direct (S2D).

3. Une fois le nœud d’unité d’échelle en mode maintenance, utilisez l’action [Mettre hors tension](azure-stack-node-actions.md#scale-unit-node-actions). Cette action n’est peut-être pas requise. Elle dépend de l’état du matériel.

   > [!NOTE]  
   > Dans le cas peu probable où la mise hors tension ne fonctionnerait pas, utilisez l’interface web du contrôleur de gestion de la carte de base (BMC).

4. Remplacez le composant matériel endommagé. Votre fournisseur de matériel OEM peut ou non échanger le composant. Cela dépend de votre contrat de support technique.  
5. Mettez à jour le microprogramme. Suivez le processus de mise à jour du microprogramme spécifique de votre fournisseur à l’aide de l’hôte de cycle de vie du matériel pour vous assurer que le niveau de microprogramme approuvé est appliqué au composant matériel remplacé. Votre fournisseur de matériel OEM peut ou non effectuer cette opération. Cela dépend de votre contrat de support technique.  
6. Utilisez l’action [Réparation](azure-stack-node-actions.md#scale-unit-node-actions) afin de ramener le nœud d’unité d’échelle dans l’unité d’échelle.
7. Utilisez le point de terminaison privilégié pour [vérifier l’état de réparation du disque virtuel](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint). Avec de nouveaux lecteurs de données, une opération de réparation de stockage complète peut prendre plusieurs heures en fonction de la charge du système et de l’espace utilisé.
8. Une fois la réparation terminée, vérifiez que toutes les alertes actives ont été automatiquement fermées.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le remplacement d’un disque physique échangeable à chaud, voir [Remplacer un disque](azure-stack-replace-disk.md).
- Pour plus d’informations sur le remplacement d’un nœud physique, voir [Remplacer un nœud d’unité d’échelle](azure-stack-replace-node.md).
