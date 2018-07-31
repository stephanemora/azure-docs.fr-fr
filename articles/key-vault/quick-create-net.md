---
title: Démarrage rapide Azure - Configurer une application web Azure pour lire un secret de coffre de clés (Key Vault) | Microsoft Docs
description: Un guide de démarrage rapide qui vous explique comment configurer une application ASP.NET Core pour lire un secret de coffre de clés (Key Vault)
services: key-vault
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 07a7bc5713f093e34a775aacab27094780ac6c7e
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247823"
---
# <a name="quickstart-set-and-read-a-secret-from-key-vault-in-a-net-web-app"></a>Démarrage rapide : Définir et lire un secret de coffre de clés (Key Vault) dans une application web .NET

Dans ce guide de démarrage rapide, vous allez passer en revue les étapes nécessaires pour qu’une application web Azure puisse lire les informations d’un coffre de clés Key Vault à l’aide d’identités de service administrées (MSI). Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Création d’un coffre de clés.
> * Stockage d’un secret dans le coffre de clés.
> * Récupération d’un secret à partir de Key Vault.
> * Création d’une application web Azure.
> * [Activation d’identités de service administrées (MSI)](../active-directory/managed-service-identity/overview.md).
> * Octroi des autorisations requises à l’application web pour lire des données venant de Key Vault.

Avant d’aller plus loin, lisez les [concepts de base](key-vault-whatis.md#basic-concepts), particulièrement [Managed Service Identity](../active-directory/managed-service-identity/overview.md).

## <a name="prerequisites"></a>Prérequis

* Sous Windows :
  * [Visual Studio 2017 version 15.7.3 ou version ultérieure](https://www.microsoft.com/net/download/windows) avec les charges de travail suivantes :
    * Développement web et ASP.NET
    * Développement multiplateforme .NET Core
  * [Kit de développement logiciel (SDK) .NET Core 2.1 ou version ultérieure](https://www.microsoft.com/net/download/windows)

* Sur Mac :
  * https://visualstudio.microsoft.com/vs/mac/

* Toutes les plateformes :
  * Téléchargez GIT [ici](https://git-scm.com/downloads).
  * Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
  * [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Vous avez besoin de l’interface de ligne de commande Azure version 2.0.4 ou ultérieure. Disponible pour Windows, Mac et Linux

## <a name="login-to-azure"></a>Connexion à Azure

   Pour vous connecter à Azure à l’aide de l’interface CLI, vous pouvez taper :

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Sélectionnez un nom de groupe de ressources et renseignez l’espace réservé.
L’exemple suivant crée un groupe de ressources nommé *<YourResourceGroupName>* à l’emplacement *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

Le groupe de ressources que vous venez de créer est utilisé tout au long de cet article.

## <a name="create-an-azure-key-vault"></a>Créer un Azure Key Vault

Ensuite, vous créez un coffre de clés dans le groupe de ressources créé à l’étape précédente. Fournissez les informations suivantes :

* Nom du coffre - **Veuillez sélectionner un nom de coffre de clés ici**. Le nom de Key Vault doit être une chaîne comprise entre 3 et 24 caractères. Il doit contenir uniquement des chiffres, des lettres et des tirets (0-9, a-z, A-Z et -).
* Nom du groupe de ressources - **Veuillez sélectionner un nom de groupe de ressources ici**.
* Emplacement - **USA Est**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.

## <a name="add-a-secret-to-key-vault"></a>Ajouter un secret au coffre de clés

Nous allons ajouter un secret pour montrer comment cela fonctionne. Vous pouvez stocker une chaîne de connexion SQL ou toute autre information que vous devez conserver en toute sécurité mais que vous devez rendre disponible à votre application. Dans ce didacticiel, le mot de passe sera appelé **AppSecret** et stocke la valeur de **MySecret**.

Tapez les commandes ci-dessous pour créer un secret dans Key Vault, appelé **AppSecret** et qui va stocker la valeur **MySecret** :

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Pour afficher sous forme de texte brut la valeur contenue dans le secret :

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Cette commande affiche les informations du secret, y compris l’URI. Après ces étapes, vous devez avoir un URI pointant vers un secret dans Azure Key Vault. Notez ces informations. Vous en aurez besoin dans une étape ultérieure.

## <a name="clone-the-repo"></a>Cloner le référentiel

Clonez le référentiel afin de créer une copie locale vous permettant de modifier la source en exécutant la commande suivante :

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>Ouvrir et modifier la solution

Modifiez le fichier program.cs pour exécuter l’exemple avec le nom de votre coffre de clés spécifique.

1. Accéder au dossier key-vault-dotnet-core-quickstart
2. Ouvrir le fichier de key-vault-dotnet-core-quickstart.sln dans Visual Studio 2017
3. Ouvrez le fichier Program.cs et remplacez l’espace réservé <YourKeyVaultName> par le nom du coffre de clés Key Vault que vous avez créé précédemment.

Cette solution utilise les bibliothèques NuGet [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) et [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

## <a name="run-the-app"></a>Exécution de l'application

Dans le menu principal de Visual Studio 2017, choisissez Déboguer > Démarrer sans débogage. Lorsque le navigateur s’affiche, accédez à la page À propos. La valeur d’AppSecret s’affiche.

## <a name="publish-the-web-application-to-azure"></a>Publier l’application web dans Azure

Nous publions cette application sur Azure pour l’afficher en direct en tant qu’application web, et pour afficher la récupération de la valeur du secret.

1. Dans Visual Studio, sélectionnez le projet **key-vault-dotnet-core-quickstart**.
2. Sélectionnez **Publier** puis **Démarrer**.
3. Créer un nouvel **App Service**, puis sélectionnez **Publier**.
4. Modifiez le nom de l’application en « keyvaultdotnetcorequickstart ».
5. Sélectionnez **Créer**.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-managed-service-identities-msi"></a>Activer les identités de service administrées (MSI, Managed Service Identity)

Azure Key Vault permet de stocker en toute sécurité des informations d’identification et d’autres clés et secrets, mais votre code doit s’authentifier sur Azure Key Vault pour les récupérer. Managed Service Identity (MSI) simplifie la résolution de ce problème en donnant aux services Azure une identité automatiquement administrée dans Azure Active Directory (Azure AD). Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, y compris Key Vault, sans avoir d’informations d’identification dans votre code.

1. Revenir à l’interface Azure CLI
2. Exécutez la commande assign-identity pour créer l’identité de cette application :

```azurecli
az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
```

>[!NOTE]
>L’exécution de cette commande revient à accéder au portail et à régler **Managed Service Identity** sur **Activer** dans les propriétés de l’application web.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Octroyer des autorisations à votre application pour lire des secrets dans Key Vault

Prenez note de la sortie quand vous [publiez l’application sur Azure] []. Elle doit être au format suivant :
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Ensuite, exécutez cette commande en utilisant le nom de votre coffre de clés Key Vault et la valeur de « PrincipalId » copiée ci-dessus :

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get

```

## <a name="next-steps"></a>Étapes suivantes

* [Page d'accueil d’Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Documentation d’Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
* [Kit de développement logiciel (SDK) Azure pour .NET](https://github.com/Azure/azure-sdk-for-net)
* [Référence de l'API REST Azure](https://docs.microsoft.com/rest/api/keyvault/)
