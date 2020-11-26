---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 10/10/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 0df920ef0c8063332a290ca5e95e1c01d755e548
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517552"
---
Enfin, déterminez les propriétés de déploiement les plus récentes du fournisseur de ressources et utilisez-les pour terminer le processus de rotation des secrets.

### <a name="determine-deployment-properties"></a>Déterminer les propriétés de déploiement

Les fournisseurs de ressources sont déployés dans votre environnement Azure Stack Hub sous forme de package de produit avec version. Les packages se voient attribuer un ID de package unique, au format `'<product-id>.<installed-version>'`. Où `<product-id>` correspond à un chaîne unique représentant le fournisseur de ressources, et `<installed-version>` à une version spécifique. Les secrets associés à chaque package sont stockés dans le service Key Vault Hub Azure Stack. 

Ouvrez une console PowerShell avec élévation de privilèges et suivez les étapes ci-dessous pour déterminer les propriétés requises afin d’effectuer la rotation des secrets du fournisseur de ressources :

1. Connectez-vous à votre environnement Azure Stack Hub à l'aide de vos informations d’identification d’opérateur. Consultez [Se connecter à Azure Stack Hub avec PowerShell](../operator/azure-stack-powershell-configure-admin.md) pour le script de connexion PowerShell. Veillez à utiliser les cmdlets PowerShell Az (plutôt que AzureRM) et remplacez toutes les valeurs d’espace réservé, telles que les URL de point de terminaison et le nom de locataire d’annuaire.

2. Exécutez la cmdlet `Get-AzsProductDeployment` pour récupérer la liste des déploiements des fournisseurs de ressources les plus récents. La collection `"value"` retournée contient un élément pour chaque fournisseur de ressources déployé. Recherchez le fournisseur de ressources qui vous intéresse et notez les valeurs correspondant à ces propriétés :
   - `"name"` - contient l’ID produit du fournisseur de ressources dans le deuxième segment de la valeur. 
   - `"properties"."deployment"."version"` - contient le numéro de la version actuellement déployée. 

   Dans l’exemple suivant, notez le déploiement RP Event Hubs dans le premier élément de la collection, qui présente l’ID produit `"microsoft.eventhub"` et la version `"1.2003.0.0"` :

   ```powershell
   PS C:\WINDOWS\system32> Get-AzsProductDeployment -AsJson
   VERBOSE: GET https://adminmanagement.myregion.mycompany.com/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productDeployments?api-version=2019-01-01 with 0-char payload
   VERBOSE: Received 2656-char response, StatusCode = OK
   {
       "value":  [
                     {
                         "id":  "/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productDeployments/microsoft.eventhub",
                         "name":  "global/microsoft.eventhub",
                         "type":  "Microsoft.Deployment.Admin/locations/productDeployments",
                         "properties":  {
                                            "status":  "DeploymentSucceeded",
                                            "subscriptionId":  "b37ae55a-a6c6-4474-ba97-81519412adf5",
                                            "deployment":  {
                                                               "version":  "1.2003.0.0",
                                                               "actionPlanInstanceResourceId":"/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/actionplans/abcdfcd3-fef0-z1a3-z85d-z6ceb0f31e36",
                                                               "parameters":  {
   
                                                                              }
                                                           },
                                            "lastSuccessfulDeployment":  {
                                                                             "version":  "1.2003.0.0",
                                                                             "actionPlanInstanceResourceId":"/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/actionplans/abcdfcd3-fef0-z1a3-z85d-z6ceb0f31e36",
                                                                             "parameters":  {
   
                                                                                            }
                                                                         },
                                            "provisioningState":  "Succeeded"
                                        }
                     },
                     {
                     ...
                     }
                 ]
   }
   ```

3. Générez l’ID de package du fournisseur de ressources en concaténant l’ID produit et la version du fournisseur de ressources. Par exemple, en utilisant les valeurs dérivées à l’étape précédente, l’ID de package RP Event Hubs est `microsoft.eventhub.1.2003.0.0`. 

4. À l’aide de l’ID de package dérivé à l’étape précédente, exécutez `Get-AzsProductSecret -PackageId` pour récupérer la liste des types de secrets utilisés par le fournisseur de ressources. Dans la collection `value` retournée, recherchez l’élément contenant la valeur `"Certificate"` pour la propriété `"properties"."secretKind"`. Cet élément contient les propriétés pour le secret de certificat du RP. Notez le nom attribué à ce secret de certificat, qui est identifié par le dernier segment de la propriété `"name"`, juste au-dessus de `"properties"`. 

   Dans l’exemple suivant, la collection de secrets renvoyée pour RP Event Hubs contient un secret `"Certificate"` nommé `aseh-ssl-gateway-pfx`. 

    ```powershell
    PS C:\WINDOWS\system32> Get-AzsProductSecret -PackageId 'microsoft.eventhub.1.2003.0.0' -AsJson
    VERBOSE: GET
    https://adminmanagement.myregion.mycompany.com/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productPackages/microsoft.eventhub.1.2003.0.0/secrets?api-version=2019-01-01 with 0-char payload
    VERBOSE: Received 617-char response, StatusCode = OK
    {
        "value":  [
                        {
                            "id":  "/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productPackages/microsoft.eventhub.1.2003.0.0/secrets/aseh-ssl-gateway-pfx",
                            "name":  "global/microsoft.eventhub.1.2003.0.0/aseh-ssl-gateway-pfx",
                            "type":  "Microsoft.Deployment.Admin/locations/productPackages/secrets",
                            "properties":  {
                                            "secretKind":  "Certificate",
                                            "description":  "Event Hubs gateway SSL certificate.",
                                            "expiresAfter":  "P730D",
                                            "secretDescriptor":  {
    
                                                                    },
                                            "secretState":  {
                                                                "status":  "Deployed",
                                                                "rotationStatus":  "None",
                                                                "expirationDate":  "2022-03-31T00:16:05.3068718Z"
                                                            },
                                            "provisioningState":  "Succeeded"
                                        }
                        },
                        ...
                    ]
    }
    ```

### <a name="rotate-the-secrets"></a>Effectuer la rotation des secrets

1. Utilisez la cmdlet `Set-AzsProductSecret` pour importer votre nouveau certificat dans Key Vault ; celui-ci sera utilisé par le processus de rotation. Remplacez les valeurs d’espace réservé de la variable en conséquence avant d’exécuter le script :

   | Espace réservé | Description | Valeur d'exemple |
   | ----------- | ----------- | --------------|
   | `<product-id>` | ID produit du déploiement du fournisseur de ressources le plus récent. | `microsoft.eventhub` |
   | `<installed-version>` | Version du dernier déploiement du fournisseur de ressources. | `1.2003.0.0` |
   | `<cert-secret-name>` | Nom sous lequel le secret de certificat est stocké. | `aseh-ssl-gateway-pfx` |
   | `<cert-pfx-file-path>` | Chemin d’accès à votre fichier de certificat PFX. | `C:\dir\eh-cert-file.pfx` |
   | `<pfx-password>` | Mot de passe attribué à votre fichier de certificat .PFX. | `strong@CertSecret6` |

   ```powershell
   $productId = '<product-id>'
   $packageId = $productId + '.' + '<installed-version>'
   $certSecretName = '<cert-secret-name>' 
   $pfxFilePath = '<cert-pfx-file-path>'
   $pfxPassword = ConvertTo-SecureString '<pfx-password>' -AsPlainText -Force   
   Set-AzsProductSecret -PackageId $packageId -SecretName $certSecretName -PfxFileName $pfxFilePath -PfxPassword $pfxPassword -Force
   ```

2. Enfin, utilisez la cmdlet `Invoke-AzsProductRotateSecretsAction` pour effectuer la rotation des secrets internes et externes :

   > [!NOTE]
   > Le processus de rotation prend de 3,5 à 4 heures.

   ```powershell
   Invoke-AzsProductRotateSecretsAction -ProductId $productId
   ```
   
   Vous pouvez surveiller la progression de la rotation des secrets à l’aide de la console PowerShell ou du portail d’administration en sélectionnant le fournisseur de ressources dans le service Place de marché :

   [![secret-rotation-progress](media/resource-provider-va-rotate-secrets-rotate/secret-rotation-in-progress.png)](media/resource-provider-va-rotate-secrets-rotate/secret-rotation-in-progress.png#lightbox)

