---
title: Développer et exécuter des fonctions Azure Functions en local
description: Apprenez à coder et à tester des fonctions Azure Functions sur votre ordinateur local avant de les exécuter dans Azure Functions.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 9a1c760eb2630ddf1c6175625b53b86db9cabc34
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562416"
---
# <a name="code-and-test-azure-functions-locally"></a>Coder et tester Azure Functions localement

Bien qu’il soit possible de développer et tester Azure Functions dans le [Azure portal], de nombreux développeurs préfèrent une expérience de développement local. Functions facilite l’utilisation de votre éditeur de code et de vos outils de développement préférés pour développer et tester des fonctions sur votre ordinateur local. Vos fonctions locales peuvent être connectées aux services Azure actifs, et vous pouvez les déboguer sur votre ordinateur local à l’aide du runtime Functions complet.

Cet article fournit des liens vers des environnements de développement spécifiques pour votre langage par défaut. Il fournit également des conseils partagés pour le développement local, comme l’utilisation du [fichier local.settings.json](#local-settings-file). 

## <a name="local-development-environments"></a>Environnements de développement local

La façon dont vous développez des fonctions sur votre ordinateur local dépend de votre [langage](supported-languages.md) et de vos préférences d’outils. Les environnements du tableau suivant prennent en charge le développement local :

|Environnement                              |Languages         |Description|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C# (bibliothèque de classes)](functions-dotnet-class-library.md)<br/>[Processus isolé C# (.NET 5.0)](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)<br/>[JavaScript](functions-reference-node.md)<br/>[PowerShell](./create-first-function-vs-code-powershell.md)<br/>[Python](functions-reference-python.md) | L’[extension Azure Functions pour VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) ajoute la prise en charge de Functions à VS Code. Requiert les outils de base. Prend en charge le développement sur Linux, macOS et Windows, lorsque vous utilisez la version 2.x des outils de base. Pour en savoir plus, voir [Créer votre première fonction à l’aide de Visual Studio Code](./create-first-function-vs-code-csharp.md). |
| [Invite de commandes terminal](functions-run-local.md) | [C# (bibliothèque de classes)](functions-dotnet-class-library.md)<br/>[Processus isolé C# (.NET 5.0)](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-cli)<br/>[JavaScript](functions-reference-node.md)<br/>[PowerShell](functions-reference-powershell.md)<br/>[Python](functions-reference-python.md) | [Azure Functions Core Tools] fournit le runtime et les modèles principaux de création de fonctions, ce qui permet le développement local. La version 2.x prend en charge le développement sur Linux, macOS et Windows. Tous les environnements s’appuient sur les outils de base pour le runtime Functions local. |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (bibliothèque de classes)](functions-dotnet-class-library.md)<br/>[Processus isolé C# (.NET 5.0)](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vs) | Les outils Azure Functions sont inclus dans la charge de travail de **développement Azure** de [Visual Studio 2019](https://www.visualstudio.com/vs/) et des versions ultérieures. Vous permet de compiler les fonctions dans une bibliothèque de classes et de publier le fichier .dll dans Azure. Inclut les principaux outils pour effectuer des test en local. Pour en savoir plus, voir [Développer Azure Functions à l’aide de Visual Studio](functions-develop-vs.md). |
| [Maven](./create-first-function-cli-java.md) (divers) | [Java](functions-reference-java.md) | L’archétype Maven prend en charge les outils de base pour permettre le développement de fonctions Java. La version 2.x prend en charge le développement sur Linux, macOS et Windows. Pour en savoir plus, consultez [Créer votre première fonction dans Azure avec Java et Maven](./create-first-function-cli-java.md). Prend également en charge le développement en utilisant [Eclipse](functions-create-maven-eclipse.md) et [IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Chacun de ces environnements de développement local vous permet de créer des projets d’application de fonction et d’utiliser des modèles Functions prédéfinis pour créer de nouvelles fonctions. Ils intègrent les outils principaux afin que vous puissiez tester et déboguer vos fonctions selon le runtime Functions réel sur votre propre ordinateur comme vous le feriez pour toute autre application. Vous pouvez également publier votre projet d’application de fonction à partir d’un de ces environnements sur Azure.

## <a name="local-settings-file"></a>Fichier de paramètres locaux

Le fichier local.settings.json stocke des paramètres d’application et des paramètres utilisés par des outils de développement locaux. Les paramètres dans le fichier local.settings.json sont uniquement utilisés lorsque vous exécutez votre projet localement. 

> [!IMPORTANT]  
> Étant donné que le fichier local.settings.json peut contenir des secrets, comme des chaînes de connexion, vous ne devez jamais le stocker dans un référentiel à distance. Les outils qui prennent en charge Functions permettent de synchroniser les paramètres du fichier local.settings.json avec les [paramètres d’application](functions-how-to-use-azure-function-app-settings.md#settings) dans l’application de fonction où votre projet est déployé.

Le fichier de paramètres locaux possède la structure suivante :

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
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
| **`IsEncrypted`** | Lorsque ce paramètre est défini sur `true`, toutes les valeurs sont chiffrées à l’aide d’une clé d’ordinateur local. Utilisé avec les commandes `func settings`. La valeur par défaut est `false`. Vous voudrez peut-être chiffrer le fichier local.settings.json sur votre ordinateur local lorsqu’il contient des secrets, tels que des chaînes de connexion de service. L’hôte déchiffre automatiquement les paramètres quand il s’exécute. Utilisez la commande `func settings decrypt` avant d’essayer de lire les paramètres chiffrés localement. |
| **`Values`** | Collection de paramètres d’application utilisés lorsqu’un projet est exécuté localement. Ces paires clé-valeur (chaîne-chaîne) correspondent aux paramètres d’application dans votre Function App dans Azure, tels que [`AzureWebJobsStorage`]. Plusieurs déclencheurs et liaisons ont une propriété qui fait référence à un paramètre d’application de chaîne de connexion, par exemple `Connection` pour le [déclencheur de stockage blob](functions-bindings-storage-blob-trigger.md#configuration). Pour ces propriétés, vous avez besoin d’un paramètre d’application défini dans le tableau `Values`. Consultez le tableau suivant pour une liste des paramètres couramment utilisés. <br/>Les valeurs doivent être des chaînes et non des objets JSON ou des tableaux. Les noms de paramètres ne peuvent pas contenir le signe deux points (`:`) ou un trait de soulignement double (`__`). Les caractères de soulignement double sont réservés par le runtime, et le signe deux-points est réservé pour la prise en charge de l’[injection de dépendances](functions-dotnet-dependency-injection.md#working-with-options-and-settings). |
| **`Host`** | Les paramètres de cette section personnalisent le processus hôte Functions lorsque vous exécutez localement des projets. Ces paramètres sont séparés des paramètres host.json, qui s’appliquent également lorsque vous exécutez localement des projets dans Azure. |
| **`LocalHttpPort`** | Définit le port par défaut utilisé lors de l’exécution de l’hôte Functions local (`func host start` et `func run`). `--port`L’option de ligne de commande est prioritaire sur ce paramètre. Par exemple, lors de l’exécution dans l’IDE Visual Studio, vous pouvez changer le numéro de port en accédant à la fenêtre « Propriétés du projet -> Débogage » et en spécifiant explicitement le numéro de port dans une commande `host start --port <your-port-number>` qui peut être fournie dans le champ « Arguments de l’application ». |
| **`CORS`** | Définit les origines autorisées pour [cross-origin resource sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Les origines sont fournies sous la forme d’une liste séparée par des virgules, sans espaces. La valeur de caractère générique (\*) est prise en charge, ce qui autorise les demandes à partir de toute origine. |
| **`CORSCredentials`** |  Lorsque qu’il est défini sur `true`, autorise `withCredentials` les requêtes. |
| **`ConnectionStrings`** | Une collection. N’utilisez pas cette collection pour les chaînes de connexion utilisées par vos liaisons de fonction. Cette collection est utilisée seulement par les infrastructures qui obtiennent généralement les chaînes de connexion à partir de la `ConnectionStrings`section d’un fichier de configuration, comme [Entity Framework](/ef/ef6/). Les chaînes de connexion dans cet objet sont ajoutées à l’environnement avec le type de fournisseur [System.Data.SqlClient](/dotnet/api/system.data.sqlclient). Les éléments de cette collection ne sont pas publiés sur Azure avec d’autres paramètres d’application. Vous devez explicitement ajouter ces valeurs à la collection `Connection strings` des paramètres de Function App. Si vous créez un [`SqlConnection`](/dotnet/api/system.data.sqlclient.sqlconnection) dans votre code de fonction, vous devez stocker la valeur de la chaîne de connexion et vos autres connexions dans **Paramètres d’application** dans le portail. |

Les paramètres d’application suivants peuvent être inclus dans le tableau **`Values`** lors d’une exécution locale :

| Paramètre | Valeurs | Description |
|-----|-----|-----|
|**`AzureWebJobsStorage`**| Chaîne de connexion de compte de stockage ou<br/>`UseDevelopmentStorage=true`| Contient la chaîne de connexion pour un compte de stockage Azure. Obligatoire lors de l’utilisation de déclencheurs autres que HTTP. Pour plus d’informations, consultez les informations de référence sur [`AzureWebJobsStorage`].<br/>Lorsque vous installez l’[émulateur de stockage Azure](../storage/common/storage-use-emulator.md) localement et définissez [`AzureWebJobsStorage`] sur `UseDevelopmentStorage=true`, Core Tools utilise l’émulateur. L’émulateur est utile lors du développement, mais vous devez le tester avec une connexion de stockage réelle avant le déploiement.| 
|**`AzureWebJobs.<FUNCTION_NAME>.Disabled`**| `true`\|`false` | Pour désactiver une fonction qui s’exécute localement, ajoutez `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"` à la collection, où `<FUNCTION_NAME>` est le nom de la fonction. Pour en savoir plus, consultez [Guide pratique pour désactiver des fonctions dans Azure Functions](disable-function.md#localsettingsjson) |
|**`FUNCTIONS_WORKER_RUNTIME`** | `dotnet`<br/>`node`<br/>`java`<br/>`powershell`<br/>`python`| Indique le langage ciblé du runtime Functions. Obligatoire pour la version 2.x et ultérieure du runtime Functions. Ce paramètre est généré pour votre projet par Core Tools. Pour plus d’informations, consultez les informations de référence sur [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime).|
| **`FUNCTIONS_WORKER_RUNTIME_VERSION`** | `~7` |Indique que PowerShell 7 doit être utilisé lors d’une exécution locale. S’il n’est pas défini, PowerShell Core 6 est utilisé. Ce paramètre est utilisé seulement lors d’une exécution locale. Lors d’une exécution dans Azure, la version du runtime PowerShell est déterminée par le paramètre de configuration du site `powerShellVersion`, qui peut être [défini dans le portail](functions-reference-powershell.md#changing-the-powershell-version). | 

## <a name="next-steps"></a>Étapes suivantes

+ Pour en savoir plus sur le développement local de fonctions C# précompilées à l’aide de Visual Studio 2019, consultez [Développer Azure Functions à l’aide de Visual Studio](functions-develop-vs.md).
+ Pour en savoir plus sur le développement local de fonctions à l’aide de VS Code sur un ordinateur Mac, Linux ou Windows, consultez l’article de prise en main de Visual Studio Code pour votre langage préféré :
    + [Bibliothèque de classes C#](create-first-function-vs-code-csharp.md)
    + [Processus isolé C# (.NET 5.0)](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)
    + [Java](create-first-function-vs-code-java.md)
    + [JavaScript](create-first-function-vs-code-node.md)
    + [PowerShell](create-first-function-vs-code-powershell.md)
    + [Python](create-first-function-vs-code-python.md)
    + [TypeScript](create-first-function-vs-code-typescript.md)
+ Pour en savoir plus sur le développement de fonctions à partir de l’invite de commandes ou d’un terminal, consultez [Coder et tester Azure Functions localement](functions-run-local.md).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure portal]: https://portal.azure.com
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[AzureWebJobsStorage]: functions-app-settings.md#azurewebjobsstorage
