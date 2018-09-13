---
title: Démarrage rapide permettant de découvrir comment appeler une application Language Understanding Intelligent Service (LUIS) à l’aide de Python | Microsoft Docs
description: Dans ce démarrage rapide, vous allez apprendre à appeler une application LUIS à l’aide de Python.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 06/27/2018
ms.author: diberry
ms.openlocfilehash: bc7ae912d762a98c34b9a1b2d6a82d5630c4794b
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "43770005"
---
# <a name="quickstart-call-a-luis-endpoint-using-python"></a>Démarrage rapide : Appeler un point de terminaison LUIS à l’aide de Python
Dans ce démarrage rapide, vous allez transmettre des énoncés à un point de terminaison LUIS et obtenir en retour une intention et des entités.

<!-- green checkmark -->
<!--
> [!div class="checklist"]
> * Create LUIS subscription and copy key value for later use
> * View LUIS endpoint results from browser to public sample IoT app
> * Create Visual Studio C# console app to make HTTPS call to LUIS endpoint
-->

Pour cet article, vous devez disposer d’un compte [LUIS](luis-reference-regions.md#luis-website) gratuit afin de créer votre application LUIS.

<a name="create-luis-subscription-key"></a>
## <a name="create-luis-endpoint-key"></a>Créer une clé de point de terminaison LUIS
Vous avez besoin d’une clé API Cognitive Services pour passer des appels à l’exemple d’application LUIS utilisé dans cette procédure pas à pas. 

Pour obtenir une clé API, procédez comme suit : 

1. Vous devez d’abord créer un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) sur le Portail Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

2. Connectez-vous au portail Azure sur https://portal.azure.com. 

3. Suivez les étapes indiquées dans [Creating Endpoint Keys using Azure](./luis-how-to-azure-subscription.md) (Créer des clés de point de terminaison à l’aide d’Azure) pour obtenir une clé.

4. Retournez sur le site web [LUIS](luis-reference-regions.md) et connectez-vous avec votre compte Azure. 

    [![](media/luis-get-started-node-get-intent/app-list.png "Capture d’écran de la liste des applications")](media/luis-get-started-node-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>Comprendre les éléments renvoyés par une application LUIS

Pour comprendre ce que renvoie une application LUIS, vous pouvez coller l’URL d’un exemple d’application LUIS dans une fenêtre de navigateur. L’exemple d’application est une application IoT qui détecte si l’utilisateur souhaite allumer ou éteindre la lumière.

1. Le point de terminaison de l’exemple d’application respecte le format suivant : `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light` Copiez l’URL et remplacez votre clé de point de terminaison par la valeur du champ `subscription-key`.
2. Collez l’URL dans une fenêtre de navigateur, puis appuyez sur Entrée. Le navigateur affiche un résultat JSON qui indique que l’application LUIS a détecté l’intention `HomeAutomation.TurnOn` et l’entité `HomeAutomation.Room` pourvue de la valeur `bedroom`.

    ![Résultat JSON indiquant la détection de l’intention TurnOn](./media/luis-get-started-node-get-intent/turn-on-bedroom.png)
3. Modifiez la valeur du paramètre `q=` dans l’URL et remplacez-la par `turn off the living room light`, puis appuyez sur Entrée. Le résultat indique maintenant que l’application LUIS a détecté l’intention `HomeAutomation.TurnOff` et l’entité `HomeAutomation.Room` pourvue de la valeur `living room`. 

    ![Résultat JSON indiquant la détection de l’intention TurnOff](./media/luis-get-started-node-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-python"></a>Utiliser un résultat LUIS à l’aide de l’API de point de terminaison avec Python

Vous pouvez utiliser Python pour accéder aux résultats que vous avez vus dans la fenêtre du navigateur à l’étape précédente.

1. Copiez l’un des extraits de code suivants dans un fichier nommé `quickstart-call-endpoint.py` :

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. Remplacez la valeur du champ `Ocp-Apim-Subscription-Key` par votre clé de point de terminaison LUIS.

3. Installez les dépendances avec `pip install requests`.

4. Exécutez le script avec `python ./quickstart-call-endpoint.py`. Des valeurs identiques à celles que vous avez vues plus tôt dans la fenêtre du navigateur s’affichent.
<!-- 
![Console window displays JSON result from LUIS](./media/luis-get-started-python-get-intent/console-turn-on.png)
-->

## <a name="clean-up-resources"></a>Supprimer les ressources
Les deux ressources créées dans ce didacticiel sont la clé de point de terminaison LUIS et le projet C#. Supprimez la clé de point de terminaison LUIS du portail Azure. Fermez le projet Visual Studio et supprimez le répertoire du système de fichiers. 

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Ajouter des énoncés](luis-get-started-python-add-utterance.md)