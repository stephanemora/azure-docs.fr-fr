---
title: 'Démarrage rapide : Reconnaître la voix stockée dans le stockage Blob, C# – Service Speech'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: b8b650920c03b14c7d55aafd6ecdb43dafaafafe
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467311"
---
## <a name="prerequisites"></a>Conditions préalables requises

Avant de commencer, assurez-vous de :

> [!div class="checklist"]
> * [Créer une ressource Azure Speech](../../../../get-started.md)
> * [Charger un fichier source dans un objet blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [Configurer votre environnement de développement](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Créer un exemple de projet vide](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Ouvrez votre projet dans Visual Studio.

La première étape consiste à vérifier que votre projet est ouvert dans Visual Studio.

1. Lancez Visual Studio 2019.
2. Chargez votre projet et ouvrez `Program.cs`.

## <a name="add-a-reference-to-newtonsoftjson"></a>Ajouter une référence à NewtonSoftJSon

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **helloworld**, puis sélectionnez **Gérer les packages NuGet** pour afficher le gestionnaire de package NuGet.

1. Dans l’angle supérieur droit, recherchez la zone de liste déroulante **Source du package** et vérifiez que **`nuget.org`** est sélectionné.

1. Dans l’angle supérieur gauche, sélectionnez **Parcourir.**

1. Dans la zone de recherche, tapez *Newtonsoft.Json*, puis sélectionnez **Entrée**.

1. Dans les résultats de la recherche, sélectionnez le package **Newtonsoft.Json**, puis sélectionnez **Installer** pour installer la dernière version stable.

1. Acceptez tous les contrats et licences pour démarrer l’installation.

   Une fois le package installé, un message de confirmation s’affiche dans la fenêtre **Console du Gestionnaire de package**.

## <a name="start-with-some-boilerplate-code"></a>Commencer avec du code réutilisable

Nous allons ajouter du code qui servira de squelette à notre projet

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=6-43,138,277)]
(vous devrez remplacer les valeurs de `YourSubscriptionKey`, `YourServiceRegion` et `YourFileUrl` par vos propres valeurs).
## <a name="json-wrappers"></a>Wrappers JSON

Étant donné que l’API REST accepte les requêtes au format JSON et retourne également des résultats au format JSON, nous pourrions interagir avec elles en utilisant uniquement des chaînes, même si cela n’est pas recommandé.
Pour faciliter la gestion des requêtes et des réponses, nous allons déclarer quelques classes à utiliser pour sérialiser/désérialiser le JSON.

Poursuivez et placez leurs déclarations après `TranscribeAsync`.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=140-276)]

## <a name="create-and-configure-an-http-client"></a>Créer et configurer un client HTTP
La première chose dont nous avons besoin est d’un client HTTP disposant d’une URL de base correcte et pour lequel l’authentification a été définie.
Insérer ce code dans `TranscribeAsync` [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=46-50)]

## <a name="generate-a-transcription-request"></a>Générer une demande de transcription
Nous allons ensuite générer la demande de transcription. Ajouter ce code à `TranscribeAsync` [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=52-57)]

## <a name="send-the-request-and-check-its-status"></a>Envoyer la requête et vérifier son état
Nous allons maintenant envoyer la requête au service Speech et vérifier le code de réponse initial. Ce code de réponse indique simplement si le service a reçu la requête. Le service va retourner une URL dans les en-têtes de réponse qui correspond à l’emplacement où il va stocker l’état de la transcription.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=59-70)]

## <a name="wait-for-the-transcription-to-complete"></a>Attendez que la transcription se termine
Étant donné que le service traite la transcription de manière asynchrone, nous devons interroger l’état régulièrement. Nous allons le vérifier toutes les 5 secondes.

Nous pouvons vérifier l’état en récupérant le contenu à partir de l’URL que nous avons obtenue en envoyant la requête. Lorsque nous obtenons le contenu, nous le désérialisons dans l’une de nos classes d’assistance pour faciliter l’interaction avec celui-ci.

Voici le code d’interrogation pour tous les états sauf celui correspondant à une tâche terminée sans erreurs (nous l’aborderons plus tard).
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=72-106,121-137)]

## <a name="display-the-transcription-results"></a>Afficher les résultats de la transcription
Une fois que le service a terminé la transcription et que celle-ci s’est bien déroulée, les résultats sont stockés dans une autre URL que nous pouvons obtenir dans la réponse d’état.

Ici, nous faisons une demande pour télécharger les résultats dans un fichier temporaire avant de les lire et de les désérialiser.
Une fois que les résultats sont chargés, nous pouvons les imprimer sur la console.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=107-120)]

## <a name="check-your-code"></a>Vérifier votre code
À ce stade, votre code doit ressembler à ceci : (Nous avons ajouté des commentaires à cette version) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/from-blob/program.cs?range=6-277)]

## <a name="build-and-run-your-app"></a>Générer et exécuter votre application

Vous êtes maintenant prêt à créer votre application et à tester la reconnaissance vocale à l’aide du service de reconnaissance vocale.

1. **Compiler le code** : à partir de la barre de menus de Visual Studio, choisissez **Générer** > **Générer la solution**.
2. **Démarrer votre application** : dans la barre de menus, choisissez **Déboguer** > **Démarrer le débogage**, ou appuyez sur **F5**.
3. **Démarrer la reconnaissance** : vous êtes invité à prononcer une phrase. Celle-ci est envoyée au service de reconnaissance vocale, transcrite sous forme de texte, puis affichée sur la console.

## <a name="next-steps"></a>Étapes suivantes

[!INCLUDE [footer](./footer.md)]
