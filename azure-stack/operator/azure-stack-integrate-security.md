---
title: Intégrer Azure Stack Hub avec des solutions de supervision via un transfert Syslog
description: Découvrez comment intégrer Azure Stack Hub à des solutions de supervision avec le transfert Syslog.
author: PatAltimore
ms.topic: article
ms.date: 01/10/2020
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 06/15/2020
ms.openlocfilehash: 9a6da457b32bf9a224a906bc24ec65c7adcf1fab
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870407"
---
# <a name="integrate-azure-stack-hub-with-monitoring-solutions-using-syslog-forwarding"></a>Intégrer Azure Stack Hub à des solutions de supervision avec le transfert Syslog

Cet article vous montre comment utiliser Syslog pour intégrer l’infrastructure Azure Stack Hub avec une ou plusieurs solutions de sécurité externes déjà déployées dans votre centre de données. Par exemple, un système SIEM (Security Information and Event Management). Le canal syslog présente des audits, des alertes et des journaux d’activité de sécurité provenant de tous les composants de l’infrastructure Azure Stack Hub. Utilisez le transfert Syslog pour intégrer des solutions de supervision de la sécurité et pour récupérer la totalité des audits, alertes et journaux de sécurité afin de les stocker et les conserver.

À compter de la mise à jour 1809, Azure Stack Hub dispose d’un client syslog intégré qui, une fois configuré, émet des messages syslog avec la charge utile au format CEF (Common Event Format).

Le diagramme suivant décrit l’intégration d’Azure Stack Hub avec un SIEM externe. Deux modèles d’intégration doivent être pris en compte : le premier (en bleu) correspond à l’infrastructure Azure Stack Hub qui englobe les machines virtuelles d’infrastructure et les nœuds Hyper-V. Tous les audits, journaux d’activité de sécurité et alertes provenant de ces composants sont recueillis et exposés de manière centralisée par le biais de syslog avec une charge utile au format CEF. Ce modèle d’intégration est décrit dans cette page de document.
Le second modèle d’intégration est celui représenté en orange. Il couvre les contrôleurs de gestion de la carte de base (BMC), l’hôte de cycle de vie du matériel (HLH), les machines virtuelles et les appliances virtuelles qui exécutent le logiciel de gestion et de supervision du partenaire matériel, et les commutateurs TOR (Top Of Rack). Ces composants étant propres au partenaire matériel, contactez celui-ci pour obtenir une documentation sur la façon de les intégrer à un SIEM externe.

![Diagramme de transfert de Syslog](media/azure-stack-integrate-security/azure-stack-hub-syslog-forwarding-diagram_bg.svg)

## <a name="configuring-syslog-forwarding"></a>Configuration du transfert de Syslog

Dans Azure Stack Hub, le client syslog prend en charge les configurations suivantes :

1. **Syslog sur TCP, avec authentification mutuelle (client et serveur) et chiffrement TLS 1.2 :** Dans cette configuration, le serveur syslog et le client syslog peuvent tous les deux vérifier l’identité de l’autre par le biais de certificats. Les messages sont envoyés via un canal chiffré TLS 1.2.

2. **Syslog sur TCP avec authentification du serveur et chiffrement TLS 1.2 :** Dans cette configuration, le client syslog peut vérifier l’identité du serveur syslog par le biais d’un certificat. Les messages sont envoyés via un canal chiffré TLS 1.2.

3. **Syslog sur TCP, sans chiffrement :** Dans cette configuration, les identités du client Syslog et du serveur Syslog ne sont pas vérifiées. Les messages sont envoyés en texte clair via TCP.

4. **Syslog sur UDP, sans chiffrement :** Dans cette configuration, les identités du client Syslog et du serveur Syslog ne sont pas vérifiées. Les messages sont envoyés en texte clair via UDP.

> [!IMPORTANT]
> Microsoft recommande d’utiliser le protocole TCP avec authentification et chiffrement (configuration #1 ou, au minimum, la configuration #2) pour les environnements de production afin de vous protéger contre les attaques de type interception (man-in-the-middle) et d’écoute des messages.

### <a name="cmdlets-to-configure-syslog-forwarding"></a>Applets de commande pour configurer le transfert de Syslog
La configuration du transfert de Syslog requiert l’accès au point de terminaison privilégié (PEP). Deux applets de commande PowerShell ont été ajoutées au PEP pour configurer le transfert de Syslog :


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-ServerPort <UInt16>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] [-OutputSeverity]
```
#### <a name="cmdlets-parameters"></a>Paramètres des applets de commande

Paramètres pour l’applet de commande *Set-SyslogServer* :

| Paramètre | Description | Type | Obligatoire |
|---------|---------|---------|---------|
|*ServerName* | Nom de domaine complet ou adresse IP du serveur Syslog. | String | Oui|
|*ServerPort* | Numéro du port où le serveur Syslog est à l’écoute. | UInt16 | Oui|
|*NoEncryption*| Forcer le client à envoyer des messages Syslog en texte clair. | indicateur | non|
|*SkipCertificateCheck*| Ignorer la validation du certificat fourni par le serveur Syslog pendant la négociation TLS initiale. | indicateur | non|
|*SkipCNCheck*| Ignorer la validation de la valeur Nom courant du certificat fourni par le serveur Syslog pendant la négociation TLS initiale. | indicateur | non|
|*UseUDP*| Utiliser Syslog avec le protocole de transport UDP. |indicateur | non|
|*Remove*| Supprimer du client la configuration du serveur et arrêter le transfert Syslog.| indicateur | non|

Paramètres pour l’applet de commande *Set-SyslogClient* :

| Paramètre | Description | Type |
|---------|---------| ---------|
| *pfxBinary* | Contenu du fichier .pfx, comprenant le certificat à utiliser par le client comme identité pour s’authentifier auprès du serveur Syslog.  | Byte[] |
| *CertPassword* |  Mot de passe pour importer la clé privée associée au fichier .pfx. | SecureString |
|*RemoveCertificate* | Supprimer le certificat du client. | indicateur|
| *OutputSeverity* | Niveau de journalisation des résultats. Les valeurs sont **Par défaut** ou **Détails**. « Par défaut » comprend les niveaux de gravité suivants : avertissement, critique ou erreur. « Détails » inclut tous les niveaux de gravité : détails, informations, avertissement, critique ou erreur.  | String |
### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>Configuration du transfert de Syslog avec TCP, authentification mutuelle et chiffrement TLS 1.2

Dans cette configuration, le client syslog dans Azure Stack Hub transfère les messages au serveur syslog via TCP, avec chiffrement TLS 1.2. Pendant la négociation initiale, le client vérifie que le serveur fournit un certificat valide et approuvé. Le client fournit également un certificat au serveur comme preuve de son identité. Cette configuration est la plus sécurisée car elle fournit une validation complète de l’identité du client et du serveur, et envoie des messages via un canal chiffré.

> [!IMPORTANT]
> Microsoft recommande fortement d’utiliser cette configuration pour les environnements de production. 

Pour configurer le transfert de Syslog avec TCP, authentification mutuelle et chiffrement TLS 1.2, exécutez ces deux applets de commande sur une session PEP :

```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```

Le certificat client doit avoir la même racine que celui fourni lors du déploiement d’Azure Stack Hub. Il doit également contenir une clé privée.

```powershell
##Example on how to set your syslog client with the certificate for mutual authentication.
##This example script must be run from your hardware lifecycle host or privileged access workstation.

$ErcsNodeName = "<yourPEP>"
$password = ConvertTo-SecureString -String "<your cloudAdmin account password" -AsPlainText -Force
 
$cloudAdmin = "<your cloudAdmin account name>"
$CloudAdminCred = New-Object System.Management.Automation.PSCredential ($cloudAdmin, $password)
 
$certPassword = $password
$certContent = Get-Content -Path C:\cert\<yourClientCertificate>.pfx -Encoding Byte
 
$params = @{ 
    ComputerName = $ErcsNodeName 
    Credential = $CloudAdminCred 
    ConfigurationName = "PrivilegedEndpoint" 
}

$session = New-PSSession @params
 
$params = @{ 
    Session = $session 
    ArgumentList = @($certContent, $certPassword) 
}
Write-Verbose "Invoking cmdlet to set syslog client certificate..." -Verbose 
Invoke-Command @params -ScriptBlock { 
    param($CertContent, $CertPassword) 
    Set-SyslogClient -PfxBinary $CertContent -CertPassword $CertPassword }
```

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>Configuration du transfert de Syslog avec TCP, authentification du serveur et chiffrement TLS 1.2

Dans cette configuration, le client syslog dans Azure Stack Hub transfère les messages au serveur syslog via TCP, avec chiffrement TLS 1.2. Pendant la négociation initiale, le client vérifie également que le serveur fournit un certificat valide et approuvé. Cette configuration empêche le client d’envoyer des messages vers des destinations non approuvées.
TCP avec authentification et chiffrement est la configuration par défaut et représente le niveau minimal de sécurité que Microsoft recommande pour un environnement de production.

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
```

Si vous souhaitez tester l’intégration de votre serveur Syslog au client Azure Stack Hub en utilisant un certificat auto-signé ou non approuvé, vous pouvez utiliser ces indicateurs pour ignorer la validation du serveur effectuée par le client pendant la négociation initiale.

```powershell
 #Skip validation of the Common Name value in the server certificate. Use this flag if you provide an IP address for your syslog server
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCNCheck

 #Skip entirely the server certificate validation
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCertificateCheck
```

> [!IMPORTANT]
> Microsoft déconseille l’utilisation de l’indicateur -SkipCertificateCheck pour les environnements de production. 

### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>Configuration du transfert de Syslog avec TCP et aucun chiffrement

Dans cette configuration, le client syslog dans Azure Stack Hub transfère les messages au serveur syslog via TCP, sans chiffrement. Le client ne vérifie pas l’identité du serveur et ne lui fournit pas sa propre identité pour vérification.

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -NoEncryption
```

> [!IMPORTANT]
> Microsoft déconseille d’utiliser cette configuration pour les environnements de production.


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>Configuration du transfert de Syslog avec UDP et aucun chiffrement

Dans cette configuration, le client syslog dans Azure Stack Hub transfère les messages au serveur syslog via UDP, sans chiffrement. Le client ne vérifie pas l’identité du serveur et ne lui fournit pas sa propre identité pour vérification.

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -UseUDP
```

Si UDP sans chiffrement est le plus facile à configurer, il ne fournit aucune protection contre les attaques de l’intercepteur et d’écoute des messages.

> [!IMPORTANT]
> Microsoft déconseille d’utiliser cette configuration pour les environnements de production.


## <a name="removing-syslog-forwarding-configuration"></a>Suppression de la configuration du transfert de Syslog

Pour supprimer complètement de la configuration du serveur syslog et arrêter le transfert de Syslog :

**Supprimez la configuration du serveur syslog du client**

```powershell  
Set-SyslogServer -Remove
```

**Supprimez le certificat client du client**

```powershell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>Vérification de la configuration syslog

Si vous avez connecté avec succès le client syslog à votre serveur syslog, vous devriez commencer à recevoir des événements. Si vous ne voyez aucun événement, vérifiez la configuration de votre client Syslog en exécutant les applets de commande suivantes :

**Vérifiez la configuration du serveur dans le client syslog**

```powershell  
Get-SyslogServer
```

**Vérifiez la configuration du certificat dans le client syslog**

```powershell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Schéma du message syslog

Le transfert Syslog de l’infrastructure Azure Stack Hub envoie des messages au format CEF (Common Event Format).
Chaque message syslog est structuré selon ce schéma :

```Syslog
<Time> <Host> <CEF payload>
```

La charge utile CEF est basée sur la structure ci-dessous, mais le mappage pour chaque champ varie selon le type de message (événements Windows, alerte créée, alerte fermée).

```CEF
# Common Event Format schema
CEF: <Version>|<Device Vendor>|<Device Product>|<Device Version>|<Signature ID>|<Name>|<Severity>|<Extensions>
* Version: 0.0
* Device Vendor: Microsoft
* Device Product: Microsoft Azure Stack Hub
* Device Version: 1.0
```

### <a name="cef-mapping-for-privileged-endpoint-events"></a>Mappage CEF pour les événements de point de terminaison privilégié

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <PEP Event ID>
* Name: <PEP Task Name>
* Severity: mapped from PEP Level (details see the PEP Severity table below)
* Who: account used to connect to the PEP
* WhichIP: IP address of the device used to connect to the PEP
```

Tableau des événements pour le point de terminaison privilégié :

| Événement | ID d’événement PEP | Nom de tâche PEP | severity |
|-------|--------------| --------------|----------|
|PrivilegedEndpointAccessed|1 000|PrivilegedEndpointAccessedEvent|5|
|SupportSessionTokenRequested |1001|SupportSessionTokenRequestedEvent|5|
|SupportSessionDevelopmentTokenRequested |1002|SupportSessionDevelopmentTokenRequestedEvent|5|
|SupportSessionUnlocked |1003|SupportSessionUnlockedEvent|10|
|SupportSessionFailedToUnlock |1004|SupportSessionFailedToUnlockEvent|10|
|PrivilegedEndpointClosed |1005|PrivilegedEndpointClosedEvent|5|
|NewCloudAdminUser |1006|NewCloudAdminUserEvent|10|
|RemoveCloudAdminUser |1007|RemoveCloudAdminUserEvent|10|
|SetCloudAdminUserPassword |1008|SetCloudAdminUserPasswordEvent|5|
|GetCloudAdminPasswordRecoveryToken |1009|GetCloudAdminPasswordRecoveryTokenEvent|10|
|ResetCloudAdminPassword |1010|ResetCloudAdminPasswordEvent|10|
|PrivilegedEndpointSessionTimedOut |1017|PrivilegedEndpointSessionTimedOutEvent|5|

Tableau de sévérité PEP :

| severity | Level | Valeur numérique |
|----------|-------| ----------------|
|0|Indéfini|Valeur : 0. Indique les journaux d’activité à tous les niveaux|
|10|Critique|Valeur : 1. Indique les journaux d’activité pour une alerte critique|
|8|Error| Valeur : 2. Indique les journaux d’activité pour une erreur|
|5|Avertissement|Valeur : 3. Indique les journaux d’activité pour un avertissement|
|2|Information|Valeur : 4. Indique les journaux d’activité pour un message d’information|
|0|Commentaires|Valeur : 5. Indique les journaux d’activité à tous les niveaux|

### <a name="cef-mapping-for-recovery-endpoint-events"></a>Mappage CEF pour les événements de point de terminaison de récupération

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <REP Event ID>
* Name: <REP Task Name>
* Severity: mapped from REP Level (details see the REP Severity table below)
* Who: account used to connect to the REP
* WhichIP: IP address of the device used to connect to the REP
```

Tableau des événements pour le point de terminaison de récupération :

| Événement | ID d’événement REP | Nom de tâche REP | severity |
|-------|--------------| --------------|----------|
|RecoveryEndpointAccessed |1010|RecoveryEndpointAccessedEvent|5|
|RecoverySessionTokenRequested |1012|RecoverySessionTokenRequestedEvent |5|
|RecoverySessionDevelopmentTokenRequested |1013|RecoverySessionDevelopmentTokenRequestedEvent|5|
|RecoverySessionUnlocked |1014|RecoverySessionUnlockedEvent |10|
|RecoverySessionFailedToUnlock |1015|RecoverySessionFailedToUnlockEvent|10|
|RecoveryEndpointClosed |1016|RecoveryEndpointClosedEvent|5|

Tableau de sévérité REP :

| severity | Level | Valeur numérique |
|----------|-------| ----------------|
|0|Indéfini|Valeur : 0. Indique les journaux d’activité à tous les niveaux|
|10|Critique|Valeur : 1. Indique les journaux d’activité pour une alerte critique|
|8|Error| Valeur : 2. Indique les journaux d’activité pour une erreur|
|5|Avertissement|Valeur : 3. Indique les journaux d’activité pour un avertissement|
|2|Information|Valeur : 4. Indique les journaux d’activité pour un message d’information|
|0|Commentaires|Valeur : 5. Indique les journaux d’activité à tous les niveaux|

### <a name="cef-mapping-for-windows-events"></a>Mappage CEF pour les événements Windows

```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Tableau de sévérité pour les événements Windows :

| Valeur de gravité CEF | Niveau d’événement Windows | Valeur numérique |
|--------------------|---------------------| ----------------|
|0|Indéfini|Valeur : 0. Indique les journaux d’activité à tous les niveaux|
|10|Critique|Valeur : 1. Indique les journaux d’activité pour une alerte critique|
|8|Error| Valeur : 2. Indique les journaux d’activité pour une erreur|
|5|Avertissement|Valeur : 3. Indique les journaux d’activité pour un avertissement|
|2|Information|Valeur : 4. Indique les journaux d’activité pour un message d’information|
|0|Commentaires|Valeur : 5. Indique les journaux d’activité à tous les niveaux|

Table d’extension personnalisée pour les événements Windows dans Azure Stack Hub :

| Nom de l’extension personnalisée | Exemple d’événement Windows | 
|-----------------------|---------|
|MasChannel | Système|
|MasComputer | test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| svchost!!4132,G,0!!!!EseDiskFlushConsistency!!ESENT!!0x800000|
|MasEventDescription| Les paramètres de stratégie de groupe pour l’utilisateur ont été traités avec succès. Aucune modification n’a été détectée depuis le dernier traitement réussi de la stratégie de groupe.|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |Succès de l’audit|
|MasLevel |4|
|MasOpcode |1|
|MasOpcodeName |info|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |Microsoft-Windows-GroupPolicy|
|MasSecurityUserId |\<Windows SID\> |
|MasTask |0|
|MasTaskCategory| Création de processus|
|MasUserData|KB4093112!!5112!!Installed!!0x0!!WindowsUpdateAgent Xpath: /Event/UserData/*|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>Mappage CEF pour les alertes créées

```
* Signature ID: Microsoft Azure Stack Hub Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Tableau de gravité des alertes :

| Gravité | Level |
|----------|-------|
|0|Indéfini|
|10|Critique|
|5|Avertissement|

Table d’extension personnalisée pour les alertes créées dans Azure Stack Hub :

| Nom de l’extension personnalisée | Exemple | 
|-----------------------|---------|
|MasEventDescription|DESCRIPTION : Un compte d'utilisateur \<TestUser\> a été créé pour \<TestDomain\>. Il existe un risque potentiel de sécurité. --CORRECTION : contactez le support technique. L’Assistance client est nécessaire pour résoudre ce problème. N’essayez pas de résoudre ce problème sans son assistance. Avant d’ouvrir une demande de support, démarrez le processus de collecte du fichier journal en utilisant les instructions disponibles à l’adresse https://aka.ms/azurestacklogfiles.

### <a name="cef-mapping-for-alerts-closed"></a>Mappage CEF pour les alertes fermées

```
* Signature ID: Microsoft Azure Stack Hub Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

L’exemple ci-dessous montre un message syslog avec une charge utile CEF :
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack Hub|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```

## <a name="syslog-event-types"></a>Types d’événement Syslog  

Le tableau liste tous les types d’événement, événements, schémas de message ou propriétés envoyés via le canal Syslog. Le commutateur détaillé de configuration doit être utilisé uniquement si des événements d’information Windows sont nécessaires à l’intégration de SIEM (Informations de sécurité et gestion d’événements). 

| Type d'événement                                 | Événements ou schéma de message                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | Nécessite un paramètre détaillé         | Description de l’événement (facultatif)                                                                                                                                   |
|--------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alertes Azure Stack Hub                     | Pour le schéma de message d’alerte, consultez [Mappage CEF pour les alertes fermées](#cef-mapping-for-alerts-closed). <br> <br>Liste de toutes les alertes partagées dans un document distinct.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | Non                               | Alertes d’intégrité du système                                                                                                                                           |
| Événements de point de terminaison privilégié                 | Pour le schéma de message de point de terminaison privilégié, consultez [Mappage CEF pour les événements de point de terminaison privilégié](#cef-mapping-for-privileged-endpoint-events).<br> <br>PrivilegedEndpointAccessed <br>SupportSessionTokenRequested <br>SupportSessionDevelopmentTokenRequested <br>SupportSessionUnlocked <br>SupportSessionFailedToUnlock <br>PrivilegedEndpointClosed <br>NewCloudAdminUser <br>RemoveCloudAdminUser <br>SetCloudAdminUserPassword <br>GetCloudAdminPasswordRecoveryToken <br>ResetCloudAdminPassword <br>PrivilegedEndpointSessionTimedOut                                                                                                                                                                                                                                                                                                      | Non                               |                                                                                                                                                                |
| Événements de point de terminaison de récupération                   | Pour le schéma de message de point de terminaison de récupération, consultez [Mappage CEF pour les événements de point de terminaison de récupération](#cef-mapping-for-recovery-endpoint-events). <br>RecoveryEndpointAccessed <br>RecoverySessionTokenRequested <br>RecoverySessionDevelopmentTokenRequested <br>RecoverySessionUnlocked <br>RecoverySessionFailedToUnlock <br>RecoveryEndpointClosed                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | Non                               |                                                                                                                                                                |
| Événements de sécurité Windows                    |   <br>Pour le schéma de message d’événement Windows, consultez [Mappage CEF pour les événements Windows](#cef-mapping-for-windows-events).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | Oui (pour obtenir les événements d’information)  | Tapez : <br>- Information <br>Avertissement <br>error <br>- Critique                                                                                               |
| Événements ARM                                 | Propriétés de message : <br> <br>AzsSubscriptionId <br>AzsCorrelationId <br>AzsPrincipalOid <br>AzsPrincipalPuid <br>AzsTenantId <br>AzsOperationName <br>AzsOperationId <br>AzsEventSource <br>AzsDescription <br>AzsResourceProvider <br>AzsResourceUri <br>AzsEventName <br>AzsEventInstanceId <br>AzsChannels <br>AzsEventLevel <br>AzsStatus <br>AzsSubStatus <br>AzsClaims <br>AzsAuthorization <br>AzsHttpRequest <br>AzsProperties <br>AzsEventTimestamp <br>AzsAudience <br>AzsIssuer <br>AzsIssuedAt <br>AzsApplicationId <br>AzsUniqueTokenId <br>AzsArmServiceRequestId <br>AzsEventCategory <br> <br>                                                                                                                                                                                                                                | Non <br>                          | Chaque ressource ARM inscrite peut déclencher un événement.                                                                                                               |
| Événements BCDR                                | Schéma de message : <br> <br>AuditingManualBackup { <br>} <br>AuditingConfig <br>{ <br>Intervalle <br>Rétention <br>IsSchedulerEnabled <br>BackupPath <br>} <br>AuditingPruneBackupStore { <br>IsInternalStore <br>} <br>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Non                               | Ces événements permettent d’effectuer le suivi des opérations d’administration de sauvegarde d’infrastructure effectuées manuellement par le client, notamment le déclenchement de la sauvegarde, le changement de configuration de la sauvegarde et l’élagage des données de sauvegarde.       |
| Événements liés à des défaillances d’ouverture et de fermeture de l’infrastructure    | Schéma de message : <br> <br>InfrastructureFaultOpen { <br>AzsFaultId, <br>AzsFaultTypeName, <br>AzsComponentType, <br>AzsComponentName, <br>AzsFaultHash, <br>AzsCreatedTimeUtc, <br>AzsSource <br>} <br>  <br>InfrastructureFaultClose {  <br>AzsFaultId, <br>AzsFaultTypeName, <br>AzsComponentType, <br>AzsComponentName, <br>AzsFaultHash, <br>AzsLastUpdatedTimeUtc, <br>AzsSource <br>}                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Non                               | Les défaillances déclenchent des workflows qui tentent de corriger les erreurs pouvant entraîner des alertes. Si une défaillance n’est pas corrigée, elle entraîne directement une alerte.            |
| Événements liés à des défaillances d’ouverture et de fermeture d’un service  | Schéma de message : <br> <br>ServiceFaultOpen { <br>AzsFaultId, <br>AzsFaultTypeName, <br>AzsSubscriptionId, <br>AzsResourceGroup, <br>AzsServiceName, <br>AzsResourceId <br>AzsFaultHash, <br>AzsCreatedTimeUtc, <br>AzsSource <br>} <br>  <br>ServiceFaultClose { <br>AzsFaultId, <br>AzsFaultTypeName, <br>AzsSubscriptionId, <br>AzsResourceGroup, <br>AzsServiceName, <br>AzsResourceId <br>AzsFaultHash, <br>AzsLastUpdatedTimeUtc, <br>AzsSource <br>}                                                                                                                                                                                                                                                                                                                                                                                     | Non                               | Les défaillances déclenchent des workflows qui tentent de corriger les erreurs pouvant entraîner des alertes.  <br>Si une défaillance n’est pas corrigée, elle entraîne directement une alerte. <br>  |
| Événements WAC PEP                             | Schéma de message : <br> <br>Champs de préfixe  <br>* ID de signature : Microsoft-AzureStack-PrivilegedEndpoint : <PEP Event ID>  <br>* Nom : <PEP Task Name>  <br>* Gravité : mappée à partir du niveau du PEP (point de terminaison privilégié). Pour plus de détails, consultez le tableau de gravité des PEP ci-dessous  <br>* Who : compte utilisé pour la connexion au PEP  <br>* WhichIP : adresse IP de l’appareil utilisé pour la connexion au PEP <br><br>WACServiceStartFailedEvent <br>WACConnectedUserNotRetrievedEvent <br>WACEnableExceptionEvent  <br>WACUserAddedEvent <br>WACAddUserToLocalGroupFailedEvent <br>WACIsUserInLocalGroupFailedEvent  <br>WACServiceStartTimeoutEvent  <br>WACServiceStartInvalidOperationEvent <br>WACGetSidFromUserFailedEvent <br>WACDisableFirewallFailedEvent <br>WACCreateLocalGroupIfNotExistFailedEvent <br>WACEnableFlagIsTrueEvent <br>WACEnableFlagIsFalseEvent <br>WACServiceStartedEvent | Non                               |                                                                                                                                                                |

## <a name="next-steps"></a>Étapes suivantes

[Stratégie de maintenance](azure-stack-servicing-policy.md)
