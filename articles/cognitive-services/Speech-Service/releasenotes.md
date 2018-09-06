---
title: Documentation du Kit de développement logiciel (SDK) Speech de Cognitive Services | Microsoft Docs
description: Notes de publication - ce qui a changé dans les versions les plus récentes
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 08/16/2018
ms.author: wolfma
ms.openlocfilehash: bbf3c5930de2ec6c709b6b527ae3eac107382420
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047797"
---
# <a name="release-notes"></a>Notes de publication

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Kit de développement logiciel (SDK) Speech de Cognitive Services version 0.6.0 : août 2018

**Nouvelles fonctionnalités**

* Les applications UWP générées avec le Kit de développement logiciel (SDK) Speech peuvent désormais passer le Kit de certification des applications Windows (WACK).
  Consultez notre [Démarrage rapide UWP](quickstart-csharp-uwp.md).
* Prise en charge de .NET Standard 2.0 sur Linux (Ubuntu 16.04 x64).
* Expérimental : Prise en charge de Java 8 sur Windows (64 bits) et Linux (Ubuntu 16.04 x64).
  Consultez le [démarrage rapide de l’environnement d’exécution Java](quickstart-java-jre.md)

**Modifications fonctionnelles**

* Exposition d’informations supplémentaires sur les erreurs de connexion.

**Dernières modifications**

* Sur Java (Android), la fonction `SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` ne requiert plus aucun paramètre de chemin d’accès. Le chemin d’accès est désormais automatiquement détecté sur toutes les plateformes prises en charge.
* L’élément get-accessor de la propriété `EndpointUrl` dans Java et C# a été supprimé.

**Résolution des bogues**

* Dans Java, le résultat de la synthèse audio sur le module de reconnaissance de traduction est maintenant implémenté.
* Correction d’un bogue, ce qui peut entraîner des threads inactifs et un plus grand nombre de sockets ouverts et inutilisés.
* Correction d’un problème, dans lequel une reconnaissance longue peut s’arrêter au milieu de la transmission.
* Correction d’une condition de concurrence lors de l’arrêt du module de reconnaissance.

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Kit de développement logiciel (SDK) Speech de Cognitive Services version 0.5.0 : juillet 2018

**Nouvelles fonctionnalités**

* Prise en charge de la plateforme Android (API 23 : Android Marshmallow 6.0 ou version ultérieure).
  Consultez le [Démarrage rapide Android](quickstart-java-android.md).
* Prise en charge de .NET Standard 2.0 sous Windows.
  Consultez le [Démarrage rapide .NET Core](quickstart-csharp-dotnetcore-windows.md).
* Expérimental : Prise en charge d’UWP sous Windows (version 1709 ou ultérieure)
  * Consultez notre [Démarrage rapide UWP](quickstart-csharp-uwp.md).
  * Remarque : Les applications UWP générées avec le Kit de développement logiciel (SDK) Speech ne passent pas encore le Kit de certification des applications Windows (WACK).
* Prise en charge de la reconnaissance à long terme avec reconnexion automatique.

**Modifications fonctionnelles**

* `StartContinuousRecognitionAsync()` prend en charge la reconnaissance à long terme
* Le résultat de la reconnaissance contient plus de champs : décalage entre le début de l’audio et la durée (tous les deux en graduations) du texte reconnu, valeurs supplémentaires représentant l’état de la reconnaissance, par exemple, `InitialSilenceTimeout`, `InitialBabbleTimeout`.
* Prise en charge d’AuthorizationToken pour la création d’instances Data Factory.

**Dernières modifications**

* Événements de reconnaissance : le type d’événement NoMatch est fusionné dans l’événement Error.
* SpeechOutputFormat dans C# est renommé OutputFormat pour rester aligné avec C++.
* Le type de retour de certaines méthodes de l’interface `AudioInputStream` a été légèrement modifié :
   * Dans Java, la méthode `read` retourne désormais `long` au lieu de `int`.
   * Dans C#, la méthode `Read` retourne désormais `uint` au lieu de `int`.
   * Dans C++, les méthodes `Read` et `GetFormat` retournent désormais `size_t` au lieu de `int`.
* C++ : les instances de flux d’entrée audio peuvent maintenant être passées comme `shared_ptr`.

**Résolution des bogues**

* Correction des valeurs de retour incorrectes dans le résultat lorsque `RecognizeAsync()` expire.
* La dépendance sur les bibliothèques media foundation sous Windows est supprimée. Le Kit de développement logiciel (SDK) utilise désormais les API Core Audio.
* Correction de la documentation : ajout d’une page [régions](regions.md) pour décrire les régions prises en charge.

**Problèmes connus**

* Le Kit de développement logiciel (SDK) Speech pour Android ne signale pas les résultats de la synthèse vocale pour la traduction.
  Une solution est prévue dans la prochaine version.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Kit de développement logiciel (SDK) Speech de Cognitive Services version 0.4.0 : juin 2018

**Modifications fonctionnelles**

- AudioInputStream

  Un module de reconnaissance peut désormais consommer un flux comme la source audio. Pour obtenir des informations détaillées, consultez le [guide pratique](how-to-use-audio-input-streams.md).

- Format de sortie détaillé

  Lors de la création d’un `SpeechRecognizer`, vous pouvez demander le format de sortie `Detailed` ou `Simple`. Le `DetailedSpeechRecognitionResult` contient un score de confiance, le texte reconnu, la forme lexicale brute, la forme normalisée et la forme normalisée avec les blasphèmes masqués.

**Modification critique**

- Remplacez `SpeechRecognitionResult.Text` par `SpeechRecognitionResult.RecognizedText` en C#.

**Résolution des bogues**

- Résolvez un problème de rappel possible dans la couche USP lors de l’arrêt.

- Si un module de reconnaissance a utilisé un fichier d’entrée audio, il a été placé sur le descripteur de fichier plus longtemps que nécessaire.

- Plusieurs blocages supprimés entre la pompe de messages et le module de reconnaissance.

- Expiration du délai de déclenchement d’un résultat `NoMatch` lors de la réponse du service.

- Les bibliothèques Media Foundation sur Windows sont chargées en différé. Cette bibliothèque n’est requise que pour l’entrée du microphone.

- La vitesse de chargement de données audio est limitée à environ deux fois la vitesse audio d’origine.

- Sous Windows, les noms d’assemblys C# .NET sont maintenant forts.

- Correction de la documentation : `Region` est obligatoire pour créer un module de reconnaissance.

D’autres exemples ont été ajoutés et sont constamment mis à jour. Pour obtenir la dernière série d’exemples, consultez le [dépôt GitHub d’exemples pour le Kit de développement logiciel (SDK) Speech](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Kit de développement logiciel (SDK) Speech de Cognitive Services version 0.2.12733 : mai 2018

La première préversion publique du Kit de développement logiciel (SDK) Speech de Cognitive Services.
