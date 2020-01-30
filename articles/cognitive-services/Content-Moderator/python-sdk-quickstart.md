---
title: 'Démarrage rapide : Bibliothèque cliente Content Moderator pour Python'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, apprenez à utiliser la bibliothèque cliente Azure Cognitive Services Content Moderator pour Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 31cdc9663283b580acc10e7ac8d5a77f9036a7a8
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772356"
---
# <a name="quickstart-content-moderator-client-library-for-python"></a>Démarrage rapide : Bibliothèque cliente Content Moderator pour Python

Commencez à utiliser la bibliothèque cliente Content Moderator pour Python. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base. Content Moderator est un service cognitif qui vérifie le texte, les images et le contenu vidéo à la recherche d’éléments potentiellement dangereux, offensants ou indésirables. Lorsque des éléments de ce type sont détectés, le service applique les étiquettes appropriées (indicateurs) au contenu. L’application peut ensuite gérer le contenu marqué afin de se conformer aux réglementations ou pour maintenir l’environnement souhaité pour les utilisateurs.

Utilisez la bibliothèque cliente Content Moderator pour Python aux fins suivantes :

* [Modérer du texte](#moderate-text)
* [Utiliser une liste de termes personnalisée](#use-a-custom-terms-list)
* [Modérer les images](#moderate-images)
* [Utiliser une liste d’images personnalisée](#use-a-custom-image-list)
* [Créer une révision](#create-a-review)

[Documentation de référence](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/contentmoderator?view=azure-python) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-contentmoderator) | [Package (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-contentmoderator/) | [Exemples C#](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="prerequisites"></a>Conditions préalables requises

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configuration

### <a name="create-a-content-moderator-azure-resource"></a>Créer une ressource Azure Content Moderator

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour Content Moderator en utilisant le [portail Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) sur votre ordinateur local. Vous pouvez également :

* Obtenir une [clé](https://azure.microsoft.com/try/cognitive-services/#decision) pour un essai gratuit valide pendant 7 jours. Une fois l’inscription terminée, elle est disponible sur le [site web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Afficher cette ressource sur le [portail Azure](https://portal.azure.com/).

Une fois que vous avez obtenu une clé à partir de votre abonnement ou ressource d’essai, [créez des variables d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la clé et l’URL de point de terminaison, nommées respectivement `CONTENT_MODERATOR_SUBSCRIPTION_KEY` et `CONTENT_MODERATOR_ENDPOINT`.
 
### <a name="create-a-python-script"></a>Créer un script Python

Créez un script Python et ouvrez-le dans votre éditeur ou IDE favori. Ajoutez ensuite les instructions `import` suivantes en haut du fichier.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imports)]

Ensuite, créez des variables pour l’emplacement et la clé du point de terminaison de votre ressource en tant que variables d’environnement. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_vars)]

> [!NOTE]
> Si vous avez créé les variables d’environnement après avoir lancé l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou le shell qui l’exécute pour accéder aux variables.

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Vous pouvez installer la bibliothèque cliente Content Moderator à l’aide de la commande suivante :

```console
pip install --upgrade azure-cognitiveservices-vision-contentmoderator
```

## <a name="object-model"></a>Modèle objet

Les classes suivantes gèrent certaines des principales fonctionnalités du SDK Python Content Moderator.

|Name|Description|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python)|Cette classe est nécessaire pour toutes les fonctionnalités Content Moderator. Vous pouvez l’instancier avec vos informations d’abonnement et l’utiliser pour produire des instances d’autres classes.|
|[ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python)|Cette classe fournit les fonctionnalités permettant d’analyser des images pour y rechercher du contenu pour adultes, des informations personnelles ou des visages.|
|[TextModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)|Cette classe fournit les fonctionnalités d’analyse de texte pour le langage, les blasphèmes, les erreurs et les informations personnelles.|
[ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python)|Cette classe fournit les fonctionnalités des API de révision, notamment les méthodes de création de travaux, les workflows personnalisés et les révisions humaines.|

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque cliente Content Moderator pour Python :

* [Authentifier le client](#authenticate-the-client)
* [Modérer du texte](#moderate-text)
* [Utiliser une liste de termes personnalisée](#use-a-custom-terms-list)
* [Modérer les images](#moderate-images)
* [Utiliser une liste d’images personnalisée](#use-a-custom-image-list)
* [Créer une révision](#create-a-review)

## <a name="authenticate-the-client"></a>Authentifier le client

> [!NOTE]
> Ce guide de démarrage rapide suppose que vous avez [créé des variables d’environnement](../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pour la clé et le point de terminaison de Content Moderator.

Instanciez un client avec votre point de terminaison et la clé. Créez un objet [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) avec votre clé et utilisez-le avec votre point de terminaison pour créer un objet [ContentModeratorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.content_moderator_client.contentmoderatorclient?view=azure-python).

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_client)]

## <a name="moderate-text"></a>Modérer du texte

Le code suivant utilise un client Content Moderator pour analyser un corps de texte et imprimer les résultats dans la console. Tout d’abord, créez un dossier **text_files/** à la racine de votre projet, puis ajoutez un fichier *content_moderator_text_moderation.txt*. Ajoutez votre propre texte à ce fichier, ou utilisez l’exemple de texte suivant :

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 2065550111
```

Ajoutez une référence à ce dossier.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

Ensuite, ajoutez le code suivant à votre script Python.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textmod)]

## <a name="use-a-custom-terms-list"></a>Utiliser une liste de termes personnalisée

Le code suivant montre comment gérer une liste de termes personnalisée pour la modération du texte. Vous pouvez utiliser la classe [ListManagementTermListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementtermlistsoperations?view=azure-python) pour créer une liste de termes, gérer les termes individuels et filtrer d’autres corps de texte par rapport à celle-ci.

### <a name="get-sample-text"></a>Se procurer un exemple de texte

Pour utiliser cet exemple, créez un dossier **text_files/** à la racine de votre projet, puis ajoutez un fichier *content_moderator_term_list.txt*. Ce fichier doit contenir du texte organique qui sera comparé à la liste des termes. Vous pouvez utiliser l’exemple de texte suivant :

```
This text contains the terms "term1" and "term2".
```

Ajoutez une référence au dossier si vous n’en avez pas déjà défini une.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_textfolder)]

### <a name="create-a-list"></a>Créer une liste

Ajoutez le code suivant à votre script Python pour créer une liste de termes personnalisée et enregistrer sa valeur d’ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_create)]

### <a name="define-list-details"></a>Définir les détails de la liste

Vous pouvez utiliser l’ID d’une liste pour modifier son nom et sa description.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_details)]

### <a name="add-a-term-to-the-list"></a>Ajouter un terme à la liste

Le code suivant ajoute les termes `"term1"` et `"term2"` à la liste.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_add)]

### <a name="get-all-terms-in-the-list"></a>Obtenir tous les termes de la liste

Vous pouvez utiliser l’ID de liste pour retourner tous les termes de la liste.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_getterms)]

### <a name="refresh-the-list-index"></a>Actualiser l’index de liste

Chaque fois que vous ajoutez ou supprimez des termes de la liste, vous devez actualiser l’index avant de pouvoir utiliser la liste mise à jour.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_refresh)]

### <a name="screen-text-against-the-list"></a>Analyser le texte par rapport à la liste

La fonctionnalité principale de la liste de termes personnalisée est de comparer un corps de texte à la liste et de déterminer s’il existe des termes correspondants. 

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_screen)]

### <a name="remove-a-term-from-a-list"></a>Supprimer un terme d’une liste

Le code suivant supprime le terme `"term1"` de la liste.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_remove)]

### <a name="remove-all-terms-from-a-list"></a>Supprimer tous les termes d’une liste

Utilisez le code suivant pour effacer tous les termes de la liste.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_removeall)]

### <a name="delete-list"></a>Supprimer la liste

Utilisez le code suivant pour supprimer une liste de termes personnalisée.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_termslist_deletelist)]

## <a name="moderate-images"></a>Modérer des images

Le code suivant utilise un client Content Moderator, ainsi qu’un objet [ImageModerationOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.imagemoderationoperations?view=azure-python), pour analyser les images et y rechercher du contenu pour adultes et osé.

### <a name="get-images"></a>Obtenir des images

Définissez une référence à certaines images à analyser.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemodvars)]

Ajoutez ensuite le code suivant pour itérer au sein de vos images. Le reste du code de cette section va dans cette boucle.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>Rechercher du contenu pour adultes et osé

Le code suivant vérifie l’image à l’URL donnée pour y rechercher du contenu pour adultes ou osé et imprime le résultat dans la console. Pour plus d’informations sur ces termes, consultez le [guide conceptuel sur la modération d’images](./image-moderation-api.md).

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_ar)]

### <a name="check-for-visible-text"></a>Rechercher le texte visible

Le code suivant recherche le contenu textuel visible dans l’image et imprime les résultats dans la console.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Rechercher des visages

Le code suivant recherche dans l’image les visages humains et imprime les résultats dans la console.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagemod_face)]

## <a name="use-a-custom-image-list"></a>Utiliser une liste d’images personnalisée

Le code suivant montre comment gérer une liste personnalisée d’images pour la modération d’image. Cette fonctionnalité est utile si votre plateforme reçoit fréquemment des instances du même jeu d’images que vous souhaitez faire sortir de l’écran. En conservant une liste de ces images spécifiques, vous pouvez améliorer les performances. La classe [ListManagementImageListsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.listmanagementimagelistsoperations?view=azure-python) vous permet de créer une liste d’images, de gérer les images individuelles dans la liste et de comparer d’autres images à celles-ci.

Créez les variables de texte suivantes pour stocker les URL d’image que vous allez utiliser dans ce scénario.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelistvars)]

> [!NOTE]
> Il ne s’agit pas de la liste proprement dite, mais d’une liste informelle d’images qui seront ajoutées à la section `add images` du code.


### <a name="create-an-image-list"></a>Créer une liste d’image

Ajoutez le code suivant pour créer une liste d’images et enregistrer une référence à son ID.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_create)]

### <a name="add-images-to-a-list"></a>Ajouter des images à une liste

Le code suivant ajoute toutes vos images à la liste.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_add)]

Définissez la fonction d’assistance **add_images** ailleurs dans votre script.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_addhelper)]

### <a name="get-images-in-list"></a>Récupérer les images dans la liste

Le code suivant imprime les noms de toutes les images de votre liste.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getimages)]

### <a name="update-list-details"></a>Mettre à jour les détails de la liste

Vous pouvez utiliser l’ID de liste pour mettre à jour le nom et la description de la liste.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_updatedetails)]

### <a name="get-list-details"></a>Obtenir les détails de la liste

Utilisez le code suivant pour imprimer les détails actuels de votre liste.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_getdetails)]

### <a name="refresh-the-list-index"></a>Actualiser l’index de liste

Chaque fois que vous ajoutez ou supprimez des images, vous devez actualiser l’index de liste avant de pouvoir l’utiliser pour vérifier d’autres images.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_refresh)]

### <a name="match-images-against-the-list"></a>Faire correspondre les images à celles de la liste

La principale fonction des listes d’images consiste à comparer les nouvelles images et à vérifier s’il existe des correspondances.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_match)]

### <a name="remove-an-image-from-the-list"></a>Supprimer une image de la liste

Le code suivant supprime un élément de la liste. Dans ce cas, il s’agit d’une image qui ne correspond pas à la catégorie de liste.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_remove)]

### <a name="remove-all-images-from-a-list"></a>Supprimer toutes les images d’une liste

Utilisez le code suivant pour effacer une liste d’images.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_removeall)]

### <a name="delete-the-image-list"></a>Supprimer la liste d’images

Utilisez le code suivant pour supprimer une liste d’images donnée.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagelist_delete)]

## <a name="create-a-review"></a>Créer une révision

Vous pouvez utiliser le SDK Python Content Moderator pour alimenter le contenu dans l’[outil de révision](https://contentmoderator.cognitive.microsoft.com) afin que les modérateurs humains puissent le passer en revue. Pour en savoir plus sur l’outil de révision, consultez le [Guide conceptuel de l’outil de révision du code](./review-tool-user-guide/human-in-the-loop.md).

Le code suivant utilise la classe [ReviewsOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.reviewsoperations?view=azure-python) pour créer une révision, récupérer son ID et vérifier ses détails après avoir reçu une entrée humaine via le portail web de l’outil de révision.

### <a name="get-review-credentials"></a>Obtenir les informations d’identification de révision

Tout d’abord, connectez-vous à l’outil de révision et récupérez le nom de votre équipe. Ensuite, assignez-le à la variable appropriée dans le code. Si vous le souhaitez, vous pouvez configurer un point de terminaison de rappel pour recevoir des mises à jour sur l’activité de la révision.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_vars)]

### <a name="create-an-image-review"></a>Créer une révision d’image

Ajoutez le code suivant pour créer et poster une révision pour l’URL de l’image donnée. Le code enregistre une référence à l’ID de révision. 
[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_create)]

### <a name="get-review-details"></a>Obtenir les informations d’une révision

Utilisez le code suivant pour vérifier les détails d’une révision donnée. Une fois la révision créée, vous pouvez accéder à l’outil de révision vous-même et interagir avec le contenu. Pour plus d’informations sur la procédure à suivre, consultez le [Guide pratiques sur les révisions du code](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/review-moderated-images). Lorsque vous avez terminé, vous pouvez réexécuter ce code et récupérer les résultats du processus de révision.

[!code-python[](~/cognitive-services-quickstart-code/python/ContentModerator/ContentModeratorQuickstart.py?name=snippet_imagereview_getdetails)]

Si vous avez utilisé un point de terminaison de rappel dans ce scénario, il doit recevoir un événement au format suivant :

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `python` de votre fichier de démarrage rapide.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portail](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à utiliser la bibliothèque Python Content Moderator pour effectuer des tâches de modération. Pour plus d’informations sur la modération des images ou d’autres éléments multimédias, consultez le guide conceptuel.

> [!div class="nextstepaction"]
>[Concepts liés à la modération d’image](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Qu’est-ce qu’Azure Content Moderator ?](./overview.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ContentModerator/ContentModeratorQuickstart.py).