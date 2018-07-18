---
title: Tutoriel d’application mobile Recherche visuelle
description: Ouvrez l’application C# source implémentant la recherche visuelle à l’aide de l’API Vision par ordinateur Cognitive Services, l’API Recherche web Bing et le framework multiplateforme Xamarin.Forms.
services: bing-web-search, computer-vision
author: Aristoddle
manager: bking
ms.service: cognitive-services
ms.devlang: csharp
ms.topic: article
ms.date: 06/22/2017
ms.author: t-jolan
ms.openlocfilehash: 54dabaeaad2e49ba7cc138636054bc3dfa4b8379
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368597"
---
# <a name="visual-search-mobile-app-tutorial"></a>Tutoriel d’application mobile Recherche visuelle

## <a name="introduction"></a>Introduction  
Ce tutoriel explore les points de terminaison [d’API Vision par ordinateur](https://azure.microsoft.com/services/cognitive-services/computer-vision/) et [d’API Recherche web Bing](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/), et montre comment ils peuvent être utilisés pour générer une application de recherche visuelle de base avec [Xamarin.Forms](https://developer.xamarin.com/guides/xamarin-forms/).  Globalement, ce tutoriel couvre les sujets suivants : 

> [!div class="checklist"]
> * Configuration de Visual Studio pour développer des applications Xamarin.Forms
> * Utilisation du [plug-in Xamarin Media](https://github.com/jamesmontemagno/MediaPlugin) pour capturer et importer des données d’images
> * Analyse du texte d’une image à l’aide des API Vision par ordinateur
> * Envoi de demandes de recherche à l’API Recherche web Bing
> * Analyse des réponses JSON des deux API à l’aide de [Json.NET](https://github.com/JamesNK/Newtonsoft.Json) (avec LINQ et la désérialisation de l’objet de modèle)
> * Création d’une interface utilisateur Xamarin.Forms multiplateforme pour la recherche visuelle 

## <a name="prerequisites"></a>Prérequis

Cet exemple a été développé à l’aide de Xamarin.Forms avec [Visual Studio 2017](https://www.visualstudio.com/downloads/). La version Community Edition de Visual Studio 2017 peut être utilisée. Pour plus d’informations sur l’utilisation de Xamarin avec Visual Studio, consultez la [documentation Xamarin](https://developer.xamarin.com/guides/cross-platform/getting_started/).

Cet exemple utilise les packages NuGet suivants :

> [!div class="checklist"]
> * [Plug-in Xamarin Media](https://github.com/jamesmontemagno/MediaPlugin)
> * [Json.NET](https://github.com/JamesNK/Newtonsoft.Json)

L’application utilise les API Cognitive Services suivantes :

> [!div class="checklist"]
> * [API Recherche web Bing](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) 
> * [AP Vision par ordinateur](https://azure.microsoft.com/services/cognitive-services/computer-vision/)

Afin d’obtenir des clés pour un essai gratuit de 30 jours de ces API, consultez [Essayer Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Pour plus d’informations sur l’obtention de clés pour un usage commercial, consultez [Tarifs](https://azure.microsoft.com/pricing/calculator/).

## <a name="setup-for-development"></a>Configuration du développement  

### <a name="installing-xamarin-on-windows"></a>Installation de Xamarin sur Windows
Avec n’importe quelle édition de Visual Studio 2017 installée, ouvrez Visual Studio Installer, sélectionnez le menu Hamburger associé à votre installation de Visual Studio et choisissez **Modifier**.  

![Visual Studio Installer](./media/computer-vision-web-search-tutorial/visual-studio-installer.png) 

Faites défiler vers le bas jusqu’à Mobile et jeux, puis activez **Développement mobile en .NET** si ce n’est pas déjà fait.

![Visual Studio Installer avec Xamarin.Forms sélectionné](./media/computer-vision-web-search-tutorial/xamarin-forms-is-enabled.png)

Enfin, cliquez sur **Modifier** dans le coin inférieur droit de la fenêtre et attendez que Xamarin soit installé.

### <a name="installing-xamarin-on-macos"></a>Installation de Xamarin sur macOS
Xamarin est pré-intégré à Visual Studio pour Mac. Aucune installation ne doit être nécessaire.

## <a name="building-and-running-the-app"></a>Génération et exécution de l’application

Un fichier solution Visual Studio *(.sln)* pour l’application de recherche visuelle peut être téléchargé depuis [Application de recherche visuelle avec Cognitive Services](https://azure.microsoft.com/resources/samples/cognitive-services-xamarin-forms-computer-vision-search/). Vous pouvez télécharger l’archive ZIP à l’aide d’un navigateur web, la cloner sur votre station de travail à partir de GitHub ou la télécharger à l’aide de Visual Studio.

Pour commencer à utiliser l’exemple, ouvrez `VisualSearchApp.sln` dans Visual Studio.  L’initialisation des composants requis peut prendre quelques instants.

L’application nécessite deux bibliothèques tierces : **Json.NET** et le **plug-in Xamarin Media**. Vous pouvez installer ces bibliothèques directement dans Visual Studio avec le Gestionnaire de package NuGet. Choisissez **Outils > Gestionnaire de package NuGet > Gérer les packages NuGet pour la solution**, ou cliquez avec le bouton droit sur la solution dans l’Explorateur de solutions et choisissez **Gérer les packages NuGet** dans le menu contextuel.

Dans la fenêtre NuGet, recherchez et installez le **plug-in Xamarin Media** (Xam.Plugin.Media) version 2.6.2 et **Json.NET** (Newtonsoft.Json) 10.0.3. Veillez à sélectionner tous les projets lors de l’installation.

Pour générer l’application pour toutes les plateformes disponibles, appuyez sur **Ctrl+Maj+B** ou cliquez sur **Générer** dans le menu du ruban, puis sélectionnez **Générer la solution**.  Pour compiler et tester le code pour iOS à partir d’un système Windows, consultez [ce guide](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/) pour obtenir de l’aide.

Avant d’exécuter l’application, vous devez sélectionner un projet, une plateforme et une configuration cibles.  Les applications Xamarin.Forms sont compilées en code natif pour Windows, Android et iOS. Ce tutoriel inclut des captures d’écran de la version Windows de l’exemple, mais toutes les versions sont fonctionnellement équivalentes. Les paramètres de déploiement utilisés dans ce guide sont présentés ici.  

![Visual Studio configuré pour la compilation pour un téléphone Android](./media/computer-vision-web-search-tutorial/configuration-selection.png)

Une fois que le processus de génération a réussi et que vous avez sélectionné une plateforme cible, cliquez sur le bouton **Démarrer** dans la barre d’outils ou appuyez sur **F5**. Visual Studio déploie la solution sur votre plateforme cible et la lance.

La page Add Keys (Ajouter des clés) s’affiche. (Cette page est définie dans `AddKeysPage.xaml`.)  

![Image de la page Ajouter des clés](./media/computer-vision-web-search-tutorial/add-keys-page.png)  

Collez ici vos clés API Vision par ordinateur et Recherche web Bing. L’API Vision par ordinateur nécessite également que vous choisissiez le serveur qui héberge le point de terminaison.

> [!TIP]
> Pour ignorer cette page, entrez ces informations aux emplacements appropriés dans `App.xaml.cs`. 

L’application valide vos clés en exécutant une requête de test, puis vous dirige vers la page OCR Select (Sélection OCR) (définie dans `OcrSelectPage.xaml`).
   
![Image de la page Sélection OCR](./media/computer-vision-web-search-tutorial/ocr-select-page.png)  

En haut de cet écran, vous pouvez choisir si le texte à reconnaître est imprimé ou manuscrit.

> [!TIP]
> Maintenez l’élément duquel vous tentez de reconnaître le texte aussi horizontal que possible et vérifiez qu’il est éclairé uniformément sans reflets. Nous avons constaté que la reconnaissance OCR de texte manuscrit fonctionne parfois mieux pour les polices de script ou tout autre texte « sophistiqué ».

Cliquez ensuite sur **Prendre une photo** ou **Import Photo** (Importer une photo) pour prendre une photo à l’aide de l’appareil photo de votre appareil ou choisir une photo stockée sur votre appareil.

Une fois que vous avez pris ou choisi une photo, l’image est passée à l’API Vision par ordinateur. La page Words Found (Mots trouvés) (définie dans `OcrResultsPage.xaml`) affiche tous les mots reconnus dans l’image.

![Image de la page Mots trouvés](./media/computer-vision-web-search-tutorial/ocr-results-page.png)  

> [!NOTE]
> L’image que nous avons utilisée pour ces résultats se trouve dans le dépôt source sous le nom `SamplePhotos\TestImage.jpg`.

Quand vous cliquez sur un élément de la page Mots trouvés, la page Web Results (Résultats sur le web) (`WebResultsPage.xaml`) apparaît et vous indique les premiers résultats Bing pour cette recherche.

![Image de la page Résultats sur le web](./media/computer-vision-web-search-tutorial/web-results-page.png)  
Enfin, choisissez un élément dans la page Résultats sur le web pour ouvrir le lien dans un affichage web incorporé. (Vous pouvez aussi naviguer vers l’arrière pour choisir un autre résultat.)

![Image de la page Web View (Affichage web)](./media/computer-vision-web-search-tutorial/web-view-page.png)  

Vous pouvez interagir avec la page comme vous le feriez dans n’importe quel navigateur web ou revenir à la page Résultats sur le web. 

## <a name="review-and-learn"></a>Passer en revue et apprendre

Maintenant que vous avez testé l’application de recherche visuelle, voyons exactement comment utiliser les deux API Microsoft Cognitive Services.  Que vous utilisiez cet exemple comme point de départ pour votre propre application ou simplement comme procédure pour les API, il est utile de parcourir l’application écran par écran pour examiner avec précision son fonctionnement.

### <a name="add-keys-page"></a>Page Ajouter des clés
L’interface utilisateur de la page Ajouter des clés est définie dans `AddKeysPage.xaml`, et sa logique principale est définie dans `AddKeysPage.xaml.cs`. Comme les clés sont validées en exécutant une requête de test, attendez le moment opportun pour décider du mode d’utilisation des points de terminaison Cognitive Services en C#.  

La structure de base de cette interaction est la suivante : 

1. Instanciez les objets *HttpResponseMessage* et *HttpClient* à partir de *System.Net.Http*
2. Joignez les en-têtes de votre choix (définis dans les informations de référence sur l’API de chaque point de terminaison) à l’objet *HttpClient*
3. Envoyez une demande GET ou POST avec vos données, en ajoutant tous les paramètres nécessaires à l’URI de votre point de terminaison
4. Vérifiez que la réponse est correcte
5. Transmettez la réponse en vue d’un traitement supplémentaire

La fonction qui vérifie la validité de la clé API Recherche Bing est `CheckBingSearchKey()`, illustrée ici.

```csharp
async Task CheckBingSearchKey(object sender = null, EventArgs e = null)
{
    HttpResponseMessage response;
    HttpClient SearchApiClient = new HttpClient();

    SearchApiClient.DefaultRequestHeaders.Add(AppConstants.OcpApimSubscriptionKey, BingSearchKeyEntry.Text);

    try
    {
        response = await SearchApiClient.GetAsync(AppConstants.BingWebSearchApiUrl + "q=test");

        if (response.StatusCode != System.Net.HttpStatusCode.Unauthorized)
        {
            BingSearchKeyEntry.BackgroundColor = Color.Green;
            AppConstants.BingWebSearchApiKey = BingSearchKeyEntry.Text;
            bingSearchKeyWorks = true;
        }
        else
        {
            BingSearchKeyEntry.BackgroundColor = Color.Red;
            bingSearchKeyWorks = false;
        }
    }
    catch( Exception exception )
    {
        BingSearchKeyEntry.BackgroundColor = Color.Red;
        Console.WriteLine($"ERROR: {exception.Message}");
    }
}
```

Une fonction similaire, `CheckComputerVisionKey()`, est utilisée pour valider la clé Vision par ordinateur.

### <a name="ocr-select-page"></a>Page Sélection OCR

La page Sélection OCR (`OcrSelectPage.xaml`) a deux rôles importants. Tout d’abord, elle détermine le type de reconnaissance OCR effectué sur la photo cible. Ensuite, elle permet à l’utilisateur de capturer ou d’ouvrir l’image à traiter.

La seconde tâche est généralement fastidieuse dans une application multiplateforme, car chaque plateforme nécessite un code différent. Le plug-in Xamarin Media la gère en quelques lignes de code seulement.

La fonction suivante comprend un exemple d’utilisation du plug-in Xamarin Media pour la capture des photos.  Nous allons ainsi :

1. Vérifier qu’un appareil photo est disponible sur l’appareil actuel
2. Instancier un objet `StoreCameraMediaOptions` pour spécifier l’emplacement où enregistrer l’image capturée
3. Capturer une image et obtenir un objet `MediaFile` contenant les données d’images
4. Décompresser `MediaFile` en un tableau d’octets
5. Retourner le tableau d’octets en vue d’un traitement supplémentaire

Voici `TakePhoto()`, la fonction qui utilise le plug-in Xamarin Media pour la capture des photos.  

```csharp
async Task<byte[]> TakePhoto()
{
    MediaFile photoMediaFile = null;
    byte[] photoByteArray = null;

    if (CrossMedia.Current.IsCameraAvailable)
    {
        var mediaOptions = new StoreCameraMediaOptions
        {
            PhotoSize = PhotoSize.Medium,
            AllowCropping = true,
            SaveToAlbum = true,
            Name = $"{DateTime.UtcNow}.jpg"
        };
        photoMediaFile = await CrossMedia.Current.TakePhotoAsync(mediaOptions);
        photoByteArray = MediaFileToByteArray(photoMediaFile);
    }
    else
    {
        await DisplayAlert("Error", "No camera found", "OK");
        Console.WriteLine($"ERROR: No camera found");
    }
    return photoByteArray;
}
```
L’utilitaire d’importation des photos fonctionne de la même façon. Les deux fonctionnalités se trouvent dans `OcrSelectPage.xaml.cs`. 
 
> [!NOTE]
> Le point de terminaison de reconnaissance OCR de texte manuscrit ne peut gérer que des photos dont la taille est inférieure à 4 Mo. Pour éviter les problèmes de taille de fichier, nous réduisons la photo à 50 % de sa taille d’origine en définissant l’option `PhotoSize = PhotoSize.Medium` sur l’objet `StoreCameraMediaOptions`.  Si votre appareil prend des photos d’une qualité exceptionnelle et que vous obtenez des erreurs, vous pouvez essayer `PhotoSize = PhotoSize.Small` à la place.

Voici la fonction utilitaire qui permet de convertir *MediaFile* en tableau d’octets.

```csharp
byte[] MediaFileToByteArray(MediaFile photoMediaFile)
{
    using (var memStream = new MemoryStream())
    {
        photoMediaFile.GetStream().CopyTo(memStream);
        return memStream.ToArray();
    }
}
```

### <a name="ocr-results-page"></a>Page OCR Results (Résultats OCR)

La page Résultats OCR affiche le texte extrait de l’image à l’aide de la [méthode SelectToken](http://www.newtonsoft.com/json/help/html/SelectToken.htm) **Json.NET**.  Les deux points de terminaison OCR fonctionnant un peu différemment, il est utile d’aborder les deux.  

Comme l’API Vision par ordinateur est hébergée uniquement à quelques emplacements du serveur, son point de terminaison doit être construit dynamiquement lors de l’exécution. La fonction `SetOcrLocation` (qui fait partie de la classe *AppConstants* statique qui figure dans `AppConstants.cs`) définit l’emplacement du point de terminaison d’URI. Il correspond à la sélection de l’utilisateur dans la page Ajouter des clés ou utilise la valeur définie dans `App.xaml.cs`. 

```csharp
public static void SetOcrLocation(string location)
{
    ComputerVisionApiOcrUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/ocr?language=en&detectOrientation=true";
    ComputerVisionApiHandwritingUrl = $"https://{location}.api.cognitive.microsoft.com/vision/v1.0/recognizeText?handwriting=true";
}
```

Examinons ces requêtes d’API de plus près. L’API OCR Vision par ordinateur est capable d’analyser du texte d’un langage indéterminé, mais nous lui demandons de rechercher du texte en anglais pour améliorer les résultats. Nous laissons également l’API détecter l’orientation du texte pour nous. L’utilisation d’une orientation fixe peut améliorer les résultats de notre analyse, mais la détection de l’orientation peut être utile dans une application mobile.

Pour plus d’informations sur les paramètres disponibles avec ce point de terminaison, consultez [Informations de référence sur l’API de reconnaissance optique des caractères de texte imprimé](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fc).  

L’API OCR de texte manuscrit est toujours en préversion et fonctionne pour l’instant uniquement avec du texte en anglais.  Pour cette raison, son seul paramètre est actuellement un indicateur qui signale s’il convient ou non d’analyser le texte manuscrit. L’API OCR de texte manuscrit peut analyser à la fois le texte manuscrit et le texte imprimé, mais `handwriting=false` produit de meilleurs résultats sur le texte imprimé. 

Comme cette application est en anglais, nous pourrions avoir utilisé uniquement la reconnaissance OCR de texte manuscrit pour cet exemple et défini l’indicateur `handwriting` en fonction de ce que l’utilisateur nous demande de reconnaître.  Nous avons utilisé les deux points de terminaison à des fins d’illustration.  

Pour plus d’informations sur les paramètres disponibles avec ce point de terminaison, consultez [Informations de référence sur l’API de reconnaissance optique des caractères de texte manuscrit](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200).

À présent, examinons les fonctions qui appellent ces API.

`FetchPrintedWordList()` utilise le point de terminaison OCR de texte imprimé pour analyser le texte imprimé à partir d’images. La requête HTTP suit une structure similaire à l’appel utilisé dans la page Ajouter des clés pour valider la clé, mais nous devons envoyer une photo. Une photo étant trop grande pour passer dans une chaîne de requête, nous devons utiliser une demande HTTP POST au lieu d’une demande GET. Nous devons encoder notre photo (qui existe en mémoire comme un tableau d’octets) dans un objet `ByteArrayContent` et ajouter un en-tête à cet objet pour définir le type de données que nous envoyons. 

Pour plus d’informations sur les types de contenu acceptables, consultez [Informations de référence sur l’API Vision par ordinateur](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/587f2c6a154055056008f200).  

```csharp
async Task<ObservableCollection<string>> FetchPrintedWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiOcrUrl, content);
        }

        string ResponseString = await response.Content.ReadAsStringAsync();
        JObject json = JObject.Parse(ResponseString);

        // Here, we pull down each "line" of text and then join it to 
        // make a string representing the entirety of each line.  
        // In the Handwritten endpoint, you are able to extract the 
        // "line" without any further processing.  If you would like 
        // to simply get a list of all extracted words,* you can do 
        // this with:
        // json.SelectTokens("$.regions[*].lines[*].words[*].text) 
        IEnumerable<JToken> lines = json.SelectTokens("$.regions[*].lines[*]");
        if (lines != null)
        {
            foreach (JToken line in lines)
            {
                IEnumerable<JToken> words = line.SelectTokens("$.words[*].text");
                if (words != null)
                {
                    wordList.Add(string.Join(" ", words.Select(x => x.ToString())));
                }
            }
        }
    }
    return wordList;
}
```
> [!TIP]
> Notez la façon dont nous utilisons la [méthode SelectToken](http://www.newtonsoft.com/json/help/html/SelectToken.htm) **Json.NET** pour extraire le texte de l’objet réponse. La méthode `SelectToken` est idéale quand nous avons besoin uniquement d’une partie spécifique de la réponse JSON, que nous pouvons ensuite transmettre à la fonction suivante. Dans d’autres parties du code, nous désérialisons les réponses JSON en objets de modèle définis dans `ModelObjects.cs`.

La principale différence entre la demande OCR de texte imprimé et la demande OCR de texte manuscrit est que le service OCR de texte imprimé retourne le texte reconnu en tant que partie de la réponse HTTP, tandis que le service OCR de texte manuscrit requiert une demande supplémentaire pour obtenir les informations. La demande OCR de texte manuscrit initiale retourne un état HTTP 202, qui indique uniquement que le traitement a commencé. Cette réponse contient un point de terminaison que le client doit vérifier pour obtenir la réponse terminée quand elle est disponible. Consultez `FetchHandwrittenWordList()` pour voir comment cela fonctionne.

```csharp
async Task<ObservableCollection<string>> FetchHandwrittenWordList()
{
    ObservableCollection<string> wordList = new ObservableCollection<string>();
    if (photo != null)
    {
        // Make the POST request to the handwriting recognition URL
        HttpResponseMessage response = null;
        using (var content = new ByteArrayContent(photo))
        {
            // The media type of the body sent to the API. 
            // "application/octet-stream" defines an image represented 
            // as a byte array
            content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
            response = await visionApiClient.PostAsync(AppConstants.ComputerVisionApiHandwritingUrl, content);
        }

        // Fetch results
        IEnumerable<string> operationLocationValues;
        string statusUri = string.Empty;
        if (response.Headers.TryGetValues("Operation-Location", out operationLocationValues))
        {
            statusUri = operationLocationValues.FirstOrDefault();

            // Ping status URL, wait for processing to finish 
            JObject obj = await FetchResultFromStatusUri(statusUri);
            IEnumerable<JToken> strings = obj.SelectTokens("$.recognitionResult.lines[*].text");
            foreach (string s in strings)
            {
                wordList.Add((string)s);
            }
        }
    }
    return wordList;
}
```

`FetchResultFromStatusUri()` est la deuxième partie du processus OCR d’écriture manuscrite. Cette fonction envoie une commande ping à l’URI extrait des métadonnées de la réponse initiale jusqu’à l’obtention d’un résultat ou l’expiration de la fonction.  Il est important que cette fonction soit appelée de façon asynchrone sur son propre thread. Sinon, cette méthode pourrait verrouiller l’interface utilisateur jusqu’à la fin du traitement.

```csharp
// Takes in the url to check for handwritten text parsing results, and pings it per second until processing is finished
// Returns the JObject holding data for a successful parse
async Task<JObject> FetchResultFromStatusUri(string statusUri)
{
    JObject obj = null;
    int timeoutcounter = 0;
    HttpResponseMessage response = await visionApiClient.GetAsync(statusUri);
    string responseString = await response.Content.ReadAsStringAsync();
    obj = JObject.Parse(responseString);
    while ((!((string)obj.SelectToken("status")).Equals("Succeeded")) && (timeoutcounter++ < 60))
    {
        await Task.Delay(1000);
        response = await visionApiClient.GetAsync(statusUri);
        responseString = await response.Content.ReadAsStringAsync();
        obj = JObject.Parse(responseString);
    } 
    return obj;
}
```

### <a name="web-results-page"></a>Page Résultats sur le web
Une fois que l’utilisateur a sélectionné un terme recherché affiché dans la page Résultats OCR, nous passons à la page Résultats sur le web.  Ici, nous créons une requête [d’API Recherche web Bing](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/) que nous envoyons au point de terminaison du service, et désérialisons la réponse JSON en utilisant la méthode [DeserializeObject](http://www.newtonsoft.com/json/help/html/DeserializeObject.htm) **Json.NET**.  

```csharp
async Task<WebResultsList> GetQueryResults()
{
    // URL-encode the query term
    var queryString = System.Net.WebUtility.UrlEncode(queryTerm);

    // Here we encode the URL that will be used for the GET request to 
    // find query results.  Its arguments are as follows:
    // 
    // - [count=20] This sets the number of webpage objects returned at 
    //   "$.webpages" in the JSON response.  Currently, the API asks for 
    //   20 webpages in the response
    //
    // - [mkt=en-US] This sets the market where the results come from.
    //   Currently, the API looks for english results based in the 
    //   United States.
    //
    // - [q=queryString] This sets the string queried using the Search 
    //   API.   
    //
    // - [responseFilter=Webpages] This filters the response to only 
    //   include Webpage results.  This tag can take a comma seperated 
    //   list of response types that you are looking for.  If left 
    //   blank, all responses (webPages, News, Videos, etc) are 
    //   returned.
    //
    // - [setLang=en] This sets the languge for user interface strings. 
    //   To learn more about UI strings, check the Web Search API 
    //   reference.
    //
    // - [API Reference] https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference
    string uri = AppConstants.BingWebSearchApiUrl + $"count=20&mkt=en-US&q={queryString}&responseFilter=Webpages&setLang=en";

    // Make the HTTP Request
    WebResultsList webResults = null;
    HttpResponseMessage httpResponseMessage = await searchApiClient.GetAsync(uri);
    var responseContentString = await httpResponseMessage.Content.ReadAsStringAsync();
    JObject json = JObject.Parse(responseContentString);
    JToken resultBlock = json.SelectToken("$.webPages");
    if (resultBlock != null)
    {
        webResults = JsonConvert.DeserializeObject<WebResultsList>(resultBlock.ToString());
    }
    return webResults;
}
```

L’API Recherche web Bing fonctionne mieux quand vous fournissez autant d’informations que possible sur les choix éventuels de l’utilisateur. En particulier, vous devez presque toujours utiliser les paramètres `mkt` et `setLang` (définis ici pour l’anglais) afin d’identifier l’emplacement et la langue par défaut de l’utilisateur.

> [!NOTE]
> Notre requête Recherche web Bing est restée simple afin de garantir que le code source a été facile à comprendre.  Dans une application réelle, vous devez ajouter les en-têtes suivants à votre requête HTTP pour obtenir de meilleurs résultats. 
> * User-Agent  
> * X-MSEdge-ClientID  
> * X-Search-ClientIP  
> * X-Search-Location  
>
> Pour plus d’informations sur ces valeurs d’en-tête, consultez [Informations de référence sur l’API Recherche web Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers)

## <a name="next-steps"></a>Étapes suivantes
Microsoft Cognitive Services fournit de nombreux services supplémentaires que vous pouvez facilement intégrer à cette application.  Par exemple, vous pouvez :

* Ajouter [Recherche d’entités Bing](https://azure.microsoft.com/services/cognitive-services/bing-entity-search-api/) pour enrichir vos résultats de recherche web
* Échanger [Recherche personnalisée Bing](https://azure.microsoft.com/services/cognitive-services/bing-custom-search/) contre Recherche web Bing
* Utiliser la fonctionnalité d’informations sur les images [Recherche d’images Bing](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) pour en savoir plus sur votre image capturée et rechercher des images similaires sur le web
* Utiliser [Vérification orthographique Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/) pour améliorer davantage la qualité de votre texte analysé
* Intégrer [Microsoft Translator](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) pour voir votre texte extrait dans différentes langues
* Combiner les autres services à partir du [portail Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) ; tout est possible !
