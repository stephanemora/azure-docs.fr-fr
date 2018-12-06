---
title: Utilisation de l’API de transcription Azure Batch
titlesuffix: Azure Cognitive Services
description: Exemples de transcription de grands volumes de contenu audio.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 8a180dfada9da92e0b8ed69373a20602b3b0a177
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495589"
---
# <a name="why-use-batch-transcription"></a>Pourquoi utiliser la transcription Batch ?

La transcription Batch est idéale si vous stockez d’importants volumes de données audio. Avec l’API REST dédiée, vous pouvez pointer vers des fichiers audio par un URI de signature d’accès partagé (SAP) et recevoir les transcriptions de manière asynchrone.

## <a name="the-batch-transcription-api"></a>L’API de transcription Batch

L’API de transcription Batch offre une transcription de parole en texte asynchrone, ainsi que des fonctionnalités supplémentaires. Il s’agit d’une API REST qui expose des méthodes pour :

1. Créer des demandes de traitement par lots
1. Interroger l’état 
1. Téléchargement des transcriptions

> [!NOTE]
> L’API de transcription Batch est la solution idéale pour les centres d’appels qui accumulent généralement plusieurs milliers d’heures de données audio. L’API est guidée par une philosophie de type « Fire & Forget » (fonctionnement autonome après déclenchement), facilitant la transcription d’importants volumes d’enregistrements audio.

### <a name="supported-formats"></a>Formats pris en charge

L’API de transcription Batch prend en charge les formats suivants :

| Format | Codec | Bitrate | Échantillonnage |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bits | 8 ou 16 kHz, mono, stéréo |
| MP3 | PCM | 16 bits | 8 ou 16 kHz, mono, stéréo |
| OGG | OPUS | 16 bits | 8 ou 16 kHz, mono, stéréo |

> [!NOTE]
> L’API de transcription Batch exige une clé S0 (niveau payant). Elle ne fonctionne pas avec une clé gratuite (f0).

Dans le cas des flux audio stéréo, l’API de transcription Batch divise les canaux gauche et droit lors de la transcription. Elle crée deux fichiers JSON contenant le résultat relatif à chacun de ces deux canaux. Les timestamps par énoncé permettent au développeur de créer une transcription finale ordonnée chronologiquement. L’exemple JSON suivant montre la sortie d’un canal, notamment les propriétés pour configurer le filtre d’obscénités et le modèle de ponctuation.

```json
{
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "models": [],
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "description": "An optional description of the transcription.",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic"
  },
```

> [!NOTE]
> L’API de transcription Batch utilise un service REST pour demander des transcriptions, l’état de ces dernières, ainsi que les résultats associés. Vous pouvez utiliser l’API dans n’importe quelle langue. La section ci-après décrit le mode d’utilisation de l’API.

### <a name="query-parameters"></a>Paramètres de requête

Ces paramètres peuvent être inclus dans la chaîne de la requête REST.

| Paramètre | Description | Obligatoire/facultatif |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | Spécifie comment traiter la vulgarité dans les résultats de la reconnaissance. Les valeurs acceptées sont `none` qui désactive le d’obscénités, `masked` qui remplace les obscénités par des astérisques, `removed` qui supprime tous les obscénités du résultat ou `tags` qui ajoute les balises « obscénité ». Le paramètre par défaut est `masked`. | Facultatif |
| `PunctuationMode` | Spécifie comment traiter la ponctuation dans les résultats de la reconnaissance. Les valeurs acceptées sont `none` qui désactive la ponctuation, `dictated` qui implique une ponctuation explicite, `automatic` qui permet au décodeur de gérer la ponctuation, ou `dictatedandautomatic` qui implique des marques de ponctuation dictées ou automatiques. | Facultatif |


## <a name="authorization-token"></a>Jeton d’autorisation

Comme pour toutes les fonctionnalités du service Speech, créez une clé d’abonnement à partir du [Portail Azure](https://portal.azure.com) en suivant les instructions du [guide de démarrage rapide](get-started.md). Si vous souhaitez obtenir des transcriptions de nos modèles de base, la création de la clé est la seule opération à faire. 

Si vous souhaitez personnaliser et utiliser un modèle personnalisé, ajoutez la clé d’abonnement au portail vocal personnalisé en procédant comme suit :

1. Connectez-vous à [Custom Speech](https://customspeech.ai).

2. En haut à droite, sélectionnez **Abonnements**.

3. Sélectionnez **Connecter un abonnement existant**.

4. Dans la fenêtre qui s’affiche, ajoutez la clé d’abonnement et un alias.

    ![Fenêtre Ajouter un abonnement](media/stt/Subscriptions.jpg)

5. Copiez et collez la clé dans l’exemple de code client ci-après.

> [!NOTE]
> Si vous prévoyez d’utiliser un modèle personnalisé, vous devrez également disposer de l’ID de ce modèle. Il ne s’agit pas de l’ID de point de terminaison qui se trouve dans la vue des détails du point de terminaison. Il s’agit de l’ID de modèle que vous pouvez récupérer lorsque vous sélectionnez les détails du modèle.

## <a name="sample-code"></a>Exemple de code

Personnalisez l’exemple de code suivant avec une clé d’abonnement et une clé API. Cette action vous permet d’obtenir un jeton du porteur.

```cs
     public static CrisClient CreateApiV2Client(string key, string hostName, int port)

        {
            var client = new HttpClient();
            client.Timeout = TimeSpan.FromMinutes(25);
            client.BaseAddress = new UriBuilder(Uri.UriSchemeHttps, hostName, port).Uri;
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);
         
            return new CrisClient(client);
        }
```

Après avoir obtenu le jeton, spécifiez l’URI SAS qui pointe vers le fichier audio qui nécessite une transcription. Le reste du code effectue une itération dans l’état et affiche les résultats. Dans un premier temps, vous configurez la clé, de la région, les modèles à utiliser et l’association de sécurité, comme illustré dans l’extrait de code suivant. Ensuite, vous instanciez le client et la demande POST. 

```cs
            private const string SubscriptionKey = "<your Speech subscription key>";
            private const string HostName = "westus.cris.ai";
            private const int Port = 443;
    
            // SAS URI 
            private const string RecordingsBlobUri = "SAS URI pointing to the file in Azure Blob Storage";

            // adapted model Ids
            private static Guid AdaptedAcousticId = new Guid("guid of the acoustic adaptation model");
            private static Guid AdaptedLanguageId = new Guid("guid of the language model");

            // Creating a Batch Transcription API Client
            var client = CrisClient.CreateApiV2Client(SubscriptionKey, HostName, Port);
            
            var transcriptionLocation = await client.PostTranscriptionAsync(Name, Description, Locale, new Uri(RecordingsBlobUri), new[] { AdaptedAcousticId, AdaptedLanguageId }).ConfigureAwait(false);
```

Maintenant que vous avez effectué la demande, vous pouvez interroger et télécharger les résultats de la transcription, comme illustré dans l’extrait de code suivant :

```cs
  
            // get all transcriptions for the user
            transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

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
                            // not created from here, continue
                            continue;
                        }
                            
                        completed++;
                            
                        // if the transcription was successful, check the results
                        if (transcription.Status == "Succeeded")
                        {
                            var resultsUri = transcription.ResultsUrls["channel_0"];
                            WebClient webClient = new WebClient();
                            var filename = Path.GetTempFileName();
                            webClient.DownloadFile(resultsUri, filename);
                            var results = File.ReadAllText(filename);
                            Console.WriteLine("Transcription succeeded. Results: ");
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
            }
        }
```

Pour plus d’informations sur les appels précédents, consultez notre [document swagger](https://westus.cris.ai/swagger/ui/index). L’exemple complet présenté ici est disponible sur [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Dans le code précédent, la clé d’abonnement provient de la ressource Speech que vous créez dans le Portail Azure. Les clés obtenues à partir de la ressource Custom Speech Service ne fonctionnent pas.

Notez la configuration asynchrone concernant la publication des données audio et la réception de l’état de la transcription. Le client que vous créez est un client HTTP .NET. Il existe une méthode `PostTranscriptions` pour l’envoi des détails du fichier audio, et une méthode `GetTranscriptions` pour la réception des résultats. `PostTranscriptions` renvoie un descripteur et `GetTranscriptions` l’utilise pour créer un descripteur permettant d’obtenir l’état de la transcription.

L’exemple de code actuel ne spécifie pas de modèle personnalisé. Le service utilise les modèles de base pour la transcription du ou des fichiers. Pour spécifier les modèles, vous pouvez transmettre à la même méthode les ID du modèle acoustique et du modèle de langage. 

Si vous ne voulez pas utiliser le modèle de base, transmettez à la fois l’ID du modèle acoustique et l’ID du modèle de langage.

> [!NOTE]
> Dans le cas des transcriptions de base, vous n’avez pas besoin de déclarer les points de terminaison des modèles de base. Si vous souhaitez utiliser des modèles personnalisés, vous fournissez les ID de point de terminaison de ces modèles en guise [d’exemple de code](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Si vous voulez utiliser un modèle acoustique de base avec un modèle de langage de base, vous devez simplement déclarer l’ID de point de terminaison du modèle personnalisé. Microsoft détecte le modèle de base partenaire (modèle acoustique ou de langage) et l’utilise pour traiter la requête de transcription.

### <a name="supported-storage"></a>Stockage pris en charge

Pour l’instant, le seul stockage pris en charge est le Stockage Blob Azure.

## <a name="download-the-sample"></a>Télécharger l’exemple

Vous trouverez l’exemple dans cet article sur [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Généralement, le temps nécessaire à l’exécution d’une transcription audio correspond à la durée du fichier audio plus un laps de temps supplémentaire de 2 à 3 minutes.

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
