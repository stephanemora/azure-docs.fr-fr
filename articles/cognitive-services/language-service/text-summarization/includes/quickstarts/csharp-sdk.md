---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 89c9a1be112788a5842e3d1fb412dc551788a31f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028895"
---
[Documentation de référence](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet-preview) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics) | [Package (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.2.0-beta.1) | [Exemples supplémentaires](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)


## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services)
* [IDE Visual Studio](https://visualstudio.microsoft.com/vs/)
* Une fois que vous avez votre abonnement Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Créer une ressource Language"  target="_blank">créez une ressource Language </a> dans le portail Azure pour obtenir votre clé et votre point de terminaison.  Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application à l’API. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.
* Pour utiliser la fonctionnalité d’analyse, vous aurez besoin d’une ressource Language avec le niveau tarifaire Standard (S).

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-net-core-application"></a>Créez une application .NET Core

En utilisant l’IDE Visual Studio, créez une application console .NET Core. Cette action va créer un projet nommé « Hello World » avec un seul fichier source C# : *program.cs*.

Installez la bibliothèque cliente en cliquant avec le bouton droit sur la solution dans l’**Explorateur de solutions** et en sélectionnant **Gérer les packages NuGet**. Dans le gestionnaire de package qui s’ouvre, sélectionnez **Parcourir** et recherchez `Azure.AI.TextAnalytics`. Vérifiez que l’option **Inclure la préversion** est activée. Sélectionnez la version `5.2.0-beta.1`, puis **Installer**. Vous pouvez aussi utiliser la [Console du Gestionnaire de package](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package).

## <a name="code-example"></a>Exemple de code

Copiez le code suivant dans votre fichier *program.cs*. N’oubliez pas de remplacer la variable `key` par la clé de votre ressource et la variable `endpoint` par le point de terminaison de votre ressource. 

[!INCLUDE [find the key and endpoint for a resource](../../../includes/find-azure-resource-info.md)]

```csharp
using Azure;
using System;
using Azure.AI.TextAnalytics;
using System.Threading.Tasks;
using System.Collections.Generic;

namespace LanguageDetectionExample
{
    class Program
    {
        private static readonly AzureKeyCredential credentials = new AzureKeyCredential("replace-with-your-key-here");
        private static readonly Uri endpoint = new Uri("replace-with-your-endpoint-here");

        // Example method for summarizing text
        static async Task TextSummarizationExample(TextAnalyticsClient client)
        {
            string document = @"The extractive summarization feature uses natural language processing techniques to locate key sentences in an unstructured text document. 
                These sentences collectively convey the main idea of the document. This feature is provided as an API for developers. 
                They can use it to build intelligent solutions based on the relevant information extracted to support various use cases. 
                In the public preview, extractive summarization supports several languages. It is based on pretrained multilingual transformer models, part of our quest for holistic representations. 
                It draws its strength from transfer learning across monolingual and harness the shared nature of languages to produce models of improved quality and efficiency." ;
        
            // Prepare analyze operation input. You can add multiple documents to this list and perform the same
            // operation to all of them.
            var batchInput = new List<string>
            {
                document
            };
        
            TextAnalyticsActions actions = new TextAnalyticsActions()
            {
                ExtractSummaryActions = new List<ExtractSummaryAction>() { new ExtractSummaryAction() }
            };
        
            // Start analysis process.
            AnalyzeActionsOperation operation = await client.StartAnalyzeActionsAsync(batchInput, actions);
            await operation.WaitForCompletionAsync();
            // View operation status.
            Console.WriteLine($"AnalyzeActions operation has completed");
            Console.WriteLine();
        
            Console.WriteLine($"Created On   : {operation.CreatedOn}");
            Console.WriteLine($"Expires On   : {operation.ExpiresOn}");
            Console.WriteLine($"Id           : {operation.Id}");
            Console.WriteLine($"Status       : {operation.Status}");
        
            Console.WriteLine();
            // View operation results.
            await foreach (AnalyzeActionsResult documentsInPage in operation.Value)
            {
                IReadOnlyCollection<ExtractSummaryActionResult> summaryResults = documentsInPage.ExtractSummaryResults;
        
                foreach (ExtractSummaryActionResult summaryActionResults in summaryResults)
                {
                    if (summaryActionResults.HasError)
                    {
                        Console.WriteLine($"  Error!");
                        Console.WriteLine($"  Action error code: {summaryActionResults.Error.ErrorCode}.");
                        Console.WriteLine($"  Message: {summaryActionResults.Error.Message}");
                        continue;
                    }
        
                    foreach (ExtractSummaryResult documentResults in summaryActionResults.DocumentsResults)
                    {
                        if (documentResults.HasError)
                        {
                            Console.WriteLine($"  Error!");
                            Console.WriteLine($"  Document error code: {documentResults.Error.ErrorCode}.");
                            Console.WriteLine($"  Message: {documentResults.Error.Message}");
                            continue;
                        }
        
                        Console.WriteLine($"  Extracted the following {documentResults.Sentences.Count} sentence(s):");
                        Console.WriteLine();
        
                        foreach (SummarySentence sentence in documentResults.Sentences)
                        {
                            Console.WriteLine($"  Sentence: {sentence.Text}");
                            Console.WriteLine();
                        }
                    }
                }
            }
        
        }

        static async Task Main(string[] args)
        {
            var client = new TextAnalyticsClient(endpoint, credentials);
            await TextSummarizationExample(client);
        }
    }
}

```

### <a name="output"></a>Output

```console
AnalyzeActions operation has completed

Created On   : 9/16/2021 8:04:27 PM +00:00
Expires On   : 9/17/2021 8:04:27 PM +00:00
Id           : 2e63fa58-fbaa-4be9-a700-080cff098f91
Status       : succeeded

Extracted the following 3 sentence(s):

Sentence: The extractive summarization feature in uses natural language processing techniques to locate key sentences in an unstructured text document.

Sentence: This feature is provided as an API for developers.

Sentence: They can use it to build intelligent solutions based on the relevant information extracted to support various use cases.
```
