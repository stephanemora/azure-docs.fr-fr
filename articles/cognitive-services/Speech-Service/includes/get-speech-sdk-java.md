---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 59835890a5baaf050868951ea683fd97b795ce3e
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656511"
---
:::row:::
    :::column span="3":::
        Le kit de développement logiciel (SDK) Java pour Android est empaqueté au format <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR (bibliothèque Android)<span class="docon docon-navigate-external x-hidden-focus"></span></a>, qui inclut les bibliothèques nécessaires et les autorisations Android requises. Il est hébergé dans un référentiel Maven dans `https://csspeechstorage.blob.core.windows.net/maven/` sous la forme d’un package `com.microsoft.cognitiveservices.speech:client-sdk:1.11.0`.
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
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.11.0'
  ```

Le Kit de développement logiciel (SDK) Java fait aussi partie du [Kit de développement logiciel (SDK) Speech Devices](../speech-devices-sdk.md).

#### <a name="additional-resources"></a>Ressources supplémentaires

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Code source de démarrage rapide Java spécifique à Android <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Code source de démarrage rapide Java Runtime (JRE) <span class="docon docon-navigate-external x-hidden-focus"></span></a>