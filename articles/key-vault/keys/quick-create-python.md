---
title: Démarrage rapide – Bibliothèque cliente Python Azure Key Vault – Gérer les clés
description: Découvrez comment créer, récupérer et supprimer des clés dans un coffre de clés Azure à l’aide de la bibliothèque cliente Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 44942067756f82c224decc218de17bf7dbc69734
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89482121"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Démarrage rapide : Bibliothèque cliente des clés Azure Key Vault pour Python

Bien démarrer avec la bibliothèque de client Azure Key Vault pour Python. Suivez les étapes ci-dessous pour installer le package et tester un exemple de code relatif à des tâches de base. En stockant des clés de chiffrement à l’aide de Key Vault, vous évitez de les stocker dans votre code, ce qui renforce la sécurité de votre application.

[Documentation de référence de l’API](/python/api/overview/azure/keyvault-keys-readme?view=azure-python) | [Code source bibliothèqueC](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys) | [Package (Index package Python)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="set-up-your-local-environment"></a>Configurer votre environnement local

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Installez la bibliothèque de clés Key Vault :

    ```terminal
    pip install azure-keyvault-keys
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Créer un groupe de ressources et un coffre de clés

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Accorder au principal de service l’accès à votre coffre de clés

Exécutez la commande [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) suivante pour autoriser votre principal de service pour les opérations delete, get, list et create sur les clés. 

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

---

Cette commande s’appuie sur les variables d’environnement `KEY_VAULT_NAME` et `AZURE_CLIENT_ID` créées aux étapes précédentes.

Pour plus d’informations, consultez [Attribuer une stratégie d’accès – CLI](../general/assign-access-policy-cli.md).

## <a name="create-the-sample-code"></a>Créer l’exemple de code

La bibliothèque de client Azure Key Vault pour Python vous permet de gérer les clés de chiffrement et les ressources associées, par exemple les certificats et les secrets. L’exemple de code suivant vous montre comment créer un client et définir, récupérer et supprimer un secret.

Créez un fichier nommé *kv_keys.py* qui contient ce code.

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Exécuter le code

Vérifiez que le code de la section précédente se trouve dans un fichier nommé *kv_keys.py*. Exécutez ensuite le code avec la commande suivante :

```terminal
python kv_keys.py
```

- Si vous rencontrez des erreurs d’autorisation, vérifiez que vous avez exécuté la commande [`az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).
- Le fait de réexécuter le code avec le même nom de clé a pour effet de générer l’erreur « La clé (en conflit) <name> est actuellement à l’état supprimé mais récupérable. » Utilisez un nom de clé différent.

## <a name="code-details"></a>Détails du code

### <a name="authenticate-and-create-a-client"></a>Authentifier et créer un client

Dans le code précédent, l’objet [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) utilise les variables d’environnement que vous avez créées pour votre principal de service. Vous fournissez ces informations d’identification chaque fois que vous créez un objet client à partir d’une bibliothèque Azure, par exemple [`KeyClient`](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python), ainsi que l’URI de la ressource que vous souhaitez utiliser via ce client :

```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Enregistrer une clé

Une fois que vous avez obtenu l’objet client pour le coffre de clés, vous pouvez stocker une clé à l’aide de la méthode [create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-rsa-key-name----kwargs-) : 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

Vous pouvez aussi utiliser [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-key-name--key-type----kwargs-) ou [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-ec-key-name----kwargs-).

L’appel d’une méthode `create` génère un appel à l’API REST Azure pour le coffre de clés.

Au moment de traiter la demande, Azure authentifie l’identité de l’appelant (le principal du service) à partir de l’objet d’informations d’identification que vous avez fourni au client.

Il vérifie aussi que l’appelant est autorisé à effectuer l’action demandée. Vous avez précédemment accordé cette autorisation au principal du service à l’aide de la commande [`az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).

## <a name="retrieve-a-key"></a>Récupérer une clé

Pour lire une clé à partir de Key Vault, utilisez la méthode [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#get-key-name--version-none----kwargs-) :

```python
retrieved_key = client.get_key(keyName)
 ```

Vous pouvez aussi vérifier que la clé a été définie à l’aide de la commande Azure CLI [az keyvault key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show).

### <a name="delete-a-key"></a>Supprimer une clé

Pour supprimer une clé, utilisez la méthode [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#begin-delete-key-name----kwargs-) :

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

La méthode `begin_delete_key` est asynchrone et retourne un objet observateur. L’appel de la méthode `result` de l’observateur attend la fin de son exécution.

Vous pouvez vérifier que la clé est supprimée avec la commande Azure CLI [az keyvault certificate show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show).

Une fois supprimée, une clé reste à l’état supprimé mais récupérable pour un temps. Si vous réexécutez le code, utilisez un nom de clé différent.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez aussi tenter une expérience avec des [certificats](../certificates/quick-create-python.md) et des [secrets](../secrets/quick-create-python.md), vous pouvez réutiliser le coffre de clés créé dans cet article.

Sinon, quand vous en avez terminé avec les ressources créées dans cet article, utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qu’il contient :

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble Azure Key Vault](../general/overview.md)
- [Guide du développeur Azure Key Vault](../general/developers-guide.md)
- [Bonnes pratiques relatives à Azure Key Vault](../general/best-practices.md)
- [S’authentifier auprès de Key Vault](../general/authentication.md)
