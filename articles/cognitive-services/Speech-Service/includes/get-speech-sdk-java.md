---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: eur
ms.openlocfilehash: 8ceec5d1b6dbc431263d1e2fe989e2a1b6d1ec0b
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131506561"
---
:::row:::
    :::column span="3":::
        Le kit de développement logiciel (SDK) Java pour Android est empaqueté au format <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR (bibliothèque Android)</a>, qui inclut les bibliothèques nécessaires et les autorisations Android requises. Il est hébergé dans un référentiel Maven dans `https://csspeechstorage.blob.core.windows.net/maven/` sous la forme d’un package `com.microsoft.cognitiveservices.speech:client-sdk:1.17.0`. (Assurez-vous que la version 1.17.0 correspond à la version la plus récente en [effectuant une recherche sur notre référentiel GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/search?q=com.microsoft.cognitiveservices.speech%3Aclient-sdk).)
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Pour utiliser le package dans votre projet Android Studio, effectuez les changements suivants :

1. Dans le fichier *build.gradle* au niveau du projet, ajoutez le texte suivant à la section `repositories` :
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. Dans le fichier *build.gradle* au niveau du module, ajoutez le texte suivant à la section `dependencies` :
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.17.0'
  ```

Le Kit de développement logiciel (SDK) Java fait aussi partie du [Kit de développement logiciel (SDK) Speech Devices](../speech-devices-sdk.md).

#### <a name="additional-resources"></a>Ressources supplémentaires

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Code source de démarrage rapide Java spécifique à Android </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Code source de démarrage rapide Java Runtime (JRE) </a>
