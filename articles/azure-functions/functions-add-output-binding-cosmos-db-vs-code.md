---
title: Connecter Azure Functions à Azure Cosmos DB avec Visual Studio Code
description: Découvrez comment connecter Azure Functions à un compte Azure Cosmos DB en ajoutant une liaison de sortie à votre projet Visual Studio Code.
author: ThomasWeiss
ms.date: 03/23/2021
ms.topic: quickstart
ms.author: thweiss
zone_pivot_groups: programming-languages-set-functions-temp
ms.openlocfilehash: 0a0c63ee54699185bcd02104b1a3f4d0070ea808
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023246"
---
# <a name="connect-azure-functions-to-azure-cosmos-db-using-visual-studio-code"></a>Connecter Azure Functions à Azure Cosmos DB avec Visual Studio Code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Cet article vous montre comment utiliser Visual Studio Code pour connecter [Azure Cosmos DB](../cosmos-db/introduction.md) à la fonction que vous avez créée dans le guide de démarrage rapide précédent. La liaison de sortie que vous ajoutez à cette fonction écrit des données provenant de la requête HTTP dans un document JSON stocké dans un conteneur Azure Cosmos DB. 

::: zone pivot="programming-language-csharp"
Avant de commencer, vous devez suivre le guide de [Démarrage rapide : Créer une fonction C# dans Azure à l’aide de Visual Studio code](create-first-function-vs-code-csharp.md). Si vous avez déjà nettoyé les ressources à la fin de cet article, suivez à nouveau les étapes pour recréer l’application Functions et les ressources associées dans Azure.
::: zone-end
::: zone pivot="programming-language-javascript"  
Avant de commencer, vous devez suivre le guide de [Démarrage rapide : Créer une fonction JavaScript dans Azure à l’aide de Visual Studio code](create-first-function-vs-code-node.md). Si vous avez déjà nettoyé les ressources à la fin de cet article, suivez à nouveau les étapes pour recréer l’application Functions et les ressources associées dans Azure.  
::: zone-end

## <a name="configure-your-environment"></a>Configurer votre environnement

Avant de commencer, veillez à installer l’[extension Azure Databases](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb) pour Visual Studio Code.

## <a name="create-your-azure-cosmos-db-account"></a>Créer votre compte Azure Cosmos DB

> [!IMPORTANT]
> [Azure Cosmos DB serverless](../cosmos-db/serverless.md) est maintenant disponible en préversion. Ce mode basé sur la consommation fait d’Azure Cosmos DB une option sérieuse pour les charges de travail serverless. Pour utiliser Azure Cosmos DB en mode serverless, choisissez **Serverless** comme **Mode de capacité** lors de la création de votre compte.

1. Dans une nouvelle fenêtre du navigateur, connectez-vous au [portail Azure](https://portal.azure.com/).

2. Cliquez sur **Créer une ressource** > **Bases de données** > **Azure Cosmos DB**.
   
    :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png" alt-text="Volet Bases de données du portail Azure" border="true":::

3. Dans la page **Créer un compte Azure Cosmos DB**, entrez les paramètres de votre nouveau compte Azure Cosmos DB. 
 
    Paramètre|Valeur|Description
    ---|---|---
    Abonnement|*Votre abonnement*|Choisissez l’abonnement Azure dans lequel vous avez créé votre application de fonction dans l’[article précédent](./create-first-function-vs-code-csharp.md).
    Groupe de ressources|*Votre groupe de ressources*|Choisissez le groupe de ressources dans lequel vous avez créé votre application de fonction dans l’[article précédent](./create-first-function-vs-code-csharp.md).
    Nom du compte|*Entrez un nom unique*|Entrez un nom unique pour identifier votre compte Azure Cosmos DB.<br><br>Le nom peut contenir uniquement des lettres minuscules, des chiffres et des traits d’union (-), et doit comporter entre 3 et 31 caractères.
    API|Core (SQL)|Sélectionnez **Core (SQL)** pour créer une base de données orientée document que vous pouvez interroger en utilisant une syntaxe SQL. [En savoir plus sur l’API SQL Azure Cosmos DB](../cosmos-db/introduction.md).|
    Emplacement|*Sélectionnez la région la plus proche de votre localisation*|Sélectionnez la zone géographique dans laquelle héberger votre compte Azure Cosmos DB. Utilisez la région la plus proche de vos utilisateurs ou de vous-même pour obtenir l’accès le plus rapide possible à vos données.
    Mode de capacité|Débit serverless ou provisionné|Sélectionnez **serverless** pour créer un compte en mode [serverless](../cosmos-db/serverless.md). Sélectionnez **Débit approvisionné** pour créer un compte dans mode de [débit approvisionné](../cosmos-db/set-throughput.md).<br><br>Choisissez **Serverless** si vous démarrez avec Azure Cosmos DB.

4. Cliquez sur **Vérifier + créer**. Vous pouvez ignorer les sections **Réseau** et **Étiquettes**. 

5. Examinez les informations récapitulatives et cliquez sur **Créer**. 

6. Attendez que votre compte Azure Cosmos DB soit créé, puis sélectionnez **Accéder à la ressource**.

    :::image type="content" source="../cosmos-db/media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="La création du compte Azure Cosmos DB est terminée" border="true":::

## <a name="create-an-azure-cosmos-db-database-and-container"></a>Créez une base de données et un conteneur Azure Cosmos DB

À partir de votre compte Azure Cosmos DB, sélectionnez **Explorateur de données**, puis **Nouveau conteneur**. Créez une base de données nommée *my-database*, un conteneur nommé *my-container* et choisissez `/id` comme [clé de partition](../cosmos-db/partitioning-overview.md).

:::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-container.png" alt-text="Création d’un conteneur Azure Cosmos DB à partir du portail Azure" border="true":::

## <a name="update-your-function-app-settings"></a>Mettre à jour les paramètres de votre application de fonction

Dans l’[article du guide de démarrage rapide précédent](./create-first-function-vs-code-csharp.md), vous avez créé une application de fonction dans Azure. Dans cet article, vous mettez à jour votre application de fonction pour écrire des documents JSON dans le conteneur Azure Cosmos DB que vous avez créé plus haut. Pour vous connecter à votre compte Azure Cosmos DB, vous devez ajouter sa chaîne de connexion aux paramètres de votre application. Vous téléchargez ensuite le nouveau paramètre dans votre fichier local.settings.json, afin de pouvoir vous connecter à votre compte Azure Cosmos DB lors de l’exécution locale.

1. Dans Visual Studio Code, recherchez le compte Azure Cosmos DB que vous venez de créer. Cliquez avec le bouton droit sur son nom, puis sélectionnez **Copier la chaîne de connexion**.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/copy-connection-string.png" alt-text="Copie de la chaîne de connexion Azure Cosmos DB" border="true":::

1. Appuyez sur <kbd>F1</kbd> pour ouvrir la palette de commandes, puis recherchez et exécutez la commande `Azure Functions: Add New Setting...`.

1. Choisissez l’application de fonction que vous avez créée dans l’article précédent. Quand vous y êtes invité, indiquez les informations suivantes :

    + **Entrez le nom du nouveau paramètre d’application** : tapez `CosmosDbConnectionString`.

    + **Entrez une valeur pour « CosmosDbConnectionString »**  : collez la chaîne de connexion de votre compte Azure Cosmos DB, telle qu’elle a été copiée.

1. Réappuyez sur <kbd>F1</kbd> pour ouvrir la palette de commandes, puis recherchez et exécutez la commande `Azure Functions: Download Remote Settings...`. 

1. Choisissez l’application de fonction que vous avez créée dans l’article précédent. Sélectionnez **Oui pour tout** pour remplacer les paramètres locaux existants. 

## <a name="register-binding-extensions"></a>Inscrire des extensions de liaison

Comme vous utilisez une liaison de sortie d’Azure Cosmos DB, vous devez disposer de l’extension de liaisons correspondante avant d’exécuter le projet. 

::: zone pivot="programming-language-csharp"

À l’exception des déclencheurs HTTP et de minuterie, les liaisons sont implémentées sous la forme de packages d’extension. Exécutez la commande [dotnet add package](/dotnet/core/tools/dotnet-add-package) suivante dans la fenêtre de terminal pour ajouter le package d’extension de Stockage à votre projet.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB
```

::: zone-end

::: zone pivot="programming-language-javascript"

Votre projet a été configuré pour utiliser des [bundles d’extension](functions-bindings-register.md#extension-bundles), et installe automatiquement un ensemble prédéfini de packages d’extension. 

L’utilisation des bundles d’extension est activée dans le fichier host.json, à la racine du projet, et se présente de la façon suivante :

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

Vous pouvez maintenant ajouter la liaison d’Azure Cosmos DB à votre projet.

## <a name="add-an-output-binding"></a>Ajouter une liaison de sortie

Dans Functions, chaque type de liaison requiert la définition d’une `direction`, d’un `type` et d’un `name` unique dans le fichier function.json. La façon dont vous définissez ces attributs dépend du langage de votre application de fonction.

::: zone pivot="programming-language-csharp"

Dans un projet de bibliothèque de classes C#, les liaisons sont définies comme attributs de liaison sur la méthode de fonction. Le fichier *function.json* nécessaire à Functions est ensuite généré automatiquement en fonction de ces attributs.

Ouvrez le fichier projet *HttpExample.cs*, puis ajoutez le paramètre suivant à la définition de la méthode `Run` :

```csharp
[CosmosDB(
    databaseName: "my-database",
    collectionName: "my-container",
    ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
```

Le paramètre `documentsOut` est un type IAsyncCollector<T>, qui représente une collection de documents JSON qui sont écrits dans votre conteneur Azure Cosmos DB quand la fonction se termine. Des attributs spécifiques définissent le nom du conteneur et le nom de sa base de données parente. La chaîne de connexion pour votre compte Azure Cosmos DB est définie par `ConnectionStringSettingAttribute`.

La définition de la méthode Run doit maintenant se présenter comme ceci :  

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
```

::: zone-end

::: zone pivot="programming-language-javascript"

Les attributs de liaison sont définis directement dans le fichier function.json. Selon le type de liaison, des propriétés supplémentaires peuvent être requises. La [configuration de sortie d’Azure Cosmos DB](./functions-bindings-cosmosdb-v2-output.md#configuration) décrit les champs requis pour une liaison de sortie d’Azure Cosmos DB. L’extension vous permet d’ajouter facilement des liaisons au fichier function.json. 

Pour créer une liaison, cliquez avec le bouton droit (Ctrl+Clic sur macOS) sur le fichier `function.json` dans votre dossier HttpTrigger, puis choisissez **Ajouter une liaison...** . Suivez les invites pour définir les propriétés de liaison suivantes pour la nouvelle liaison :

| Prompt | Valeur | Description |
| -------- | ----- | ----------- |
| **Sélectionner le sens de la liaison** | `out` | La liaison est une liaison de sortie. |
| **Sélectionner une liaison sortante** | `Azure Cosmos DB` | La liaison est une liaison Azure Cosmos DB. |
| **Nom utilisé pour identifier cette liaison dans votre code** | `outputDocument` | Nom qui identifie le paramètre de liaison référencé dans votre code. |
| **Base de données Cosmos DB dans laquelle les données sont écrites** | `my-database` | Nom de la base de données Azure Cosmos DB contenant le conteneur cible. |
| **Collection de bases de données où les données sont écrites** | `my-container` | Nom du conteneur Azure Cosmos DB dans lequel les documents JSON sont écrits. |
| **Si la valeur est true, crée la collection et la base de données Cosmos DB** | `false` | La base de données et le conteneur cibles existent déjà. |
| **Sélectionnez le paramètre dans « local.setting.json »** | `CosmosDbConnectionString` | Nom d’un paramètre d’application qui contient la chaîne de connexion du compte Azure Cosmos DB. |
| **Clé de partition (facultative)** | *Laisser vide* | Obligatoire uniquement quand la liaison de sortie crée le conteneur. |
| **Débit de collection (facultatif)** | *Laisser vide* | Obligatoire uniquement quand la liaison de sortie crée le conteneur. |

Une liaison est ajoutée au tableau `bindings` dans votre fichier function.json, qui doit ressembler à ce qui suit :

```json
{
    "type": "cosmosDB",
    "direction": "out",
    "name": "outputDocument",
    "databaseName": "my-database",
    "collectionName": "my-container",
    "createIfNotExists": "false",
    "connectionStringSetting": "CosmosDbConnectionString"
}
```

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Ajouter le code qui utilise la liaison de sortie

::: zone pivot="programming-language-csharp"  

Ajoutez le code qui utilise l’objet de liaison de sortie `documentsOut` pour créer un document JSON. Ajoutez ce code avant que la méthode retourne.

```csharp
if (!string.IsNullOrEmpty(name))
{
    // Add a JSON document to the output container.
    await documentsOut.AddAsync(new
    {
        // create a random ID
        id = System.Guid.NewGuid().ToString(),
        name = name
    });
}
```

À ce stade, votre fonction doit se présenter comme suit :

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a JSON document to the output container.
        await documentsOut.AddAsync(new
        {
            // create a random ID
            id = System.Guid.NewGuid().ToString(),
            name = name
        });
    }

    string responseMessage = string.IsNullOrEmpty(name)
        ? "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."
        : $"Hello, {name}. This HTTP triggered function executed successfully.";

    return new OkObjectResult(responseMessage);
}
```

::: zone-end

::: zone pivot="programming-language-javascript"  

Ajoutez le code qui utilise l’objet de liaison de sortie `outputDocument` sur `context.bindings` pour créer un document JSON. Ajoutez ce code avant l’instruction `context.res`.

```javascript
if (name) {
    context.bindings.outputDocument = JSON.stringify({
        // create a random ID
        id: new Date().toISOString() + Math.random().toString().substr(2,8),
        name: name
    });
}
```

À ce stade, votre fonction doit se présenter comme suit :

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    if (name) {
        context.bindings.outputDocument = JSON.stringify({
            // create a random ID
            id: new Date().toISOString() + Math.random().toString().substr(2,8),
            name: name
        });
    }

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

::: zone-end  

## <a name="run-the-function-locally"></a>Exécuter la fonction localement

1. Comme dans l’article précédent, appuyez sur <kbd>F5</kbd> pour démarrer le projet d’application de fonction et Core Tools. 

1. Une fois Core Tools en cours d’exécution, accédez à la zone **Azure : Functions**. Sous **Fonctions**, développez **Projet local** > **Fonctions**. Cliquez avec le bouton droit (Ctrl-clic sur Mac) sur la fonction `HttpExample`, puis choisissez **Exécuter la fonction maintenant**.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Exécuter la fonction maintenant dans Visual Studio Code":::

1. Dans **Entrer le corps de la requête**, vous pouvez voir la valeur du corps du message de requête pour `{ "name": "Azure" }`. Appuyez sur Entrée pour envoyer ce message de requête à votre fonction.  
 
1. Une fois la réponse retournée, appuyez sur <kbd>Ctrl+C</kbd> pour arrêter Core Tools.

### <a name="verify-that-a-json-document-has-been-created"></a>Vérifier qu’un document JSON a été créé

1. Dans le portail Azure, revenez à votre compte Azure Cosmos DB et sélectionnez **Explorateur de données**.

1. Développez votre base de données et votre conteneur, puis sélectionnez **Éléments** pour lister les documents créés dans votre conteneur.

1. Vérifiez qu’un document JSON a été créé par la liaison de sortie.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/verify-output.png" alt-text="Vérification de la création d’un document dans le conteneur Azure Cosmos DB" border="true":::

## <a name="redeploy-and-verify-the-updated-app"></a>Redéployer et tester l’application mise à jour

1. Dans Visual Studio Code, appuyez sur F1 pour ouvrir la palette de commandes. Dans la palette de commandes, recherchez et sélectionnez `Azure Functions: Deploy to function app...`.

1. Choisissez l’application de fonction que vous avez créée dans le premier article. Comme vous redéployez votre projet sur la même application, sélectionnez **Déployer** pour ignorer l’avertissement sur le remplacement des fichiers.

1. Une fois le déploiement effectué, vous pouvez réutiliser la fonctionnalité **Exécuter la fonction maintenant** pour déclencher la fonction dans Azure.

1. [Revérifiez les documents créés dans votre conteneur Azure Cosmos DB](#verify-that-a-json-document-has-been-created) pour vérifier que la liaison de sortie regénère un nouveau document JSON.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Dans Azure, le terme *ressources* fait référence aux applications de fonction, fonctions, comptes de stockage, et ainsi de suite. Elles sont rassemblées en *groupes de ressources*, et vous pouvez supprimer tous les éléments d’un groupe en supprimant le groupe.

Vous avez créé des ressources pour effectuer ces démarrages rapides. Vous pouvez être facturé pour ces ressources, en fonction de [l’état de votre compte](https://azure.microsoft.com/account/) et de la [tarification du service](https://azure.microsoft.com/pricing/). Si vous n’avez plus besoin des ressources, voici comment les supprimer :

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez mis à jour votre fonction déclenchée par HTTP pour écrire des documents JSON dans un conteneur Azure Cosmos DB. Pour en savoir plus sur le développement de fonctions à l’aide de Visual Studio Code, consultez maintenant ces articles :

+ [Développer des fonctions Azure Functions à l’aide de Visual Studio Code](functions-develop-vs-code.md)

+ [Déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md)
::: zone pivot="programming-language-csharp"  
+ [Exemples de projets Functions complets en C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Informations de référence pour les développeurs C# sur Azure Functions](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Exemples de projets Functions complets en JavaScript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Guide des développeurs JavaScript sur Azure Functions](functions-reference-node.md)  
::: zone-end  