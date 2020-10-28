---
title: Guide de démarrage rapide sur la création de mot clé - Service de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Votre appareil est toujours à l’écoute d’un mot (ou d’une phrase) clé. Quand l’utilisateur prononce le mot clé, l’appareil envoie tous les sons suivants vers le cloud, jusqu’à ce que l’utilisateur arrête de parler. Pour différencier votre appareil et renforcer votre marque, vous pouvez personnaliser votre mot clé.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 2ed5c554e6307b08c412de16d1bb92b458c5f15f
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92166452"
---
# <a name="get-started-with-custom-keyword"></a>Bien démarrer avec Mot clé personnalisé

Dans ce guide de démarrage rapide, vous allez découvrir les principes de base de l’utilisation des mots clés personnalisés, en utilisant Speech Studio et le kit SDK Speech. Un mot clé est un mot ou une expression courte qui permet d’activer la fonction vocale de votre produit. Vous créez des modèles de mots clés dans Speech Studio, puis vous exportez un fichier de modèle que vous utilisez avec le kit SDK Speech dans vos applications.

## <a name="prerequisites"></a>Prérequis

Les étapes décrites dans cet article nécessitent un abonnement Speech et le kit SDK Speech. Si vous n’avez pas encore d’abonnement, [essayez gratuitement le service Speech](overview.md#try-the-speech-service-for-free). Pour obtenir le kit SDK, consultez le [guide d’installation](quickstarts/setup-platform.md) pour votre plateforme.

## <a name="create-a-keyword-in-speech-studio"></a>Créer un mot clé dans Speech Studio

Avant de pouvoir utiliser un mot clé personnalisé, vous devez créer ce mot clé dans la page [Mot clé personnalisé](https://aka.ms/sdsdk-wakewordportal), dans [Speech Studio](https://aka.ms/sdsdk-speechportal). Une fois que vous avez fourni un mot clé, le logiciel génère un fichier `.table` que vous pouvez utiliser avec le kit SDK Speech.

> [!IMPORTANT]
> Les modèles de mots clés personnalisés et les fichiers `.table` résultants peuvent **uniquement** être créés dans Speech Studio.
> Vous ne pouvez pas créer de mots clés personnalisés à partir du kit SDK ni avec des appels REST.

1. Accédez au [Speech Studio](https://aka.ms/sdsdk-speechportal) et **connectez-vous** , ou, si vous n’avez pas d’abonnement vocal, choisissez [**Créer un abonnement**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Sur la page [Mot clé personnalisé](https://aka.ms/sdsdk-wakewordportal), créez un **Nouveau projet** . 

1. Entrez un **Nom** et une **Description** facultative, puis sélectionnez la langue. Vous avez besoin d’un projet par langue et la prise en charge est actuellement limitée à la langue `en-US` (anglais des États-Unis).

    ![Décrire votre projet de mot clé](media/custom-keyword/custom-kws-portal-new-project.png)

1. Sélectionnez votre projet dans la liste. 

    ![Sélectionner votre projet de mot clé](media/custom-keyword/custom-kws-portal-project-list.png)

1. Pour créer un nouveau modèle de mot clé, cliquez sur **Entraîner le modèle** .

1. Entrez un **nom** pour le modèle, une **description** facultative et le **mot clé** de votre choix, puis cliquez sur **Suivant** . Consultez les [instructions](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) sur le choix d’un mot clé efficace.

    ![Entrez votre mot clé](media/custom-keyword/custom-kws-portal-new-model.png)

1. Le portail crée des propositions de prononciation pour votre mot clé. Écoutez chaque prononciation en cliquant sur les boutons de lecture, supprimez les coches en regard des prononciations incorrectes. Une fois que seules les bonnes prononciations sont cochées, cliquez sur **Entraîner** pour commencer à générer le modèle de mot clé. 

    ![Capture d’écran montrant où vous choisissez les bonnes prononciations.](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. La génération du modèle peut prendre jusqu’à trente minutes. La liste de mots clés passe de **Traitement en cours** à **Terminé** lorsque le modèle est terminé. Vous pouvez ensuite télécharger le fichier.

    ![Vérifier votre mot clé](media/custom-keyword/custom-kws-portal-download-model.png)

1. Le fichier téléchargé est une archive `.zip`. Extrayez l’archive et un fichier avec l’extension `.table` apparaît. Il s’agit du fichier que vous allez utiliser avec le kit SDK dans la section suivante. Veillez donc à noter son chemin d’accès. Le nom de fichier reflète votre nom de mot clé. Par exemple, un mot clé **Activate device** (Activer l’appareil) a le nom de fichier `Activate_device.table`.

## <a name="use-a-keyword-model-with-the-sdk"></a>Utiliser un modèle de mot clé avec le kit SDK

Tout d’abord, chargez votre fichier de modèle de mot clé à l’aide de la fonction statique `FromFile()`, qui retourne un élément `KeywordRecognitionModel`. Utilisez le chemin du fichier `.table` que vous avez téléchargé à partir de Speech Studio. De plus, vous créez un fichier `AudioConfig` à l’aide du microphone par défaut, puis instanciez un nouveau `KeywordRecognizer` à l’aide de la configuration audio.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Ensuite, l’exécution de la reconnaissance de mot clé s’effectue avec un appel à `RecognizeOnceAsync()` en transmettant votre objet de modèle. Cela démarre une session de reconnaissance de mots clés qui dure jusqu’à ce que le mot clé soit reconnu. Ainsi, vous utilisez généralement ce modèle de conception dans les applications multithread ou dans les cas d’utilisation où vous pouvez attendre indéfiniment un mot de mise en éveil.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> L’exemple indiqué ici utilise la reconnaissance de mot clé locale, car il ne demande pas d’objet `SpeechConfig` pour le contexte d’authentification et ne contacte pas le back-end. Toutefois, vous pouvez exécuter la reconnaissance de mot clé et la vérification [en utilisant une connexion continue au back-end](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword).

## <a name="next-steps"></a>Étapes suivantes

Testez votre mot clé personnalisé avec le [Guide de démarrage rapide du kit SDK Speech Devices](https://aka.ms/sdsdk-quickstart).
