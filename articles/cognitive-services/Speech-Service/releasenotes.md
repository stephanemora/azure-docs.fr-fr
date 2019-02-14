---
title: Notes de publication - Services Speech
titlesuffix: Azure Cognitive Services
description: Consultez un journal constamment mis à jour des futures versions, des améliorations, des correctifs de bogues et des problèmes connus pour les services Azure Speech.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: a5b8cd7da465bc2dc58c2b89852903669c18bf4b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55871793"
---
# <a name="release-notes"></a>Notes de publication

## <a name="speech-sdk-120-2018-december-release"></a>SDK Speech 1.2.0 : Version de décembre 2018

**Nouvelles fonctionnalités**

* Python
  * La version bêta de la prise en charge de Python (3.5 et au-delà) est disponible avec cette version. Pour plus d’informations, [consultez cette page](quickstart-python.md).
* JavaScript
  * Le SDK Speech pour JavaScript est open source. Le code source est disponible sur [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js).
  * Nous prenons désormais en charge Node.js. Pour plus d’informations, [consultez cette page](quickstart-js-node.md).
  * La restriction sur la longueur des sessions audio a été supprimée. La reconnexion se produit automatiquement à l’arrière-plan.
* Objet Connection
  * Vous pouvez accéder à un objet Connection à partir du module de reconnaissance. Cet objet vous permet de lancer explicitement la connexion au service et de vous abonner à des événements de connexion et de déconnexion.
    (JavaScript et Python ne proposent pas encore cette fonctionnalité.)
* Prise en charge d’Ubuntu 18.04.
* Android
  * Prise en charge de ProGuard activée durant la génération d’APK.

**Améliorations**

* Améliorations apportées à l’utilisation des threads internes afin de réduire le nombre de threads, de verrous et de mutex.
* Amélioration des rapports d’erreurs et des informations sur les erreurs. Dans plusieurs cas, des messages d’erreur n’ont pas été entièrement propagés.
* Mise à jour des dépendances de développement dans JavaScript pour utiliser des modules à jour.

**Résolution des bogues**

* Résolution des fuites de mémoire causés par une incompatibilité de type dans RecognizeAsync.
* Dans certains cas, des exceptions étaient divulguées.
* Résolution des fuites de mémoire dans les arguments d’événement de traduction.
* Résolution d’un problème de verrouillage à la suite d’une reconnexion dans les sessions de longue durée.
* Résolution d’un problème pouvant entraîner l’absence d’un résultat final en cas d’échec de la traduction.
* C# : Si une opération asynchrone n’était pas attendue dans le thread principal, le module de reconnaissance pouvait être supprimé avant la fin de la tâche asynchrone.
* Java : Résolution d’un problème entraînant un blocage de la machine virtuelle Java.
* Objective-C : Résolution d’un mappage d’enum ; RecognizedIntent était retourné à la place de RecognizingIntent.
* JavaScript : Définition du format de sortie par défaut « simple » dans SpeechConfig.
* JavaScript : Suppression d’une incohérence au niveau des propriétés sur l’objet config entre JavaScript et d’autres langages.

**Exemples**

* Mise à jour et résolution de plusieurs exemples (par exemple, voix de sortie pour la traduction, etc.).
* Ajout d’exemples Node.js dans le [dépôt d’exemples](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-110"></a>SDK Speech 1.1.0

**Nouvelles fonctionnalités**

* Prise en charge d'Android x86/x64.
* Prise en charge de proxy : dans l’objet SpeechConfig, vous pouvez maintenant appeler une fonction pour définir les informations de proxy (nom d’hôte, port, nom d’utilisateur et mot de passe). Cette fonctionnalité n'est pas encore disponible sous iOS.
* Amélioration du code d’erreur et des messages. Si une reconnaissance a renvoyé une erreur, celle-ci a déjà défini `Reason` (dans l’événement annulé) ou `CancellationDetails` (dans le résultat de la reconnaissance) sur `Error`. L’événement annulé contient maintenant deux membres supplémentaires, `ErrorCode` et `ErrorDetails`. Si le serveur a renvoyé des informations d’erreur supplémentaires avec l’erreur signalée, elles sont désormais disponibles dans les nouveaux membres.

**Améliorations**

* Ajout d'une vérification supplémentaire dans la configuration du module de reconnaissance, et ajout d'un message d’erreur supplémentaire.
* Amélioration de la gestion des longs silences au milieu d’un fichier audio.
* Package NuGet : pour les projets .NET Framework, il empêche toute génération avec une configuration AnyCPU.

**Résolution des bogues**

* Correction de plusieurs exceptions détectées dans les modules de reconnaissance. En outre, les exceptions sont interceptées et converties en événement annulé.
* Correction d'une fuite de mémoire dans la gestion des propriétés.
* Correction d’un bogue dans lequel un fichier d’entrée audio pouvait bloquer le module de reconnaissance.
* Correction d’un bogue dans lequel des événements pouvaient être reçus après un événement d’arrêt de session.
* Correction de certaines conditions de concurrence dans le thread.
* Correction d’un problème de compatibilité avec iOS qui pouvait entraîner un blocage.
* Améliorations de la stabilité dans la prise en charge du microphone Android.
* Correction d’un bogue dans lequel un module de reconnaissance de JavaScript ignorait la langue de reconnaissance.
* Correction d’un bogue qui empêchait de définir EndpointId (dans certains cas) dans JavaScript.
* Modification de l'ordre des paramètres dans AddIntent dans JavaScript, et ajout de la signature AddIntent JavaScript manquante.

**Exemples**

* Ajout d’un exemple C++ et C# pour la diffusion en continu dans l’[exemple de référentiel](https://aka.ms/csspeech/samples).

## <a name="speech-sdk-101"></a>SDK Speech 1.0.1

Améliorations de la fiabilité et résolution des bogues :

* Correction d’une erreur irrécupérable potentielle due à une condition de concurrence lors de la suppression du module de reconnaissance
* Correction d’une erreur irrécupérable potentielle en cas de propriétés non définies.
* Vérification supplémentaire des erreurs et des paramètres.
* Objective-C : correction d’une erreur irrécupérable possible provoquée par le remplacement d’un nom dans une chaîne NSString.
* Objective-C : réglage de la visibilité de l’API.
* JavaScript : correction des événements et de leurs charges utiles.
* Améliorations de la documentation.

Dans notre [exemple de référentiel](https://aka.ms/csspeech/samples), un nouvel échantillon pour JavaScript a été ajouté.

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>SDK Cognitive Services Speech 1.0.0 : version de septembre 2018

**Nouvelles fonctionnalités**

* Prise en charge d’Objective-C sur iOS. Découvrez le [guide de démarrage rapide sur Objective-C pour iOS](quickstart-objectivec-ios.md).
* Prise en charge de JavaScript dans le navigateur. Découvrez le [guide de démarrage rapide JavaScript](quickstart-js-browser.md).

**Dernières modifications**

* Cette version contient plusieurs changements cassants.
  Pour plus d’informations, consultez [cette page](https://aka.ms/csspeech/breakingchanges_1_0_0).

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0 : version d’août 2018

**Nouvelles fonctionnalités**

* Les applications UWP créées à partir du SDK Speech peuvent désormais passer le Kit de certification des applications Windows (WACK).
  Consultez le [guide de démarrage rapide UWP](quickstart-csharp-uwp.md).
* Prise en charge de .NET Standard 2.0 sur Linux (Ubuntu 16.04 x64).
* Expérimental : prise en charge de Java 8 sur Windows (64 bits) et Linux (Ubuntu 16.04 x64).
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

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>SDK Cognitive Services Speech 0.5.0 : version de juillet 2018

**Nouvelles fonctionnalités**

* Prise en charge de la plateforme Android (API 23 : Android 6.0 Marshmallow ou supérieur). Consultez le [Démarrage rapide Android](quickstart-java-android.md).
* Prise en charge de .NET Standard 2.0 sous Windows. Consultez le [Démarrage rapide .NET Core](quickstart-csharp-dotnetcore-windows.md).
* Expérimental : Prise en charge d’UWP sur Windows (version 1709 ou ultérieure).
  * Consultez le [guide de démarrage rapide UWP](quickstart-csharp-uwp.md).
  * Remarque : les applications UWP générées avec le kit SDK Speech ne réussissent pas encore le test du Kit de certification des applications Windows (WACK).
* Prise en charge des reconnaissances de longue durée avec la reconnexion automatique

**Modifications fonctionnelles**

* `StartContinuousRecognitionAsync()` prend en charge les reconnaissances de longue durée.
* Le résultat des reconnaissances contient davantage de champs. Ils sont décalés par rapport au début de l’audio et de la durée (tous les deux en cycles) du texte reconnu et des valeurs supplémentaires représentant l’état de la reconnaissance, par exemple, `InitialSilenceTimeout` et `InitialBabbleTimeout`.
* Prise en charge d’AuthorizationToken pour la création d’instances Data Factory.

**Dernières modifications**

* Événements de reconnaissance : le type d’événement NoMatch a été fusionné avec l’événement Error.
* Le SpeechOutputFormat du langage C# a été renommé OutputFormat pour s’aligner sur le C++.
* Le type de retour de certaines méthodes de l’interface `AudioInputStream` a été légèrement modifié :
   * Dans Java, la méthode `read` retourne désormais `long` au lieu de `int`.
   * Dans C#, la méthode `Read` retourne désormais `uint` au lieu de `int`.
   * Dans C++, les méthodes `Read` et `GetFormat` retournent désormais `size_t` au lieu de `int`.
* C++ : les instances de flux d’entrée audio peuvent maintenant être passées comme `shared_ptr`.

**Résolution des bogues**

* Correction des valeurs de retour incorrectes dans les résultats lorsque `RecognizeAsync()` expire.
* La dépendance aux bibliothèques Media Foundation Windows a été supprimée. Le SDK utilise désormais les API Core Audio.
* Correction de la documentation : ajout de la page [régions](regions.md) pour répertorier les régions prises en charge.

**Problème connu**

* Le SDK Speech pour Android ne signale pas les résultats de la synthèse vocale pour la traduction. Ce problème sera corrigé dans la prochaine version.

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>SDK Cognitive Services Speech 0.4.0 : version de juin 2018

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

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>SDK Cognitive Services Speech 0.2.12733 : version de mai 2018

Cette version est la première préversion publique du SDK Speech de Cognitive Services.
