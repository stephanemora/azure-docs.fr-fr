---
title: 'Tutoriel : Utiliser Azure Key Vault avec une machine virtuelle Windows dans .NET | Microsoft Docs'
description: Dans ce tutoriel, vous allez configurer une application ASP.NET Core pour lire un secret dans votre coffre de clés.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 6ba78a44af7beb9b5b79aa1a87e08f5a82589cce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81425758"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Tutoriel : Utiliser Azure Key Vault avec une machine virtuelle Windows dans .NET

Azure Key Vault vous permet de protéger des secrets tels que les clés API et les chaînes de connexion de base de données nécessaires pour accéder à vos applications, services et ressources.

Dans ce tutoriel, vous allez vous procurer une application console pour lire des informations d’Azure Key Vault. Pour ce faire, vous allez utiliser des identités managées pour des ressources Azure. 

Ce didacticiel explique les procédures suivantes :

> [!div class="checklist"]
> * Créez un groupe de ressources.
> * Création d’un coffre de clés
> * Ajoutez un secret au coffre de clés.
> * Récupération d’un secret à partir du coffre de clés.
> * Créez une machine virtuelle Azure.
> * Activer une [identité managée](../../active-directory/managed-identities-azure-resources/overview.md) pour la machine virtuelle.
> * Attribuez des autorisations à l’identité de machine virtuelle.

Avant de continuer, lisez les [concepts de base de Key Vault](basic-concepts.md). 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Prérequis

Pour Windows, Mac et Linux :
  * [Git](https://git-scm.com/downloads)
  * Ce didacticiel nécessite que vous exécutiez l’interface Azure CLI localement. Vous devez avoir installé Azure CLI 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau l’interface CLI, consultez l’article [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="about-managed-service-identity"></a>À propos de Managed Service Identity (MSI)

Azure Key Vault stocke les informations d’identification de manière sécurisée, de façon à ce qu’elles n’apparaissent pas dans votre code. Toutefois, vous devez vous authentifier auprès d’Azure Key Vault pour récupérer vos clés. Pour vous authentifier auprès de Key Vault, vous avez besoin d’informations d’identification. Il s’agit d’un dilemme de démarrage classique. Managed Service Identity (MSI) résout ce problème en fournissant une _identité de démarrage_.

Lorsque vous activez MSI pour un service Azure, par exemple, Machines virtuelles Azure, Azure App Service ou Azure Functions, Azure crée un [principal de service](basic-concepts.md). MSI procède ainsi pour l’instance du service dans Azure Active Directory (Azure AD) et injecte les informations d’identification du principal de service dans cette instance. 

![MSI](../media/MSI.png)

Ensuite, pour obtenir un jeton d’accès, votre code appelle un service de métadonnées local disponible dans la ressource Azure. Pour s’authentifier auprès d’un service Azure Key Vault, votre code utilise alors le jeton qu’il obtient du point de terminaison local MSI_ENDPOINT. 

## <a name="create-resources-and-assign-permissions"></a>Créer des ressources et affecter des autorisations

Avant de commencer à coder, vous devez créer des ressources, placer une clé secrète dans votre coffre de clés et affecter des autorisations.

### <a name="sign-in-to-azure"></a>Connexion à Azure

Pour vous connecter à Azure à l’aide de l’interface CLI Azure, entrez ceci :

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). 

Cet exemple crée un groupe de ressources dans l’emplacement USA Ouest :

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Vous allez utiliser ce nouveau groupe de ressources tout au long du tutoriel.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Créer un coffre de clés et le remplir avec une clé secrète

Créez un coffre de clés dans votre groupe de ressources en fournissant à la commande [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) avec les informations suivantes :

* Nom du coffre de clés : chaîne de 3 à 24 caractères qui ne peut contenir que des chiffres (0-9), des lettres (a-z, A-Z) et des traits d’union (-).
* Nom de groupe ressources
* Localisation : **USA Ouest**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.

Maintenant, ajoutez une clé secrète à votre coffre de clés à l’aide de la commande [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)


Pour créer un secret dans le coffre de clés appelé **AppSecret**, tapez la commande suivante :

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Ce secret stocke la valeur **MySecret**.

### <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle
Créez une machine virtuelle à l’aide de l’une des méthodes suivantes :

* [L’interface de ligne de commande Microsoft Azure](../../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Le portail Azure](../../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>Affecter une identité à la machine virtuelle
Créez une identité attribuée par le système pour la machine virtuelle avec la commande [az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) :

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

### <a name="assign-permissions-to-the-vm-identity"></a>Attribuer des autorisations à l’identité de machine virtuelle
Attribuez les autorisations d’identité créées précédemment pour votre clé de coffres avec la commande [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) :

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Se connecter à la machine virtuelle

Pour vous connecter à la machine virtuelle, suivez les instructions de l’article [Se connecter à une machine virtuelle Azure exécutant Windows](../../virtual-machines/windows/connect-logon.md).

## <a name="set-up-the-console-app"></a>Modifier l’application console

Créez une application de console et installez les packages requis à l’aide de la commande `dotnet`.

### <a name="install-net-core"></a>Installez .NET Core

Pour installer .NET Core, accédez à la page de [téléchargements .NET](https://www.microsoft.com/net/download).

### <a name="create-and-run-a-sample-net-app"></a>Créer et exécuter l’exemple d’application .NET

Ouvrez une invite de commandes.

Vous pouvez envoyer un message « Hello World » à la console en exécutant les commandes suivantes :

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

### <a name="install-the-packages"></a>Installer les packages

À partir de la fenêtre de console, installez les packages .NET requis pour ce guide de démarrage rapide :

```console
dotnet add package System.IO;
dotnet add package System.Net;
dotnet add package System.Text;
dotnet add package Newtonsoft.Json;
dotnet add package Newtonsoft.Json.Linq;
```

## <a name="edit-the-console-app"></a>Modifier l’application console

Ouvrez le fichier *Program.cs* et ajoutez ces packages :

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Modifiez le fichier de classe pour qu’il contienne le code dans le processus suivant en trois étapes :

1. Récupérer un jeton du point de terminaison MSI local sur la machine virtuelle. Cela extrait également un jeton d’Azure AD.
2. Passez le jeton au coffre de clés et récupérez votre secret. 
3. Ajoutez le nom du coffre et le nom du secret à la demande.

```csharp
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from the local (URI) Managed Service Identity endpoint, which in turn fetches it from Azure AD
            var token = GetToken();

            // Step 2: Fetch the secret value from your key vault
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
            //Step 3: Add the vault name and secret name to the request.
            WebRequest kvRequest = WebRequest.Create("https://<YourVaultName>.vault.azure.net/secrets/<YourSecretName>?api-version=2016-10-01");
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

Le code précédent montre comment effectuer des opérations avec Azure Key Vault dans une machine virtuelle Windows.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand ils ne sont plus nécessaires, supprimez la machine virtuelle et le coffre de clés.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [API REST Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
