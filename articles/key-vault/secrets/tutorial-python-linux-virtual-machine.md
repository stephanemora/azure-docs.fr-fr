---
title: 'Tutoriel : Utiliser une machine virtuelle Linux et une application Python pour stocker des secrets dans Azure Key Vault | Microsoft Docs'
description: Dans ce tutoriel, vous allez découvrir comment configurer une application Python pour qu’elle lise un secret dans Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc, tracking-python
ms.openlocfilehash: 6b3fb07322009134a75621a19cd013e2f967972a
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561616"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Tutoriel : Utiliser une machine virtuelle Linux et une application Python pour stocker des secrets dans Azure Key Vault

Azure Key Vault vous permet de protéger des secrets tels que les clés API et les chaînes de connexion de base de données nécessaires pour accéder à vos applications, services et ressources informatiques.

Dans ce tutoriel, vous allez configurer une application web Azure pour lire des informations dans Azure Key Vault à l’aide d’identités managées pour les ressources Azure. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Création d’un coffre de clés
> * Stocker un secret dans votre coffre de clés
> * Créer une machine virtuelle Linux
> * Activer une [identité managée](../../active-directory/managed-identities-azure-resources/overview.md) pour la machine virtuelle
> * Octroyer les autorisations nécessaires à l’application console pour lire les données provenant du coffre de clés
> * Récupérer un secret dans votre coffre de clés

Avant d’aller plus loin, assurez-vous d’avoir bien compris les [concepts de base sur Key Vault](../general/basic-concepts.md).

## <a name="prerequisites"></a>Prérequis

* [Git](https://git-scm.com/downloads).
* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* [Azure CLI version 2.0.4 ou ultérieure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ou Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Comprendre Managed Service Identity

Azure Key Vault peut stocker les informations d’identification de manière sécurisée, en dehors de votre code. Pour les récupérer, vous devez vous authentifier auprès d’Azure Key Vault. Toutefois, pour vous authentifier auprès de Key Vault, vous avez besoin d’informations d’identification. Il s’agit d’un problème de démarrage classique. Avec Azure et Azure Active Directory (Azure AD), Managed Service Identity (MSI) offre une identité de démarrage qui simplifie le démarrage.

Quand vous activez MSI pour un service Azure comme Machines virtuelles, App Service ou Functions, Azure crée un principal de service pour l’instance du service dans Azure AD. Il injecte les informations d’identification du principal de service dans l’instance du service.

![MSI](../media/MSI.png)

Ensuite, votre code appelle un service de métadonnée local disponible dans la ressource Azure pour obtenir un jeton d’accès. Votre code utilise alors le jeton qu’il obtient du point de terminaison local MSI_ENDPOINT pour s’authentifier auprès d’un service Azure Key Vault.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Pour vous connecter à Azure à l’aide de l’interface CLI Azure, entrez ceci :

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources à l’aide de la commande `az group create` à l’emplacement USA Ouest avec le code suivant. Remplacez `YourResourceGroupName` par un nom de votre choix.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Vous allez utiliser ce groupe de ressources tout au long du tutoriel.

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

Ensuite, vous créez un coffre de clés dans le groupe de ressources que vous avez créé à l’étape précédente. Fournissez les informations suivantes :

* Nom du coffre de clés : le nom doit être une chaîne de 3 à 24 caractères et contenir uniquement les caractères 0-9, a-z, A-Z et des traits d’union (-).
* Nom du groupe de ressources.
* Localisation : **USA Ouest**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.

## <a name="add-a-secret-to-the-key-vault"></a>Ajouter un secret au coffre de clés

Nous allons ajouter un secret pour montrer comment cela fonctionne. Vous souhaiterez peut-être stocker une chaîne de connexion SQL ou toute autre information qui doit être à la fois sécurisée et disponible pour votre application.

Saisissez les commandes suivantes pour créer un secret dans le coffre de clés appelé *AppSecret*. Ce secret stockera la valeur **MySecret**.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Créer une machine virtuelle Linux

Créez une machine virtuelle avec la commande `az vm create`.

L’exemple suivant crée une machine virtuelle nommée **myVM** et ajoute un compte d’utilisateur nommé **azureuser**. Le paramètre `--generate-ssh-keys` génère automatiquement une clé SSH qu’il place dans l’emplacement de clé par défaut ( **~/.ssh**). Pour créer un ensemble spécifique de clés à la place, utilisez l’option `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

La création de la machine virtuelle et des ressources de support ne nécessite que quelques minutes. L’exemple de sortie suivant illustre la création réussie d’une machine virtuelle :

```output
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

Notez votre propre `publicIpAddress` dans la sortie à partir de votre machine virtuelle. Vous utiliserez cette adresse pour accéder à la machine virtuelle dans des étapes ultérieures.

## <a name="assign-an-identity-to-the-vm"></a>Affecter une identité à la machine virtuelle

Créez une identité affectée par le système pour la machine virtuelle en exécutant la commande suivante :

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

La sortie de la commande est la suivante.

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Notez la valeur de `systemAssignedIdentity`. Vous l’utiliserez à l’étape suivante.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Donner l’autorisation d’identité de la machine virtuelle au coffre de clés

Maintenant, vous pouvez donner l’autorisation de coffre de clés à l’identité que vous avez créée. Exécutez la commande suivante :

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Se connecter à la machine virtuelle

Connectez-vous à la machine virtuelle à l’aide d’un terminal.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>Installer la bibliothèque Python sur la machine virtuelle

Téléchargez et installez la bibliothèque Python [requests](https://pypi.org/project/requests/2.7.0/) pour effectuer des appels HTTP GET.

## <a name="create-edit-and-run-the-sample-python-app"></a>Créer, modifier et exécuter l’exemple d’application Python

Créez un fichier Python appelé **Sample.py**.

Ouvrez Sample.py et modifiez-le pour qu’il contienne le code suivant :

```python
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

Le code précédent exécute un processus en deux étapes :

   1. Il récupère un jeton du point de terminaison MSI local sur la machine virtuelle. Le point de terminaison récupère alors un jeton d’Azure Active Directory.
   1. Il passe le jeton au coffre de clés et récupère votre secret.

Exécutez la commande suivante : Vous devriez voir la valeur du secret.

```console
python Sample.py
```

Dans ce tutoriel, vous avez appris à utiliser Azure Key Vault avec une application Python exécutée sur une machine virtuelle Linux.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Supprimez le groupe de ressources, la machine virtuelle et toutes les ressources associées quand vous n’en avez plus besoin. Pour ce faire, sélectionnez le groupe de ressources de la machine virtuelle et sélectionnez **Supprimer**.

Supprimez le coffre de clés à l’aide de la commande `az keyvault delete` :

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [API REST Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
