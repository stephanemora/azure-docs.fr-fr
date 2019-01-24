---
title: 'Tutoriel : utiliser Azure Key Vault avec une machine virtuelle Azure dans Python | Microsoft Docs'
description: Dans ce tutoriel, vous configurez une application Python pour qu’elle lise un secret dans un coffre de clés.
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
ms.openlocfilehash: 1e364003093d5e37a75830386cafe855b0bdcad2
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467399"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-virtual-machine-in-python"></a>Didacticiel : utiliser Azure Key Vault avec une machine virtuelle Azure dans Python

Azure Key Vault vous permet de protéger des secrets tels que les clés API et les chaînes de connexion de base de données nécessaires pour accéder à vos applications, services et ressources informatiques.

Ce tutoriel décrit les étapes que vous devez suivre pour qu’une application web Azure puisse lire des informations dans Azure Key Vault à l’aide d’identités managées pour les ressources Azure. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Création d’un coffre de clés
> * Stockage d’un secret dans le coffre de clés.
> * Créez une machine virtuelle Azure.
> * Activer une [identité managée](../active-directory/managed-identities-azure-resources/overview.md) pour la machine virtuelle.
> * Octroyer les autorisations nécessaires à l’application console pour lire les données provenant du coffre de clés.
> * Récupération d’un secret à partir du coffre de clés.

Avant d’aller plus loin, lisez les [concepts de base sur Key Vault](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Prérequis
Pour toutes les plateformes, vous avez besoin des éléments suivants :

* Git ([télécharger](https://git-scm.com/downloads)).
* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) version 2.0.4 ou ultérieure. Disponible pour Windows, Mac et Linux.

### <a name="managed-service-identity-and-how-it-works"></a>Fonctionnement de Managed Service Identity
Ce tutoriel utilise Managed Service Identity (MSI).

Azure Key Vault peut stocker les informations d’identification de manière sécurisée, en dehors de votre code. Pour les récupérer, vous devez vous authentifier auprès de Key Vault. Pour vous authentifier auprès de Key Vault, vous avez besoin d’informations d’identification. Il s’agit d’un problème de démarrage classique. Avec Azure et Azure Active Directory (Azure AD), MSI offre une « identité de démarrage » qui simplifie le démarrage.

Quand vous activez MSI pour un service Azure comme Machines virtuelles, App Service ou Functions, Azure crée un [principal de service](key-vault-whatis.md#basic-concepts) pour l’instance du service dans Azure AD. Azure injecte les informations d’identification du principal de service dans l’instance du service. 

![MSI](media/MSI.png)

Ensuite, votre code appelle un service de métadonnée local disponible dans la ressource Azure pour obtenir un jeton d’accès.
Votre code utilise alors le jeton qu’il obtient du point de terminaison local MSI_ENDPOINT pour s’authentifier auprès d’un service Azure Key Vault. 

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

* Nom du coffre de clés : le nom doit être une chaîne de 3 à 24 caractères et contenir uniquement les caractères 0-9, a-z, A-Z et des traits d’union (-).
* Nom du groupe de ressources.
* Localisation : **USA Ouest**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.

## <a name="add-a-secret-to-the-key-vault"></a>Ajouter un secret au coffre de clés

Nous allons ajouter un secret pour montrer comment cela fonctionne. Vous pouvez stocker une chaîne de connexion SQL ou toute autre information que vous devez conserver en toute sécurité mais que vous devez rendre disponible à votre application.

Saisissez les commandes suivantes pour créer un secret dans le coffre de clés appelé *AppSecret*. Ce secret stockera la valeur **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm).

L’exemple suivant crée une machine virtuelle nommée *myVM* et ajoute un compte d’utilisateur nommé *azureuser*. Le paramètre `--generate-ssh-keys` génère automatiquement une clé SSH qu’il place dans l’emplacement de clé par défaut (*~/.ssh*). Pour utiliser un ensemble spécifique de clés à la place, utilisez l’option `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

La création de la machine virtuelle et des ressources de support ne nécessite que quelques minutes. L’exemple de sortie suivant illustre la création réussie d’une machine virtuelle :

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

Notez votre propre valeur `publicIpAddress` dans la sortie à partir de votre machine virtuelle. Cette adresse permet d’accéder à la machine virtuelle lors des étapes suivantes.

## <a name="assign-an-identity-to-the-virtual-machine"></a>Affecter une identité à la machine virtuelle
Dans cette étape, nous créons une identité affectée par le système pour la machine virtuelle. Dans Azure CLI, exécutez la commande suivante :

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

La sortie de la commande est la suivante. Notez la valeur de **systemAssignedIdentity**. 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-the-virtual-machine-identity-permission-to-the-key-vault"></a>Donner l’autorisation d’identité de la machine virtuelle au coffre de clés.
Maintenant, nous pouvons donner l’autorisation d’identité au coffre de clés. Exécutez la commande suivante :

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-virtual-machine"></a>Connectez-vous à la machine virtuelle.

Connectez-vous à la machine virtuelle en suivant [ce tutoriel](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

## <a name="create-and-run-the-sample-python-app"></a>Créer et exécuter l’exemple d’application Python

L’exemple de fichier suivant est nommé *Sample.py*. Il utilise la bibliothèque [requests](https://pypi.org/project/requests/2.7.0/) pour effectuer des appels HTTP GET.

## <a name="edit-samplepy"></a>Modifier Sample.py
Après avoir créé Sample.py, ouvrez le fichier et copiez le code suivant. Le code comprend deux étapes : 
1. Récupérer un jeton du point de terminaison MSI local sur la machine virtuelle. Le point de terminaison récupère alors un jeton d’Azure Active Directory.
2. Passer le jeton au coffre de clés et récupérer votre secret. 

```
    # importing the requests library 
    import requests 

    # Step 1: Fetch an access token from an MSI-enabled Azure resource      
    # Note that the resource here is https://vault.azure.net for the public cloud, and api-version is 2018-02-01
    MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
    r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"}) 
      
    # Extracting data in JSON format 
    # This request gets an access token from Azure Active Directory by using the local MSI endpoint
    data = r.json() 
    
    # Step 2: Pass the access token received from the previous HTTP GET call to the key vault
    KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
    kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})
    
    print(kvSecret.json()["value"])
```

En exécutant la commande suivante, vous devez voir la valeur du secret : 

```
python Sample.py
```

Le code précédent montre comment effectuer des opérations avec Azure Key Vault dans une machine virtuelle Windows. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [API REST Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
