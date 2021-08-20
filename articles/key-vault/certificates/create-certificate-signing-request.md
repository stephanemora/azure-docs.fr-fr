---
title: Création et fusion d’une demande de signature de certificat dans Azure Key Vault
description: Apprenez à créer et fusionner une demande de signature de certificat dans Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: 77a46beceda451d456dfc4f576f855ebf7312e50
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112463510"
---
# <a name="create-and-merge-a-csr-in-key-vault"></a>Créer et fusionner une demande de signature de certificat dans Key Vault

Azure Key Vault prend en charge le stockage de certificats numériques émis par une autorité de certification. La solution prend en charge la création d’une demande de signature de certificat avec une paire de clés privée/publique. Cette demande peut être signée par une autorité de certification (interne d’entreprise ou publique externe). Une demande de signature de certificat est un message que vous envoyez à une autorité de certification afin de demander un certificat numérique.

Pour plus d’informations générales sur les certificats, consultez [Certificats Azure Key Vault](./about-certificates.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="add-certificates-in-key-vault-issued-by-partnered-cas"></a>Ajouter des certificats dans Key Vault émis par des autorités de certification partenaires

Key Vault s’associe aux autorités de certification suivantes pour simplifier la création de certificats.

|Fournisseur|Type de certificat|Configuration  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault propose des certificats SSL OV ou EV avec DigiCert| [Guide d’intégration](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault propose des certificats SSL OV ou EV avec GlobalSign| [Guide d’intégration](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="add-certificates-in-key-vault-issued-by-non-partnered-cas"></a>Ajouter des certificats dans Key Vault émis par des autorités de certification non partenaires

Effectuez ces étapes pour ajouter un certificat issu d’autorités de certification qui ne sont pas associées à Key Vault. (Par exemple, GoDaddy n’est pas une autorité de certification Key Vault approuvée.)

# <a name="portal"></a>[Portail](#tab/azure-portal)

1. Accédez au coffre de clés auquel vous voulez ajouter le certificat.
1. Dans la page Propriétés, sélectionnez **Certificats**.
1. Sélectionnez l’onglet **Générer/Importer**.
1. Dans l’écran **Créer un certificat**, choisissez les valeurs suivantes :
    - **Méthode de création de certificat** : Générer.
    - **Nom du certificat** : ContosoManualCSRCertificate.
    - **Type d’autorité de certification** : Certificat émis par une autorité de certification non intégrée.
    - **Objet** : `"CN=www.contosoHRApp.com"`.
     > [!NOTE]
     > Si vous utilisez un nom unique relatif qui comporte une virgule (,) dans la valeur, mettez la valeur qui contient le caractère spécial entre guillemets doubles. 
     >
     > Exemple d’entrée pour **Objet** : `DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`
     >
     > Dans cet exemple, le nom unique relatif `OU` contient une valeur qui comporte une virgule dans son nom. La sortie obtenue pour `OU` est **Docs, Contoso**.
1. Sélectionnez les autres valeurs selon vos besoins, puis sélectionnez **Créer** pour ajouter le certificat à la liste **Certificats**.

    ![Capture d’écran des propriétés du certificat](../media/certificates/create-csr-merge-csr/create-certificate.png)  

1. Dans la liste **Certificats**, sélectionnez le nouveau certificat. L’état actuel du certificat est **désactivé**, car il n’a pas encore été émis par l’autorité de certification.
1. Cliquez sur l’onglet **Opération de certificat** et sélectionnez **Télécharger CSR**.

   ![Capture d’écran mettant en évidence le bouton Télécharger CSR.](../media/certificates/create-csr-merge-csr/download-csr.png)

1. Demandez à l’autorité de certification de signer la demande de signature de certificat (.csr).
1. Une fois la demande signée, sélectionnez **Fusionner la demande signée** sous l’onglet **Opération de certificat** pour ajouter le certificat signé à Key Vault.

La demande de certificat est maintenant correctement fusionnée.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Créez une stratégie de certificat. Étant donné que l’autorité de certification choisie dans ce scénario n’est pas partenaire, **IssuerName** a la valeur **Unknown** et Key Vault n’inscrit pas ou ne renouvelle pas le certificat.

   ```azure-powershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
     > [!NOTE]
     > Si vous utilisez un nom unique relatif qui comporte une virgule (,) dans la valeur, utilisez des guillemets simples pour la valeur complète ou l’ensemble de valeurs et placez la valeur qui contient le caractère spécial entre guillemets doubles. 
     >
     >Exemple d’entrée pour **SubjectName** : `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown`. Dans cet exemple, la valeur `OU` est **Docs, Contoso**. Ce format fonctionne pour toutes les valeurs qui contiennent une virgule.
     > 
     > Dans cet exemple, le nom unique relatif `OU` contient une valeur qui comporte une virgule dans son nom. La sortie obtenue pour `OU` est **Docs, Contoso**.

1. Créez la demande de signature de certificat.

   ```azure-powershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

1. Demandez à l’autorité de certification de signer la demande de signature de certificat. `$csr.CertificateSigningRequest` correspond à la demande de signature de certificat encodée en base pour le certificat. Vous pouvez vider ce blob sur le site web de la demande de certificat de l’émetteur. Cette étape varie d’une autorité de certification à l’autre. Recherchez les instructions de votre autorité de certification pour effectuer cette étape. Vous pouvez également utiliser des outils comme certreq ou openssl pour faire signer la demande de signature de certificat et terminer le processus de génération d’un certificat.

1. Fusionnez la demande signée dans Key Vault. Une fois la demande de certificat signée, vous pouvez la fusionner avec la paire de clés privée/publique initiale créée dans Azure Key Vault.

    ```azure-powershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

La demande de certificat est maintenant correctement fusionnée.

---

## <a name="add-more-information-to-the-csr"></a>Ajouter d’autres informations à la demande de signature de certificat

Si vous voulez ajouter des informations supplémentaires lors de la création de la demande de signature de certificat, définissez-les dans **SubjectName**. Vous pouvez éventuellement des informations comme celles-ci :
- Pays ou région
- Ville/Localité
- État/Province
- Organisation
- Unité d'organisation

Exemple

   ```azure-powershell
   SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
   ```

> [!NOTE]
> Si vous demandez un certificat de validation de domaine (DV) avec des informations supplémentaires, l’autorité de certification peut rejeter la demande si elle ne parvient pas à valider toutes les informations contenues dans la demande. Les informations supplémentaires sont peut-être plus appropriées si vous demandez un certificat de validation d’organisation (OV).

## <a name="faqs"></a>Foire aux questions

- Comment surveiller ou gérer ma demande de signature de certificat ?

     Consultez [Surveiller et gérer la création de certificats](./create-certificate-scenarios.md).

- Que se passe-t-il si je vois **Type d’erreur « La clé publique du certificat d’entité finale dans le contenu du certificat X.509 spécifié ne correspond pas à la partie publique de la clé privée spécifiée. Vérifiez que le certificat est valide. »**  ?

     Cette erreur se produit si vous ne fusionnez pas la demande de signature de certificat signée avec la même demande de signature de certificat que celle que vous avez lancée. Chaque nouvelle demande de signature de certificat que vous créez a une clé privée, qui doit correspondre quand vous fusionnez la demande signée.

- Quand une demande de signature de certificat est fusionnée, est-ce que la chaîne entière est fusionnée ?

     Oui, l’ensemble de la chaîne est fusionné, à condition que l’utilisateur ait remis un fichier .p7b à fusionner.

- Que se passe-t-il si le certificat émis présente l’état désactivé dans le portail Azure ?

     Affichez l’onglet **Opération de certificat** pour consulter le message d’erreur de ce certificat.

- Que se passe-t-il si je vois **Type d’erreur « Le nom d’objet fourni n’est pas un nom X500 valide »** ?

     Cette erreur peut se produire si **SubjectName** contient des caractères spéciaux. Consultez les remarques dans les instructions du portail Azure et PowerShell.

- Type d’erreur **La demande de signature de certificat utilisée pour obtenir votre certificat a déjà été utilisée. Essayez de générer un nouveau certificat avec une nouvelle demande de signature de certificat.**
     Accédez à la section « Stratégie avancée » du certificat et vérifiez si l’option « Réutiliser la clé lors du renouvellement » est désactivée.
---

## <a name="next-steps"></a>Étapes suivantes

- [Authentification, requêtes et réponses](../general/authentication-requests-and-responses.md)
- [Guide du développeur Key Vault](../general/developers-guide.md)
- [Informations de référence sur l’API REST Azure Key Vault](/rest/api/keyvault)
- [Coffres - Créer ou mettre à jour](/rest/api/keyvault/vaults/createorupdate)
- [Coffres - Mettre à jour une stratégie d’accès](/rest/api/keyvault/vaults/updateaccesspolicy)
