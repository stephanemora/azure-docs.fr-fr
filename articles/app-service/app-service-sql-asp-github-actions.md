---
title: 'Tutoriel : Utiliser GitHub Actions pour déployer sur App Service et se connecter à une base de données'
description: Déployer une application ASP.NET Core qui s’appuie sur une base de données sur Azure avec GitHub Actions
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/13/2021
ms.author: jukullam
ms.custom: github-actions-azure
ms.openlocfilehash: 01d4293b33eb0756dec8bc9ae870a972959ced89
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128701508"
---
# <a name="tutorial-use-github-actions-to-deploy-to-app-service-and-connect-to-a-database"></a>Tutoriel : Utiliser GitHub Actions pour déployer sur App Service et se connecter à une base de données

Découvrez comment configurer un workflow GitHub Actions visant à déployer une application ASP.NET Core avec un back-end [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) . Quand vous avez terminé, vous disposez d’une application ASP.NET s’exécutant dans Azure et connectée à SQL Database. Vous allez d’abord utiliser un [modèle ARM](/azure/azure-resource-manager/templates/overview) pour créer des ressources.

Ce tutoriel n’utilise pas de conteneurs. Si vous souhaitez déployer sur une application ASP.NET Core conteneurisée, consultez [Utiliser GitHub Actions pour déployer sur App Service pour conteneurs et se connecter à une base de données](app-service-sql-github-actions.md).

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> - Utiliser un workflow GitHub Actions pour ajouter des ressources à Azure à l'aide d'un modèle Azure Resource Manager (modèle ARM)
> - Utiliser un workflow GitHub Actions pour créer une application ASP.NET Core

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous aurez besoin des éléments suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un compte GitHub. Si vous n’en avez pas, inscrivez-vous [gratuitement](https://github.com/join).
  - Un référentiel GitHub pour stocker vos modèles Resource Manager et vos fichiers de workflow. Pour en créer un, consultez [Création d’un référentiel](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository).

## <a name="download-the-sample"></a>Télécharger l’exemple

[Dupliquez (fork) l'exemple de projet](https://github.com/Azure-Samples/dotnetcore-sqldb-ghactions) dans le référentiel Exemples Azure.

```
https://github.com/Azure-Samples/dotnetcore-sqldb-ghactions
```

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Ouvrez Azure Cloud Shell sur https://shell.azure.com. Vous pouvez également utiliser l'interface de ligne de commande Azure si vous l'avez installée localement. (Pour plus d’informations sur Cloud Shell, consultez [Vue d’ensemble de Cloud Shell](../cloud-shell/overview.md).)

```azurecli-interactive
az group create --name {resource-group-name} --location {resource-group-location}
```

## <a name="generate-deployment-credentials"></a>Générer les informations d’identification du déploiement

Vous devez vous authentifier auprès d'un principal de service pour que le script de déploiement de ressources fonctionne. Vous pouvez créer un [principal de service](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) à l’aide de la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) dans [Azure CLI](/cli/azure/). Exécutez cette commande en utilisant [Azure Cloud Shell](https://shell.azure.com/) dans le portail Azure ou en sélectionnant le bouton **Essayer**.

```azurecli-interactive
    az ad sp create-for-rbac --name "{service-principal-name}" --sdk-auth --role contributor --scopes /subscriptions/{subscription-id}
```

Dans l'exemple, remplacez les espaces réservés par votre ID d'abonnement, le nom du groupe de ressources et le nom du principal de service. La sortie correspond à un objet JSON avec les informations d’identification de l’attribution de rôle qui fournit l’accès à votre application App Service. Copiez cet objet JSON pour une version ultérieure. Pour obtenir de l'aide, accédez à [Configurer les informations d'identification de déploiement](https://github.com/Azure/login#configure-deployment-credentials).

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret-for-authentication"></a>Configurer le secret GitHub pour l’authentification

Dans [GitHub](https://github.com/), parcourez votre référentiel, sélectionnez **Paramètres > Secrets > Ajouter un nouveau secret**.

Pour utiliser les [informations d’identification au niveau de l’utilisateur](#generate-deployment-credentials), collez l’intégralité de la sortie JSON à partir de la commande Azure CLI dans le champ de valeur du secret. Nommez le secret `AZURE_CREDENTIALS`.

## <a name="add-github-secrets-for-your-build"></a>Ajouter des secrets GitHub pour votre build

1. Créez [deux nouveaux secrets](https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository) dans votre dépôt GitHub pour `SQLADMIN_PASS` et `SQLADMIN_LOGIN`. Veillez à choisir un mot de passe complexe, sinon l’étape de création du serveur de base de données SQL échouera. Enregistrez ce mot de passe séparément car vous n'y aurez plus accès ensuite.

2. Créez un secret `AZURE_SUBSCRIPTION_ID` pour votre ID d’abonnement Azure. Si vous ne connaissez pas votre ID d’abonnement, utilisez cette commande dans le shell Azure pour le trouver. Copiez la valeur dans la colonne `SubscriptionId`.
    ```azurecli
    az account list -o table
    ```
 
## <a name="create-azure-resources"></a>Créer des ressources Azure

Le workflow de création de ressources Azure exécute un [modèle ARM](/azure/azure-resource-manager/templates/overview) pour déployer les ressources vers Azure. Le workflow :

- extrait le code source à l'aide de l'[action d'extraction](https://github.com/marketplace/actions/checkout) ;
- se connecte à Azure à l'aide de l'[action de connexion à Azure](https://github.com/marketplace/actions/azure-login) et recueille des informations sur l'environnement et les ressources Azure ;
- déploie les ressources à l'aide de l'[action de déploiement Azure Resource Manager](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template).

Pour exécuter le workflow de création de ressources Azure :

1. Ouvrez le fichier `infraworkflow.yml` dans `.github/workflows` au sein de votre référentiel.

1. Remplacez la valeur de `AZURE_RESOURCE_GROUP` par le nom de votre groupe de ressources.

1. Définissez l’entrée pour `region` dans vos actions de déploiement ARM pour votre région. 
    1. Ouvrez `templates/azuredeploy.resourcegroup.parameters.json` et mettez à jour la propriété `rgLocation` avec votre région.
 
1. Accédez à **Actions** et sélectionnez **Exécuter le workflow**.

   :::image type="content" source="media/github-actions-workflows/github-actions-run-workflow.png" alt-text="Exécutez le workflow GitHub Actions permettant d'ajouter les ressources.":::

1. Vérifiez qu'une coche verte apparaît sur la page **Actions** pour vous assurer que votre action a bien été exécutée.

   :::image type="content" source="media/github-actions-workflows/create-resources-success.png" alt-text="Exécution réussie de la création des ressources.":::

1. Une fois que vous avez créé vos ressources, accédez à **Actions**, sélectionnez Créer des ressources Azure et désactivez le workflow. 
 
    :::image type="content" source="media/github-actions-workflows/disable-workflow-github-actions.png" alt-text="Désactiver le workflow Créer des ressources Azure.":::

## <a name="create-a-publish-profile-secret"></a>Créer un secret de profil de publication

1. Dans le portail Azure, ouvrez votre nouvelle instance App Service intermédiaire (emplacement) créé avec le workflow `Create Azure Resources`.

1. Sélectionnez **Obtenir le profil de publication**.

1. Ouvrez le fichier de profil de publication dans un éditeur de texte et copiez son contenu. 

1. Créez un nouveau secret GitHub pour `AZURE_WEBAPP_PUBLISH_PROFILE`. 

## <a name="build-and-deploy-your-app"></a>Créer et déployer votre application

Pour exécuter le workflow de création et de déploiement :

1. Ouvrez votre fichier `workflow.yaml` dans `.github/workflows` au sein de votre référentiel.

1. Vérifiez que les variables d’environnement pour `AZURE_RESOURCE_GROUP`, `AZURE_WEBAPP_NAME`, `SQLSERVER_NAME` et `DATABASE_NAME` correspondent à celles dans `infraworkflow.yml`.

1. Vérifiez que votre application a été déployée en visitant l’URL dans le résultat Changez l’emplacement en emplacement de production. Vous devriez voir un exemple d’application, My TodoList App. 
 
## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin de votre exemple de projet, supprimez votre groupe de ressources dans le portail Azure et supprimez votre dépôt sur GitHub. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’intégration d’Azure et de GitHub](/azure/developer/github/)
