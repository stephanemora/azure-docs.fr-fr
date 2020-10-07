---
title: Démarrage rapide – Bibliothèque cliente Python Azure Key Vault – Gérer les secrets
description: Découvrez comment créer, récupérer et supprimer des secrets d’un coffre de clés Azure à l’aide de la bibliothèque cliente Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: cd8a5751c018b9b3b3b2ef96765545f2edab685b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89489202"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>Démarrage rapide : Bibliothèque cliente des secrets Azure Key Vault pour Python

Bien démarrer avec la bibliothèque de client Azure Key Vault pour Python. Suivez les étapes ci-dessous pour installer le package et tester un exemple de code relatif à des tâches de base. En stockant des secrets à l’aide de Key Vault, vous évitez de les stocker dans votre code, ce qui renforce la sécurité de votre application.

[Documentation de référence de l’API](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python) | [Code source bibliothèqueC](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets) | [Package (Index package Python)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="set-up-your-local-environment"></a>Configurer votre environnement local

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Installez la bibliothèque de secrets Key Vault :

    ```terminal
    pip install azure-keyvault-secrets
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Créer un groupe de ressources et un coffre de clés

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Accorder au principal de service l’accès à votre coffre de clés

Exécutez la commande [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) suivante pour autoriser votre principal de service pour les opérations get, list et set sur les secrets. Cette commande s’appuie sur les variables d’environnement `KEY_VAULT_NAME` et `AZURE_CLIENT_ID` créées aux étapes précédentes.

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

---

Cette commande s’appuie sur les variables d’environnement `KEY_VAULT_NAME` et `AZURE_CLIENT_ID` créées aux étapes précédentes.

Pour plus d’informations, consultez [Attribuer une stratégie d’accès – CLI](../general/assign-access-policy-cli.md).

## <a name="create-the-sample-code"></a>Créer l’exemple de code

La bibliothèque de client Azure Key Vault pour Python vous permet de gérer les secrets et les ressources associées comme les certificats et les clés de chiffrement. L’exemple de code suivant vous montre comment créer un client et définir, récupérer et supprimer un secret.

Créez un fichier nommé *kv_secrets.py* qui contient ce code.

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = input("Input a name for your secret > ")
secretValue = input("Input a value for your secret > ")

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...")

client.set_secret(secretName, secretValue)

print(" done.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Exécuter le code

Vérifiez que le code de la section précédente se trouve dans un fichier nommé *kv_secrets.py*. Exécutez ensuite le code avec la commande suivante :

```terminal
python kv_secrets.py
```

- Si vous rencontrez des erreurs d’autorisation, vérifiez que vous avez exécuté la commande [`az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).
- Le fait de réexécuter le code avec le même nom de secret a pour effet de générer l’erreur « Le secret (en conflit) <name> est actuellement à l’état supprimé mais récupérable. » Utilisez un nom de secret différent.

## <a name="code-details"></a>Détails du code

### <a name="authenticate-and-create-a-client"></a>Authentifier et créer un client

Dans le code précédent, l’objet [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) utilise les variables d’environnement que vous avez créées pour votre principal de service. Vous fournissez ces informations d’identification chaque fois que vous créez un objet client à partir d’une bibliothèque Azure, par exemple [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python), ainsi que l’URI de la ressource que vous souhaitez utiliser via ce client :

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Enregistrer un secret

Une fois que vous avez obtenu l’objet client pour le coffre de clés, vous pouvez stocker un secret à l’aide de la méthode [set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#set-secret-name--value----kwargs-) : 

```python
client.set_secret(secretName, secretValue)
```

L’appel de `set_secret` génère un appel à l’API REST Azure pour le coffre de clés.

Au moment de traiter la demande, Azure authentifie l’identité de l’appelant (le principal du service) à partir de l’objet d’informations d’identification que vous avez fourni au client.

Il vérifie aussi que l’appelant est autorisé à effectuer l’action demandée. Vous avez précédemment accordé cette autorisation au principal du service à l’aide de la commande [`az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).

### <a name="retrieve-a-secret"></a>Récupérer un secret

Pour lire un secret à partir de Key Vault, utilisez la méthode [get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-) :

```python
retrieved_secret = client.get_secret(secretName)
 ```

La valeur du secret est contenue dans `retrieved_secret.value`.

Vous pouvez aussi récupérer un secret avec la commande Azure CLI [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show).

### <a name="delete-a-secret"></a>Supprimer un secret

Pour supprimer un secret, utilisez la méthode [begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#begin-delete-secret-name----kwargs-) :

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

La méthode `begin_delete_secret` est asynchrone et retourne un objet observateur. L’appel de la méthode `result` de l’observateur attend la fin de son exécution.

Vous pouvez vérifier que le secret a été supprimé avec la commande Azure CLI [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show).

Une fois supprimé, un secret reste à l’état supprimé mais récupérable pour un temps. Si vous réexécutez le code, utilisez un nom de secret différent.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez aussi tenter une expérience avec des [certificats](../certificates/quick-create-python.md) et des [clés](../keys/quick-create-python.md), vous pouvez réutiliser le coffre de clés créé dans cet article.

Sinon, quand vous en avez terminé avec les ressources créées dans cet article, utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qu’il contient :

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble Azure Key Vault](../general/overview.md)
- [Guide du développeur Azure Key Vault](../general/developers-guide.md)
- [Bonnes pratiques relatives à Azure Key Vault](../general/best-practices.md)
- [S’authentifier auprès de Key Vault](../general/authentication.md)
