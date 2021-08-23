---
title: API Audio long – Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment l’API Audio long est conçue pour la synthèse asynchrone de texte de long en parole.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: lajanuar
ms.openlocfilehash: 62546d4f7a1cd93d787faee22132d967bb0def8d
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122533055"
---
# <a name="long-audio-api"></a>API Audio long

L’API Audio long fournit une synthèse asynchrone de texte long en parole (par exemple, livres audio, articles de presse et documents). Cette API ne renvoie pas l’audio synthétisé en temps réel. Au lieu de cela, vous interrogez les réponses et consommez les sorties au fur et à mesure que le service les rend disponibles. Contrairement à l’API de synthèse vocale utilisée par le Kit de développement logiciel (SDK) Speech, l’API Audio long peut créer de l’audio synthétisé de plus de 10 minutes. Cela la rend idéale pour les éditeurs et les plateformes de contenu audio qui souhaitent créer des contenus audio longs, comme des livres audio, en un seul lot.

Autres avantages de l’API Audio long :

* La synthèse vocale renvoyée par le service utilise les meilleures voix neuronales.
* Il n’est pas nécessaire de déployer un point de terminaison vocal.

> [!NOTE]
> L’API Audio long prend en charge les [voix neuronales publiques](./language-support.md#neural-voices) et les [voix neuronales personnalisées](./how-to-custom-voice.md).

## <a name="workflow"></a>Workflow

Lorsque vous utilisez l’API Audio long, vous envoyez généralement un ou plusieurs fichiers texte à synthétiser, interrogez l’état et téléchargez la sortie audio lorsque l’état indique une réussite.

Le diagramme suivant donne une vue d’ensemble du workflow.

![Diagramme de workflow de l’API Audio long](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Préparer du contenu pour la synthèse

Lors de la préparation de votre fichier texte, vérifiez les points suivants :

* Il s’agit d’un texte brut (.txt) ou d’un texte SSML (.txt).
* Le texte est encodé au format [UTF-8 avec marque d’ordre d’octet (BOM, Byte Order Mark)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom).
* Il s’agit d’un fichier unique, non d’un fichier zip.
* Il contient plus de 400 caractères pour le texte brut ou 400 [caractères facturables](./text-to-speech.md#pricing-note) pour du texte SSML, et moins de 10 000 paragraphes.
  * Pour du texte brut, chaque paragraphe est séparé en appuyant sur la touche **Entrée/Retour**. Voir un [exemple d’entrée de texte brut](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt).
  * Pour du texte SSML, chaque élément SSML est considéré comme un paragraphe. Séparez les éléments SSML par des paragraphes différents. Voir un [exemple d’entrée de texte SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt).

## <a name="sample-code"></a>Exemple de code

Le reste de cette page est consacré à Python, mais des exemples de code pour l’API Audio long sont disponibles sur GitHub pour les langages de programmation suivants :

* [Exemple de code : Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Exemple de code : C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Exemple de code : Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)

## <a name="python-example"></a>Exemple Python

Cette section contient des exemples Python qui illustrent l’utilisation de base de l’API Audio long. Créez un projet Python dans votre IDE ou votre éditeur favori. Copiez cet extrait de code dans un fichier nommé `long_audio_synthesis_client.py`.

```python
import json
import ntpath
import requests
```

Ces bibliothèques permettent de construire la requête HTTP et d’appeler l’API REST de synthèse vocale d’audio long.

### <a name="get-a-list-of-supported-voices"></a>Obtenir une liste des voix prises en charge

Pour obtenir la liste des voix prises en charge, envoyez une requête GET à `https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/voices`.

Ce code permet d’obtenir une liste complète des voix que vous pouvez utiliser dans une région/un point de terminaison spécifique.

```python
def get_voices():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/voices'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print(response.text)

get_voices()
```

Remplacez les valeurs suivantes :

* Remplacez `<your_key>` par votre clé d’abonnement de service de reconnaissance vocale. Ces informations sont disponibles dans l’onglet **Vue d’ensemble** de votre ressource dans le [portail Azure](https://aka.ms/azureportal).
* Remplacez `<region>` par la région dans laquelle votre ressource Speech a été créée (par exemple `eastus` ou `westus`). Ces informations sont disponibles dans l’onglet **Vue d’ensemble** de votre ressource dans le [portail Azure](https://aka.ms/azureportal).

Vous devez obtenir une sortie similaire à celle-ci :

```json
{
  "values": [
    {
      "locale": "en-US",
      "voiceName": "en-US-AriaNeural",
      "description": "",
      "gender": "Female",
      "createdDateTime": "2020-05-21T05:57:39.123Z",
      "properties": {
        "publicAvailable": true
      }
    },
    {
      "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141"
      "locale": "en-US",
      "voiceName": "my custom neural voice",
      "description": "",
      "gender": "Male",
      "createdDateTime": "2020-05-21T05:25:40.243Z",
      "properties": {
        "publicAvailable": false
      }
    }
  ]
}
```

Si **properties.publicAvailable** est **true**, la voix est une voix neuronale publique. Dans le cas contraire, il s’agit d’une voix neuronale personnalisée.

### <a name="convert-text-to-speech"></a>Convertir du texte en parole

Préparez un fichier texte d’entrée, soit en texte brut, soit en texte SSML, puis ajoutez le code suivant à `long_audio_synthesis_client.py` :

> [!NOTE]
> `concatenateResult` est un paramètre facultatif. S’il n’est pas défini, les sorties audio sont générées par paragraphe. Vous pouvez également concaténer les données audio en une sortie en incluant le paramètre. 
> `outputFormat` est également facultatif. Par défaut, la sortie audio est définie sur `riff-16khz-16bit-mono-pcm`. Pour plus d’informations sur le formats de sortie audio pris en charge, consultez [Formats de sortie audio](#audio-output-formats).

```python
def submit_synthesis():
    region = '<region>'
    key = '<your_key>'
    input_file_path = '<input_file_path>'
    locale = '<locale>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    voice_identities = [
        {
            'voicename': '<voice_name>'
        }
    ]

    payload = {
        'displayname': 'long audio synthesis sample',
        'description': 'sample description',
        'locale': locale,
        'voices': json.dumps(voice_identities),
        'outputformat': 'riff-16khz-16bit-mono-pcm',
        'concatenateresult': True,
    }

    filename = ntpath.basename(input_file_path)
    files = {
        'script': (filename, open(input_file_path, 'rb'), 'text/plain')
    }

    response = requests.post(url, payload, headers=header, files=files)
    print('response.status_code: %d' % response.status_code)
    print(response.headers['Location'])

submit_synthesis()
```

Remplacez les valeurs suivantes :

* Remplacez `<your_key>` par votre clé d’abonnement de service de reconnaissance vocale. Ces informations sont disponibles dans l’onglet **Vue d’ensemble** de votre ressource dans le [portail Azure](https://aka.ms/azureportal).
* Remplacez `<region>` par la région dans laquelle votre ressource Speech a été créée (par exemple `eastus` ou `westus`). Ces informations sont disponibles dans l’onglet **Vue d’ensemble** de votre ressource dans le [portail Azure](https://aka.ms/azureportal).
* Remplacez `<input_file_path>` par le chemin au fichier texte que vous avez préparé pour la conversion de texte par synthèse vocale.
* Remplacez `<locale>` par les paramètres régionaux de sortie souhaités. Consultez [Prise en charge linguistique](language-support.md#neural-voices) pour plus d'informations.

Utilisez l’une des voix retournées par votre appel précédent au point de terminaison `/voices`.

* Si vous utilisez une voix neuronale publique, remplacez `<voice_name>` par la voix de sortie souhaitée.
* Pour utiliser une voix neuronale personnalisée, remplacez la variable `voice_identities` par le code suivant et remplacez `<voice_id>` par l’`id` de votre voix neuronale personnalisée.
```Python
voice_identities = [
    {
        'id': '<voice_id>'
    }
]
```

Vous devez obtenir une sortie similaire à celle-ci :

```console
response.status_code: 202
https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/<guid>
```

> [!NOTE]
> Si vous avez plusieurs fichiers d’entrée, vous devrez envoyer plusieurs demandes, et vous devez tenir compte de certaines limites.
> * Le client peut envoyer jusqu’à **cinq** demandes par seconde pour chaque compte d’abonnement Azure. Si la limite est dépassée, un **code d’erreur 429 (trop de demandes)** est renvoyé. Réduisez le rythme des soumissions pour éviter cette limite.
> * Le serveur peut mettre en file d’attente jusqu’à **120** demandes pour chaque compte d’abonnement Azure. Si la file d’attente dépasse cette limite, le serveur renvoie le **code d’erreur 429 (trop de demandes)** . Attendez que les demandes soient terminées avant d’envoyer d’autres demandes.

Vous pouvez utiliser l’URL dans la sortie pour obtenir l’état de la demande.

### <a name="get-details-about-a-submitted-request"></a>Obtenir des détails sur une demande envoyée

Pour obtenir l’état d’une demande de synthèse envoyée, envoyez une requête GET à l’URL renvoyée à l’étape précédente.

```Python

def get_synthesis():
    url = '<url>'
    key = '<your_key>'
    header = {
        'Ocp-Apim-Subscription-Key': key
    }
    response = requests.get(url, headers=header)
    print(response.text)

get_synthesis()
```

La sortie se présente comme suit :

```json
response.status_code: 200
{
  "models": [
    {
      "voiceName": "en-US-AriaNeural"
    }
  ],
  "properties": {
    "outputFormat": "riff-16khz-16bit-mono-pcm",
    "concatenateResult": false,
    "totalDuration": "PT5M57.252S",
    "billableCharacterCount": 3048
  },
  "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
  "lastActionDateTime": "2021-01-14T11:12:27.240Z",
  "status": "Succeeded",
  "createdDateTime": "2021-01-14T11:11:02.557Z",
  "locale": "en-US",
  "displayName": "long audio synthesis sample",
  "description": "sample description"
}
```

La propriété `status` passe de l’état `NotStarted` à `Running`, et enfin à `Succeeded` ou `Failed`. Vous pouvez interroger cette API en boucle jusqu’à ce que l’état devienne `Succeeded` ou `Failed`.

### <a name="download-audio-result"></a>Télécharger le résultat audio

Une fois qu’une demande de synthèse a abouti, vous pouvez télécharger le résultat audio en appelant l’API GET `/files`.

```python
def get_files():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/files'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_files()
```

Remplacez `<request_id>` par l’ID de la demande dont vous voulez télécharger le résultat. Il se trouve dans la réponse de l’étape précédente.

La sortie se présente comme suit :

```json
response.status_code: 200
{
  "values": [
    {
      "name": "2779f2aa-4e21-4d13-8afb-6b3104d6661a.txt",
      "kind": "LongAudioSynthesisScript",
      "properties": {
        "size": 4200
      },
      "createdDateTime": "2021-01-14T11:11:02.410Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/input.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "name": "voicesynthesis_waves.zip",
      "kind": "LongAudioSynthesisResult",
      "properties": {
        "size": 9290000
      },
      "createdDateTime": "2021-01-14T11:12:27.226Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/voicesynthesis_waves.zip?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ]
}
```
Cet exemple de sortie contient des informations sur deux fichiers. Celui avec `"kind": "LongAudioSynthesisScript"` est le script d’entrée envoyé. L’autre avec `"kind": "LongAudioSynthesisResult"` est le résultat de cette requête.

Le résultat est un dossier zip qui contient les fichiers de sortie audio générés, ainsi qu’une copie du texte d’entrée.

Les deux fichiers peuvent être téléchargés à partir de l’URL dans leur propriété `links.contentUrl`.

### <a name="get-all-synthesis-requests"></a>Récupérer toutes les demandes de synthèse

Le code suivant répertorie toutes les demandes envoyées :

```python
def get_synthesis():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/'.format(region)    
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_synthesis()
```

La sortie se présente comme suit :

```json
response.status_code: 200
{
  "values": [
    {
      "models": [
        {
          "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141",
          "voiceName": "my custom neural voice"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT1S",
        "billableCharacterCount": 5
      },
      "id": "f9f0bb74-dfa5-423d-95e7-58a5e1479315",
      "lastActionDateTime": "2021-01-05T07:25:42.433Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-05T07:25:13.600Z",
      "locale": "en-US",
      "displayName": "Long Audio Synthesis",
      "description": "Long audio synthesis sample"
    },
    {
      "models": [
        {
          "voiceName": "en-US-AriaNeural"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT5M57.252S",
        "billableCharacterCount": 3048
      },
      "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
      "lastActionDateTime": "2021-01-14T11:12:27.240Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-14T11:11:02.557Z",
      "locale": "en-US",
      "displayName": "long audio synthesis sample",
      "description": "sample description"
    }
  ]
}
```

La propriété `values` répertorie vos demandes de synthèse. La liste est paginée, avec une taille de page maximale de 100. S’il y a plus de 100 demandes, une propriété `"@nextLink"` est fournie pour obtenir la page suivante de la liste paginée.

```console
  "@nextLink": "https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/?top=100&skip=100"
```

Vous pouvez également personnaliser la taille de la page et le nombre de sauts en indiquant `skip` et `top` dans le paramètre URL.

### <a name="remove-previous-requests"></a>Supprimer les requêtes précédentes

Le service conserve jusqu’à **20 000** requêtes pour chaque compte d’abonnement Azure. Si le nombre de vos demandes dépasse cette limite, supprimez les demandes précédentes avant d’en créer d’autres. Si vous ne supprimez pas des demandes existantes, vous recevez une notification d’erreur.

Le code suivant montre comment supprimer une demande de synthèse spécifique.

```python
def delete_synthesis():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.delete(url, headers=header)
    print('response.status_code: %d' % response.status_code)
```

Si la demande est bien supprimée, le code d’état de la réponse sera HTTP 204 (Aucun contenu).

```console
response.status_code: 204
```

> [!NOTE]
> Les requêtes dont l’état est `NotStarted` ou `Running` ne peuvent pas être supprimées.

La `long_audio_synthesis_client.py` complète est disponible sur [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="http-status-codes"></a>Codes d’état HTTP

Le tableau suivant détaille les codes de réponse HTTP et les messages de l’API REST.

| API | Code d'état HTTP | Description | Solution |
|-----|------------------|-------------|----------|
| Créer | 400 | La synthèse vocale n’est pas activée dans cette région. | Modifiez la clé d’abonnement au service Speech de façon à utiliser une région prise en charge. |
|        | 400 | Seuls sont valides les abonnements de niveau **Standard** au service Speech pour cette région. | Modifiez la clé d’abonnement au service Speech de façon à utiliser le niveau de tarification « Standard ». |
|        | 400 | Dépassement de la limite de 20 000 demandes pour le compte Azure. Supprimez quelques demandes pour pouvoir en envoyer de nouvelles. | Le serveur conserve un maximum de 20 000 demandes pour chaque compte Azure. Supprimez des demandes pour pouvoir en envoyer de nouvelles. |
|        | 400 | Ce modèle ne peut pas être utilisé dans la synthèse vocale : {modelID}. | Vérifiez que l’état de {modelID} est correct. |
|        | 400 | La région de la demande ne correspond pas à celle du modèle : {modelID}. | Vérifiez que la région de {modelID} correspond à celle de la demande. |
|        | 400 | La synthèse vocale ne prend en charge que le fichier texte au format d’encodage UTF-8 avec le marqueur d’ordre d’octet. | Vérifiez que les fichiers d’entrée sont au format d’encodage UTF-8 avec le marqueur d’ordre d’octet. |
|        | 400 | Seules les entrées SSML valides sont autorisées dans la demande de synthèse vocale. | Vérifiez que les expressions SSML d’entrée sont correctes. |
|        | 400 | Le nom de voix {voiceName} est introuvable dans le fichier d’entrée. | Le nom de voix SSML d’entrée n’est pas aligné avec l’ID de modèle. |
|        | 400 | Le nombre de paragraphes dans le fichier d’entrée ne doit pas excéder 10 000. | Vérifiez que le nombre de paragraphes dans le fichier est inférieur à 10 000. |
|        | 400 | Le fichier d’entrée doit comprendre plus de 400 caractères. | Vérifiez que votre fichier d’entrée comporte plus de 400 caractères. |
|        | 404 | Le modèle déclaré dans la définition de synthèse vocale est introuvable : {modelID}. | Vérifiez que le {modelID} est correct. |
|        | 429 | Dépassement de la limite de synthèse vocale active. Attendez que des demandes soient traitées. | Le serveur est autorisé à exécuter et à mettre en file d’attente un maximum de 120 demandes pour chaque compte Azure. Attendez et évitez d’envoyer de nouvelles demandes avant que quelques demandes ne soient traitées. |
| Tous       | 429 | Il y a trop de demandes. | Le client est autorisé à envoyer un maximum de 5 demandes par seconde au serveur pour chaque compte Azure. Réduisez la quantité de demandes par seconde. |
| DELETE    | 400 | La tâche de synthèse vocale est toujours en cours d’utilisation. | Vous ne pouvez supprimer que les demandes ayant l’état **Terminé** ou **Échec**. |
| GetById   | 404 | L’entité spécifiée est introuvable. | Vérifiez que l’ID de synthèse est correct. |

## <a name="regions-and-endpoints"></a>Régions et points de terminaison

L’API Audio long est disponible dans plusieurs régions avec des points de terminaison uniques.

| Région | Point de terminaison |
|--------|----------|
| USA Est | `https://eastus.customvoice.api.speech.microsoft.com` |
| Inde Centre | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Asie Sud-Est | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Sud du Royaume-Uni | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europe Ouest | `https://westeurope.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Formats de sortie aduio

Nous prenons en charge différents formats de sortie audio. Vous pouvez générer des sorties audio par paragraphe ou concaténer les sorties audio en une seule sortie en définissant le paramètre `concatenateResult`. Les formats de sortie audio suivants sont pris en charge par l’API Audio long :

> [!NOTE]
> Le format audio par défaut est riff-16 khz-16 bits-mono-pcm.

* riff-8 khz-16 bits-mono-pcm
* riff-16 khz-16 bits-mono-pcm
* riff-24 khz-16 bits-mono-pcm
* riff-48 khz-16 bits-mono-pcm
* audio-16 khz-32 kbitrate-mono-mp3
* audio-16 khz-64 kbitrate-mono-mp3
* audio-16 khz-128 kbitrate-mono-mp3
* audio-24 khz-48 kbitrate-mono-mp3
* audio-24 khz-96 kbitrate-mono-mp3
* audio-24 khz-160 kbitrate-mono-mp3
