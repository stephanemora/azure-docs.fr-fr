---
title: 'Démarrage rapide : Kit de développement logiciel (SDK) Recherche personnalisée, C#'
titleSuffix: Azure Cognitive Services
description: Configuration de l’application console du SDK C# de recherche personnalisée.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 6dc10bc2dedfe99573b5ad646461e66827c6df9e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49320167"
---
# <a name="quickstart-using-the-bing-custom-search-sdk-with-c"></a>Démarrage rapide : Utilisation du Kit de développement logiciel (SDK) Recherche personnalisée Bing avec C#

Le SDK Recherche personnalisée Bing fournit un modèle de programmation plus simple que l’API REST Recherche personnalisée Bing. Cette section vous guide tout au long de la réalisation de vos premiers appels Recherche personnalisée à l’aide du SDK C#.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce démarrage rapide, les éléments suivants sont requis :

- Une instance de recherche personnalisée prête à l’emploi. Consultez [Créer votre première instance Recherche personnalisée Bing](quick-start.md).  
  
- Une clé d’abonnement Vous pouvez obtenir une clé d’abonnement quand vous activez votre [essai gratuit](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), ou vous pouvez utiliser une clé d’abonnement payant de votre tableau de bord Azure (voir [Compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).  
  
- Visual Studio 2017 installé. Si vous ne l’avez pas, vous pouvez télécharger l’**édition gratuite**, [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).  
  
- Package [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) installé. Dans l’Explorateur de solutions de Visual Studio, cliquez avec le bouton droit sur votre projet et sélectionnez `Manage NuGet Packages` dans le menu. Installez le package `Microsoft.Azure.CognitiveServices.Search.CustomSearch`.

L’installation du package NuGet Custom Search installe aussi les assemblys suivants :

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json



## <a name="run-the-code"></a>Exécuter le code

> [!TIP]
> Récupérez le dernier code en tant que solution Visual Studio à partir de [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

Pour exécuter cet exemple, suivez ces étapes :

1. Ouvrez Visual Studio 2017.
  
2. Cliquez sur le menu **Fichier**, sur **Nouveau**, sur **Projet**, puis sur le modèle **Application console Visual C#**.
  
3. Nommez votre solution CustomSearchSolution et accédez au dossier dans lequel la placer.
  
4. Cliquez sur OK pour créer la solution.  
  
4. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre projet (il a le même nom que la solution) et sélectionnez `Manage NuGet Packages`. Dans le Gestionnaire de package NuGet, cliquez sur **Parcourir**. Entrez Microsoft.Azure.CognitiveServices.Search.CustomSearch dans la zone de recherche et appuyez sur Entrée. Sélectionnez le package et cliquez sur Installer.  
  
4. Copiez le code suivant dans le fichier Program.cs. Remplacez **YOUR-SUBSCRIPTION-KEY** et **YOUR-CUSTOM-CONFIG-ID** par votre clé d’abonnement et votre ID de configuration.  
  
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

    namespace CustomSrchSDK
    {
        class Program
        {
            static void Main(string[] args)
            {

                var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));

                try
                {
                    // This will look up a single query (Xbox).
                    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
                    Console.WriteLine("Searched for Query# \" Xbox \"");

                    //WebPages
                    if (webData?.WebPages?.Value?.Count > 0)
                    {
                        // find the first web page
                        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                        if (firstWebPagesResult != null)
                        {
                            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
                            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find web results!");
                        }
                    }
                    else
                    {
                        Console.WriteLine("Didn't see any Web data..");
                    }
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Encountered exception. " + ex.Message);
                }

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

        }
    }
    ```  
  
5. Générez et exécutez (déboguez) la solution. 




## <a name="breaking-it-down"></a>Décomposition du code

Après avoir installé le package NuGet Custom Search, vous devez lui ajouter une directive using.

```csharp
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
```

Ensuite, instanciez le client de recherche personnalisée, que vous utilisez pour vos demandes de recherche. Veillez à remplacer `YOUR-SUBSCRIPTION-KEY` par votre clé.

```csharp
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));
```

Maintenant, vous pouvez utiliser le client pour envoyer une demande de recherche. Veillez à remplacer votre `YOUR-CUSTOM-CONFIG-ID` par l’ID de configuration de votre instance (vous trouverez l’ID dans le [portail de recherche personnalisée](https://www.customsearch.ai/)). Cet exemple recherche Xbox. Apportez des changements si besoin.

```csharp
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
```

La méthode `SearchAsync` retourne un objet `WebData`. Utilisez `WebData` pour itérer au sein des `WebPages` qui ont été trouvés. Ce code recherche le premier résultat de page web et affiche le `Name` et l’`URL` de la page web.

```csharp
//WebPages
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Couldn't find web results!");
    }
}
else
{
    Console.WriteLine("Didn't see any Web data..");
}

```


## <a name="next-steps"></a>Étapes suivantes

Consultez les exemples du SDK. Chaque exemple comprend un fichier ReadMe avec des détails sur les prérequis et l’installation/exécution des exemples.

* Bien commencer avec les [exemples .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 
    * [Package NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)
    * Voir aussi les [bibliothèques .NET](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingCustomSearch) pour les définitions et les dépendances.
* Bien commencer avec les [exemples NodeJS](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Voir aussi les [bibliothèques NodeJs](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/customSearch) pour les définitions et les dépendances.
* Bien commencer avec les [exemples Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Voir aussi [bibliothèques Java](https://github.com/Azure/azure-sdk-for-java/tree/master/cognitiveservices/azure-customsearch) pour les définitions et les dépendances.
* Bien commencer avec les [exemples Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Voir aussi les [bibliothèques Python](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-customsearch) pour les définitions et les dépendances.

