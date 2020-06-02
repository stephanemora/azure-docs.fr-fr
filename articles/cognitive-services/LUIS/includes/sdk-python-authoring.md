---
title: Fichier include
description: Fichier include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/26/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: 1e51c4e9d0c3da8b6ad76b4b45869ea8b2394008
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83871268"
---
Utilisez la bibliothèque de client de création Language Understanding (LUIS) pour Python afin de :

* Créer une application.
* Ajouter des intentions, des entités et des exemples d’énoncés.
* Ajouter des fonctionnalités telles qu’une liste d’expressions.
* Entraîner et publier une application.

[Documentation de référence](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [Package de création (Pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [Exemples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* Version actuelle de [Python 3.x](https://www.python.org/).
* Une fois en possession de votre abonnement Azure, [créez une ressource de création LUIS](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) sur le portail Azure pour obtenir vos clé et point de terminaison. Attendez qu’elle se déploie, puis cliquez sur le bouton **Accéder à la ressource**.
    * Vous aurez besoin de la clé et du point de terminaison de la ressource que vous [créez](../luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal) pour connecter votre application à la création LUIS. Vous collerez votre clé et votre point de terminaison dans le code ci-dessous plus loin dans le guide de démarrage rapide. Vous pouvez utiliser le niveau tarifaire gratuit (`F0`) pour tester le service.

## <a name="setting-up"></a>Configuration

### <a name="install-the-python-library-for-luis"></a>Installer la bibliothèque Python pour LUIS

Dans le répertoire de l’application, installez la bibliothèque de client de création Language Understanding (LUIS) pour Python avec la commande suivante :

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Modèle objet

Le client de création Language Understanding (LUIS) est un objet [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python) qui s’authentifie auprès d’Azure et contient votre clé de création.

Une fois le client créé, utilisez-le pour accéder aux fonctionnalités, notamment :

* Applications : [créer](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-), [supprimer](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-), [publier](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* Exemples d’énoncés : [ajouter par lot](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-), [supprimer par ID](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-)
* Fonctionnalités : gérer les [listes d’expressions](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-)
* Modèle : gérer les [intentions](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) et les entités
* Modèle : gérer les [modèles](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-)
* Entraînement : [entraînez](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) l’application et interrogez l’[état de l’entraînement](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-)
* [Versions](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python) : gérer avec clonage, exportation et suppression


## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les opérations suivantes avec la bibliothèque de client de création Language Understanding (LUIS) pour Python :

* [Créer une application](#create-a-luis-app)
* [Ajouter des entités](#create-entities-for-the-app)
* [Ajouter des intentions](#create-intent-for-the-app)
* [Ajouter des exemples d’énoncés](#add-example-utterance-to-intent)
* [Entraîner l’application](#train-the-app)
* [Publier l’application](#publish-a-language-understanding-app)

## <a name="create-a-new-python-application"></a>Créer une application Python

Créez une application Python dans votre éditeur ou IDE favori. Importez ensuite les bibliothèques suivantes.

[!code-python[Create a new Python application in your preferred editor or IDE.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Dependencies)]

Créez des variables pour le point de terminaison et la clé Azure de votre ressource. Si vous avez créé la variable d’environnement après avoir lancé l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou le shell qui l’exécute pour accéder à la variable.

[!code-python[Create variables for your resource's Azure endpoint and key.](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=AuthorizationVariables)]

## <a name="authenticate-the-client"></a>Authentifier le client

Créez un objet [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) avec votre clé et utilisez-le avec votre point de terminaison pour créer un objet [LUISAuthoringClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python).

[!code-python[Create LUIS client object](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=Client)]

## <a name="create-a-luis-app"></a>Créer une application LUIS

1. Créez une application LUIS pour stocker le modèle de traitement en langage naturel contenant les intentions, les entités et les exemples d’énoncés.

1. Créez une méthode [add](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-) pour l’objet [AppsOperation](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python) afin de créer l’application. Le nom et la culture de la langue sont des propriétés obligatoires.

    [!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=createApp)]


## <a name="create-intent-for-the-app"></a>Créer une intention pour l’application
L’objet principal dans un modèle d’application LUIS est l’intention. L’intention s’aligne sur un regroupement d’_intentions_ d’énoncés utilisateur. Un utilisateur peut poser une question ou émettre un énoncé en souhaitant obtenir une réponse _prévue_ particulière d’un bot (ou d’une autre application cliente). Réserver un billet d’avion, demander quelle est la météo dans une ville de destination et demander des informations de contact pour un service client sont des exemples d’intentions.

Utilisez la méthode [model.add_intent](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) avec le nom de l’intention unique, puis transmettez l’ID de l’application, l’ID de version et le nom de la nouvelle intention.

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addIntents)]

## <a name="create-entities-for-the-app"></a>Créer des entités pour l’application

Bien que les entités ne soient pas obligatoires, elles sont présentes dans la plupart des applications. L’entité extrait des informations à partir de l’énoncé utilisateur, qui sont nécessaires pour répondre à l’intention de l’utilisateur. Il existe plusieurs types d’entités [prédéfinies](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) et personnalisées, chacune avec leurs propres modèles DTO (Data Transformation Object).  Les entités prédéfinies courantes à ajouter à votre application incluent [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md) et [ordinal](../luis-reference-prebuilt-ordinal.md).

Cette méthode **add_entities** crée une entité simple `Location` avec deux rôles, une entité simple `Class`, une entité composite `Flight`, et ajoute plusieurs entités prédéfinies.

Il est important de savoir que les entités ne sont pas marquées avec une intention. Elles peuvent s’appliquer à de nombreuses intentions. Seuls les exemples d’énoncés utilisateur sont marqués pour une intention unique spécifique.

Les méthodes de création pour les entités font partie de la classe [ModelOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python). Chaque type d’entité possède son propre modèle DTO (Data Transformation Object).

[!code-python[Create LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addEntities)]

## <a name="add-example-utterance-to-intent"></a>Ajouter un exemple d’énoncé à une intention

Pour déterminer l’intention d’un énoncé et extraire des entités, l’application a besoin d’exemples d’énoncés. Les exemples doivent cibler une intention spécifique et unique, et doivent marquer toutes les entités personnalisées. Les entités prédéfinies n’ont pas besoin d’être marquées.

Ajoutez des exemples d’énoncés en créant une liste d’objets [ExampleLabelObject](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python), un objet pour chaque exemple d’énoncé. Chaque exemple doit marquer toutes les entités avec un dictionnaire de paires nom/valeur de nom d’entité et de valeur d’entité. La valeur de l’entité doit être exactement telle qu’elle apparaît dans le texte de l’exemple d’énoncé.

Appelez [examples.batch](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-) avec l’ID de l’application, l’ID de version et la liste des exemples. L’appel répond avec une liste de résultats. Vous devez vérifier le résultat de chaque exemple pour vous assurer qu’il a été correctement ajouté au modèle.

[!code-python[Add example utterances via a batch](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=addUtterances)]

## <a name="train-the-app"></a>Effectuer l’apprentissage de l’application

Une fois le modèle créé, l’application LUIS doit être entraînée pour cette version du modèle. Un modèle entraîné peut être utilisé dans un [conteneur](../luis-container-howto.md) ou [publié](../luis-how-to-publish-app.md) dans les emplacements intermédiaires ou produits.

La méthode [train.train_version](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) a besoin de l’ID de l’application et de l’ID de version.

Un modèle très petit, comme l’illustre ce guide de démarrage rapide, sera entraîné très rapidement. Pour les applications de niveau production, l’entraînement de l’application doit inclure un appel d’interrogation à la méthode [get_status](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) pour déterminer si l’entraînement a réussi. La réponse est une liste d’objets [ModelTrainingInfo](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python) avec un état distinct pour chaque objet. Tous les objets doivent réussir pour que l’entraînement soit considéré comme terminé.

[!code-python[Train LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=train)]

## <a name="publish-a-language-understanding-app"></a>Publier une application Language Understanding

Publiez l’application LUIS à l’aide de la méthode [app.publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-). Cela permet de publier la version entraînée actuelle à l’emplacement spécifié au point de terminaison. Votre application cliente utilise ce point de terminaison afin d’envoyer des énoncés utilisateur pour la prédiction de l’intention et l’extraction d’entité.

[!code-python[Publish LUIS client app](~/cognitive-services-quickstart-code/python/LUIS/application_quickstart.py?name=publish)]

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `python` de votre fichier de démarrage rapide.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Une fois vos prédictions effectuées, nettoyez le travail accompli dans ce guide de démarrage rapide en supprimant le fichier et ses sous-répertoires.