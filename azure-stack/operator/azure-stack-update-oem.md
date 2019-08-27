---
title: Appliquer une mise à jour de fabricant d’ordinateurs (OEM) à Azure Stack | Microsoft Docs
description: Apprenez à appliquer une mise à jour de fabricant d’ordinateurs (OEM) à Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2019
ms.author: mabrigg
ms.lastreviewed: 08/15/2019
ms.reviewer: ppacent
ms.openlocfilehash: 1342eb503abb81308740c0103b1d54887a46cf85
ms.sourcegitcommit: f62d58ae724020a24fa5905b6663abb5f1d62178
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69520927"
---
# <a name="apply-azure-stack-original-equipment-manufacturer-oem-updates"></a>Appliquer des mises à jour de fabricants d’ordinateurs (OEM) à Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Vous pouvez appliquer des mises à jour de fabricant d’ordinateur (OEM) à vos composants matériels Azure Stack pour bénéficier d’améliorations de pilotes et de microprogrammes ainsi que de correctifs de sécurité tout en réduisant l’impact sur vos utilisateurs. Dans cet article, vous pouvez en savoir plus sur les mises à jour OEM, les informations de contact OEM et l’application d’une mise à jour OEM.

## <a name="overview-of-oem-updates"></a>Vue d’ensemble des mises à jour OEM

Outre les mises à jour Microsoft Azure Stack, de nombreux OEM publient également des mises à jour régulières pour votre matériel Azure Stack, telles que des mises à jour de pilotes et de microprogrammes. Elles sont appelées **Mises à jour de packages OEM**. Pour savoir si votre OEM publie des mises à jour de packages OEM, consultez la [documentation Azure Stack de votre OEM](#oem-contact-information).

Ces mises à jour de packages OEM sont téléchargées dans le compte de stockage**updateadminaccount** et appliquées via le portail d’administration Azure Stack. Pour plus d’informations, consultez [Application de mises à jour OEM](#apply-oem-updates).

Consultez votre fabricant de matériel OEM au sujet de son processus de notification pour vous assurer que votre organisation reçoit bien les notifications de mise à jour du package OEM.

Certains fournisseurs de matériel peuvent nécessiter une *machine virtuelle de fournisseur de matériel* qui gère le processus de mise à jour du microprogramme interne. Pour plus d’informations, consultez [Configurer la machine virtuelle du fournisseur de matériel](#configure-hardware-vendor-vm)

## <a name="oem-contact-information"></a>Informations de contact OEM 

Cette section contient des informations de contact OEM et des liens vers des documents de référence OEM Azure Stack.

| Partenaire matériel | Région | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Tous | [Guide des opérations Cisco Integrated System pour Microsoft Azure Stack](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[Notes de publication pour Cisco Integrated System pour Microsoft Azure Stack](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | Tous | [Cloud pour Microsoft Azure Stack 14G (compte et connexion requis)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud pour Microsoft Azure Stack 13G (compte et connexion requis)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPON | [Support technique de service géré Fujitsu (compte et connexion requis)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA | [Support des produits et systèmes informatiques Fujitsu](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  | EU | [MySupport Fujitsu (compte et connexion requis)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | Tous | [HPE ProLiant pour Microsoft Azure Stack](http://www.hpe.com/info/MASupdates) |
| Lenovo | Tous | [Meilleures recettes ThinkAgile SXM](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [Package OEM/microprogramme](https://drive.terracloud.de/dl/fiTdTb66mwDAJWgUXUW8KNsd/OEM)<br>[documentation Terra Azure Stack (y compris FRU)](https://drive.terracloud.de/dl/fiWGZwCySZSQyNdykXCFiVCR/TerraAzSDokumentation)

## <a name="apply-oem-updates"></a>Appliquer les mises à jour OEM

Installez les packages OEM en suivant les étapes ci-dessous :

1. Vous devez contacter votre OEM pour :
      - déterminer la version actuelle de votre package OEM ;  
      - rechercher la meilleure méthode pour télécharger votre package OEM.  
2. Préparez votre package OEM avec les étapes décrites dans [Télécharger des packages de mise à jour pour les systèmes intégrés](azure-stack-servicing-policy.md#download-update-packages-for-integrated-systems).
3. Appliquez les mises à jour à l’aide des étapes décrites dans [Appliquer des mises à jour dans Azure Stack](azure-stack-apply-updates.md).

## <a name="configure-hardware-vendor-vm"></a>Configurer la machine virtuelle du fournisseur de matériel

Certains fournisseurs de matériel peuvent nécessiter une machine virtuelle pour faciliter le processus de mise à jour OEM. Votre fournisseur de matériel sera responsable de la création de ces machines virtuelles et de la documentation indiquant si vous avez besoin de `ProxyVM` ou de `HardwareManager` pour **-VMType** lors de l’exécution de l’applet de commande  **Set-OEMExternalVM**. Une fois les machines virtuelles créées, configurez-les avec **Set-OEMExternalVM** à partir du point de terminaison privilégié.

Pour plus d’informations sur le point de terminaison privilégié sur Azure Stack, voir [Utilisation du point de terminaison privilégié dans Azure Stack](azure-stack-privileged-endpoint.md).

1.  Accédez au point de terminaison privilégié.

    ```powershell  
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS>
    -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```

2. Configurez la machine virtuelle du fournisseur de matériel avec l’applet de commande **Set-OEMExternalVM**. L’applet de commande valide l’adresse IP et les informations d’identification pour **-VMType** `ProxyVM`. Pour **-VMType** `HardwareManager`, l’applet de commande ne valide pas l’entrée .

    ```powershell  
    
    Invoke-Command -Session $session
        { 
    Set-OEMExternalVM -VMType <Either "ProxyVM" or "HardwareManager">
        -IPAddress <IP Address of hardware vendor VM>
        }
    ```

## <a name="next-steps"></a>Étapes suivantes

[Mises à jour d’Azure Stack](azure-stack-updates.md)