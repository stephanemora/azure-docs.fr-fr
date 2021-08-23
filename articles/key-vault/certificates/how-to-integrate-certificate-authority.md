---
title: Intégration de Key Vault à l'autorité de certification DigiCert
description: Cet article explique comment intégrer Key Vault à l'autorité de certification DigiCert afin de pouvoir approvisionner, gérer et déployer des certificats pour votre réseau.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9cec61df061c7a504da412851349064273507173
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110678853"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>Intégration de Key Vault à l'autorité de certification DigiCert

Azure Key Vault vous permet de provisionner, gérer et déployer facilement des certificats numériques pour votre réseau et de sécuriser les communications pour les applications. Un certificat numérique correspond à des informations d'identification électroniques qui fournissent une preuve d'identité dans le cadre d'une transaction électronique. 

Les utilisateurs d'Azure Key Vault peuvent générer des certificats DigiCert directement à partir de leur coffre de clé. Key Vault a conclu un partenariat de confiance avec l'autorité de certification DigiCert. Ce partenariat garantit une gestion de bout en bout du cycle de vie des certificats émis par DigiCert.

Pour plus d’informations générales sur les certificats, consultez [Certificats Azure Key Vault](./about-certificates.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour suivre les procédures décrites dans cet article, vous devez disposer des éléments suivants :
* Un coffre de clés. Vous pouvez utiliser un coffre de clés existant ou en créer un en suivant les étapes d'un des guides de démarrage rapide suivants :
   - [Créer un coffre de clés à l'aide de l'interface de ligne de commande Azure](../general/quick-create-cli.md)
   - [Créer un coffre de clés à l'aide d'Azure PowerShell](../general/quick-create-powershell.md)
   - [Créez un coffre de clés à l’aide du portail Azure](../general/quick-create-portal.md)
*   Un compte DigiCert CertCentral activé. [Inscrivez-vous](https://www.digicert.com/account/signup/) pour obtenir un compte CertCentral.
*   Des autorisations de niveau administrateur dans vos comptes.


### <a name="before-you-begin"></a>Avant de commencer

Récupérez les informations suivantes à partir de votre compte DigiCert CertCentral :
-   ID de compte CertCentral
-   ID d’organisation
-   Clé API

## <a name="add-the-certificate-authority-in-key-vault"></a>Ajouter l'autorité de certification dans Key Vault 
Après avoir récupéré les informations ci-dessus à partir de votre compte DigiCert CertCentral, vous pouvez ajouter DigiCert à la liste des autorités de certification du coffre de clés.

### <a name="azure-portal"></a>Portail Azure

1.  Pour ajouter l'autorité de certification DigiCert, accédez au coffre de clés auquel vous souhaitez l'ajouter. 
2.  Sur la page de propriétés Key Vault, sélectionnez **Certificats**.
3.  Sélectionnez l'onglet **Autorités de certification** : :::image type="content" source="../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png" alt-text="Capture d'écran illustrant la sélection de l'onglet Autorités de certification.":::
4.  Sélectionnez **Ajouter** : :::image type="content" source="../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png" alt-text="Capture d'écran illustrant le bouton Ajouter de l'onglet Autorités de certification.":::
5.  Sous **Créer une autorité de certification**, entrez les valeurs suivantes :
    -   **Nom** : nom d'un émetteur identifiable. Par exemple, **DigiCertCA**.
    -   **Fournisseur** : **DigiCert**.
    -   **ID du compte** : ID de votre compte DigiCert CertCentral.
    -   **Mot de passe du compte** : clé API que vous avez générée sur votre compte DigiCert CertCentral.
    -   **ID d'organisation** : ID d'organisation de votre compte DigiCert CertCentral. 

1. Sélectionnez **Create** (Créer).
   
DigicertCA figure désormais dans la liste des autorités de certification.


### <a name="azure-powershell"></a>Azure PowerShell

Vous pouvez utiliser Azure PowerShell pour créer et gérer des ressources Azure à l'aide de commandes ou de scripts. Azure héberge Azure Cloud Shell, un environnement d'interpréteur de commandes interactif que vous pouvez utiliser via le portail Azure dans un navigateur.

Si vous choisissez d'installer et d'utiliser PowerShell en local, vous devez exécuter le module Azure AZ PowerShell version 1.0.0 ou ultérieure pour suivre les procédures décrites ici. Entrez `$PSVersionTable.PSVersion` pour déterminer la version. Si vous devez procéder à une mise à niveau, consultez [Installer le module Azure AZ PowerShell](/powershell/azure/install-az-ps). Si vous exécutez PowerShell en local, vous devez également exécuter `Login-AzAccount` pour créer une connexion avec Azure :

```azurepowershell-interactive
Login-AzAccount
```

1.  Créez un groupe de ressources Azure à l'aide de [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

    ```azurepowershell-interactive
    New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
    ```

2. Créez un coffre de clés doté d'un nom unique. `Contoso-Vaultname` est ici le nom du coffre de clés.

   - **Nom du coffre** : `Contoso-Vaultname`
   - **Nom du groupe de ressources** : `ContosoResourceGroup`
   - **Emplacement** : `EastUS`

    ```azurepowershell-interactive
    New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
   ```

3. Définissez des variables pour les valeurs suivantes de votre compte DigiCert CertCentral :

   - **ID de compte** 
   - **ID d’organisation** 
   - **Clé API** 

   ```azurepowershell-interactive
   $accountId = "myDigiCertCertCentralAccountID"
   $org = New-AzKeyVaultCertificateOrganizationDetail -Id OrganizationIDfromDigiCertAccount
   $secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
   ```

4. Définissez l'émetteur. Cela permet d'ajouter DigiCert en tant qu'autorité de certification dans le coffre de clés. [Découvrez-en plus sur les paramètres.](/powershell/module/az.keyvault/Set-AzKeyVaultCertificateIssuer)
   ```azurepowershell-interactive
   Set-AzKeyVaultCertificateIssuer -VaultName "Contoso-Vaultname" -Name "TestIssuer01" -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org -PassThru
   ```

5. Définissez la stratégie à attribuer au certificat et émettez le certificat dans Key Vault à partir de DigiCert :

   ```azurepowershell-interactive
   $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "TestIssuer01" -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
   Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
   ```

Le certificat est alors émis par l'autorité de certification DigiCert dans le coffre de clés spécifié.

## <a name="troubleshoot"></a>Dépanner

Si le certificat émis est à l'état « désactivé » sur le portail Azure, examinez le message d'erreur DigiCert de ce certificat dans Opération de certificat.

:::image type="content" source="../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png" alt-text="Capture d'écran illustrant l'onglet Opération de certificat.":::

Message d'erreur : « Veuillez effectuer une fusion pour exécuter cette demande de certificat ».
   
Fusionnez la demande de signature de certificat signée par l'autorité de certification pour effectuer la demande. Pour en savoir plus sur la fusion d'une demande de signature de certificat, consultez [Créer et fusionner une demande de signature de certificat](./create-certificate-signing-request.md).

Pour plus d'informations, consultez [Informations de référence sur les opérations liées aux certificats dans l'API REST Key Vault](/rest/api/keyvault). Pour en savoir plus sur l'établissement d'autorisations, consultez [Coffres : créer ou mettre à jour](/rest/api/keyvault/vaults/createorupdate) et [Coffres : mettre à jour la stratégie d'accès](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="frequently-asked-questions"></a>Forum aux questions

- **Puis-je générer un certificat avec caractères génériques DigiCert à l'aide de KeyVault ?** 
   
  Oui, mais cela dépend de la façon dont vous avez configuré votre compte DigiCert.
- **Comment créer un certificat OV SSL ou EV SSL avec DigiCert ?**
 
   Key Vault prend en charge la création des certificats OV et EV SSL. Lorsque vous créez un certificat, sélectionnez **Configuration de stratégie avancée**, puis spécifiez le type de certificat. Valeurs prises en charge : OV SSL, EV SSL
   
   Vous pouvez créer ce type de certificat dans Key Vault si votre compte DigiCert le permet. Pour ce type de certificat, la validation est effectuée par DigiCert. En cas d'échec de la validation, l'équipe du support technique DigiCert peut vous aider. Lors de la création d'un certificat, vous pouvez ajouter des informations en les définissant dans `subjectName`.

  Par exemple,     `SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"`.
   
- **La création d'un certificat DigiCert via l'intégration prend-elle plus de temps que son acquisition directe auprès de DigiCert ?**
   
   Non. Lorsque vous créez un certificat, le processus de vérification peut prendre du temps. DigiCert contrôle ce processus.


## <a name="next-steps"></a>Étapes suivantes

- [Authentification, requêtes et réponses](../general/authentication-requests-and-responses.md)
- [Guide du développeur Key Vault](../general/developers-guide.md)
