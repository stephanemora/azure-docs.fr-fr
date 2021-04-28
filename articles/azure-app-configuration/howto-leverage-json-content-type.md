---
title: Utiliser le type de contenu JSON pour les valeurs de clé
titleSuffix: Azure App Configuration
description: Découvrir comment utiliser le type de contenu JSON pour les valeurs de clés
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: how-to
ms.date: 08/03/2020
ms.author: avgupta
ms.openlocfilehash: 2be560d16b133696c520b8872406ff6a36b0087b
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108137108"
---
# <a name="leverage-content-type-to-store-json-key-values-in-app-configuration"></a>Tirer parti du type de contenu pour stocker des valeurs de clés JSON dans App Configuration

Les données sont stockées dans la configuration de l’application en tant que valeurs de clés, où les valeurs sont traitées par défaut en tant que type de chaîne. Toutefois, vous pouvez spécifier un type personnalisé en tirant parti de la propriété type de contenu associée à chaque clé-valeur, afin que vous puissiez conserver le type d’origine de vos données ou faire en sorte que votre application se comporte différemment en fonction du type de contenu.


## <a name="overview"></a>Vue d’ensemble

Dans App Configuration, vous pouvez utiliser le type de média JSON comme type de contenu de vos valeurs de clés pour bénéficier d’avantages tels que :
- **Gestion des données plus simple** : La gestion des valeurs de clés, comme les tableaux, est beaucoup plus facile dans le Portail Azure.
- **Exportation de données améliorée** : Les types primitifs, les tableaux et les objets JSON sont conservés lors de l’exportation des données.
- **Support natif avec le fournisseur App Configuration** : Les valeurs de clés avec le type de contenu JSON fonctionnent correctement quand elles sont consommées par les bibliothèques du fournisseur App Configuration dans vos applications.

#### <a name="valid-json-content-type"></a>Type de contenu JSON valide

Les types de médias, tels que définis [ici](https://www.iana.org/assignments/media-types/media-types.xhtml), peuvent être attribués au type de contenu associé à chaque clé-valeur.
Un type de média se compose d’un type et d’un sous-type. Si le type est `application` et que le sous-type (ou suffixe) est `json`, le type de média sera considéré comme un type de contenu JSON valide.
Voici quelques exemples de types de contenu JSON valides :

- application/json
- application/activity+json
- application/vnd.foobar+json;charset=utf-8

#### <a name="valid-json-values"></a>Valeurs JSON valides

Quand une clé-valeur a un type de contenu JSON, sa valeur doit être au format JSON valide pour que les clients la traitent correctement. Dans le cas contraire, les clients peuvent échouer ou revenir en arrière et la traiter en tant que format de chaîne.
Exemples de valeurs JSON valides :

- « John Doe »
- 723
- false
- null
- « 2020-01-01T12:34:56.789Z »
- [1, 2, 3, 4]
- {"ObjectSetting":{"Targeting":{"Default":true,"Level":"Information"}}}

> [!NOTE]
> Pour le reste de cet article, toute clé-valeur dans App Configuration qui possède un type de contenu JSON valide et une valeur JSON valide sera appelée **clé-valeur JSON**. 

Dans ce tutoriel, vous apprendrez à :
> [!div class="checklist"]
> * Créer des valeurs de clés JSON dans App Configuration.
> * Importer des valeurs de clés JSON à partir d’un fichier JSON.
> * Exporter des valeurs de clés JSON à partir d’un fichier JSON.
> * Utiliser des valeurs de clé JSON dans vos applications.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Cet tutoriel nécessite la version 2.10.0 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-an-app-configuration-store"></a>Créer un magasin App Configuration

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]


## <a name="create-json-key-values-in-app-configuration"></a>Créer des valeurs de clés JSON dans App Configuration

Les valeurs de clé JSON peuvent être créées à l’aide du Portail Azure, d’Azure CLI ou d’une importation à partir d’un fichier JSON. Dans cette section, vous trouverez des instructions sur la création des mêmes valeurs de clés JSON à l’aide des trois méthodes.

### <a name="create-json-key-values-using-azure-portal"></a>Créer des valeurs de clés JSON à l’aide du Portail Azure

Parcourez votre magasin App Configuration et sélectionnez **Explorateur de configurations** > **Créer** > **Clé-valeur** pour ajouter les paires clé-valeur suivantes :

| Clé | Valeur | Type de contenu |
|---|---|---|
| Settings:BackgroundColor | « Vert » | application/json |
| Settings:FontSize | 24 | application/json |
| Settings:UseDefaultRouting | false | application/json |
| Settings:BlockedUsers | null | application/json |
| Settings:ReleaseDate | « 2020-08-04T12:34:56.789Z » | application/json |
| Settings:RolloutPercentage | [25,50,75,100] | application/json |
| Settings:Logging | {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}} | application/json |

Laissez **Étiquette** vide et sélectionnez **Appliquer**.

### <a name="create-json-key-values-using-azure-cli"></a>Créer des valeurs de clés JSON à l’aide d’Azure CLI

Les commandes suivantes créent des valeurs de clés JSON dans votre magasin App Configuration. Remplacez `<appconfig_name>` par le nom de votre magasin App Configuration.

```azurecli-interactive
appConfigName=<appconfig_name>
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BackgroundColor --value \"Green\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:FontSize --value 24
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:UseDefaultRouting --value false
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BlockedUsers --value null
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:ReleaseDate --value \"2020-08-04T12:34:56.789Z\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:RolloutPercentage --value [25,50,75,100]
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:Logging --value {\"Test\":{\"Level\":\"Debug\"},\"Prod\":{\"Level\":\"Warning\"}}
```

> [!IMPORTANT]
> Si vous utilisez Azure CLI ou Azure Cloud Shell pour créer des valeurs de clés JSON, la valeur fournie doit être une chaîne JSON placée dans une séquence d’échappement.

### <a name="import-json-key-values-from-a-file"></a>Importer des valeurs de clés JSON à partir d’un fichier

Créez un fichier JSON nommé `Import.json` avec le contenu suivant et importez-le en tant que valeurs de clés dans App Configuration :

```json
{
  "Settings": {
    "BackgroundColor": "Green",
    "BlockedUsers": null,
    "FontSize": 24,
    "Logging": {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}},
    "ReleaseDate": "2020-08-04T12:34:56.789Z",
    "RolloutPercentage": [25,50,75,100],
    "UseDefaultRouting": false
  }
}
```

```azurecli-interactive
az appconfig kv import -s file --format json --path "~/Import.json" --content-type "application/json" --separator : --depth 2
```

> [!Note]
> L’argument `--depth` est utilisé pour aplatir des données hiérarchiques d’un fichier dans des paires clé-valeur. Dans ce tutoriel, une profondeur est spécifiée pour démontrer que vous pouvez également stocker des objets JSON en tant que valeurs dans App Configuration. Si la profondeur n’est pas spécifiée, les objets JSON sont aplatis au niveau le plus profond par défaut.

Les valeurs de clés JSON que vous avez créées doivent ressembler à ceci dans App Configuration :

![Magasin de configuration contenant des valeurs de clés JSON](./media/create-json-settings.png)


## <a name="export-json-key-values-to-a-file"></a>Exporter des valeurs de clés JSON à partir d’un fichier

L’un des principaux avantages de l’utilisation des valeurs de clés JSON est la possibilité de conserver le type de données d’origine de vos valeurs lors de l’exportation. Si une clé-valeur dans App Configuration n’a pas de type de contenu JSON, sa valeur sera traitée comme une chaîne. 

Considérez ces valeurs de clés sans type de contenu JSON :

| Clé | Valeur | Type de contenu |
|---|---|---|
| Settings:FontSize | 24 | |
| Settings:UseDefaultRouting | false | |

Lorsque vous exportez ces valeurs de clés vers un fichier JSON, les valeurs sont exportées sous forme de chaînes :
```json
{
  "Settings": {
    "FontSize": "24",
    "UseDefaultRouting": "false"
  }
}
```

Toutefois, lorsque vous exportez des valeurs de clés JSON dans un fichier, toutes les valeurs conservent leur type de données d’origine. Pour vérifier cela, exportez les valeurs de clés de la configuration de votre application vers un fichier JSON. Vous verrez que le fichier exporté a le même contenu que le fichier `Import.json` que vous avez importé précédemment.

```azurecli-interactive
az appconfig kv export -d file --format json --path "~/Export.json" --separator :
```

> [!NOTE]
> Si votre magasin App Configuration a des valeurs de clés sans type de contenu JSON, elles seront également exportées vers le même fichier au format de chaîne.


## <a name="consuming-json-key-values-in-applications"></a>Utiliser des valeurs de clés JSON dans des applications

Le moyen le plus simple d’utiliser des valeurs de clés JSON dans votre application est d’utiliser les bibliothèques du fournisseur App Configuration. Avec les bibliothèques du fournisseur, vous n’avez pas besoin d’implémenter une gestion spéciale des valeurs de clés JSON dans votre application. Elles seront analysées et converties pour correspondre à la configuration native de votre application.

Par exemple, si vous avez la paire clé-valeur suivante dans App Configuration :

| Clé | Valeur | Type de contenu |
|---|---|---|
| Paramètres | {"FontSize":24,"UseDefaultRouting":false} | application/json |

La configuration de votre application .NET comprendra les paires clé-valeur suivantes :

| Clé | Valeur |
|---|---|
| Settings:FontSize | 24 |
| Settings:UseDefaultRouting | false |

Vous pouvez accéder directement aux nouvelles clés, ou choisir de [lier des valeurs de configuration à des instances d’objets .NET](/aspnet/core/fundamentals/configuration/#bind-hierarchical-configuration-data-using-the-options-pattern).


> [!Important]
> Le support natif des valeurs de clés JSON est disponible dans la version 4.0.0 (ou ultérieure) du fournisseur de la configuration .NET. Pour plus d’informations, consultez la section [*Étapes suivantes*](#next-steps).

Si vous utilisez le Kit de développement logiciel (SDK) ou l’API REST pour lire les valeurs de clés d’App Configuration, en fonction du type de contenu, votre application est responsable de l’analyse de la valeur d’une paire clé-valeur JSON.


## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment utiliser les valeurs de clés JSON dans votre magasin App Configuration, créez une application pour utiliser ces valeurs de clés :

* [Démarrage rapide ASP.NET Core](./quickstart-aspnet-core-app.md)
    * Condition préalable : Package [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) v4.0.0 ou version ultérieure.

* [Démarrage rapide .NET Core](./quickstart-dotnet-core-app.md)
    * Condition préalable : Package [Microsoft.Extensions.Configuration.AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration) v4.0.0 ou version ultérieure.
