---
title: 'Démarrage rapide : Créer une application web qui lance le lecteur immersif avec C#'
titlesuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous générez une application web à partir de zéro et lui ajoutez les fonctions de l’API Lecteur immersif.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 3b408de6b60e7e7704ee228b52c399e5b80e3a9e
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718421"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Démarrage rapide : Créer une application web qui lance le lecteur immersif (C#)

Le [lecteur immersif](https://www.onenote.com/learningtools) est un outil conçu de façon inclusive qui implémente des techniques éprouvées pour améliorer la compréhension de la lecture.

Dans ce guide de démarrage rapide, vous allez créer une application web à partir de zéro et intégrer le lecteur immersif à l’aide du SDK Lecteur immersif. Un exemple complet fonctionnel de ce guide de démarrage rapide est disponible [ici](https://github.com/microsoft/immersive-reader-sdk/tree/master/samples/quickstart-csharp).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* Une clé d’abonnement pour le lecteur immersif. Obtenez-en une en suivant [ces instructions](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

## <a name="create-a-web-app-project"></a>Créer un projet d’application web

Créez un nouveau projet dans Visual Studio à l’aide du modèle d’application web ASP.NET Core avec modèle-vue-contrôleur intégré.

![Nouveau projet](./media/vswebapp.png)

![Nouvelle application web ASP.NET Core](./media/vsmvc.png)

## <a name="acquire-an-access-token"></a>Obtenir un jeton d’accès

Vous avez besoin de votre clé d’abonnement et d’un point de terminaison pour cette étape. Vous trouverez votre clé d’abonnement sur la page Clés de votre ressource de lecteur immersif dans le portail Azure. Vous pouvez la trouver sur la page Vue d’ensemble.

Cliquez avec le bouton droit sur le projet dans _l’Explorateur de solutions_ et choisissez **Gérer les données secrètes de l’utilisateur**. Cela ouvrira un fichier appelé _secrets.json_. Remplacez le contenu de ce fichier avec les éléments suivants, en fournissant votre clé d’abonnement et votre point de terminaison où cela est nécessaire.

```json
{
  "SubscriptionKey": YOUR_SUBSCRIPTION_KEY,
  "Endpoint": YOUR_ENDPOINT
}
```

Ouvrez _Controllers\HomeController.cs_et remplacez la classe `HomeController` par le code suivant.

```csharp
public class HomeController : Controller
{
    private readonly string SubscriptionKey;
    private readonly string Endpoint;

    public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
    {
        SubscriptionKey = configuration["SubscriptionKey"];
        Endpoint = configuration["Endpoint"];

        if (string.IsNullOrEmpty(Endpoint) || string.IsNullOrEmpty(SubscriptionKey))
        {
            throw new ArgumentNullException("Endpoint or subscriptionKey is null!");
        }
    }

    public IActionResult Index()
    {
        return View();
    }

    [Route("token")]
    public async Task<string> Token()
    {
        return await GetTokenAsync();
    }

    /// <summary>
    /// Exchange your Azure subscription key for an access token
    /// </summary>
    private async Task<string> GetTokenAsync()
    {
        using (var client = new System.Net.Http.HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
            using (var response = await client.PostAsync(Endpoint, null))
            {
                return await response.Content.ReadAsStringAsync();
            }
        }
    }
}
```

## <a name="add-sample-content"></a>Ajouter un contenu exemple

Maintenant, nous allons ajouter des exemples de contenu à cette application web. Ouvrez _Views\Home\Index.cshtml_ et remplacez le code généré automatiquement par cet exemple :

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
<script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js'></script>
<script type='text/javascript' src='https://code.jquery.com/jquery-3.3.1.min.js'></script>
<script type='text/javascript'>
    function getImmersiveReaderTokenAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/token',
                type: 'GET',
                success: token => {
                    resolve(token);
                }
            });
        });
    }

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        ImmersiveReader.launchAsync(token, content, { uiZIndex: 1000000 });
    }
</script>
}
```

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

Dans la barre de menus, sélectionnez **Déboguer > Démarrer le débogage**, ou appuyez sur **F5** pour démarrer l’application.

Dans votre navigateur, vous devriez voir :

![Exemple d'application](./media/quickstart-result.png)

Lorsque vous cliquez sur le bouton « Lecteur immersif », vous verrez le lecteur immersif lancé avec le contenu de la page.

![Lecteur immersif](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>Étapes suivantes

* Consulter le [didacticiel](./tutorial.md) pour voir que vous pouvez faire d’autre avec le SDK Lecteur immersif
* Explorer le [SDK Lecteur Immersive](https://github.com/Microsoft/immersive-reader-sdk) et la [référence du SDK Lecteur immersif](./reference.md)
