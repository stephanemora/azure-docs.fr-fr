---
title: 'Démarrage rapide : Publier une base de connaissances - REST, C# -  QnA Maker'
titleSuffix: Azure Cognitive Services
description: Ce démarrage rapide vous aide à publier votre base de connaissances qui envoie (push) la dernière version de la base de connaissances testée à un index de Recherche Azure dédié, qui représente la base de connaissances publiée. Il crée également un point de terminaison qui peut être appelé dans votre application ou bot conversationnel.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/01/2018
ms.author: diberry
ms.openlocfilehash: f2aa73dacdffaebcddbf91b2f5c7c3db4a331431
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883582"
---
# <a name="quickstart-publish-a-qna-maker-knowledge-base-in-c"></a>Démarrage rapide : Publier une base de connaissances QnA Maker en C#

Ce démarrage rapide vous aide à publier votre base de connaissances par programmation. La publication envoie la dernière version de la base de connaissances à un index de Recherche Azure dédié et crée un point de terminaison pouvant être appelé dans votre application ou chatbot.

Ce démarrage rapide fait appel aux API QnA Maker :
* [Publish](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) : cette API ne nécessite aucune information dans le corps de la requête.

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

## <a name="add-supporting-functions-and-structures"></a>Ajouter les fonctions et les structures de prise en charge

Ajoutez le bloc de code suivant dans la classe Program :

```csharp
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

## <a name="add-post-request-to-publish-kb"></a>Ajouter une requête POST pour publier la base de connaissances

Le code suivant envoie une requête HTTPS à l’API QnA Maker afin de publier une base de connaissances et reçoit la réponse :

```csharp
async static void PublishKB()
{
    string responseText;

    var uri = host + service + method + kb;
    Console.WriteLine("Calling " + uri + ".");
    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(uri);
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);

        // successful status doesn't return an JSON so create one
        if (response.IsSuccessStatusCode)
        {
            responseText = "{'result' : 'Success.'}";
        }
        else
        {
            responseText =  await response.Content.ReadAsStringAsync();
        }
    }
    Console.WriteLine(PrettyPrint(responseText));
    Console.WriteLine("Press any key to continue.");
}
```

L’appel API renvoie un état 204 si la publication réussit sans aucun contenu dans le corps de la réponse. Le code ajoute du contenu pour les réponses 204.

Toutes les autres réponses sont renvoyées inchangées.
 
## <a name="add-the-publishkb-method-to-main"></a>Ajouter la méthode PublishKB à la méthode Main

Modifiez la méthode principale de façon à appeler la méthode CreateKB :

```csharp
static void Main(string[] args)
{

    // Call the PublishKB() method to publish a knowledge base.
    PublishKB();

    // The console waits for a key to be pressed before closing.
    Console.ReadLine();
}
```

## <a name="build-and-run-the-program"></a>Créez et exécutez le projet.

Créez et exécutez le projet. Il envoie automatiquement la requête à l’API de QnA Maker visant à publier la base de connaissances, puis la réponse est affichée dans la fenêtre de la console.

Une fois votre base de connaissances publiée, vous pouvez l’interroger à partir du point de terminaison à l’aide d’une application client ou d’un chatbot. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
