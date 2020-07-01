---
title: 'Démarrage rapide : Bibliothèque de client Vision par ordinateur pour Java'
description: Dans ce guide de démarrage rapide, commencez à utiliser la bibliothèque cliente Vision par ordinateur pour Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/19/2019
ms.author: pafarley
ms.openlocfilehash: 3d52e83aa574dcd506270dc8e2f244a9f96add53
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "85073241"
---
<a name="HOLTop"></a>

[Documentation de référence](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [Artifact (Maven)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [Exemples](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/cognitive-services/)
* La version actuelle du [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* L’[outil de génération Gradle](https://gradle.org/install/) ou un autre gestionnaire de dépendances.
* Une fois que vous avez votre abonnement Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="créez une ressource Vision par ordinateur"  target="_blank">créer une ressource Vision par ordinateur <span class="docon docon-navigate-external x-hidden-focus"></span></a> dans le portail Azure pour obtenir votre clé et votre point de terminaison. Une fois le déploiement effectué, cliquez sur **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous créez pour connecter votre application au service Vision par ordinateur. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide.
    * Vous pouvez utiliser le niveau tarifaire Gratuit (`F0`) pour tester le service, puis passer par la suite à un niveau payant pour la production.
* [Créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la clé et l’URL du point de terminaison, nommées respectivement `COMPUTER_VISION_SUBSCRIPTION_KEY` et `COMPUTER_VISION_ENDPOINT`.

## <a name="setting-up"></a>Configuration

### <a name="create-a-new-gradle-project"></a>Créer un projet Gradle

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y. 

```console
mkdir myapp && cd myapp
```

Exécutez la commande `gradle init` à partir de votre répertoire de travail. Cette commande crée des fichiers de build essentiels pour Gradle, notamment *build.gradle.kts*, qui est utilisé au moment de l’exécution pour créer et configurer votre application.

```console
gradle init --type basic
```

Quand vous êtes invité à choisir un **DSL**, sélectionnez **Kotlin**.

Recherchez *build.gradle.kts* et ouvrez-le avec votre IDE ou votre éditeur de texte habituel. Copiez-y ensuite la configuration de build suivante. Cette configuration définit le projet en tant qu’application Java dont le point d’entrée est la classe **ComputerVisionQuickstarts**. Elle importe la bibliothèque Vision par ordinateur.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
```

Dans votre répertoire de travail, exécutez la commande suivante pour créer un dossier de projet source :

```console
mkdir -p src/main/java
```

Accédez au nouveau dossier et créez le fichier *ComputerVisionQuickstarts.java*. Ouvrez-le dans votre éditeur ou IDE habituel et ajoutez les instructions `import` suivantes :

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports)]

Ajoutez ensuite une définition de classe pour **ComputerVisionQuickstarts**.

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Ce guide de démarrage rapide utilise le gestionnaire de dépendances Gradle. Vous trouverez la bibliothèque de client et des informations concernant d’autres gestionnaires de dépendances sur le [référentiel central Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

Dans le fichier *build.gradle.kts* de votre projet, ajoutez la bibliothèque de client Vision par ordinateur en tant que dépendance.

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>Modèle objet

Les classes et interfaces suivantes gèrent certaines des principales fonctionnalités du SDK Java Vision par ordinateur.

|Nom|Description|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | Cette classe est nécessaire pour toutes les fonctionnalités de Vision par ordinateur. Vous pouvez l’instancier avec vos informations d’abonnement et l’utiliser pour produire des instances d’autres classes.|
|[ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| Cette classe provient de l’objet client et gère directement toutes les opérations d’image, telles que l’analyse d’image, la détection de texte et la génération de miniatures.|
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| Cette énumération définit les différents types d’analyse d’image qui peuvent être effectués dans le cadre d’une opération d’analyse standard. Vous spécifiez un ensemble de valeurs VisualFeatureTypes en fonction de vos besoins. |

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque de client Vision par ordinateur pour Java :

* [Authentifier le client](#authenticate-the-client)
* [Analyser une image](#analyze-an-image)
* [Lire du texte imprimé et manuscrit](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Authentifier le client

> [!NOTE]
> Ce guide de démarrage rapide part du principe que vous avez [créé une variable d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour votre clé Vision par ordinateur, nommée `COMPUTER_VISION_SUBSCRIPTION_KEY`.

Le code suivant ajoute une méthode `main` à votre classe et crée des variables pour le point de terminaison et la clé Azure de votre ressource. Vous devez entrer votre propre chaîne de point de terminaison. Vous la trouverez dans la section **Vue d’ensemble** du portail Azure. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_mainvars)]

Ensuite, ajoutez le code suivant pour créer un objet [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) et le transmettre à d’autres méthodes, que vous définirez ultérieurement.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> Si vous avez créé la variable d’environnement après avoir lancé l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou l’interpréteur de commandes qui l’exécute pour y accéder.

## <a name="analyze-an-image"></a>Analyser une image

Le code suivant définit la méthode `AnalyzeLocalImage`, qui utilise l’objet client pour analyser une image locale et afficher les résultats. La méthode retourne une description textuelle, une catégorisation, une liste d’étiquettes, les visages détectés, les indicateurs de contenu pour adultes, les couleurs principales et le type d’image.

### <a name="set-up-test-image"></a>Configurer une image de test

Tout d’abord, créez un dossier **resources/** dans le dossier **src/main/** de votre projet, puis ajoutez une image que vous souhaitez analyser. Ajoutez ensuite la définition de méthode suivante à la classe **ComputerVisionQuickstarts**. Si nécessaire, changez la valeur de `pathToLocalImage` pour qu’elle corresponde à votre fichier image. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

> [!NOTE]
> Vous pouvez également analyser une image distante à l’aide de son URL. Consultez l’exemple de code sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) pour obtenir des scénarios impliquant des images distantes.

### <a name="specify-visual-features"></a>Spécifier les caractéristiques visuelles

Ensuite, spécifiez les caractéristiques visuelles que vous souhaitez extraire dans votre analyse. Pour obtenir une liste complète, consultez la section sur l’énumération [VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analyser
Cette méthode affiche les résultats détaillés sur la console pour chaque étendue de l’analyse d’image. Nous vous recommandons d’inclure cet appel de méthode dans un bloc try/catch. L’a méthode **analyzeImageInStream** retourne un objet **ImageAnalysis** qui contient toutes les informations extraites.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

Les sections suivantes montrent comment analyser ces informations en détail.

### <a name="get-image-description"></a>Obtenir une description d’image

Le code suivant obtient la liste des légendes générées pour l’image. Pour plus d’informations, consultez [Décrire les images](../../concept-describing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>Obtenir une catégorie d’image

Le code suivant obtient la catégorie détectée de l’image. Pour plus d’informations, consultez [Classer des images](../../concept-categorizing-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>Obtenir des étiquettes d’image

Le code suivant obtient l’ensemble d’étiquettes détectées dans l’image. Pour plus d’informations, consultez [Étiquettes de contenu](../../concept-tagging-images.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>Détecter des visages

Le code suivant retourne les visages détectés dans l’image avec les coordonnées de leur rectangle et sélectionne les attributs du visage. Pour plus d’informations, consultez [Détection des visages](../../concept-detecting-faces.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>Détecter des contenus pour adultes, choquants ou sordides

Le code suivant imprime la présence détectée de contenu pour adultes dans l’image. Pour plus d’informations, consultez [Contenu pour adultes choquant ou sordide](../../concept-detecting-adult-content.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>Obtenir le modèle de couleurs d’une image

Le code suivant imprime les attributs de couleur détectés dans l’image, comme les couleurs dominantes et la couleur d’accentuation. Pour plus d’informations, consultez [Jeux de couleurs](../../concept-detecting-color-schemes.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>Obtenir le contenu spécifique à un domaine

La Vision par ordinateur peut utiliser un modèle spécialisé pour effectuer une analyse approfondie des images. Pour plus d’informations, consultez [Contenu spécifique à un domaine](../../concept-detecting-domain-content.md). 

Le code suivant analyse des données relatives aux célébrités détectées dans l’image.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

Le code suivant analyse des données relatives aux monuments détectés dans l’image.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>Obtenir le type d’image

Le code suivant affiche des informations sur le type d’image, qu’il s’agisse d’une image clipart ou d’un dessin au trait.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

## <a name="read-printed-and-handwritten-text"></a>Lire du texte imprimé et manuscrit

Vision par ordinateur peut lire du texte visible dans une image et le convertir en flux de caractères.

> [!NOTE]
> Vous pouvez également lire du texte dans une image distante à l’aide de son URL. Consultez l’exemple de code sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) pour obtenir des scénarios impliquant des images distantes.

### <a name="call-the-recognize-api"></a>Appeler l’API de reconnaissance

Commencez par utiliser le code suivant afin d’appeler la méthode **recognizePrintedTextInStream** pour l’image donnée. Lorsque vous ajoutez ce code à votre projet, vous devez remplacer la valeur de `localTextImagePath` par le chemin à votre image locale. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

### <a name="print-recognize-results"></a>Imprimer les résultats de la reconnaissance

Le bloc de code suivant traite le texte retourné et l’analyse pour imprimer le premier mot de chaque ligne. Vous pouvez utiliser ce code pour comprendre rapidement la structure d’une instance **OcrResult**.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_print)]

Pour terminer, fermez le bloc try/catch et la définition de la méthode.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

## <a name="run-the-application"></a>Exécution de l'application

Vous pouvez générer l’application avec :

```console
gradle build
```

Exécutez l’application avec la commande `gradle run` :

```console
gradle run
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

Dans le cadre de ce guide de démarrage rapide, vous avez appris à utiliser la bibliothèque Java Vision par ordinateur pour effectuer des tâches de base. Pour plus d’informations sur la bibliothèque, reportez-vous à la documentation de référence.

> [!div class="nextstepaction"]
>[Informations de référence sur l’API Vision par ordinateur (Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [Qu’est-ce que le service Vision par ordinateur ?](../../Home.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).