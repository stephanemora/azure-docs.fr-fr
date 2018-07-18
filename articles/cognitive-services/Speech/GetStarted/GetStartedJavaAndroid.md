---
title: Bien démarrer avec l’API Reconnaissance vocale Microsoft en Java sur Android | Microsoft Docs
description: Utilisez l’API Microsoft Speech pour développer des applications Android qui convertissent un enregistrement vocal en texte.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/29/2017
ms.author: zhouwang
ms.openlocfilehash: a10f7be1c36fb431016a9867f606e26be858069e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368420"
---
# <a name="get-started-with-speech-recognition-in-java-on-android"></a>Bien démarrer avec l’API de reconnaissance vocale en Java sur Android

Avec l’API Reconnaissance vocale, vous pouvez développer des applications Android qui utilisent le service informatique Speech pour convertir un contenu audio en texte. L’API prend en charge la diffusion en continu en temps réel, afin que votre application puisse de façon simultanée et asynchrone recevoir des résultats de reconnaissance partiels et envoyer du contenu audio au service.

Cet article utilise un exemple d’application pour expliquer comment utiliser la bibliothèque de client Speech pour Android, afin de développer des applications de reconnaissance vocale en Java pour des appareils Android.

## <a name="prerequisites"></a>Prérequis

### <a name="platform-requirements"></a>Exigences relatives à la plateforme

L’exemple est développé par [Android Studio](http://developer.android.com/sdk/index.html) pour Windows en Java.

### <a name="get-the-client-library-and-sample-application"></a>Obtenir la bibliothèque de client et l’exemple d'application

La bibliothèque de client et les exemples Speech pour Android sont disponibles dans le [Kit de développement logiciel (SDK) client Speech pour Android](https://github.com/microsoft/cognitive-speech-stt-android). Vous pouvez trouver l’exemple pouvant être généré dans le répertoire samples/SpeechRecoExample. Vous pouvez trouver les deux bibliothèques nécessaires dans vos propres applications dans SpeechSDK/libs, sous le dossier armeabi et x86. La taille du fichier libandroid_platform.so est de 22 Mo, mais est réduite à 4 Mo au moment du déploiement.

#### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>S’abonner à l’API Microsoft Speech et obtenir une clé d’abonnement d’essai

L’API Microsoft Speech fait partie de Cognitive Services (anciennement Project Oxford). Vous pouvez obtenir des clés d’abonnement d’essai à partir de la page [d’abonnement à Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Après avoir sélectionné l’API Microsoft Speech, sélectionnez **Obtenir la clé API** pour obtenir la clé. Cette opération renvoie une clé primaire et une clé secondaire. Les deux clés étant liées au même quota, vous pouvez utiliser l’une ou l’autre.

Si vous souhaitez utiliser la *reconnaissance de l’intention*, vous devez également vous inscrire au [service LUIS (Language Understanding Intelligent Service)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

> [!IMPORTANT]
>* Obtenez une clé d’abonnement. Pour pouvoir utiliser des bibliothèques de client Speech, vous devez disposer d’une [clé d’abonnement](https://azure.microsoft.com/try/cognitive-services/).
>
>* Utilisez votre clé d’abonnement. Avec l’exemple d'application Android fourni, mettez à jour le fichier samples/SpeechRecoExample/res/values/strings.xml avec vos clés d’abonnement. Pour plus d’informations, consultez [Générer et exécuter des exemples](#build-and-run-samples).

## <a name="use-the-speech-client-library"></a>Utiliser la bibliothèque de client Speech

Pour utiliser la bibliothèque de client dans votre application, suivez les [instructions](https://github.com/microsoft/cognitive-speech-stt-android#the-client-library).

Vous pouvez trouver la référence de bibliothèque de client pour Android dans le dossier de documents du [Kit de développement logiciel (SDK) Speech pour Android](https://github.com/microsoft/cognitive-speech-stt-android).

## <a name="build-and-run-samples"></a>Générer et exécuter des exemples

Pour apprendre à générer et exécuter des exemples, consultez cette [page Lisez-moi](https://github.com/microsoft/cognitive-speech-stt-android#the-sample).

## <a name="samples-explained"></a>Exemples expliqués

### <a name="create-recognition-clients"></a>Créer des clients de reconnaissance

Le code dans l’exemple suivant montre comment créer des classes clientes de reconnaissance basées sur des scénarios utilisateur :

```java
void initializeRecoClient()
    {
        String language = "en-us";

        String subscriptionKey = this.getString(R.string.subscription_key);
        String luisAppID = this.getString(R.string.luisAppID);
        String luisSubscriptionID = this.getString(R.string.luisSubscriptionID);

        if (m_isMicrophoneReco && null == m_micClient) {
            if (!m_isIntent) {
                m_micClient = SpeechRecognitionServiceFactory.createMicrophoneClient(this,
                                                                                     m_recoMode,
                                                                                     language,
                                                                                     this,
                                                                                     subscriptionKey);
            }
            else {
                MicrophoneRecognitionClientWithIntent intentMicClient;
                intentMicClient = SpeechRecognitionServiceFactory.createMicrophoneClientWithIntent(this,
                                                                                                   language,
                                                                                                   this,
                                                                                                   subscriptionKey,
                                                                                                   luisAppID,
                                                                                                   luisSubscriptionID);
                m_micClient = intentMicClient;

            }
        }
        else if (!m_isMicrophoneReco && null == m_dataClient) {
            if (!m_isIntent) {
                m_dataClient = SpeechRecognitionServiceFactory.createDataClient(this,
                                                                                m_recoMode,
                                                                                language,
                                                                                this,
                                                                                subscriptionKey);
            }
            else {
                DataRecognitionClientWithIntent intentDataClient;
                intentDataClient = SpeechRecognitionServiceFactory.createDataClientWithIntent(this,
                                                                                              language,
                                                                                              this,
                                                                                              subscriptionKey,
                                                                                              luisAppID,
                                                                                              luisSubscriptionID);
                m_dataClient = intentDataClient;
            }
        }
    }

```

La bibliothèque de client fournit des classes clientes de reconnaissance pré-implémentées pour des scénarios de reconnaissance vocale classiques :

* `DataRecognitionClient` : reconnaissance vocale avec des données PCM (par exemple, à partir d’un fichier ou d’une source audio). Les données sont réparties sur des mémoires tampons, puis chaque mémoire tampon est envoyée au service Speech. Aucune modification n’étant apportée aux mémoires tampons, l’utilisateur peut appliquer sa propre détection de silence, s’il le souhaite. Si les données proviennent de fichiers WAV, vous pouvez les envoyer directement au service Speech. Si vous avez des données brutes, par exemple, du contenu audio via Bluetooth, vous envoyez un en-tête de format au service Speech, puis les données.
* `MicrophoneRecognitionClient` : reconnaissance vocale avec contenu audio provenant du microphone. Vérifiez que le microphone est allumé et que les données du microphone sont envoyées au service de reconnaissance vocale. Un détecteur de silence intégré est appliqué aux données du microphone avant qu’elles ne soient envoyées au service de reconnaissance.
* `DataRecognitionClientWithIntent` et `MicrophoneRecognitionClientWithIntent` : en plus du texte de reconnaissance, ces clients renvoient des informations structurées sur l’intention de l’orateur, à partir desquelles vos applications peuvent effectuer des actions pour vos applications. Pour utiliser la fonctionnalité d’intention, vous devez former un modèle à l’aide du service [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

### <a name="recognition-language"></a>Langue de reconnaissance

Quand vous utilisez `SpeechRecognitionServiceFactory` pour créer le client, vous devez sélectionner une langue. Pour obtenir la liste complète des langues prises en charge par le service Speech, consultez [Langues prises en charge](../API-Reference-REST/supportedlanguages.md).

### `SpeechRecognitionMode`

Vous devez également spécifier `SpeechRecognitionMode` quand vous créez le client avec `SpeechRecognitionServiceFactory` :

* `ShortPhrase` : énoncé d’une longueur de 15 secondes maximum. À mesure que les données sont envoyées au service, le client reçoit plusieurs résultats partiels et un résultat final avec les n-meilleurs choix.
* `LongDictation` : énoncé d’une longueur de deux minutes maximum. À mesure que les données sont envoyées au service, le client reçoit plusieurs résultats partiels et plusieurs résultats finaux, selon les emplacements où le service identifie des pauses dans la phrase.

### <a name="attach-event-handlers"></a>Joindre des gestionnaires d’événements

Vous pouvez joindre plusieurs gestionnaires d’événements au client que vous avez créé :

* **Événements de résultats partiels** : cet événement est appelé chaque fois que le service Speech prédit ce que vous pouvez dire, avant même que vous n’ayez terminé de parler (si vous utilisez `MicrophoneRecognitionClient`) ou d’envoyer les données (si vous utilisez `DataRecognitionClient`).
* **Événements d’erreur** : cet événement est appelé quand le service détecte une erreur.
* **Événements d’intention** : cet événement est appelé sur les clients « WithIntent » (uniquement en mode `ShortPhrase`) une fois le résultat final de la reconnaissance converti en intention JSON structurée.
* **Événements de résultat** :
  * En mode `ShortPhrase`, cet événement est appelé et renvoie les n meilleurs résultats une fois que vous avez terminé de parler.
  * En mode `LongDictation`, le gestionnaire d’événements est appelé plusieurs fois, selon les emplacements où le service identifie des pauses dans la phrase.
  * **Pour chacun des n meilleurs choix**, une valeur de confiance et plusieurs formes différentes du texte reconnu sont renvoyées. Pour plus d’informations, consultez [Format de sortie](../Concepts.md#output-format).

## <a name="related-topics"></a>Rubriques connexes

* [Informations de référence sur la bibliothèque cliente pour Android](https://github.com/Azure-Samples/Cognitive-Speech-STT-Android/tree/master/docs)
* [Get started with the Speech Recognition API in C# for .NET on Windows](GetStartedCSharpDesktop.md) (Prendre en main l’API Microsoft Speech en C# pour .NET)
* [Bien démarrer avec l’API Reconnaissance vocale en Objective-C sur iOS](Get-Started-ObjectiveC-iOS.md)
* [Bien démarrer avec l’API Reconnaissance vocale en JavaScript](GetStartedJSWebsockets.md)
* [Bien démarrer avec l’API Microsoft Speech à l’aide de REST](GetStartedREST.md)
