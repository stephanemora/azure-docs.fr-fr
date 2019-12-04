---
title: Démarrage rapide - Bibliothèque de client Azure Key Vault pour .NET (v4)
description: Découvrez comment créer, récupérer et supprimer des secrets d’un coffre de clés Azure à l’aide de la bibliothèque cliente .NET (v4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 05/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: 61e176314c655ef6380a196043fb3159d003cb6e
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273952"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Démarrage rapide : Bibliothèque de client Azure Key Vault pour .NET (SDK v4)

Bien démarrer avec la bibliothèque de client Azure Key Vault pour .NET. Suivez les étapes ci-dessous pour installer le package et tester un exemple de code relatif à des tâches de base.

Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Utilisez la bibliothèque de client Key Vault pour .NET pour :

- Accroître la sécurité et le contrôle des clés et des mots de passe
- Créer et importer des clés de chiffrement en quelques minutes
- Réduire la latence avec la mise à l’échelle du cloud et la redondance globale
- Simplifier et automatiser les tâches associées aux certificats SSL/TLS
- Utiliser des HSM (modules de sécurité matériels) validés conformes à la norme FIPS 140-2 de niveau 2

[Documentation de référence sur l’API](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Package (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Kit de développement logiciel (SDK) [.NET Core 2.1 ou version ultérieure](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/overview)

Ce guide de démarrage rapide suppose que vous exécutez les commandes `dotnet`, [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) et Windows dans un terminal Windows (par exemple [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6) ou [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Configuration

### <a name="create-new-net-console-app"></a>Créer une application console .NET

Dans une fenêtre de console, utilisez la commande `dotnet new` pour créer une application console .NET nommée `akv-dotnet`.

```console
dotnet new console -n key-vault-console-app
```

Déplacez vos répertoires vers le dossier d’application nouvellement créé. Vous pouvez générer l’application avec :

```console
dotnet build
```

La sortie de génération ne doit contenir aucun avertissement ni erreur.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Installer le package

À partir de la fenêtre de la console, installez la bibliothèque de client Azure Key Vault pour .NET :

```console
dotnet add package Azure.Security.KeyVault.Secrets --version 4.0.0
```

Pour ce guide de démarrage rapide, vous devez également installer les packages suivants :

```console
dotnet add package Azure.Identity --version 1.0.0
```

### <a name="create-a-resource-group-and-key-vault"></a>Créer un groupe de ressources et un coffre de clés

Ce guide de démarrage rapide utilise un coffre de clés Azure créé au préalable. Vous pouvez créer un coffre de clés en suivant les étapes décrites dans le [guide de démarrage rapide d’Azure CLI](quick-create-cli.md), le [guide de démarrage rapide d’Azure PowerShell](quick-create-powershell.md) ou le [guide de démarrage rapide du portail Azure](quick-create-portal.md). Sinon, vous pouvez simplement exécuter les commandes Azure CLI ci-dessous.

> [!Important]
> Chaque coffre de clés doit avoir un nom unique. Remplacez <your-unique-keyvault-name> par le nom de votre coffre de clés dans les exemples suivants.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Créer un principal du service

Le moyen le plus simple d’authentifier une application .NET cloud consiste à utiliser une identité managée. Pour plus d’informations, consultez [Utiliser une identité managée App Service pour accéder à Azure Key Vault](managed-identity.md). Cependant, par souci de simplicité, ce guide de démarrage rapide crée une application console .NET. L’authentification d’une application de bureau auprès d’Azure nécessite l’utilisation d’un principal de service et d’une stratégie de contrôle d’accès.

Créez un principal de service à l’aide de la commande Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Cette opération retourne une série de paires clé/valeur. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Notez les valeurs clientId, clientSecret et tenantId, car nous allons les utiliser dans les étapes suivantes.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Accorder au principal de service l’accès à votre coffre de clés

Créez une stratégie d’accès pour votre coffre de clés qui accorde l’autorisation à votre principal de service en passant le clientId à la commande [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy). Accordez au principal de service les autorisations get, list et set sur les clés et les secrets.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Définir des variables d’environnement

La méthode DefaultAzureCredential dans notre application s’appuie sur trois variables d’environnement : `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET` et `AZURE_TENANT_ID`. Définissez ces variables sur les valeurs clientId, clientSecret et tenantId que vous avez notées à l’étape [Créer un principal de service](#create-a-service-principal) ci-dessus.

Vous devrez également enregistrer votre nom de coffre de clés en tant que variable d’environnement appelée `KEY_VAULT_NAME` ;

```console
setx AZURE_CLIENT_ID <your-clientID>

setx AZURE_CLIENT_SECRET <your-clientSecret>

setx AZURE_TENANT_ID <your-tenantId>

setx KEY_VAULT_NAME <your-key-vault-name>
````

Chaque fois que vous appelez `setx`, vous devez obtenir la réponse « RÉUSSITE : La valeur spécifiée a été enregistrée. »

## <a name="object-model"></a>Modèle objet

La bibliothèque de client Azure Key Vault pour .NET vous permet de gérer les clés et les ressources associées, par exemple les certificats et les secrets. Les exemples de code ci-dessous vous montrent comment créer un client et définir, récupérer et supprimer un secret.

L’application console complète est disponible à l’adresse https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app.

## <a name="code-examples"></a>Exemples de code

### <a name="add-directives"></a>Ajouter des directives

Ajoutez les directives suivantes en haut de votre code :

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Authentifier et créer un client

L’authentification auprès de votre coffre de clés et la création d’un client de coffre de clés dépendent des variables d’environnement de l’étape [Définir des variables d’environnement](#set-environmental-variables) ci-dessus. Le nom de votre coffre de clés est étendu à l’URI du coffre de clés, au format « https://\<nom_de_votre_coffre_de_clés\>.vault.azure.net ».

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Enregistrer un secret

Une fois votre application authentifiée, vous pouvez placer un secret dans votre coffre de clés à l’aide de la [client.SetSecret method](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync). Cela nécessite un nom pour le secret ; nous allons utiliser mySecret ici.  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

Vous pouvez vérifier que le secret a été défini à l’aide de la commande [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Récupérer un secret

Vous pouvez désormais récupérer la valeur définie avec la [client.GetSecret method](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

Votre secret est désormais enregistré en tant que `secret.Value`.

### <a name="delete-a-secret"></a>supprimer un secret

Enfin, nous allons supprimer le secret de votre coffre de clés avec [la méthode client.DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync).

[!code-csharp[Delete secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

Vous pouvez vérifier que le secret a été effacé à l’aide de la commande [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Une fois que vous n’en avez plus besoin, vous pouvez supprimer votre coffre de clés et le groupe de ressources correspondant à l’aide d’Azure CLI ou d’Azure PowerShell.

```azurecli
az group delete -g "myResourceGroup" -l "EastUS" 
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Exemple de code

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace key_vault_console_app
{
    class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

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
}
```


## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés, enregistré un secret et récupéré ce secret. Accédez à l’[application console complète dans GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app).

Pour en savoir plus sur Key Vault et sur la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

- Implémenter l’[authentification de service à service auprès d’Azure Key Vault à l’aide de .NET](service-to-service-authentication.md)
- Lire la [vue d’ensemble Azure Key Vault](key-vault-overview.md)
- Consulter le [Guide du développeur Azure Key Vault](key-vault-developers-guide.md)
- Découvrir les [clés, secrets et certificats](about-keys-secrets-and-certificates.md)
- Passer en revue les [bonnes pratiques relatives à Azure Key Vault](key-vault-best-practices.md)
