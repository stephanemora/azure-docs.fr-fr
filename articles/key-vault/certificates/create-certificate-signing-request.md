---
title: Création et fusion d’une demande de signature de certificat dans Azure Key Vault
description: Création et fusion d’une demande de signature de certificat dans Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: ae2d6259bac6a2034edc98de9b0405f32f17fbc3
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85849491"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>Création et fusion d’une demande de signature de certificat dans Key Vault

Azure Key Vault prend en charge le stockage d’un certificat numérique émis par toute autorité de certification de votre choix dans votre coffre de clés. Il prend en charge la création de la demande de signature de certificat avec une paire de clés publique/privée pouvant être signée par toute autorité de certification de votre choix. Il peut s’agir d’une autorité de certification interne de l’entreprise ou d’une autorité de certification publique externe. Une demande de signature de certificat (également appelée CSR ou demande de certification) est un message envoyé par l’utilisateur à une autorité de certification afin de demander l’émission d’un certificat numérique.

Pour plus d’informations générales sur les certificats, consultez [Certificats Azure Key Vault](/azure/key-vault/certificates/about-certificates).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="adding-certificate-in-key-vault-issued-by-a-non-trusted-ca"></a>Ajout d’un certificat dans Key Vault émis par une autorité de certification non approuvée

Les étapes suivantes vous aideront à créer un certificat à partir d’autorités de certification qui ne sont pas partenaires de Key Vault (par exemple, GoDaddy n’est pas une autorité de certification de coffre de clés approuvée). 


### <a name="azure-powershell"></a>Azure PowerShell



1.  Tout d’abord, **créez la stratégie de certificat**. Key Vault n’inscrit pas ni ne renouvelle le certificat issu par l’émetteur au nom de l’utilisateur, puisque l’autorité de certification choisie dans ce scénario n’est pas prise en charge et, donc, IssuerName a la valeur Inconnu.

    ```azurepowershell
    $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
    ```


2. Créez une **demande de signature de certificat**.

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. Obtenez la **demande CSR signée par l’autorité de certification**. `$certificateOperation.CertificateSigningRequest` correspond à la demande de signature de certificat codée en base4. Vous pouvez prendre cet objet blob et le vider dans le site web de la demande de certificat de l’émetteur. Cette étape varie d’une autorité de certification à l’autre. Le meilleur moyen consiste à rechercher les instructions de votre autorité de certification quant à l’exécution de cette étape. Vous pouvez également utiliser des outils comme certreq ou openssl pour faire signer la demande de certificat et terminer le processus de génération d’un certificat.


4. **Fusionnez la demande signée** dans Key Vault. Une fois que la demande de certificat a été signée par l’émetteur, vous pouvez reprendre le certificat signé pour le fusionner avec la paire de clés publique/privée initiale créée dans Azure Key Vault.

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    La demande de certificat est maintenant correctement fusionnée.

### <a name="azure-portal"></a>Portail Azure

1.  Pour générer la CSR pour l’autorité de certification de votre choix, accédez au coffre de clés auquel vous voulez ajouter le certificat.
2.  Dans les pages des propriétés Key Vault, sélectionnez **Certificats**.
3.  Sélectionnez l’onglet **Générer/Importer**.
4.  Dans l’écran **Créer un certificat**, choisissez les valeurs suivantes :
    - **Méthode de création de certificat :** Générer.
    - **Nom du certificat :** ContosoManualCSRCertificate.
    - **Type d’autorité de certification :** Certificat délivré par une autorité de certification non intégrée
    - **Objet :** `"CN=www.contosoHRApp.com"`
    - Sélectionnez les autres valeurs si besoin. Cliquez sur **Créer**.

    ![Propriétés du certificat](../media/certificates/create-csr-merge-csr/create-certificate.png)
6.  Vous verrez que le certificat est maintenant ajouté à la liste des certificats. Sélectionnez ce nouveau certificat que vous venez de créer. L’état actuel du certificat est « désactivé », car il n’a pas encore été émis par l’autorité de certification.
7. Cliquez sur l’onglet **Opération de certificat** et sélectionnez **Télécharger CSR**.
 ![Propriétés de certificat](../media/certificates/create-csr-merge-csr/download-csr.png)

8.  Présentez le fichier .csr à l’autorité de certification pour faire signer la demande.
9.  Une fois que la demande est signée par l’autorité de certification, reprenez le fichier de certificat pour **fusionner la demande signée** dans le même écran d’opération de certificat.

La demande de certificat est maintenant correctement fusionnée.

## <a name="troubleshoot"></a>Dépanner

Si le certificat émis est à l’état « désactivé » dans le portail Azure, examinez le message d’erreur pour ce certificat dans **Opération de certificat**.

Pour plus d’informations, voir [Informations de référence sur les opérations liées aux certificats dans l’API REST Key Vault](/rest/api/keyvault). Pour plus d’informations sur l’établissement d’autorisations, consultez [Coffres : créer ou mettre à jour](/rest/api/keyvault/vaults/createorupdate) et [Coffres : mettre à jour la stratégie d’accès](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Étapes suivantes

- [Authentification, requêtes et réponses](../general/authentication-requests-and-responses.md)
- [Guide du développeur Key Vault](../general/developers-guide.md)
