---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205671"
---
## <a name="local-settings-file"></a>Fichier de paramètres locaux

Le fichier local.settings.json stocke des paramètres d’application, des chaînes de connexion et des paramètres utilisés par des outils de développement locaux. Les paramètres dans le fichier local.settings.json sont uniquement utilisés lorsque vous exécuter les projets localement. Le fichier de paramètres locaux possède la structure suivante :

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

Ces paramètres sont pris en charge lorsque vous exécutez des projets localement :

| Paramètre      | Description                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | Lorsque ce paramètre est défini sur `true`, toutes les valeurs sont chiffrées à l’aide d’une clé d’ordinateur local. Utilisé avec les commandes `func settings`. La valeur par défaut est `false`. |
| **`Values`** | Tableau des paramètres d’application et des chaînes de connexion utilisés lors de l’exécution locale d’un projet. Ces paires clé-valeur (chaîne-chaîne) correspondent aux paramètres d’application dans votre Function App dans Azure, tels que [`AzureWebJobsStorage`]. Plusieurs déclencheurs et liaisons ont une propriété qui fait référence à un paramètre d’application de chaîne de connexion, par exemple `Connection` pour le [déclencheur de stockage blob](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration). Pour ces propriétés, vous avez besoin d’un paramètre d’application défini dans le tableau `Values`. <br/>[`AzureWebJobsStorage`] est un paramètre d’application requis pour les déclencheurs autres que HTTP. <br/>Les versions 2.x et ultérieures du runtime Functions nécessitent le paramètre [`FUNCTIONS_WORKER_RUNTIME`], qui est généré pour votre projet par Core Tools. <br/> Lorsque vous installez [l’émulateur de stockage Azure](../articles/storage/common/storage-use-emulator.md) localement et définissez [`AzureWebJobsStorage`] sur `UseDevelopmentStorage=true`, et Core Tools utilise l’émulateur. L’émulateur est utile lors du développement, mais vous devez le tester avec une connexion de stockage réelle avant le déploiement.<br/> Les valeurs doivent être des chaînes et non des objets JSON ou des tableaux. Les noms de paramètres ne peuvent pas contenir le signe deux points (`:`) ou un trait de soulignement double (`__`). Ces caractères sont réservés par le runtime.  |
| **`Host`** | Les paramètres de cette section personnalisent le processus hôte Functions lorsque vous exécutez localement des projets. Ces paramètres sont séparés des paramètres host.json, qui s’appliquent également lorsque vous exécutez localement des projets dans Azure. |
| **`LocalHttpPort`** | Définit le port par défaut utilisé lors de l’exécution de l’hôte Functions local (`func host start` et `func run`). `--port`L’option de ligne de commande est prioritaire sur ce paramètre. |
| **`CORS`** | Définit les origines autorisées pour [cross-origin resource sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Les origines sont fournies sous la forme d’une liste séparée par des virgules, sans espaces. La valeur de caractère générique (\*) est prise en charge, ce qui autorise les demandes à partir de toute origine. |
| **`CORSCredentials`** |  Lorsque qu’il est défini sur `true`, autorise `withCredentials` les requêtes. |
| **`ConnectionStrings`** | Une collection. N’utilisez pas cette collection pour les chaînes de connexion utilisées par vos liaisons de fonction. Cette collection est utilisée seulement par les infrastructures qui obtiennent généralement les chaînes de connexion à partir de la `ConnectionStrings`section d’un fichier de configuration, comme [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Les chaînes de connexion dans cet objet sont ajoutées à l’environnement avec le type de fournisseur [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Les éléments de cette collection ne sont pas publiés sur Azure avec d’autres paramètres d’application. Vous devez explicitement ajouter ces valeurs à la collection `Connection strings` des paramètres de Function App. Si vous créez un [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) dans votre code de fonction, vous devez stocker la valeur de la chaîne de connexion et vos autres connexions dans **Paramètres d’application** dans le portail. |

[AzureWebJobsStorage]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
