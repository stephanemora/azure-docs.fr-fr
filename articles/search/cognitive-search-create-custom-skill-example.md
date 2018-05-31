---
title: 'Exemple : créer une compétence personnalisée dans un pipeline de recherche cognitive (Recherche Azure) | Microsoft Docs'
description: Illustre l’utilisation de l’API Traduire le texte dans une compétence personnalisée mappée à un pipeline d’indexation de recherche cognitive dans Recherche Azure.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: a295bf741862bb58a86234b5c85f48d7a1b52be7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786858"
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

1. Sélectionnez le type **Déclencheur HTTP**

1. Pour Compte de stockage, vous pouvez sélectionner **Aucun**, car vous n’aurez besoin d’aucun stockage pour cette fonction.

1. Sélectionnez **OK** pour créer le projet de fonction et la fonction HTTP déclenchée.

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>Modifier le code pour appeler le service cognitif de traduction

Visual Studio crée un projet qui contient une classe qui présente un code réutilisable pour le type de fonction sélectionné. L’attribut *FunctionName* de la méthode définit le nom de la fonction. L’attribut *HttpTrigger* spécifie que la fonction est déclenchée par une requête HTTP.

À présent, remplacez tout le contenu du fichier *Function1.cs* par le code suivant :

```csharp
using System.IO;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
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


        /// <summary>
        /// Note that this function can translate up to 1000 characters. If you expect to need to translate more characters, use 
        /// the paginator skill before calling this custom enricher
        /// </summary>
        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req, 
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string originalLanguage = null;
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
            originalLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            // Only translate records that actually need to be translated. 
            if (!originalLanguage.Contains("en"))
            {
                translatedText = TranslateText(originalText, "en-us").Result;
            }
            else
            {
                // text is already in English.
                translatedText = originalText;
            }

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
        /// Use Cognitive Service to translate text from one language to antoher.
        /// </summary>
        /// <param name="myText">The text to translate</param>
        /// <param name="destinationLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string myText, string destinationLanguage)
        {
            string host = "https://api.microsofttranslator.com";
            string path = "/V2/Http.svc/Translate";

            // NOTE: Replace this example key with a valid subscription key.
            string key = "064d8095730d4a99b49f4bcf16ac67f8";

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

            List<KeyValuePair<string, string>> list = new List<KeyValuePair<string, string>>() {
                new KeyValuePair<string, string>(myText, "en-us")
            };

            StringBuilder totalResult = new StringBuilder();

            foreach (KeyValuePair<string, string> i in list)
            {
                string uri = host + path + "?to=" + i.Value + "&text=" + System.Net.WebUtility.UrlEncode(i.Key);

                HttpResponseMessage response = await client.GetAsync(uri);

                string result = await response.Content.ReadAsStringAsync();

                // Parse the response XML
                System.Xml.XmlDocument xmlResponse = new System.Xml.XmlDocument();
                xmlResponse.LoadXml(result);
                totalResult.Append(xmlResponse.InnerText); 
            }

            return totalResult.ToString();
        }
    }
}
```

Veillez à entrer votre propre valeur *key* dans la méthode *TranslateText* en fonction de la clé que vous avez obtenue lors de l’inscription à l’API Traduire le texte.

Cet exemple est un enrichisseur simple qui ne fonctionne que sur un enregistrement à la fois. Cela devient important par la suite, lors de la définition de la taille du lot pour le jeu de compétences.

## <a name="test-the-function-from-visual-studio"></a>Tester la fonction à partir de Visual Studio

Appuyez sur **F5** pour exécuter le programme et tester le comportement de la fonction. Utilisez Postman ou Fiddler pour émettre un appel tel que celui ci-dessous :

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
               "language": "es"
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
               "language": "es"
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
            "source": "/document/languageCode"
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
Félicitations ! Vous avez créé votre premier enrichisseur personnalisé. Vous pouvez maintenant suivre le même schéma pour ajouter vos propres fonctionnalités personnalisées. 

+ [Ajouter une compétence personnalisée à un pipeline de recherche cognitive](cognitive-search-custom-skill-interface.md)
+ [Guide pratique pour définir un jeu de compétences](cognitive-search-defining-skillset.md)
+ [Créer un jeu de compétences (REST)](ref-create-skillset.md)
+ [Guide pratique pour mapper des champs enrichis](cognitive-search-output-field-mapping.md)