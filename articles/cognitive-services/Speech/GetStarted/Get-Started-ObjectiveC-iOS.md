---
title: Bien démarrer avec l’API Reconnaissance vocale Bing en Objective-C sur iOS | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Utilisez l’API Reconnaissance vocale Bing pour développer des applications iOS qui convertissent du contenu vocal en texte.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: a12c9b5325898afe508398f67939c39e591eb1cc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954498"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-objective-c-on-ios"></a>Guide de démarrage rapide : Utiliser l’API Reconnaissance vocale en Objective-C sur iOS

Avec l’API Reconnaissance vocale, vous pouvez développer des applications iOS qui utilisent le service informatique Speech pour convertir du contenu vocal en texte. L’API prend en charge le streaming en temps réel, afin que votre application puisse de façon simultanée et asynchrone recevoir des résultats de reconnaissance partiels et envoyer du contenu audio au service.

Cet article utilise un exemple d’application pour illustrer les principes fondamentaux de la prise en main de l’API Reconnaissance vocale pour développer une application iOS. Pour obtenir des informations de référence complètes sur l’API, consultez les [informations de référence sur la bibliothèque cliente du SDK Speech](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="prerequisites"></a>Prérequis

### <a name="platform-requirements"></a>Plateforme requise

Vérifiez que l’IDE XCode Mac est installé.

### <a name="get-the-client-library-and-examples"></a>Obtenir la bibliothèque cliente et les exemples

La bibliothèque cliente et les exemples Speech pour iOS sont disponibles dans le [SDK client Speech pour iOS](https://github.com/microsoft/cognitive-speech-stt-ios).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>S’abonner à l’API Reconnaissance vocale et obtenir une clé d’abonnement d’essai

L’API Microsoft Speech fait partie de Cognitive Services (anciennement Project Oxford). Vous pouvez obtenir des clés d’abonnement d’essai à partir de la page [d’abonnement à Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Après avoir sélectionné l’API Microsoft Speech, sélectionnez **Obtenir la clé API** pour obtenir la clé. Cette opération renvoie une clé primaire et une clé secondaire. Les deux clés étant liées au même quota, vous pouvez utiliser l’une ou l’autre.

Si vous souhaitez utiliser la *reconnaissance de l’intention*, vous devez également vous inscrire au [service LUIS (Language Understanding Intelligent Service)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
> * Obtenez une clé d’abonnement. Pour pouvoir utiliser des bibliothèques de client Speech, vous devez disposer d’une [clé d’abonnement](https://azure.microsoft.com/try/cognitive-services/).
>
> * Utilisez votre clé d’abonnement. Avec l’exemple d’application iOS fourni, vous devez mettre à jour le fichier Samples/SpeechRecognitionServerExample/settings.plist avec votre clé d’abonnement. Pour plus d’informations, consultez [Générer et exécuter des exemples](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Utiliser la bibliothèque cliente Speech

Pour ajouter la bibliothèque cliente à un projet XCode, suivez ces [instructions](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-client-library).

Pour obtenir les informations de référence sur la bibliothèque cliente pour iOS, consultez cette [page web](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html).

## <a name="build-and-run-samples"></a>Générer et exécuter des exemples

Pour plus d’informations sur la génération et l’exécution des exemples, consultez cette [page Lisez-moi](https://github.com/Azure-Samples/Cognitive-Speech-STT-iOS#the-sample).

## <a name="samples-explained"></a>Exemples expliqués

### <a name="create-recognition-clients"></a>Créer des clients de reconnaissance

Le code suivant dans l’exemple montre comment créer des classes clientes de reconnaissance basées sur des scénarios utilisateur :

```
{
    NSString* language = @"en-us";

    NSString* path = [[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"];
    NSDictionary* settings = [[NSDictionary alloc] initWithContentsOfFile:path];

    NSString* primaryOrSecondaryKey = [settings objectForKey:(@"primaryKey")];
    NSString* luisAppID = [settings objectForKey:(@"luisAppID")];
    NSString* luisSubscriptionID = [settings objectForKey:(@"luisSubscriptionID")];

    if (isMicrophoneReco) {
        if (!isIntent) {
            micClient = [SpeechRecognitionServiceFactory createMicrophoneClient:(recoMode)
                                                                   withLanguage:(language)
                                                                        withKey:(primaryOrSecondaryKey)
                                                                   withProtocol:(self)];
        }
        else {
            MicrophoneRecognitionClientWithIntent* micIntentClient;
            micIntentClient = [SpeechRecognitionServiceFactory createMicrophoneClientWithIntent:(language)
                                                                                        withKey:(primaryOrSecondaryKey)
                                                                                  withLUISAppID:(luisAppID)
                                                                                 withLUISSecret:(luisSubscriptionID)
                                                                                   withProtocol:(self)];
            micClient = micIntentClient;
        }
    }
    else {
        if (!isIntent) {
            dataClient = [SpeechRecognitionServiceFactory createDataClient:(recoMode)
                                                              withLanguage:(language)
                                                                   withKey:(primaryOrSecondaryKey)
                                                              withProtocol:(self)];
        }
        else {
            DataRecognitionClientWithIntent* dataIntentClient;
            dataIntentClient = [SpeechRecognitionServiceFactory createDataClientWithIntent:(language)
                                                                                   withKey:(primaryOrSecondaryKey)
                                                                             withLUISAppID:(luisAppID)
                                                                            withLUISSecret:(luisSubscriptionID)
                                                                              withProtocol:(self)];
            dataClient = dataIntentClient;
        }
    }
}

```

La bibliothèque cliente fournit des classes clientes de reconnaissance pré-implémentées pour des scénarios de reconnaissance vocale classiques :

* `DataRecognitionClient` : reconnaissance vocale avec des données PCM (par exemple, à partir d’un fichier ou d’une source audio). Les données sont réparties sur des mémoires tampons, puis chaque mémoire tampon est envoyée au service Speech. Aucune modification n’étant apportée aux mémoires tampons, les utilisateurs peuvent appliquer leur propre détection de silence, s’ils le souhaitent. Si les données proviennent de fichiers WAV, vous pouvez les envoyer directement au serveur. Si vous avez des données brutes, par exemple, du contenu audio via Bluetooth, vous envoyez un en-tête de format au serveur, puis les données.
* `MicrophoneRecognitionClient` : reconnaissance vocale avec contenu audio provenant du microphone. Vérifiez que le microphone est allumé et que les données du microphone sont envoyées au service de reconnaissance vocale. Un détecteur de silence intégré est appliqué aux données du microphone avant qu’elles ne soient envoyées au service de reconnaissance.
* `DataRecognitionClientWithIntent` et `MicrophoneRecognitionClientWithIntent` : en plus du texte de reconnaissance, ces clients retournent des informations structurées sur l’intention de l’orateur, à partir desquelles vos applications peuvent effectuer des actions. Pour utiliser la fonctionnalité d’intention, vous devez entraîner un modèle à l’aide du service [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Langue de reconnaissance

Quand vous utilisez `SpeechRecognitionServiceFactory` pour créer le client, vous devez sélectionner une langue. Pour obtenir la liste complète des langues prises en charge par le service Speech, consultez [Langues prises en charge](../API-Reference-REST/supportedlanguages.md).

### <a name="speechrecognitionmode"></a>SpeechRecognitionMode

Vous devez également spécifier `SpeechRecognitionMode` quand vous créez le client avec `SpeechRecognitionServiceFactory` :

* `SpeechRecognitionMode_ShortPhrase` : énoncé d’une longueur de 15 secondes maximum. À mesure que les données sont envoyées au service, le client reçoit plusieurs résultats partiels et un résultat final avec les n-meilleurs choix.
* `SpeechRecognitionMode_LongDictation` : énoncé d’une longueur jusqu’à deux minutes. À mesure que les données sont envoyées au service, le client reçoit plusieurs résultats partiels et plusieurs résultats finaux, selon les emplacements où le serveur identifie des pauses dans la phrase.

### <a name="attach-event-handlers"></a>Attacher des gestionnaires d’événements

Vous pouvez attacher plusieurs gestionnaires d’événements au client que vous avez créé :

* **Événements de résultats partiels** : cet événement est appelé chaque fois que le service Speech prédit ce que vous pouvez dire, avant même que vous n’ayez terminé de parler (si vous utilisez `MicrophoneRecognitionClient`) ou d’envoyer les données (si vous utilisez `DataRecognitionClient`).
* **Événements d’erreur** : appelé quand le service détecte une erreur.
* **Événements d’intention** : cet événement est appelé sur les clients « WithIntent » (uniquement en mode ShortPhrase) une fois le résultat final de la reconnaissance converti en intention JSON structurée.
* **Événements de résultat** :
  * En mode `SpeechRecognitionMode_ShortPhrase`, cet événement est appelé et renvoie les n meilleurs résultats une fois que vous avez terminé de parler.
  * En mode `SpeechRecognitionMode_LongDictation`, le gestionnaire d’événements est appelé plusieurs fois, selon les emplacements où le service identifie des pauses dans la phrase.
  * **Pour chacun des n meilleurs choix**, une valeur de confiance et plusieurs formes différentes du texte reconnu sont renvoyées. Pour plus d’informations, consultez [Format de sortie](../Concepts.md#output-format).

## <a name="related-topics"></a>Rubriques connexes

* [Informations de référence sur la bibliothèque cliente pour iOS](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-iOS/master/com.Microsoft.SpeechSDK-1_0-for-iOS.docset/Contents/Resources/Documents/index.html)
* [Bien démarrer avec l’intention et/ou la reconnaissance vocale Microsoft en Java sur Android](GetStartedJavaAndroid.md)
* [Bien démarrer avec l’API Microsoft Speech en JavaScript](GetStartedJSWebsockets.md)
* [Bien démarrer avec l’API Microsoft Speech à l’aide de REST](GetStartedREST.md)
