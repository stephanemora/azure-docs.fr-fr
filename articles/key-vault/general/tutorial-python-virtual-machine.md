---
title: Tutoriel - Utiliser Azure Key Vault avec une machine virtuelle dans Python | Microsoft Docs
description: Dans ce tutoriel, vous allez configurer une application ASP.NET Core pour lire un secret dans votre coffre de clés.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, tracking-python
ms.openlocfilehash: 453307b304c4cb1899b1de31117c944ac66fcddb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093561"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Tutoriel : Utiliser Azure Key Vault avec une machine virtuelle dans Python

Azure Key Vault vous permet de protéger des secrets tels que les clés API et les chaînes de connexion de base de données nécessaires pour accéder à vos applications, services et ressources.

Dans ce tutoriel, vous allez vous procurer une application console pour lire des informations d’Azure Key Vault. Pour ce faire, vous allez utiliser des identités managées pour des ressources Azure. 

Ce didacticiel explique les procédures suivantes :

> [!div class="checklist"]
> * Création d’un coffre de clés
> * Ajoutez un secret au coffre de clés.
> * Récupération d’un secret à partir du coffre de clés.
> * Créez une machine virtuelle Azure.
> * Activez une identité managée.
> * Attribuez des autorisations à l’identité de machine virtuelle.

Avant de continuer, lisez les [concepts de base de Key Vault](basic-concepts.md). 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Prérequis

Pour Windows, Mac et Linux :
  * [Git](https://git-scm.com/downloads)
  * Ce didacticiel nécessite que vous exécutiez l’interface Azure CLI localement. Vous devez avoir installé Azure CLI 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau l’interface CLI, consultez l’article [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Connexion à Azure

Pour vous connecter à Azure à l’aide de l’interface CLI, entrez :

```azurecli
az login
```

### <a name="create-a-resource-group-and-key-vault"></a>Créer un groupe de ressources et un coffre de clés

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="add-a-secret-to-the-key-vault"></a>Ajouter un secret au coffre de clés

Nous allons ajouter un secret pour montrer comment cela fonctionne. Il peut s’agir d’une chaîne de connexion SQL ou de toute autre information que vous devez sécuriser et garder à disposition pour votre application.

Pour créer un secret dans le coffre de clés appelé **AppSecret**, tapez la commande suivante :

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Ce secret stocke la valeur **MySecret**.

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle
Vous pouvez créer une machine virtuelle à l’aide de l’une des méthodes suivantes :

* [L’interface de ligne de commande Microsoft Azure](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Le portail Azure](../../virtual-machines/windows/quick-create-portal.md)

## <a name="assign-an-identity-to-the-vm"></a>Affecter une identité à la machine virtuelle
Dans cette étape, vous créez une identité affectée par le système pour la machine virtuelle en exécutant la commande suivante dans Azure CLI :

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Notez l’identité affectée par le système qui est affichée dans le code suivant. La sortie de la commande ci-dessus doit être la suivante : 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Attribuer des autorisations à l’identité de machine virtuelle
À présent, vous pouvez attribuer à votre coffre de clés les autorisations d’identité créées précédemment en exécutant la commande suivante :

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-on-to-the-virtual-machine"></a>Connexion à la machine virtuelle

Pour vous connecter à la machine virtuelle, suivez les instructions de l’article [Se connecter à une machine virtuelle Azure exécutant Windows](../../virtual-machines/windows/connect-logon.md).

## <a name="create-and-run-a-sample-python-app"></a>Créer et exécuter l’exemple d’application Python

La section suivante inclut un exemple de fichier nommé *Sample.py*. Il utilise la bibliothèque [requests](https://2.python-requests.org/en/master/) pour effectuer des appels HTTP GET.

## <a name="edit-samplepy"></a>Modifier Sample.py

Après avoir créé *Sample.py*, ouvrez le fichier et copiez le code dans cette section. 

Le code comprend deux étapes :
1. Récupérer un jeton du point de terminaison MSI local sur la machine virtuelle.  
  Cela extrait également un jeton d’Azure AD.
1. Passez le jeton au coffre de clés et récupérez votre secret. 

```python
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource.
    # Resources with an MSI configured recieve an AAD access token by using the Azure Instance Metadata Service (IMDS)
    # IMDS provides an endpoint accessible to all IaaS VMs using a non-routable well-known IP Address
    # To learn more about IMDS and MSI Authentication see the following link: https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets an access_token from Azure AD by using the local MSI endpoint.
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to your key vault.
    KeyVaultURL = "https://{YOUR KEY VAULT NAME}.vault.azure.net/secrets/{YOUR SECRET NAME}?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Vous pouvez afficher la valeur de secret en exécutant le code suivant : 

```console
python Sample.py
```

Le code précédent montre comment effectuer des opérations avec Azure Key Vault dans une machine virtuelle Windows. 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand ils ne sont plus nécessaires, supprimez la machine virtuelle et le coffre de clés.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [API REST Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
