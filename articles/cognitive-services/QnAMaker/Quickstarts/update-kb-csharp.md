---
title: 'Démarrage rapide : Mettre à jour une base de connaissances - REST, C# - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ce démarrage rapide vous aide à mettre à jour votre exemple de base de connaissances QnA Maker par programmation. La définition JSON que vous utilisez pour mettre à jour une base de connaissances vous permet d’ajouter, de modifier ou de supprimer des paires de questions/réponses.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/01/2018
ms.author: diberry
ms.openlocfilehash: 9fa57a02f11461a3ea9a08b7b1b101b143924037
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801691"
---
# <a name="quickstart-update-a-qna-maker-knowledge-base-in-c"></a>Démarrage rapide : Mettre à jour une base de connaissances QnA Maker en C#

Ce démarrage rapide vous aide à mettre à jour une base de connaissances QnA Maker existante par programmation.  Ce code JSON vous permet de mettre à jour une base de connaissances en ajoutant de nouvelles sources de données, en modifiant ou supprimant des sources de données.

Cette API correspond aux actions de modification, puis d’utilisation du bouton **Save and train** (Enregistrer et effectuer l’apprentissage) dans le portail QnA Maker.

Ce démarrage rapide fait appel aux API QnA Maker :
* [Mise à jour](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600) : le modèle de la base de connaissances est défini dans le code JSON envoyé dans le corps de la requête d’API. 
* [Obtenir les détails de l’opération](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>Prérequis

* La dernière [**édition de Visual Studio Community**](https://www.visualstudio.com/downloads/).
* Vous devez disposer d’un [service QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pour récupérer votre clé, sélectionnez **Clés** sous **Gestion des ressources** dans votre tableau de bord.
* ID de la base de connaissances QnA Maker se trouvant dans l’URL du paramètre de chaîne de requête kbid, comme indiqué ci-dessous.

    ![ID de base de connaissances QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

Si vous n’avez pas encore de base de connaissances, vous pouvez créer un exemple à utiliser pour ce démarrage rapide : [Créer une base de connaissances](create-new-kb-csharp.md).

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-csharp-repo-note.md)]

## <a name="create-knowledge-base-project"></a>Créer un projet de base de connaissances

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-required-dependencies"></a>Ajouter les dépendances nécessaires

[!INCLUDE [Add required dependencies to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-dependencies.md)] 

## <a name="add-required-constants"></a>Ajouter les constantes nécessaires

[!INCLUDE [Add required constants to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-constants.md)] 

## <a name="add-knowledge-base-id"></a>Ajouter l’ID de la base de connaissances

[!INCLUDE [Add knowledge base ID as constant](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-kb-id.md)] 

## <a name="add-the-kb-update-definition"></a>Ajouter la définition de mise à jour de base de connaissances

Après les constantes, ajoutez la définition de mise à jour de base de connaissances suivante. La définition de mise à jour comprend trois sections :

* add
* update
* delete

Chaque section peut être utilisée dans la même requête unique envoyée à l’API. 

```csharp
static string new_kb = @"
{
    'add': {
        'qnaList': [
            {
            'id': 1,
            'answer': 'You can change the default message if you use the QnAMakerDialog. See this for details: https://docs.botframework.com/en-us/azure-bot-service/templates/qnamaker/#navtitle',
            'source': 'Custom Editorial',
            'questions': [
                'How can I change the default message from QnA Maker?'
            ],
            'metadata': []
            }
        ],
        'urls': []
    },
    'update' : {
        'name' : 'QnA Maker FAQ from quickstart - updated'
    },
    'delete': {
        'ids': [
            0
        ]
    }
}
";
```

## <a name="add-supporting-functions-and-structures"></a>Ajouter les fonctions et les structures de prise en charge

[!INCLUDE [Add supporting functions and structures](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-support-functions.md)] 

## <a name="add-patch-request-to-update-kb"></a>Ajouter une requête PATCH pour mettre à jour la base de connaissances

Le code suivant envoie une requête HTTPS à l’API QnA Maker afin de mettre à jour les groupes de questions/réponses d’une base de connaissances, et reçoit la réponse :

```csharp
async static Task<Response> PatchUpdateKB(string kb, string new_kb)
{
    string uri = host + service + method + kb;
    Console.WriteLine("Calling " + uri + ".");

    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = new HttpMethod("PATCH");
        request.RequestUri = new Uri(uri);

        request.Content = new StringContent(new_kb, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();
        return new Response(response.Headers, responseBody);
    }
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Ajouter une requête GET pour déterminer l’état de création

La mise à jour d’une base de connaissances ajoute, met à jour et supprime des paires de questions/réponses. 

```csharp
async static Task<Response> GetStatus(string operation)
{
    string uri = host + service + operation;
    Console.WriteLine("Calling " + uri + ".");

    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Get;
        request.RequestUri = new Uri(uri);
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();
        return new Response(response.Headers, responseBody);
    }
}
```

Cet appel d’API renvoie une réponse JSON qui inclut l’état de l’opération : 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

Répétez l’appel jusqu’à ce que l’opération réussisse ou échoue : 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-updatekb-method"></a>Ajouter la méthode UpdateKB
La méthode suivante met à jour la base de connaissances et répète les vérifications sur l’état. Étant donné que la création de la base de connaissances peut prendre un certain temps, vous devez répéter les appels pour vérifier l’état jusqu’à ce que l’état indique une réussite ou un échec.

```csharp
async static void UpdateKB(string kb, string new_kb)
{
    try
    {
        // Starts the QnA Maker operation to update the knowledge base.
        var response = await PatchUpdateKB(kb, new_kb);

        // Retrieves the operation ID, so the operation's status can be
        // checked periodically.
        var operation = response.headers.GetValues("Location").First();

        // Displays the JSON in the HTTP response returned by the 
        // PostUpdateKB(string, string) method.
        Console.WriteLine(PrettyPrint(response.response));

        // Iteratively gets the state of the operation updating the
        // knowledge base. Once the operation state is something other
        // than "Running" or "NotStarted", the loop ends.
        var done = false;
        while (true != done)
        {
            // Gets the status of the operation.
            response = await GetStatus(operation);
            // Displays the JSON in the HTTP response returned by the
            // GetStatus(string) method.
            Console.WriteLine(PrettyPrint(response.response));

            // Deserialize the JSON into key-value pairs, to retrieve the
            // state of the operation.
            var fields = JsonConvert.DeserializeObject<Dictionary<string, string>>(response.response);

            // Gets and checks the state of the operation.
            String state = fields["operationState"];
            if (state.CompareTo("Running") == 0 || state.CompareTo("NotStarted") == 0)
            {
                // QnA Maker is still updating the knowledge base. The thread is
                // paused for a number of seconds equal to the Retry-After
                // header value, and then the loop continues.
                var wait = response.headers.GetValues("Retry-After").First();
                Console.WriteLine("Waiting " + wait + " seconds...");
                Thread.Sleep(Int32.Parse(wait) * 1000);
            }
            else
            {
                // QnA Maker has completed updating the knowledge base.
                done = true;
            }
        }
    }
    catch(Exception ex)
    {
        // An error occurred while updating the knowledge base. Ensure that
        // you included your QnA Maker subscription key and knowledge base ID
        // where directed in the sample.
        Console.WriteLine("An error occurred while updating the knowledge base." + ex.InnerException);
    }
    finally
    {
        Console.WriteLine("Press any key to continue.");
    }
}
```

## <a name="add-the-updatekb-method-to-main"></a>Ajouter la méthode UpdateKB à la méthode Main

Modifiez la méthode Main de façon à appeler la méthode UpdateKB :

```csharp
static void Main(string[] args)
{
    // Invoke the UpdateKB() method to update a knowledge base, periodically
    // checking the status of the QnA Maker operation until the
    // knowledge base is updated.
    UpdateKB(kb, new_kb);

    // The console waits for a key to be pressed before closing.
    Console.ReadLine();
}
```


## <a name="build-and-run-the-program"></a>Créez et exécutez le projet.

Créez et exécutez le programme. Il envoie automatiquement la requête à l’API QnA Maker pour mettre à jour la base de connaissances, puis demande des résultats toutes les 30 secondes. Chaque réponse est imprimée sur la fenêtre de console.

Une fois que votre base de connaissances est mise à jour, vous pouvez l’afficher sur votre portail QnA Maker, sur la page [Mes bases de connaissances](https://www.qnamaker.ai/Home/MyServices). 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)