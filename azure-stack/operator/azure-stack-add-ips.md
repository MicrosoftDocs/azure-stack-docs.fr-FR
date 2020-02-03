---
title: Ajouter des adresses IP publiques dans Azure Stack Hub
description: Apprenez à ajouter des adresses IP publiques dans Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 09/10/2019
ms.author: justinha
ms.reviewer: scottnap
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: e047f50dbdb9e7c645351fd83f9f1551f75c157e
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76875163"
---
# <a name="add-public-ip-addresses"></a>Ajouter des adresses IP publiques

Dans cet article, nous faisons référence aux adresses externes en tant qu’adresses IP publiques. Dans le contexte d’Azure Stack Hub, une adresse IP publique est une adresse IP accessible à partir de l’extérieur d’Azure Stack Hub. Que ce réseau externe soit routable sur l’Internet public ou sur un intranet, et qu’il utilise un espace d’adressage privée, n’est pas vraiment important dans cet article : les étapes sont les mêmes.

> [!IMPORTANT]
> Les étapes décrites dans cet article s’appliquent uniquement aux systèmes qui ont été déployés à l’aide de la boîte à outils partenaire version 1809 ou ultérieure. Sur les systèmes qui ont été déployés avant la version 1809, vous devez mettre à jour les listes de contrôle d’accès (ACL) du commutateur ToR de façon à autoriser la nouvelle plage de pools d’adresses IP virtuelles publiques. Si vous exécutez des configurations de commutateur plus anciennes, contactez votre OEM pour qu’il vous aide à ajouter les listes de contrôle d’accès « PERMIT » appropriées au nouveau pool d’adresses IP publiques, ou à reconfigurer votre commutateur à l’aide de la dernière boîte à outils partenaire pour empêcher que les nouvelles adresses IP publiques ne soient bloquées.

## <a name="add-a-public-ip-address-pool"></a>Ajouter un pool d’adresses IP publiques
Vous pouvez ajouter des adresses IP publiques à votre système Azure Stack Hub n’importe quand après le déploiement initial dudit système. Découvrez comment [afficher la consommation d’adresses IP publiques](azure-stack-viewing-public-ip-address-consumption.md) pour voir l’utilisation actuelle et la disponibilité d’adresses IP publiques sur Azure Stack Hub.

À un niveau élevé, le processus d’ajout d’un nouveau bloc d’adresses IP publiques dans Azure Stack Hub ressemble à ceci :

 ![Ajouter des flux IP](media/azure-stack-add-ips/flow.PNG)

## <a name="obtain-the-address-block-from-your-provider"></a>Obtenir le bloc d’adresses de votre fournisseur
Vous devez en premier lieu obtenir le bloc d’adresses que vous souhaitez ajouter à Azure Stack Hub. En fonction d’où vous obtenez votre bloc d’adresses, réfléchissez au délai et comparez-le à votre taux de consommation des adresses IP publiques dans Azure Stack Hub.

> [!IMPORTANT]
> Azure Stack Hub accepte n’importe quel bloc d’adresses que vous fournissez, tant qu’il s’agit d’un bloc valide qui ne chevauche pas une plage d’adresses existante dans Azure Stack Hub. Veillez à obtenir un bloc d’adresses valide routable et ne chevauchant pas le réseau externe auquel Azure Stack Hub est connecté. Une fois que vous avez ajouté la plage à Azure Stack Hub, vous ne pouvez plus la retirer.

## <a name="add-the-ip-address-range-to-azure-stack-hub"></a>Ajouter la plage d’adresses IP à Azure Stack Hub

1. Dans un navigateur, accédez au tableau de bord de votre portail administrateur. Pour cet exemple, nous allons utiliser https://adminportal.local.azurestack.external.
2. Connectez-vous au portail administrateur Azure Stack Hub en tant qu’opérateur cloud.
3. Dans le tableau de bord par défaut, recherchez la liste Gestion des régions et sélectionnez sur la région que vous voulez gérer. Pour cet exemple, nous allons utiliser « local ».
4. Trouvez la vignette Fournisseurs de ressources et cliquez sur Fournisseur de ressources réseau.
5. Cliquez sur la vignette Utilisation des pools IP publics.
6. Cliquez sur le bouton Ajouter un pool IP.
7. Donnez un nom au pool IP. Le nom que vous choisissez vous aide à identifier facilement le pool d’adresses IP. Il est conseillé de donner le même nom que la plage d’adresses, mais cela n’est pas obligatoire.
8. Entrez le bloc d’adresses que vous voulez ajouter dans la notation CIDR. Par exemple :  192.168.203.0/24
9. Lorsque vous fournissez une plage CIDR valide dans le champ Plage d’adresses (bloc CIDR), les champs Adresse IP de début, Adresse IP de fin et Adresses IP disponibles sont automatiquement renseignés. Ils sont en lecture seule et générés automatiquement. Vous ne pouvez donc pas les modifier sans modifier la valeur dans le champ Plage d’adresses.
10. Une fois que vous avez vérifié que les informations du panneau sont correctes, sélectionnez **OK** pour valider les modifications et ajouter la plage d’adresses à Azure Stack Hub.


## <a name="next-steps"></a>Étapes suivantes 
[Examiner les actions de nœud d’unité d’échelle](azure-stack-node-actions.md).
