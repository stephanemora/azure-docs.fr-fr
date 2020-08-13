---
title: Démarrage rapide – Bibliothèque cliente Python Azure Key Vault – Gérer les clés
description: Découvrez comment créer, récupérer et supprimer des clés dans un coffre de clés Azure à l’aide de la bibliothèque cliente Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 3/30/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: c590fea274900f580a5c8415f6b04c2f9108f74f
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876935"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Démarrage rapide : Bibliothèque cliente des clés Azure Key Vault pour Python

Bien démarrer avec la bibliothèque de client Azure Key Vault pour Python. Suivez les étapes ci-dessous pour installer le package et tester un exemple de code relatif à des tâches de base.

Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Utilisez la bibliothèque de client Key Vault pour Python pour :

- Accroître la sécurité et le contrôle des clés et des mots de passe
- Créer et importer des clés de chiffrement en quelques minutes
- Réduire la latence avec la mise à l’échelle du cloud et la redondance globale
- Simplifier et automatiser les tâches associées aux certificats TLS/SSL
- Utiliser des HSM (modules de sécurité matériels) validés conformes à la norme FIPS 140-2 de niveau 2

[Documentation de référence de l’API](/python/api/overview/azure/keyvault-keys-readme?view=azure-python) | [Code source bibliothèqueC](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [Package (Index package Python)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Python 2.7, 3.5.3 ou version ultérieure
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/)

Ce guide de démarrage rapide suppose que vous exécutez [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) dans une fenêtre de terminal Linux.

## <a name="setting-up"></a>Configuration

### <a name="install-the-package"></a>Installer le package

À partir de la fenêtre de la console, installez la bibliothèque de clés Azure Key Vault pour Python.

```console
pip install azure-keyvault-keys
```

Pour ce guide de démarrage rapide, vous devez également installer le package azure.identity :

```console
pip install azure.identity
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

Le moyen le plus simple d’authentifier une application .NET cloud consiste à utiliser une identité managée. Pour plus d’informations, consultez [Utiliser une identité managée App Service pour accéder à Azure Key Vault](../general/managed-identity.md). 

Cependant, pour des raisons de simplicité, ce guide de démarrage rapide crée une application pour poste de travail, qui nécessite l’utilisation d’un principal de service et d’une stratégie de contrôle d’accès. Votre principal de service nécessite un nom unique au format « http://&lt;nom_unique_mon_principal_de_service&gt; ».

Créez un principal de service à l’aide de la commande Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principal-name&gt;" --sdk-auth
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
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Prenez note des valeurs de clientId et de clientSecret, car nous allons les utiliser dans l’étape [Définir des variables d'environnement](#set-environmental-variables) ci-dessous.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Accorder au principal de service l’accès à votre coffre de clés

Créez une stratégie d’accès pour votre coffre de clés qui accorde l’autorisation à votre principal de service en passant le clientId à la commande [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy). Accordez au principal de service les autorisations get, list et create sur les clés.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --key-permissions delete get list create 
```

#### <a name="set-environmental-variables"></a>Définir des variables d’environnement

La méthode DefaultAzureCredential dans notre application s’appuie sur trois variables d’environnement : `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` et `AZURE_TENANT_ID`. Définissez ces variables sur les valeurs clientId, clientSecret et tenantId que vous avez notées à l’étape [Créer un principal de service](#create-a-service-principal) au format `export VARNAME=VALUE`. (Cette méthode définit seulement les variables pour votre interpréteur de commandes actuel et les processus créés depuis celui-ci ; pour ajouter ces variables de façon permanente à votre environnement, modifiez votre fichier `/etc/environment `.) 

Vous devrez également enregistrer votre nom de coffre de clés en tant que variable d’environnement appelée `KEY_VAULT_NAME`.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Modèle objet

La bibliothèque de client Azure Key Vault pour Python vous permet de gérer les clés et les ressources associées, par exemple les certificats et les secrets. Les exemples de code ci-dessous vous montrent comment créer un client et comment créer, récupérer et supprimer une clé.

## <a name="code-examples"></a>Exemples de code

### <a name="add-directives"></a>Ajouter des directives

Ajoutez les directives suivantes en haut de votre code :

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Authentifier et créer un client

L’authentification auprès de votre coffre de clés et la création d’un client de coffre de clés dépendent des variables d’environnement de l’étape [Définir des variables d’environnement](#set-environmental-variables) ci-dessus. Le nom de votre coffre de clés est étendu à l’URI du coffre de clés, au format « https://<nom-du-coffre>.vault.azure.net ».

```python
credential = DefaultAzureCredential()

client = KeyClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-key"></a>Enregistrer une clé

Maintenant que votre application est authentifiée, vous pouvez placer une clé dans votre coffre de clés 

```python
rsa_key = client.create_rsa_key(myKey,size=2048)
```

Vous pouvez vérifier que la clé a été définie à l’aide de la commande [az keyvault key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) :

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Récupérer une clé

Vous pouvez maintenant récupérer la clé créée précédemment

```python
retrieved_key = client.get_key(keyName)
print(retrieve_key.name)

 ```

Votre clé est désormais enregistrée en tant que `retrieved_key`.

### <a name="delete-a-key"></a>Supprimer une clé

Enfin, nous allons supprimer la clé de votre coffre de clés

```python
client.delete_key(keyName)
```

Vous pouvez vérifier que la clé a disparu à l’aide de la commande [az keyvault key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) :

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
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

```python
import os
from azure.keyvault.key import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = "myKey"

print("Creating a key in " + keyVaultName + " called '" + keyName  + "` ...")

rsa_key = client.create_rsa_key(myKey,size=2048)

print(" done.")

print("Retrieving your key from " + keyVaultName + ".")

retrieved_key = client.get_key(keyName)

print("Key with name '{0}' was found'.".format(retrieved_key.name))
print("Deleting your key from " + keyVaultName + " ...")

client.begin_delete_key(keyName).result()

print(" done.")
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés, stocké une clé et récupéré cette clé. Pour en savoir plus sur Key Vault et sur la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

- Lire la [vue d’ensemble Azure Key Vault](../general/overview.md)
- Consulter le [Guide du développeur Azure Key Vault](../general/developers-guide.md)
- Passer en revue les [bonnes pratiques relatives à Azure Key Vault](../general/best-practices.md)
