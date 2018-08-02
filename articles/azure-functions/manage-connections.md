---
title: Guide pratique pour gérer les connexions dans Azure Functions
description: Découvrez comment éviter les problèmes de performances dans Azure Functions à l’aide de clients de connexion statiques.
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2018
ms.author: glenga
ms.openlocfilehash: 86727355d36e16f5b3c7edef8ce666fb27805a80
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346298"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Guide pratique pour gérer les connexions dans Azure Functions

Dans une application de fonction, les fonctions partagent des ressources, parmi lesquelles des connexions &mdash; connexions HTTP, connexions de base de données et connexions aux services Azure tels que Stockage. L’exécution de nombreuses fonctions simultanément peut engendrer une pénurie de connexions disponibles. Cet article explique comment coder vos fonctions pour n’utiliser que le nombre de connexions nécessaire.

## <a name="connections-limit"></a>Limite de connexions

Le nombre de connexions disponibles est limité en partie car une application de fonction s’exécute dans le [bac à sable Azure App Service](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). L’une des restrictions qu’impose le bac à sable à votre code est le [nombre maximal de connexions, soit 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits). Quand vous atteignez cette limite, le runtime des fonctions crée un journal avec le message suivant : `Host thresholds exceeded: Connections`.

La probabilité de dépasser la limite augmente quand le [contrôleur de mise à l’échelle ajoute des instances d’application de fonction](functions-scale.md#how-the-consumption-plan-works) pour gérer plus de requêtes. Chaque instance d’application de fonction peut exécuter plusieurs fonctions en même temps, chacune utilisant des connexions dans la limite des 300 connexions.

## <a name="use-static-clients"></a>Utiliser des clients statiques

Pour éviter d’avoir plus de connexions que nécessaire, réutilisez les instances clientes au lieu d’en créer à chaque appel de fonction. Les clients .NET, comme les clients [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) et Stockage Azure peuvent gérer les connexions si vous utilisez un seul client statique.

Voici quelques recommandations à suivre quand vous utilisez un client spécifique au service dans une application Azure Functions :

- **Ne créez pas** un client à chaque appel de fonction.
- **Créez** un client statique unique qui peut être utilisé par chaque appel de fonction.
- **Envisagez** de créer un client statique unique dans une classe d’assistance partagée si différentes fonctions utilisent le même service.

## <a name="httpclient-code-example"></a>Exemple de code HttpClient

Voici un exemple de code de fonction qui crée un [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) statique :

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("http://example.com");
    // Rest of function
}
```

Une question fréquente concernant [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) .NET est « Dois-je supprimer mon client ? ». En règle générale, vous supprimez les objets qui implémentent `IDisposable` quand vous avez terminé de les utiliser. Par contre, vous ne supprimez pas un client statique, car vous n’avez pas terminé de l’utiliser quand la fonction prend fin. Vous souhaitez que le client statique existe pendant la durée de votre application.

## <a name="documentclient-code-example"></a>Exemple de code DocumentClient

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

## <a name="sqlclient-connections"></a>Connexions SqlClient

Le code de votre fonction peut utiliser le fournisseur de données .NET Framework pour SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) pour établir des connexions à une base de données relationnelle SQL. Il s’agit également du fournisseur sous-jacent pour les frameworks de données qui s’appuient sur ADO.NET, comme Entity Framework. Contrairement aux connexions [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) et [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), ADO.NET implémente par défaut le regroupement de connexions. Cependant, comme vous êtes toujours susceptible d’avoir un nombre insuffisant de connexions, vous devez optimiser les connexions à la base de données. Pour plus d’informations, consultez [Regroupement de connexions SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Certains frameworks de données, comme [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx), obtiennent généralement les chaînes de connexion auprès de la section **ConnectionStrings** d’un fichier de configuration. Dans ce cas, vous devez ajouter explicitement les chaînes de connexion de base de données SQL à la collection **Chaînes de connexion** de vos paramètres d’application de fonction et dans le [fichier local.settings.json](functions-run-local.md#local-settings-file) de votre projet local. Si vous créez une [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) dans votre code de fonction, vous devez stocker la valeur de la chaîne de connexion dans les **Paramètres d’application** avec vos autres connexions.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’intérêt que présentent les clients statiques, consultez [Antimodèle d’instanciation incorrect](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Pour obtenir des conseils permettant d’améliorer les performances d’Azure Functions, consultez [Optimisation des performances et de la fiabilité d’Azure Functions](functions-best-practices.md).