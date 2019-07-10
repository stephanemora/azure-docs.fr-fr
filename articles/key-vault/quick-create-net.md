---
title: "Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l'aide d'une application web .NET - Azure Key Vault | Microsoft Docs"
description: Dans ce guide de démarrage rapide, vous définissez et récupérez un secret depuis Azure Key Vault à l’aide d’une application web .NET
services: key-vault
author: msmbaldwin
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 01/02/2019
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 4f9fff41e4b9043c271d656583fb8b9a11ff3a7a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052786"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-net-web-app"></a>Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l’aide d’une application web .NET

Dans ce guide de démarrage rapide, vous suivez les étapes permettant qu’une application web Azure lise des informations depuis Azure Key Vault à l’aide d’identités managées pour les ressources Azure. L’utilisation de Key Vault permet de protéger les informations. Vous allez apprendre à effectuer les actions suivantes :

* Création d’un coffre de clés
* Stockage d’un secret dans le coffre de clés.
* Récupération d’un secret à partir du coffre de clés.
* Création d’une application web Azure.
* Activer une [identité de service managée](../active-directory/managed-identities-azure-resources/overview.md) pour l’application web.
* Octroi des autorisations requises à l’application web pour lire des données venant du coffre de clés.

Avant d’aller plus loin, veuillez lire les [concepts de base de Key Vault](key-vault-whatis.md#basic-concepts).

>[!NOTE]
>Key Vault est un référentiel central pour stocker les secrets par programmation. Mais pour cela, les applications et les utilisateurs doivent d’abord s’authentifier sur Key Vault, et donc présenter un secret. Conformément aux bonnes pratiques de sécurité, ce premier secret doit être changé régulièrement. 
>
>Avec les [identités de service managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md), les applications s’exécutant dans Azure bénéficient d’une identité managée automatiquement par Azure. Cela permet de résoudre le *problème d’introduction de secrets* où les utilisateurs et les applications peuvent suivre les meilleures pratiques et n’ont pas à se soucier de la rotation du premier secret.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prérequis

* Sous Windows :
  * [Visual Studio 2019](https://www.microsoft.com/net/download/windows) avec les charges de travail suivantes :
    * Développement web et ASP.NET
    * Développement multiplateforme .NET Core
  * [Kit de développement logiciel (SDK) .NET Core 2.1 ou version ultérieure](https://www.microsoft.com/net/download/windows)

* Sur Mac :
  * Consultez [les nouveautés de Visual Studio pour Mac](https://visualstudio.microsoft.com/vs/mac/).

* Toutes les plateformes :
  * Git ([télécharger](https://git-scm.com/downloads)).
  * Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) version 2.0.4 ou ultérieure. Disponible pour Windows, Mac et Linux.

## <a name="log-in-to-azure"></a>Connexion à Azure

Pour vous connecter à Azure à l’aide de l’interface CLI, entrez :

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Sélectionnez un nom de groupe de ressources et renseignez l’espace réservé.
L’exemple suivant crée un groupe de ressources dans l’emplacement Est des États-Unis :

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

Le groupe de ressources que vous venez de créer est utilisé tout au long de cet article.

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

Ensuite, vous créez un coffre de clés dans le groupe de ressources que vous avez créé à l’étape précédente. Fournissez les informations suivantes :

* Nom du coffre de clés : le nom doit être une chaîne de 3 à 24 caractères, et contenir uniquement les caractères allant de 0 à 9, de a à z, de A à Z et un trait d’union (-).
* Nom du groupe de ressources.
* Localisation : **USA Est**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.

## <a name="add-a-secret-to-the-key-vault"></a>Ajouter un secret au coffre de clés

Nous allons ajouter un secret pour montrer comment cela fonctionne. Vous pouvez stocker une chaîne de connexion SQL ou toute autre information que vous devez conserver en toute sécurité mais que vous devez rendre disponible à votre application.

Saisissez les commandes suivantes pour créer un secret dans le coffre de clés appelé **AppSecret**. Ce secret stockera la valeur **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Pour afficher sous forme de texte brut la valeur contenue dans le secret :

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Cette commande affiche les informations du secret, y compris l’URI. Une fois ces étapes terminées, vous devez avoir un URI pointant vers un secret dans le coffre de clés. Notez ces informations. Vous en aurez besoin dans une étape ultérieure.

## <a name="clone-the-repo"></a>Cloner le référentiel

Clonez le référentiel pour créer une copie locale dans laquelle vous pouvez modifier la source. Exécutez la commande suivante :

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>Ouvrir et modifier la solution

Modifiez le fichier program.cs pour exécuter l’exemple avec le nom de votre coffre de clés spécifique :

1. Naviguez jusqu’au dossier key-vault-dotnet-core-quickstart.
2. Ouvrez le fichier key-vault-dotnet-core-quickstart.sln dans Visual Studio 2019.
3. Ouvrez le fichier Program.cs et remplacez l’espace réservé *YourKeyVaultName* par le nom du coffre de clés créé précédemment.

Cette solution utilise les bibliothèques NuGet [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) et [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

## <a name="run-the-app"></a>Exécution de l'application

Dans le menu principal de Visual Studio 2019, sélectionnez **Déboguer** > **Démarrer sans débogage**. Lorsque le navigateur s’affiche, accédez à la page **À propos**. La valeur d’**AppSecret** s’affiche.

## <a name="publish-the-web-application-to-azure"></a>Publier l’application web dans Azure

Publiez cette application sur Azure pour l’afficher en direct en tant qu’application web, et pour afficher la récupération de la valeur du secret :

1. Dans Visual Studio, sélectionnez le projet **key-vault-dotnet-core-quickstart**.
2. Sélectionnez **Publier** > **Démarrer**.
3. Créer un nouvel **App Service**, puis sélectionnez **Publier**.
4. Modifiez le nom de l’application en **keyvaultdotnetcorequickstart**.
5. Sélectionnez **Créer**.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-a-managed-identity-for-the-web-app"></a>Activer une identité managée pour l’application web

Azure Key Vault permet de stocker en toute sécurité des informations d’identification et autres clés et secrets, mais votre code doit s’authentifier sur Key Vault pour les récupérer. La [vue d’ensemble des identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) simplifie la résolution de ce problème en fournissant aux services Azure une identité managée automatiquement dans Azure AD (Azure Active Directory). Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, y compris Key Vault, sans avoir d’informations d’identification dans votre code.

Dans Azure CLI, exécutez la commande assign-identity pour créer l’identité de cette application :

   ```azurecli
   az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
   ```

>[!NOTE]
>La commande dans cette procédure revient à accéder au portail et à affecter au paramètre **Identité / Affecté(e) par le système** la valeur **Activer** dans les propriétés de l’application web.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Octroyer des autorisations à votre application pour lire des secrets dans Key Vault

Prenez note de la sortie quand vous publiez l’application sur Azure. Elle doit être au format suivant :
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Ensuite, exécutez cette commande en utilisant le nom de votre coffre de clés et la valeur de **PrincipalId** :

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

Maintenant lorsque vous exécutez l’application, vous devriez voir votre valeur secrète récupérée. Dans la commande précédente, vous donnez l’identité des autorisations du service d’applications pour effectuer des opérations **get** et **list** sur votre coffre de clés.

## <a name="clean-up-resources"></a>Supprimer des ressources
Supprimez le groupe de ressources, la machine virtuelle et toutes les ressources associées quand vous n’en avez plus besoin. Pour ce faire, sélectionnez le groupe de ressources du coffre de clés et sélectionnez **Supprimer**.

Supprimez le coffre de clés à l’aide de la commande [az keyvault delete](https://docs.microsoft.com/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-delete) :

```azurecli
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)
