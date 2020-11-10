---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 10/10/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 900d7ac882a37e229bec6dc916653cf55992cccb
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "93050266"
---
Ensuite, créez ou renouvelez votre certificat TLS pour sécuriser les points de terminaison de fournisseur de ressources à valeur ajoutée :

1. Suivez les étapes décrites dans [Générer des demandes de signature de certificat (CSR) pour le renouvellement de certificat](../operator/azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-certificate-renewal) pour votre fournisseur de ressources. Pour créer la CSR, utilisez l'outil Readiness Checker d'Azure Stack Hub. À l'étape « Générer des demandes de certificat pour d'autres services Azure Stack Hub », veillez à exécuter la cmdlet qui correspond à votre fournisseur de ressources. Par exemple, la cmdlet `New-AzsHubEventHubsCertificateSigningRequest` est utilisée pour Event Hubs. Lorsque vous avez terminé, vous devez envoyer le fichier .REQ généré à votre autorité de certification pour obtenir le nouveau certificat.

2. Une fois le fichier de certificat reçu de l'autorité de certification, suivez les étapes décrites dans [Préparer des certificats pour le déploiement ou la rotation](../operator/azure-stack-prepare-pki-certs.md). Utilisez à nouveau l'outil Readiness Checker pour traiter le fichier renvoyé par l'autorité de certification.

3. Enfin, suivez les étapes décrites dans [Valider des certificats PKI Azure Stack Hub](../operator/azure-stack-validate-pki-certs.md). Utilisez à nouveau l'outil Readiness Checker pour procéder aux tests de validation sur votre nouveau certificat.


