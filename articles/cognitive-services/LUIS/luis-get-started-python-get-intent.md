---
title: Démarrage rapide pour Python - Prédire une intention - LUIS
titleSuffix: Azure Cognitive Services
description: Dans ce démarrage rapide, vous allez apprendre à appeler une application LUIS à l’aide de Python.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: e560aeffecf63f63966a49053e0f79d012b4a0a3
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038270"
---
# <a name="quickstart-get-intent-using-python"></a>Démarrage rapide : Reconnaître l’intention à l’aide de Python
Dans ce démarrage rapide, vous allez transmettre des énoncés à un point de terminaison LUIS et obtenir en retour une intention et des entités.

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Prérequis

* [Python 3.6](https://www.python.org/downloads/) ou version ultérieure.
* [Visual Studio Code](https://code.visualstudio.com/)

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obtenir la clé LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Reconnaître une intention avec le navigateur

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent--programmatically"></a>Reconnaître une intention par programmation

Vous pouvez utiliser Python pour accéder aux résultats que vous avez vus dans la fenêtre du navigateur à l’étape précédente.

1. Copiez l’un des extraits de code suivants dans un fichier nommé `quickstart-call-endpoint.py` :

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. Remplacez la valeur du champ `Ocp-Apim-Subscription-Key` par votre clé de point de terminaison LUIS.

3. Installez les dépendances avec `pip install requests`.

4. Exécutez le script avec `python ./quickstart-call-endpoint.py`. Des valeurs identiques à celles que vous avez vues plus tôt dans la fenêtre du navigateur s’affichent.

## <a name="luis-keys"></a>Clés LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources
Supprimez le fichier python. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des énoncés](luis-get-started-python-add-utterance.md)