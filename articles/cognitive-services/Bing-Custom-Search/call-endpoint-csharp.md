---
title: Appeler un point de terminaison à l’aide de C# - Recherche personnalisée Bing - Microsoft Cognitive Services
description: Ce guide de démarrage rapide montre comment demander les résultats de la recherche depuis votre instance de recherche personnalisée, en utilisant C# pour appeler le point de terminaison Recherche personnalisée Bing.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 32644fe0cf0a6e1666d2d1ee6efb826bf753f001
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42814861"
---
# <a name="call-bing-custom-search-endpoint-c"></a>Appeler le point de terminaison Recherche personnalisée Bing (C#)

Ce guide de démarrage rapide montre comment demander les résultats de la recherche depuis votre instance de recherche personnalisée, en utilisant C# pour appeler le point de terminaison Recherche personnalisée Bing. 

## <a name="prerequisites"></a>Prérequis

-  Une instance de recherche personnalisée prête à l’emploi. Consultez [Créer votre première instance Recherche personnalisée Bing](quick-start.md).
-  [.NET core](https://www.microsoft.com/net/download/core) installé.
- Un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec les **API Recherche Bing**. Vous pouvez utiliser un [essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) pour suivre ce guide de démarrage rapide. Vous avez besoin de la clé d’accès fournie lors de l’activation de votre essai gratuit, ou d’une clé d’un abonnement payant de votre tableau de bord Azure.  

  >[!NOTE]  
  >Les clients actuels de la Recherche personnalisée Bing qui ont provisionné une clé en préversion avant le 15 octobre 2017 (inclus) peuvent l’utiliser jusqu’au 30 novembre 2017 ou jusqu’à l’expiration du nombre maximal de requêtes autorisées. Après cette date, ils doivent migrer vers la version mise à la disposition générale sur Azure. 
 
## <a name="run-the-code"></a>Exécuter le code

Pour exécuter cet exemple, suivez ces étapes :

1. Créez un dossier pour votre code.
2. À partir d’un terminal ou d’une invite de commandes, accédez au dossier que vous venez de créer.
3. Exécutez les commandes suivantes :
    ```
    dotnet new console -o BingCustomSearch
    cd BingCustomSearch
    dotnet add package Newtonsoft.Json
    dotnet restore
   ```

4. Copiez le code suivant dans Program.cs.
5. Remplacez **YOUR-SUBSCRIPTION-KEY** et **YOUR-CUSTOM-CONFIG-ID** par votre clé et votre ID de configuration.

    ``` CSharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    
    namespace bing_custom_search_example_dotnet
    {
        class Program
        {
            static void Main(string[] args)
            {
                var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
                var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
                var searchTerm = args.Length > 0 ? args[0]: "microsoft";            
    
                var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                    "q=" + searchTerm +
                    "&customconfig=" + customConfigId;
    
                var client = new HttpClient();
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                var httpResponseMessage = client.GetAsync(url).Result;
                var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
                BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
                
                for(int i = 0; i < response.webPages.value.Length; i++)
                {                
                    var webPage = response.webPages.value[i];
                    
                    Console.WriteLine("name: " + webPage.name);
                    Console.WriteLine("url: " + webPage.url);                
                    Console.WriteLine("displayUrl: " + webPage.displayUrl);
                    Console.WriteLine("snippet: " + webPage.snippet);
                    Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
                    Console.WriteLine();
                }            
            }
        }
    
        public class BingCustomSearchResponse
        {        
            public string _type{ get; set; }            
            public WebPages webPages { get; set; }
        }
    
        public class WebPages
        {
            public string webSearchUrl { get; set; }
            public int totalEstimatedMatches { get; set; }
            public WebPage[] value { get; set; }        
        }
    
        public class WebPage
        {
            public string name { get; set; }
            public string url { get; set; }
            public string displayUrl { get; set; }
            public string snippet { get; set; }
            public DateTime dateLastCrawled { get; set; }
            public string cachedPageUrl { get; set; }
            public OpenGraphImage openGraphImage { get; set; }        
        }
        
        public class OpenGraphImage
        {
            public string contentUrl { get; set; }
            public int width { get; set; }
            public int height { get; set; }
        }
    }
    ```
6. Créez l’application à l’aide de la commande suivante. Notez le chemin de la dll qui est référencé par la sortie de commande.

    <pre>
    dotnet build 
    </pre>
    
7. Exécutez l’application à l’aide de la commande suivante en remplaçant **PATH TO OUTPUT** par le chemin référencé par l’étape de génération.

    <pre>    
    dotnet **PATH TO OUTPUT**
    </pre>

## <a name="next-steps"></a>Étapes suivantes
- [Configurer votre expérience d’interface utilisateur hébergée](./hosted-ui.md)
- [Utiliser des marqueurs d’ornement pour mettre en surbrillance du texte](./hit-highlighting.md)
- [Paginer des pages web](./page-webpages.md)
