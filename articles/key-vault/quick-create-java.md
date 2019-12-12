---
title: Démarrage rapide - Bibliothèque de client Azure Key Vault pour Java
description: Fournit des critères de format et de contenu pour l’écriture de guides de démarrage rapide liés aux bibliothèques de client du kit Azure SDK.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: e06c8d5373f8740e8091daae242a4f7af6c0ea4a
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995862"
---
# <a name="quickstart-azure-key-vault-client-library-for-java"></a>Démarrage rapide : Bibliothèque de client Azure Key Vault pour Java

Bien démarrer avec la bibliothèque de client Azure Key Vault pour Java. Suivez les étapes ci-dessous pour installer le package et tester un exemple de code relatif à des tâches de base.

Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Utilisez la bibliothèque de client Key Vault pour Java pour :

- Accroître la sécurité et le contrôle des clés et des mots de passe
- Créer et importer des clés de chiffrement en quelques minutes
- Réduire la latence avec la mise à l’échelle du cloud et la redondance globale
- Simplifier et automatiser les tâches associées aux certificats SSL/TLS
- Utiliser des HSM (modules de sécurité matériels) validés conformes à la norme FIPS 140-2 de niveau 2

[Code source](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault) | [Documentation de référence de l’API](https://azure.github.io/azure-sdk-for-java) | [Documentation du produit](index.yml) | [Exemples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Kit de développement Java (JDK)](/java/azure/jdk/?view=azure-java-stable), version 8 ou ultérieure
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/overview)

Ce guide de démarrage rapide suppose que vous exécutez [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) et [Apache Maven](https://maven.apache.org) dans une fenêtre de terminal Linux.

## <a name="setting-up"></a>Configuration

### <a name="create-new-java-console-app"></a>Créer une application console Java

Dans une fenêtre de console, utilisez la commande `mvn` pour créer une application console Java nommée `akv-java`.

```console
mvn archetype:generate -DgroupId=com.keyvault.quickstart
                       -DartifactId=akv-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

Le résultat de la génération du projet doit ressembler à ceci :

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Changez de répertoire pour le dossier akv-java/ nouvellement créé.

```console
cd akv-java
```

### <a name="install-the-package"></a>Installer le package

Ouvrez le fichier *pom.xml* dans votre éditeur de texte. Ajoutez les éléments de dépendance suivants au groupe de dépendances.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-secrets</artifactId>
      <version>4.0.0</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.0.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Créer un groupe de ressources et un coffre de clés

Ce guide de démarrage rapide utilise un coffre de clés Azure créé au préalable. Vous pouvez créer un coffre de clés en suivant les étapes décrites dans le [guide de démarrage rapide d’Azure CLI](quick-create-cli.md), le [guide de démarrage rapide d’Azure PowerShell](quick-create-powershell.md) ou le [guide de démarrage rapide du portail Azure](quick-create-portal.md). Vous pouvez aussi exécuter les commandes Azure CLI ci-dessous.

> [!Important]
> Chaque coffre de clés doit avoir un nom unique. Remplacez <your-unique-keyvault-name> par le nom de votre coffre de clés dans les exemples suivants.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Créer un principal du service

Le moyen le plus simple d’authentifier une application cloud consiste à utiliser une identité managée. Pour plus d’informations, consultez [Utiliser une identité managée App Service pour accéder à Azure Key Vault](managed-identity.md). Cependant, pour des raisons de simplicité, ce guide de démarrage rapide crée une application pour poste de travail, qui nécessite l’utilisation d’un principal de service et d’une stratégie de contrôle d’accès.

Créez un principal de service à l’aide de la commande Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Cette opération retourne une série de paires clé/valeur. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Prenez note des valeurs clientId, clientSecret et tenantId, car nous allons les utiliser dans les deux étapes suivantes.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Accorder au principal de service l’accès à votre coffre de clés

Créez une stratégie d’accès pour votre coffre de clés qui accorde l’autorisation à votre principal de service en passant le clientId à la commande [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy). Accordez au principal de service les autorisations get, list et set sur les clés et les secrets.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Définir des variables d’environnement

La méthode DefaultAzureCredential dans notre application s’appuie sur trois variables d’environnement : `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` et `AZURE_TENANT_ID`. Définissez ces variables sur les valeurs clientId, clientSecret et tenantId que vous avez notées à l’étape [Créer un principal de service](#create-a-service-principal) ci-dessus. Utilisez le format `export VARNAME=VALUE` pour définir vos variables d’environnement. (Cette méthode définit seulement les variables pour votre interpréteur de commandes actuel et les processus créés depuis celui-ci ; pour ajouter ces variables de façon permanente à votre environnement, modifiez votre fichier `/etc/environment `.) 

Vous devrez également enregistrer votre nom de coffre de clés en tant que variable d’environnement appelée `KEY_VAULT_NAME`.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Modèle objet

La bibliothèque de client Azure Key Vault pour Java vous permet de gérer des clés et les ressources associées, par exemple des certificats et des secrets. Les exemples de code ci-dessous vous montrent comment créer un client et définir, récupérer et supprimer un secret.

L’application console complète est disponible à l’adresse https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Exemples de code

### <a name="add-directives"></a>Ajouter des directives

Ajoutez les directives suivantes en haut de votre code :

```java
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>Authentifier et créer un client

L’authentification auprès de votre coffre de clés et la création d’un client de coffre de clés dépendent des variables d’environnement de l’étape [Définir des variables d’environnement](#set-environmental-variables) ci-dessus. Le nom de votre coffre de clés est étendu à l’URI du coffre de clés, au format `https://<your-key-vault-name>.vault.azure.net`.

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String kvUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(kvUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Enregistrer un secret

Maintenant que votre application est authentifiée, vous pouvez placer un secret dans votre coffre en utilisant la méthode `secretClient.setSecret`. Ceci nécessite un nom pour la clé secrète : dans cet exemple, nous avons affecté la valeur « mySecret » à la variable `secretName`.  

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

Vous pouvez vérifier que le secret a été défini à l’aide de la commande [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Récupérer un secret

Vous pouvez maintenant récupérer la valeur définie précédemment avec la méthode `secretClient.getSecret`.

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

Vous pouvez à présent accéder à la valeur du secret récupéré avec `retrievedSecret.getValue()`.

### <a name="delete-a-secret"></a>supprimer un secret

Enfin, nous allons supprimer le secret de votre coffre de clés avec la méthode `secretClient.beginDeleteSecret`.

```java
secretClient.beginDeleteSecret(secretName);
```

Vous pouvez vérifier que le secret a été effacé à l’aide de la commande [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Une fois que vous n’en avez plus besoin, vous pouvez supprimer votre coffre de clés et le groupe de ressources correspondant à l’aide d’Azure CLI ou d’Azure PowerShell.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Exemple de code

```java
package com.keyvault.quickstart;

import java.io.Console;   

import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;

public class App {

    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {

        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String kvUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and kv uri = %s \n", keyVaultName, kvUri);

        SecretClient secretClient = new SecretClientBuilder()
            .vaultUrl(kvUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();


        Console con = System.console();  

        String secretName = "mySecret";

        System.out.println("Input the value of your secret > ");
        String secretValue = con.readLine();

        System.out.print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ... ");

        secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));

        System.out.println("done.");

        System.out.println("Forgetting your secret.");
        secretValue = "";
        System.out.println("Your secret is '" + secretValue + "'.");

        System.out.println("Retrieving your secret from " + keyVaultName + ".");

        KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);

        System.out.println("Your secret is '" + retrievedSecret.getValue() + "'.");
        System.out.print("Deleting your secret from " + keyVaultName + " ... ");

        secretClient.beginDeleteSecret(secretName);

        System.out.println("done.");


    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés, enregistré un secret et récupéré ce secret. Pour en savoir plus sur Key Vault et sur la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

- Lire la [vue d’ensemble Azure Key Vault](key-vault-overview.md)
- Consulter le [Guide du développeur Azure Key Vault](key-vault-developers-guide.md)
- Découvrir les [clés, secrets et certificats](about-keys-secrets-and-certificates.md)
- Passer en revue les [bonnes pratiques relatives à Azure Key Vault](key-vault-best-practices.md)
