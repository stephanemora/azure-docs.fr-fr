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
ms.openlocfilehash: 863f3a8c18eab5b42b5f1377ca0f91f9740c06e7
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52265968"
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
Suivez les liens ci-dessous pour créer une machine virtuelle Windows

[Interface de ligne de commande Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-cli) 

[PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell)

[Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

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

```
The below is a 2 step process. 
1. Fetch a token from the local MSI endpoint on the VM which in turn fetches a token from Azure Active Directory
2. Pass the token to Key Vault and fetch your secret 
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

By running you should see the secret value 
```
Sample.py Python
```

The above code shows you how to do operations with Azure Key Vault in an Azure Windows Virtual Machine. 

## Next steps

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
