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
ms.openlocfilehash: 8db1c511ab9defb140720655588b27279a0f08be
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085478"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Tutoriel : Utiliser Azure Key Vault avec une machine virtuelle Windows dans .NET

Azure Key Vault vous permet de protéger des secrets tels que les clés API et les chaînes de connexion de base de données nécessaires pour accéder à vos applications, services et ressources.

Dans ce tutoriel, vous allez vous procurer une application console pour lire des informations d’Azure Key Vault. L’application utilise l’identité managée de la machine virtuelle pour s’authentifier auprès de Key Vault. 

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
  * [Kit SDK .NET Core 3.1 ou version ultérieure](https://dotnet.microsoft.com/download/dotnet-core/3.1)
  * [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

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
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>Installer le package

À partir de la fenêtre de la console, installez la bibliothèque de client Azure Key Vault Secrets pour .NET :

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

Pour ce guide de démarrage rapide, vous devez installer le package d’identité suivant pour vous authentifier auprès d’Azure Key Vault :

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>Modifier l’application console

Ouvrez le fichier *Program.cs* et ajoutez ces packages :

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Ajoutez ces lignes, en mettant à jour l’URI pour refléter la valeur `vaultUri` de votre coffre de clés. Le code ci-dessous utilise ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) pour l’authentification auprès du coffre de clés, en utilisant le jeton de l’identité managée par l’application pour s’authentifier. Il utilise également l’interruption exponentielle pour les nouvelles tentatives en cas de limitation du coffre de clés.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            var kvUri = "https://<your-key-vault-name>.vault.azure.net";
            SecretClientOptions options = new SecretClientOptions()
            {
                Retry =
                {
                    Delay= TimeSpan.FromSeconds(2),
                    MaxDelay = TimeSpan.FromSeconds(16),
                    MaxRetries = 5,
                    Mode = RetryMode.Exponential
                 }
            };

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential(),options);

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
```

Le code précédent montre comment effectuer des opérations avec Azure Key Vault dans une machine virtuelle Windows.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand ils ne sont plus nécessaires, supprimez la machine virtuelle et le coffre de clés.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [API REST Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
