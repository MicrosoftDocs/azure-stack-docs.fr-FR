---
title: Informations de référence sur le point de terminaison privilégié Azure Stack Hub
description: Informations de référence sur le point de terminaison privilégié Azure Stack PowerShell
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 76eb340da04e9254bcf8d8a626822c65362f44d2
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486034"
---
# <a name="azure-stack-hub-privileged-endpoint-reference"></a>Informations de référence sur le point de terminaison privilégié Azure Stack Hub

Le point de terminaison privilégié Azure Stack PowerShell est une console PowerShell distante préconfigurée qui vous fournit suffisamment de fonctionnalités pour vous permettre d’effectuer une tâche requise. Le point de terminaison s’appuie sur PowerShell JEA (Just Enough Administration) pour exposer seulement un ensemble limité d’applets de commande.

## <a name="privilege-endpoint-cmdlets"></a>Applets de commande du point de terminaison privilégié

| Applet de commande | Description |
| --- | --- |
| [Close-PrivilegedEndpoint](Close-PrivilegedEndpoint.md) | Pas de description. |
| [Get-ActionStatus](Get-ActionStatus.md) | Obtient l’état de la dernière action pour l’opération avec le nom de fonction spécifié. |
| [Get-AzureStackLog](Get-AzureStackLog.md) | Obtient les journaux de différents rôles d’Azure Stack avec dépassement du délai d’expiration. |
| [Get-AzureStackStampInformation](Get-AzureStackStampInformation.md) | Obtient les informations du tampon. |
| [Get-AzureStackSupportConfiguration](Get-AzureStackSupportConfiguration.md) | Obtient les paramètres de configuration du service de support. |
| [Get-CloudAdminPasswordRecoveryToken](Get-CloudAdminPasswordRecoveryToken.md) | Pas de description. |
| [Get-CloudAdminUserList](Get-CloudAdminUserList.md) | Pas de description. |
| [Get-ClusterLog](Get-ClusterLog.md) | Pas de description. |
| [Get-GraphApplication](Get-GraphApplication.md) | Get-GraphApplication est une fonction wrapper qui permet d’obtenir des informations sur l’application Graph pour le nom ou l’identificateur de l’application spécifié. |
| [Get-StorageJob](Get-StorageJob.md) | Pas de description. |
| [Get-SupportSessionInfo](Get-SupportSessionInfo.md) | Pas de description. |
| [Get-SupportSessionToken](Get-SupportSessionToken.md) | Pas de description. |
| [Get-SyslogClient](Get-SyslogClient.md) | Obtient les paramètres du client Syslog. |
| [Get-SyslogServer](Get-SyslogServer.md) | Obtient le point de terminaison du serveur Syslog. |
| [Get-ThirdPartyNotices](Get-ThirdPartyNotices.md) | Pas de description. |
| [Get-TLSPolicy](Get-TLSPolicy.md) | Pas de description. |
| [Get-VirtualDisk](Get-VirtualDisk.md) | Pas de description. |
| [Invoke-AzureStackOnDemandLog](Invoke-AzureStackOnDemandLog.md) | Génère des journaux à la demande à partir des rôles Azure Stack, là où c’est applicable. |
| [New-AzureBridgeServicePrincipal](New-AzureBridgeServicePrincipal.md) | Crée un principal du service dans Azure Active Directory. |
| [New-AzureStackActivation](New-AzureStackActivation.md) | Active Azure Stack. |
| [New-CloudAdminUser](New-CloudAdminUser.md) | Pas de description. |
| [New-GraphApplication](New-GraphApplication.md) | New-GraphApplication est une fonction wrapper pour appeler des applets de commande Graph ADFS sur AD FS. |
| [New-RegistrationToken](New-RegistrationToken.md) | Crée un nouveau jeton d’inscription |
| [Register-CustomAdfs](Register-CustomAdfs.md) | Script pour inscrire un service ADFS (Active Directory Federation Service) personnalisé en tant que fournisseur de revendications auprès d’Azure Stack AD FS. |
| [Register-CustomDnsServer](Register-CustomDnsServer.md) | Script pour inscrire des serveurs DNS personnalisés auprès d’Azure Stack DNS. |
| [Register-DirectoryService](Register-DirectoryService.md) | Script pour inscrire un annuaire Active Directory d’un client auprès du service Graph. |
| [Remove-AzureStackActivation](Remove-AzureStackActivation.md) | Pas de description. |
| [Remove-CloudAdminUser](Remove-CloudAdminUser.md) | Pas de description. |
| [Remove-GraphApplication](Remove-GraphApplication.md) | Remove-GraphApplication est une fonction wrapper pour appeler des applets de commande Graph ADFS sur AD FS. |
| [Repair-VirtualDisk](Repair-VirtualDisk.md) | Pas de description. |
| [Reset-DatacenterIntegrationConfiguration](Reset-DatacenterIntegrationConfiguration.md) | Script pour réinitialiser des modifications de l’intégration du centre de données. |
| [Send-AzureStackDiagnosticLog](Send-AzureStackDiagnosticLog.md) | Envoie les journaux de diagnostic Azure Stack à Microsoft. |
| [Set-CloudAdminUserPassword](Set-CloudAdminUserPassword.md) | Pas de description. |
| [Set-GraphApplication](Set-GraphApplication.md) | Set-GraphApplication est une fonction wrapper pour appeler des applets de commande Graph ADFS sur AD FS. |
| [Set-ServiceAdminOwner](Set-ServiceAdminOwner.md) | Script pour mettre à jour l’administrateur du service. |
| [Set-SyslogClient](Set-SyslogClient.md) | Importe et applique le certificat du point de terminaison du client Syslog. |
| [Set-SyslogServer](Set-SyslogServer.md) | Définit le point de terminaison du serveur Syslog. |
| [Set-Telemetry](Set-Telemetry.md) | Active ou désactive le transfert des données de télémétrie à Microsoft. |
| [Set-TLSPolicy](Set-TLSPolicy.md) | Pas de description. |
| [Start-AzureStack](Start-AzureStack.md) | Démarre tous les services Azure Stack. |
| [Start-SecretRotation](Start-SecretRotation.md) | Déclenche la rotation de secret sur un tampon. |
| [Stop-AzureStack](Stop-AzureStack.md) | Arrête tous les services Azure Stack. |
| [Test-AzureStack](Test-AzureStack.md) | Valide l’état du système Azure Stack. |
| [Unlock-SupportSession](Unlock-SupportSession.md) | Pas de description. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le point de terminaison privilégié sur Azure Stack Hub, consultez [Utilisation du point de terminaison privilégié dans Azure Stack](../../operator/azure-stack-privileged-endpoint.md).
