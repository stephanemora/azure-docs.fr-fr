---
title: Démarrage rapide - Bibliothèque de client de clés Azure Key Vault pour Java
description: Fournit un guide de démarrage rapide pour la bibliothèque de client de clés Azure Key Vault pour Java.
author: msmbaldwin
ms.custom: devx-track-java, devx-track-azurecli
ms.author: mbaldwin
ms.date: 01/05/2021
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.openlocfilehash: cb5abf59c446ef0835375bac45d1e852144a6f28
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935272"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-java"></a>Démarrage rapide : Bibliothèque de client de clés Azure Key Vault pour Java
Découvrez comment démarrer avec la bibliothèque de client de clés Azure Key Vault pour Java. Suivez les étapes ci-dessous pour installer le package et tester un exemple de code relatif à des tâches de base.

Ressources supplémentaires :

* [Code source](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)
* [Documentation de référence de l’API](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Documentation du produit](index.yml)
* [Exemples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys/src/samples/java/com/azure/security/keyvault/keys)

## <a name="prerequisites"></a>Prérequis
- Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Kit de développement Java (JDK)](/java/azure/jdk/), version 8 ou ultérieure
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli)

Ce guide de démarrage rapide suppose que vous exécutez [Azure CLI](/cli/azure/install-azure-cli) et [Apache Maven](https://maven.apache.org) dans une fenêtre de terminal Linux.

## <a name="setting-up"></a>Configuration
Ce guide de démarrage rapide utilise la bibliothèque Azure Identity avec Azure CLI pour authentifier l’utilisateur auprès des services Azure. Les développeurs peuvent également utiliser Visual Studio ou Visual Studio Code pour authentifier leurs appels. Pour plus d’informations, consultez [Authentifier le client avec la bibliothèque de client Azure Identity](/java/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Connexion à Azure
1. Exécutez la commande `login`.

    ```azurecli-interactive
    az login
    ```

   Si l’interface CLI peut ouvrir votre navigateur par défaut, elle le fait et charge une page de connexion Azure par la même occasion.

   Sinon, ouvrez une page de navigateur à l’adresse [https://aka.ms/devicelogin](https://aka.ms/devicelogin) et entrez le code d’autorisation affiché dans votre terminal.

2. Dans le navigateur, connectez-vous avec les informations d’identification de votre compte.

### <a name="create-a-new-java-console-app"></a>Créer une application console Java
Dans une fenêtre de console, utilisez la commande `mvn` pour créer une application console Java nommée `akv-keys-java`.

```console
mvn archetype:generate -DgroupId=com.keyvault.keys.quickstart
                       -DartifactId=akv-keys-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Le résultat de la génération du projet doit ressembler à ceci :

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-keys-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Changez de répertoire pour le dossier `akv-keys-java/` créé.

```console
cd akv-keys-java
```

### <a name="install-the-package"></a>Installer le package
Ouvrez le fichier *pom.xml* dans votre éditeur de texte. Ajoutez les éléments de dépendance suivants au groupe de dépendances.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-keys</artifactId>
      <version>4.2.3</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.2.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Créer un groupe de ressources et un coffre de clés
[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Accorder l’accès à votre coffre de clés
Créez une stratégie d’accès pour votre coffre de clés, qui accorde des autorisations de clé à votre compte d’utilisateur.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```

#### <a name="set-environment-variables"></a>Définir des variables d’environnement
Cette application utilise le nom de votre coffre de clés en tant que variable d’environnement appelée `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS ou Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Modèle objet
La bibliothèque de client de clés Azure Key Vault pour Java vous permet de gérer des clés. La section [Exemples de code](#code-examples) montre comment créer un client et créer, récupérer et supprimer une clé.

L’application console complète est [ci-dessous](#sample-code).

## <a name="code-examples"></a>Exemples de code
### <a name="add-directives"></a>Ajouter des directives
Ajoutez les directives suivantes en haut de votre code :

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;
```

### <a name="authenticate-and-create-a-client"></a>Authentifier et créer un client
Dans ce guide de démarrage rapide, un utilisateur connecté est utilisé pour l’authentification auprès de Key Vault, qui est la méthode recommandée pour le développement local. Pour les applications déployées sur Azure, une identité managée doit être affectée à un service d’application ou à une machine virtuelle. Pour plus d’informations, consultez [Vue d’ensemble des identités managées](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Dans l’exemple ci-dessous, le nom de votre coffre de clés est étendu à l’URI du coffre de clés, au format « https://\<your-key-vault-name\>.vault.azure.net ». Cet exemple utilise la classe [« DefaultAzureCredential() »](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential), qui permet d’utiliser le même code dans différents environnements avec des options différentes pour fournir une identité. Pour plus d’informations, consultez [Authentification des informations d’identification Azure par défaut](https://docs.microsoft.com/java/api/overview/azure/identity-readme).

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

KeyClient keyClient = new KeyClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="create-a-key"></a>Créer une clé
Votre application étant authentifiée, vous pouvez créer une clé dans votre coffre de clés en utilisant la méthode `keyClient.createKey`. Cela nécessite un nom pour la clé et un type de clé. Nous avons affecté la valeur « myKey » à la variable `keyName` et utilisons un `KeyType` RSA dans cet exemple.

```java
keyClient.createKey(keyName, KeyType.RSA);
```

Vous pouvez vérifier que la clé a été définie à l’aide de la commande [az keyvault key show](/cli/azure/keyvault/key?#az-keyvault-key-show) :

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Récupérer une clé
Vous pouvez maintenant récupérer la clé créée précédemment à l’aide de la méthode `keyClient.getKey`.

```java
KeyVaultKey retrievedKey = keyClient.getKey(keyName);
 ```

Vous pouvez maintenant accéder aux détails de la clé récupérée avec les opérations comme `retrievedKey.getProperties`, `retrievedKey.getKeyOperations`, etc.

### <a name="delete-a-key"></a>Supprimer une clé
Enfin, nous allons supprimer la clé de votre coffre de clés avec la méthode `keyClient.beginDeleteKey`.

La suppression de clé est une opération longue, dont vous pouvez interroger la progression ou attendre la fin.

```java
SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
deletionPoller.waitForCompletion();
```

Vous pouvez vérifier que la clé a été supprimée avec la commande [az keyvault key show](/cli/azure/keyvault/key?#az-keyvault-key-show) :

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

## <a name="clean-up-resources"></a>Nettoyer les ressources
Une fois que vous n’en avez plus besoin, vous pouvez supprimer votre coffre de clés et le groupe de ressources correspondant en utilisant Azure CLI ou Azure PowerShell.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Exemple de code
```java
package com.keyvault.keys.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        KeyClient keyClient = new KeyClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String keyName = "myKey";

        System.out.print("Creating a key in " + keyVaultName + " called '" + keyName + " ... ");

        keyClient.createKey(keyName, KeyType.RSA);

        System.out.print("done.");
        System.out.println("Retrieving key from " + keyVaultName + ".");

        KeyVaultKey retrievedKey = keyClient.getKey(keyName);

        System.out.println("Your key's ID is '" + retrievedKey.getId() + "'.");
        System.out.println("Deleting your key from " + keyVaultName + " ... ");

        SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous avez créé un coffre de clés et créé, récupéré, puis supprimé une clé. Pour en savoir plus sur Key Vault et sur la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

- Lire la [vue d’ensemble Azure Key Vault](../general/overview.md)
- Consultez la [Vue d’ensemble de la sécurité de Key Vault](../general/security-overview.md)
- Consulter le [Guide du développeur Azure Key Vault](../general/developers-guide.md)
- Découvrir comment [Sécuriser l’accès à un coffre de clés](../general/secure-your-key-vault.md)