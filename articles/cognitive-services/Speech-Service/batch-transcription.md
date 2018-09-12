---
title: API de transcription Azure Batch
description: Exemples
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: b6fb39ef5941157cfe0d18324deeb9d836d7ab09
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377619"
---
# <a name="batch-transcription"></a>Transcription Batch

La transcription Batch convient parfaitement si vous avez d’importants volumes de données audio. Vous pouvez pointer vers des fichiers audios et d’en récupérer les transcriptions en mode asynchrone.

## <a name="batch-transcription-api"></a>API de transcription Batch

L’API de transcription Batch offre une transcription de parole en texte asynchrone, ainsi que des fonctionnalités supplémentaires.

> [!NOTE]
> L’API de transcription Batch constitue la solution idéale pour les centres d’appels qui accumulent généralement plusieurs milliers d’heures de données audio. L’API est guidée par une philosophie de type « Fire & Forget » (fonctionnement autonome après déclenchement), facilitant la transcription d’importants volumes d’enregistrements audio.

### <a name="supported-formats"></a>Formats pris en charge

L’API de transcription Batch prend en charge les formats suivants :

NOM| Canal  |
----|----------|
mp3 |   Mono   |   
mp3 |  Stéréo  | 
wav |   Mono   |
wav |  Stéréo  |

Dans le cas des flux audio stéréo, l’API de transcription Batch fractionne les canaux gauche et droit lors de la transcription. Elle crée deux fichiers JSON contenant le résultat relatif à chacun de ces deux canaux. Les timestamps par énoncé permettent au développeur de créer une transcription finale ordonnée chronologiquement. L’exemple JSON ci-après illustre la sortie d’un canal.

```json
       {
        "recordingsUrl": "https://mystorage.blob.core.windows.net/cris-e2e-datasets/TranscriptionsDataset/small_sentence.wav?st=2018-04-19T15:56:00Z&se=2040-04-21T15:56:00Z&sp=rl&sv=2017-04-17&sr=b&sig=DtvXbMYquDWQ2OkhAenGuyZI%2BYgaa3cyvdQoHKIBGdQ%3D",
        "resultsUrls": {
            "channel_0": "https://mystorage.blob.core.windows.net/bestor-87a0286f-304c-4636-b6bd-b3a96166df28/TranscriptionData/24265e4c-e459-4384-b572-5e3e7795221f?sv=2017-04-17&sr=b&sig=IY2qd%2Fkgtz2PwRe2C88BphH4Hv%2F1VCb1UVJ33xsw%2BEY%3D&se=2018-04-23T14:48:24Z&sp=r"
        },
        "statusMessage": "None.",
        "id": "0bb95356-ff06-469d-acc7-81f9144a269a",
        "createdDateTime": "2018-04-20T14:11:57.167",
        "lastActionDateTime": "2018-04-20T14:12:54.643",
        "status": "Succeeded",
        "locale": "en-US"
    },
```

> [!NOTE]
> L’API de transcription Batch utilise un service REST pour demander des transcriptions, l’état de ces dernières, ainsi que les résultats associés. Vous pouvez utiliser l’API dans n’importe quelle langue. La section ci-après décrit le mode d’utilisation de l’API.

## <a name="authorization-token"></a>Jeton d’autorisation

Comme pour toutes les fonctionnalités du service Speech unifié, vous créez une clé d’abonnement à partir du [Portail Azure](https://portal.azure.com). En outre, vous obtenez une clé API à partir du portail de reconnaissance vocale : 

1. Connectez-vous à [Custom Speech](https://customspeech.ai).

2. Sélectionnez **Abonnements**.

3. Sélectionnez **Générer une clé API**.

    ![Capture d’écran de la page Abonnements de discours personnalisé](media/stt/Subscriptions.jpg)

4. Copiez et collez la clé dans l’exemple de code client ci-après.

> [!NOTE]
> Si vous prévoyez d’utiliser un modèle personnalisé, vous devrez également disposer de l’ID de ce modèle. Notez qu’il ne s’agit pas de l’ID de déploiement ou de point de terminaison que vous trouvez sur l’affichage des détails du point de terminaison. Il s’agit de l’ID de modèle que vous pouvez récupérer lorsque vous sélectionnez les détails du modèle.

## <a name="sample-code"></a>Exemple de code

Personnalisez l’exemple de code suivant avec une clé d’abonnement et une clé API. Cela vous permet d’obtenir un jeton du porteur.

```cs
    public static async Task<CrisClient> CreateApiV1ClientAsync(string username, string key, string hostName, int port)
        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;

            var tokenProviderPath = "/oauth/ctoken";
            var clientToken = await CreateClientTokenAsync(client, hostName, port, tokenProviderPath, username, key).ConfigureAwait(false);
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("bearer", clientToken.AccessToken);

            return new CrisClient(client);
        }
```

Une fois le jeton obtenu, vous devez spécifier l’URI SAP qui pointe vers le fichier audio à transcrire. Le reste du code effectue une itération dans l’état et affiche les résultats.

```cs
   static async Task TranscribeAsync()
        { 
            private const string SubscriptionKey = "<your Speech[Preview] subscription key>";
            private const string HostName = "cris.ai";
            private const int Port = 443;
    
            // Creating a Batch transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);

            // get the transcription Id from the location URI
            var createdTranscriptions = new List<Guid>();
            createdTranscriptions.Add(new Guid(transcriptionLocation.ToString().Split('/').LastOrDefault()))

            while (true)
            {
                // get all transcriptions for the user
                transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);
                completed = 0; running = 0; notStarted = 0;

                // for each transcription in the list we check the status
                foreach (var transcription in transcriptions)
                {
                    switch(transcription.Status)
                    {
                        case "Failed":
                        case "Succeeded":

                            // we check to see if it was one of the transcriptions we created from this client.
                            if (!createdTranscriptions.Contains(transcription.Id))
                            {
                                // not creted form here, continue
                                continue;
                            }
                            
                            completed++;
                            
                            // if the transcription was successfull, check the results
                            if (transcription.Status == "Succeeded")
                            {
                                var resultsUri = transcription.ResultsUrls["channel_0"];
                                WebClient webClient = new WebClient();
                                var filename = Path.GetTempFileName();
                                webClient.DownloadFile(resultsUri, filename);
                                var results = File.ReadAllText(filename);
                                Console.WriteLine("Transcription succedded. Results: ");
                                Console.WriteLine(results);
                            }
                            break;
                        case "Running":
                            running++;
                            break;
                        case "NotStarted":
                            notStarted++;
                            break;
                    }
                }

                Console.WriteLine(string.Format("Transcriptions status: {0} completed, {1} running, {2} not started yet", completed, running, notStarted));

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }

            Console.WriteLine("Press any key...");
        }
```

> [!NOTE]
> Dans le code précédent, la clé d’abonnement est la clé de la ressource Speech (préversion) que vous créez dans le Portail Azure. Les clés obtenues à partir de la ressource Custom Speech Service ne fonctionnent pas.

Notez la configuration asynchrone concernant la publication des données audio et la réception de l’état de la transcription. Le client créé est un client http .NET. Le code utilise une méthode `PostTranscriptions` pour l’envoi des détails du fichier audio, et une méthode `GetTranscriptions` pour la réception des résultats. `PostTranscriptions` renvoie un descripteur, et `GetTranscriptions` l’utilise pour créer un descripteur permettant d’obtenir l’état de la transcription.

L’exemple de code actuel ne spécifie aucun modèle personnalisé. Le service utilise les modèles de base pour la transcription du ou des fichiers. Pour spécifier les modèles, vous pouvez transmettre à la même méthode les ID du modèle acoustique et du modèle de langage. 

Si vous ne voulez pas utiliser le modèle de base, vous devez transmettre à la fois l’ID du modèle acoustique et l’ID du modèle de langage.

> [!NOTE]
> Dans le cas d’une transcription de base, vous n’avez pas besoin de déclarer les points de terminaison des modèles de base. Si vous souhaitez utiliser des modèles personnalisés, vous fournissez les ID de point de terminaison de ces modèles en guise [d’exemple de code](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Si vous voulez utiliser un modèle acoustique de base avec un modèle de langage de base, vous devez simplement déclarer l’ID de point de terminaison du modèle personnalisé. Microsoft détecte le modèle de base partenaire (modèle acoustique ou de langage) et l’utilise pour traiter la requête de transcription.

### <a name="supported-storage"></a>Stockage pris en charge

Pour l’instant, le seul stockage pris en charge est le Stockage Blob Azure.

## <a name="downloading-the-sample"></a>Téléchargement de l’exemple

L’exemple montré ici est disponible sur [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Généralement, le temps nécessaire à l’exécution d’une transcription audio correspond à la durée du fichier audio plus un laps de temps supplémentaire de 2 à 3 minutes.

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
