---
title: 'Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l’aide d’une application web Node | Microsoft Docs'
description: Dans ce guide de démarrage rapide, vous définissez et récupérez un secret depuis Azure Key Vault à l’aide d’une application web Node
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.workload: identity
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: c7bf6a22da89f0bf1e3897ec8fc30238b86b7b75
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882748"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-node-web-app"></a>Démarrage rapide : Définir et récupérer un secret à partir d’Azure Key Vault à l’aide d’une application web Node 

Ce démarrage rapide vous montre comment stocker un secret dans Azure Key Vault et le récupérer à l’aide d’une application web. L’utilisation de Key Vault permet de protéger les informations. Pour afficher la valeur du secret, vous devrez exécuter ce démarrage rapide sur Azure. Le guide de démarrage rapide utilise Node.js et des identités managées pour les ressources Azure. Vous allez apprendre à effectuer les actions suivantes :

* Création d’un coffre de clés
* Stockage d’un secret dans le coffre de clés.
* Récupération d’un secret à partir du coffre de clés.
* Création d’une application web Azure.
* Activer une [identité managée](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) pour l’application web.
* Octroi des autorisations requises à l’application web pour lire des données venant du coffre de clés.

Avant de procéder, assurez-vous d’être familiarisé avec les [concepts de base](key-vault-whatis.md#basic-concepts) de Key Vault.

> [!NOTE]
> Key Vault est un référentiel central pour stocker les secrets par programmation. Mais pour cela, les applications et les utilisateurs doivent d’abord s’authentifier sur Key Vault, et donc présenter un secret. Conformément aux bonnes pratiques de sécurité, ce premier secret doit être changé régulièrement. 
>
> Avec les [identités de service managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md), les applications s’exécutant dans Azure bénéficient d’une identité managée automatiquement par Azure. Cela permet de résoudre le *problème d’introduction de secrets* où les utilisateurs et les applications peuvent suivre les meilleures pratiques et n’ont pas à se soucier de la rotation du premier secret.

## <a name="prerequisites"></a>Prérequis

* [Node.js](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) version 2.0.4 ou ultérieure. Ce démarrage rapide nécessite que vous exécutiez l’interface Azure CLI localement. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau l’interface CLI, consultez l’article [Installation d’Azure CLI 2.0](https://review.docs.microsoft.com/en-us/cli/azure/install-azure-cli?branch=master&view=azure-cli-latest).
* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure

Pour vous connecter à Azure à l’aide de l’interface CLI, entrez :

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Sélectionnez un nom de groupe de ressources et renseignez l’espace réservé.
L’exemple suivant crée un groupe de ressources dans l’emplacement USA Est.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

Le groupe de ressources que vous venez de créer est utilisé tout au long de cet article.

## <a name="create-a-key-vault"></a>Création d’un coffre de clés

Ensuite, vous créez un coffre de clés avec le groupe de ressources que vous avez créé à l’étape précédente. Bien que cet article utilise « ContosoKeyVault » comme nom, vous devez utiliser un nom unique. Fournissez les informations suivantes :

* Nom du coffre de clés.
* Nom du groupe de ressources. le nom doit être une chaîne de 3 à 24 caractères, et contenir uniquement les caractères allant de 0 à 9, de a à z, de A à Z et un trait d’union (-).
* Localisation : **USA Est**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.

## <a name="add-a-secret-to-the-key-vault"></a>Ajouter un secret au coffre de clés

Nous allons ajouter un secret pour montrer comment cela fonctionne. Vous pouvez stocker une chaîne de connexion SQL ou toute autre information que vous devez conserver en toute sécurité mais que vous devez rendre disponible à votre application. Dans ce didacticiel, le mot de passe sera appelé **AppSecret** et stocke la valeur de **MySecret**.

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
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Installer des dépendances

Exécutez les commandes suivantes pour installer les dépendances :

```
cd key-vault-node-quickstart
npm install
```

Ce projet utilise deux modules de nœud : [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) et [azure-keyvault](https://www.npmjs.com/package/azure-keyvault).

## <a name="publish-the-web-app-to-azure"></a>Publier l’application web dans Azure

Créer un [plan Azure App Service](https://azure.microsoft.com/services/app-service/). Vous pouvez stocker plusieurs applications web dans ce plan.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
Créez ensuite une application web. Dans l’exemple suivant, remplacez `<app_name>` par un nom d’application unique (les caractères autorisés sont a-z, 0-9 et -). Le runtime est défini sur NODE|6.9. Pour voir tous les runtimes, exécutez `az webapp list-runtimes`.

    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    ```
Une fois l’application web créée, Azure CLI affiche une sortie similaire à l’exemple suivant :

    ```
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "<app_name>.azurewebsites.net",
      "enabled": true,
      "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
      < JSON data removed for brevity. >
    }
    ```
Accédez à votre application web nouvellement créée. Vous devriez voir qu’elle fonctionne. Remplacez `<app_name>` par un nom d’application unique.

    ```
    http://<app name>.azurewebsites.net
    ```
La commande précédente crée également une application prenant en charge Git qui vous permet de la déployer vers Azure à partir de votre référentiel Git local. Le référentiel Git local est configuré avec cette URL : https://<username>@<nom_app>.scm.azurewebsites.net/<nom_app>.git.

Une fois que vous avez terminé la commande précédente, vous pouvez ajouter Azure à distance à votre référentiel Git local. Remplacez `<url>` par l’URL du référentiel Git.

    ```
    git remote add azure <url>
    ```

## <a name="enable-a-managed-identity-for-the-web-app"></a>Activer une identité managée pour l’application web

Azure Key Vault permet de stocker en toute sécurité des informations d’identification et autres clés et secrets, mais votre code doit s’authentifier sur Key Vault pour les récupérer. La [vue d’ensemble des identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md) simplifie la résolution de ce problème en fournissant aux services Azure une identité managée automatiquement dans Azure AD (Azure Active Directory). Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, y compris Key Vault, sans avoir d’informations d’identification dans votre code.

Exécutez la commande assign-identity pour créer l’identité de cette application :

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

Cette commande revient à accéder au portail et à affecter au paramètre **Identité / Affecté(e) par le système** la valeur **Activer** dans les propriétés de l’application web.

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Octroyer des autorisations à votre application pour lire des secrets dans Key Vault

Notez le résultat de la commande précédente. Elle doit respecter le format :
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Ensuite, exécutez la commande suivante en utilisant le nom de votre coffre de clés et la valeur de **PrincipalId** :

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get set
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Déployer l’application Node sur Azure et récupérer la valeur du secret

Exécutez la commande suivante pour déployer l’application sur Azure :

```
git push azure master
```

Lorsque c’est fait et que vous accédez à https://<nom_app>.azurewebsites.net, vous pouvez voir la valeur du secret. Assurez-vous d’avoir remplacé le nom <YourKeyVaultName> par le nom de votre coffre.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [SDK Azure pour Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
