---
title: Démarrage rapide - Bibliothèque de client Azure Key Vault pour Node.js (v4)
description: Découvrir comment créer, récupérer et supprimer des secrets dans un coffre de clés Azure en utilisant la bibliothèque de client Node.js
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-javascript
ms.openlocfilehash: 99424e5a7b39d3f60f3337ff10bf7d9265e2ab4c
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422876"
---
# <a name="quickstart-azure-key-vault-client-library-for-nodejs-v4"></a>Démarrage rapide : Bibliothèque de client Azure Key Vault pour Node.js (v4)

Bien démarrer avec la bibliothèque de client Azure Key Vault pour Node.js. Suivez les étapes ci-dessous pour installer le package et tester un exemple de code relatif à des tâches de base.

Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Utilisez la bibliothèque de client Key Vault pour Node.js pour :

- Accroître la sécurité et le contrôle des clés et des mots de passe
- Créer et importer des clés de chiffrement en quelques minutes
- Réduire la latence avec la mise à l’échelle du cloud et la redondance globale
- Simplifier et automatiser les tâches associées aux certificats TLS/SSL
- Utiliser des HSM (modules de sécurité matériels) validés conformes à la norme FIPS 140-2 de niveau 2

[Documentation de référence sur les API](https://docs.microsoft.com/javascript/api/overview/azure/key-vault-index?view=azure-node-latest) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [Package (npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org) actuel pour votre système d’exploitation.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/)

Ce guide de démarrage rapide suppose que vous exécutez [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) dans une fenêtre de terminal Linux.

## <a name="setting-up"></a>Configuration

### <a name="install-the-package"></a>Installer le package

Dans la fenêtre de console, installez la bibliothèque de secrets Azure Key Vault pour Node.js.

```console
npm install @azure/keyvault-secrets
```

Pour ce guide de démarrage rapide, vous devez également installer le package azure.identity :

```console
npm install @azure/identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Créer un groupe de ressources et un coffre de clés

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Créer un principal du service

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Accorder au principal de service l’accès à votre coffre de clés

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>Définir des variables d’environnement

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>Modèle objet

La bibliothèque de client Azure Key Vault pour Node.js vous permet de gérer des clés et des ressources associées, comme des certificats et des secrets. Les exemples de code ci-dessous vous montrent comment créer un client et définir, récupérer et supprimer un secret.

L’application console complète est disponible à l’adresse https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Exemples de code

### <a name="add-directives"></a>Ajouter des directives

Ajoutez les directives suivantes en haut de votre code :

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>Authentifier et créer un client

L’authentification auprès de votre coffre de clés et la création d’un client de coffre de clés dépendent des variables d’environnement de l’étape [Définir les variables d’environnement](#set-environmental-variables) ci-dessus, et du [constructeur SecretClient](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#secretclient-string--tokencredential--pipelineoptions-). 

Le nom de votre coffre de clés est étendu à l’URI du coffre de clés, au format `https://<your-key-vault-name>.vault.azure.net`. 

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>Enregistrer un secret

Maintenant que votre application est authentifiée, vous pouvez placer un secret dans votre coffre avec la [méthode client.setSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#setsecret-string--string--setsecretoptions-). Ceci nécessite un nom pour le secret : dans cet exemple, nous utilisons « mySecret ».  

```javascript
await client.setSecret(secretName, secretValue);
```

Vous pouvez vérifier que le secret a été défini à l’aide de la commande [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Récupérer un secret

Vous pouvez maintenant récupérer la valeur définie précédemment avec la [méthode client.getSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#getsecret-string--getsecretoptions-).

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

Votre secret est désormais enregistré en tant que `retrievedSecret.value`.

### <a name="delete-a-secret"></a>supprimer un secret

Enfin, nous supprimons le secret de votre coffre de clés avec la [méthode client.beginDeleteSecret](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#begindeletesecret-string--begindeletesecretoptions-).

```javascript
await client.beginDeleteSecret(secretName)
```

Vous pouvez vérifier que le secret a été effacé à l’aide de la commande [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois que vous n’en avez plus besoin, vous pouvez supprimer votre coffre de clés et le groupe de ressources correspondant à l’aide d’Azure CLI ou d’Azure PowerShell.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Exemple de code

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");
  console.log("Deleting your secret from " + keyVaultName + " ...");

  await client.beginDeleteSecret(secretName);

  console.log("Done.");

}

main()

```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés, enregistré un secret et récupéré ce secret. Pour en savoir plus sur Key Vault et sur la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

- Lire la [vue d’ensemble Azure Key Vault](../general/overview.md)
- Consulter le [Guide du développeur Azure Key Vault](../general/developers-guide.md)
- Passer en revue les [bonnes pratiques relatives à Azure Key Vault](../general/best-practices.md)