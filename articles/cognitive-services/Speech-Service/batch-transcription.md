---
title: Guide pratique pour utiliser la transcription Batch - Services Speech
titlesuffix: Azure Cognitive Services
description: La transcription Batch est idéale si vous souhaitez transcrire une grande quantité de données audio stockées dans des objets blob Azure, par exemple. Avec l’API REST dédiée, vous pouvez pointer vers des fichiers audio à l’aide d’un URI de signature d’accès partagé (SAS) et recevoir les transcriptions de manière asynchrone.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 2/20/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 2148d1bd79a858bec37e6c574c2a6b6e2009fe46
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190412"
---
# <a name="why-use-batch-transcription"></a>Pourquoi utiliser la transcription Batch ?

La transcription Batch est idéale pour transcrire une grande quantité de données audio stockées dans des objets blob Azure, par exemple. Avec l’API REST dédiée, vous pouvez pointer vers des fichiers audio à l’aide d’un URI de signature d’accès partagé (SAS) et recevoir les transcriptions de manière asynchrone.

## <a name="prerequisites"></a>Conditions préalables

### <a name="subscription-key"></a>Clé de l’abonnement

Comme pour toutes les fonctionnalités du service Speech, créez une clé d’abonnement à partir du [Portail Azure](https://portal.azure.com) en suivant les instructions du [guide de démarrage rapide](get-started.md). Si vous souhaitez obtenir des transcriptions de nos modèles de base, la création de la clé est la seule opération à faire.

>[!NOTE]
> Pour pouvoir utiliser la transcription Batch, vous avez besoin d’un abonnement standard (S0) pour les services Speech. Les clés d’abonnement gratuit (F0) ne fonctionnent pas. Pour obtenir des informations complémentaires, consultez [tarifs et limites](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Modèles personnalisés

Si vous souhaitez personnaliser des modèles acoustiques ou de langage, suivez les étapes de [Personnaliser des modèles acoustiques](how-to-customize-acoustic-models.md) et [Personnalisation de modèles de langage](how-to-customize-language-model.md). Pour utiliser les modèles créés dans la transcription de lot, vous avez besoin de leur ID de modèle. Cet ID n’est pas le point de terminaison que vous trouvez dans la vue des détails des points de terminaison, mais l’ID du modèle que vous pouvez récupérer en cliquant sur les détails des modèles.

## <a name="the-batch-transcription-api"></a>L’API de transcription Batch

L’API de transcription Batch offre une transcription de parole en texte asynchrone, ainsi que des fonctionnalités supplémentaires. Il s’agit d’une API REST qui expose des méthodes pour :

1. Créer des demandes de traitement par lots
1. Interroger l’état
1. Téléchargement des transcriptions

> [!NOTE]
> L’API de transcription Batch est la solution idéale pour les centres d’appels qui accumulent généralement plusieurs milliers d’heures de données audio. Elle facilite la transcription de volumes importants d’enregistrements audio.

### <a name="supported-formats"></a>Formats pris en charge

L’API de transcription Batch prend en charge les formats suivants :

| Format | Codec | Bitrate | Échantillonnage |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bits | 8 ou 16 kHz, mono, stéréo |
| MP3 | PCM | 16 bits | 8 ou 16 kHz, mono, stéréo |
| OGG | OPUS | 16 bits | 8 ou 16 kHz, mono, stéréo |

Dans le cas des flux audio stéréo, l’API de transcription Batch divise les canaux gauche et droit lors de la transcription. Elle crée deux fichiers JSON contenant le résultat relatif à chacun de ces deux canaux. Les timestamps par énoncé permettent au développeur de créer une transcription finale ordonnée chronologiquement. Cet exemple de demande inclut les propriétés de filtrage des obscénités et les timestamps au niveau des mots. 

### <a name="configuration"></a>Configuration

Les paramètres de configuration sont fournis au format JSON :

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<local to us, for example en-US>",
  "name": "<user define name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True",
    "AddSentiment" : "True"
  }
}
```

> [!NOTE]
> L’API de transcription Batch utilise un service REST pour demander des transcriptions, l’état de ces dernières, ainsi que les résultats associés. Vous pouvez utiliser l’API dans n’importe quelle langue. La section ci-après décrit le mode d’utilisation de l’API.

### <a name="configuration-properties"></a>Propriétés de configuration

| Paramètre | Description  | Obligatoire/facultatif |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | Spécifie comment traiter la vulgarité dans les résultats de la reconnaissance. Les valeurs acceptées sont `none` qui désactive le d’obscénités, `masked` qui remplace les obscénités par des astérisques, `removed` qui supprime tous les obscénités du résultat ou `tags` qui ajoute les balises « obscénité ». Le paramètre par défaut est `masked`. | Facultatif |
| `PunctuationMode` | Spécifie comment traiter la ponctuation dans les résultats de la reconnaissance. Les valeurs acceptées sont `none` qui désactive la ponctuation, `dictated` qui implique une ponctuation explicite, `automatic` qui permet au décodeur de gérer la ponctuation, ou `dictatedandautomatic` qui implique des marques de ponctuation dictées ou automatiques. | Facultatif |
 | `AddWordLevelTimestamps` | Spécifie si les timestamps au niveau des mots doivent être ajoutés à la sortie. Les valeurs acceptées sont `true`, qui permet des timestamps au niveau des mots, et `false` (la valeur par défaut) pour les désactiver. | Facultatif |
 | `AddSentiment` | Spécifie le sentiment doit être ajouté à l’énoncé. Valeurs acceptées sont `true` ce qui permet de sentiment par énoncé et `false` (valeur par défaut) pour la désactiver. | Facultatif |

### <a name="storage"></a>Stockage

Batch prend en charge de la transcription [stockage Blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) pour la lecture audio et transcriptions d’écriture vers le stockage.

## <a name="webhooks"></a>Webhooks 

Interrogeant le statut de transcription ne peut pas être la plus performante, ou fournir la meilleure expérience utilisateur. Pour effectuer une interrogation pour l’état, vous pouvez inscrire des rappels, qui informe le client quand longue transcription tâches sont terminées.

Pour plus d’informations, consultez [Webhooks](webhooks.md).

## <a name="sentiment"></a>Sentiments

Sentiment est une nouvelle fonctionnalité dans l’API de Transcription de Batch et est une fonctionnalité importante dans le domaine de centre d’appel. Les clients peuvent utiliser le `AddSentiment` paramètres à leurs demandes à 

1.  Obtenir des informations sur la satisfaction des clients
2.  Obtenir des informations sur les performances des agents (équipe en prenant les appels)
3.  Identifier le point exact dans le temps quand un appel a pris un tour dans une direction négative
4.  Identifier la cause du problème bien lors de l’activation des appels négatives positive
5.  Identifier ce que les clients aiment et qu’ils n’aime pas sur un produit ou un service

Sentiment est évalué par segment audio où un segment audio est défini comme le temps écoulé entre le début de l’énoncé (décalage) et la latence de détection de la fin du flux d’octets. L’intégralité du texte au sein de ce segment est utilisé pour calculer les sentiments. Nous ne pas calculer des valeurs de sentiment d’agrégation pour l’appel entière ou la reconnaissance vocale entière de chaque canal. Ceux-ci sont laissées au propriétaire du domaine pour appliquer davantage.

Sentiment est appliqué sur la forme lexicale.

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
Les fonctionnalités utilise un modèle de Sentiment qui est actuellement en version bêta.

## <a name="sample-code"></a>Exemple de code

L’exemple complet est disponible dans le [référentiel d’exemples GitHub](https://aka.ms/csspeech/samples) à l’intérieur du sous-répertoire `samples/batch`.

Vous devez personnaliser l’exemple de code avec vos informations d’abonnement, la région du service, le pointage SAS URI vers le fichier audio à transcrire et les ID du modèle si vous souhaitez utiliser un modèle acoustique ou de langage personnalisé. 

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

L’exemple de code installe le client et envoie la demande de transcription. Il demande ensuite des informations d’état et imprime les détails de la progression de la transcription.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Pour plus d’informations sur les appels précédents, consultez notre [document Swagger](https://westus.cris.ai/swagger/ui/index). L’exemple complet présenté ici est disponible sur [GitHub](https://aka.ms/csspeech/samples) dans le sous-répertoire `samples/batch`.

Notez la configuration asynchrone concernant la publication des données audio et la réception de l’état de la transcription. Le client que vous créez est un client HTTP .NET. Il existe une méthode `PostTranscriptions` pour l’envoi des détails du fichier audio, et une méthode `GetTranscriptions` pour la réception des résultats. `PostTranscriptions` renvoie un descripteur et `GetTranscriptions` l’utilise pour créer un descripteur permettant d’obtenir l’état de la transcription.

L’exemple de code actuel ne spécifie pas de modèle personnalisé. Le service utilise les modèles de base pour la transcription du ou des fichiers. Pour spécifier les modèles, vous pouvez transmettre à la même méthode les ID du modèle acoustique et du modèle de langage.

> [!NOTE]
> Dans le cas des transcriptions de base, vous n’avez pas besoin de déclarer l’ID des modèles de base. Si vous spécifiez uniquement un ID de modèle de langage (et pas un ID de modèle acoustique), un modèle acoustique correspondant est automatiquement sélectionné. Si vous spécifiez uniquement un ID de modèle acoustique (et pas un ID de modèle de langage), un modèle de langage correspondant est automatiquement sélectionné.

## <a name="download-the-sample"></a>Télécharger l’exemple

Vous trouverez l’exemple dans le répertoire `samples/batch` du [référentiel d’exemples GitHub](https://aka.ms/csspeech/samples).

> [!NOTE]
> Les travaux de transcription par lots sont planifiés au mieux des capacités, il n’y a pas de durée estimée jusqu’au moment où un travail passe à l’état en cours d’exécution. Une fois en cours d’exécution, la transcription réelle est traitée plus rapidement que l’audio en temps réel.

## <a name="next-steps"></a>Étapes suivantes

* [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
