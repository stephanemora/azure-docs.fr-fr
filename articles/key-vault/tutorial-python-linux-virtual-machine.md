---
title: Tutoriel - Utiliser Azure Key Vault avec une machine virtuelle Linux Azure et le langage Python | Microsoft Docs
description: 'Didacticiel : configurer une application ASP.NET Core pour lire un secret dans le coffre de clés'
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 5f56022be7968d3be65fd06fef791d859acf14c0
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585231"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-linux-virtual-machine-in-python"></a>Utiliser Azure Key Vault avec une machine virtuelle Linux Azure et le langage Python

Azure Key Vault vous permet de protéger des secrets tels que les clés API et les chaînes de connexion de base de données nécessaires pour accéder à vos applications, services et ressources.

Dans ce tutoriel, vous suivez les étapes nécessaires pour qu’une application web Azure puisse lire les informations depuis Azure Key Vault à l’aide d’identités managées pour les ressources Azure. Ce didacticiel est basé sur [Azure Web Apps](../app-service/app-service-web-overview.md). Vous apprendrez à :

> [!div class="checklist"]
> * Création d’un coffre de clés
> * Stockage d’un secret dans le coffre de clés.
> * Récupération d’un secret à partir du coffre de clés.
> * Créer une machine virtuelle Azure.
> * Activer une [identité managée](../active-directory/managed-identities-azure-resources/overview.md) pour la machine virtuelle.
> * Octroyer les autorisations nécessaires à l’application console pour lire les données provenant du coffre de clés.
> * Récupérer des secrets à partir de Key Vault

Avant d’aller plus loin, veuillez lire les [concepts de base](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Prérequis
* Toutes les plateformes :
  * Git ([télécharger](https://git-scm.com/downloads)).
  * Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) version 2.0.4 ou ultérieure. Disponible pour Windows, Mac et Linux.

Ce tutoriel utilise Managed Service Identity

## <a name="what-is-managed-service-identity-and-how-does-it-work"></a>Qu’est-ce que le service Managed Service Identity et comment fonctionne-t-il ?
Avant d’avancer, il faut comprendre ce qu’est MSI. Azure Key Vault peut stocker des informations d'identification de façon sécurisée afin qu’elles ne se retrouvent pas dans votre code, mais pour les récupérer vous devez vous authentifier sur Azure Key Vault. Pour vous authentifier sur Key Vault, il vous faut des informations d’identification. Problème de démarrage classique. Grâce à la magie d’Azure et Azure AD, MSI offre une « identité de démarrage » qui simplifie grandement le démarrage.

Fonctionnement : Lorsque vous activez MSI pour un service Azure tel que des machines virtuelles, App Service ou Functions, Azure crée un [principal de service](key-vault-whatis.md#basic-concepts) pour l’instance du service dans Azure Active Directory, et injecte les informations d'identification du principal de service dans l’instance du service. 

![MSI](media/MSI.png)

Ensuite, votre code appelle un service de métadonnée local disponible sur les ressources Azure pour obtenir un jeton d'accès.
Il utilise alors le jeton obtenu du point de terminaison local MSI_ENDPOINT pour vous authentifier sur un service Azure Key Vault. 

## <a name="log-in-to-azure"></a>Connexion à Azure

Pour vous connecter à Azure à l’aide de l’interface CLI, entrez :

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Sélectionnez un nom de groupe de ressources et renseignez l’espace réservé.
L’exemple suivant crée un groupe de ressources dans l’emplacement USA Ouest :

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Le groupe de ressources que vous venez de créer est utilisé tout au long de cet article.

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

Ensuite, vous créez un coffre de clés dans le groupe de ressources que vous avez créé à l’étape précédente. Fournissez les informations suivantes :

* Nom de coffre de clés : le nom doit être une chaîne de 3 à 24 caractères et doit contenir uniquement (0-9, a-z, A-Z et -).
* Nom du groupe de ressources.
* Emplacement : **USA Ouest**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.

## <a name="add-a-secret-to-the-key-vault"></a>Ajouter un secret au coffre de clés

Nous allons ajouter un secret pour montrer comment cela fonctionne. Vous pouvez stocker une chaîne de connexion SQL ou toute autre information que vous devez conserver en toute sécurité mais que vous devez rendre disponible à votre application.

Saisissez les commandes suivantes pour créer un secret dans le coffre de clés appelé **AppSecret**. Ce secret stockera la valeur **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create).

L’exemple suivant crée une machine virtuelle nommée *myVM* et ajoute un compte d’utilisateur nommé *azureuser*. Le paramètre `--generate-ssh-keys` permet de générer automatiquement une clé SSH et de la placer dans l’emplacement de clé par défaut (*~/.ssh*). Pour utiliser un ensemble spécifique de clés à la place, utilisez l’option `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

La création de la machine virtuelle et des ressources de support ne nécessite que quelques minutes. L’exemple de sortie suivant illustre la réussite de l’opération de création d’une machine virtuelle.

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

Notez votre propre `publicIpAddress` dans la sortie à partir de votre machine virtuelle. Cette adresse permet d’accéder à la machine virtuelle lors des étapes suivantes.

## <a name="assign-identity-to-virtual-machine"></a>Affecter l’identité à la machine virtuelle
Dans cette étape, nous créons une identité affectée par le système pour la machine virtuelle en exécutant la commande suivante dans Azure CLI.

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Veuillez noter la valeur de systemAssignedIdentity indiquée ci-dessous. La sortie de la commande ci-dessus devrait être : 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-virtual-machine-identity-permission-to-key-vault"></a>Donner l’autorisation d’identité de la machine virtuelle à Key Vault
À présent, nous pouvons donner à Key Vault l’autorisation d’identité créée ci-dessus en exécutant la commande suivante :

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="login-to-the-virtual-machine"></a>Se connecter à la machine virtuelle

Vous pouvez suivre ce [tutoriel](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="create-and-run-sample-python-app"></a>Créer et exécuter l’exemple d’application Python

Le fichier ci-dessous est un exemple de fichier nommé « Sample.py ». Il utilise la bibliothèque [requests](http://docs.python-requests.org/master/) pour effectuer des appels HTTP GET.

## <a name="edit-samplepy"></a>Modifier Sample.py
Après avoir créé Sample.py, ouvrez le fichier puis copiez-y le code ci-dessous.

Le processus ci-dessous comprend deux étapes. 
1. Récupérez (fetch) un jeton du point de terminaison MSI local sur la machine virtuelle qui, à son tour, extrait un jeton à partir d’Azure Active Directory.
2. Passez le jeton à Key Vault et récupérez votre secret. 

```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from a Managed Identity enabled azure resource      
    # Note that the resource here is https://vault.azure.net for public cloud and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # extracting data in json format 
    # This request gets a access_token from Azure Active Directory using the local MSI endpoint
    data = r.json() 
    
    # Step 2: Pass the access_token received from previous HTTP GET call to Key Vault
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

Après exécution, vous devriez voir la valeur du secret 

```
python Sample.py
```

Le code ci-dessus montre comment effectuer des opérations avec Azure Key Vault dans une machine virtuelle Windows Azure. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [API REST Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
