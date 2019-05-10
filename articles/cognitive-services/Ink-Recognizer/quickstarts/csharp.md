---
title: 'Démarrage rapide : Reconnaître l’encre numérique avec l’API REST de module de reconnaissance de l’encre etC#'
description: Utilisez l’API de module de reconnaissance de l’encre pour démarrer la reconnaissance des traits d’encre numérique.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: article
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 9bb9c23cc1f807cae1d0d22f1652e8f4408f1f91
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65518668"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>Démarrage rapide : Reconnaître l’encre numérique avec l’API REST de module de reconnaissance de l’encre etC#

Utilisez ce guide de démarrage rapide pour commencer l’envoi des traits d’encre numérique à l’API de module de reconnaissance de l’encre. Cela C# application envoie une demande d’API contenant des données de trait d’encre au format JSON et reçoit la réponse.

Bien que cette application soit écrite en C#, l’API est un service web RESTful compatible avec la plupart des langages de programmation.

En règle générale, vous appelez l’API à partir d’une application pour l’écriture manuscrite numérique. Ce démarrage rapide envoie des données de trait d’encre pour l’exemple suivant manuscrite à partir d’un fichier JSON.

![une image de texte manuscrit](../media/handwriting-sample.jpg)

Le code source de ce guide de démarrage rapide est disponible sur [GitHub](https://go.microsoft.com/fwlink/?linkid=2089502).

## <a name="prerequisites"></a>Conditions préalables

- N’importe quelle édition de [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/).
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Pour installer Newtonsoft.Json sous forme de package NuGet dans Visual studio :
        1. Cliquez avec le bouton droit sur le **Manager de la Solution**
        2. Cliquez sur **Gérer les packages NuGet...**
        3. Recherchez `Newtonsoft.Json` et installez le package
- Si vous utilisez Linux/Mac OS, cette application peut être exécuté à l’aide de [Mono](https://www.mono-project.com/).

- Vous trouverez l’exemple de données de trait d’encre pour ce démarrage rapide sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json).

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


## <a name="create-a-new-application"></a>Créer une application

1. Dans Visual Studio, créez une nouvelle solution de console et ajoutez les packages suivants. 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    ```

2. Créer des variables pour votre clé d’abonnement et votre point de terminaison. Voici l’URI que vous pouvez utiliser pour la reconnaissance de l’encre. Il sera ajouté à votre point de terminaison de service ultérieurement pour créer l’API URl de la demande.

    ```csharp
    // Replace the subscriptionKey string with your valid subscription key.
    const string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Replace the dataPath string with a path to the JSON formatted ink stroke data.
    const string dataPath = @"PATH-TO-INK-STROKE-DATA"; 

    // URI information for ink recognition:
    const string endpoint = "https://api.cognitive.microsoft.com";
    const string inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>Créer une fonction pour envoyer des demandes

1. Créez une fonction async appelée `Request` qui accepte les variables créées ci-dessus.

2. Définir le protocole de sécurité et informations d’en-tête à l’aide du client un `HttpClient` objet. Veillez à ajouter votre clé d’abonnement pour le `Ocp-Apim-Subscription-Key` en-tête. Créez ensuite un `StringContent` objet pour la requête.
 
3. Envoyer la demande avec `PutAsync()`. Si la demande est acceptée, retourner la réponse.  
    
    ```csharp
    static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
        
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
            System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            var res = await client.PutAsync(endpoint, content);
            if (res.IsSuccessStatusCode){
                return await res.Content.ReadAsStringAsync();
            }
            else{
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
    ```

## <a name="send-an-ink-recognition-request"></a>Envoyer une demande de reconnaissance de l’encre

1. Créer une nouvelle fonction appelée `recognizeInk()`. Construire la demande et l’envoyer en appelant le `Request()` fonction avec votre point de terminaison clé d’abonnement, l’URL de l’API et les données du trait d’encre numérique.

2. Désérialiser l’objet JSON et l’écrire dans la console. 
    
    ```csharp
    static void recognizeInk(string requestData){

        //construct the request
        var result = Request(
            endpoint,
            inkRecognitionUrl,
            subscriptionKey,
            requestData).Result;

        dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
        System.Console.WriteLine(jsonObj);
    }
    ```

## <a name="load-your-digital-ink-data"></a>Chargez vos données de l’encre numérique

Créer une fonction appelée `LoadJson()` pour charger le fichier JSON de données d’encre. Utilisez un `StreamReader` et `JsonTextReader` pour créer un `JObject` et renvoyez-le.
    
```csharp
public static JObject LoadJson(string fileLocation){

    var jsonObj = new JObject();

    using (StreamReader file = File.OpenText(fileLocation))
    using (JsonTextReader reader = new JsonTextReader(file)){
        jsonObj = (JObject)JToken.ReadFrom(reader);
    }
    return jsonObj;
}
```

## <a name="send-the-api-request"></a>Envoyer la demande d’API

1. Dans la méthode principale de votre application, charger vos données JSON avec la fonction créée ci-dessus. 

2. Appelez le `recognizeInk()` fonction créée ci-dessus. Utilisez `System.Console.ReadKey()` pour maintenir la fenêtre de console ouverte après l’exécution de l’application.
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>Exécutez l’application et afficher la réponse

Exécutez l'application. Une réponse correcte est retournée au format JSON. Vous pouvez également trouver la réponse JSON sur [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json).


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Référence d’API REST](https://go.microsoft.com/fwlink/?linkid=2089907)


Pour observer le fonctionnement de l’API Ink Recognizer dans une application d’écriture manuscrite numérique, examinez les exemples d’applications suivants sur GitHub :
* [C# et UWP (plateforme Windows universelle)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# et WPF (Windows Presentation Foundation)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Application de navigateur web JavaScript](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Application mobile Android et Java](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Application mobile Swift et iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
