---
title: 'Démarrage rapide : Convertir du texte par synthèse vocale, .NET Core - Services Speech'
titleSuffix: Azure Cognitive Services
description: Dans ce démarrage rapide, vous allez apprendre à convertir la synthèse vocale avec l’API REST Synthèse vocale. L’exemple de texte inclus dans ce guide est structuré en tant que Langage de balisage de synthèse vocale (SSML). Cela vous permet de choisir la voix et la langue de la réponse de la reconnaissance vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 03/13/2019
ms.author: erhopf
ms.openlocfilehash: 5db2a1eebd5b2184ceff8793cb51f4e95b9dea38
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067588"
---
# <a name="quickstart-convert-text-to-speech-using-net-core"></a>Démarrage rapide : Convertir du texte par synthèse vocale à l’aide de .NET Core

Dans ce démarrage rapide, vous allez apprendre à convertir la synthèse vocale à l’aide de .NET Core et de l’API REST Synthèse vocale. L’exemple de texte inclus dans ce guide est structuré en tant que [Langage de balisage de synthèse vocale (SSML)](speech-synthesis-markup.md), ce qui vous permet de choisir la voix et la langue de la réponse.

Ce guide de démarrage rapide nécessite un [compte Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec une ressource Speech Services. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](https://azure.microsoft.com/try/cognitive-services/) pour obtenir une clé d’abonnement.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* [Kit de développement logiciel (SDK) .NET Core](https://dotnet.microsoft.com/download)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) ou l’éditeur de texte de votre choix
* Clé d’abonnement Azure pour le service Speech

## <a name="create-a-net-core-project"></a>Créer un projet .NET Core

Ouvrez une nouvelle invite de commandes (ou une session Terminal Server) et exécutez les commandes suivantes :

```console
dotnet new console -o tts-sample
cd tts-sample
```

La première commande effectue deux opérations. Elle crée une application console .NET et un répertoire nommé `tts-sample`. La deuxième commande bascule vers le répertoire de votre projet.

## <a name="select-the-c-language-version"></a>Sélectionner la version du langage C#

Ce démarrage rapide requiert C# 7.1 ou version ultérieure. Il existe plusieurs façons de changer la version C# de votre projet. Dans ce guide, nous allons vous montrer comment ajuster le fichier `tts-sample.csproj`. Pour toutes les options disponibles, comme la modification du langage dans Visual Studio, consultez [Sélectionner la version du langage C#](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version).

Ouvrez votre projet, puis ouvrez `tts-sample.csproj`. Vérifiez que `LangVersion` est défini sur 7.1 ou version ultérieure. En l’absence d’un groupe de propriétés pour la version du langage, ajoutez ces lignes :

```xml
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

N’oubliez pas d’enregistrer vos modifications.

## <a name="add-required-namespaces-to-your-project"></a>Ajouter les espaces de noms requis à votre projet

La commande `dotnet new console` que vous avez exécutée précédemment a créé un projet, notamment `Program.cs`. C’est dans ce fichier que vous placerez votre code d’application. Ouvrez `Program.cs` et remplacez les instructions using existantes. Ces instructions garantissent que vous avez accès à tous les types nécessaires pour générer et exécuter l’exemple d’application.

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.IO;
using System.Threading.Tasks;
```

## <a name="create-a-class-for-token-exchange"></a>Créer une classe pour l’échange de jeton

L’API REST Synthèse vocale requiert un jeton d’accès pour l’authentification. Pour obtenir un jeton d’accès, un échange est nécessaire. Cet exemple échange votre clé d’abonnement Speech Services contre un jeton d’accès utilisant le point de terminaison `issueToken`.

Cet exemple suppose que votre abonnement Speech Services se situe dans la région USA Ouest. Si vous utilisez une autre région, mettez à jour la valeur de `FetchTokenUri`. Pour obtenir la liste complète, consultez [Régions](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

```csharp
public class Authentication
{
    private string subscriptionKey;
    private string tokenFetchUri;

    public Authentication(string tokenFetchUri, string subscriptionKey)
    {
        if (string.IsNullOrWhiteSpace(tokenFetchUri))
        {
            throw new ArgumentNullException(nameof(tokenFetchUri));
        }
        if (string.IsNullOrWhiteSpace(subscriptionKey))
        {
            throw new ArgumentNullException(nameof(subscriptionKey));
        }
        this.tokenFetchUri = tokenFetchUri;
        this.subscriptionKey = subscriptionKey;
    }

    public async Task<string> FetchTokenAsync()
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(this.tokenFetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null).ConfigureAwait(false);
            return await result.Content.ReadAsStringAsync().ConfigureAwait(false);
        }
    }
}
```

> [!NOTE]
> Pour plus d’informations sur l’authentification, voir [S’authentifier avec un jeton d’accès](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="get-an-access-token-and-set-the-host-url"></a>Obtenir un jeton d’accès et définir l’URL de l’hôte

Recherchez `static void Main(string[] args)` et remplacez-le par `static async Task Main(string[] args)`.

Ensuite, copiez ce code dans la méthode principale. Il effectue plusieurs opérations, mais plus important encore, il prend le texte comme entrée et appelle la fonction `Authentication` pour échanger votre clé d’abonnement avec un jeton d’accès. Si une erreur survient, l’erreur est affichée dans la console.

Veillez à ajouter votre clé d’abonnement avant d’exécuter l’application.

```csharp
// Prompts the user to input text for TTS conversion
Console.Write("What would you like to convert to speech? ");
string text = Console.ReadLine();

// Gets an access token
string accessToken;
Console.WriteLine("Attempting token exchange. Please wait...\n");

// Add your subscription key here
// If your resource isn't in WEST US, change the endpoint
Authentication auth = new Authentication("https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken", "YOUR_SUBSCRIPTION_KEY");
try
{
    accessToken = await auth.FetchTokenAsync().ConfigureAwait(false);
    Console.WriteLine("Successfully obtained an access token. \n");
}
catch (Exception ex)
{
    Console.WriteLine("Failed to obtain an access token.");
    Console.WriteLine(ex.ToString());
    Console.WriteLine(ex.Message);
    return;
}
```

Ensuite, définissez l’hôte et l’itinéraire pour la synthèse vocale :

```csharp
string host = "https://westus.tts.speech.microsoft.com/cognitiveservices/v1";
```

## <a name="build-the-ssml-request"></a>Générer la requête SSML

Le texte est envoyé en tant que corps d’une requête `POST`. Avec SSML, vous pouvez spécifier la voix et la langue. Dans ce démarrage rapide, nous allons utiliser SSML avec la langue définie sur `en-US` et la voix définie sur `ZiraRUS`. Construisons le langage SSML pour votre requête :

```csharp
string body = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
              <voice name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>" +
              text + "</voice></speak>";
```

> [!NOTE]
> Cet exemple utilise la police de la voix `ZiraRUS`. Pour obtenir la liste complète des voix/langues fournies par Microsoft, consultez [Prise en charge de la langue](language-support.md). Si vous êtes intéressé par la création d’une voix unique, reconnaissable pour votre marque, consultez [Création de polices de la voix personnalisées](how-to-customize-voice-font.md).

## <a name="instantiate-the-client-make-a-request-and-save-synthesized-audio-to-a-file"></a>Instanciez le client, effectuez une demande et enregistrez l’audio synthétisé dans un fichier

Cet exemple de code fourmille d’actions. Passons rapidement en revue ce qui se passe :

* Le client et la requête sont instanciés.
* La méthode HTTP est définie sur `POST`.
* Les en-têtes requis sont ajoutés à la requête.
* La requête est envoyée et le code d’état est vérifié.
* La réponse est lue de façon asynchrone et écrite dans un fichier nommé sample.wav.

Copiez ce code dans votre projet. Veillez à remplacer la valeur de l’en-tête `User-Agent` par le nom de votre ressource dans le portail Azure.

```csharp
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Set the HTTP method
        request.Method = HttpMethod.Post;
        // Construct the URI
        request.RequestUri = new Uri(host);
        // Set the content type header
        request.Content = new StringContent(body, Encoding.UTF8, "application/ssml+xml");
        // Set additional header, such as Authorization and User-Agent
        request.Headers.Add("Authorization", "Bearer " + accessToken);
        request.Headers.Add("Connection", "Keep-Alive");
        // Update your resource name
        request.Headers.Add("User-Agent", "YOUR_RESOURCE_NAME");
        request.Headers.Add("X-Microsoft-OutputFormat", "riff-24khz-16bit-mono-pcm");
        // Create a request
        Console.WriteLine("Calling the TTS service. Please wait... \n");
        using (var response = await client.SendAsync(request).ConfigureAwait(false))
        {
            response.EnsureSuccessStatusCode();
            // Asynchronously read the response
            using (var dataStream = await response.Content.ReadAsStreamAsync().ConfigureAwait(false))
            {
                Console.WriteLine("Your speech file is being written to file...");
                using (var fileStream = new FileStream(@"sample.wav", FileMode.Create, FileAccess.Write, FileShare.Write))
                {
                    await dataStream.CopyToAsync(fileStream).ConfigureAwait(false);
                    fileStream.Close();
                }
                Console.WriteLine("\nYour file is ready. Press any key to exit.");
                Console.ReadLine();
            }
        }
    }
}
```

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Voilà, vous êtes prêt à exécuter votre application de synthèse vocale. À partir de la ligne de commande (ou d’une session Terminal Server), accédez au répertoire de votre projet et exécutez :

```console
dotnet run
```

En cas de réussite, le fichier vocal est enregistré dans votre dossier de projet. Vous pouvez procéder à sa lecture à l’aide du lecteur multimédia de votre choix.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous avez codé en dur votre clé d’abonnement dans votre programme, veillez à supprimer la clé d’abonnement une fois ce démarrage rapide terminé.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples .NET sur GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NETCore)

## <a name="see-also"></a>Voir aussi

* [Référence sur l’API conversion de texte par synthèse vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Création de polices de voix personnalisée](how-to-customize-voice-font.md)
* [Enregistrer des échantillons vocaux pour créer une voix personnalisée](record-custom-voice-samples.md)
