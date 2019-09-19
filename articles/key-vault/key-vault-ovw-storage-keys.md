---
title: Gérer les clés de compte de stockage avec Azure Key Vault et l’interface de ligne de commande Azure
description: Les clés de compte de stockage assurent une intégration transparente entre Azure Key Vault et l’accès au compte de stockage Azure basé sur les clés.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/01/2019
ms.openlocfilehash: 68c9279333e9ee92cbca1034f70801c153b6cdb8
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000553"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Gérer les clés de compte de stockage avec Azure Key Vault et l’interface de ligne de commande Azure 

Azure Key Vault gère les clés des comptes de stockage Azure ainsi que des comptes de stockage Classic. Vous pouvez utiliser la fonctionnalité de compte de stockage géré de Key Vault pour effectuer plusieurs opérations de gestion des clés pour vous.

Un [compte de stockage Azure](/azure/storage/storage-create-storage-account) utilise des informations d’identification qui se composent d’un nom de compte et d’une clé. La clé qui est générée automatiquement sert de mot de passe et non pas de clé de chiffrement. Key Vault gère les clés de compte de stockage en les stockant en tant que [secrets Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). Les clés sont répertoriées (synchronisées) avec un compte Stockage Azure et sont périodiquement régénérées (elles font l’objet d’une _rotation_). 

Lorsque vous utilisez la fonctionnalité de clé de compte de stockage managé, tenez compte des points suivants :

- Les valeurs de clés ne sont jamais retournées en réponse à l’appelant.
- Seul Key Vault doit gérer vos clés de compte de stockage. Ne gérez pas les clés vous-même et évitez d’interférer avec les processus de Key Vault.
- Un seul objet Key Vault doit gérer les clés de compte de stockage. Vous ne devez pas autoriser la gestion des clients par des objets multiples.
- Vous pouvez demander à Key Vault de gérer votre compte de stockage avec un principal utilisateur, mais pas avec un principal de service.
- Régénérez les clés à l’aide de Key Vault uniquement. Ne régénérez pas manuellement vos clés de compte de stockage. 

> [!NOTE]
> L’intégration de Stockage Azure avec Azure Active Directory (Azure AD) est le service Microsoft basé sur le cloud qui gère les identités et les accès.
> L’intégration Azure AD est disponible pour les [objets blob et files d’attente Azure](../storage/common/storage-auth-aad.md).
> Utilisez Azure AD pour l’authentification et l’autorisation.
> Azure AD offre un accès au stockage Azure via un jeton OAuth2, à l’instar d’Azure Key Vault.
>
> Azure AD vous permet d’authentifier votre application cliente en utilisant une identité d’application ou d’utilisateur plutôt que les informations d’identification du compte de stockage.
> Vous pouvez utiliser une [identité Azure AD managée](/azure/active-directory/managed-identities-azure-resources/) lors de l’exécution sur Azure. Les identités managées suppriment l’authentification du client ainsi que le stockage des informations d’identification dans ou avec votre application.
> Azure AD utilise le contrôle d’accès en fonction du rôle (RBAC) pour gérer les autorisations, ce qui est également pris en charge par Key Vault.

### <a name="service-principal-application-id"></a>ID d’application du principal de service

Un locataire Azure AD fournit à chaque application inscrite un [principal de service](/azure/active-directory/develop/developer-glossary#service-principal-object), qui représente l’identité de l’application (ID). L’ID d’application est utilisé lors de la configuration des autorisations pour l’accès aux autres ressources Azure à l’aide du contrôle d’accès en fonction du rôle (RBAC).

Key Vault est une application Microsoft préinscrite dans tous les locataires Azure AD. Le coffre Key Vault est inscrit sous le même ID d’application dans chaque cloud Azure.

| Locataires | Cloud | ID de l'application |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure public | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Autres  | Quelconque | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Prérequis

Avant d’utiliser Key Vault pour gérer votre clé de compte de stockage, passez en revue les conditions préalables suivantes :

- Installez [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Créez un [compte de stockage Azure](https://azure.microsoft.com/services/storage/). Procédez [comme suit](../storage/index.yml).
- Le nom du compte de stockage doit contenir uniquement des lettres minuscules et des chiffres. Le nom doit compter 3 à 24 caractères.        
      
## <a name="manage-storage-account-keys"></a>Gérer les clés de compte de stockage

La gestion des clés de compte de stockage à l’aide de Key Vault comporte quatre étapes de base :

1. Obtenez un compte de stockage existant.
1. Récupérez un coffre de clés existant.
1. Ajoutez un compte de stockage managé Key Vault au coffre. Définissez `key1` en tant que clé active avec une période de régénération de 90 jours.
1. Utilisez `key1` pour définir un contexte de stockage pour le compte de stockage spécifié.

> [!NOTE]
> Le service Key Vault reçoit des autorisations d’opérateur sur votre compte de stockage.
> 
> Après avoir configuré les clés de compte de stockage managé Azure Key Vault, ne modifiez les clés qu’en utilisant Key Vault.
> Concernant les clés du compte de stockage managé, Key Vault gère la rotation de la clé du compte de stockage.

1. Après avoir créé un compte de stockage, exécutez la commande suivante pour obtenir l’ID de ressource du compte de stockage que vous voulez gérer :
    ```
    az storage account show -n storageaccountname
    ```

    Copiez la valeur d’ID de la ressource à partir de la sortie de commande :
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    Exemple de sortie :
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Attribuez le rôle du contrôle d’accès en fonction du rôle (RBAC) « Rôle de service d’opérateur de clé de compte de stockage » à Key Vault. Ce rôle limite l’étendue d’accès à votre compte de stockage. Pour un type de compte classique, utilisez le rôle « Rôle de service d’opérateur de clé de compte de stockage classique ».

    ```
    az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>"
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74` est l’ID d’objet de Key Vault dans le cloud public Azure. Pour obtenir l’ID d’objet de Key Vault dans le cloud Azure Government, consultez [ID d’application du principal du service](#service-principal-application-id).
    
1. Créez un compte de stockage managé Key Vault :

    Définissez une période de régénération de 90 jours. Au bout de 90 jours, Key Vault régénère `key1` et remplace la clé active `key2` par `key1`. `key1` est alors désignée comme la clé active. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Créer et générer des jetons

Vous pouvez également demander à Key Vault de générer des jetons de signature d’accès partagé. Une signature d'accès partagé fournit un accès délégué aux ressources de votre compte de stockage. Vous pouvez accorder aux clients l’accès aux ressources dans votre compte de stockage sans partager les clés de votre compte. Une signature d’accès partagé constitue un moyen sécurisé de partager vos ressources de stockage sans compromettre vos clés de compte.

Les commandes figurant dans cette section effectuent les actions suivantes :

- Définissez une définition de signature d’accès partagé `<YourSASDefinitionName>`. La définition pointe sur un compte de stockage managé Key Vault `<YourStorageAccountName>` dans votre coffre de clés `<VaultName>`.
- Créez un jeton de signature d’accès partagé pour les services Blob, Fichier, Table et File d’attente. Le jeton est créé pour les types de ressource Service, Conteneur et Objet. Le jeton est créé avec toutes les autorisations, via le protocole https et avec les dates de début et de fin spécifiées.
- Définissez une définition de signature d’accès partagé au stockage géré par Key Vault dans le coffre. La définition comporte l’URI du modèle du jeton de signature d’accès partagé créé. La définition possède le type de signature d’accès partagé `account` et est valide pendant N jours.
- Récupérez le jeton d’accès réel à partir du secret Key Vault correspondant à la définition de la signature d’accès partagé.

Après avoir terminé les étapes de la section précédente, exécutez les commandes suivantes pour demander à Key Vault de générer des jetons de signature d’accès partagé. 

1. Créez une définition de signature d’accès partagé. Une fois la définition de signature d’accès partagé créée, demandez à Key Vault de générer d’autres jetons de signature d’accès partagé. Cette opération nécessite les autorisations `storage` et `setsas`.
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    Pour obtenir de l’aide sur l’opération, consultez la documentation de référence sur [az storage account generate-sas](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas).

    Une fois l’opération exécutée avec succès, copiez la sortie.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. Utilisez le `$sasToken` généré par la commande précédente pour créer une définition de signature d’accès partagé. Pour plus d’informations sur les paramètres de commande, consultez la documentation de référence sur [az keyvault storage sas-definition create](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters).
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    Lorsque l’utilisateur ne dispose pas des autorisations sur le compte de stockage, commencez par obtenir l’ID d’objet de l’utilisateur :
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Récupérer des jetons dans le code

Exécutez des opérations sur votre compte de stockage en extrayant des [jetons d’accès partagé](../storage/common/storage-dotnet-shared-access-signature-part-1.md) à partir de Key Vault.

Il existe trois façons de s’authentifier auprès de Key Vault :

- Utiliser l’identité du service managé. Cette approche est vivement recommandée.
- Utiliser un principal de service et un certificat. 
- Utiliser un principal de service et un mot de passe. Cette approche est déconseillée.

Pour plus d’informations, consultez [Azure Key Vault : concepts de base](basic-concepts.md).

L’exemple suivant montre comment récupérer des jetons de signature d’accès partagé. Vous pouvez récupérer les tokens après avoir créé une définition de signature d’accès partagé. 

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

Si votre jeton de signature d’accès partagé est sur le point d’expirer, récupérez-le à nouveau à partir de Key Vault, puis mettez à jour le code.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Commandes d’interface de ligne de commande Azure

Pour plus d’informations sur les commandes d’interface de ligne de commande Azure qui sont pertinentes pour les comptes de stockage managés, consultez la documentation de référence sur [az keyvault storage](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest).

## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur [les clés, les secrets et les certificats](https://docs.microsoft.com/rest/api/keyvault/).
- Passez en revue les articles de [blog de l’équipe Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
