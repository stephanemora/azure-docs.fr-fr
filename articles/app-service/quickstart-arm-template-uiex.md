---
title: Créer une application App Service avec un modèle Azure Resource Manager
description: Créez votre première application pour Azure App Service en quelques secondes en utilisant un modèle Azure Resource Manager (modèle ARM), qui constitue l’un des nombreux moyens d’effectuer un déploiement sur App Service.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f0412b6e67ff62247feb3f8643553c5a6101ad73
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111892667"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>Démarrage rapide : Créer une application App Service à l’aide d’un modèle ARM

Bien démarrer avec [Azure App Service](overview.md) en déployant une application sur le cloud en utilisant un <abbr title="Un fichier JSON qui définit de façon déclarative une ou plusieurs ressources Azure ainsi que les dépendances entre les ressources déployées. Le modèle peut être utilisé pour déployer les ressources de manière cohérente et répétée.">Modèle ARM</abbr> et [Azure CLI](/cli/azure/get-started-with-azure-cli) dans Cloud Shell. Comme vous utilisez un niveau App Service gratuit, vous pouvez suivre ce guide de démarrage rapide gratuitement. <abbr title="Dans la syntaxe déclarative, vous décrivez le déploiement souhaité sans écrire la séquence de commandes de programmation pour créer le déploiement.">Le modèle utilise la syntaxe déclarative.</abbr>

 Si votre environnement satisfait les prérequis et que vous êtes déjà familiarisé avec l’utilisation des [modèles ARM](../azure-resource-manager/templates/overview.md), sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

::: zone pivot="platform-windows"
[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.web%2Fapp-service-docs-windows%2Fazuredeploy.json)
::: zone-end

::: zone pivot="platform-linux"
[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.web%2Fapp-service-docs-linux%2Fazuredeploy.json)
::: zone-end

<hr/>

## <a name="1-prepare-your-environment"></a>1. Préparation de votre environnement

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<hr/>

## <a name="2-review-the-template"></a>2. Vérifier le modèle

::: zone pivot="platform-windows"
Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/app-service-docs-windows). Il déploie un plan App Service et une application App Service sur Windows.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.web/app-service-docs-windows/azuredeploy.json":::

<details>
<summary>Quelles sont les ressources et les paramètres définis dans le modèle ?</summary>

Deux ressources Azure sont définies dans le modèle :

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms) : créer un plan App Service.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites) : créer une application App Service.

Le tableau suivant détaille les paramètres par défaut et leur description :

| Paramètres | Type    | Valeur par défaut                | Description |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Nom de l’application |
| location   | string  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Région de l’application |
| sku        | string  | "F1"                         | Taille de l’instance (F1 = niveau gratuit) |
| langage   | string  | ".net"                       | Pile du langage de programmation (.net, php, node, html) |
| helloWorld | boolean | False                        | True = déployer l’application « Hello World » |
| repoUrl    | string  | " "                          | Dépôt Git externe (facultatif) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/app-service-docs-linux). Il déploie un plan App Service et une application App Service sur Windows.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.web/app-service-docs-linux/azuredeploy.json":::

Ce modèle comprend des ressources et des paramètres Azure qui sont définis pour des raisons pratiques.

<details>
<summary>Quelles sont les ressources et les paramètres définis dans le modèle ?</summary>

Deux ressources Azure sont définies dans le modèle :

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms) : créer un plan App Service.
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites) : créer une application App Service.

Le tableau suivant détaille les paramètres par défaut et leur description :

| Paramètres | Type    | Valeur par défaut                | Description |
|------------|---------|------------------------------|-------------|
| webAppName | string  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | Nom de l’application |
| location   | string  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | Région de l’application |
| sku        | string  | "F1"                         | Taille de l’instance (F1 = niveau gratuit) |
| linuxFxVersion   | string  | "DOTNETCORE&#124;3.0        | Pile du langage de programmation &#124; Version |
| repoUrl    | string  | " "                          | Dépôt Git externe (facultatif) |

---

</details>

::: zone-end

<hr/>

## <a name="3-deploy-the-template"></a>3. Déployer le modèle

::: zone pivot="platform-windows"
Exécutez le code ci-dessous pour déployer une application .NET Framework sur Windows en utilisant Azure CLI. 

Replace <abbr title="Les caractères valides sont `a-z`, `0-9` et `-`.">`<app-name>`</abbr> par un nom d’application global unique. Pour découvrir d’autres <abbr title="Vous pouvez également utiliser le portail Azure, Azure PowerShell ou l’API REST.">méthodes de déploiement</abbr>, consultez [Déployer des modèles](../azure-resource-manager/templates/deploy-powershell.md). Vous trouverez d’autres exemples de [modèles Azure App Service ici](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites).

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/app-service-docs-windows/azuredeploy.json"
```
::: zone-end
::: zone pivot="platform-linux"
Exécutez le code ci-dessous pour créer une application Python sur Linux. 

Replace <abbr title="Les caractères valides sont `a-z`, `0-9` et `-`.">`<app-name>`</abbr> par un nom d’application global unique.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/app-service-docs-linux/azuredeploy.json"
```
::: zone-end

<details>
<summary>Que fait le code ?</summary>
<p>Les commandes effectuent les actions suivantes :</p>
<ul>
<li>Créer un <abbr title="Conteneur logique pour des ressources Azure associées que vous pouvez gérer en tant qu’unité.">resource group</abbr>.</li>
<li>Créer un <abbr title="Plan qui spécifie l’emplacement, la taille et les fonctionnalités de la batterie de serveurs web qui héberge votre application.">Plan App Service</abbr>.</li>
<li><a href="/cli/azure/webapp#az_webapp_create">Créez une <abbr title="Représentation de votre application web, qui contient le code de votre application, les noms d’hôte DNS, les certificats et les ressources associées.">application App Service</abbr></a> avec le nom spécifié.</li>
</ul>
</details>

::: zone pivot="platform-windows"
<details>
<summary>Comment déployer une autre pile de langages ?</summary>
Pour déployer une autre pile de langages, mettez à jour <abbr title="Ce modèle est compatible avec .NET Core, .NET Framework, PHP, Node.js et les applications HTML statiques.">paramètre de langage</abbr> avec les valeurs appropriées. Pour Java, consultez <a href="/azure/app-service/quickstart-java-uiex">Créer une application Java</a>.

| Paramètres | Type    | Valeur par défaut                | Description |
|------------|---------|------------------------------|-------------|
| langage   | string  | ".net"                       | Pile du langage de programmation (.net, php, node, html) |

---

</details>
::: zone-end
::: zone pivot="platform-linux"
<details>
<summary>Comment déployer une autre pile de langages ?</summary>
Pour déployer une autre pile de langage, mettez à jour `linuxFxVersion` avec les valeurs appropriées. Des exemples sont présentés ci-dessous. Pour afficher les versions actuelles, exécutez la commande suivante dans Cloud Shell : `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Langage    | Exemple :                                               |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.JS** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---

</details>
::: zone-end

<hr/>

## <a name="4-validate-the-deployment"></a>4. Valider le déploiement

Accédez à `http://<app_name>.azurewebsites.net/` et vérifiez qu’il a été créé.

<hr/>

## <a name="5-clean-up-resources"></a>5. Nettoyer les ressources

Quand il n’est plus nécessaire, [supprimez le groupe de ressources](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group).

<hr/>

## <a name="next-steps"></a>Étapes suivantes

- [Déployer à partir de Git local](deploy-local-git.md)
- [Build a .NET Core and SQL Database web app in Azure App Service](tutorial-dotnetcore-sqldb-app.md) (Créer une application web .NET Core et SQL Database dans Azure App Service)
- Python avec Postgres
- [PHP avec MySQL](tutorial-php-mysql-app.md)
- [Se connecter à Azure SQL Database à l’aide de Java](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)
- [Mapper un domaine personnalisé](app-service-web-tutorial-custom-domain-uiex.md)
