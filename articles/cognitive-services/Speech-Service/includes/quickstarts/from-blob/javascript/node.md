---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: trbye
ms.custom: devx-track-javascript
ms.openlocfilehash: 5d1d7008151ae61a72368d3d8ecfaf545a2080fa
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406166"
---
## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous de :

> [!div class="checklist"]
> * [Configurer votre environnement de développement et créer un projet vide](../../../../quickstarts/setup-platform.md?tabs=vs&pivots=programmming-language-javascript)
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Charger un fichier source dans un objet blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="create-a-new-js-file"></a>Créer un nouveau fichier JS

La première étape consiste à vérifier que votre projet est ouvert votre éditeur préféré.

Appelez votre fichier index.js.

## <a name="start-with-some-boilerplate-code"></a>Commencer avec du code réutilisable

Nous allons ajouter du code qui servira de squelette à notre projet

```JavaScript
const https = require("https");

// Replace with your subscription key
SubscriptionKey = "YourSubscriptionKey";

// Update with your service region
Region = "YourServiceRegion";
Port = 443;

// Recordings and locale
Locale = "en-US";
RecordingsBlobUri = "YourFileUrl";

// Name and description
Name = "Simple transcription";
Description = "Simple transcription description";

SpeechToTextBasePath = "/api/speechtotext/v2.0/";
```

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>Wrappers JSON

Lorsque l’API REST prend des requêtes au format JSON et retourne également des résultats au format JSON.
Pour faciliter la compréhension des requêtes et des réponses, nous allons déclarer quelques classes à utiliser pour sérialiser/désérialiser le JSON.


```JavaScript
class ModelIdentity {
    id;
}

class Transcription {
    Name;
    Description;
    Locale;
    RecordingsUrl;
    ResultsUrls;
    Id;
    CreatedDateTime;
    LastActionDateTime;
    Status;
    StatusMessage;
}

class TranscriptionDefinition {
    Name;
    Description;
    RecordingsUrl;
    Locale;
    Models;
    Properties;
}
```

## <a name="create-an-initial-transcription-request"></a>Créez une demande de transcription initiale.
Nous allons ensuite générer la demande de transcription.

```JavaScript
const ts = {
    Name: Name,
    Description: Description,
    Locale: Locale,
    RecordingsUrl: RecordingsBlobUri,
    Properties: {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True"
    },
    Models: []
}

const postPayload = JSON.stringify(ts);

const startOptions = {
    hostname: Region + ".cris.ai",
    port: Port,
    path: SpeechToTextBasePath + "Transcriptions/",
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        'Content-Length': postPayload.length,
        "Ocp-Apim-Subscription-Key": SubscriptionKey
    }
}
```

## <a name="send-the-transcription-request"></a>Envoyez la demande de transcription.
Nous allons maintenant envoyer la requête au service Speech et vérifier le code de réponse initial. Ce code de réponse indique simplement si le service a reçu la requête. Le service va retourner une URL dans les en-têtes de réponse qui correspond à l’emplacement où il va stocker l’état de la transcription.

Ensuite, nous appelons une méthode `CheckTranscriptionStatus` pour vérifier l’état et enfin imprimer les résultats. Nous allons ensuite implémenter `CheckTranscriptionStatus`.

```JavaScript
const request = https.request(startOptions, (response) => {
    if (response.statusCode != 202) {
        console.error("Error, status code " + response.statusCode);
    } else {

        const transcriptionLocation = response.headers.location;

        console.info("Created transcription at location " + transcriptionLocation);
        console.info("Checking status.");

        CheckTranscriptionStatus(transcriptionLocation);
    }
});

request.on("error", error => {
    console.error(error);
});

request.write(postPayload);
request.end();
```

## <a name="check-the-requests-status"></a>Vérifier l’état des requêtes
Étant donné que le service traite la transcription de manière asynchrone, nous devons interroger l’état régulièrement. Nous allons le vérifier toutes les 5 secondes.

Nous pouvons vérifier l’état en récupérant le contenu à partir de l’URL que nous avons obtenue en envoyant la requête. Lorsque nous obtenons le contenu, nous le désérialisons dans l’une de nos classes d’assistance pour faciliter l’interaction avec celui-ci.

Voici le code d’interrogation pour tous les états sauf celui correspondant à une tâche terminée sans erreurs (nous l’aborderons plus tard). 

`CheckTranscriptionStatus` prend l’URL d’état de la demande de transcription et l’interroge toutes les 5 secondes jusqu’à ce qu’elle indique une réussite ou une erreur. Il appelle ensuite `PrintResults` pour imprimer les résultats de la transcription. Nous allons ensuite implémenter `PrintResults`.
```csharp
function CheckTranscriptionStatus(statusUrl) {
    transcription = null;
    const fetchOptions = {
        headers: {
            "Ocp-Apim-Subscription-Key": SubscriptionKey
        }
    }

    const fetchRequest = https.get(new URL(statusUrl), fetchOptions, (response) => {
        if (response.statusCode !== 200) {
            console.info("Error retrieving status: " + response.statusCode);
        } else {
            let responseText = '';
            response.setEncoding('utf8');
            response.on("data", (chunk) => {
                responseText += chunk;
            });

            response.on("end", () => {
                const statusObject = JSON.parse(responseText);

                var done = false;
                switch (statusObject.status) {
                    case "Failed":
                        console.info("Transcription failed. Status: " + transcription.StatusMessage);
                        done = true;
                        break;
                    case "Succeeded":
                        done = true;
                        PrintResults(statusObject.resultsUrls["channel_0"]);
                        break;
                    case "Running":
                        console.info("Transcription is still running.");
                        break;
                    case "NotStarted":
                        console.info("Transcription has not started.");
                        break;
                }

                if (!done) {
                    setTimeout(() => {
                        CheckTranscriptionStatus(statusUrl);
                    }, (5000));
                }
            });
        }
    });

    fetchRequest.on("error", error => {
        console.error(error);
    });
}
```

## <a name="display-the-transcription-results"></a>Afficher les résultats de la transcription
Une fois que le service a terminé la transcription et que celle-ci s’est bien déroulée, les résultats sont stockés dans une autre URL que nous pouvons obtenir dans la réponse d’état. Ici, nous faisons une demande pour télécharger les résultats dans un fichier temporaire avant de les lire et de les désérialiser.
Une fois que les résultats sont chargés, nous pouvons les imprimer sur la console. 

```JavaScript
function PrintResults(resultUrl)
{
    const fetchOptions = {
        headers: {
            "Ocp-Apim-Subscription-Key": SubscriptionKey
        }
    }

    const fetchRequest = https.get(new URL(resultUrl), fetchOptions, (response) => {
        if (response.statusCode !== 200) {
            console.info("Error retrieving status: " + response.statusCode);
        } else {
            let responseText = '';
            response.setEncoding('utf8');
            response.on("data", (chunk) => {
                responseText += chunk;
            });

            response.on("end", () => {
                console.info("Transcription Results:");
                console.info(responseText);
            });
        }
    });
}
```

## <a name="check-your-code"></a>Vérifier votre code
À ce stade, votre code doit ressembler à ceci :

```JavaScript
const https = require("https");

// Replace with your subscription key
SubscriptionKey = "YourSubscriptionKey";

// Update with your service region
Region = "YourServiceRegion";
Port = 443;

// Recordings and locale
Locale = "en-US";
RecordingsBlobUri = "YourFileUrl";

// Name and description
Name = "Simple transcription";
Description = "Simple transcription description";

SpeechToTextBasePath = "/api/speechtotext/v2.0/";

class ModelIdentity {
    id;
}

class Transcription {
    Name;
    Description;
    Locale;
    RecordingsUrl;
    ResultsUrls;
    Id;
    CreatedDateTime;
    LastActionDateTime;
    Status;
    StatusMessage;
}

class TranscriptionDefinition {
    Name;
    Description;
    RecordingsUrl;
    Locale;
    Models;
    Properties;
}

const ts = {
    Name: Name,
    Description: Description,
    Locale: Locale,
    RecordingsUrl: RecordingsBlobUri,
    Properties: {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True"
    },
    Models: []
}

const postPayload = JSON.stringify(ts);

const startOptions = {
    hostname: Region + ".cris.ai",
    port: Port,
    path: SpeechToTextBasePath + "Transcriptions/",
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        'Content-Length': postPayload.length,
        "Ocp-Apim-Subscription-Key": SubscriptionKey
    }
}

function PrintResults(resultUrl)
{
    const fetchOptions = {
        headers: {
            "Ocp-Apim-Subscription-Key": SubscriptionKey
        }
    }

    const fetchRequest = https.get(new URL(resultUrl), fetchOptions, (response) => {
        if (response.statusCode !== 200) {
            console.info("Error retrieving status: " + response.statusCode);
        } else {
            let responseText = '';
            response.setEncoding('utf8');
            response.on("data", (chunk) => {
                responseText += chunk;
            });

            response.on("end", () => {
                console.info("Transcription Results:");
                console.info(responseText);
            });
        }
    });
}

function CheckTranscriptionStatus(statusUrl) {
    transcription = null;
    const fetchOptions = {
        headers: {
            "Ocp-Apim-Subscription-Key": SubscriptionKey
        }
    }

    const fetchRequest = https.get(new URL(statusUrl), fetchOptions, (response) => {
        if (response.statusCode !== 200) {
            console.info("Error retrieving status: " + response.statusCode);
        } else {
            let responseText = '';
            response.setEncoding('utf8');
            response.on("data", (chunk) => {
                responseText += chunk;
            });

            response.on("end", () => {
                const statusObject = JSON.parse(responseText);

                var done = false;
                switch (statusObject.status) {
                    case "Failed":
                        console.info("Transcription failed. Status: " + transcription.StatusMessage);
                        done = true;
                        break;
                    case "Succeeded":
                        done = true;
                        PrintResults(statusObject.resultsUrls["channel_0"]);
                        break;
                    case "Running":
                        console.info("Transcription is still running.");
                        break;
                    case "NotStarted":
                        console.info("Transcription has not started.");
                        break;
                }

                if (!done) {
                    setTimeout(() => {
                        CheckTranscriptionStatus(statusUrl);
                    }, (5000));
                }
            });
        }
    });

    fetchRequest.on("error", error => {
        console.error(error);
    });
}

const request = https.request(startOptions, (response) => {
    if (response.statusCode != 202) {
        console.error("Error, status code " + response.statusCode);
    } else {

        const transcriptionLocation = response.headers.location;

        console.info("Created transcription at location " + transcriptionLocation);
        console.info("Checking status.");

        CheckTranscriptionStatus(transcriptionLocation);
    }
});

request.on("error", error => {
    console.error(error);
});

request.write(postPayload);
request.end();
```

## <a name="run-your-app"></a>Exécutez l'application.

Vous êtes maintenant prêt à créer votre application et à tester la reconnaissance vocale à l’aide du service de reconnaissance vocale.

**Démarrer votre application** : Exécutez node index.js.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [footer](./footer.md)]
