---
title: Tutoriel - Utiliser Azure Key Vault avec une machine virtuelle Linux Azure dans .NET - Azure Key Vault | Microsoft Docs
description: 'Didacticiel : configurer une application ASP.NET Core pour lire un secret dans le coffre de clés'
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 88d06518c6cabe1f796dbdef6d954db83bc5ae28
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65234007"
---
# <a name="tutorial-how-to-use-azure-key-vault-with-azure-linux-virtual-machine-in-net"></a>Didacticiel : Utiliser Azure Key Vault avec une machine virtuelle Linux Azure dans .NET

Azure Key Vault vous permet de protéger des secrets tels que les clés API et les chaînes de connexion de base de données nécessaires pour accéder à vos applications, services et ressources.

Dans ce tutoriel, vous allez suivre les étapes nécessaires pour qu’une application console puisse lire des informations dans Azure Key Vault à l’aide d’identités managées pour les ressources Azure. Vous apprendrez à :

> [!div class="checklist"]
> * Création d’un coffre de clés
> * Stockage d’un secret dans le coffre de clés.
> * Récupération d’un secret à partir du coffre de clés.
> * Créer une machine virtuelle Azure.
> * Activer une [identité managée](../active-directory/managed-identities-azure-resources/overview.md) pour la machine virtuelle.
> * Octroyer les autorisations nécessaires à l’application console pour lire les données provenant du coffre de clés.
> * Récupérer des secrets à partir de Key Vault

Avant d’aller plus loin, lisez les [concepts de base](key-vault-whatis.md#basic-concepts).

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

## <a name="sign-in-to-azure"></a>Connexion à Azure

Pour vous connecter à Azure à l’aide de l’interface CLI Azure, entrez ceci :

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

* Nom du coffre de clés : le nom doit être une chaîne de 3 à 24 caractères et doit contenir uniquement (0-9, a-z, A-Z et -).
* Nom du groupe de ressources.
* Localisation : **USA Ouest**.

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

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm).

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

Dans cette étape, nous allons créer une identité affectée par le système pour la machine virtuelle en exécutant la commande suivante :

```
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Notez la valeur de systemAssignedIdentity indiquée ci-dessous. La sortie de la commande ci-dessus devrait être : 

```
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="give-vm-identity-permission-to-key-vault"></a>Donner l’autorisation d’identité de la machine virtuelle à Key Vault

À présent, nous pouvons donner à Key Vault l’autorisation d’identité créée ci-dessus en exécutant la commande suivante :

```
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="sign-in-to-the-virtual-machine"></a>Se connecter à la machine virtuelle

Connectez-vous maintenant à la machine virtuelle avec un terminal.

```
ssh azureuser@<PublicIpAddress>
```

## <a name="install-dot-net-core-on-linux"></a>Installer Dot Net core sur Linux

### <a name="register-the-microsoft-product-key-as-trusted-run-the-following-two-commands"></a>Enregistrez la clé de produit Microsoft comme digne de confiance. Exécutez les deux commandes suivantes :

```
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
```

### <a name="set-up-desired-version-host-package-feed-based-on-operating-system"></a>Configurez le flux du package d’hôte de la version souhaitée en fonction du système d’exploitation :

```
 
# Ubuntu 16.04 / Linux Mint 18
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-get update
 
# Ubuntu 14.04 / Linux Mint 17
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-get update
```

### <a name="install-net-core"></a>Installez .NET Core

Et vérifiez la version de .NET

```
sudo apt-get install dotnet-sdk-2.1.4
dotnet --version
```

## <a name="create-and-run-sample-dot-net-app"></a>Créer et exécuter l’exemple d’application Dot Net (.Net)

En exécutant les commandes ci-dessous, vous devriez voir le texte « Hello World » affiché dans la console.

```
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-console-app"></a>Modifier l’application console

Ouvrez le fichier Program.cs et ajoutez ces packages :
```
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Ensuite, modifiez le fichier de classe pour contenir le code ci-dessous. Ce processus comprend deux étapes.

1. Récupérez (fetch) un jeton du point de terminaison MSI local sur la machine virtuelle qui, à son tour, extrait un jeton à partir d’Azure Active Directory.
2. Passez le jeton à Key Vault et récupérez votre secret. 

```
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
            var token = GetToken();

            // Step 2: Fetch the secret value from Key Vault
            System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
        }

        static string GetToken()
        {
            WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
            request.Headers.Add("Metadata", "true");
            WebResponse response = request.GetResponse();
            return ParseWebResponse(response, "access_token");
        }

        static string FetchSecretValueFromKeyVault(string token)
        {
            WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
            kvRequest.Headers.Add("Authorization", "Bearer "+  token);
            WebResponse kvResponse = kvRequest.GetResponse();
            return ParseWebResponse(kvResponse, "value");
        }

        private static string ParseWebResponse(WebResponse response, string tokenName)
        {
            string token = String.Empty;
            using (Stream stream = response.GetResponseStream())
            {
                StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                String responseString = reader.ReadToEnd();

                JObject joResponse = JObject.Parse(responseString);    
                JValue ojObject = (JValue)joResponse[tokenName];   
                token = ojObject.Value.ToString();
            }
            return token;
        }
    }
```

Le code ci-dessus montre comment effectuer des opérations avec Azure Key Vault dans une machine virtuelle Linux Azure. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [API REST Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
