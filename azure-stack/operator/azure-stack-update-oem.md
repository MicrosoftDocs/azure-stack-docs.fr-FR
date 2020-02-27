---
title: Appliquer une mise à jour de fabricant d’ordinateurs (OEM) à Azure Stack Hub
description: Apprenez à appliquer une mise à jour de fabricant d’ordinateurs (OEM) à Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 10/15/2019
ms.author: inhenkel
ms.lastreviewed: 08/15/2019
ms.reviewer: ppacent
ms.openlocfilehash: 82c2af02b26e9a661e600b6296b19cf4b0ba802c
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77508937"
---
# <a name="apply-azure-stack-hub-original-equipment-manufacturer-oem-updates"></a>Appliquer des mises à jour de fabricants d’ordinateurs (OEM) à Azure Stack Hub

Vous pouvez appliquer des mises à jour de fabricant d’ordinateur (OEM) à vos composants matériels Azure Stack Hub pour bénéficier des améliorations apportées aux pilotes et aux microprogrammes ainsi que des correctifs logiciels de sécurité, tout en réduisant l’impact sur vos utilisateurs. Dans cet article, vous pouvez en savoir plus sur les mises à jour OEM, les informations de contact OEM et l’application d’une mise à jour OEM.

## <a name="overview-of-oem-updates"></a>Vue d’ensemble des mises à jour OEM

Outre les mises à jour Microsoft Azure Stack Hub, de nombreux OEM publient également des mises à jour régulières pour votre matériel Azure Stack Hub, telles que des mises à jour de pilotes et de microprogrammes. Elles sont appelées **Mises à jour de packages OEM**. Pour savoir si votre OEM publie des mises à jour de packages OEM, consultez la [documentation Azure Stack Hub de votre OEM](#oem-contact-information).

Ces mises à jour de package OEM sont chargées dans le compte de stockage **updateadminaccount** et appliquées via le portail administrateur Azure Stack Hub. Pour plus d’informations, consultez [Application de mises à jour OEM](#apply-oem-updates).

Consultez votre fabricant de matériel OEM au sujet de son processus de notification pour vous assurer que votre organisation reçoit bien les notifications de mise à jour du package OEM.

Certains fournisseurs de matériel peuvent nécessiter une *machine virtuelle de fournisseur de matériel* qui gère le processus de mise à jour du microprogramme interne. Pour plus d’informations, consultez [Configurer la machine virtuelle du fournisseur de matériel](#configure-hardware-vendor-vm)

## <a name="oem-contact-information"></a>Informations de contact OEM 

Cette section contient des informations de contact OEM et des liens vers des documents de référence OEM Azure Stack Hub.

| Partenaire matériel | Région | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Tous | [Guide des opérations Cisco Integrated System pour Microsoft Azure Stack Hub](https://aka.ms/aa708e2)<br><br>[UCS C-Series Rack-Mount UCS-Managed Server Software](https://aka.ms/aa700rq) |
| Dell EMC | Tous | [Cloud pour Microsoft Azure Stack Hub 14G (compte et connexion requis)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud pour Microsoft Azure Stack Hub 13G (compte et connexion requis)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPON | [Support technique de service géré Fujitsu (compte et connexion requis)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EMEA ET ÉTATS-UNIS | [Support des produits et systèmes informatiques Fujitsu](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
| HPE | Tous | [HPE ProLiant pour Microsoft Azure Stack Hub](http://www.hpe.com/info/MASupdates) |
| Lenovo | Tous | [Meilleures recettes ThinkAgile SXM](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [Package OEM/microprogramme](https://aka.ms/AA6z600)<br>[Documentation Terra Azure Stack Hub (y compris FRU)](https://aka.ms/aa6zktc)

## <a name="apply-oem-updates"></a>Appliquer les mises à jour OEM

Installez les packages OEM en suivant les étapes ci-dessous :

> [!IMPORTANT]
> Avant d’appliquer des mises à jour dans Azure Stack Hub, assurez-vous que vous avez effectué **TOUTES** les étapes répertoriées dans la [liste de contrôle préalable à la mise à jour](release-notes-checklist.md) et que vous avez planifié une fenêtre de maintenance appropriée pour le type de mise à jour que vous appliquez.

1. Vous devez contacter votre OEM pour :
      - déterminer la version actuelle de votre package OEM ;  
      - rechercher la meilleure méthode pour télécharger votre package OEM.  
2. Avant d’appliquer une mise à jour de package OEM, vous devez toujours appliquer le dernier correctif logiciel Azure Stack Hub disponible à la version actuelle d’Azure Stack Hub sur votre système. Pour plus d’informations sur les correctifs logiciels, consultez [Correctifs logiciels Azure Stack Hub](azure-stack-servicing-policy.md).
3. Préparez votre package OEM avec les étapes décrites dans [Télécharger des packages de mise à jour pour les systèmes intégrés](azure-stack-servicing-policy.md).
4. Appliquez les mises à jour en suivant les étapes décrites dans [Appliquer les mises à jour dans Azure Stack Hub](azure-stack-apply-updates.md).

## <a name="configure-hardware-vendor-vm"></a>Configurer la machine virtuelle du fournisseur de matériel

Certains fournisseurs de matériel peuvent nécessiter une machine virtuelle pour faciliter le processus de mise à jour OEM. Votre fournisseur de matériel sera responsable de la création de ces machines virtuelles et de la documentation indiquant si vous avez besoin de `ProxyVM` ou de `HardwareManager` pour **-VMType** lors de l’exécution de l’applet de commande  **Set-OEMExternalVM** ainsi que des informations d'identification à utiliser pour **-Credential**. Une fois les machines virtuelles créées, configurez-les avec **Set-OEMExternalVM** à partir du point de terminaison privilégié.

Pour plus d’informations sur le point de terminaison privilégié sur Azure Stack Hub, consultez [Utilisation du point de terminaison privilégié dans Azure Stack Hub](azure-stack-privileged-endpoint.md).

1.  Accédez au point de terminaison privilégié.

    ```powershell  
    $cred = Get-Credential
    $session = New-PSSession -ComputerName <IP Address of ERCS>
    -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```

2. Configurez la machine virtuelle du fournisseur de matériel avec l’applet de commande **Set-OEMExternalVM**. L’applet de commande valide l’adresse IP et les informations d’identification pour **-VMType**`ProxyVM`. Pour **-VMType**`HardwareManager`, l’applet de commande ne valide pas l’entrée. Le paramètre **-Credential** fourni à **Set-OEMExternalVM** est un paramètre qui sera clairement documenté par la documentation du fournisseur de matériel.  Il ne s’agit pas des informations d’identification CloudAdmin utilisées avec le point de terminaison privilégié ou d’autres informations d’identification Azure Stack Hub existantes.

    ```powershell  
    $VmCred = Get-Credential
    Invoke-Command -Session $session
        { 
    Set-OEMExternalVM -VMType <Either "ProxyVM" or "HardwareManager">
        -IPAddress <IP Address of hardware vendor VM> -Credential $using:VmCred
        }
    ```

## <a name="next-steps"></a>Étapes suivantes

[Mises à jour Azure Stack Hub](azure-stack-updates.md)
