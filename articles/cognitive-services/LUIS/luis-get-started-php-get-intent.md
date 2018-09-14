---
title: Démarrage rapide pour analyser le texte en langage naturel dans l’API Language Understanding (LUIS) à l’aide de PHP - Cognitive Services - Azure Cognitive Services | Microsoft Docs
description: Dans ce démarrage rapide, utilisez une application LUIS publique disponible pour déterminer l’intention d’un utilisateur à partir du texte conversationnel. À l’aide de PHP, envoyez l’intention de l’utilisateur sous forme de texte au point de terminaison de prédiction HTTP de l’application publique. Au niveau du point de terminaison, LUIS applique le modèle de l’application publique pour analyser le sens du texte en langage naturel, déterminer l’intention globale et extraire les données pertinentes pour le domaine de l’application.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 14ac32ffc0f4332d0cbc0da59a55ccc500e216d7
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "43769966"
---
# <a name="quickstart-analyze-text-using-php"></a>Démarrage rapide : Analyser le texte à l’aide de PHP

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Prérequis

* Langage de programmation [PHP](http://php.net/)
* [Visual Studio Code](https://code.visualstudio.com/)
* ID d’application publique : df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obtenir la clé LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Analyser le texte avec le navigateur

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-php"></a>Analyser le texte avec PHP 

Vous pouvez utiliser PHP pour accéder aux résultats que vous avez vus dans la fenêtre du navigateur à l’étape précédente. 

1. Copiez le code qui suit et enregistrez-le avec `endpoint-call.php` comme nom de fichier :

   [!code-php[PHP code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/php/endpoint-call.php)]

2. Remplacez `"YOUR-KEY"` par votre clé de point de terminaison.

3. Exécutez l’application PHP avec `php endpoint-call.php`. Des valeurs identiques à celles que vous avez vues plus tôt dans la fenêtre du navigateur s’affichent.

## <a name="luis-keys"></a>Clés LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Supprimer les ressources

Supprimez le fichier PHP.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des énoncés](luis-get-started-php-add-utterance.md)