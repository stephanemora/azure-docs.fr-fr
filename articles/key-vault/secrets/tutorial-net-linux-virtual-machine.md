---
title: Tutoriel - Utiliser une machine virtuelle Linux et une application console ASP.NET pour stocker des secrets dans Azure Key Vault | Microsoft Docs
description: Dans ce tutoriel, vous allez découvrir comment configurer une application ASP.NET Core pour qu’elle puisse lire un secret dans Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 50810dd1fbf2d97989df47b537ef5c574be059d2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81426168"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Tutoriel : Utiliser une machine virtuelle Linux et une application .NET pour stocker des secrets dans Azure Key Vault

Azure Key Vault vous permet de protéger des secrets tels que les clés API et les chaînes de connexion de base de données qui sont nécessaires pour accéder à vos applications, services et ressources informatiques.

Dans ce tutoriel, vous allez configurer une application console .NET pour lire des informations dans Azure Key Vault à l’aide d’identités managées pour les ressources Azure. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Création d’un coffre de clés
> * Stocker un secret dans Key Vault
> * Créer une machine virtuelle Azure Linux
> * Activer une [identité managée](../../active-directory/managed-identities-azure-resources/overview.md) pour la machine virtuelle
> * Octroyer les autorisations nécessaires à l’application console pour lire les données provenant de Key Vault
> * Récupérer un secret à partir de Key Vault

Avant d’aller plus loin, lisez les [concepts de base relatifs à Key Vault](../general/basic-concepts.md).

## <a name="prerequisites"></a>Prérequis

* [Git](https://git-scm.com/downloads).
* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* [Azure CLI 2.0 ou ultérieur](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) ou Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Comprendre Managed Service Identity

Azure Key Vault peut stocker des informations d’identification de façon sécurisée afin de ne pas les avoir dans votre code, mais pour les récupérer, vous devez vous authentifier auprès d’Azure Key Vault. Toutefois, pour vous authentifier auprès de Key Vault, vous avez besoin d’informations d’identification. Il s’agit d’un problème de démarrage classique. Avec Azure et Azure Active Directory (Azure AD), Managed Service Identity (MSI) peut fournir une identité de démarrage qui simplifie grandement le démarrage.

Quand vous activez MSI pour un service Azure comme Machines virtuelles, App Service ou Functions, Azure crée un principal de service pour l’instance du service dans Azure Active Directory. Il injecte les informations d’identification du principal de service dans l’instance du service.

![MSI](../media/MSI.png)

Ensuite, votre code appelle un service de métadonnée local disponible dans la ressource Azure pour obtenir un jeton d’accès.
Il utilise alors le jeton obtenu du point de terminaison local MSI_ENDPOINT pour vous authentifier sur un service Azure Key Vault.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Pour vous connecter à Azure à l’aide de l’interface CLI Azure, entrez ceci :

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande `az group create`. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources dans la région USA Ouest. Choisissez un nom pour votre groupe de ressources et remplacez `YourResourceGroupName` dans l’exemple suivant :

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Vous allez utiliser ce groupe de ressources tout au long du tutoriel.

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

Ensuite, créez un coffre de clés dans le groupe de ressources. Fournissez les informations suivantes :

* Nom du coffre de clés : chaîne de 3 à 24 caractères qui ne peut contenir que des chiffres, des lettres et des traits d’union ( 0-9, a-z, A-Z et \-).
* Nom de groupe ressources
* Localisation : **USA Ouest**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.

## <a name="add-a-secret-to-the-key-vault"></a>Ajouter un secret au coffre de clés

Vous pouvez maintenant ajouter un secret. Dans un scénario réel, vous pouvez stocker une chaîne de connexion SQL ou toute autre information que vous devez conserver de façon sécurisée, mais que vous devez rendre disponible à votre application.

Pour ce tutoriel, tapez les commandes suivantes pour créer un secret dans le coffre de clés. Le secret s’appelle **AppSecret** et sa valeur est **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Créer une machine virtuelle Linux

Créez une machine virtuelle à l’aide de la commande `az vm create`.

L’exemple suivant crée une machine virtuelle nommée **myVM** et ajoute un compte d’utilisateur nommé **azureuser**. Le paramètre `--generate-ssh-keys` permet de générer automatiquement une clé SSH et de la placer dans l’emplacement de clé par défaut ( **~/.ssh**). Pour utiliser un ensemble spécifique de clés à la place, utilisez l’option `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

La création de la machine virtuelle et des ressources de support ne nécessite que quelques minutes. L’exemple de sortie suivant illustre la réussite de l’opération de création d’une machine virtuelle.

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

Notez votre `publicIpAddress` dans la sortie à partir de votre machine virtuelle. Vous utiliserez cette adresse pour accéder à la machine virtuelle dans des étapes ultérieures.

## <a name="assign-an-identity-to-the-vm"></a>Affecter une identité à la machine virtuelle

Créez une identité affectée par le système pour la machine virtuelle en exécutant la commande suivante :

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

La commande doit retourner ce qui suit :

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Notez la valeur de `systemAssignedIdentity`. Vous l’utiliserez à l’étape suivante.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Donner l’autorisation d’identité de la machine virtuelle au coffre de clés

Maintenant, vous pouvez donner l’autorisation de coffre de clés à l’identité que vous avez créée. Exécutez la commande suivante :

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Se connecter à la machine virtuelle

Connectez-vous à la machine virtuelle à l’aide d’un terminal.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>Installer .NET Core sur Linux

Sur une machine virtuelle Linux :

Inscrivez la clé de produit Microsoft approuvée en exécutant les commandes suivantes :

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

Configurez le flux du package d’hôte de la version souhaitée en fonction du système d’exploitation :

```console
   # Ubuntu 17.10
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-artful-prod artful main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 17.04
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-zesty-prod zesty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 16.04 / Linux Mint 18
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 14.04 / Linux Mint 17
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
```

Installez .NET et vérifiez la version :

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>Créer et exécuter l’exemple d’application .NET

Exécutez les commandes suivantes : Le texte « Hello World » doit s’afficher dans la console.

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>Modifier l’application console pour extraire votre secret

Ouvrez le fichier Program.cs et ajoutez ces packages :

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

Il s’agit d’un processus en deux étapes permettant de changer le fichier de classe en vue de permettre à l’application d’accéder au secret du coffre de clés.

1. Récupérez (fetch) un jeton du point de terminaison MSI local sur la machine virtuelle qui, à son tour, extrait un jeton d’Azure Active Directory.
1. Passez le jeton à Key Vault et récupérez votre secret.

   Modifiez le fichier de classe pour qu’il contienne le code suivant :

   ```csharp
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

Vous venez de voir comment effectuer des opérations avec Azure Key Vault dans une application .NET qui s’exécute sur une machine virtuelle Azure Linux.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Supprimez le groupe de ressources, la machine virtuelle et toutes les ressources associées quand vous n’en avez plus besoin. Pour ce faire, sélectionnez le groupe de ressources de la machine virtuelle et sélectionnez **Supprimer**.

Supprimez le coffre de clés à l’aide de la commande `az keyvault delete` :

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [API REST Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
