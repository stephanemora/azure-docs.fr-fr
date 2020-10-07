---
title: Démarrage rapide – Bibliothèque de client Python Azure Key Vault – Gérer les certificats
description: Découvrez comment créer, récupérer et supprimer des certificats dans un coffre de clés Azure à l’aide de la bibliothèque cliente Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b9ff7397ad29ac681e21c32608ade9c6ce557c37
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89488624"
---
# <a name="quickstart-azure-key-vault-certificates-client-library-for-python"></a>Démarrage rapide : Bibliothèque de client de certificats Azure Key Vault pour Python

Bien démarrer avec la bibliothèque de client Azure Key Vault pour Python. Suivez les étapes ci-dessous pour installer le package et tester un exemple de code relatif à des tâches de base. En stockant des certificats à l’aide de Key Vault, vous évitez de les stocker dans votre code, ce qui renforce la sécurité de votre application.

[Documentation de référence de l’API](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python) | [Code source bibliothèqueC](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates) | [Package (Index package Python)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="set-up-your-local-environment"></a>Configurer votre environnement local

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Installez la bibliothèque de certificats Key Vault :

    ```terminal
    pip install azure-keyvault-certificates
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Créer un groupe de ressources et un coffre de clés

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>Accorder au principal de service l’accès à votre coffre de clés

Exécutez la commande [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) suivante pour autoriser votre principal de service pour les opérations get, list et create sur les certificats.

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create 
```

---

Cette commande s’appuie sur les variables d’environnement `KEY_VAULT_NAME` et `AZURE_CLIENT_ID` créées aux étapes précédentes.

Pour plus d’informations, consultez [Attribuer une stratégie d’accès – CLI](../general/assign-access-policy-cli.md).

## <a name="create-the-sample-code"></a>Créer l’exemple de code

La bibliothèque de client Azure Key Vault pour Python vous permet de gérer les certificats et les ressources associées comme les secrets et les clés de chiffrement. L’exemple de code suivant vous montre comment créer un client et définir, récupérer et supprimer un secret.

Créez un fichier nommé *kv_certificates.py* qui contient ce code.

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Exécuter le code

Vérifiez que le code de la section précédente se trouve dans un fichier nommé *kv_certificates.py*. Exécutez ensuite le code avec la commande suivante :

```terminal
python kv_certificates.py
```

- Si vous rencontrez des erreurs d’autorisation, vérifiez que vous avez exécuté la commande [`az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).
- Le fait de réexécuter le code avec le même nom de clé a pour effet de générer l’erreur « Le certificat (en conflit) <name> est actuellement à l’état supprimé mais récupérable. » Utilisez un nom de clé différent.

## <a name="code-details"></a>Détails du code

### <a name="authenticate-and-create-a-client"></a>Authentifier et créer un client

Dans le code précédent, l’objet [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) utilise les variables d’environnement que vous avez créées pour votre principal de service. Vous fournissez ces informations d’identification chaque fois que vous créez un objet client à partir d’une bibliothèque Azure, par exemple [`CertificateClient`](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python), ainsi que l’URI de la ressource que vous souhaitez utiliser par l’intermédiaire de ce client :

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Enregistrer un certificat

Une fois que vous avez obtenu l’objet client pour le coffre de clés, vous pouvez créer un certificat en utilisant la méthode [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-create-certificate-certificate-name--policy----kwargs-) : 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

Ici, le certificat a besoin d’une stratégie obtenue avec la méthode [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?view=azure-python#get-default--).

L’appel d’une méthode `begin_create_certificate` génère un appel asynchrone à l’API REST Azure pour le coffre de clés. L’appel asynchrone retourne un objet observateur. Pour attendre le résultat de l’opération, appelez la méthode `result` de l’observateur.

Au moment de traiter la demande, Azure authentifie l’identité de l’appelant (le principal du service) à partir de l’objet d’informations d’identification que vous avez fourni au client.

Il vérifie aussi que l’appelant est autorisé à effectuer l’action demandée. Vous avez précédemment accordé cette autorisation au principal du service à l’aide de la commande [`az keyvault set-policy`](#give-the-service-principal-access-to-your-key-vault).

### <a name="retrieve-a-certificate"></a>Récupérer un certificat

Pour lire un certificat à partir de Key Vault, utilisez la méthode [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#get-certificate-certificate-name----kwargs-) :

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Vous pouvez aussi vérifier que le certificat a été défini avec la commande Azure CLI [az keyvault certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show).

### <a name="delete-a-certificate"></a>Supprimer un certificat

Pour supprimer un certificat, utilisez la méthode [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-delete-certificate-certificate-name----kwargs-) :

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

La méthode `begin_delete_certificate` est asynchrone et retourne un objet observateur. L’appel de la méthode `result` de l’observateur attend la fin de son exécution.

Vous pouvez vérifier que le certificat est supprimé avec la commande Azure CLI [az keyvault certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show).

Une fois supprimé, un certificat reste à l’état supprimé mais récupérable pour un temps. Si vous réexécutez le code, utilisez un nom de certificat différent.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez aussi tenter une expérience avec des [secrets](../secrets/quick-create-python.md) et des [clés](../keys/quick-create-python.md), vous pouvez réutiliser le coffre de clés créé dans cet article.

Sinon, quand vous en avez terminé avec les ressources créées dans cet article, utilisez la commande suivante pour supprimer le groupe de ressources et toutes les ressources qu’il contient :

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble Azure Key Vault](../general/overview.md)
- [Guide du développeur Azure Key Vault](../general/developers-guide.md)
- [Bonnes pratiques relatives à Azure Key Vault](../general/best-practices.md)
- [S’authentifier auprès de Key Vault](../general/authentication.md)
