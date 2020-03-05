---
title: 'Démarrage rapide : Synthèse asynchrone pour l’audio long (préversion) - Service Speech'
titleSuffix: Azure Cognitive Services
description: Utilisez l’API Audio long pour convertir du texte en parole de manière asynchrone et récupérer la sortie audio à partir de l’URI que le service retourne. Cette API REST est idéale pour les fournisseurs de contenu qui doivent convertir des fichiers texte d’une taille supérieure à 10 000 caractères ou 50 paragraphes en synthèse vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: afa9437a00c6f30688c7942feea94f7db7ed490a
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919343"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Démarrage rapide : Synthèse asynchrone pour l’audio long dans Python (préversion)

Dans ce guide de démarrage rapide, vous utilisez l’API Audio long pour convertir du texte en parole de manière asynchrone et récupérer la sortie audio à partir de l’URI que le service retourne. Cette API REST est idéale pour les fournisseurs de contenu qui doivent synthétiser l’audio à partir d’un texte contenant plus de 5 000 caractères (ou durant plus de 10 minutes). Pour plus d’informations, consultez [API Audio long](../../long-audio-api.md).

> [!NOTE]
> La synthèse asynchrone de l’audio de long peut uniquement être utilisée avec des [voix neuronales personnalisées](../../how-to-custom-voice.md#custom-neural-voices).

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* Python 2.7.x ou 3.x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) ou l’éditeur de texte de votre choix.
* Un abonnement Azure et une clé d’abonnement au service Speech. [Créez un compte Azure](../../get-started.md#new-resource) et [créez une ressource Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) pour obtenir la clé. Lors de la création de la ressource Speech, vérifiez que votre niveau tarifaire est défini sur **S0** et que l’emplacement est défini sur une [région prise en charge](../../regions.md#standard-and-neural-voices).

## <a name="create-a-project-and-import-required-modules"></a>Créez un projet et importez les modules requis

Créez un projet Python dans votre IDE ou votre éditeur favori. Copiez cet extrait de code dans un fichier nommé `voice_synthesis_client.py`.

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

> [!NOTE]
> Si vous n’avez jamais utilisé ces modules, vous devrez les installer avant d’exécuter votre programme. Pour installer ces packages, exécuter `pip install requests urllib3`.

Ces modules permettent d’analyser les arguments, de construire la requête HTTP et d’appeler l’API REST de synthèse vocale d’audio long.

## <a name="get-a-list-of-supported-voices"></a>Obtenir une liste des voix prises en charge

Ce code obtient une liste des voix disponibles que vous pouvez utiliser pour convertir le texte par synthèse vocale. Ajoutez le code à `voice_synthesis_client.py` :

```python
parser = argparse.ArgumentParser(description='Cris client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the cris subscription key, like ff1eb62d06d34767bda0207acb1da7d7 ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.cris.ai/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

### <a name="test-your-code"></a>Test de votre code

Nous allons tester ce que vous avez fait jusqu’à présent. Vous devez modifier quelques éléments dans la requête ci-dessous :

* Remplacez `<your_key>` par votre clé d’abonnement de service de reconnaissance vocale. Ces informations sont disponibles dans l’onglet **Vue d’ensemble** de votre ressource dans le [portail Azure](https://aka.ms/azureportal).
* Remplacez `<region>` par la région dans laquelle votre ressource Speech a été créée (par exemple `eastus` ou `westus`). Ces informations sont disponibles dans l’onglet **Vue d’ensemble** de votre ressource dans le [portail Azure](https://aka.ms/azureportal).

Exécutez cette commande :

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

Vous devez obtenir une sortie similaire à celle-ci :

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="prepare-input-files"></a>Préparer des fichiers d’entrée

Préparez un fichier texte d’entrée. Il peut s’agir d’un texte brut ou d’un texte SSML. Pour connaître les exigences relatives au fichier d’entrée, lisez comment [préparer du contenu pour la synthèse](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis).

## <a name="convert-text-to-speech"></a>Convertir du texte en parole

Après avoir préparé le fichier texte d’entrée, ajoutez ce code de synthèse vocale à `voice_synthesis_client.py` :

> [!NOTE]
> « concatenateResult » est un paramètre facultatif. S’il n’est pas défini, les sorties audio sont générées par paragraphe. Vous pouvez également concaténer les données audio en une sortie en définissant le paramètre. Par défaut, la sortie audio est définie sur riff-16khz-16bit-mono-pcm. Pour plus d’informations sur les sorties audio prises en charge, consultez [Formats de sortie audio](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats).

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

### <a name="test-your-code"></a>Test de votre code

Essayons d’effectuer une demande de synthèse de texte avec votre fichier d’entrée comme source. Vous devez modifier quelques éléments dans la requête ci-dessous :

* Remplacez `<your_key>` par votre clé d’abonnement de service de reconnaissance vocale. Ces informations sont disponibles dans l’onglet **Vue d’ensemble** de votre ressource dans le [portail Azure](https://aka.ms/azureportal).
* Remplacez `<region>` par la région dans laquelle votre ressource Speech a été créée (par exemple `eastus` ou `westus`). Ces informations sont disponibles dans l’onglet **Vue d’ensemble** de votre ressource dans le [portail Azure](https://aka.ms/azureportal).
* Remplacez `<input>` par le chemin au fichier texte que vous avez préparé pour la conversion de texte par synthèse vocale.
* Remplacez `<locale>` par les paramètres régionaux de sortie souhaités. Consultez [Prise en charge linguistique](../../language-support.md#neural-voices) pour plus d'informations.
* Remplacez `<voice_guid>` par la voix de sortie souhaitée. Utilisez l’une des voix retournées par [Obtenir une liste des voix prises en charge](#get-a-list-of-supported-voices).

Convertissez le texte par synthèse vocale avec cette commande :

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> Si vous avez plusieurs fichiers d’entrée, vous devrez envoyer plusieurs demandes. Il existe certaines limites à connaître. 
> * Le client est autorisé à envoyer jusqu’à **5** demandes par seconde au serveur pour chaque compte d’abonnement Azure. Si la limite est dépassée, le client reçoit un code d’erreur 429 (trop de demandes). Réduisez la quantité de demandes par seconde.
> * Le serveur est autorisé à exécuter et à mettre en file d’attente jusqu’à **120** demandes pour chaque compte d’abonnement Azure. Si la limite est dépassée, le serveur retourne un code d’erreur 429 (trop de demandes). Attendez et évitez d’envoyer une nouvelle demande avant que quelques demandes ne soient traitées.

Vous devez obtenir une sortie similaire à celle-ci :

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

Le résultat contient le texte d’entrée et les fichiers de sortie audio générés par le service. Vous pouvez télécharger ces fichiers dans un fichier zip.

## <a name="remove-previous-requests"></a>Supprimer les requêtes précédentes

Le serveur conserve jusqu’à **20 000** demandes pour chaque compte d’abonnement Azure. Si votre nombre de demandes dépasse cette limite, supprimez des demandes précédentes avant d’en créer d’autres. Si vous ne supprimez pas des demandes existantes, vous recevez une notification d’erreur.

Ajoutez le code à `voice_synthesis_client.py` :

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

### <a name="test-your-code"></a>Test de votre code

Examinons à présent les demandes que vous avez envoyées précédemment. Avant de continuer, vous devez apporter quelques modifications à cette demande :

* Remplacez `<your_key>` par votre clé d’abonnement de service de reconnaissance vocale. Ces informations sont disponibles dans l’onglet **Vue d’ensemble** de votre ressource dans le [portail Azure](https://aka.ms/azureportal).
* Remplacez `<region>` par la région dans laquelle votre ressource Speech a été créée (par exemple `eastus` ou `westus`). Ces informations sont disponibles dans l’onglet **Vue d’ensemble** de votre ressource dans le [portail Azure](https://aka.ms/azureportal).

Exécutez cette commande :

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

Cette opération retourne la liste des demandes de synthèse que vous avez effectuées. Vous devriez obtenir un résultat similaire à ceci :

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Supprimons à présent une demande précédemment soumise. Vous devez apporter quelques modifications au code ci-dessous :

* Remplacez `<your_key>` par votre clé d’abonnement de service de reconnaissance vocale. Ces informations sont disponibles dans l’onglet **Vue d’ensemble** de votre ressource dans le [portail Azure](https://aka.ms/azureportal).
* Remplacez `<region>` par la région dans laquelle votre ressource Speech a été créée (par exemple `eastus` ou `westus`). Ces informations sont disponibles dans l’onglet **Vue d’ensemble** de votre ressource dans le [portail Azure](https://aka.ms/azureportal).
* Remplacez `<synthesis_id>` par la valeur retournée à l’étape précédente.

> [!NOTE]
> Les requêtes dont l’état est « En cours »/« En attente » ne peuvent pas être supprimées.

Exécutez cette commande :

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

Vous devriez obtenir un résultat similaire à ceci :

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>Obtenir le client complet

Le fichier `voice_synthesis_client.py` complet est disponible en téléchargement sur [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’API Audio long](../../long-audio-api.md)
