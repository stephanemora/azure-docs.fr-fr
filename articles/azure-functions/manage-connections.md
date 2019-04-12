---
title: Gérer les connexions dans Azure Functions
description: Découvrez comment éviter les problèmes de performances dans Azure Functions à l’aide de clients de connexion statiques.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/25/2018
ms.author: glenga
ms.openlocfilehash: 4e9bd4e9ea467446c2814cdb8956a40b1503b027
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489448"
---
# <a name="manage-connections-in-azure-functions"></a>Gérer les connexions dans Azure Functions

Fonctions dans une application de fonction partagent des ressources. Parmi ces ressources partagées sont connexions : Les connexions HTTP, les connexions de base de données et les connexions aux services tels que le stockage Azure. L’exécution de nombreuses fonctions simultanément peut engendrer une pénurie de connexions disponibles. Cet article explique comment coder vos fonctions pour éviter d’utiliser davantage de connexions qu’ils ont besoin.

## <a name="connection-limit"></a>Limite de connexion

Le nombre de connexions disponibles est limité en partie parce qu’une application de fonction s’exécute dans un [environnement de bac à sable](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Une des restrictions qui le bac à sable impose sur votre code est un [limite concernant le nombre de connexions (actuellement à 600 connexions actives et le nombre total de 1 200 connexions)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits) par instance. Quand vous atteignez cette limite, le runtime des fonctions crée un journal avec le message suivant : `Host thresholds exceeded: Connections`.

Cette limite s’effectue par instance.  Lorsque le [contrôleur de mise à l’échelle ajoute des instances d’application de fonction](functions-scale.md#how-the-consumption-and-premium-plans-work) pour traiter les demandes de plus, chaque instance a une limite de connexion indépendante. Cela signifie qu’il n’existe aucune limite de connexion global, et vous pouvez avoir beaucoup plus de 600 connexions actives entre toutes les instances actives.

Lors du dépannage, assurez-vous que vous avez activé Application Insights pour votre function app. Application Insights vous permet d’afficher les métriques pour vos applications de fonction comme des exécutions. Pour plus d’informations, consultez [afficher les données de télémétrie dans Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Clients statiques

Pour éviter d’avoir plus de connexions que nécessaire, réutilisez les instances clientes au lieu d’en créer à chaque appel de fonction. Nous vous recommandons de réutiliser des connexions de client pour n’importe quel langage que vous pouvez écrire votre fonction dans. Par exemple, les clients .NET comme le [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), et les clients de stockage Azure peuvent gérer les connexions si vous utilisez un client unique et statique.

Voici quelques conseils à suivre lorsque vous utilisez un client de service spécifique dans une application Azure Functions :

- *Ne le faites pas* créer un nouveau client à chaque invocation de fonction.
- *Faire* créer un client unique et statique, qui permet de chaque appel de fonction.
- *Envisagez* création d’un client unique et statique dans une classe d’assistance partagé si différentes fonctions utilisent le même service.

## <a name="client-code-examples"></a>Exemples de code client

Cette section présente les meilleures pratiques en matière de création et d’utilisation de clients à partir de votre code Function.

### <a name="httpclient-example-c"></a>Exemple HttpClient (C#)

Voici un exemple de C# fonction le code qui crée un statique [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) instance :

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Une question courante concernant [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) dans .NET est « Dois-je supprimer mon client ? » En règle générale, vous supprimez des objets qui implémentent `IDisposable` lorsque vous avez terminé leur utilisation. Mais vous ne dispose d’un client statique, car vous n’êtes pas terminé l’utiliser pour la fonction se termine. Vous souhaitez que le client statique existe pendant la durée de votre application.

### <a name="http-agent-examples-javascript"></a>Exemples de l’agent HTTP (JavaScript)

Parce qu’elle fournit des meilleures options de gestion des connexions, vous devez utiliser la classe [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) native au lieu de méthodes non natives, telles que le module `node-fetch`. Paramètres de connexion sont configurés via les options sur la `http.agent` classe. Pour les options détaillées disponibles avec l’agent HTTP, consultez [nouvel Agent (\[options\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

Global `http.globalAgent` classe utilisée par `http.request()` dispose de toutes ces valeurs définies à leurs valeurs par défaut respectifs. La méthode recommandée pour configurer des limites de connexion dans Functions consiste à définir un nombre maximal à l’échelle globale. L’exemple suivant définit le nombre maximal de sockets pour l’application de fonction :

```js
http.globalAgent.maxSockets = 200;
```

 L’exemple suivant crée une nouvelle requête HTTP avec un agent HTTP personnalisé uniquement pour cette requête :

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Exemple de code DocumentClient (C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) se connecte à une instance d’Azure Cosmos DB. La documentation d’Azure Cosmos DB recommande [d’utiliser un client Azure Cosmos DB singleton pendant la durée de vie de votre application](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). L’exemple suivant montre un modèle pour effectuer cette opération dans une fonction :

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

### <a name="cosmosclient-code-example-javascript"></a>Exemple de code CosmosClient (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) se connecte à une instance d’Azure Cosmos DB. La documentation d’Azure Cosmos DB recommande [d’utiliser un client Azure Cosmos DB singleton pendant la durée de vie de votre application](../cosmos-db/performance-tips.md#sdk-usage). L’exemple suivant montre un modèle pour effectuer cette opération dans une fonction :

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const masterKey = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, auth: { masterKey } });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { result: itemArray } = await container.items.readAll().toArray();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>Connexions SqlClient

Votre code de fonction peut utiliser le fournisseur de données .NET Framework pour SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) pour établir des connexions à une base de données relationnelle SQL. Il s’agit également du fournisseur sous-jacent pour les infrastructures de données qui s’appuient sur ADO.NET, tels que [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Contrairement aux connexions [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) et [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), ADO.NET implémente par défaut le regroupement de connexions. Mais étant donné que vous pouvez toujours suffisamment de connexions, vous devez optimiser les connexions à la base de données. Pour plus d’informations, consultez [Regroupement de connexions SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Certaines infrastructures de données, telles que de Entity Framework, obtiennent généralement des chaînes de connexion à partir de la **ConnectionStrings** section d’un fichier de configuration. Dans ce cas, vous devez ajouter explicitement les chaînes de connexion de base de données SQL à la collection **Chaînes de connexion** de vos paramètres d’application de fonction et dans le [fichier local.settings.json](functions-run-local.md#local-settings-file) de votre projet local. Si vous créez une instance de [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) dans votre code de fonction, vous devez stocker la valeur de chaîne de connexion dans **paramètres d’Application** avec les autres connexions.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la raison pour laquelle nous vous recommandons de clients statiques, consultez [antimodèle d’instanciation incorrecte](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Pour obtenir des conseils permettant d’améliorer les performances d’Azure Functions, consultez [Optimisation des performances et de la fiabilité d’Azure Functions](functions-best-practices.md).
