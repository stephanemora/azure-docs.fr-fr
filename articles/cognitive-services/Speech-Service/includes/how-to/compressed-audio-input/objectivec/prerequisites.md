---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 7106e139108681e1908b20d2daac5e619a63555d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422352"
---
La gestion d'un contenu audio compressé est implémentée à l’aide de [GStreamer](https://gstreamer.freedesktop.org). Pour une raison liée à la gestion des licences, les fichiers binaires GStreamer ne sont pas compilés et liés avec le Kit de développement logiciel (SDK) Speech. Au lieu de cela, une bibliothèque de wrappers contenant ces fonctions doit être créée et livrée avec les applications à l’aide du Kit de développement logiciel (SDK).

Pour créer cette bibliothèque wrapper, commencez par télécharger et installer le [kit de développement logiciel (SDK) GStreamer](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Ensuite, téléchargez le projet **Xcode** pour la [bibliothèque de wrappers](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Ouvrez le projet dans **Xcode**, puis créez-le pour la cible **Appareil iOS générique**. Sa génération pour une cible spécifique ne fonctionnera *pas*.

L’étape de génération crée un bundle de framework doté d'une bibliothèque dynamique pour toutes les architectures requises portant le nom `GStreamerWrapper.framework`.

Ce framework doit être inclus dans toutes les applications qui utilisent des flux audio compressés avec le SDK du service Speech.

Pour ce faire, appliquez les paramètres suivants à votre projet **Xcode** :

1. Dans le répertoire contenant votre exemple de projet, copiez le `GStreamerWrapper.framework` que vous venez de créer, ainsi que le framework du Kit de développement logiciel (SDK) Speech de Cognitive Services, que vous pouvez télécharger à partir d’[ici](https://aka.ms/csspeech/iosbinary).
1. Ajustez les chemins des frameworks dans les *paramètres du projet*.
   1. Sous l’onglet **Général** sous l’en-tête **Embedded Binaries** (Binaires incorporés), ajoutez la bibliothèque du SDK comme framework : **Ajouter des binaires incorporées** > **Ajouter d'autres...** > Accédez au répertoire que vous avez choisi et sélectionnez les deux frameworks.
   1. Accédez à l’onglet **Build Settings** (paramètres de Build) et activez tous les paramètres (**All**).
1. Ajoutez le répertoire `$(SRCROOT)/..` aux chemins de recherche de framework (_Framework Search Paths_) sous l’en-tête **Search Paths** (chemins de recherche).
