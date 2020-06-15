---
title: Coder une application cliente
titleSuffix: Azure Digital Twins
description: Tutoriel expliquant comment écrire le code minimal d’une application cliente à l’aide du kit SDK (C#) .NET.
author: cschormann
ms.author: cschorm
ms.date: 05/05/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 7e057d6d973eedd3ac53fd7b2ea228470e9123d7
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84611483"
---
# <a name="coding-with-the-azure-digital-twins-apis"></a>Codage avec les API Azure Digital Twins

Il arrive souvent aux développeurs qui travaillent avec Azure Digital Twins de devoir écrire une application cliente pour interagir avec leur instance du service Azure Digital Twins. Ce tutoriel destiné aux développeurs fournit une introduction à la programmation par rapport au service Azure Digital Twins à l’aide de la [bibliothèque de client Azure IoT Digital Twins pour .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Il décrit étape par étape comment écrire une application console cliente C# à partir de rien.

## <a name="prerequisites"></a>Prérequis

Ce tutoriel utilise la ligne de commande pour la création et la configuration du projet. Vous pouvez donc utiliser n’importe quel éditeur de code pour effectuer les exercices.

Pour commencer, il vous faut :
* Un éditeur de code.
* **.NET Core 3.1** sur votre ordinateur de développement. Vous pouvez télécharger cette version du kit SDK .NET Core pour plusieurs plateformes à partir du site [Télécharger .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

[!INCLUDE [Azure Digital Twins tutorials: instance prereq](../../includes/digital-twins-tutorial-prereq-instance.md)]

## <a name="set-up-project"></a>Configurer le projet

Une fois que vous êtes prêt à utiliser votre instance Azure Digital Twins, commencez à configurer le projet d’application cliente. 

Ouvrez une invite de commandes ou une autre fenêtre de console sur votre ordinateur, puis créez un répertoire de projet vide dans lequel vous souhaitez stocker votre travail au cours de ce tutoriel. Nommez le répertoire comme vous le souhaitez (par exemple *TutorielCodeDigitalTwins*).

Accédez au nouveau répertoire.

Une fois dans le répertoire du projet, créez un projet d’application console .NET vide. Dans la fenêtre de commande, exécutez la commande suivante pour créer un projet C# minimal pour la console :

```cmd/sh
dotnet new console
```

Plusieurs fichiers seront alors créés dans votre répertoire, notamment un nommé *Program.cs* où vous écrirez la plupart de votre code.

Ensuite, ajoutez deux dépendances nécessaires à l’utilisation d’Azure Digital Twins :

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.2
dotnet add package Azure.identity
```

La première dépendance est la [bibliothèque de client Azure IoT Digital Twins pour .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). La deuxième dépendance fournit des outils pour faciliter l’authentification auprès d’Azure.

Laissez la fenêtre de commande ouverte parce que vous allez l’utiliser tout au long du tutoriel.

## <a name="get-started-with-project-code"></a>Démarrer avec le code de projet

Dans cette section, vous allez commencer à écrire le code de votre nouveau projet d’application pour fonctionner avec Azure Digital Twins. Les actions couvertes sont les suivantes :
* Authentification auprès du service
* Chargement d’un modèle
* Interception des erreurs
* Création de jumeaux numériques
* Création de relations
* Interrogation de jumeaux numériques

Vous trouverez également une section contenant le code complet à la fin du tutoriel. Vous pouvez l’utiliser en guise de référence pour vérifier votre programme au fur et à mesure.

Pour commencer, ouvrez le fichier *Program.cs* dans un éditeur de code de votre choix. Vous verrez un modèle de code minimal qui ressemble à ceci :

```csharp
using System;

namespace DigitalTwinsCodeTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

Tout d’abord, ajoutez des lignes `using` en haut du code pour tirer les dépendances nécessaires.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
```

Ensuite, vous allez ajouter du code à ce fichier pour activer certaines fonctionnalités. 

### <a name="authenticate-against-the-service"></a>S’authentifier auprès du service

La première chose que votre application doit faire, c’est s’authentifier auprès du service Azure Digital Twins. Vous pouvez ensuite créer une classe cliente de service pour accéder aux fonctions du SDK.

Pour vous authentifier, vous avez besoin de trois informations :
* L’*ID de l’annuaire (locataire)* pour votre abonnement
* L’*ID d’application (client)* créé quand vous avez configuré l’instance de service
* Le *hostName* de votre instance de service

>[!TIP]
> Si vous ne connaissez pas votre *ID de l’annuaire (locataire)* , vous pouvez l’obtenir en exécutant la commande suivante dans [Azure Cloud Shell](https://shell.azure.com) :
> 
> ```azurecli-interactive
> az account show --query tenantId
> ```

Dans *Program.cs*, collez le code suivant sous la ligne « Hello, World ! » dans la méthode `Main`. Affectez le *hostName* de votre instance Azure Digital Twins comme valeur de `adtInstanceUrl`, votre *ID d’application* comme `clientId`, et votre *ID d’annuaire* comme `tenantId`.

```csharp
string clientId = "<your-application-ID>";
string tenantId = "<your-directory-ID>";
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
Console.WriteLine($"Service client created – ready to go");
```

Enregistrez le fichier. 

Notez que cet exemple utilise des informations d’identification de navigateur interactives :
```csharp
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
```

Ce type d’informations d’identification entraîne l’ouverture d’une fenêtre de navigateur vous invitant à fournir vos informations d’identification Azure. 

>[!NOTE]
> Pour plus d’informations sur les autres types d’informations d’identification, consultez la documentation des [bibliothèques d’authentification de plateforme des identités Microsoft](../active-directory/develop/reference-v2-libraries.md).

Dans votre fenêtre de commande, exécutez le code avec cette commande : 

```cmd/sh
dotnet run
```

Cette opération restaure les dépendances lors de la première exécution, puis exécute le programme. 
* Si aucune erreur ne se produit, le programme imprime *Service client created - ready to go*.
* Étant donné qu’il n’y a pas encore de gestion des erreurs dans ce projet, si un problème se produit, une exception est levée par le code.

### <a name="upload-a-model"></a>Charger un modèle

Azure Digital Twins n’a aucun vocabulaire de domaine intrinsèque. C’est vous qui définissez, à l’aide de **modèles**, les types d’éléments de votre environnement que vous pouvez représenter dans Azure Digital Twins. Les [modèles](concepts-twins-graph.md) sont similaires aux classes dans les langages de programmation orientés objet. Ils fournissent des modèles de [jumeaux numériques](concepts-models.md) définis par l’utilisateur à suivre et à instancier ultérieurement. Ils sont écrits dans un langage de type JSON appelé **DTDL (Digital Twins Definition Language)** .

La première étape de la création d’une solution Azure Digital Twins consiste à définir au moins un modèle dans un fichier DTDL.

Dans le répertoire où vous avez créé votre projet, créez un fichier *.json* nommé *SampleModel.json*. Collez-y le corps de fichier suivant : 

```json
{
  "@id": "dtmi:com:contoso:SampleModel;1",
  "@type": "Interface",
  "displayName": "SampleModel",
  "contents": [
    {
      "@type": "Relationship",
      "name": "contains"
    },
    {
      "@type": "Property",
      "name": "data",
      "schema": "string"
    }
  ],
  "@context": "dtmi:dtdl:context;2"
}
```

> [!TIP]
> Si vous utilisez Visual Studio pour ce tutoriel, vous souhaiterez peut-être sélectionner le nouveau fichier JSON et affecter la valeur *Copier si plus récent* ou *Toujours copier* à la propriété *Copier dans le répertoire de sortie* dans l’inspecteur de propriété. Cela permettra à Visual Studio de trouver le fichier JSON avec le chemin par défaut quand vous exécuterez le programme avec **F5** lors des étapes suivantes du tutoriel.

> [!TIP] 
> Il existe un [exemple de validateur DTDL](https://github.com/Azure-Samples/DTDL-Validator) indépendant du langage qui vous permet de vérifier les documents de modèle et la validité du DTDL. Il repose sur la bibliothèque de l’analyseur DTDL, qui est décrite en détail dans [Guide pratique pour analyser et valider les modèles](how-to-use-parser.md).

Ensuite, ajoutez du code à *Program.cs* pour charger le modèle que vous venez de créer dans votre instance Azure Digital Twins.

Tout d’abord, ajoutez quelques instructions `using` au début du fichier :

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Models;
```

Ensuite, préparez l’utilisation des méthodes asynchrones dans le SDK de service C# en modifiant la signature de la méthode `Main` pour autoriser l’exécution asynchrone. 

```csharp
static async Task Main(string[] args)
```

> [!NOTE]
> L’utilisation de `async` n’est pas strictement nécessaire, car le SDK fournit également des versions synchrones de tous les appels. Ce tutoriel utilise `async`.

Vient ensuite le premier bloc de code qui interagit avec le service Azure Digital Twins. Ce code charge le fichier DTDL que vous avez créé à partir de votre disque, puis le charge sur votre instance de service Azure Digital Twins. 

Collez le code suivant sous le code d’autorisation que vous avez ajouté plus tôt.

```csharp
Console.WriteLine();
Console.WriteLine($"Upload a model");
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
// Upload the model to the service
await client.CreateModelsAsync(typeList);
```

Dans votre fenêtre de commande, exécutez le programme avec cette commande : 

```cmd/sh
dotnet run
```

Vous remarquerez qu’à ce stade, il n’y a pas de sortie indiquant que l’appel a réussi. 

Pour ajouter une instruction d’impression indiquant si les modèles ont effectivement été chargés avec succès, ajoutez le code suivant juste après la section précédente :

```csharp
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Avant de réexécuter le programme pour tester ce nouveau code, rappelez-vous que la dernière fois que vous avez exécuté le programme, vous avez déjà chargé votre modèle. Azure Digital Twins ne vous permettra pas de charger deux fois le même modèle. Attendez-vous donc à voir une exception quand vous réexécuterez le programme.

À présent, réexécutez le programme avec cette commande dans votre fenêtre de commande :

```cmd/sh
dotnet run
```

Le programme doit lever une exception. Quand vous essayez de charger un modèle qui a déjà été chargé, le service retourne une erreur de « requête incorrecte » par le biais de l’API REST. Par conséquent, le SDK client Azure Digital Twins lèvera à son tour une exception pour chaque code de retour de service autre qu’un succès. 

La section suivante traite des exceptions de ce type et explique comment les gérer dans votre code.

### <a name="catch-errors"></a>Intercepter les erreurs

Pour empêcher que le programme se plante, vous pouvez ajouter du code d’exception autour du code de chargement du modèle. Encapsulez l’appel client existant `client.CreateModelsAsync` dans un gestionnaire try/catch comme suit :

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```

Si maintenant vous exécutez le programme avec `dotnet run` dans votre fenêtre de commande, vous verrez que vous obtenez un code d’erreur. La sortie ressemble à ceci :

```cmd/sh
Hello World!
Service client created - ready to go

Upload a model
Load model: 409:Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"DocumentAlreadyExists","message":"A document with same identifier already exists.","details":[]}}

Headers:
api-supported-versions: REDACTED
Date: Tue, 05 May 2020 01:57:51 GMT
Content-Length: 115
Content-Type: application/json; charset=utf-8

Type name: : dtmi:com:contoso:SampleModel;1
```

Désormais, ce tutoriel encapsulera tous les appels aux méthodes de service dans des gestionnaires try/catch.

### <a name="create-digital-twins"></a>Créer des jumeaux numériques

Maintenant que vous avez chargé un modèle dans Azure Digital Twins, vous pouvez utiliser cette définition de modèle pour créer des **jumeaux numériques**. Les [jumeaux numériques](concepts-twins-graph.md) sont des instances d’un modèle ; ils représentent les entités au sein de votre environnement d’entreprise (par exemple les capteurs dans une ferme, les salles d’un bâtiment ou les voyants d’une voiture). Cette section crée quelques jumeaux numériques basés sur le modèle que vous avez chargé.

Ajoutez une nouvelle instruction `using` en haut, car vous aurez besoin du sérialiseur JSON .NET intégré dans `System.Text.Json` :

```csharp
using System.Text.Json;
```

Ensuite, ajoutez le code suivant à la fin de la méthode `Main` pour créer et initialiser trois jumeaux numériques basés sur ce modèle.

```csharp
// Initialize twin metadata
var meta = new Dictionary<string, object>
{
    { "$model", "dtmi:com:contoso:SampleModel;1" },
};
// Initialize the twin properties
var initData = new Dictionary<string, object>
{
    { "$metadata", meta },
    { "data", "Hello World!" }
};
string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(initData));
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin: {rex.Status}:{rex.Message}");  
    }
}
```

Dans votre fenêtre de commande, exécutez le programme avec `dotnet run`. Ensuite, répétez l’opération pour réexécuter le programme. 

Notez qu’aucune erreur n’est générée quand les jumeaux sont créés la deuxième fois, bien qu’ils existent déjà après la première exécution. Contrairement à la création de modèle, la création de jumeau est, au niveau REST, un appel *PUT* avec une sémantique *upsert*. Cela signifie que si un jumeau existe déjà, toute tentative visant à le recréer ne fera que le remplacer. Aucune erreur n’est requise.

### <a name="create-relationships"></a>Créer des relations

Ensuite, vous pouvez créer des **relations** entre les jumeaux que vous avez créés, afin de les raccorder sur un **graphe de jumeaux**. Les [graphes de jumeaux](concepts-twins-graph.md) servent à représenter votre environnement entier.

Pour pouvoir créer des relations, ajoutez une instruction `using` pour le type de base de relation dans le SDK :
```csharp
using Azure.DigitalTwins.Core.Serialization;
```

Ensuite, ajoutez une nouvelle méthode statique à la classe `Program`, sous la méthode `Main` :
```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

Ensuite, ajoutez le code suivant à la fin de la méthode `Main` pour appeler le code `CreateRelationship` :
```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

Dans votre fenêtre de commande, exécutez le programme avec `dotnet run`.

Notez qu’Azure Digital Twins ne vous permet pas de créer une relation s’il en existe déjà une portant le même ID. Par conséquent, si vous exécutez le programme plusieurs fois, vous verrez des exceptions lors de la création de la relation. Ce code intercepte les exceptions et les ignore. 

### <a name="list-relationships"></a>Lister les relations

Le code que vous allez ensuite ajouter vous permet d’afficher la liste des relations que vous avez créées.

Ajoutez la nouvelle méthode suivante à la classe `Program` :

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (string rel in results)
        {
            var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
            Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
        }
    } catch (RequestFailedException rex) {
        Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
    }
}
```

Ensuite, ajoutez le code suivant à la fin de la méthode `Main` pour appeler le code `ListRelationships` :

```csharp
//List the relationships
await ListRelationships(client, "sampleTwin-0");
```

Dans votre fenêtre de commande, exécutez le programme avec `dotnet run`. Vous devez voir une liste de toutes les relations que vous avez créées.

### <a name="query-digital-twins"></a>Interroger des jumeaux numériques

L’une des principales fonctionnalités d’Azure Digital Twins est la capacité à [interroger facilement et efficacement](concepts-query-language.md) votre graphe de jumeaux pour répondre à des questions sur votre environnement.

La dernière section de code à ajouter dans ce tutoriel exécute une requête sur l’instance Azure Digital Twins. La requête utilisée dans cet exemple retourne tous les jumeaux numériques de l’instance.

Ajoutez le code suivant à la fin de la méthode `Main` :

```csharp
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    Console.WriteLine("---------------");
}
```

Dans votre fenêtre de commande, exécutez le programme avec `dotnet run`. La sortie doit afficher tous les jumeaux numériques de cette instance.

## <a name="complete-code-example"></a>Exemple de code complet

À ce stade du tutoriel, vous disposez d’une application cliente complète, capable d’effectuer des actions de base sur Azure Digital Twins. Pour référence, le code complet du programme dans *Program.cs* est indiqué ci-dessous :

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Models;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            
            string clientId = "<your-application-ID>";
            string tenantId = "<your-directory-ID>";
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
            var credentials = new InteractiveBrowserCredential(tenantId, clientId);
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");

            Console.WriteLine();
            Console.WriteLine($"Upload a model");
            var typeList = new List<string>();
            string dtdl = File.ReadAllText("SampleModel.json");
            typeList.Add(dtdl);
            // Upload the model to the service
            try {
                await client.CreateModelsAsync(typeList);
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
            }
            // Read a list of models back from the service
            AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
            await foreach (ModelData md in modelDataList)
            {
                Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
            }

            // Initialize twin metadata
            var meta = new Dictionary<string, object>
            {
                { "$model", "dtmi:com:contoso:SampleModel;1" },
            };
            // Initialize the twin properties
            var initData = new Dictionary<string, object>
            {
                { "$metadata", meta },
                { "data", "Hello World!" }
            };
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(initData));
                    Console.WriteLine($"Created twin: {prefix}{i}");
                } catch(RequestFailedException rex) {
                    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
                }
            }

            // Connect the twins with relationships
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");

            //List the relationships
            await ListRelationships(client, "sampleTwin-0");

            // Run a query    
            AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
            await foreach (string twin in result)
            {
                object jsonObj = JsonSerializer.Deserialize<object>(twin);
                string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
                Console.WriteLine(prettyTwin);
                Console.WriteLine("---------------");
            }
        }

        public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = "contains"
            };
        
            try
            {
                string relId = $"{srcId}-contains->{targetId}";
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine("Created relationship successfully");
            }
            catch (RequestFailedException rex) {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
        }
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (string rel in results)
                {
                    var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
                    Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
                }
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
            }
        }

    }
}
```
## <a name="clean-up-resources"></a>Nettoyer les ressources
 
L’instance utilisée dans ce tutoriel peut être réutilisée dans le tutoriel suivant, intitulé [Tutoriel : Explorer les bases avec un exemple d’application cliente](tutorial-command-line-app.md). Si vous envisagez de passer au tutoriel suivant, vous pouvez conserver l’instance Azure Digital Twins que vous avez configurée ici.
 
Si vous n’avez plus besoin des ressources créées dans ce tutoriel, effectuez les étapes suivantes pour les supprimer.

Dans [Azure Cloud Shell](https://shell.azure.com), vous pouvez supprimer toutes les ressources Azure d’un groupe de ressources avec la commande [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). Cela supprime le groupe de ressources et l’instance Azure Digital Twins.

> [!IMPORTANT]
> La suppression d’un groupe de ressources est irréversible. Le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects. 

Ouvrez Azure Cloud Shell et exécutez la commande suivante pour supprimer le groupe de ressources et tout ce qu’il contient.

```azurecli-interactive
az group delete --name <your-resource-group>
```

Ensuite, supprimez l’inscription d’application Azure Active Directory que vous avez créée pour votre application cliente à l’aide de cette commande :

```azurecli
az ad app delete --id <your-application-ID>
```

Pour finir, supprimez le dossier de projet que vous avez créé sur votre ordinateur local.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé une application console cliente .NET à partir de rien. Vous avez écrit du code pour cette application cliente afin d’effectuer les actions de base sur une instance Azure Digital Twins.

Passez au tutoriel suivant pour découvrir les opérations que vous pouvez effectuer avec cet exemple d’application cliente : 

> [!div class="nextstepaction"]
> [Tutoriel : Explorer les bases avec un exemple d’application cliente](tutorial-command-line-app.md)

Vous pouvez également étendre le code que vous avez écrit dans ce tutoriel en apprenant à effectuer d’autres opérations de gestion dans les articles de procédures, ou consulter la documentation du concept pour en savoir plus sur les éléments que vous avez utilisés dans le tutoriel.
* [Guide pratique pour gérer un modèle de jumeau](how-to-manage-model.md)
* [Concepts : modèles personnalisés](concepts-models.md)
