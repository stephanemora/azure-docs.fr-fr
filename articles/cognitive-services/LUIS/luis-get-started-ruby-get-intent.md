---
title: Didacticiel permettant de découvrir comment appeler une application Language Understanding Intelligent Service (LUIS) à l’aide de Ruby | Microsoft Docs
description: Dans ce didacticiel, vous allez apprendre à appeler une application LUIS à l’aide de Ruby.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 683f17df29388e9d645dc813785f1c545c1506dc
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265107"
---
# <a name="tutorial-call-a-luis-endpoint-using-ruby"></a>Didacticiel : Appeler un point de terminaison LUIS à l’aide de Ruby
Transmettez des énoncés à un point de terminaison LUIS et obtenez en retour une intention et des entités.

<!-- green checkmark -->
> [!div class="checklist"]
> * Créer un abonnement LUIS et copier la valeur de la clé pour une utilisation ultérieure
> * Afficher les résultats du point de terminaison LUIS du navigateur dans un exemple d’application IoT publique
> * Créer une application console Visual Studio en C# pour passer un appel HTTPS au point de terminaison LUIS

Pour cet article, vous devez disposer d’un compte [LUIS][LUIS] gratuit afin de créer votre application LUIS.

## <a name="create-luis-subscription-key"></a>Créer une clé d’abonnement LUIS
Vous avez besoin d’une clé API Cognitive Services pour passer des appels à l’exemple d’application LUIS utilisé dans cette procédure pas à pas. 

Pour obtenir une clé API, procédez comme suit : 

1. Vous devez d’abord créer un [compte d’API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) sur le Portail Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

2. Connectez-vous au portail Azure sur https://portal.azure.com. 

3. Suivez les étapes indiquées dans [Create LUIS endpoint key](./luis-how-to-azure-subscription.md) (Créer une clé de point de terminaison LUIS) pour obtenir une clé.

4. Retournez sur le site web [LUIS](luis-reference-regions.md) et connectez-vous avec votre compte Azure. 

    [![](media/luis-get-started-node-get-intent/app-list.png "Capture d’écran de la liste des applications")](media/luis-get-started-node-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>Comprendre les éléments renvoyés par une application LUIS

Pour comprendre ce que renvoie une application LUIS, vous pouvez coller l’URL d’un exemple d’application LUIS dans une fenêtre de navigateur. L’exemple d’application est une application IoT qui détecte si l’utilisateur souhaite allumer ou éteindre la lumière.

1. Le point de terminaison de l’exemple d’application respecte le format suivant : `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light` Copiez l’URL et remplacez votre clé d’abonnement par la valeur du champ `subscription-key`.
2. Collez l’URL dans une fenêtre de navigateur, puis appuyez sur Entrée. Le navigateur affiche un résultat JSON qui indique que l’application LUIS a détecté l’intention `HomeAutomation.TurnOn` et l’entité `HomeAutomation.Room` pourvue de la valeur `bedroom`.

    ![Résultat JSON indiquant la détection de l’intention TurnOn](./media/luis-get-started-node-get-intent/turn-on-bedroom.png)
3. Modifiez la valeur du paramètre `q=` dans l’URL et remplacez-la par `turn off the living room light`, puis appuyez sur Entrée. Le résultat indique maintenant que l’application LUIS a détecté l’intention `HomeAutomation.TurnOff` et l’entité `HomeAutomation.Room` pourvue de la valeur `living room`. 

    ![Résultat JSON indiquant la détection de l’intention TurnOff](./media/luis-get-started-node-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-ruby"></a>Utiliser un résultat LUIS à l’aide de l’API de point de terminaison avec Ruby 

Vous pouvez utiliser Ruby pour accéder aux résultats que vous avez vus dans la fenêtre du navigateur à l’étape précédente. 
1. Copiez le code qui suit et enregistrez-le dans un fichier HTML :

   [!code-ruby[Ruby code that calls a LUIS endpoint](~/samples-luis/documentation-samples/endpoint-api-samples/ruby/endpoint-call.rb)]
2. Remplacez `"YOUR-SUBSCRIPTION-KEY"` par votre clé d’abonnement dans cette ligne de code : `subscriptionKey = "YOUR-SUBSCRIPTION-KEY"`

3. Exécutez l’application Ruby. Des valeurs identiques à celles que vous avez vues plus tôt dans la fenêtre du navigateur s’affichent.

## <a name="clean-up-resources"></a>Supprimer des ressources
Les deux ressources créées dans ce didacticiel sont la clé d’abonnement LUIS et le projet C#. Supprimez la clé d’abonnement LUIS du Portail Azure. Fermez le projet Visual Studio et supprimez le répertoire du système de fichiers. 

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Ajouter des énoncés](luis-get-started-ruby-add-utterance.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website