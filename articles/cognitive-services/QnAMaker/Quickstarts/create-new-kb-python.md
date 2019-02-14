---
title: 'Démarrage rapide : Créer une base de connaissances - REST, Python - QnA Maker'
titlesuffix: Azure Cognitive Services
description: Ce guide de démarrage rapide basé sur REST en Python vous guide dans la création, par programmation, d’un exemple de base de connaissances QnA Maker qui apparaîtra dans le tableau de bord Azure de votre compte d’API Cognitive Services.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: 766fe32736ae0d8ea23805ea017d93b32119636a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873119"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-python"></a>Démarrage rapide : Créer une base de connaissances dans QnA Maker à l’aide de Python

Ce guide de démarrage rapide vous aide à créer et publier par programmation un exemple de base de connaissances QnA Maker. QnA Maker extrait automatiquement les questions et les réponses à partir du contenu semi-structuré, telles que les FAQ, de [sources de données](../Concepts/data-sources-supported.md). Le modèle de la base de connaissances est défini dans le code JSON envoyé dans le corps de la requête d’API. 

Ce démarrage rapide fait appel aux API QnA Maker :
* [Créer la base de connaissances](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Obtenir les détails de l’opération](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>Prérequis

* [Python 3.7](https://www.python.org/downloads/)
* Vous devez disposer d’un service QnA Maker. Pour récupérer votre clé, sélectionnez Clés sous Gestion des ressources dans votre tableau de bord.

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

## <a name="create-a-knowledge-base-python-file"></a>Créer un fichier Python de base de connaissances

Créez un fichier appelé `create-new-knowledge-base-3x.py`.

## <a name="add-the-required-dependencies"></a>Ajouter les dépendances nécessaires

En haut de `create-new-knowledge-base-3x.py`, ajoutez les lignes suivantes pour ajouter les dépendances nécessaires au projet :

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Ajouter les constantes nécessaires
Après les dépendances nécessaires précédentes, ajoutez les constantes nécessaires pour accéder à QnA Maker. Remplacez la valeur de la variable `subscriptionKey` par votre clé QnA Maker.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=5-13 "Add the required constants")]

## <a name="add-the-kb-model-definition"></a>Ajouter la définition du modèle de base de connaissances

Après les constantes, ajoutez la définition du modèle de base de connaissances suivante. Après la définition, le modèle est converti en une chaîne.

[!code-python[Add the KB model definition](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=15-41 "Add the KB model definition")]

## <a name="add-supporting-function"></a>Ajouter une fonction de prise en charge

Ajoutez la fonction suivante pour imprimer JSON dans un format lisible :

[!code-python[Add supporting function](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=43-45 "Add supporting function")]

## <a name="add-function-to-create-kb"></a>Ajouter une fonction pour créer la base de connaissances

Ajoutez la fonction suivante pour exécuter une requête HTTP POST afin de créer la base de connaissances. Cet appel d’API renvoie une réponse JSON qui inclut l’ID d’opération dans le champ d’en-tête **Emplacement**. Utilisez l’ID d’opération pour déterminer si la base de connaissances a bien été créée. `Ocp-Apim-Subscription-Key` correspond à la clé de service QnA Maker, utilisée pour l’authentification. 

[!code-python[Add function to create KB](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=48-59 "Add function to create KB")]

Cet appel d’API renvoie une réponse JSON qui inclut l’ID d’opération. Utilisez l’ID d’opération pour déterminer si la base de connaissances a bien été créée. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-function-to-check-creation-status"></a>Ajouter une fonction pour vérifier l’état de la création

La fonction suivante vérifie l’état de la création en envoyant l’ID d’opération à la fin de l’itinéraire d’URL. L’appel à `check_status` se trouve à l’intérieur de la boucle _while_ principale.

[!code-python[Add function to check creation status](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=61-67 "Add function to check creation status")]

Cet appel d’API renvoie une réponse JSON qui inclut l’état de l’opération : 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

Répétez l’appel jusqu’à ce que l’opération réussisse ou échoue : 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-main-code-block"></a>Ajouter un bloc de code principal
La boucle suivante interroge l’état de l’opération de création régulièrement jusqu’à la fin de l’opération. 

[!code-python[Add main code block](~/samples-qnamaker-python/documentation-samples/quickstarts/create-knowledge-base/create-new-knowledge-base-3x.py?range=70-96 "Add main code block")]

## <a name="build-and-run-the-program"></a>Créez et exécutez le projet.

Entrez la commande suivante sur une ligne de commande pour exécuter le programme. Il envoie la requête à l’API QnA Maker pour créer la base de connaissances, puis demande des résultats toutes les 30 secondes. Chaque réponse est imprimée sur la fenêtre de console.

```bash
python create-new-knowledge-base-3x.py
```

Une fois votre base de connaissances créée, vous pouvez l’afficher dans votre portail QnA Maker, sur la page [Mes bases de connaissances](https://www.qnamaker.ai/Home/MyServices). Sélectionnez le nom de votre base de connaissances, par exemple QnA Maker FAQ, pour l’afficher.

[!INCLUDE [Clean up files and KB](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Documentation de référence pour l’API REST QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
