---
title: 'Tutoriel : Utiliser GitHub Actions pour effectuer un déploiement sur App Service pour conteneurs et se connecter à une base de données'
description: Apprenez à déployer une application ASP.NET Core sur Azure et Azure SQL Database à l'aide de GitHub Actions
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/22/2021
ms.author: jukullam
ms.custom: github-actions-azure
ms.openlocfilehash: f8048dab26451fea85b52caa3ffdc27d7e0a677a
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111894143"
---
# <a name="tutorial-use-github-actions-to-deploy-to-app-service-for-containers-and-connect-to-a-database"></a>Tutoriel : Utiliser GitHub Actions pour effectuer un déploiement sur App Service pour conteneurs et se connecter à une base de données

Ce tutoriel vous guidera tout au long du processus de configuration d'un workflow GitHub Actions visant à déployer une application ASP.NET Core conteneurisée avec un back-end [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) . Quand vous avez terminé, vous disposez d’une application ASP.NET s’exécutant dans Azure et connectée à SQL Database. Vous allez d'abord créer des ressources Azure à l'aide d'un workflow GitHub Actions de [modèle ARM](../azure-resource-manager/templates/overview.md).

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> - Utiliser un workflow GitHub Actions pour ajouter des ressources à Azure à l'aide d'un modèle Azure Resource Manager (modèle ARM)
> - Utiliser un workflow GitHub Actions pour créer un conteneur à l'aide des dernières modifications apportées à l'application web

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous aurez besoin des éléments suivants :

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un compte GitHub. Si vous n’en avez pas, inscrivez-vous [gratuitement](https://github.com/join).
  - Un référentiel GitHub pour stocker vos modèles Resource Manager et vos fichiers de workflow. Pour en créer un, consultez [Création d’un référentiel](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository).

## <a name="download-the-sample"></a>Télécharger l’exemple

[Dupliquez (fork) l'exemple de projet](https://github.com/Azure-Samples/dotnetcore-containerized-sqldb-ghactions/) dans le référentiel Exemples Azure.

```
https://github.com/Azure-Samples/dotnetcore-containerized-sqldb-ghactions/
```

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Ouvrez Azure Cloud Shell sur https://shell.azure.com. Vous pouvez également utiliser l'interface de ligne de commande Azure si vous l'avez installée localement. (Pour plus d'informations sur Cloud Shell, consultez Présentation de Cloud Shell).

```azurecli-interactive
    az group create --name {resource-group-name} --location {resource-group-location}
```

## <a name="generate-deployment-credentials"></a>Générer les informations d’identification du déploiement

Vous devez vous authentifier auprès d'un principal de service pour que le script de déploiement de ressources fonctionne. Vous pouvez créer un [principal de service](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) à l’aide de la commande [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) dans [Azure CLI](/cli/azure/). Exécutez cette commande en utilisant [Azure Cloud Shell](https://shell.azure.com/) dans le portail Azure ou en sélectionnant le bouton **Essayer**.

```azurecli-interactive
    az ad sp create-for-rbac --name "{service-principal-name}" --sdk-auth --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
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

> [!IMPORTANT]
> Il est toujours conseillé d’accorder un accès minimal. L’étendue dans l’exemple précédent est limitée à l’application App Service spécifique, et non à l’ensemble du groupe de ressources.

## <a name="configure-the-github-secret-for-authentication"></a>Configurer le secret GitHub pour l’authentification

Dans [GitHub](https://github.com/), parcourez votre référentiel, sélectionnez **Paramètres > Secrets > Ajouter un nouveau secret**.

Pour utiliser les [informations d’identification au niveau de l’utilisateur](#generate-deployment-credentials), collez l’intégralité de la sortie JSON à partir de la commande Azure CLI dans le champ de valeur du secret. Nommez le secret `AZURE_CREDENTIALS`.

## <a name="add-a-sql-server-secret"></a>Ajouter un secret SQL Server

Dans votre référentiel, créez un secret pour `SQL_SERVER_ADMIN_PASSWORD`. Il peut s'agir de n'importe quel mot de passe conforme aux normes Azure en matière de sécurité des mots de passe. Enregistrez ce mot de passe séparément car vous n'y aurez plus accès ensuite.

## <a name="create-azure-resources"></a>Créer des ressources Azure

Le workflow de création de ressources Azure exécute un [modèle ARM](../azure-resource-manager/templates/overview.md) pour déployer les ressources vers Azure. Le workflow :

- extrait le code source à l'aide de l'[action d'extraction](https://github.com/marketplace/actions/checkout) ;
- se connecte à Azure à l'aide de l'[action de connexion à Azure](https://github.com/marketplace/actions/azure-login) et recueille des informations sur l'environnement et les ressources Azure ;
- déploie les ressources à l'aide de l'[action de déploiement Azure Resource Manager](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template).

Pour exécuter le workflow de création de ressources Azure :

1. Ouvrez le fichier `azuredeploy.yaml` dans `.github/workflows` au sein de votre référentiel.

1. Remplacez la valeur de `AZURE_RESOURCE_GROUP` par le nom de votre groupe de ressources.

1. Accédez à **Actions** et sélectionnez **Exécuter le workflow**.

   :::image type="content" source="media/github-actions-workflows/github-actions-run-workflow.png" alt-text="Exécutez le workflow GitHub Actions permettant d'ajouter les ressources.":::

1. Vérifiez qu'une coche verte apparaît sur la page **Actions** pour vous assurer que votre action a bien été exécutée.

   :::image type="content" source="media/github-actions-workflows/create-resources-success.png" alt-text="Exécution réussie de la création des ressources.":::

## <a name="add-container-registry-and-sql-secrets"></a>Ajouter un registre de conteneurs et des secrets SQL

1. Sur le portail Azure, ouvrez l'instance d'Azure Container Registry que vous venez de créer dans votre groupe de ressources.

1. Accédez à **Clés d'accès** et copiez le nom d'utilisateur et le mot de passe.

1. Dans votre référentiel, créez de nouveaux secrets GitHub pour `ACR_USERNAME` et `ACR_PASSWORD`.

1. Sur le portail Azure, ouvrez votre base de données Azure SQL. Ouvrez **Chaînes de connexion** et copiez la valeur.

1. Créez un secret pour `SQL_CONNECTION_STRING`. Remplacez la valeur `{your_password}` par vôtre valeur `SQL_SERVER_ADMIN_PASSWORD`.

## <a name="build-push-and-deploy-your-image"></a>Créer, envoyer et déployer votre image

Le workflow de création, d'envoi et de déploiement génère un conteneur qui intègre les dernières modifications apportées à l'application, envoie le conteneur à [Azure Container Registry](../container-registry/index.yml) et met à jour l'emplacement de préproduction de l'application web pour qu'il pointe vers le dernier conteneur envoyé. Le workflow contient un travail de création et de déploiement :

- Le travail de création extrait le code source à l'aide de l'[action d'extraction](https://github.com/marketplace/actions/checkout). Le travail utilise ensuite l'[action de connexion à Docker](https://github.com/marketplace/actions/docker-login) et un script personnalisé pour s'authentifier auprès d'Azure Container Registry, générer une image conteneur et la déployer sur Azure Container Registry.
- Le travail de déploiement se connecte à Azure à l'aide de l'[action de connexion à Azure](https://github.com/marketplace/actions/azure-login) et recueille des informations sur l'environnement et les ressources Azure. Le travail procède ensuite à la mise à jour des paramètres de l'application web à l'aide de l'[action des paramètres Azure App Service](https://github.com/marketplace/actions/azure-app-service-settings), et lance le déploiement sur un emplacement de préproduction App Service à l'aide de l'[action Azure Web Deploy](https://github.com/marketplace/actions/azure-webapp). Enfin, le travail exécute un script personnalisé pour mettre à jour la base de données SQL et bascule l'emplacement de préproduction vers la production.

Pour exécuter le workflow de création, d'envoi et de déploiement :

1. Ouvrez votre fichier `build-deploy.yaml` dans `.github/workflows` au sein de votre référentiel.

1. Vérifiez que les variables d'environnement de `AZURE_RESOURCE_GROUP` et `WEB_APP_NAME` correspondent à celles du fichier `azuredeploy.yaml`.

1. Mettez à jour la valeur `ACR_LOGIN_SERVER` de votre serveur de connexion Azure Container Registry.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’intégration d’Azure et de GitHub](/azure/developer/github/)