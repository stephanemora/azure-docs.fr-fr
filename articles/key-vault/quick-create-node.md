---
title: 'Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l’aide d’une application web Node | Microsoft Docs'
description: 'Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l’aide d’une application web Node'
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
ms.openlocfilehash: 860294ebc7fbadd3eeefc4298ec740ca7f704587
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714392"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-a-node-web-app"></a>Démarrage rapide : définir et récupérer un secret depuis Azure Key Vault à l’aide d’une application web Node 

Ce démarrage rapide vous montre comment stocker un secret dans Key Vault et le récupérer à l’aide d’une application web. Pour afficher la valeur du secret, vous devrez exécuter cette procédure sur Azure. Le guide de démarrage rapide utilise Node.js et des identités managées pour les ressources Azure.

> [!div class="checklist"]
> * Création d’un coffre de clés.
> * Stockage d’un secret dans le coffre de clés.
> * Récupération d’un secret à partir de Key Vault.
> * Création d’une application web Azure.
> * Activation d’une [identité managée](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) pour l’application web.
> * Octroi des autorisations requises à l’application web pour lire des données venant de Key Vault.

Avant de procéder, assurez-vous d’être familiarisé avec les [concepts de base](key-vault-whatis.md#basic-concepts).

>[!NOTE]
Pour comprendre pourquoi le didacticiel ci-dessous est la meilleure pratique, il est nécessaire de comprendre certains concepts. Key Vault est un référentiel central pour stocker les secrets par programmation. Mais pour cela, les applications / utilisateurs doivent d’abord s’authentifier sur Key Vault, et donc présenter un secret. Pour suivre les meilleures pratiques de sécurité, ce premier secret doit faire l’objet d’une rotation périodique. Par contre, avec les [identités managées pour les ressources Azure](../active-directory/managed-identities-azure-resources/overview.md), les applications s’exécutant dans Azure bénéficient d’une identité qui est automatiquement managée par Azure. Cela permet de résoudre le **problème d’introduction de secrets** où les utilisateurs / applications peuvent suivre les meilleures pratiques et n’ont pas à se soucier de la rotation du premier secret

## <a name="prerequisites"></a>Prérequis

* [Node JS](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) version 2.0.4 ou ultérieure
* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="login-to-azure"></a>Connexion à Azure

Pour vous connecter à Azure à l’aide de l’interface CLI, vous pouvez taper la commande suivante :

```azurecli
az login
```

## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Sélectionnez un nom de groupe de ressources et renseignez l’espace réservé.
L’exemple suivant crée un groupe de ressources nommé *<YourResourceGroupName>* à l’emplacement *eastus*.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

Le groupe de ressources que vous venez de créer est utilisé tout au long de ce didacticiel.

## <a name="create-an-azure-key-vault"></a>Créer un Azure Key Vault

Ensuite, vous créez un coffre de clés en utilisant le groupe de ressources créé à l’étape précédente. Bien que « ContosoKeyVault » soit utilisé comme le nom du coffre de clés dans cet article, vous devez utiliser un nom unique. Fournissez les informations suivantes :

* Nom du coffre - **Sélectionnez un nom de coffre de clés ici**.
* Nom du groupe de ressources - **Sélectionnez un nom de groupe de ressources ici**.
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
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Installer des dépendances

Ici, nous installons les dépendances. Exécutez les commandes suivantes cd key-vault-node-quickstart npm install

Ce projet a utilisé 2 modules de nœud :

* [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) 
* [azure-keyvault](https://www.npmjs.com/package/azure-keyvault)

## <a name="publish-the-web-application-to-azure"></a>Publier l’application web dans Azure

Voici les quelques étapes que nous devons faire

- La 1ère étape consiste à créer un [Azure App Service](https://azure.microsoft.com/services/app-service/) planifier. Vous pouvez stocker plusieurs applications web dans ce plan.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
- Nous créons maintenant une application web. Dans l’exemple suivant, remplacez <app_name> par un nom d’application unique (les caractères autorisés sont a-z, 0-9 et -). Le runtime est défini sur NODE|6.9. Pour afficher tous les runtimes pris en charge, exécutez az webapp list-runtimes
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
    Accédez à votre application web nouvellement créée et vous devriez voir une application web qui fonctionne. Remplacez <app name> par un nom d’application unique.

    ```
    http://<app name>.azurewebsites.net
    ```
    La commande ci-dessus crée également une application prenant en charge Git qui vous permet de déployer vers Azure à partir de votre git local. 
    Le git local est configuré avec l’URL de « https://<username>@< app_name >.scm.azurewebsites.net/ < app_name > .git »

- Créez un utilisateur de déploiement. Une fois la commande précédente terminée, vous pouvez ajouter un référentiel Azure distant à votre dépôt Git local. Remplacez <url> par l’URL du Git distant de la section Activer Git pour votre application.

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

Notez ou copiez la sortie de la commande ci-dessus. Elle doit respecter le format :
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Ensuite, exécutez cette commande en utilisant le nom de votre coffre de clés Key Vault et la valeur de « PrincipalId » copiée ci-dessus :

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Déployer l’application Node sur Azure et récupérer la valeur du secret

Maintenant que tout est configuré. Exécutez la commande suivante pour déployer l’application sur Azure

```
git push azure master
```

Lorsque c’est fait et que vous parcourez https://<app_name>.azurewebsites.net, vous pouvez voir la valeur du secret.
Assurez-vous d’avoir remplacé le nom <YourKeyVaultName> par le nom de votre coffre

## <a name="next-steps"></a>Étapes suivantes

* [Page d'accueil d’Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
* [Documentation d’Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
* [SDK Azure pour Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Référence de l'API REST Azure](https://docs.microsoft.com/rest/api/keyvault/)
