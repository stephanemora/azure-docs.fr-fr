---
title: Documentation du kit SDK Speech de Cognitive Services | Microsoft Docs
description: Notes de publication - ce qui a changé dans les versions les plus récentes
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: cfc8f973ac9bced9396a081f7fb2ab6a4a1b2f7f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963081"
---
# <a name="release-notes"></a>Notes de publication

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>SDK Speech de Cognitive Services version 1.0.0 : septembre 2018

**Nouvelles fonctionnalités**

* Prise en charge d’Objective-C sur iOS. Découvrez le [guide de démarrage rapide sur Objective-C pour iOS](quickstart-objectivec-ios.md).
* Prise en charge de JavaScript dans le navigateur. Découvrez le [guide de démarrage rapide JavaScript](quickstart-js-browser.md).

**Dernières modifications**

* Cette version contient plusieurs changements cassants.
  Pour plus d’informations, consultez [cette page](https://aka.ms/csspeech/breakingchanges_1_0_0).

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Kit de développement logiciel (SDK) Speech de Cognitive Services version 0.6.0 : août 2018

**Nouvelles fonctionnalités**

* Les applications UWP créées à partir du SDK Speech peuvent désormais passer le Kit de certification des applications Windows (WACK).
  Consultez le [guide de démarrage rapide UWP](quickstart-csharp-uwp.md).
* Prise en charge de .NET Standard 2.0 sur Linux (Ubuntu 16.04 x64).
* Expérimental : Prise en charge de Java 8 sur Windows (64 bits) et Linux (Ubuntu 16.04 x64).
  Consultez le [guide de démarrage rapide Java Runtime Environment](quickstart-java-jre.md).

**Changement fonctionnel**

* Exposition d’informations supplémentaires sur les erreurs de connexion

**Dernières modifications**

* Sur Java (Android), la fonction `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` ne requiert plus aucun paramètre de chemin d’accès. Le chemin est désormais automatiquement détecté sur toutes les plateformes prises en charge.
* L’élément get-accessor de la propriété `EndpointUrl` dans Java et C# a été supprimé.

**Résolution des bogues**

* Dans Java, le résultat de la synthèse audio sur le module de reconnaissance de traduction est maintenant implémenté.
* Correction d’un bogue qui pouvait provoquer l’inactivité des threads et un plus grand nombre de sockets ouverts et inutilisés
* Correction d’un problème qui provoquait l’arrêt d’une reconnaissance de longue durée au milieu d’une transmission
* Correction d’une condition de concurrence lors de l’arrêt du module de reconnaissance.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Kit de développement logiciel (SDK) Speech de Cognitive Services version 0.5.0 : juillet 2018

**Nouvelles fonctionnalités**

* Prise en charge de la plateforme Android (API 23 : Android Marshmallow 6.0 ou version ultérieure). Consultez le [Démarrage rapide Android](quickstart-java-android.md).
* Prise en charge de .NET Standard 2.0 sous Windows. Consultez le [Démarrage rapide .NET Core](quickstart-csharp-dotnetcore-windows.md).
* Expérimental : Prise en charge d’UWP sur Windows (version 1709 ou ultérieure)
  * Consultez le [guide de démarrage rapide UWP](quickstart-csharp-uwp.md).
  * Remarque : Les applications UWP générées avec le Kit de développement logiciel (SDK) Speech ne passent pas encore le Kit de certification des applications Windows (WACK).
* Prise en charge des reconnaissances de longue durée avec la reconnexion automatique

**Modifications fonctionnelles**

* `StartContinuousRecognitionAsync()` prend en charge les reconnaissances de longue durée.
* Le résultat des reconnaissances contient davantage de champs. Ils sont décalés par rapport au début de l’audio et de la durée (tous les deux en cycles) du texte reconnu et des valeurs supplémentaires représentant l’état de la reconnaissance, par exemple, `InitialSilenceTimeout` et `InitialBabbleTimeout`.
* Prise en charge d’AuthorizationToken pour la création d’instances Data Factory.

**Dernières modifications**

* Événements de reconnaissance : le type d’événement NoMatch a été fusionné avec l’événement Error.
* Le SpeechOutputFormat du langage C# a été renommé OutputFormat pour s’aligner sur le C++.
* Le type de retour de certaines méthodes de l’interface `AudioInputStream` a été légèrement modifié :
   * Dans Java, la méthode `read` retourne désormais `long` au lieu de `int`.
   * Dans C#, la méthode `Read` retourne désormais `uint` au lieu de `int`.
   * Dans C++, les méthodes `Read` et `GetFormat` retournent désormais `size_t` au lieu de `int`.
* C++ : les instances de flux d’entrée audio peuvent maintenant être passées comme `shared_ptr`.

**Résolution des bogues**

* Correction des valeurs de retour incorrectes dans les résultats lorsque `RecognizeAsync()` expire.
* La dépendance aux bibliothèques Media Foundation Windows a été supprimée. Le SDK utilise désormais les API Core Audio.
* Correction de la documentation : ajout de la page [Régions](regions.md) pour répertorier les régions prises en charge.

**Problème connu**

* Le SDK Speech pour Android ne signale pas les résultats de la synthèse vocale pour la traduction. Ce problème sera corrigé dans la prochaine version.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Kit de développement logiciel (SDK) Speech de Cognitive Services version 0.4.0 : juin 2018

**Modifications fonctionnelles**

- AudioInputStream

  Un module de reconnaissance peut désormais consommer un flux en tant que source audio. Pour plus d’informations, consultez ce [guide pratique](how-to-use-audio-input-streams.md).

- Format de sortie détaillé

  Lorsque vous créez un `SpeechRecognizer`, vous pouvez demander le format de sortie `Detailed` ou `Simple`. Le `DetailedSpeechRecognitionResult` contient un score de confiance, le texte reconnu, la forme lexicale brute, la forme normalisée et la forme normalisée avec les blasphèmes masqués.

**Modification critique**

- `SpeechRecognitionResult.Text` a été remplacé par `SpeechRecognitionResult.RecognizedText` pour le langage C#.

**Résolution des bogues**

- Correction d’un problème de rappel possible dans la couche USP qui se produisait lors de l’arrêt.

- Si un module de reconnaissance a utilisé un fichier d’entrée audio, il a été placé sur le descripteur de fichier plus longtemps que nécessaire.

- Suppression de plusieurs blocages entre la pompe de messages et le module de reconnaissance.

- Expiration du délai de déclenchement d’un résultat `NoMatch` lors de la réponse du service.

- Les bibliothèques Media Foundation Windows sont chargées en différé. Cette bibliothèque est nécessaire uniquement pour l’entrée du microphone.

- La vitesse de chargement de données audio est limitée à environ deux fois la vitesse audio d’origine.

- Désormais, les noms d’assemblys C# .NET dans Windows sont forts.

- Correction de la documentation : `Region` est obligatoire pour créer un module de reconnaissance.

D’autres exemples ont été ajoutés et sont constamment mis à jour. Pour obtenir la dernière série d’exemples, accédez au [dépôt GitHub d’exemples pour le SDK Speech](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Kit de développement logiciel (SDK) Speech de Cognitive Services version 0.2.12733 : mai 2018

Cette version est la première préversion publique du SDK Speech de Cognitive Services.
