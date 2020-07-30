---
title: Démarrage rapide - Bibliothèque de client Azure Key Vault pour .NET (SDK v3)
description: Apprenez à créer, récupérer et supprimer des secrets d'un coffre de clés Azure à l'aide de la bibliothèque de client .NET (v3)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 2e5f3357baa774c9690e079a82b11d2c651ebe9b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87078877"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Démarrage rapide : Bibliothèque de client Azure Key Vault pour .NET (SDK v3)

Bien démarrer avec la bibliothèque de client Azure Key Vault pour .NET. Suivez les étapes ci-dessous pour installer le package et tester un exemple de code relatif à des tâches de base.

> [!NOTE]
> Ce guide de démarrage rapide utilise la version v3.0.4 de la bibliothèque de client Microsoft.Azure.KeyVault. Pour utiliser la version la plus récente de la bibliothèque de client Key Vault, consultez [Bibliothèque de client Azure Key Vault pour .NET (SDK v4)](quick-create-net.md). 

Azure Key Vault permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. Utilisez la bibliothèque de client Key Vault pour .NET pour :

- Accroître la sécurité et le contrôle des clés et des mots de passe
- Créer et importer des clés de chiffrement en quelques minutes
- Réduire la latence avec la mise à l’échelle du cloud et la redondance globale
- Simplifier et automatiser les tâches associées aux certificats TLS/SSL
- Utiliser des HSM (modules de sécurité matériels) validés conformes à la norme FIPS 140-2 de niveau 2

[Documentation de référence sur l’API](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Chaque coffre de clés doit avoir un nom unique. Remplacez <your-unique-keyvault-name> par le nom de votre coffre de clés dans les exemples suivants.


## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Kit SDK .NET Core 3.1 ou version ultérieure](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/)

Ce guide de démarrage rapide suppose que vous exécutez les commandes `dotnet`, [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) et Windows dans un terminal Windows (par exemple [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6) ou [Azure Cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Configuration

### <a name="create-new-net-console-app"></a>Créer une application console .NET

Dans une fenêtre de console, utilisez la commande `dotnet new` pour créer une application console .NET nommée `akv-dotnet`.


```console
dotnet new console -n akvdotnet
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
dotnet add package Microsoft.Azure.KeyVault
```

Pour ce guide de démarrage rapide, vous devez également installer les packages suivants :

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Créer un groupe de ressources et un coffre de clés

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Créer un principal du service

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Accorder au principal de service l’accès à votre coffre de clés

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

## <a name="object-model"></a>Modèle objet

La bibliothèque de client Azure Key Vault pour .NET vous permet de gérer les clés et les ressources associées, par exemple les certificats et les secrets. Les exemples de code ci-dessous vous montrent comment définir et récupérer un secret.

L’application console complète est disponible à l’adresse https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet.

## <a name="code-examples"></a>Exemples de code

### <a name="add-directives"></a>Ajouter des directives

Ajoutez les directives suivantes en haut de votre code :

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>S’authentifier auprès de votre coffre de clés

Ce guide de démarrage rapide .NET s’appuie sur des variables d’environnement pour stocker les informations d’identification qui ne doivent pas être placées dans le code. 

Avant de générer et d’exécuter votre application, utilisez la commande `setx` pour affecter les valeurs notées ci-dessus aux variables d’environnement `akvClientId`, `akvClientSecret`, `akvTenantId` et `akvSubscriptionId`.

**Windows**

```console
setx akvClientId "<your-clientID>"
setx akvClientSecret "<your-clientSecret>"
```

**Linux**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

**MacOS**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

Affectez ces variables d’environnement aux chaînes de votre code, puis authentifiez votre application en les passant à la classe [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) :

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Enregistrer un secret

Une fois votre application authentifiée, vous pouvez placer un secret dans votre coffre de clés à l’aide de la [méthode SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync). Cela nécessite l’URL de votre coffre de clés, qui se présente sous la forme `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`. Un nom est également nécessaire pour le secret. Nous utilisons « mySecret ». 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Vous pouvez vérifier que le secret a été défini à l’aide de la commande [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Récupérer un secret

Vous pouvez désormais récupérer la valeur définie avec la [méthode GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Votre secret est désormais enregistré en tant que `keyvaultSecret.Value;`.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois que vous n’en avez plus besoin, vous pouvez supprimer votre coffre de clés et le groupe de ressources correspondant à l’aide d’Azure CLI ou d’Azure PowerShell.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un coffre de clés, enregistré un secret et récupéré ce secret. Accédez à l’[application console complète dans GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Pour en savoir plus sur Key Vault et sur la manière de l’intégrer à vos applications, consultez les articles ci-dessous.

- Implémenter l’[authentification de service à service auprès d’Azure Key Vault à l’aide de .NET](../general/service-to-service-authentication.md)
- Lire la [vue d’ensemble Azure Key Vault](../general/overview.md)
- Consulter le [Guide du développeur Azure Key Vault](../general/developers-guide.md)
- Passer en revue les [bonnes pratiques relatives à Azure Key Vault](../general/best-practices.md)
