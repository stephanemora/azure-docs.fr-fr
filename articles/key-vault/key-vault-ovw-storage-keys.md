---
title: Gérer les clés du compte de stockage Azure Key Vault et Azure CLI
description: Clés de compte de stockage fournissent une intégration transparente entre Azure Key Vault et basée sur la clé d’accès à un compte de stockage Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 91cc3f96f9cdd231c38232c972c2628d12b9f4b3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476149"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Gérer les clés du compte de stockage Azure Key Vault et Azure CLI 

Azure Key Vault gère les clés pour les comptes de stockage Azure et des comptes de stockage classiques. Vous pouvez utiliser la fonctionnalité de compte de stockage managé Key Vault pour effectuer plusieurs fonctions de gestion de clés pour vous.

Un [compte de stockage Azure](/azure/storage/storage-create-storage-account) utilise des informations d’identification qui se composent d’un nom de compte et d’une clé. La clé est généré automatiquement et sert d’un mot de passe, plutôt que selon une clé de chiffrement. Key Vault gère les clés de compte de stockage en les stockant en tant que [secrets Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). Les clés sont répertoriés (synchronisés) avec un compte de stockage Azure et sont régulièrement RE ou _pivoté_. 

Lorsque vous utilisez la fonctionnalité de clé de compte de stockage géré, considérez les points suivants :

- Valeurs de clés ne sont jamais retournés en réponse à un appelant.
- Uniquement Key Vault doit gérer vos clés de compte de stockage. Ne pas gérer les clés vous-même et éviter toute interférence avec le processus de Key Vault.
- Un seul objet Key Vault doit gérer les clés de compte de stockage. Ne pas autoriser la gestion de clés à partir de plusieurs objets.
- Vous pouvez demander de Key Vault pour gérer votre compte de stockage avec un utilisateur principal, mais pas avec un principal de service.
- Régénère les clés à l’aide de Key Vault uniquement. Ne pas régénérer manuellement vos clés de compte de stockage. 

> [!NOTE]
> Intégration du stockage Azure avec Azure Active Directory (Azure AD) est identités et des accès gestion des services de cloud computing de Microsoft.
> Intégration d’Azure AD est disponible pour [des objets BLOB Azure et files d’attente](https://docs.microsoft.com/azure/storage/common/storage-auth-aad).
> Utiliser Azure AD pour l’authentification et l’autorisation.
> Azure AD offre un accès jeton OAuth2 vers le stockage Azure à l’instar d’Azure Key Vault.
>
> Azure AD vous permet d’authentifier votre application cliente à l’aide d’une identité d’application ou un utilisateur, au lieu des informations d’identification du compte de stockage.
> Vous pouvez utiliser un [Azure AD gérés identité](/azure/active-directory/managed-identities-azure-resources/) lorsque vous exécutez sur Azure. Identités gérées suppriment la nécessité pour l’authentification du client et le stockage des informations d’identification dans ou avec votre application.
> Azure AD utilise le contrôle d’accès en fonction du rôle (RBAC) pour gérer l’autorisation, qui est également pris en charge par Key Vault.

### <a name="service-principal-application-id"></a>ID d’application du principal du service

Un locataire Azure AD fournit à chaque application inscrite avec un [principal du service](/azure/active-directory/develop/developer-glossary#service-principal-object). Le principal du service sert de l’identité de l’application (ID). L’ID d’Application est utilisé pendant l’installation de l’autorisation pour l’accès aux autres ressources Azure au moyen de RBAC.

Key Vault est une application de Microsoft qui est déjà enregistrée dans AD Azure tous les clients. Le coffre de clés est inscrit sous le même ID d’Application et au sein de chaque cloud Azure.

| Locataires | Cloud | ID de l'application |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure public | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Autres  | Quelconque | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Conditions préalables

Avant d’utiliser Key Vault pour gérer votre clé de compte de stockage, passez en revue les conditions préalables :

- Installez [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Créer un [compte de stockage Azure](https://azure.microsoft.com/services/storage/). Suivez [suit](https://docs.microsoft.com/azure/storage/).
- Le nom de compte de stockage doit utiliser uniquement des lettres minuscules et chiffres. La longueur du nom doit être comprise entre 3 et 24 caractères.        
      
## <a name="manage-storage-account-keys"></a>Gérer les clés de compte de stockage

Il existe quatre étapes de base à utiliser Key Vault pour gérer les clés de compte de stockage :

1. Obtenir un compte de stockage existant.
1. Extraire un coffre de clés existant.
1. Ajouter un compte de stockage Key Vault est géré dans le coffre. Définir `key1` comme clé active avec une période de régénération de 180 jours.
1. Utilisez `key1` pour définir un contexte de stockage pour le compte de stockage spécifié.

> [!NOTE]
> Key Vault en tant que service est affecté d’autorisations d’opérateur sur votre compte de stockage.
> 
> Après avoir configuré les clés de compte de stockage Azure Key Vault est géré, vous devez uniquement modifier les clés à l’aide de Key Vault.
> Pour les clés de compte de stockage géré, Key Vault gère la rotation de la clé de compte de stockage.

1. Après avoir créé un compte de stockage, exécutez la commande suivante pour obtenir l’ID de ressource du compte de stockage à gérer :
    ```
    az storage account show -n storageaccountname
    ```

    Copiez la valeur d’ID de ressource à partir de la sortie de commande :
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    Exemple de sortie :
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Attribuer le rôle RBAC de « Stockage compte rôle opérateur de clé Service » à Key Vault. Ce rôle limite l’étendue d’accès à votre compte de stockage. Pour un compte de stockage classique, utilisez le rôle de « Classic stockage compte rôle opérateur de clé Service ».

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope 93c27d83-f79b-4cb2-8dd4-4aa716542e74
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74` est l’ID d’objet pour Key Vault dans le cloud public Azure. Pour obtenir l’ID d’objet pour Key Vault dans le cloud Azure Government, consultez [ID d’application du principal du Service](#service-principal-application-id).
    
1. Créer un compte de stockage gérés de Key Vault :

    Définir une période de régénération de 90 jours. Après 90 jours, Key Vault régénère `key1` et Permute la clé active `key2` à `key1`. `key1` est alors marqué en tant que la clé active. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Créer et générer des jetons

Vous pouvez également demander Key Vault pour générer des jetons de signature d’accès partagé. Une signature d'accès partagé fournit un accès délégué aux ressources de votre compte de stockage. Vous pouvez accorder aux clients l’accès aux ressources dans votre compte de stockage sans partager les clés de votre compte. Une signature d’accès partagé vous offre un moyen sécurisé de partager vos ressources de stockage sans compromettre vos clés de compte.

Les commandes de cette section effectuer les actions suivantes :

- Définir une définition de signature d’accès partagé de compte `<YourSASDefinitionName>`. La définition est définie sur un compte de stockage gérés de Key Vault `<YourStorageAccountName>` dans votre coffre de clés `<VaultName>`.
- Créer un jeton de signature d’accès partagé compte pour les services Blob, fichier, Table et file d’attente. Le jeton est créé pour les types de ressources de Service, du conteneur et objet. Le jeton est créé avec toutes les autorisations, via le protocole https, les dates de début et de fin spécifiées.
- Définir une définition de signature accès partagé de stockage Key Vault est géré dans le coffre. La définition a le modèle URI de jeton de signature d’accès partagé qui a été créé. La définition a le type de signature d’accès partagé `account` et est valide pendant les N derniers jours.
- Récupérer le jeton d’accès réel à partir de la clé secrète de coffre de clés qui correspond à la définition de signature d’accès partagé.

Après avoir terminé les étapes décrites dans la section précédente, exécutez les commandes suivantes pour demander de Key Vault pour générer des jetons de signature d’accès partagé. 

1. Créer une définition de signature d’accès partagé. Une fois la définition de signature d’accès partagé est créée, demandez à Key Vault pour générer plus des jetons de signature accès partagé. Cette opération nécessite le `storage` et `setsas` autorisations.
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    Pour obtenir de l’aide sur l’opération, consultez le [az stockage compte générer sas](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) documentation de référence.

    Une fois que l’opération s’exécute correctement, copiez la sortie.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. Utilisez le `$sasToken` généré par la commande précédente et créer une définition de signature d’accès partagé. Pour plus d’informations sur les paramètres de commande, consultez le [création az keyvault stockage sas-définition](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) documentation de référence.
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    Lorsque l’utilisateur ne dispose des autorisations au compte de stockage, tout d’abord obtenir l’ID d’objet de l’utilisateur :
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Récupérer des jetons dans le code

Exécuter des opérations sur votre compte de stockage en extrayant [partagé des jetons de signature d’accès](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) à partir de Key Vault.

Il existe trois façons de s’authentifier sur Key Vault :

- Utiliser une identité de service géré. Cette approche est vivement recommandée.
- Utiliser un principal de service et un certificat. 
- Utilisez un principal du service et le mot de passe. Cette approche n’est pas recommandée.

Pour plus d’informations, consultez [Azure Key Vault : Concepts de base](key-vault-whatis.md#basic-concepts).

L’exemple suivant montre comment extraire des jetons de signature d’accès partagé. Vous récupérez les jetons après avoir créé une définition de signature d’accès partagé. 

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Si votre jeton de signature d’accès partagé est sur le point d’expirer, extraire le jeton de signature d’accès partagé à nouveau à partir de Key Vault et mettre à jour le code.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Commandes d’interface de ligne de commande Azure

Pour plus d’informations sur les commandes Azure CLI qui sont pertinents pour les comptes de stockage géré, voir la [stockage du coffre de clés az](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) documentation de référence.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [clés, secrets et certificats](https://docs.microsoft.com/rest/api/keyvault/).
- Passez en revue les articles de la [blog de l’équipe Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
