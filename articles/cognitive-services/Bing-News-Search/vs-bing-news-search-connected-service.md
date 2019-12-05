---
title: Se connecter à l’API Recherche d’actualités Bing avec les Services connectés dans Visual Studio et C#
titleSuffix: Azure Cognitive Services
description: La Recherche d’actualités Bing permet à des applications et services d’exploiter toute la puissance d’un moteur de recherche sans publicité à l’échelle du web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: tutorial
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: 2afe9a66d9f0ebecaef9626ef6caf9fc30f20841
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74771068"
---
# <a name="tutorial-connect-to-bing-news-search-api-with-connected-services-in-visual-studio-and-c"></a>Didacticiel : Se connecter à l’API Recherche d’actualités Bing avec les Services connectés dans Visual Studio et C#

La Recherche d’actualités Bing permet à des applications et services d’exploiter toute la puissance d’un moteur de recherche sans publicité à l’échelle du web. Elle fait partie des services de recherche disponibles avec Cognitive Services.

Cet article donne des détails sur l’utilisation de la fonctionnalité Service connecté Visual Studio pour la Recherche d’actualités Bing. Elle est disponible dans Visual Studio 2017 15.7 et les versions ultérieures, à condition que l’extension Cognitive Services soit installée.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’en possédez pas, vous pouvez vous inscrire pour créer dès aujourd’hui un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2019 avec installation de la charge de travail Développement web. [Téléchargez-le maintenant](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>Ajouter la prise en charge de l’API Recherche d’actualités Bing à un projet

1. Créez un projet web ASP.NET Core nommé « MyWebApplication ». Utilisez le modèle de projet **Application web (modèle-vue-contrôleur)** , avec tous les paramètres par défaut. Il est important de nommer le projet MyWebApplication, afin que l’espace de noms corresponde lors de la copie du code dans le projet. 

1. Dans **l’Explorateur de solutions**, choisissez **Ajouter** > **Service connecté**.
   La page Service connecté s’affiche et montre les services qui peuvent être ajoutés au projet.

   ![Capture d’écran de l’élément de menu Ajouter un Service connecté](../media/vs-common/Connected-Service-Menu.PNG)

1. Dans le menu des services disponibles, choisissez **Intégrer la recherche intelligente à des applications**.

   ![Capture d’écran de la liste de Services connectés](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   Si vous êtes connecté à Visual Studio et si un abonnement Azure est associé à votre compte, une page s’affiche et montre la liste déroulante de vos abonnements. Sélectionnez l’abonnement que vous souhaitez utiliser, puis choisissez un nom pour l’API Recherche d’actualités Bing. Vous pouvez également choisir **Modifier** pour modifier le nom généré automatiquement.

   ![Capture d’écran des champs d’abonnement et de nom](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. Choisissez le groupe de ressources et le niveau tarifaire.

   ![Capture d’écran des champs de groupe de ressources et de niveau tarifaire](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   Si vous souhaitez de plus amples informations sur les niveaux tarifaires, sélectionnez **Consulter les tarifs**.

1. Cliquez sur **Ajouter** pour ajouter la prise en charge du Service connecté.
   Visual Studio modifie le projet en ajoutant les packages NuGet, les entrées du fichier de configuration et autres afin de prendre en charge une connexion à l’API Recherche d’actualités Bing. La sortie affiche le journal des événements relatifs au projet. Un résultat tel que celui-ci doit s’afficher :

   ```output
   [5/4/2018 12:41:21.084 PM] Adding Intelligent Search to the project.
   [5/4/2018 12:41:21.271 PM] Creating new Intelligent Search...
   [5/4/2018 12:41:24.128 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.ImageSearch' version 1.2.0...
   [5/4/2018 12:41:24.135 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.NewsSearch' version 1.2.0...
   [5/4/2018 12:41:24.154 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.WebSearch' version 1.2.0...
   [5/4/2018 12:41:24.168 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.CustomSearch' version 1.2.0...
   [5/4/2018 12:41:24.187 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.VideoSearch' version 1.2.0...
   [5/4/2018 12:42:07.287 PM] Retrieving keys...
   [5/4/2018 12:42:07.741 PM] Updating appsettings.json setting: 'ServiceKey' = 'c271412f3e4c4e1dacc7c4145fa0572a'
   [5/4/2018 12:42:07.745 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://api.cognitive.microsoft.com/bing/v7.0'
   [5/4/2018 12:42:07.749 PM] Updating appsettings.json setting: 'Name' = 'WebApplicationCore-Search_IntelligentSearch'
   [5/4/2018 12:42:10.217 PM] Successfully added Intelligent Search to the project.
   ```

   Le fichier appsettings.json comporte désormais les nouveaux paramètres suivants :

   ```json
   "CognitiveServices": {
     "IntelligentSearch": {
       "ServiceKey": "<your service key>",
       "ServiceEndPoint": "https://api.cognitive.microsoft.com/bing/v7.0",
       "Name": "WebApplicationCore-Search_IntelligentSearch"
     }
   }
   ```
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>Utiliser l’API Recherche d’actualités Bing pour ajouter des fonctionnalités de recherche à une page web

Maintenant que vous avez ajouté la prise en charge de l’API Recherche d’actualités Bing à votre projet, voici comment l’utiliser pour intégrer la recherche intelligente à une page web.

1. Dans *Startup.cs*, ajoutez un appel à `IServiceCollection.AddSingleton` dans la méthode `ConfigureServices`. L’objet de configuration qui contient les paramètres de clés devient ainsi accessible au code du projet.
 
   ```csharp
       public void ConfigureServices(IServiceCollection services)
       {
           services.AddMvc();
           services.AddSingleton<IConfiguration>(Configuration);
       }
   ```


1. Ajoutez un nouveau fichier de classe sous le dossier **Modèles**, appelé *BingNewsModel.cs*. Si vous avez nommé différemment votre projet, utilisez son propre espace de noms au lieu de MyWebApplication. Remplacez le contenu par le code suivant :
 
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch.Models;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace MyWebApplication.Models
    {
        public class BingNewsModel
        {
            public News SearchResult { get; set; } 
            public string SearchText { get; set; }
        }
    }
    ```

   Ce modèle permet de stocker les résultats d’un appel au service Recherche d’actualités Bing.
 
1. Dans le dossier **Contrôleurs**, ajoutez un nouveau fichier de classe nommé *IntelligentSearchController.cs*. Remplacez le contenu par le code suivant :

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using MyWebApplication.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    using Microsoft.Extensions.Configuration;
    
    namespace MyWebApplication.Controllers
    {
        // A controller to handle News Search requests
        public class IntelligentSearchController : Controller
        {
            private IConfiguration configuration;
  
            // Set up the configuration that contains the keys
            // (from the appsettings.json file)
            // that you will use to access the service  
            public IntelligentSearchController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }

            // Call the Bing News Search API and put the result in the model object.    
            public async Task<IActionResult> BingSearchResult(BingNewsModel model)
            {
                if (!string.IsNullOrWhiteSpace(model.SearchText))
                { 
                    INewsSearchAPI client = this.GetNewsSearchClient(new MyHandler());
                    model.SearchResult = await client.News.SearchAsync(model.SearchText);
                }
                return View(model);
            }
    
            // Forward requests to the Search endpoint to the BingSearchResult method
            [HttpPost("Search")]
            public IActionResult Search(BingNewsModel model)
            {
                return RedirectToAction("BingSearchResult", model);
            }

            // Get the search client object
            private INewsSearchAPI GetNewsSearchClient(DelegatingHandler handler)
            {
                string key =
                   configuration.GetSection("CognitiveServices")["IntelligentSearch:ServiceKey"];
    
                INewsSearchAPI client = new NewsSearchAPI(
                   new ApiKeyServiceClientCredentials(key), handlers: handler);
    
                return client;
            }
        }
    }
   ```

   Dans ce code, le constructeur définit l’objet de configuration qui contient vos clés. La méthode de l’itinéraire `Search` est simplement une redirection vers la fonction `BingSearchResult`. Elle appelle la méthode `GetNewsSearchClient` pour récupérer l’objet client `NewsSearchAPI`.  L’objet client `NewsSearchAPI` contient la méthode `SearchAsync`, qui appelle le service et retourne les résultats dans le modèle `SearchResult` qui vient d’être créé. 

1. Ajoutez la classe `MyHandler` à laquelle le code précédent faisait référence. Elle délègue l’appel asynchrone au service de recherche à sa classe de base, `DelegatingHandler`.

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Threading;

    class MyHandler : DelegatingHandler
    {
        protected async override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Call the inner handler.
            var response = await base.SendAsync(request, cancellationToken);
            
            return response;
        }
    }
   ```

1. Pour prendre en charge la possibilité de soumettre des recherches et d’afficher les résultats, créez un dossier nommé **IntelligentSearch** dans le dossier **Vues**. Dans ce nouveau dossier, ajoutez une vue *BingSearchResult.cshtml*. Copiez-y le code suivant :

    ```cshtml
    @using System
    @model MyWebApplication.Models.BingNewsModel
    
    @{
        ViewData["Title"] = "BingSearchResult";
    }
    
    <h2>Search News</h2>
    
    <div class="row">
        <section>
            <form asp-controller="IntelligentSearch" asp-action="Search" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width ="90%">
                    <tr>
                        <td>
                            <input type="text" name="SearchText" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Search</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    <h2>Search Result</h2=
    <table>
    @if (!string.IsNullOrEmpty(Model.SearchText)) {
        foreach (var item in Model.SearchResult.Value) {
        <tr>
            <td rowspan="2" width="90">
               <img src=@item?.Image?.Thumbnail?.ContentUrl width="80" height="80" />
            </td>
            <td><a href=@item.Url>@item.Name</a></td>
        </tr>   
        <tr>
            <td>@item.Description</td>
        </tr>
        <tr height="10">
            <td/><td/>
         </tr>
        } }
     </table>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    ```

1. Démarrez l’application web localement, entrez l’URL de la page qui vient d’être créée (/IntelligentSearch/BingSearchResult) et publiez une demande de recherche en utilisant le bouton Rechercher.

   ![Capture d’écran des résultats de la Recherche d’actualités Bing](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>Supprimer des ressources

Dès que le groupe de ressources n’est plus nécessaire, vous pouvez le supprimer. Cette opération supprime le service cognitif et les ressources associées. Pour supprimer le groupe de ressources à l’aide du portail :

1. Entrez le nom de votre groupe de ressources dans la zone de recherche en haut du portail. Recherchez le groupe de ressources à supprimer.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Dans le champ **Taper le nom du groupe de ressources**, entrez le nom du groupe de ressources et sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’API Recherche d’actualités Bing, voir [Présentation de la Recherche d’actualités Bing](search-the-web.md).
