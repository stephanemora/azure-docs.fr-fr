---
title: Tutoriel - Utiliser Azure Key Vault avec une machine virtuelle dans .NET | Microsoft Docs
description: Dans ce tutoriel, vous allez configurer sur une machine virtuelle une application ASP.NET Core pour lire un secret dans votre coffre de clés.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: mbaldwin
ms.custom: mvc, devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: ba299ff52ccdf811d739efdb1d379889444a02b8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868390"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-net"></a>Tutoriel : Utiliser Azure Key Vault avec une machine virtuelle dans .NET

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
  * [Azure CLI](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="create-resources-and-assign-permissions"></a>Créer des ressources et affecter des autorisations

Avant de commencer à coder, vous devez créer des ressources, placer une clé secrète dans votre coffre de clés et affecter des autorisations.

### <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à Azure en utilisant la commande suivante :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az login
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Connect-AzAccount
```
---

## <a name="create-a-resource-group-and-key-vault"></a>Créer un groupe de ressources et un coffre de clés

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Remplir votre coffre de clés avec un secret

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle
Créez une machine virtuelle Windows ou Linux en utilisant une des méthodes suivantes :

| Windows | Linux |
|--|--|
| [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) | [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) |  
| [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) | [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) |
| [Azure portal](../../virtual-machines/windows/quick-create-portal.md) | [Azure portal](../../virtual-machines/linux/quick-create-portal.md) |

## <a name="assign-an-identity-to-the-vm"></a>Affecter une identité à la machine virtuelle
Créez une identité affectée par le système pour la machine virtuelle à partir de l’exemple suivant :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
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

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$vm = Get-AzVM -Name <NameOfYourVirtualMachine>
Update-AzVM -ResourceGroupName <YourResourceGroupName> -VM $vm -IdentityType SystemAssigned
```

Notez le PrincipalId affiché dans le code suivant. La sortie de la commande ci-dessus doit être la suivante : 


```output
PrincipalId          TenantId             Type             UserAssignedIdentities
-----------          --------             ----             ----------------------
xxxxxxxx-xx-xxxxxx   xxxxxxxx-xxxx-xxxx   SystemAssigned
```
---

## <a name="assign-permissions-to-the-vm-identity"></a>Attribuer des autorisations à l’identité de machine virtuelle
Attribuez les autorisations d’identité créées précédemment pour votre clé de coffres avec la commande [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) :

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault set-policy --name '<your-unique-key-vault-name>' --object-id <VMSystemAssignedIdentity> --secret-permissions  get list set delete
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Set-AzKeyVaultAccessPolicy -ResourceGroupName <YourResourceGroupName> -VaultName '<your-unique-key-vault-name>' -ObjectId '<VMSystemAssignedIdentity>' -PermissionsToSecrets  get,list,set,delete
```
---

## <a name="sign-in-to-the-virtual-machine"></a>Se connecter à la machine virtuelle

Pour vous connecter à la machine virtuelle, suivez les instructions de l’article [Connexion à une machine virtuelle Azure exécutant Windows](../../virtual-machines/windows/connect-logon.md) ou [Se connecter à une machine virtuelle Linux dans Azure](../../virtual-machines/linux/login-using-aad.md).

## <a name="set-up-the-console-app"></a>Modifier l’application console

Créez une application de console et installez les packages requis à l’aide de la commande `dotnet`.

### <a name="install-net-core"></a>Installez .NET Core

Pour installer .NET Core, accédez à la page de [téléchargements .NET](https://dotnet.microsoft.com/download).

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
using Azure.Core;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Ajoutez ces lignes, en mettant à jour l’URI pour refléter la valeur `vaultUri` de votre coffre de clés. Le code ci-dessous utilise ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential) pour l’authentification auprès du coffre de clés, en utilisant le jeton de l’identité managée par l’application pour s’authentifier. Il utilise également l’interruption exponentielle pour les nouvelles tentatives en cas de limitation du coffre de clés.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";
            string keyVaultName = "<your-key-vault-name>";
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

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand ils ne sont plus nécessaires, supprimez la machine virtuelle et le coffre de clés.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [API REST Azure Key Vault](/rest/api/keyvault/)
