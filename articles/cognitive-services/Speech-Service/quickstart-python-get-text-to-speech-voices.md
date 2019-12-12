---
title: 'Démarrage rapide : Lister les voix de la synthèse vocale, Python – Service Speech'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous allez apprendre à obtenir la liste complète des voix standard et neuronales pour une région/un point de terminaison avec Python. La liste est retournée au format JSON, et la disponibilité des voix varie selon la région.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: c645d01dd37fd370911019446c99c76c9da6221c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975973"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-python"></a>Démarrage rapide : Obtenir la liste des voix de synthèse vocale à l’aide de Python

Dans ce guide de démarrage rapide, vous allez apprendre à obtenir la liste complète des voix standard et neuronales pour une région/un point de terminaison avec Python. La liste est retournée au format JSON, et la disponibilité des voix varie selon la région. Pour obtenir la liste des régions prises en charge, consultez [Régions](regions.md).

Ce guide de démarrage rapide demande un [compte Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) avec une ressource du service Speech. Si vous n’avez pas de compte, vous pouvez utiliser la [version d’évaluation gratuite](get-started.md) pour obtenir une clé d’abonnement.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide nécessite :

* Python 2.7.x ou 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) ou l’éditeur de texte de votre choix
* Une clé d’abonnement Azure pour le service Speech

## <a name="create-a-project-and-import-required-modules"></a>Créez un projet et importez les modules requis

Créez un projet Python dans votre IDE ou votre éditeur favori. Copiez cet extrait de code dans votre projet, dans un fichier nommé `get-voices.py`.

```python
import requests
```

> [!NOTE]
> Si vous n’avez jamais utilisé ces modules auparavant, vous devrez les installer avant d’exécuter votre programme. Pour installer ces packages, exécuter `pip install requests`.

Requests est utilisé pour les requêtes HTTP au service de synthèse vocale.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Définir la clé d’abonnement et créer une invite pour la conversion de texte par synthèse vocale (TTS)

Dans les sections suivantes, vous allez créer des méthodes pour gérer l’autorisation, appeler l’API Synthèse vocale et valider la réponse. Nous allons commencer par créer une classe. C’est là que nous mettrons nos méthodes pour l’échange de jeton et l’appel de l’API Synthèse vocale.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

`subscription_key` est votre clé unique à partir du portail Azure.

## <a name="get-an-access-token"></a>Obtention d’un jeton d’accès

Ce point de terminaison nécessite un jeton d’accès pour l’authentification. Pour obtenir un jeton d’accès, un échange est nécessaire. Cet exemple échange votre clé d’abonnement du service Speech pour obtenir un jeton accès à l’aide du point de terminaison `issueToken`.

Cet exemple suppose que votre abonnement au service Speech se situe dans la région USA Ouest. Si vous utilisez une autre région, mettez à jour la valeur de `fetch_token_url`. Pour obtenir la liste complète, consultez [Régions](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copiez le code suivant dans la classe `GetVoices` :

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> Pour plus d'informations sur l'authentification, consultez [S'authentifier avec un jeton d'accès](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Effectuer une requête et enregistrer la réponse

Ici, vous allez générer la requête et enregistrer la liste des voix retourné. Tout d’abord, vous devez définir les valeurs `base_url` et `path`. Cet exemple part du principe que vous utilisez le point de terminaison USA Ouest. Si votre ressource est inscrite dans une autre région, veillez à mettre à jour la valeur `base_url`. Pour plus d’informations, consultez [Régions du service Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Ajoutez ensuite les en-têtes nécessaires pour la requête. Enfin, vous envoyez une requête au service. Si la requête réussit et qu’un code d’état 200 est retourné, la réponse est écrite dans un fichier.

Copiez le code suivant dans la classe `GetVoices` :

```python
def get_voices(self):
    base_url = 'https://westus.tts.speech.microsoft.com'
    path = '/cognitiveservices/voices/list'
    get_voices_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token
    }
    response = requests.get(get_voices_url, headers=headers)
    if response.status_code == 200:
        with open('voices.json', 'wb') as voices:
            voices.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nvoices.json is ready to view.\n")
    else:
        print("\nStatus code: " + str(
            response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Assemblage

Vous avez presque terminé. La dernière étape consiste à instancier votre classe et à appeler vos fonctions.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Exécution de l'exemple d'application

Voilà, vous pouvez maintenant exécuter l’exemple. À partir de la ligne de commande (ou d’une session terminal), accédez au répertoire de votre projet, puis exécutez :

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Supprimer des ressources

N’oubliez pas de supprimer toutes les informations confidentielles (telles que les clés d’abonnement) dans le code source de votre exemple d’application.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer des exemples Python sur GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Voir aussi

* [Référence sur l’API conversion de texte par synthèse vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Création de polices de voix personnalisée](how-to-customize-voice-font.md)
* [Enregistrer des échantillons vocaux pour créer une voix personnalisée](record-custom-voice-samples.md)
