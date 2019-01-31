---
title: 'Exemple : créer une compétence personnalisée dans un pipeline de recherche cognitive - Recherche Azure'
description: Illustre l’utilisation de l’API Traduire le texte dans une compétence personnalisée mappée à un pipeline d’indexation de recherche cognitive dans Recherche Azure.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: c49a6320b7ba37297a7c1c0775aef2c2d8c6c868
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104336"
---
# <a name="example-create-a-custom-skill-using-the-text-translate-api"></a>Exemple : créer une compétence personnalisée à l’aide de l’API Traduire le texte

Cet exemple montre comment créer une compétence personnalisée d’API web qui accepte un texte dans une langue quelconque et le traduit en anglais. L’exemple utilise une [Fonction Azure](https://azure.microsoft.com/services/functions/) pour inclure dans un wrapper l’[API Traduire le texte](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) de façon à ce qu’elle implémente l’interface de la compétence personnalisée.

## <a name="prerequisites"></a>Prérequis

+ Si vous ne connaissez pas l’interface d’entrée/sortie qu’une compétence personnalisée doit implémenter, lisez l’article sur l’[interface de compétence personnalisée](cognitive-search-custom-skill-interface.md).

+ [Inscrivez-vous pour l’API Traduire le texte](../cognitive-services/translator/translator-text-how-to-signup.md) afin d’obtenir une clé API permettant de l’utiliser.

+ Installez [Visual Studio 2017 version 15.5](https://www.visualstudio.com/vs/) ou version ultérieure, incluant la charge de travail de développement Azure.

## <a name="create-an-azure-function"></a>Création d’une fonction Azure

Bien que cet exemple utilise une Fonction Azure pour héberger une API web, elle n’est pas indispensable.  Pour autant que vous respectiez les [exigences d’interface pour une compétence cognitive](cognitive-search-custom-skill-interface.md), l’approche que vous adoptez est sans importance. Toutefois, Azure Functions facilite la création d’une compétence personnalisée.

### <a name="create-a-function-app"></a>Créer une application de fonction

1. Dans Visual Studio, dans le menu Fichier, sélectionnez **Nouveau** > **Projet**.

1. Dans la boîte de dialogue Nouveau projet, sélectionnez **Installé**, développez **Visual C#** > **Cloud**, sélectionnez **Azure Functions**, tapez un Nom pour votre projet, puis cliquez sur **OK**. Le nom d’application de la fonction doit être valide en tant qu’espace de noms C#, afin de ne pas utiliser des traits d’union, des traits de soulignement ou d’autres caractères non alphanumériques.

1. Sélectionnez **Azure Functions v2 (.NET Core)**. Vous pouvez également utiliser la version 1, mais le code écrit ci-dessous est basé sur le modèle v2.

1. Sélectionnez le type **Déclencheur HTTP**

1. Pour Compte de stockage, vous pouvez sélectionner **Aucun**, car vous n’aurez besoin d’aucun stockage pour cette fonction.

1. Sélectionnez **OK** pour créer le projet de fonction et la fonction HTTP déclenchée.

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>Modifier le code pour appeler le service cognitif de traduction

Visual Studio crée un projet qui contient une classe qui présente un code réutilisable pour le type de fonction sélectionné. L’attribut *FunctionName* de la méthode définit le nom de la fonction. L’attribut *HttpTrigger* spécifie que la fonction est déclenchée par une requête HTTP.

À présent, remplacez tout le contenu du fichier *Function1.cs* par le code suivant :

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
        static string path = "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "<enter your api key here>";

        #region classes used to serialize the response
        private class WebApiResponseError
        {
            public string message { get; set; }
        }

        private class WebApiResponseWarning
        {
            public string message { get; set; }
        }

        private class WebApiResponseRecord
        {
            public string recordId { get; set; }
            public Dictionary<string, object> data { get; set; }
            public List<WebApiResponseError> errors { get; set; }
            public List<WebApiResponseWarning> warnings { get; set; }
        }

        private class WebApiEnricherResponse
        {
            public List<WebApiResponseRecord> values { get; set; }
        }
        #endregion

        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string toLanguage = null;
            string translatedText = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Validation
            if (data?.values == null)
            {
                return new BadRequestObjectResult(" Could not find values array");
            }
            if (data?.values.HasValues == false || data?.values.First.HasValues == false)
            {
                // It could not find a record, then return empty values array.
                return new BadRequestObjectResult(" Could not find valid records in values array");
            }

            recordId = data?.values?.First?.recordId?.Value as string;
            originalText = data?.values?.First?.data?.text?.Value as string;
            toLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            translatedText = TranslateText(originalText, toLanguage).Result;
        
            // Put together response.
            WebApiResponseRecord responseRecord = new WebApiResponseRecord();
            responseRecord.data = new Dictionary<string, object>();
            responseRecord.recordId = recordId;
            responseRecord.data.Add("text", translatedText);

            WebApiEnricherResponse response = new WebApiEnricherResponse();
            response.values = new List<WebApiResponseRecord>();
            response.values.Add(responseRecord);

            return (ActionResult)new OkObjectResult(response);
        }


        /// <summary>
        /// Use Cognitive Service to translate text from one language to another.
        /// </summary>
        /// <param name="originalText">The text to translate.</param>
        /// <param name="toLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string originalText, string toLanguage)
        {
            System.Object[] body = new System.Object[] { new { Text = originalText } };
            var requestBody = JsonConvert.SerializeObject(body);

            var uri = $"{path}&to={toLanguage}";

            string result = "";

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                dynamic data = JsonConvert.DeserializeObject(responseBody);
                result = data?.First?.translations?.First?.text?.Value as string;

            }
            return result;
        }
    }
}
```

Veillez à entrer votre propre valeur *key* dans la méthode *TranslateText* en fonction de la clé que vous avez obtenue lors de l’inscription à l’API Traduire le texte.

Cet exemple est un enrichisseur simple qui ne fonctionne que sur un enregistrement à la fois. Cela devient important par la suite, lors de la définition de la taille du lot pour le jeu de compétences.

## <a name="test-the-function-from-visual-studio"></a>Tester la fonction à partir de Visual Studio

Appuyez sur **F5** pour exécuter le programme et tester le comportement de la fonction. Dans ce cas, nous allons utiliser la fonction ci-dessous pour traduire un texte de l’espagnol vers l’anglais. Utilisez Postman ou Fiddler pour émettre un appel tel que celui ci-dessous :

```http
POST https://localhost:7071/api/Translate
```
### <a name="request-body"></a>Corps de la demande
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```
### <a name="response"></a>response
Vous devriez voir une réponse similaire à l’exemple suivant :

```json
{
    "values": [
        {
            "recordId": "a1",
            "data": {
                "text": "This is a contract in English"
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publier la fonction sur Azure

Lorsque vous êtes satisfait du comportement de la fonction, vous pouvez la publier.

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**. Choisissez **Créer** > **Publier**.

1. Si vous n’avez pas encore connecté Visual Studio à votre compte Azure, sélectionnez **Ajouter un compte...**.

1. Suivez les invitations qui s’affichent à l’écran. Vous êtes invité à spécifier le compte Azure, le groupe de ressources, le plan d’hébergement et le compte de stockage que vous souhaitez utiliser. Si vous n’en avez pas de groupe de ressources, de plan d’hébergement et de compte de stockage, vous pouvez les créer. Quand vous avez terminé, sélectionnez **Créer**.

1. Une fois le déploiement terminé, notez l’URL du site. Il s’agit de l’adresse de votre application de fonction dans Azure. 

1. Dans le [portail Azure](https://portal.azure.com), accédez au groupe de ressources, puis recherchez la fonction de traduction que vous avez publiée. Dans la section **Gérer**, vous devriez voir Host Keys. Sélectionnez l’icône **Copier** pour la clé d’hôte *par défaut*.  

## <a name="test-the-function-in-azure"></a>Tester la fonction dans Azure

À présent que vous disposez de la clé d’hôte par défaut, testez votre fonction comme suit :

```http
POST https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]
```
### <a name="request-body"></a>Corps de la requête
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```

Cela devrait produire un résultat similaire à celui que vous avez obtenu précédemment lors de l’exécution de la fonction dans l’environnement local.

## <a name="connect-to-your-pipeline"></a>Connexion à votre pipeline
À présent que vous avez une nouvelle compétence personnalisée, vous pouvez l’ajouter à vos jeu de compétences. L’exemple ci-dessous montre comment appeler la compétence. Comme la compétence ne gère pas les lots, ajoutez une instruction pour définir la taille maximale de lot sur ```1``` afin d’envoyer les documents un par un.

```json
{
    "skills": [
      ...,  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new translator custom skill",
        "uri": "http://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]",
        "batchSize":1,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/destinationLanguage"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "translatedText"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes
Félicitations ! Vous avez créé votre premier enrichisseur personnalisé. Vous pouvez maintenant suivre le même schéma pour ajouter vos propres fonctionnalités personnalisées. 

+ [Ajouter une compétence personnalisée à un pipeline de recherche cognitive](cognitive-search-custom-skill-interface.md)
+ [Guide pratique pour définir un jeu de compétences](cognitive-search-defining-skillset.md)
+ [Créer un jeu de compétences (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Guide pratique pour mapper des champs enrichis](cognitive-search-output-field-mapping.md)
