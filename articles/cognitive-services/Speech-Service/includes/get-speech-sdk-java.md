---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: d7d41a875d8e0c30085bafd346e316672359de26
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87375211"
---
:::row:::
    :::column span="3":::
        Le kit de développement logiciel (SDK) Java pour Android est empaqueté au format <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR (bibliothèque Android)<span class="docon docon-navigate-external x-hidden-focus"></span></a>, qui inclut les bibliothèques nécessaires et les autorisations Android requises. Il est hébergé dans un référentiel Maven dans `https://csspeechstorage.blob.core.windows.net/maven/` sous la forme d’un package `com.microsoft.cognitiveservices.speech:client-sdk:1.13.0`.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Pour utiliser le package dans votre projet Android Studio, effectuez les changements suivants :

1. Dans le fichier *build.gradle* au niveau du projet, ajoutez le texte suivant à la section `repository` :
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. Dans le fichier *build.gradle* au niveau du module, ajoutez le texte suivant à la section `dependencies` :
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.13.0'
  ```

Le Kit de développement logiciel (SDK) Java fait aussi partie du [Kit de développement logiciel (SDK) Speech Devices](../speech-devices-sdk.md).

#### <a name="additional-resources"></a>Ressources supplémentaires

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Code source de démarrage rapide Java spécifique à Android <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Code source de démarrage rapide Java Runtime (JRE) <span class="docon docon-navigate-external x-hidden-focus"></span></a>