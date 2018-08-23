---
title: Charger et indexer vos vidéos avec Azure Video Indexer | Microsoft Docs
description: Cette rubrique montre comment utiliser les API pour charger et indexer vos vidéos avec Azure Video Indexer
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 08/17/2018
ms.author: juliako
ms.openlocfilehash: 8a9409c46cac8397bc449c586374729a4d864036
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2018
ms.locfileid: "41931333"
---
# <a name="upload-and-index-your-videos"></a>Charger et indexer vos vidéos  

Cet article montre comment utiliser l’API [Charger une vidéo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) pour charger et indexer vos vidéos avec Azure Video Indexer. Il aborde également certains des paramètres que vous pouvez définir sur l’API pour en modifier le processus et la sortie.

> [!Note]
> Lorsque vous créez un compte Video Indexer, vous pouvez choisir un compte d’essai gratuit (où vous obtenez un certain nombre de minutes d’indexation gratuites) ou une option payante (où vous n’êtes pas limités par le quota). <br/>Avec l’essai gratuit, Video Indexer fournit jusqu’à 600 heures d’indexation gratuite aux utilisateurs du site web et jusqu’à 2 400 heures d’indexation gratuite aux utilisateurs de l’API. <br/>Avec l’option payante, vous créez un compte Video Indexer [connecté à votre abonnement Azure et un compte Azure Media Services](connect-to-azure.md). Vous payez pour les minutes indexées, ainsi que pour les frais liés au compte média. 

## <a name="configurations-and-params"></a>Configurations et paramètres

Cette section décrit certains des paramètres facultatifs et les moments où vous souhaitez les définir.

### <a name="externalid"></a>externalID 

Ce paramètre vous permet de spécifier un ID qui sera associé à la vidéo. L’ID peut être appliqué à l’intégration de système externe « Gestion de contenu vidéo» (VCM). Les vidéos situées dans le portail Video Indexer peuvent faire l’objet d’une recherche à l’aide de l’ID externe spécifié.

### <a name="indexingpreset"></a>indexingPreset

Utilisez ce paramètre si des enregistrements bruts ou externes contiennent un bruit de fond. Ce paramètre est utilisé pour configurer le processus d’indexation. Vous pouvez spécifier les valeurs suivantes :

- `Default` – Permet d’indexer et d’extraire des insights de l’audio et de la vidéo
- `AudioOnly` – Permet d’indexer et d’extraire des insights de l’audio uniquement (la vidéo est ignorée)
- `DefaultWithNoiseReduction` – Permet d’indexer et d’extraire des insights de l’audio et vidéo, tout en appliquant des algorithmes de réduction du bruit sur les flux audio

Le prix dépend de l’option d’indexation sélectionnée.  

### <a name="callbackurl"></a>callbackUrl

Une URL POST pour notifier la fin de l’indexation. Video Indexer y ajoute deux paramètres de la chaîne de requête : l’ID et l’état. Par exemple, si l’URL de rappel est « https://test.com/notifyme?projectName=MyProject », la notification sera envoyée avec des paramètres supplémentaires vers « https://test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed ».

Vous pouvez également ajouter davantage de paramètres à l’URL avant de publier l’appel auprès de Video Indexer et ces paramètres figureront dans le rappel. Plus tard, dans votre code, vous pouvez analyser la chaîne de requête et obtenir en rappel tous les paramètres spécifiés dans la chaîne de requête (les données que vous aviez initialement ajoutées à l’URL et les informations fournies par Video Indexer.) 

### <a name="streamingpreset"></a>streamingPreset

Une fois votre vidéo chargée, Video Indexer peut éventuellement l’encoder. Il passe ensuite à l’indexation et à l’analyse de la vidéo. Lorsque Video Indexer a terminé l’analyse, vous recevrez une notification avec l’ID de la vidéo.  

Lorsque vous utilisez l’API [Charger une vidéo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [Réindexer une vidéo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?), un des paramètres facultatifs est `streamingPreset`. Si vous définissez `streamingPreset` sur `Default`, `SingleBitrate`, ou `AdaptiveBitrate`, le processus d’encodage est déclenché. Une fois les travaux d’indexation et d’encodage effectués, la vidéo est publiée et vous pouvez donc la diffuser. Le point de terminaison de streaming à partir duquel vous souhaitez diffuser la vidéo doit se trouver dans l’état **En cours d’exécution**.

Pour exécuter les travaux d’indexation et d’encodage, le [compte Azure Media Services connecté à votre compte Video Indexer](connect-to-azure.md), nécessite des unités réservées. Pour plus d’informations, consultez [Mise à l’échelle du traitement multimédia](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview). Dans la mesure où il s’agit de tâches de calcul intensif, un type d’unité S3 est fortement recommandé. Le nombre d’unités de demande définit le nombre maximal de travaux pouvant s’exécuter en parallèle. La suggestion de base de référence est 10 unités de demande S3. 

Si vous souhaitez uniquement indexer votre vidéo sans l’encoder, définissez `streamingPreset` sur `NoStreaming`.

## <a name="code-sample"></a>Exemple de code

L’extrait de code C# suivant illustre l’utilisation conjointe de toutes les API Video Indexer.

```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var location = "westus2";
    var apiKey = "...";

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    // take the relevant account, here we simply take the first
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Debug.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =newbyte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(newByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video id from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Debug.WriteLine("Uploaded");
    Debug.WriteLine("Video ID:");
    Debug.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

        Debug.WriteLine("");
        Debug.WriteLine("State:");
        Debug.WriteLine(processingState);

        // job is finished
        if (processingState != "Uploaded" && processingState != "Processing")
        {
            Debug.WriteLine("");
            Debug.WriteLine("Full JSON:");
            Debug.WriteLine(videoGetIndexResult);
            break;
        }
    }

    // search for the video
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Debug.WriteLine("");
    Debug.WriteLine("Search:");
    Debug.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Debug.WriteLine("Insights Widget url:");
    Debug.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
    Debug.WriteLine("");
    Debug.WriteLine("Player Widget url:");
    Debug.WriteLine(playerWidgetLink);
}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```



## <a name="next-steps"></a>Étapes suivantes

[Examiner la sortie de Azure Video Indexer générée par l’API v2](video-indexer-output-json-v2.md)
