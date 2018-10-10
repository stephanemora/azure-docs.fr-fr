---
title: Bien démarrer avec l’API Reconnaissance vocale de Microsoft à l’aide de la bibliothèque de services C# | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Utilisez la bibliothèque de services de la Reconnaissance vocale Bing pour convertir une langue parlée en texte.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: 5716f7a0af16bd3e40dea4468e2fae884f911718
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967555"
---
# <a name="quickstart-use-the-bing-speech-recognition-service-library-in-c35-for-net-windows"></a>Guide de démarrage rapide : Utiliser la bibliothèque de services de la Reconnaissance vocale Bing en C&#35; pour Windows .NET

La bibliothèque de services est destinée aux développeurs qui ont leur propre service cloud et souhaitent appeler le service Speech à partir de leur service. Si vous souhaitez appeler le service de reconnaissance vocale à partir d’applications liées à l’appareil, n’utilisez pas ce SDK. (Utilisez d’autres bibliothèques clientes ou des API REST à cette fin.)

Pour utiliser la bibliothèque de services C#, installez le [package NuGet Microsoft.Bing.Speech](https://www.nuget.org/packages/Microsoft.Bing.Speech/). Pour obtenir des informations de référence sur l’API de la bibliothèque, consultez la [bibliothèque de services C# Microsoft Speech](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

Les sections suivantes décrivent comment installer, générer et exécuter l’exemple d’application C# à l’aide de la bibliothèque de services C#.

## <a name="prerequisites"></a>Prérequis

### <a name="platform-requirements"></a>Plateforme requise

L’exemple suivant a été développé pour Windows 8+ et .NET 4.5+ Framework à l’aide de [Visual Studio 2015 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Obtenir l’exemple d’application

Clonez l’exemple à partir du dépôt de [l’exemple de bibliothèque de services C# Speech](https://github.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>S’abonner à l’API Reconnaissance vocale et obtenir une clé d’abonnement d’essai

L’API Microsoft Speech fait partie de Cognitive Services (anciennement Project Oxford). Vous pouvez obtenir des clés d’abonnement d’essai à partir de la page [d’abonnement à Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Après avoir sélectionné l’API Microsoft Speech, sélectionnez **Obtenir la clé API** pour obtenir la clé. Cette opération renvoie une clé primaire et une clé secondaire. Les deux clés étant liées au même quota, vous pouvez utiliser l’une ou l’autre.

> [!IMPORTANT]
> * Obtenez une clé d’abonnement. Pour pouvoir utiliser les bibliothèques clientes Speech, vous devez disposer d’une [clé d’abonnement](https://azure.microsoft.com/try/cognitive-services/).
>
> * Utilisez votre clé d’abonnement. Avec l’exemple d’application de bibliothèque de services C# fourni, vous devez fournir votre clé d’abonnement en tant qu’un des paramètres de ligne de commande. Pour plus d’informations, consultez [Exécuter l’exemple d’application](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Étape 1 : Installer l’exemple d’application

1. Démarrez Visual Studio 2015 et sélectionnez **Fichier** > **Ouvrir** > **Projet/Solution**.

2. Double-cliquez pour ouvrir le fichier solution (.sln) Visual Studio 2015 nommé SpeechClient.sln. La solution s’ouvre dans Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Étape 2 : Générer l’exemple d’application

Appuyez sur Ctrl+Maj+B ou sélectionnez **Générer** dans le menu du ruban. Ensuite, sélectionnez **Générer la solution**.

## <a name="step-3-run-the-sample-application"></a>Étape 3 : Exécuter l’exemple d’application

1. Une fois la génération terminée, appuyez sur F5 ou sélectionnez **Démarrer** dans le menu du ruban pour exécuter l’exemple.

2. Ouvrez le répertoire de sortie de l’exemple, par exemple, SpeechClientSample\bin\Debug. Appuyez sur Maj+Clic droit, puis sélectionnez **Ouvrir une fenêtre de commandes ici**.

3. Exécutez `SpeechClientSample.exe` avec les arguments suivants :

   * Arg [0] : spécifiez un fichier WAV audio d’entrée.
   * Arg [1] : spécifiez les paramètres régionaux audio.
   * Arg [2] : spécifiez les modes de reconnaissance : *Short* pour le mode `ShortPhrase` et *Long* pour le mode `LongDictation`.
   * Arg [3] : spécifiez la clé d’abonnement permettant d’accéder au service de reconnaissance vocale.

## <a name="samples-explained"></a>Exemples expliqués

### <a name="recognition-modes"></a>Modes de reconnaissance

* Mode `ShortPhrase` : énoncé d’une longueur jusqu’à 15 secondes. À mesure que les données sont envoyées au serveur, le client reçoit plusieurs résultats partiels et un meilleur résultat final.
* Mode `LongDictation` : énoncé d’une longueur jusqu’à 10 minutes. À mesure que les données sont envoyées au serveur, le client reçoit plusieurs résultats partiels et plusieurs résultats finaux, selon les emplacements où le serveur indique des pauses dans la phrase.

### <a name="supported-audio-formats"></a>Formats audio pris en charge

L’API Microsoft Speech prend en charge les contenus audio/WAV en utilisant les codecs suivants :

* Canal unique PCM
* Siren
* SirenSR

### <a name="preferences"></a>Preferences

Pour créer un SpeechClient, vous devez d’abord créer un objet Preferences. L’objet Preferences est un ensemble de paramètres qui configure le comportement du service de reconnaissance vocale. Il comprend les champs suivants :

* `SpeechLanguage` : paramètres régionaux du contenu audio envoyé au service de reconnaissance vocale.
* `ServiceUri` : point de terminaison utilisé pour appeler le service de reconnaissance vocale.
* `AuthorizationProvider` : implémentation d’IAuthorizationProvider permettant d’extraire des jetons afin d’accéder au service de reconnaissance vocale. Bien que l’exemple procure un fournisseur d’autorisation Cognitive Services, nous vous recommandons vivement de créer votre propre implémentation pour gérer la mise en cache des jetons.
* `EnableAudioBuffering` : option avancée. Consultez [Gestion des connexions](#connection-management).

### <a name="speech-input"></a>SpeechInput

L’objet SpeechInput se compose de deux champs :

* **Audio** : implémentation de flux de votre choix de laquelle le SDK extrait le contenu audio. Il peut s’agir de tout [flux](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx) prenant en charge la lecture.
   > [!NOTE]
   > Le SDK détecte la fin du flux quand celui-ci retourne **0** en lecture.

* **RequestMetadata** : métadonnées relatives à la demande de reconnaissance vocale. Pour plus d’informations, consultez les [informations de référence](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html).

### <a name="speech-request"></a>Demande de reconnaissance vocale

Une fois que vous avez instancié un objet SpeechClient et un objet SpeechInput, utilisez RecognizeAsync pour effectuer une demande auprès du service Speech.

```cs
    var task = speechClient.RecognizeAsync(speechInput);
```

Une fois la demande terminée, la tâche retournée par RecognizeAsync se termine. Le dernier RecognitionResult marque la fin de la reconnaissance. La tâche peut échouer si le service ou le SDK échoue de manière inattendue.

### <a name="speech-recognition-events"></a>Événements de reconnaissance vocale

#### <a name="partial-results-event"></a>Événement de résultats partiels

Cet événement est appelé chaque fois que le service Speech prédit ce que vous pouvez dire, avant même que vous n’ayez terminé de parler (si vous utilisez `MicrophoneRecognitionClient`) ou d’envoyer les données (si vous utilisez `DataRecognitionClient`). Vous pouvez vous abonner à l’événement à l’aide de `SpeechClient.SubscribeToPartialResult()`. Vous pouvez également utiliser la méthode d’abonnement aux événements génériques `SpeechClient.SubscribeTo<RecognitionPartialResult>()`.

**Format de retour** | Description |
------|------
**LexicalForm** | Ce format est optimal pour les applications nécessitant des résultats de reconnaissance vocale bruts non traités.
**DisplayText** | Expression reconnue avec application de la normalisation de texte inverse, de la casse, des signes de ponctuation et du masquage des vulgarités. Les vulgarités sont masquées par des astérisques après le caractère initial, par exemple, « b*** ». Ce format est optimal pour les applications qui affichent les résultats de la reconnaissance vocale à l’utilisateur.
**Confidence** | Niveau de confiance que l’expression reconnue représente pour le contenu audio associé tel que défini par le serveur de reconnaissance vocale.
**MediaTime** | Heure actuelle par rapport au début du flux audio (en unités de temps de 100 nanosecondes).
**MediaDuration** | Longueur/durée de l’expression actuelle par rapport au segment audio (en unités de temps de 100 nanosecondes).

#### <a name="result-event"></a>Événement de résultat
Quand vous avez terminé de parler (en mode `ShortPhrase`), cet événement est appelé. Vous obtenez n-meilleurs choix pour le résultat. En mode `LongDictation`, l’événement peut être appelé plusieurs fois, selon les emplacements où le serveur indique des pauses dans la phrase. Vous pouvez vous abonner à l’événement à l’aide de `SpeechClient.SubscribeToRecognitionResult()`. Vous pouvez également utiliser la méthode d’abonnement aux événements génériques `SpeechClient.SubscribeTo<RecognitionResult>()`.

**Format de retour** | Description |
------|------|
**RecognitionStatus** | État sur la façon dont la reconnaissance a été générée. Par exemple, a-t-elle été générée après une reconnaissance réussie ou l’annulation de la connexion, etc. ?
**Expressions** | Ensemble des n-meilleures expressions reconnues avec le degré de confiance de la reconnaissance.

Pour plus d’informations sur les résultats de la reconnaissance, consultez [Format de sortie](../Concepts.md#output-format).

### <a name="speech-recognition-response"></a>Réponse de la reconnaissance vocale

Exemple de réponse de la reconnaissance vocale :
```
--- Partial result received by OnPartialResult  
---what  
--- Partial result received by OnPartialResult  
--what's  
--- Partial result received by OnPartialResult  
---what's the web  
--- Partial result received by OnPartialResult   
---what's the weather like  
---***** Phrase Recognition Status = [Success]   
***What's the weather like? (Confidence:High)  
What's the weather like? (Confidence:High) 
```

## <a name="connection-management"></a>Gestion des connexions

L’API utilise une seule connexion WebSocket par demande. Pour une expérience utilisateur optimale, le SDK tente de se reconnecter au service Speech et de démarrer la reconnaissance à partir du dernier RecognitionResult qu’il a reçu. Par exemple, si la demande de contenu audio dure deux minutes, que le SDK a reçu un RecognitionEvent à la marque d’une minute et qu’une erreur réseau s’est produite après cinq secondes, le SDK démarre une nouvelle connexion qui commence à la marque d’une minute.

>[!NOTE]
>Le SDK n’effectue pas de recherche vers la marque d’une minute, car le flux ne prend peut-être pas en charge la recherche. Au lieu de cela, le SDK conserve une mémoire tampon interne qu’il utilise pour mettre en mémoire le contenu audio et efface la mémoire tampon quand il reçoit des événements RecognitionResult.

## <a name="buffering-behavior"></a>Comportement de la mise en mémoire tampon

Par défaut, le SDK met en mémoire tampon le contenu audio afin qu’il puisse le récupérer en cas d’interruption réseau. Dans un scénario où il est préférable d’ignorer le contenu audio perdu pendant une déconnexion du réseau et de redémarrer la connexion, il vaut mieux désactiver la mise en mémoire tampon du contenu audio en définissant `EnableAudioBuffering` sur `false` dans l’objet Preferences.

## <a name="related-topics"></a>Rubriques connexes

[Informations de référence sur la bibliothèque de services C# Microsoft Speech](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-ServiceLibrary/master/docs/index.html)
