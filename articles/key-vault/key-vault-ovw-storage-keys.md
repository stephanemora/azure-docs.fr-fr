---
title: Gérer les clés de compte de stockage avec Azure Key Vault et l’interface de ligne de commande Azure
description: Les clés de compte de stockage assurent une intégration transparente entre Azure Key Vault et l’accès au compte de stockage Azure basé sur les clés.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 880a85676ff7a0364431b33b90093298b12bffed
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980460"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Gérer les clés de compte de stockage avec Key Vault et l’interface de ligne de commande Azure

Un compte de stockage Azure utilise des informations d’identification comprenant un nom de compte et une clé. La clé qui est générée automatiquement sert de mot de passe et non de clé de chiffrement. Key Vault gère les clés de compte de stockage en les stockant en tant que [secrets Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

Vous pouvez utiliser la fonctionnalité de clé de compte de stockage gérée de Key Vault pour lister (synchroniser) les clés avec un compte de stockage Azure et regénérer (faire tourner) régulièrement les clés. Vous pouvez gérer les clés des comptes de stockage et des comptes de stockage classiques.

Lorsque vous utilisez la fonctionnalité de clé de compte de stockage managé, tenez compte des points suivants :

- Les valeurs de clés ne sont jamais retournées en réponse à l’appelant.
- Seul Key Vault doit gérer vos clés de compte de stockage. Ne gérez pas les clés vous-même et évitez d’interférer avec les processus de Key Vault.
- Un seul objet Key Vault doit gérer les clés de compte de stockage. Vous ne devez pas autoriser la gestion des clients par des objets multiples.
- Vous pouvez demander à Key Vault de gérer votre compte de stockage avec un principal utilisateur, mais pas avec un principal de service.
- Régénérez les clés à l’aide de Key Vault uniquement. Ne régénérez pas manuellement vos clés de compte de stockage.

Nous vous recommandons d’utiliser l’intégration de Stockage Azure avec Azure Active Directory (Azure AD), le service Microsoft basé sur le cloud qui gère les identités et les accès. L’intégration d’Azure AD est disponible pour les [objets blob et les files d’attente Azure](../storage/common/storage-auth-aad.md) et fournit un accès basé sur les jetons OAuth2 au Stockage Azure (comme Azure Key Vault).

Azure AD vous permet d’authentifier votre application cliente en utilisant une identité d’application ou d’utilisateur plutôt que les informations d’identification du compte de stockage. Vous pouvez utiliser une [identité Azure AD managée](/azure/active-directory/managed-identities-azure-resources/) lors de l’exécution sur Azure. Les identités managées suppriment l’authentification du client ainsi que le stockage des informations d’identification dans ou avec votre application.

Azure AD utilise le contrôle d’accès en fonction du rôle (RBAC) pour gérer les autorisations, ce qui est également pris en charge par Key Vault.

## <a name="service-principal-application-id"></a>ID d’application du principal de service

Un locataire Azure AD fournit à chaque application inscrite un [principal de service](/azure/active-directory/develop/developer-glossary#service-principal-object), Le principal de service fait office d’ID d’application, qui est utilisé lors de la configuration des autorisations pour l’accès aux autres ressources Azure à l’aide du contrôle d’accès en fonction du rôle (RBAC).

Key Vault est une application Microsoft préinscrite dans tous les locataires Azure AD. Key Vault est inscrit sous le même ID d’application dans chaque cloud Azure.

| Locataires | Cloud | ID de l'application |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure public | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Autres  | Quelconque | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce guide, vous devez d’abord effectuer ce qui suit :

- [Installer l’interface de ligne de commande Microsoft Azure](/cli/azure/install-azure-cli).
- [Création d’un coffre de clés](quick-create-cli.md)
- [Création d’un compte de stockage Azure](../storage/common/storage-account-create.md?tabs=azure-cli). Le nom du compte de stockage doit contenir uniquement des lettres minuscules et des chiffres. Le nom doit compter 3 à 24 caractères.
      
## <a name="manage-storage-account-keys"></a>Gérer les clés de compte de stockage

### <a name="connect-to-your-azure-account"></a>Se connecter au compte Azure

Authentifiez votre session Azure CLI à l’aide des commandes [az login](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0).

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Octroyer un accès Key Vault à votre compte de stockage

Utilisez la commande Azure CLI [az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest) pour permettre à Key Vault d’accéder à votre compte de stockage. Fournissez à la commande les valeurs de paramètre suivantes :

- `--role`: Transmettez le rôle du contrôle d’accès en fonction du rôle (RBAC) « Rôle de service d’opérateur de clé de compte de stockage ». Ce rôle limite l’étendue d’accès à votre compte de stockage. Pour un type de compte classique, transmettez « Rôle de service d’opérateur de clé de compte de stockage classique » à la place.
- `--assignee-object-id`: Transmettez la valeur « 93c27d83-f79b-4cb2-8dd4-4aa716542e74 », qui est l’ID d’objet pour Key Vault dans le cloud public Azure. (Pour obtenir l’ID d’objet de Key Vault dans le cloud Azure Government, consultez [ID d’application du principal du service](#service-principal-application-id).)
- `--scope`: Transmettez l’ID de ressource de votre compte de stockage, qui se présente sous la forme `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`. Pour trouver votre ID d’abonnement, utilisez la commande Azure CLI [az account list](/cli/azure/account?view=azure-cli-latest#az-account-list) ; pour trouver le nom de votre compte de stockage et le groupe de ressources du compte de stockage, utilisez la commande Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list).

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

### <a name="create-a-key-vault-managed-storage-account"></a>Créer un compte de stockage managé Key Vault

 Créez un compte de stockage managé Key Vault à l’aide de la commande Azure CLI [az keyvault storage](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add). Définissez une période de régénération de 90 jours. Au bout de 90 jours, Key Vault régénère `key1` et remplace la clé active `key2` par `key1`. `key1` est alors désignée comme la clé active. Fournissez à la commande les valeurs de paramètre suivantes :

- `--vault-name`: transmettez le nom de votre coffre de clés. Pour trouver le nom de votre coffre de clés, utilisez la commande Azure CLI [az keyvault list](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list).
- `-n`: transmettez le nom de votre compte de stockage Pour trouver le nom de votre compte de stockage, utilisez la commande Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list).
- `--resource-id`: Transmettez l’ID de ressource de votre compte de stockage, qui se présente sous la forme `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`. Pour trouver votre ID d’abonnement, utilisez la commande Azure CLI [az account list](/cli/azure/account?view=azure-cli-latest#az-account-list) ; pour trouver le nom de votre compte de stockage et le groupe de ressources du compte de stockage, utilisez la commande Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list).
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Jetons de signature d’accès partagé

Vous pouvez également demander à Key Vault de générer des jetons de signature d’accès partagé. Une signature d'accès partagé fournit un accès délégué aux ressources de votre compte de stockage. Vous pouvez accorder aux clients l’accès aux ressources dans votre compte de stockage sans partager les clés de votre compte. Une signature d’accès partagé constitue un moyen sécurisé de partager vos ressources de stockage sans compromettre vos clés de compte.

Les commandes figurant dans cette section effectuent les actions suivantes :

- Définissez une définition de signature d’accès partagé `<YourSASDefinitionName>`. La définition pointe sur un compte de stockage managé Key Vault `<YourStorageAccountName>` dans votre coffre de clés `<YourKeyVaultName>`.
- Créez un jeton de signature d’accès partagé pour les services Blob, Fichier, Table et File d’attente. Le jeton est créé pour les types de ressource Service, Conteneur et Objet. Le jeton est créé avec toutes les autorisations, via le protocole https et avec les dates de début et de fin spécifiées.
- Définissez une définition de signature d’accès partagé au stockage géré par Key Vault dans le coffre. La définition comporte l’URI du modèle du jeton de signature d’accès partagé créé. La définition possède le type de signature d’accès partagé `account` et est valide pendant N jours.
- Vérifiez que la signature d’accès partagé a été enregistrée dans votre coffre de clés en tant que secret.

### <a name="create-a-shared-access-signature-token"></a>Créer un jeton de signature d’accès partagé

Créez une définition de signature d’accès partagé à l’aide de la commande Azure CLI [az storage account generate-sas](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas). Cette opération nécessite les autorisations `storage` et `setsas`.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Une fois l’opération exécutée avec succès, copiez la sortie.

```console
"se=2020-01-01&sp=***"
```

Cette sortie est transmise au paramètre `--template-id` à l’étape suivante.

### <a name="generate-a-shared-access-signature-definition"></a>Générer une définition de signature d’accès partagé

Utilisez la commande Azure CLI [az keyvault storage sas-definition create](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create), en transmettant la sortie de l’étape précédente au paramètre `--template-id`, afin de créer une définition de signature d’accès partagé.  Vous pouvez fournir le nom de votre choix au paramètre `-n`.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Vérifier la définition de la signature d’accès partagé

Vous pouvez vérifier que la définition de la signature d’accès partagé a été stockée dans votre coffre de clés à l’aide des commandes Azure CLI [az keyvault secret list](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) et [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show).

Tout d’abord, recherchez la définition de la signature d’accès partagé dans votre coffre de clés à l’aide de la commande [az keyvault secret list](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list).

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

Le secret correspondant à votre définition SAS a les propriétés suivantes :

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

Vous pouvez maintenant utiliser la commande [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) et la propriété `id` pour afficher le contenu de ce secret.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

La sortie de cette commande affiche la chaîne de votre définition SAS sous la forme `value`.


## <a name="next-steps"></a>Étapes suivantes

- Apprenez-en davantage sur [les clés, les secrets et les certificats](https://docs.microsoft.com/rest/api/keyvault/).
- Passez en revue les articles de [blog de l’équipe Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
- Consultez la documentation de référence sur la commande [az keyvault storage](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest).
