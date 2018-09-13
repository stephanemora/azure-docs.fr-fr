---
title: Analyser le texte en langage naturel dans l’API Language Understanding (LUIS) à l’aide de Node.js - Cognitive Services - Azure Cognitive Services | Microsoft Docs
description: Dans ce démarrage rapide, utilisez une application LUIS publique disponible pour déterminer l’intention d’un utilisateur à partir du texte conversationnel. À l’aide de Node.js, envoyez l’intention de l’utilisateur sous forme de texte au point de terminaison de prédiction HTTP de l’application publique. Au point de terminaison, LUIS applique le modèle de l’application publique pour analyser le sens du texte en langage naturel, déterminer l’intention globale et extraire les données pertinentes pour le domaine de l’application.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: d7067041ae8e413675de3c95ca4ba0c1b987f47a
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "43769973"
---
# <a name="quickstart-analyze-text-using-nodejs"></a>Démarrage rapide : Analyser le texte à l’aide de Node.js

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Prérequis

* Langage de programmation [Node.js](https://nodejs.org/) 
* [Visual Studio Code](https://code.visualstudio.com/)
* ID d’application publique : df67dcdb-c37d-46af-88e1-8b97951ca1c2


> [!NOTE] 
> La solution Node.js complète est disponible dans le [**référentiel Github** LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/analyze-text/node) (Exemples-LUIS).

## <a name="get-luis-key"></a>Obtenir la clé LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Analyser le texte avec le navigateur

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-nodejs"></a>Analyser le texte avec Node.js

Vous pouvez utiliser Node.js pour accéder aux résultats que vous avez vus dans la fenêtre du navigateur à l’étape précédente.

1. Copiez l’extrait de code suivant :

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/quickstarts/analyze-text/node/call-endpoint.js)]

2. Créez un fichier `.env` avec le texte suivant ou définissez ces variables dans l’environnement système :

    ```CMD
    LUIS_APP_ID=df67dcdb-c37d-46af-88e1-8b97951ca1c2
    LUIS_ENDPOINT_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    ```

3. Définissez la variable d’environnement `LUIS_ENDPOINT_KEY` sur votre clé.

4. Installez les dépendances en exécutant la commande suivante dans la ligne de commande : `npm install`.

5. Exécutez le code avec `npm start`. Il affiche des valeurs identiques à celles que vous avez vues plus tôt dans la fenêtre du navigateur.

## <a name="luis-keys"></a>Clés LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Supprimer les ressources

Supprimez le fichier Node.js.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Ajouter des énoncés](luis-get-started-node-add-utterance.md)