---
title: 'Démarrage rapide : créer une application web sur Azure Arc'
description: Commencez à utiliser App Service sur Azure Arc en déployant votre première application web.
ms.topic: quickstart
ms.date: 05/11/2021
ms.openlocfilehash: b57c5e80ecef87901221c3ead49419f3cce89302
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385723"
---
# <a name="create-an-app-service-app-on-azure-arc-preview"></a>Créer une application App Service sur Azure Arc (préversion)

Dans ce démarrage rapide, vous allez créer une [application App service sur un cluster Kubernetes avec Azure Arc](overview-arc-integration.md) (préversion). Ce scénario ne prend en charge que les applications Linux, et vous pouvez utiliser une pile de langage intégrée ou un conteneur personnalisé.

## <a name="prerequisites"></a>Prérequis

- [Configurez votre Kubernetes avec Azure Arc pour exécuter App Service](manage-create-arc-environment.md).

[!INCLUDE [app-service-arc-cli-install-extensions](../../includes/app-service-arc-cli-install-extensions.md)]

## <a name="1-create-a-resource-group"></a>1. Créer un groupe de ressources

Exécutez la commande suivante.

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

<!-- ## 2. Create an App Service plan

Run the following command and replace `<environment-name>` with the name of the App Service Kubernetes environment (see [Prerequisites](#prerequisites)).

```azurecli-interactive
az appservice plan create --resource-group myResourceGroup --name myAppServicePlan --custom-location <environment-name> --kube-sku K1
``` 

Currently does not work

-->

## <a name="2-get-the-custom-location"></a>2. Accéder à l’emplacement personnalisé

[!INCLUDE [app-service-arc-get-custom-location](../../includes/app-service-arc-get-custom-location.md)]


## <a name="3-create-an-app"></a>3. Créer une application

L’exemple suivant crée une application Node.js. Remplacez `<app-name>` par un nom unique au sein de votre cluster (les caractères valides sont `a-z`, `0-9` et `-`). Pour voir tous les runtimes, exécutez [`az webapp list-runtimes --linux`](/cli/azure/webapp).

```azurecli-interactive
 az webapp create \
    --resource-group myResourceGroup \
    --name <app-name> \
    --custom-location $customLocationId \
    --runtime 'NODE|12-lts'
```

## <a name="4-deploy-some-code"></a>4. Déployer du code

> [!NOTE]
> `az webapp up` n’est pas pris en charge pendant la préversion publique.

Obtenir un exemple d’application Node.js à l’aide de Git et le déployer à l’aide de [ZIP deploy](deploy-zip.md). Remplacez `<app-name>` par le nom de votre application web.

```azurecli-interactive
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
cd nodejs-docs-hello-world
zip -r package.zip .
az webapp deployment source config-zip --resource-group myResourceGroup --name <app-name> --src package.zip
```

## <a name="5-get-diagnostic-logs-using-log-analytics"></a>5. Récupérer des journaux de diagnostic à l’aide de Log Analytics

> [!NOTE]
> Pour utiliser Log Analytics, vous devez l’avoir activé lors de l’[installation de l’extension App Service](manage-create-arc-environment.md#install-the-app-service-extension). Si vous avez installé l’extension sans Log Analytics, ignorez cette étape.

Accédez à l’[espace de travail Log Analytics configuré avec votre extension App service](manage-create-arc-environment.md#install-the-app-service-extension), puis cliquez sur Journaux dans le volet de navigation gauche. Exécutez l’exemple de requête suivant pour afficher les journaux des dernières 72 heures. Remplacez `<app-name>` par le nom de votre application web. 

```kusto
let StartTime = ago(72h);
let EndTime = now();
AppServiceConsoleLogs_CL
| where TimeGenerated between (StartTime .. EndTime)
| where AppName_s =~ "<app-name>"
```

Les journaux d’application pour toutes les applications hébergées dans votre cluster Kubernetes sont enregistrés dans l’espace de travail Log Analytics de la table de journaux personnalisée nommée `AppServiceConsoleLogs_CL`. 

**Log_s** contient les journaux d’applications pour un App Service donné, et **AppName_s** contient le nom d’application App service. En plus des journaux que vous écrivez à l’aide de votre code d’application, la colonne Log_s contient des journaux sur le démarrage et l’arrêt de conteneur, ainsi que sur les applications de fonction.

Pour en savoir plus sur les requêtes de journal, consultez [Bien démarrer avec Kusto](../azure-monitor/logs/get-started-queries.md).

## <a name="optional-deploy-a-custom-container"></a>(Facultatif) Déployer un conteneur personnalisé

Pour créer une application de conteneur personnalisé, exécutez la commande [az webapp create](/cli/azure/webapp#az_webapp_create) avec `--deployment-container-image-name`. Pour un dépôt privé, ajoutez `--docker-registry-server-user` et `--docker-registry-server-password`.

Par exemple, essayez :

```azurecli-interactive
az webapp create 
    --resource-group myResourceGroup \
    --name <app-name> \
    --custom-location $customLocationId \
    --deployment-container-image-name mcr.microsoft.com/appsvc/node:12-lts
```

<!-- `TODO: currently gets an error but the app is successfully created: "Error occurred in request., RetryError: HTTPSConnectionPool(host='management.azure.com', port=443): Max retries exceeded with url: /subscriptions/62f3ac8c-ca8d-407b-abd8-04c5496b2221/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/cephalin-arctest4/config/appsettings?api-version=2020-12-01 (Caused by ResponseError('too many 500 error responses',))"` -->

Pour mettre à jour l’image une fois l’application créée, consultez [Modifier l’image Docker d’un conteneur personnalisé](configure-custom-container.md?pivots=container-linux#change-the-docker-image-of-a-custom-container)

## <a name="next-steps"></a>Étapes suivantes

- [Configurer une application ASP.NET Core](configure-language-dotnetcore.md?pivots=platform-linux)
- [Configurer une application Node.js](configure-language-nodejs.md?pivots=platform-linux)
- [Configurer une application PHP](configure-language-php.md?pivots=platform-linux)
- [Configurer une application Python Linux](configure-language-python.md)
- [Configurer une application Java](configure-language-java.md?pivots=platform-linux)
- [Configurer une application Ruby Linux](configure-language-ruby.md)
- [Configurer un conteneur personnalisé](configure-custom-container.md?pivots=container-linux)