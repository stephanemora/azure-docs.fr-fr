---
title: Qu’est-ce que la transcription par lots – Service Speech
titleSuffix: Azure Cognitive Services
description: La transcription Batch est idéale pour transcrire une grande quantité de données audio stockées dans des objets blob Azure, par exemple. Avec l’API REST dédiée, vous pouvez pointer vers des fichiers audio à l’aide d’un URI de signature d’accès partagé (SAS) et recevoir les transcriptions de manière asynchrone.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: 1f88df186526c2f9903337bb3331940be0989c3d
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892459"
---
# <a name="what-is-batch-transcription"></a>Qu’est-ce que la transcription par lots ?

La transcription par lots est un ensemble d’opérations d’API REST qui vous permet de transcrire une grande quantité de données audio dans un stockage. Vous pouvez pointer vers des fichiers audio à l’aide d’un URI de signature d’accès partagé (SAP) et recevoir les résultats de la transcription de manière asynchrone.

La asynchrone transcription de parole en texte n’est que l’une des fonctionnalités. Vous pouvez utiliser des API REST de transcription par lots pour appeler les méthodes suivantes :



|    Opération de transcription par lots                                             |    Méthode    |    Appel d’API REST                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Crée une transcription.                                              |    POST      |    api/speechtotext/v2.0/transcriptions            |
|    Récupère une liste de transcriptions pour l’abonnement authentifié.    |    GET       |    api/speechtotext/v2.0/transcriptions            |
|    Obtient la liste des paramètres régionaux pris en charge pour les transcriptions hors connexion.              |    GET       |    api/speechtotext/v2.0/transcriptions/locales    |
|    Met à jour les détails mutables de la transcription identifiée par son ID.    |    PATCH     |    api/speechtotext/v2.0/transcriptions/{id}       |
|    Supprime la tâche de transcription spécifiée.                                 |    Suppression    |    api/speechtotext/v2.0/transcriptions/{id}       |
|    Obtient la transcription identifiée par l’ID donné.                        |    GET       |    api/speechtotext/v2.0/transcriptions/{id}       |




Vous pouvez examiner et tester l’API détaillée, disponible sous forme de [document Swagger](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A) intitulé `Custom Speech transcriptions`.

Les travaux de transcription par lots sont planifiés en faisant au mieux selon les circonstances. Il n’existe actuellement aucune estimation concernant le passage d’une tâche à l’état d’exécution. Dans une charge de système normale, l’exécution doit survenir au bout de quelques minutes. Une fois en cours d’exécution, la transcription réelle est traitée plus rapidement que le temps réel de l’audio.

En plus de l’API facile à utiliser, il n’est pas nécessaire de déployer des points de terminaison personnalisés, ni de respecter des exigences en matière de concurrence.

## <a name="prerequisites"></a>Prérequis

### <a name="subscription-key"></a>Clé d'abonnement

Comme pour toutes les fonctionnalités du service Speech, créez une clé d’abonnement à partir du [Portail Azure](https://portal.azure.com) en suivant les instructions du [guide de démarrage rapide](get-started.md).

>[!NOTE]
> Pour pouvoir utiliser la transcription Batch, vous avez besoin d’un abonnement standard (S0) pour le service Speech. Les clés d’abonnement gratuit (F0) ne fonctionnent pas. Pour plus d’informations, voir [Tarification et limites](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modèles personnalisés

Si vous souhaitez personnaliser des modèles acoustiques ou de langage, suivez les étapes de [Personnaliser des modèles acoustiques](how-to-customize-acoustic-models.md) et [Concevoir des modèles de langage de personnalisation](how-to-customize-language-model.md). Pour utiliser les modèles créés dans la transcription de lot, vous avez besoin de leur ID de modèle. L’ID d’un modèle figure dans les détails associés. Il n’est pas nécessaire de déployer un point de terminaison personnalisé pour le service de transcription par lot.

## <a name="the-batch-transcription-api"></a>L’API de transcription Batch

### <a name="supported-formats"></a>Formats pris en charge

L’API de transcription Batch prend en charge les formats suivants :

| Format | Codec | Bitrate | Échantillonnage                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 bits  | 8 kHz ou 16 kHz, mono ou stéréo |
| MP3    | PCM   | 16 bits  | 8 kHz ou 16 kHz, mono ou stéréo |
| OGG    | OPUS  | 16 bits  | 8 kHz ou 16 kHz, mono ou stéréo |

Dans le cas des flux audio stéréo, les canaux gauche et droit sont fractionnés lors de la transcription. Un fichier de résultat JSON est créé pour chaque canal. Les timestamps générés par énoncé permettent au développeur de créer une transcription finale ordonnée chronologiquement.

### <a name="configuration"></a>Configuration

Les paramètres de configuration sont fournis au format JSON :

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to use, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "None | Removed | Tags | Masked",
    "PunctuationMode": "None | Dictated | Automatic | DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True | False",
    "AddSentiment" : "True | False",
    "AddDiarization" : "True | False",
    "TranscriptionResultsContainerUrl" : "<service SAS URI to Azure container to store results into (write permission required)>"
  }
}
```

### <a name="configuration-properties"></a>Propriétés de configuration

Utilisez les propriétés facultatives suivantes pour configurer la transcription :

:::row:::
   :::column span="1":::
      **Paramètre**
   :::column-end:::
   :::column span="2":::
      **Description**
:::row-end:::
:::row:::
   :::column span="1":::
      `ProfanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Spécifie comment traiter la vulgarité dans les résultats de la reconnaissance. Les valeurs acceptées sont `None` pour désactiver le filtrage des termes vulgaires, `Masked` pour remplacer les termes vulgaires par des astérisques, `Removed` pour supprimer tous les termes vulgaires du résultat ou `Tags` pour ajouter des balises « termes vulgaires ». La valeur par défaut est `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      Spécifie comment traiter la ponctuation dans les résultats de la reconnaissance. Les valeurs acceptées sont `None` pour désactiver la ponctuation, `Dictated` pour indiquer une ponctuation explicite (parlée), `Automatic` pour permettre au décodeur de traiter la ponctuation ou `DictatedAndAutomatic` pour utiliser la ponctuation dictée et automatique. La valeur par défaut est `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      Spécifie si les timestamps au niveau des mots doivent être ajoutés à la sortie. Les valeurs acceptées sont `true` pour activer des horodatages au niveau des mots et `false` (valeur par défaut) pour les désactiver.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      Spécifie si l’analyse des sentiments doit être appliquée à l’énoncé. Les valeurs acceptées sont `true` pour l’activer et `false` (valeur par défaut) pour la désactiver. Pour plus d’informations, consultez [Analyse des sentiments](#sentiment-analysis).
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      Spécifie que l’analyse de diarisation doit être effectuée sur l’entrée qui est censée être un canal unique contenant deux voix. Les valeurs acceptées sont `true` pour activer la diarisation et `false` (valeur par défaut) pour la désactiver. De plus, cela nécessite également que `AddWordLevelTimestamps` soit défini sur True.
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      URL facultative avec [SAP de service](../../storage/common/storage-sas-overview.md) vers un conteneur accessible en écriture dans Azure. Le résultat est stocké dans ce conteneur.
:::row-end:::

### <a name="storage"></a>Stockage

La transcription Batch prend en charge le [Stockage Blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) pour la lecture audio et l’écriture de transcriptions dans le stockage.

## <a name="the-batch-transcription-result"></a>Résultat de la transcription par lots

Pour les fichiers audio d’entrée mono, un fichier de résultat de transcription est créé. Pour les fichiers audio d’entrée stéréo, deux fichiers de résultat de transcription sont créés. Chacun d’eux présente la structure suivante :

```json
{
  "AudioFileResults":[
    {
      "AudioFileName": "Channel.0.wav | Channel.1.wav"      'maximum of 2 channels supported'
      "AudioFileUrl": null                                  'always null'
      "AudioLengthInSeconds": number                        'Real number. Two decimal places'
      "CombinedResults": [
        {
          "ChannelNumber": null                             'always null'
          "Lexical": string
          "ITN": string
          "MaskedITN": string
          "Display": string
        }
      ]
      SegmentResults:[                                      'for each individual segment'
        {
          "RecognitionStatus": "Success | Failure"
          "ChannelNumber": null
          "SpeakerId": null | "1 | 2"                       'null if no diarization
                                                             or stereo input file, the
                                                             speakerId as a string if
                                                             diarization requested for
                                                             mono audio file'
          "Offset": number                                  'time in ticks (1 tick is 100 nanosec)'
          "Duration": number                                'time in ticks (1 tick is 100 nanosec)'
          "OffsetInSeconds" : number                        'Real number. Two decimal places'
          "DurationInSeconds" : number                      'Real number. Two decimal places'
          "NBest": [
            {
              "Confidence": number                          'between 0 and 1'
              "Lexical": string
              "ITN": string
              "MaskedITN": string
              "Display": string
              "Sentiment":
                {                                           'this is omitted if sentiment is
                                                             not requested'
                  "Negative": number                        'between 0 and 1'
                  "Neutral": number                         'between 0 and 1'
                  "Positive": number                        'between 0 and 1'
                }
              "Words": [
                {
                  "Word": string
                  "Offset": number                          'time in ticks (1 tick is 100 nanosec)'
                  "Duration": number                        'time in ticks (1 tick is 100 nanosec)'
                  "OffsetInSeconds": number                 'Real number. Two decimal places'
                  "DurationInSeconds": number               'Real number. Two decimal places'
                  "Confidence": number                      'between 0 and 1'
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

Le résultat contient les formes suivantes :

:::row:::
   :::column span="1":::
      **Forme**
   :::column-end:::
   :::column span="2":::
      **Contenu**
:::row-end:::
:::row:::
   :::column span="1":::
      `Lexical`
   :::column-end:::
   :::column span="2":::
      Les mots réels sont reconnus.
:::row-end:::
:::row:::
   :::column span="1":::
      `ITN`
   :::column-end:::
   :::column span="2":::
      Forme « normalisation du texte inversée » du texte reconnu. Abréviations (« Docteur Smith » en « Dr Smith »), numéros de téléphone, et d’autres transformations sont appliquées.
:::row-end:::
:::row:::
   :::column span="1":::
      `MaskedITN`
   :::column-end:::
   :::column span="2":::
      Forme « normalisation du texte inversée » avec masquage des termes vulgaires appliqué.
:::row-end:::
:::row:::
   :::column span="1":::
      `Display`
   :::column-end:::
   :::column span="2":::
      Forme d’affichage du texte reconnu. Des signes de ponctuation et des majuscules ajoutés sont inclus.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Séparation des orateurs (diarisation)

La diarisation est le processus de séparation des orateurs dans une partie de l’audio. Notre pipeline Batch prend en charge la diarisation et est capable de reconnaître deux orateurs dans les enregistrements sur canal mono. La fonctionnalité n’est pas disponible pour les enregistrements stéréo.

Tout résultat de transcription contient un `SpeakerId`. Si la diarisation n’est pas utilisée, la mention `"SpeakerId": null` apparaît dans la sortie JSON. Nous prenons en charge deux voix pour la diarisation. Les orateurs sont identifiés en tant que `"1"` ou `"2"`.

Pour la diarisation, il vous suffit d’ajouter le paramètre concerné dans la requête HTTP comme indiqué ci-dessous.

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

Les timestamps au niveau du mot doivent également être « activés », comme l’indiquent les paramètres dans la requête ci-dessus.

## <a name="sentiment-analysis"></a>analyse de sentiments

La fonctionnalité de sentiment évalue le sentiment exprimé dans l’audio. Le sentiment est exprimé par une valeur comprise entre 0 et 1 pour les sentiments `Negative`, `Neutral` et `Positive`. Par exemple, l’analyse des sentiments peut être utilisée dans des scénarios de centre d’appels :

- Obtenir des insights sur la satisfaction des clients
- Obtenir des insights sur les performances des agents (équipe prenant les appels)
- Identifier le point exact dans le temps où un appel a pris une tournure négative
- Identifier pourquoi un appel négatif a pris une orientation positive
- Identifier ce que les clients aiment et ce qu’ils n’aiment pas dans un produit ou service

Le sentiment est évalué par segment audio en fonction de la forme lexicale. L’intégralité du texte au sein du segment audio est utilisée pour calculer les sentiments. Aucun sentiment d’agrégat n’est calculé pour l’intégralité de la transcription. Actuellement, l’analyse des sentiments est disponible uniquement pour la langue anglaise.

> [!NOTE]
> Nous vous recommandons d’utiliser à la place l’API Analyse de texte Microsoft. Elle offre des fonctionnalités plus avancées qui vont au-delà de l’analyse des sentiments, comme l’extraction d’expressions clés, la détection automatique de la langue et bien plus encore. Vous trouverez des informations et des exemples dans la [documentation Analyse de texte](https://azure.microsoft.com/services/cognitive-services/text-analytics/).
>

Un exemple de sortie JSON se présente comme suit :

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="best-practices"></a>Meilleures pratiques

Le service de transcription peut traiter un grand nombre de transcriptions envoyées. Vous pouvez interroger l’état de vos transcriptions par une opération `GET` sur la [méthode de transcription](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions). Conservez les informations renvoyées dans une taille raisonnable en spécifiant le paramètre `take` (quelques centaines). [Supprimez régulièrement des transcriptions](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) du service une fois que vous avez récupéré les résultats Cela garantit des réponses rapides des appels de gestion des transcriptions.

## <a name="sample-code"></a>Exemple de code

Des exemples complets sont disponibles dans le [dépôt d’exemples GitHub](https://aka.ms/csspeech/samples) à l’intérieur du sous-répertoire `samples/batch`.

Vous devez personnaliser l’exemple de code avec vos informations d’abonnement, la région du service, le pointage SAS URI vers le fichier audio à transcrire et les ID du modèle si vous souhaitez utiliser un modèle acoustique ou de langage personnalisé.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

L’exemple de code configure le client et envoie la demande de transcription. Il demande ensuite des informations d’état et imprime les détails de la progression de la transcription.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Pour plus d’informations sur les appels précédents, consultez notre [document Swagger](https://westus.cris.ai/swagger/ui/index). L’exemple complet présenté ici est disponible sur [GitHub](https://aka.ms/csspeech/samples) dans le sous-répertoire `samples/batch`.

Notez la configuration asynchrone concernant la publication des données audio et la réception de l’état de la transcription. Le client que vous créez est un client HTTP .NET. Il existe une méthode `PostTranscriptions` pour l’envoi des détails du fichier audio, et une méthode `GetTranscriptions` pour la réception des résultats. `PostTranscriptions` renvoie un descripteur et `GetTranscriptions` l’utilise pour créer un descripteur permettant d’obtenir l’état de la transcription.

L’exemple de code actuel ne spécifie pas de modèle personnalisé. Le service utilise les modèles de base pour la transcription du ou des fichiers. Pour spécifier les modèles, vous pouvez transmettre à la même méthode les ID du modèle acoustique et du modèle de langage.

> [!NOTE]
> Dans le cas des transcriptions de base, vous n’avez pas besoin de déclarer l’ID des modèles de base. Si vous spécifiez uniquement un ID de modèle de langage (et pas un ID de modèle acoustique), un modèle acoustique correspondant est automatiquement sélectionné. Si vous spécifiez uniquement un ID de modèle acoustique (et pas un ID de modèle de langage), un modèle de langage correspondant est automatiquement sélectionné.

## <a name="download-the-sample"></a>Télécharger l’exemple

Vous trouverez l’exemple dans le répertoire `samples/batch` du [référentiel d’exemples GitHub](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
