---
title: Démarrage rapide avec C# de l’API Analyse de texte dans Azure Cognitive Services | Microsoft Docs
description: Cet article contient des informations et des exemples de code pour une prise en main rapide de l’API Analyse de texte dans Microsoft Cognitive Services sur Azure.
services: cognitive-services
documentationcenter: ''
author: luiscabrer
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 09/20/2017
ms.author: ashmaka
ms.openlocfilehash: 59e2254054f51a8d5f30e1b38dc5e6c23899c054
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284319"
---
# <a name="quickstart-for-text-analytics-api-with-c"></a>Démarrage rapide de l’API Analyse de texte avec C# 
<a name="HOLTop"></a>

Cet article montre comment détecter la langue, analyser les sentiments et extraire les phrases clés à l’aide des [API Analyse de texte](//go.microsoft.com/fwlink/?LinkID=759711) avec C#. Le code a été écrit pour fonctionner sur une application .NET Core avec des références minimes à des bibliothèques externes, ce qui fait qu’il est également exécutable sous Linux ou MacOS.

Consultez les [définitions des API](//go.microsoft.com/fwlink/?LinkID=759346) pour accéder à la documentation technique des API.

## <a name="prerequisites"></a>Prérequis

Vous devez disposer d’un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec **l’API Analyse de texte**. Vous pouvez utiliser le **niveau gratuit pour 5 000 transactions par mois** afin de suivre ce guide de démarrage rapide.

Il vous faut également le [point de terminaison et la clé d'accès](../How-tos/text-analytics-how-to-access-key.md) générée pendant le processus d’inscription. 


## <a name="install-the-nuget-sdk-package"></a>Installer le package de Kit SDK NuGet
1. Créez une solution de console dans Visual Studio.
1. Cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet de la solution**.
1. Cochez la case **Inclure les préversions**.
1. Sélectionnez l’onglet **Parcourir** et recherchez **Microsoft.Azure.CognitiveServices.Language**.
1. Sélectionnez le package NuGet et installez-le.

> [!Tip]
>  Il est possible d’appeler directement les [points de terminaison HTTP](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) avec C#, mais il est plus facile d’appeler le service avec le Kit de développement logiciel (SDK) Microsoft.Azure.CognitiveServices.Language sans avoir à se soucier de la sérialisation et de la désérialisation du code JSON.
>
> Quelques liens utiles :
> - [Page du Kit SDK NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [Code du Kit SDK](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-using-the-sdk"></a>Appeler l’API Analyse de texte avec le Kit SDK
1. Remplacez Program.cs par le code ci-dessous. Ce programme illustre les fonctionnalités de l’API Analyse de texte en trois sections (extraction de la langue, extraction de phrases clés et analyse des sentiments).
1. Remplacez la valeur d’en-tête `Ocp-Apim-Subscription-Key` par une clé d’accès valide pour votre abonnement.
1. Remplacez l’emplacement dans `Endpoint` par le point de terminaison sur lequel porte votre inscription. Vous trouverez le point de terminaison dans les ressources du portail Azure. Le point de terminaison commence généralement par « https://[region].api.cognitive.microsoft.com », et veuillez uniquement y indiquer le protocole et le nom d’hôte.
1. Exécutez le programme.

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        /// <summary>
        /// Container for subscription credentials. Make sure to enter your valid key.
        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", "ENTER KEY HERE");
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            };

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result =  client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine("Document ID: {0} , Language: {1}", document.Id, document.DetectedLanguages[0].Name);
            }

            // Getting key-phrases
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing keyphrases
            foreach (var document in result2.Documents)
            {
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine("\t\t" + keyphrase);
                }
            }

            // Extracting sentiment
            Console.WriteLine("\n\n===== SENTIMENT ANALYSIS ======");

            SentimentBatchResult result3 = client.SentimentAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "I had the best day of my life."),
                          new MultiLanguageInput("en", "1", "This was a waste of my time. The speaker put me to sleep."),
                          new MultiLanguageInput("es", "2", "No tengo dinero ni nada que dar..."),
                          new MultiLanguageInput("it", "3", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
                        })).Result;


            // Printing sentiment results
            foreach (var document in result3.Documents)
            {
                Console.WriteLine("Document ID: {0} , Sentiment Score: {1:0.00}", document.Id, document.Score);
            }
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Analyse de texte avec Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Voir aussi 

 [Vue d’ensemble d’Analyse de texte](../overview.md)  
 [Questions fréquentes (FAQ)](../text-analytics-resource-faq.md)

