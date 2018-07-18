---
title: API de transcription Azure Batch | Azure Microsoft Docs
description: Exemples
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 01bbf4ca19b0fb702aa76d5149fb0e38389fe455
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054821"
---
# <a name="batch-transcription"></a>Transcription Batch

La transcription Batch convient parfaitement aux cas d’usage impliquant d’importants volumes de données audio. Elle permet au développeur de pointer vers des fichiers audio et d’en récupérer les transcriptions en mode asynchrone.

## <a name="batch-transcription-api"></a>API de transcription Batch

L’API de transcription Batch a été conçue pour prendre en charge le scénario ci-dessus. Elle offre une transcription de parole en texte asynchrone, ainsi que des fonctionnalités supplémentaires.

> [!NOTE]
> L’API de transcription Batch constitue la solution idéale pour les centres d’appels qui accumulent généralement plusieurs milliers d’heures de données audio. Grâce à sa philosophie de type « Fire & Forget » (fonctionnement autonome après déclenchement), l’API facilite la transcription d’importants volumes d’enregistrements audio.

### <a name="supported-formats"></a>Formats pris en charge

L’API de transcription Batch est amenée à devenir la référence dans tous les scénarios impliquant des centres d’appels et à prendre en charge tous les formats associés. Les formats actuellement pris en charge sont les suivants :

NOM| Canal  |
----|----------|
mp3 |   Mono   |   
mp3 |  Stéréo  | 
wav |   Mono   |
wav |  Stéréo  |

Dans le cas des flux audio stéréo, l’API de transcription Batch fractionne les canaux gauche et droit lors de la transcription. Elle crée deux fichiers JSON contenant le résultat relatif à chacun de ces deux canaux. Les timestamps par énoncé permettent au développeur de créer une transcription finale ordonnée chronologiquement. L’exemple JSON ci-après illustre la sortie d’un canal.

    ```
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
> L’API de transcription Batch utilise un service REST pour demander des transcriptions, l’état de ces dernières, ainsi que les résultats associés. Elle repose sur .NET et ne comporte aucune dépendance externe. La section ci-après décrit le mode d’utilisation de l’API.

## <a name="authorization-token"></a>Jeton d’autorisation

Comme pour toutes les fonctionnalités du service Speech unifié, l’utilisateur doit créer une clé d’abonnement à partir du [Portail Azure](https://portal.azure.com). En outre, l’utilisateur doit acquérir une clé API à partir du portail Speech. Pour générer une clé API, procédez comme suit :

1. Connectez-vous à l’adresse https://customspeech.ai.

2. Cliquez sur Subscriptions (Abonnements).

3. Cliquez sur l’option `Generate API Key`.

    ![Vue de chargement](media/stt/Subscriptions.jpg)

4. Copiez et collez la clé dans l’exemple de code client ci-après.

> [!NOTE]
> Si vous prévoyez d’utiliser un modèle personnalisé, vous devrez également disposer de l’ID de ce modèle. Notez qu’il ne s’agit pas de l’ID de déploiement ou de point de terminaison que vous pouvez visualiser dans la vue Détails des points de terminaison, mais de l’ID de modèle que vous pouvez récupérer en cliquant sur le lien Détails de ce modèle.

## <a name="sample-code"></a>Exemple de code

L’utilisation de l’API est relativement simple. Vous devez personnaliser l’exemple de code ci-après avec une clé d’abonnement et une clé API.

```cs
   static async Task TranscribeAsync()
        { 
            // Creating a Batch transcription API Client
            var client = 
                await CrisClient.CreateApiV1ClientAsync(
                    "<your msa>", // MSA email
                    "<your api key>", // API key
                    "stt.speech.microsoft.com",
                    443).ConfigureAwait(false);
            
            var newLocation = 
                await client.PostTranscriptionAsync(
                    "<selected locale i.e. en-us>", // Locale 
                    "<your subscripition key>", // Subscription Key
                    new Uri("<SAS URI to your file>")).ConfigureAwait(false);

            var transcription = await client.GetTranscriptionAsync(newLocation).ConfigureAwait(false);

            while (true)
            {
                transcription = await client.GetTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                if (transcription.Status == "Failed" || transcription.Status == "Succeeded")
                {
                    Console.WriteLine("Transcription complete!");

                    if (transcription.Status == "Succeeded")
                    {
                        var resultsUri = transcription.ResultsUrls["channel_0"];

                        WebClient webClient = new WebClient();

                        var filename = Path.GetTempFileName();
                        webClient.DownloadFile(resultsUri, filename);

                        var results = File.ReadAllText(filename);
                        Console.WriteLine(results);
                    }

                    await client.DeleteTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                    break;
                }
                else
                {
                    Console.WriteLine("Transcription status: " + transcription.Status);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }

            Console.ReadLine();
        }
```

> [!NOTE]
> La clé d’abonnement mentionnée dans l’extrait de code ci-dessus est la clé de la ressource Speech (préversion) que vous créez dans le Portail Azure. Les clés obtenues à partir de la ressource Custom Speech Service ne fonctionneront pas.


Notez la configuration asynchrone concernant la publication des données audio et la réception de l’état de la transcription. Le client créé est un client HTTP .NET. Le code utilise une méthode `PostTranscriptions` pour l’envoi des détails du fichier audio, et une méthode `GetTranscriptions` pour la réception des résultats. `PostTranscriptions` renvoie un descripteur, que la méthode `GetTranscriptions` utilise pour créer un descripteur permettant d’obtenir l’état de la transcription.

L’exemple de code actuel ne spécifie aucun modèle personnalisé. Le service utilisera les modèles de base pour la transcription du ou des fichiers. Si l’utilisateur souhaite spécifier les modèles, il peut transmettre à la même méthode les ID du modèle acoustique et du modèle de langage. 

Si l’utilisateur ne veut pas utiliser le modèle de base, il doit transmettre à la fois l’ID du modèle acoustique et l’ID du modèle de langage.

> [!NOTE]
> Dans le cas d’une transcription de base, l’utilisateur n’a pas besoin de déclarer les points de terminaison des modèles de base. Si l’utilisateur souhaite utiliser des modèles personnalisés, il doit fournir les ID de point de terminaison de ces modèles en guise [d’exemple de code](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Si l’utilisateur veut utiliser un modèle acoustique de base avec un modèle de langage de base, il lui suffit de déclarer l’ID de point de terminaison du modèle personnalisé. Notre système déterminera en interne le modèle de base partenaire (modèle acoustique ou de langage) et l’utilisera pour traiter la requête de transcription.

### <a name="supported-storage"></a>Stockage pris en charge

Pour l’instant, le seul stockage pris en charge est le Stockage Blob Azure.

## <a name="downloading-the-sample"></a>Téléchargement de l’exemple

L’exemple affiché ici est disponible sur [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Généralement, le temps nécessaire à l’exécution d’une transcription audio correspond à la durée du fichier audio plus un laps de temps supplémentaire de 2 à 3 minutes.

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit du service Speech](https://azure.microsoft.com/try/cognitive-services/)
