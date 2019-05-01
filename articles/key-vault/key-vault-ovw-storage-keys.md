---
title: Compte de stockage managé Azure Key Vault – CLI
description: Les clés de compte de stockage assurent une intégration transparente entre Azure Key Vault et l’accès au compte de stockage Azure basé sur les clés.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: mbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: eefdb4d644c97bb55342e21c9a2fcf0a122a6ec5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64724776"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Compte de stockage managé Azure Key Vault – CLI

> [!NOTE]
> [L’intégration du stockage Azure avec Azure Active Directory (Azure AD) est désormais disponible en préversion](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Nous recommandons d’utiliser Azure AD pour l’authentification et l’autorisation, offrant un accès au stockage Azure via un jeton OAuth2, à l’instar d’Azure Key Vault. Vous pouvez ainsi :
> - Authentifier votre application cliente en utilisant une identité d’application ou d’utilisateur plutôt que les informations d’identification du compte de stockage. 
> - Utiliser une [identité Azure AD managée](/azure/active-directory/managed-identities-azure-resources/) lors de l’exécution sur Azure. Les identités managées suppriment totalement l’authentification du client ainsi que le stockage des informations d’identification dans ou avec votre application.
> - Utiliser le contrôle d’accès en fonction du rôle (RBAC) pour gérer les autorisations, ce qui est également pris en charge par Key Vault.

Un [compte de stockage Azure](/azure/storage/storage-create-storage-account) utilise des informations d’identification qui se composent d’un nom de compte et d’une clé. La clé est générée automatiquement et sert plus de « mot de passe » que de clé de chiffrement. Key Vault peut gérer ces clés de compte de stockage en les stockant en tant que [secrets Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

## <a name="overview"></a>Présentation

La fonctionnalité de compte de stockage Key Vault managé exécute plusieurs fonctions de gestion interne à votre place :

- Répertorie (synchronise) les clés avec un compte de stockage Azure.
- Regénère (fait tourner) les clés régulièrement.
- Gère les clés des comptes de stockage et des comptes de stockage classiques.
- Les valeurs de clés ne sont jamais retournées en réponse à l’appelant.

Lorsque vous utilisez la fonctionnalité de clé de compte de stockage managé :

- **Autorisez uniquement Key Vault à gérer vos clés de compte de stockage.** N’essayez pas de les gérer vous-même, vous interféreriez avec les processus de Key Vault.
- **Ne permettez pas que les clés de compte de stockage soient gérées par plusieurs objets Key Vault**.
- **Ne régénérez pas manuellement vos clés de compte de stockage**. Nous vous recommandons de les régénérer via Key Vault.
- Vous demandant de Key Vault pour gérer votre compte de stockage est possible par un Principal d’utilisateur pour l’instant et pas un Principal de Service

L’exemple suivant vous montre comment autoriser Key Vault à gérer vos clés de compte de stockage.

> [!IMPORTANT]
> Un locataire Azure AD fournit à chaque application inscrite un **[principal de service](/azure/active-directory/develop/developer-glossary#service-principal-object)**, qui représente l’identité de l’application. L’ID d’application du principal de service est utilisé quand il reçoit l’autorisation d’accéder à d’autres ressources Azure, via le contrôle d’accès en fonction du rôle (RBAC). Key Vault étant une application Microsoft, elle est préinscrite dans tous les locataires Azure AD sous le même ID d’application dans chaque cloud Azure :
> - Les locataires Azure AD dans le cloud Azure Government utilisent l’ID d’application `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Les locataires Azure AD dans le cloud public Azure et tous les autres utilisent l’ID d’application `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

<a name="prerequisites"></a>Conditions préalables
--------------
1. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) Installer Azure CLI   
2. [Créer un compte de stockage](https://azure.microsoft.com/services/storage/)
    - Suivez les étapes de ce [document](https://docs.microsoft.com/azure/storage/) pour créer un compte de stockage.  
    - **Aide pour le nommage :** Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Instructions étape par étape sur la façon d'utiliser Key Vault pour gérer les clés du compte de stockage
--------------------------------------------------------------------------------
Sur le plan conceptuel, la liste des étapes à suivre est
- Nous commençons par obtenir un compte de stockage (préexistant)
- Nous récupérons ensuite un coffre de clés (préexistant)
- Puis, nous ajoutons un compte de stockage géré par Key Vault au coffre, en définissant Key1 en tant que clé active et avec une période de régénération de 180 jours
- Enfin, nous définissons un contexte de stockage pour le compte de stockage spécifié, avec Key1

Dans les instructions ci-dessous, nous attribuons au service Key Vault des autorisations d’opérateur sur votre compte de stockage.

> [!NOTE]
> Veuillez noter qu'une fois que vous avez configuré les clés du compte de stockage managé Azure Key Vault, elles ne doivent **PLUS**être modifiées, sauf via Key Vault. Clés du compte de stockage managé signifie que Key Vault gère la rotation de la clé du compte de stockage

> [!IMPORTANT]
> Un locataire Azure AD fournit à chaque application inscrite un **[principal de service](/azure/active-directory/develop/developer-glossary#service-principal-object)**, qui représente l’identité de l’application. L’ID d’application du principal de service est utilisé quand il reçoit l’autorisation d’accéder à d’autres ressources Azure, via le contrôle d’accès en fonction du rôle (RBAC). Key Vault étant une application Microsoft, elle est préinscrite dans tous les locataires Azure AD sous le même ID d’application dans chaque cloud Azure :
> - Les locataires Azure AD dans le cloud Azure Government utilisent l’ID d’application `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - Les locataires Azure AD dans le cloud public Azure et tous les autres utilisent l’ID d’application `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

> - Actuellement, vous pouvez utiliser pour demander de Key Vault pour gérer un compte de stockage Principal de l’utilisateur et pas un Principal de Service


1. Après avoir créé un compte de stockage, exécutez la commande suivante pour obtenir l’ID de ressource du compte de stockage que vous voulez gérer.

    ```
    az storage account show -n storageaccountname 
    ```
    Champ d’ID de copie hors le résultat de la commande ci-dessus, qui se présente comme suit
    ```
    /subscriptions/0xxxxxx-4310-48d9-b5ca-0xxxxxxxxxx/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```
            "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    
2. Attribuer un rôle RBAC « Stockage compte rôle opérateur de clé Service » à Key Vault, limitant l’étendue d’accès à votre compte de stockage. Pour un compte de stockage classique, utiliser « Classic stockage compte clé Service rôle d’opérateur. »
    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope 93c27d83-f79b-4cb2-8dd4-4aa716542e74
    ```
    
    « 93c27d83-f79b-4cb2-8dd4-4aa716542e74 » est l’ID d’objet pour Key Vault dans le Cloud Public. Pour obtenir l’ID d’objet pour le coffre de clés dans les clouds nationaux consultez la section Important ci-dessus
    
3. Créez un compte de stockage managé Key Vault.     <br /><br />
   Ci-dessous, nous définissons une période de regénération de 90 jours. Après 90 jours, Key Vault regénère « key1 » et remplace la clé active « key2 » par « key1 ». Il désigne maintenant Key1 comme la clé active. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<a name="step-by-step-instructions-on-how-to-use-key-vault-to-create-and-generate-sas-tokens"></a>Instructions étape par étape sur la façon d'utiliser Key Vault pour créer et générer des jetons SAS
--------------------------------------------------------------------------------
Vous pouvez également demander à Key Vault de générer des jetons SAS (Shared Access Signature, signature d'accès partagé). Une signature d'accès partagé fournit un accès délégué aux ressources de votre compte de stockage. Avec une signature d’accès partagé, vous pouvez accorder aux clients l’accès aux ressources dans votre compte de stockage sans partager les clés de votre compte. C’est tout l’intérêt d’utiliser des signatures d’accès partagé dans vos applications : une SAP est un moyen sécurisé de partager vos ressources de stockage sans compromettre vos clés de compte.

Une fois que vous avez terminé les étapes répertoriées ci-dessus, vous pouvez exécuter les commandes suivantes pour demander à Key Vault de générer des jetons SAS pour vous. 

Voici les choses qui peuvent être accomplies avec les étapes ci-dessous
- Définit un compte de sa définition nommé `<YourSASDefinitionName>` sur un compte de stockage géré KeyVault `<YourStorageAccountName>` dans votre coffre `<VaultName>`. 
- Crée un jeton SAS de compte pour les services Blob, Fichier, Table et File d’attente, pour les types de ressources Service, Conteneur et Objet, avec toutes les autorisations, via le protocole https et avec les dates de début et de fin spécifiées
- Configure une définition SAQ de stockage géré par KeyVault dans le coffre, avec l’URI de modèle en tant que le jeton SAS créé ci-dessus, avec un « compte » de type SAS et une validité de N jours
- Récupère le jeton d’accès réel à partir du secret KeyVault correspondant à la définition SAS

1. Dans cette étape, nous allons créer une définition SAS. Une fois que cette définition SAS est créée, vous pouvez demander à Key Vault de générer plus de jetons SAS pour vous. Cette opération nécessite l’autorisation storage/setsas.

```
$sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
```
Vous trouverez [ici](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) plus d’aide sur l’opération ci-dessus

Lorsque cette opération s’exécute correctement, vous devriez voir une sortie similaire à celle ci-dessous. Copiez cela

```console
   "se=2020-01-01&sp=***"
```

1. Dans cette étape, nous allons utiliser la sortie ($sasToken) générée ci-dessus pour créer une définition SAS. Pour plus d’informations, lisez [ceci](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters)   

```
az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
```
                        

 > [!NOTE] 
 > Si l’utilisateur ne dispose pas des autorisations sur le compte de stockage, nous commençons par obtenir l’ID d’objet de l’utilisateur.

 ```
 az ad user show --upn-or-object-id "developer@contoso.com"

 az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
 ```
    
## <a name="fetch-sas-tokens-in-code"></a>Récupérer des jetons SAS dans le code

Dans cette section, nous allons étudier comment vous pouvez effectuer des opérations sur votre compte de stockage en extrayant des [jetons SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) à partir de Key Vault

Dans la section ci-dessous, nous montrons comment récupérer des jetons SAS après la création d’une définition SAS comme indiqué ci-dessus.

> [!NOTE]
>   Il existe 3 façons de s’authentifier sur Key Vault comme vous pouvez le lire dans les [concepts de base](key-vault-whatis.md#basic-concepts)
> - Utilisation de MSI (Managed Service Identity), fortement recommandée
> - Utilisation d’un principal de service et d’un certificat 
> - Utilisation d’un principal de service et d’un mot de passe, non recommandée

```cs
// Once you have a security token from one of the above methods, then create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault. SecretUri is of the format https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Si votre jeton SAS est sur le point d’expirer, extrayez-le à nouveau à partir de Key Vault, puis mettez à jour le code

```cs
// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

### <a name="relevant-azure-cli-commands"></a>Commandes Azure CLI pertinentes

[Commandes de stockage Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

## <a name="see-also"></a>Voir aussi

- [Présentation des clés, des secrets et des certificats](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog de l’équipe Key Vault](https://blogs.technet.microsoft.com/kv/)
