---
title: 'Démarrage rapide : Bibliothèque de client Vision par ordinateur pour Node.js'
description: Commencez à utiliser la bibliothèque cliente Vision par ordinateur pour Node.js avec ce guide de démarrage rapide
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 01/22/2020
ms.author: pafarley
ms.openlocfilehash: 530665168cc1ed21f1664e3012bbf37e52d96fbf
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716167"
---
# <a name="quickstart-computer-vision-client-library-for-nodejs"></a>Démarrage rapide : Bibliothèque de client Vision par ordinateur pour Node.js

Découvrez comment bien démarrer avec la bibliothèque de client Vision par ordinateur pour Node.js. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base.

Utilisez la bibliothèque de client Vision par ordinateur pour Node.js afin de :

* [Analyser une image](#analyze-an-image)
* [Lire du texte imprimé et manuscrit](#read-printed-and-handwritten-text)

[Documentation de référence](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-computervision) | [Package (npm)](https://www.npmjs.com/package/@azure/cognitiveservices-computervision) | [Exemples](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Conditions préalables requises

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* Version actuelle de [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>Configuration

### <a name="create-a-computer-vision-azure-resource"></a>Créer une ressource Azure Vision par ordinateur

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour Vision par ordinateur en utilisant le [portail Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) sur votre ordinateur local. Vous pouvez également :

* Obtenir une [clé](https://azure.microsoft.com/try/cognitive-services/#decision) pour un essai gratuit valide pendant 7 jours. Une fois l’inscription terminée, elle est disponible sur le [site web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).
* Afficher cette ressource sur le [portail Azure](https://portal.azure.com/).

Une fois que vous avez obtenu une clé à partir de votre abonnement ou ressource d’essai, [créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la clé et l’URL de point de terminaison, nommées respectivement `COMPUTER_VISION_SUBSCRIPTION_KEY` et `COMPUTER_VISION_ENDPOINT`.

### <a name="create-a-new-nodejs-application"></a>Création d’une application Node.js

Dans une fenêtre de console (telle que cmd, PowerShell ou bash), créez un répertoire pour votre application et accédez-y.

```console
mkdir myapp && cd myapp
```

Exécutez la commande `npm init` pour créer une application de nœud avec un fichier `package.json`.

```console
npm init
```

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Installez les packages NPM `ms-rest-azure` et `@azure/cognitiveservices-computervision` :

```console
npm install @azure/cognitiveservices-computervision
```

Le fichier `package.json` de votre application sera mis à jour avec les dépendances.

### <a name="prepare-the-nodejs-script"></a>Préparer le script Node.js

Créez un fichier, *index.js*, puis ouvrez-le dans un éditeur de texte. Ajoutez les instructions d’importation suivantes.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imports)]

Ensuite, définissez une fonction `computerVision` et déclarez une série asynchrone avec la fonction principale et la fonction de rappel. Vous allez ajouter votre code de démarrage rapide à la fonction principale, et appeler `computerVision` en bas du script.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_begin)]

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_functiondef_end)]

## <a name="object-model"></a>Modèle objet

Les classes et interfaces suivantes prennent en charge certaines des fonctionnalités principales du kit SDK Vision par ordinateur pour Node.js.

|Name|Description|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest) | Cette classe est nécessaire pour toutes les fonctionnalités de Vision par ordinateur. Vous l’instanciez avec vos informations d’abonnement, et vous l’utilisez pour effectuer la plupart des opérations relatives aux images.|
|[VisualFeatureTypes](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/visualfeaturetypes?view=azure-node-latest)| Cette énumération définit les différents types d’analyse d’image qui peuvent être effectués dans le cadre d’une opération d’analyse standard. Vous spécifiez un ensemble de valeurs **VisualFeatureTypes** en fonction de vos besoins. |

## <a name="code-examples"></a>Exemples de code

Ces extraits de code vous montrent comment effectuer les tâches suivantes avec la bibliothèque de client Vision par ordinateur pour Node.js :

* [Authentifier le client](#authenticate-the-client)
* [Analyser une image](#analyze-an-image)
* [Lire du texte imprimé et manuscrit](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Authentifier le client

Créez des variables pour le point de terminaison et la clé Azure de votre ressource. Si vous avez créé la variable d’environnement après avoir lancé l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou le shell qui l’exécute pour accéder à la variable.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_vars)]

Instanciez un client avec votre point de terminaison et la clé. Créez un objet [ApiKeyCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.apikeycredentials?view=azure-python) à l’aide de votre clé et point de terminaison, puis utilisez-le pour créer un objet [ComputerVisionClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/computervisionclient?view=azure-node-latest).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_client)]

## <a name="analyze-an-image"></a>Analyser une image

Le code de cette section analyse les images distantes pour extraire différentes fonctionnalités visuelles. Vous pouvez effectuer ces opérations dans le cadre de la méthode **analyzeImage** de l’objet client, ou vous pouvez les appeler à l’aide de méthodes individuelles. Consultez la [documentation de référence](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest) pour plus d’informations.

> [!NOTE]
> Vous pouvez également analyser une image locale. Consultez l’exemple de code sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) pour obtenir des scénarios impliquant des images locales.

### <a name="get-image-description"></a>Obtenir une description d’image

Le code suivant obtient la liste des légendes générées pour l’image. Pour plus d’informations, consultez [Décrire des images](../concept-describing-images.md).

Tout d’abord, définissez l’URL d’une image à analyser :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe_image)]

Ajoutez ensuite le code suivant pour récupérer la description de l’image et l’imprimer sur la console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_describe)]

### <a name="get-image-category"></a>Obtenir une catégorie d’image

Le code suivant obtient la catégorie détectée de l’image. Pour plus d’informations, consultez [Classer des images](../concept-categorizing-images.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories)]

Définissez la fonction d’assistance `formatCategories` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_categories_format)]

### <a name="get-image-tags"></a>Obtenir des étiquettes d’image

Le code suivant obtient l’ensemble d’étiquettes détectées dans l’image. Pour plus d’informations, consultez [Étiquettes de contenu](../concept-tagging-images.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tags)]

Définissez la fonction d’assistance `formatTags` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_tagsformat)]

### <a name="detect-objects"></a>Détecter des objets

Le code suivant détecte les objets courants présents dans l’image et les affiche sur la console. Pour plus d’informations, consultez [Détection d’objets](../concept-object-detection.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objects)]

Définissez la fonction d’assistance `formatRectObjects` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_objectformat)]

### <a name="detect-brands"></a>Détecter les marques

Le code suivant détecte les marques et logos d’entreprise dans l’image, et les affiche sur la console. Pour plus d’informations, consultez [Détection des marques](../concept-brand-detection.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_brands)]

### <a name="detect-faces"></a>Détecter des visages

Le code suivant retourne les visages détectés dans l’image avec les coordonnées de leur rectangle et sélectionne les attributs du visage. Pour plus d’informations, consultez [Détection des visages](../concept-detecting-faces.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_faces)]

Définissez la fonction d’assistance `formatRectFaces` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_formatfaces)]

### <a name="detect-adult-racy-or-gory-content"></a>Détecter des contenus pour adultes, choquants ou sordides

Le code suivant imprime la présence détectée de contenu pour adultes dans l’image. Pour plus d’informations, consultez [Contenu pour adultes choquant ou sordide](../concept-detecting-adult-content.md).

Définissez l’URL de l’image à utiliser :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult_image)]

Ajoutez ensuite le code suivant pour détecter le contenu pour adultes et imprimer les résultats sur la console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>Obtenir le modèle de couleurs d’une image

Le code suivant imprime les attributs de couleur détectés dans l’image, comme les couleurs dominantes et la couleur d’accentuation. Pour plus d’informations, consultez [Modèles de couleurs](../concept-detecting-color-schemes.md).

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors)]

Définissez la fonction d’assistance `printColorScheme` pour imprimer les détails du modèle de couleurs sur la console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_colors_print)]

### <a name="get-domain-specific-content"></a>Obtenir le contenu spécifique à un domaine

La Vision par ordinateur peut utiliser un modèle spécialisé pour effectuer une analyse approfondie des images. Pour plus d’informations, consultez [Contenu spécifique à un domaine](../concept-detecting-domain-content.md).

Tout d’abord, définissez l’URL d’une image à analyser :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_domain_image)]

Le code suivant analyse des données relatives aux monuments détectés dans l’image.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks)]

Définissez la fonction d’assistance `formatRectDomain` pour analyser les données d’emplacement relatives aux points de repère détectés.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_landmarks_rect)]

### <a name="get-the-image-type"></a>Obtenir le type d’image

Le code suivant affiche des informations sur le type d’image, qu’il s’agisse d’une image clipart ou d’un dessin au trait.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype)]

Définissez la fonction d’assistance `describeType` :

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_imagetype_describe)]

## <a name="read-printed-and-handwritten-text"></a>Lire du texte imprimé et manuscrit

Vision par ordinateur peut lire du texte visible dans une image et le convertir en flux de caractères.

> [!NOTE]
> Vous pouvez également lire du texte à partir d’une image locale. Consultez l’exemple de code sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js) pour obtenir des scénarios impliquant des images locales.

### <a name="set-up-test-images"></a>Configurer des images de test

Enregistrez une référence à l’URL des images à partir desquelles vous souhaitez extraire du texte.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_images)]

### <a name="call-the-recognize-api"></a>Appeler l’API de reconnaissance

Ajoutez le code ci-dessous afin d’appeler la fonction `recognizeText` pour les images données.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_call)]

Définissez la fonction `recognizeText`. La méthode **recognizeText** est ainsi appelée sur l’objet client, qui retourne un ID d’opération et démarre un processus asynchrone pour lire le contenu de l’image. Puis, il utilise l’ID d’opération pour vérifier l’opération par intervalles d’une seconde, jusqu’à ce que les résultats soient retournés. Il retourne ensuite les résultats extraits.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_helper)]

Définissez alors la fonction d’assistance `printRecText`, qui imprime les résultats d’une opération de reconnaissance sur la console.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/ComputerVision/ComputerVisionQuickstart.js?name=snippet_read_print)]

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `node` de votre fichier de démarrage rapide.

```console
node index.js
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
>[Référence de l’API Vision par ordinateur (Node.js)](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-computervision/?view=azure-node-latest)

* [Qu’est-ce que le service Vision par ordinateur ?](../Home.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/ComputerVision/ComputerVisionQuickstart.js).
