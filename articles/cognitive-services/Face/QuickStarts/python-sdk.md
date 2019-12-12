---
title: 'Démarrage rapide : Bibliothèque de client Visage pour Python | Microsoft Docs'
description: Cet article vous aidera à vous familiariser avec la bibliothèque cliente Visage pour Python afin de détecter, rechercher des informations similaires, les identifier, les vérifier et bien plus encore.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: cd797567b381fb89c568b06dc8b056648e5c734a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977809"
---
# <a name="quickstart-face-client-library-for-python"></a>Démarrage rapide : Bibliothèque de client Visage pour Python

Commencez à utiliser la bibliothèque de client Visage pour Python. Suivez les étapes suivantes pour installer le package et essayer l’exemple de code pour les tâches de base. Le service API Visage vous donne accès à des algorithmes avancés pour la détection et la reconnaissance des visages dans des images.

Utilisez la bibliothèque de client Visage pour Python pour :

* Détecter des visages dans une image
* Rechercher des visages semblables
* Créer et entraîner un groupe de personnes
* Identifier un visage
* Vérifier les visages
* Prendre une capture instantanée pour la migration de données

[Documentation de référence](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [Package (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [Exemples C#](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Prérequis

* Abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Configuration

### <a name="create-a-face-azure-resource"></a>Créer une ressource Visage Azure

Les services Azure Cognitive Services sont représentés par des ressources Azure auxquelles vous vous abonnez. Créez une ressource pour Visage en utilisant le [portail Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) ou [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) sur votre ordinateur local. Vous pouvez également :

* Obtenir une [clé](https://azure.microsoft.com/try/cognitive-services/#decision) pour un essai gratuit valide pendant 7 jours. Une fois l’inscription terminée, elle est disponible sur le [site web Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Afficher cette ressource sur le [portail Azure](https://portal.azure.com/).

Après avoir obtenu une clé à partir de votre abonnement ou ressource d’évaluation, [créez une variable d’environnement](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pour la clé, nommée `FACE_SUBSCRIPTION_KEY`.
 
### <a name="create-a-new-python-application"></a>Créer une application Python

Créez un script Python&mdash;*quickstart-file.py*, par exemple. Puis, ouvrez-le dans votre éditeur ou IDE habituel et importez les bibliothèques suivantes.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Ensuite, créez des variables pour le point de terminaison et la clé Azure de votre ressource. Vous devrez peut-être modifier la première partie du point de terminaison (`westus`) pour qu’il corresponde à votre abonnement.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Si vous avez créé la variable d’environnement après avoir lancé l’application, vous devez fermer et rouvrir l’éditeur, l’IDE ou le shell qui l’exécute pour accéder à la variable.

### <a name="install-the-client-library"></a>Installer la bibliothèque de client

Vous pouvez installer la bibliothèque de client avec :

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>Modèle objet

Les classes et interfaces suivantes gèrent certaines des principales fonctionnalités du SDK Visage Python.

|Nom|Description|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Cette classe représente votre autorisation d’utiliser le service Visage. Vous en avez besoin pour toutes les fonctionnalités de Visage. Vous pouvez l’instancier avec vos informations d’abonnement et l’utiliser pour produire des instances d’autres classes. |
|[FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Cette classe gère les tâches de détection et de reconnaissance de base que vous pouvez effectuer avec les visages. |
|[DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Cette classe représente toutes les données détectées à partir d’un visage unique dans une image. Vous pouvez l’utiliser pour récupérer des informations détaillées sur le visage.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Cette classe gère les constructions **FaceList** stockées dans le cloud, comprenant un ensemble de visages assortis. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Cette classe gère les constructions **Person** stockées dans le cloud, comprenant un ensemble de visages appartenant à une même personne.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Cette classe gère les constructions **PersonGroup** stockées dans le cloud, comprenant un ensemble d’objets **Person** assortis. |
|[ShapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Cette classe gère la fonctionnalité de capture instantanée. Vous pouvez l’utiliser pour enregistrer temporairement toutes vos données de visage informatiques et migrer ces données vers un nouvel abonnement Azure. |

## <a name="code-examples"></a>Exemples de code

Ces extraits de code montrent comment effectuer les tâches suivantes avec la bibliothèque de client Visage pour Python :

* [Authentifier le client](#authenticate-the-client)
* [Détecter des visages sur une image](#detect-faces-in-an-image)
* [Rechercher des visages semblables](#find-similar-faces)
* [Créer et entraîner un groupe de personnes](#create-and-train-a-person-group)
* [Identifier un visage](#identify-a-face)
* [Vérifier les visages](#verify-faces)
* [Prendre une capture instantanée pour la migration de données](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Authentifier le client

> [!NOTE]
> Ce guide de démarrage rapide part du principe que vous avez [créé une variable d’environnement](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) pour votre clé Visage, nommée `FACE_SUBSCRIPTION_KEY`.

Instanciez un client avec votre point de terminaison et la clé. Créez un objet [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) avec votre clé et utilisez-le avec votre point de terminaison pour créer un objet [FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python).

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Détecter des visages dans une image

Le code suivant détecte un visage dans une image distante. Il affiche l’ID du visage détecté sur la console et le stocke dans la mémoire du programme. Ensuite, il détecte les visages dans une image où figurent plusieurs personnes et affiche également leurs ID sur la console. En modifiant les paramètres de la méthode [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-), vous pouvez retourner différentes informations avec chaque objet [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python).

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Pour consulter d’autres scénarios de détection, reportez-vous à l’exemple de code sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).

### <a name="display-and-frame-faces"></a>Afficher et encadrer des visages

Le code suivant génère l’image donnée à l’écran et trace des rectangles autour des visages, à l’aide de la propriété DetectedFace.faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Jeune femme dont le visage est indiqué par un rectangle rouge](../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Rechercher des visages semblables

Le code suivant recherche un ensemble de visages correspondant à un unique visage détecté. Quand il trouve une correspondance, il affiche les coordonnées du rectangle du visage sur la console. 

### <a name="find-matches"></a>Rechercher des correspondances

Tout d’abord, exécutez le code de la section ci-dessus ([Détecter des visages dans une image](#detect-faces-in-an-image)) pour enregistrer une référence à un visage unique. Exécutez ensuite le code suivant pour obtenir des références à plusieurs visages dans une image de groupe.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Ajoutez ensuite le bloc de code suivant pour rechercher les instances du premier visage du groupe. Pour savoir comment modifier ce comportement, consultez la section sur la méthode [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-).

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Afficher les correspondances

Utilisez le code suivant pour afficher les détails sur les correspondances sur la console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Créer et entraîner un groupe de personnes

Le code suivant crée un **PersonGroup** avec trois objets **Person** différents. Il associe chaque objet **Person** à un ensemble d’exemples d’images, puis s’entraîne pour pouvoir reconnaître chaque personne. 

### <a name="create-persongroup"></a>Créer un PersonGroup

Pour suivre ce scénario, vous devez enregistrer les images suivantes dans le répertoire racine de votre projet : https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Ce groupe d’images contient trois ensembles d’images de visage correspondant à trois personnes différentes. Le code définit trois objets **Person** et les associe aux fichiers image qui commencent par `woman`, `man`et `child`.

Une fois que vous avez configuré vos images, définissez une étiquette en haut de votre script pour l’objet **PersonGroup** que vous allez créer.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Ajoutez ensuite le code suivant au bas de votre script. Ce code crée un objet **PersongGroup** et trois objets **Person**.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Attribuer des visages aux personnes

Le code suivant trie vos images en fonction de leur préfixe, détecte les visages et attribue les visages à chaque objet **Person**.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>Entraîner le PersonGroup

Une fois que vous avez attribué des visages, vous devez entraîner le **PersonGroup** pour qu’il puisse identifier les caractéristiques visuelles associées à chacun de ses objets **Person**. Le code suivant appelle la méthode **train** asynchrone et interroge le résultat en affichant l’état sur la console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Identifier un visage

Le code suivant recherche l’identité de chaque personne dans une image contenant plusieurs visages. Il compare chaque visage détecté à un **PersonGroup**, une base de données comprenant différents objets **Person** dont les caractéristiques du visage sont connues.

> [!IMPORTANT]
> Pour exécuter cet exemple, vous devez d’abord exécuter le code fourni à la section [Créer et entraîner un groupe de personnes](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Obtenir une image de test

Le code suivant recherche une image _test-image-person-group.jpg_ à la racine du projet et détecte les visages dans l’image. Vous pouvez trouver cette image avec les images utilisées pour la gestion de **PersonGroup** : https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Identifier des visages

La méthode **identify** compare un groupe de visages détectés à un **PersonGroup**. Si un visage détecté correspond à un objet **Person**, elle enregistre le résultat. Ce code affiche les résultats de correspondance détaillés sur la console.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Vérifier les visages

L’opération Vérifier accepte un ID de visage et soit un autre ID de visage, soit un objet **Person**, et détermine s’ils appartiennent à la même personne.

Le code suivant détecte des visages dans deux images sources, puis les compare à un visage détecté à partir d’une image cible.

### <a name="get-test-images"></a>Obtenir les images de test

Les blocs de code suivants déclarent des variables qui pointent vers les images source et cible pour l’opération de vérification.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Détecter les visages à vérifier

Le code suivant détecte des visages dans les images source et cible, et les enregistre dans des variables.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Obtenir les résultats de la vérification

Le code suivant compare chacune des images sources à l’image cible et affiche un message indiquant si elles appartiennent à la même personne.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="take-a-snapshot-for-data-migration"></a>Prendre une capture instantanée pour la migration de données

La fonctionnalité de captures instantanées vous permet de déplacer les données de visage enregistrées, telles qu’un **PersonGroup** entraîné, vers un autre abonnement Azure Cognitive Services Visage. Vous pouvez l’utiliser si, par exemple, vous avez créé un objet **PersonGroup** à l’aide d’un abonnement d’essai gratuit et vous souhaitez à présent migrer cet objet vers un abonnement payant. Consultez la section [Migrer vos données de visage](../Face-API-How-to-Topics/how-to-migrate-face-data.md) pour obtenir une vue d’ensemble de la fonctionnalité de captures instantanées.

Dans cet exemple, vous allez migrer le **PersonGroup** que vous avez créé à l’étape [Créer et entraîner un groupe de personnes](#create-and-train-a-person-group). Vous pouvez d’abord terminer cette section ou utiliser vos propres constructions de données Visage.

### <a name="set-up-target-subscription"></a>Configurer l’abonnement cible

Tout d’abord, vous devez disposer d’un deuxième abonnement Azure avec une ressource Visage. Pour ce faire, procédez comme indiqué dans la section [Configuration](#setting-up). 

Ensuite, créez les variables suivantes dans la partie supérieure de votre script. Vous devez également créer des variables d’environnement pour l’ID d’abonnement de votre compte Azure ainsi que la clé, le point de terminaison et l’ID d’abonnement de votre nouveau compte (cible). 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Authentifier le client cible

Plus tard dans le script, enregistrez votre objet client actuel en tant que client source, puis authentifiez un nouvel objet client pour votre abonnement cible. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Utiliser une capture instantanée

Le reste des opérations de capture instantanée s’effectue dans une fonction asynchrone. 

1. La première étape consiste à **prendre** la capture instantanée, ce qui enregistre les données de visage de votre abonnement d’origine à un emplacement temporaire dans le cloud. Cette méthode retourne un ID que vous utilisez pour interroger l’état de l’opération.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Interrogez ensuite l’ID jusqu’à ce que l’opération soit terminée.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Ce code utilise la fonction `wait_for_operation`, que vous devez définir séparément :

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Revenez à votre fonction asynchrone. Utilisez l’opération **apply** pour écrire les données de visage dans votre abonnement cible. Cette méthode retourne également un ID.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Utilisez de nouveau la fonction `wait_for_operation` pour interroger l’ID jusqu’à ce que l’opération soit terminée.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

Une fois ces étapes terminées, vous pouvez accéder à vos constructions de données de visage à partir de votre nouvel abonnement (cible).

## <a name="run-the-application"></a>Exécution de l'application

Exécutez l’application avec la commande `python` de votre fichier de démarrage rapide.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Interface de ligne de commande Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Si vous avez créé un **PersonGroup** dans le cadre de ce guide de démarrage rapide et que vous souhaitez le supprimer, exécutez le code suivant dans votre script :

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

Si vous avez migré des données à l’aide de la fonctionnalité de capture instantanée dans le cadre de ce guide de démarrage rapide, vous devez également supprimer le **PersonGroup** enregistré dans l’abonnement cible.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez découvert comment utiliser la bibliothèque Visage pour Python afin d’effectuer des tâches de base. Pour plus d’informations sur la bibliothèque, reportez-vous à la documentation de référence.

> [!div class="nextstepaction"]
> [Informations de référence sur l’API Visage (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [Qu’est ce que le l’API Visage ?](../overview.md)
* Le code source de cet exemple est disponible sur [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).