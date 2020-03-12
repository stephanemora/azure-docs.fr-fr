---
title: 'Démarrage rapide : Bibliothèque de client Visage pour .NET'
description: Découvrez la bibliothèque cliente Visage pour .NET avec ce guide de démarrage rapide.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: e51937dfe2afa0e92ce98b4c305555b53896e5f1
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78401752"
---
# <a name="quickstart-face-client-library-for-net"></a>Démarrage rapide : Bibliothèque de client Visage pour .NET

Découvrez la bibliothèque de client Visage pour .NET. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base. Le service Visage vous donne accès à des algorithmes avancés pour la détection et la reconnaissance des visages dans des images.

Utilisez la bibliothèque de client Visage pour .NET afin d’effectuer les opérations suivantes :

* [Détecter des visages sur une image](#detect-faces-in-an-image)
* [Rechercher des visages semblables](#find-similar-faces)
* [Créer et entraîner un groupe de personnes](#create-and-train-a-person-group)
* [Identifier un visage](#identify-a-face)
* [Prendre une capture instantanée pour la migration de données](#take-a-snapshot-for-data-migration)

[Documentation de référence](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.5.0-preview.1) | [Exemples](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* Version actuelle de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="setting-up"></a>Configuration

### <a name="create-a-face-azure-resource"></a>Créer une ressource Visage Azure

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour Visage en utilisant le [portail Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) sur votre ordinateur local. Vous pouvez également :

* Obtenir une [clé](https://azure.microsoft.com/try/cognitive-services/#decision) pour un essai gratuit valide pendant 7 jours. Une fois l’inscription terminée, elle est disponible sur le [site web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Afficher cette ressource sur le [portail Azure](https://portal.azure.com/).

Après avoir obtenu une clé à partir de votre abonnement ou votre ressource d’essai, [créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la clé et l’URL de point de terminaison, nommées respectivement `FACE_SUBSCRIPTION_KEY` et `FACE_ENDPOINT`.

### <a name="create-a-new-c-application"></a>Créer une application C#

Créez une application .NET Core dans votre éditeur ou IDE favori. 

Dans une fenêtre de console (par exemple cmd, PowerShell ou Bash), utilisez la commande `dotnet new` pour créer une application console avec le nom `face-quickstart`. Cette commande crée un projet C# « Hello World » simple avec un seul fichier source : *Program.cs*. 

```dotnetcli
dotnet new console -n face-quickstart
```

Déplacez vos répertoires vers le dossier d’application nouvellement créé. Vous pouvez générer l’application avec :

```dotnetcli
dotnet build
```

La sortie de génération ne doit contenir aucun avertissement ni erreur. 

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

À partir du répertoire de projet, ouvrez le fichier *Program.cs* dans votre éditeur ou votre IDE favori. Ajoutez les directives `using` suivantes :

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_using)]

Dans la méthode `Main` de l’application, créez des variables pour le point de terminaison et la clé Azure de votre ressource.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_mainvars)]

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Dans le répertoire de l’application, installez la bibliothèque de client Visage pour .NET avec la commande suivante :

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.5.0-preview.1
```

Si vous utilisez l’IDE Visual Studio, la bibliothèque de client est disponible sous forme de package NuGet téléchargeable.

## <a name="object-model"></a>Modèle objet

Les classes et interfaces suivantes gèrent certaines des principales fonctionnalités du SDK .NET Visage :

|Nom|Description|
|---|---|
|[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) | Cette classe représente votre autorisation d’utiliser le service Visage. Vous en avez besoin pour toutes les fonctionnalités de Visage. Vous pouvez l’instancier avec vos informations d’abonnement et l’utiliser pour produire des instances d’autres classes. |
|[FaceOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations?view=azure-dotnet)|Cette classe gère les tâches de détection et de reconnaissance de base que vous pouvez effectuer avec les visages. |
|[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)|Cette classe représente toutes les données détectées à partir d’un visage unique dans une image. Vous pouvez l’utiliser pour récupérer des informations détaillées sur le visage.|
|[FaceListOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations?view=azure-dotnet)|Cette classe gère les constructions **FaceList** stockées dans le cloud, comprenant un ensemble de visages assortis. |
|[PersonGroupPersonExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions?view=azure-dotnet)| Cette classe gère les constructions **Person** stockées dans le cloud, comprenant un ensemble de visages appartenant à une même personne.|
|[PersonGroupOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations?view=azure-dotnet)| Cette classe gère les constructions **PersonGroup** stockées dans le cloud, comprenant un ensemble d’objets **Person** assortis. |
|[ShapshotOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations?view=azure-dotnet)|Cette classe gère la fonctionnalité de capture instantanée. Vous pouvez l’utiliser pour enregistrer temporairement toutes vos données de visage basées sur le cloud et migrer ces données vers un nouvel abonnement Azure. |

## <a name="code-examples"></a>Exemples de code

Les extraits de code ci-dessous vous montrent comment effectuer les tâches suivantes avec la bibliothèque de client Visage pour .NET :

* [Authentifier le client](#authenticate-the-client)
* [Détecter des visages sur une image](#detect-faces-in-an-image)
* [Rechercher des visages semblables](#find-similar-faces)
* [Créer et entraîner un groupe de personnes](#create-and-train-a-person-group)
* [Identifier un visage](#identify-a-face)
* [Prendre une capture instantanée pour la migration de données](#take-a-snapshot-for-data-migration)


## <a name="authenticate-the-client"></a>Authentifier le client

> [!NOTE]
> Ce guide de démarrage suppose que vous avez [créé des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour votre clé et votre point de terminaison Visage, nommées `FACE_SUBSCRIPTION_KEY` et `FACE_ENDPOINT`.

Dans une nouvelle méthode, instanciez un client avec votre point de terminaison et la clé. Créez un objet **[ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials?view=azure-dotnet)** avec votre clé et utilisez-le avec votre point de terminaison pour créer un objet **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** .

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_auth)]

Vous voudrez probablement appeler cette méthode dans la méthode `Main`.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_client)]

## <a name="detect-faces-in-an-image"></a>Détecter des visages dans une image

À la racine de votre classe, définissez la chaîne d’URL suivante. Cette URL pointe vers un ensemble d’exemples d’images.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

Si vous le souhaitez, vous pouvez choisir le modèle IA à utiliser pour extraire des données du ou des visages détectés. Pour plus d’informations sur ces options, consultez [Spécifier un modèle de reconnaissance](../Face-API-How-to-Topics/specify-recognition-model.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

L’opération de détection finale prendra un objet **[FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet)** , une URL d’image et un modèle de reconnaissance.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_call)]

### <a name="get-detected-face-objects"></a>Recevoir les objets de visage détectés

Dans le prochain bloc de code, la méthode `DetectFaceExtract` détecte les visages dans trois des images à l’URL donnée, puis crée une liste d’objets **[DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet)** dans la mémoire du programme. La liste des valeurs **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** spécifie les fonctionnalités à extraire. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Afficher les données de visage détectées

Le reste de la méthode `DetectFaceExtract` analyse et imprime les données d’attribut pour chaque visage détecté. Chaque attribut doit être spécifié séparément dans l’appel initial de l’API de détection des visages (dans la liste **[FaceAttributeType](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet)** ). Le code suivant traite chaque attribut, mais vous n’aurez probablement besoin d’en utiliser qu’un ou plusieurs.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Rechercher des visages semblables

Le code suivant recherche un ensemble de visages (la cible) correspondant à un unique visage détecté (la source). Quand il trouve une correspondance, il affiche l’ID du visage correspondant sur la console.

### <a name="detect-faces-for-comparison"></a>Détecter des visages pour les comparer

Tout d’abord, définissez une deuxième méthode de détection de visage. Vous devez détecter des visages dans des images avant de pouvoir les comparer. Cette méthode de détection est optimisée pour les opérations de comparaison. Elle n’extrait pas les attributs de visage détaillés comme dans la section ci-dessus, et utilise un modèle de reconnaissance différent.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Rechercher des correspondances

La méthode suivante détecte des visages dans un ensemble d’images cibles et dans une image source unique. Elle les compare ensuite et recherche toutes les images cibles semblables à l’image source.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Afficher les correspondances

Le code suivant affiche les détails relatifs aux correspondances sur la console :

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_find_similar_print)]

## <a name="create-and-train-a-person-group"></a>Créer et entraîner un groupe de personnes

Le code suivant crée un **PersonGroup** avec six objets **Person** différents. Il associe chaque objet **Person** à un ensemble d’exemples d’images, puis s’entraîne à reconnaître chaque personne par leurs caractéristiques faciales. Les objets **Person** et **PersonGroup** sont utilisés dans les opérations de vérification, d’identification et de groupe.

Si vous ne l’avez pas déjà fait, définissez la chaîne d’URL suivante à la racine de votre classe. L’emplacement pointé est un ensemble d’exemples d’images.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_image_url)]

Le code présenté plus loin dans cette section spécifie un modèle de reconnaissance permettant d’extraire des données à partir de visages, et l’extrait de code suivant crée des références aux modèles disponibles. Pour plus d’informations sur les modèles de reconnaissance, consultez [Spécifier un modèle de reconnaissance](../Face-API-How-to-Topics/specify-recognition-model.md).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_detect_models)]

### <a name="create-persongroup"></a>Créer un PersonGroup

Déclarez une variable de chaîne à la racine de votre classe pour représenter l’ID du **PersonGroup** que vous allez créer.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_declare)]

Dans une nouvelle méthode, ajoutez le code suivant. Ce code associe les noms des personnes à leurs exemples d’images.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_files)]

Ajoutez ensuite le code suivant pour créer un objet **Person** pour chaque personne du dictionnaire et ajouter les données de visage à partir des images appropriées. Chaque objet **Person** est associé au même **PersonGroup** par le biais de sa chaîne d’ID unique. N’oubliez pas de passer les variables `client`, `url` et `RECOGNITION_MODEL1` dans cette méthode.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_create)]

### <a name="train-persongroup"></a>Entraîner le PersonGroup

Une fois que vous avez extrait les données de visage de vos images et que vous les avez triées dans des objets**Person** distincts, vous devez entraîner le **PersonGroup** à identifier les caractéristiques visuelles associées à chacun de ses objets **Person**. Le code suivant appelle la méthode **train** asynchrone et interroge les résultats, en affichant l’état sur la console.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_train)]

Ce groupe **Person** et ses objets **Person** associés sont maintenant prêts à être utilisés dans les opérations de vérification, d’identification ou de groupe.

## <a name="identify-a-face"></a>Identifier un visage

L’opération d’identification prend une image d’une personne (ou de plusieurs personnes) et recherche l’identité de chaque visage dans l’image. Il compare chaque visage détecté à un **PersonGroup**, une base de données comprenant différents objets **Person** dont les caractéristiques du visage sont connues.

> [!IMPORTANT]
> Pour exécuter cet exemple, vous devez d’abord exécuter le code fourni à la section [Créer et entraîner un groupe de personnes](#create-and-train-a-person-group). Les variables utilisées dans cette section (`client`, `url` et `RECOGNITION_MODEL1`) doivent également être disponibles ici.

### <a name="get-a-test-image"></a>Obtenir une image de test

Notez que le code correspondant à [Créer et entraîner un groupe de personnes](#create-and-train-a-person-group) définit une variable `sourceImageFileName`. Cette variable correspond à l’image source (l’image qui contient les personnes à identifier).

### <a name="identify-faces"></a>Identifier des visages

Le code suivant prend l’image source et crée une liste de tous les visages détectés qu’elle contient. Il s’agit des visages qui seront identifiés par rapport au **PersonGroup**.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify_sources)]

L’extrait de code suivant appelle l’opération d’identification et affiche les résultats sur la console. Ici, le service tente de faire correspondre chaque visage de l’image source à un **Person** dans le **PersonGroup** donné.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Prendre une capture instantanée pour la migration de données

La fonctionnalité de capture instantanée vous permet de déplacer vos données de visage enregistrées, comme un **PersonGroup** entraîné, vers un autre abonnement Visage Azure Cognitive Services. Vous pouvez utiliser cette fonctionnalité si, par exemple, vous avez créé un objet **PersonGroup** à l’aide d’un abonnement d’essai gratuit et que vous voulez le migrer cet objet vers un abonnement payant. Pour obtenir une vue d’ensemble de la fonctionnalité de capture instantanée, consultez [Migrer vos données de visage](../Face-API-How-to-Topics/how-to-migrate-face-data.md).

Dans cet exemple, vous allez migrer le **PersonGroup** que vous avez créé à l’étape [Créer et entraîner un groupe de personnes](#create-and-train-a-person-group). Vous pouvez d’abord terminer cette section ou créer vos propres constructions de données Visage à migrer.

### <a name="set-up-target-subscription"></a>Configurer l’abonnement cible

Tout d’abord, vous devez disposer d’un deuxième abonnement Azure avec une ressource Visage. Pour ce faire, procédez comme indiqué dans la section [Configuration](#setting-up). 

Définissez ensuite les variables suivantes dans la méthode `Main` de votre programme. Vous devez créer des variables d’environnement pour l’ID d’abonnement de votre compte Azure ainsi que la clé, le point de terminaison et l’ID d’abonnement de votre nouveau compte (cible). 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

Pour cet exemple, déclarez une variable pour l’ID du **PersonGroup** cible (l’objet qui appartient au nouvel abonnement et dans lequel vous allez copier vos données).

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_vars)]

### <a name="authenticate-target-client"></a>Authentifier le client cible

Ajoutez ensuite le code pour authentifier votre abonnement Visage secondaire.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_client)]

### <a name="use-a-snapshot"></a>Utiliser une capture instantanée

Le reste des opérations de capture instantanée doivent s’effectuer dans une méthode asynchrone. 

1. La première étape consiste à **prendre** la capture instantanée, ce qui enregistre les données de Face de votre abonnement d’origine à un emplacement temporaire dans le cloud. Cette méthode retourne un ID que vous utilisez pour interroger l’état de l’opération.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take)]

1. Interrogez ensuite l’ID jusqu’à ce que l’opération soit terminée.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_take_wait)]

1. Utilisez ensuite l’opération d’**application** pour écrire vos données de visage dans votre abonnement cible. Cette méthode retourne également une valeur d’ID.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Interrogez à nouveau le nouvel ID jusqu’à ce que l’opération soit terminée.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_apply)]

1. Enfin, complétez le bloc try/catch et terminez la méthode.

    [!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_snapshot_trycatch)]

À ce stade, votre nouvel objet **PersonGroup** doit avoir les mêmes données que celui d’origine et doit être accessible à partir de votre nouvel abonnement (cible) Visage Azure.

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application à partir de votre répertoire d’application avec la commande `dotnet run`.

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

Si vous avez créé un **PersonGroup** dans le cadre de ce guide de démarrage rapide et que vous voulez le supprimer, exécutez le code suivant dans votre programme :

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_persongroup_delete)]

Définissez la méthode de suppression avec le code suivant :

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_deletepersongroup)]

Si vous avez migré des données à l’aide de la fonctionnalité de capture instantanée dans le cadre de ce guide de démarrage rapide, vous devez également supprimer le **PersonGroup** enregistré dans l’abonnement cible.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/documentation-samples/quickstarts/Face/Program.cs?name=snippet_target_persongroup_delete)]

## <a name="next-steps"></a>Étapes suivantes

Dans le cadre de ce guide de démarrage rapide, vous avez appris à utiliser la bibliothèque Visage pour .NET afin d’effectuer des tâches de base. Pour plus d’informations sur la bibliothèque, reportez-vous à la documentation de référence.

> [!div class="nextstepaction"]
> [Informations de référence sur l’API Visage (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)

* [Qu’est ce que le service Visage ?](../overview.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/documentation-samples/quickstarts/Face/Program.cs).
