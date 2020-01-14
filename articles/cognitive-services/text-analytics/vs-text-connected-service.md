---
title: 'Tutoriel : Se connecter au service Analyse de texte avec des services connectés dans Visual Studio'
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser la fonctionnalité Service connecté de Visual Studio pour le service Analyse de texte.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 317a5f4ae642dc2306ae84c42a042206b9059ba7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446154"
---
# <a name="tutorial-connect-to-the-text-analytics-service-with-connected-services-in-visual-studio"></a>Tutoriel : Se connecter au service Analyse de texte avec des services connectés dans Visual Studio

Le service Analyse de texte vous permet d’extraire de riches informations afin de catégoriser et de traiter des données visuelles et d’effectuer une modération des images assistée par ordinateur pour organiser vos services.

Cet article et ceux qui l’accompagnent donnent des détails sur l’utilisation de la fonctionnalité Service connecté Visual Studio du service Analyse de texte. Elle est disponible dans Visual Studio 2019 et les versions ultérieures, à condition que l’extension Cognitive Services soit installée.

## <a name="prerequisites"></a>Conditions préalables requises

- Un abonnement Azure. Si vous n’en possédez pas, vous pouvez vous inscrire pour créer dès aujourd’hui un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2019 avec installation de la charge de travail Développement web. [Téléchargez-le maintenant](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-the-text-analytics-service"></a>Ajouter la prise en charge du service Analyse de texte à votre projet

1. Créez un projet web ASP.NET Core nommé TextAnalyticsDemo. Utilisez le modèle de projet Application web (modèle-vue-contrôleur), avec tous les paramètres par défaut. Il est important de nommer le projet MyWebApplication, afin que l’espace de noms corresponde lors de la copie du code dans le projet.  L’exemple de cet article utilise MVC, mais vous pouvez utiliser le service connecté Analyse de texte avec n’importe quel type de projet ASP.NET.

1. Dans **l’Explorateur de solutions**, double-cliquez sur l’élément **Service connecté**.
   La page Service connecté s’affiche et montre les services qui peuvent être ajoutés au projet.

   ![Capture d’écran de Service connecté dans l’Explorateur de solutions](../media/vs-common/Connected-Services-Solution-Explorer.PNG)

1. Dans le menu des services disponibles, choisissez **Evaluate Sentiment with Text Analytics** (Évaluer le sentiment avec Analyse de texte).

   ![Capture d’écran de l’écran Services connectés](./media/vs-text-connected-service/Cog-Text-Connected-Service-0.PNG)

   Si vous êtes connecté à Visual Studio et si un abonnement Azure est associé à votre compte, une page s’affiche et montre la liste déroulante de vos abonnements.

   ![Capture d’écran du service connecté Analyse de texte](media/vs-text-connected-service/Cog-Text-Connected-Service-1.PNG)

1. Sélectionnez l’abonnement que vous souhaitez utiliser. Ensuite, choisissez un nom pour le service Analyse de texte ou cliquez sur le lien **Modifier** pour modifier le nom généré automatiquement. Choisissez le groupe de ressources et le niveau tarifaire.

   ![Capture d’écran des champs de groupe de ressources et de niveau tarifaire](media/vs-text-connected-service/Cog-Text-Connected-Service-2.PNG)

   Pour plus d’informations sur les niveaux tarifaires, suivez le lien.

1. Cliquez sur **Ajouter** pour ajouter la prise en charge du Service connecté.
   Visual Studio modifie le projet en ajoutant les packages NuGet, les entrées du fichier de configuration et autres afin de prendre en charge une connexion au service Analyse de texte. La **fenêtre Sortie** affiche le journal des événements relatifs au projet. La sortie doit ressembler à celle-ci :

   ```output
    [6/1/2018 3:04:02.347 PM] Adding Text Analytics to the project.
    [6/1/2018 3:04:02.906 PM] Creating new Text Analytics...
    [6/1/2018 3:04:06.314 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Language' version 1.0.0-preview...
    [6/1/2018 3:04:56.759 PM] Retrieving keys...
    [6/1/2018 3:04:57.822 PM] Updating appsettings.json setting: 'ServiceKey' = '<service key>'
    [6/1/2018 3:04:57.827 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.1'
    [6/1/2018 3:04:57.832 PM] Updating appsettings.json setting: 'Name' = 'TextAnalyticsDemo'
    [6/1/2018 3:05:01.840 PM] Successfully added Text Analytics to the project.
    ```
 
## <a name="use-the-text-analytics-service-to-detect-the-language-for-a-text-sample"></a>Utilisez le service Analyse de texte pour détecter la langue d’un exemple de texte.

1. Ajoutez les instructions using suivantes dans Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Net.Http;
   using System.Net.Http.Headers;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   ```
 
1. Ajoutez un champ configuration, puis un constructeur qui l’initialise dans la classe Startup pour activer la configuration de votre programme.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Ajoutez un fichier de classe dans le dossier *Contrôleurs* appelé `DemoTextAnalyzeController` et remplacez son contenu par le code suivant :

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Extensions.Configuration;
    using TextAnalyticsDemo.Models;
    
    namespace TextAnalyticsDemo.Controllers
    {
        public class DemoTextAnalyzeController : Controller
        {
            private IConfiguration configuration;
    
            public DemoTextAnalyzeController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }
    
            public IActionResult TextAnalyzeResult(TextAnalyzeModel model)
            {
    
                if (!string.IsNullOrWhiteSpace(model.TextStr))
                {
                    ITextAnalyticsAPI client = this.GetTextAnalyzeClient(new MyHandler());
                    model.AnalyzeResult = client.DetectLanguage(
                        new BatchInput(
                            new List<Input>()
                            {
                                new Input("id",model.TextStr)
                            }));
                }
                return View(model);
            }
    
            [HttpPost("Analyze")]
            public IActionResult Analyze(TextAnalyzeModel model)
            {
                return RedirectToAction("TextAnalyzeResult", model);
            }
    
            // Using the ServiceKey from the configuration file,
            // get an instance of the Text Analytics client.
            private ITextAnalyticsAPI GetTextAnalyzeClient(DelegatingHandler handler)
            {
                string key = configuration.GetSection("CognitiveServices")["TextAnalytics:ServiceKey"];
    
                ITextAnalyticsAPI client = new TextAnalyticsAPI( handlers: handler);
                client.SubscriptionKey = key;
                client.AzureRegion = AzureRegions.Westus;
    
                return client;
            }
        }
    }
    ```
    
    Le code inclut `GetTextAnalyzeClient` afin d’obtenir l’objet client pour effectuer des appels à l’API Analyse de texte, et un gestionnaire de requêtes qui appelle DetectLanguage sur un texte donné.

1. Ajoutez la classe d’assistance MyHandler utilisée par le code précédent.

    ```csharp
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

1. Dans le dossier *Modèles*, ajoutez une classe pour le modèle.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    
    namespace Demo.Models
    {
        public class TextAnalyzeModel
        {
            public string TextStr { get; set; }
    
            public LanguageBatchResult AnalyzeResult { get; set; }
    
            public SentimentBatchResult AnalyzeResult2 { get; set; }
        }
    }
    ```

1. Ajoutez une vue pour afficher le texte analysé, la langue déterminée et le score représentant le niveau de confiance dans l’analyse. Pour cela, cliquez sur le dossier **Vues**, choisissez **Ajouter**, puis **Vue**. Dans la boîte de dialogue qui apparaît, spécifiez un nom _TextAnalyzeResult_, acceptez les valeurs par défaut pour ajouter un nouveau fichier appelé _TextAnalyzeResult.cshtml_ dans le dossier **Vues**, puis copiez-y le contenu suivant :
    
    ```cshtml
    @using System
    @model Demo.Models.TextAnalyzeModel
    
    @{
        ViewData["Title"] = "TextAnalyzeResult";
    }
    
    <h2>Text Language</h2>
    
    <div class="row">
        <section>
            <form asp-controller="DemoTextAnalyze" asp-action="Analyze" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width="90%">
                    <tr>
                        <td>
                            <input type="text" name="TextStr" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Analyze</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    
    <h2>Result</h2>
    <div>
        <dl class="dl-horizontal">
            <dt>
                Text :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.TextStr)
            </dd>
            <dt>
                Language Name :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Name)
            </dd>
            <dt>
                Score :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Score)
            </dd>
        </dl>
    </div>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    
    ```
 
1. Générez et exécutez l’exemple localement. Entrez un texte pour voir quelle langue Analyse de texte détecte.
   
## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsqu’il n’est plus nécessaire, supprimez le groupe de ressources. Cette opération supprime le service cognitif et les ressources associées. Pour supprimer le groupe de ressources à l’aide du portail :

1. Entrez le nom de votre groupe de ressources dans la zone Recherche en haut du portail. Lorsque vous voyez le groupe de ressources utilisé dans ce tutoriel dans les résultats de recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Dans le champ **TYPE THE RESOURCE GROUP NAME: (TAPER LE NOM DU GROUPE DE RESSOURCES :)** , tapez le nom du groupe de ressources et sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le service Analyse de texte en lisant la [documentation sur le service Analyse de texte](index.yml).
