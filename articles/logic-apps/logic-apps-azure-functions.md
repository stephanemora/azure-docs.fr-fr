---
title: Ajouter et exécuter du code personnalisé dans Azure Logic Apps avec Azure Functions | Microsoft Docs
description: Découvrez comment ajouter et exécuter des extraits de code personnalisés dans Azure Logic Apps avec Azure Functions
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 08/20/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: a63bd8e3b071ed996db8ad5aeaeb5e451b4d92e9
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42144835"
---
# <a name="add-and-run-custom-code-snippets-in-azure-logic-apps-with-azure-functions"></a>Ajouter et exécuter des extraits de code personnalisés dans Azure Logic Apps avec Azure Functions

Quand vous souhaitez exécuter uniquement du code qui effectue une tâche spécifique dans vos applications logiques, vous pouvez créer vos propres fonctions avec [Azure Functions](../azure-functions/functions-overview.md). Ce service vous permet de créer des extraits de code Node.js, C# et F# pour ne pas avoir à créer une application complète ou l’infrastructure pour l’exécution de votre code. Azure Functions fournit une informatique sans serveur dans le cloud et est utile pour effectuer des tâches telles que les suivantes :

* Étendre le comportement de votre application logique avec des fonctions dans Node.js ou C#
* Effectuer des calculs dans le workflow de votre application logique
* Appliquer une mise en forme avancée ou calculer des champs dans vos applications logiques

Vous pouvez également [appeler des applications logiques à l’intérieur de fonctions Azure](#call-logic-app).

## <a name="prerequisites"></a>Prérequis

Pour suivre cet article, vous avez besoin de ce qui suit :

* Si vous n’avez pas encore d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. 

* Une application de fonction Azure, qui est un conteneur pour les fonctions Azure, et votre fonction Azure. Si vous n’avez pas d’application de fonction, [créez-la en premier lieu](../azure-functions/functions-create-first-azure-function.md). Vous pouvez ensuite créer votre fonction soit [séparément à l’extérieur de votre application logique](#create-function-external), soit [à partir de l’intérieur de votre application logique](#create-function-designer) dans le Concepteur d’applications logiques

  Les applications de fonction et les fonctions nouvelles et existantes ont les mêmes exigences pour travailler avec les applications logiques :

  * Votre application de fonction doit détenir le même abonnement Azure que votre application logique.

  * Votre fonction utilise un déclencheur HTTP, par exemple, le modèle de fonction **Déclencheur HTTP** pour **JavaScript** ou **C#**. 

    Le modèle de déclencheur HTTP peut accepter du contenu ayant le type `application/json` à partir de votre application logique. 
    Lorsque vous ajoutez une fonction Azure à votre application logique, le Concepteur d’application logique affiche les fonctions personnalisées créées à partir de ce modèle dans votre abonnement Azure. 

  * Votre fonction n’utilise pas d’itinéraires personnalisés, sauf si vous avez défini une [définition OpenAPI](../azure-functions/functions-openapi-definition.md), anciennement appelée [fichier Swagger](http://swagger.io/). 
  
  * Si vous avez défini une définition OpenAPI pour votre fonction, le Concepteur d’application logique vous offre une meilleure expérience pour l’utilisation des paramètres de fonction. Pour que votre application logique puisse trouver les fonctions qui ont des définitions OpenAPI et y accéder, [configurez votre application de fonction en suivant ces étapes](#function-swagger).

* L’application logique dans laquelle vous souhaitez ajouter la fonction, y compris un [déclencheur](../logic-apps/logic-apps-overview.md#logic-app-concepts) comme première étape de votre application logique 

  Pour que vous puissiez ajouter des actions capables d’exécuter des fonctions, votre application logique doit démarrer avec un déclencheur.

  Si vous ne connaissez pas les applications logiques, consultez les sections [Présentation d’Azure Logic Apps](../logic-apps/logic-apps-overview.md) et [Démarrage rapide : créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-function-external"></a>

## <a name="create-functions-outside-logic-apps"></a>Créer des fonctions en dehors des applications logiques

Dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a>, créez votre application de fonction Azure, qui doit avoir le même abonnement Azure que votre application logique, puis créez votre fonction Azure.
Si vous débutez avec la création de fonctions Azure, découvrez comment [créer votre première fonction dans le portail Azure](../azure-functions/functions-create-first-azure-function.md), mais notez ces exigences relatives à la création de fonctions que vous pouvez appeler à partir d’applications logiques :

* Veillez à sélectionner le modèle de fonction **Déclencheur HTTP** pour **JavaScript** ou **C#**.

  ![Déclencheur HTTP - JavaScript ou C#](./media/logic-apps-azure-functions/http-trigger-function.png)

<a name="function-swagger"></a>

* Si vous le souhaitez, si vous [générez une définition d’API](../azure-functions/functions-openapi-definition.md) (anciennement connue sous le nom de [fichier Swagger](http://swagger.io/)) pour votre fonction, vous pouvez bénéficier d’une expérience enrichie quand vous travaillez avec des paramètres de fonction dans le Concepteur d’applications logiques. Pour configurer votre application de fonction afin que votre application logique puisse trouver les fonctions qui ont des descriptions Swagger et y accéder, procédez comme suit :

  1. Vérifiez que votre application de fonction est en cours d’exécution.

  2. Dans votre application de fonction, configurez le [Partage des ressources cross-origin (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) afin que toutes les origines soient autorisées en procédant comme suit :

     1. À partir de la liste **Applications de fonction**, sélectionnez votre application de fonction > **Fonctionnalités de la plateforme** > **CORS**.

        ![Sélectionnez votre application de fonction > « Fonctionnalités de la plateforme » > « CORS »](./media/logic-apps-azure-functions/function-platform-features-cors.png)

     2. Sous **CORS**, ajoutez le caractère générique `*`, mais supprimez toutes les autres origines de la liste, puis choisissez **Enregistrer**.

        ![Définir CORS* sur le caractère générique « * »](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

### <a name="access-property-values-inside-http-requests"></a>Accéder aux valeurs de propriétés à l’intérieur des requêtes HTTP

Les fonctions webhook peuvent accepter des requêtes HTTP en tant qu’entrées, et transmettre ces requêtes à d’autres fonctions. Par exemple, bien que Logic Apps ait des [fonctions qui convertissent les valeurs DateTime](../logic-apps/workflow-definition-language-functions-reference.md), cette exemple de fonction JavaScript de base montre comment accéder à une propriété à l’intérieur d’un objet de requête qui est passé à la fonction, et comment effectuer des opérations sur la valeur de cette propriété. Pour accéder aux propriétés à l’intérieur d’objets, cet exemple utilise l’[opérateur point (.)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors) : 

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Voici ce qui se passe à l’intérieur de cette fonction :

1. La fonction crée une variable `data` et assigne l’objet `body` à l’intérieur de l’objet `request` à cette variable. La fonction utilise l’opérateur point (.) pour référencer l’objet `body` à l’intérieur de l’objet `request` : 

   ```javascript
   var data = request.body;
   ```

2. La fonction peut maintenant accéder à la propriété `date` par le biais de la variable `data`, et convertir cette valeur de propriété du type DateTime vers le type DateString en appelant la fonction `ToDateString()`. La fonction retourne également le résultat par le biais de la propriété `body` dans la réponse de la fonction : 

   ```javascript
   body: data.date.ToDateString();
   ```

Maintenant que vous avez créé votre fonction Azure, suivez les étapes pour découvrir comment [ajouter des fonctions à des applications logiques](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Créer des fonctions au sein d’applications logiques

Avant de pouvoir créer une fonction Azure à partir de l’intérieur de votre application logique dans le Concepteur d’applications logiques, vous devez d’abord disposer d’une application de fonction Azure, qui est un conteneur pour vos fonctions. Si vous n’avez pas d’application de fonction, créez-la en premier lieu. Voir [Créer votre première fonction à l’aide du portail Azure](../azure-functions/functions-create-first-azure-function.md). 

1. Dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a>, ouvrez votre application logique dans le Concepteur d’applications logiques. 

2. Pour créer et ajouter votre fonction, suivez l’étape qui s’applique à votre scénario :

   * Sous la dernière étape du flux de travail de votre application logique, choisissez **Nouvelle étape**.

   * Entre les étapes existantes du flux de travail de votre application logique, déplacez votre souris sur la flèche, cliquez sur le signe plus (+), puis sélectionnez **Ajouter une action**.

3. Dans la zone de recherche, entrez le filtre « azure functions ».
Dans la liste d’actions, sélectionnez cette action : **Choisir une fonction Azure - Azure Functions**. 

   ![Recherchez « Azure Functions »](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Dans la liste d’applications de fonction, sélectionnez la vôtre. Une fois la liste d’actions ouverte, sélectionnez cette action : **Azure Functions - Créer une nouvelle fonction**

   ![Sélectionnez votre application de fonction](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. Dans l’éditeur de définition de fonction, définissez votre fonction :

   1. Dans la zone **Nom de la fonction**, fournissez un nom pour votre fonction. 

   2. Dans la zone **Code**, ajoutez votre code au modèle de fonction, y compris la réponse et la charge utile que vous souhaitez retourner à votre application logique une fois l’exécution de votre fonction terminée. 

      ![Définissez votre fonction](./media/logic-apps-azure-functions/function-definition.png)

      Dans le code du modèle, *`context` l’objet* fait référence au message envoyé par votre application logique via le champ **Corps de la demande** au cours d’une étape ultérieure. 
      Pour accéder aux propriétés de l’objet `context` depuis l’intérieur de votre fonction, utilisez la syntaxe suivante : 

      `context.body.<property-name>`

      Par exemple, pour référencer la propriété `content` à l’intérieur de l’objet `context`, utilisez la syntaxe suivante : 

      `context.body.content`

      Le code du modèle inclut également une variable `input`, qui stocke la valeur du paramètre `data` afin que votre fonction puisse effectuer des opérations sur cette valeur. 
      À l’intérieur des fonctions JavaScript, la variable `data` est également un raccourci pour `context.body`.

      > [!NOTE]
      > La propriété `body` s’applique ici à l’objet `context`. Ce n’est pas la même chose que le jeton **Corps** du résultat d’une action, que vous pouvez également transmettre à votre fonction. 
 
   3. Lorsque vous êtes prêt, choisissez **Créer**.

6. Dans la zone **Corps de la demande**, spécifiez l’entrée de votre fonction, qui doit être au format JSON (JavaScript Objet Notation). 

   Cette entrée est l’*objet de contexte* ou le message que votre application logique envoie à votre fonction. Quand vous cliquez dans le champ **Corps de la demande**, la liste de contenu dynamique s’ouvre afin que vous puissiez sélectionner des jetons pour les sorties issues des étapes précédentes. Cet exemple indique que la charge utile du contexte contient une propriété nommée `content` qui a la valeur du jeton **De** à partir du déclencheur d’e-mail :

   ![Exemple « Corps de la demande » : charge utile d’objet de contexte](./media/logic-apps-azure-functions/function-request-body-example.png)

   Ici, l’objet de contexte n’est pas converti sous forme de chaîne. Le contenu de l’objet est donc ajouté directement à la charge utile JSON. Toutefois, lorsque l’objet de contexte n’est pas un jeton JSON qui renvoie une chaîne, un objet JSON ou un tableau JSON, une erreur se produit. Par conséquent, en utilisant à la place le jeton **Heure de réception**, vous pouvez convertir l’objet de contexte sous forme de chaîne en ajoutant des guillemets doubles :  

   ![Caster un objet en tant que chaîne](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

7. Pour spécifier d’autres détails tels que la méthode à utiliser, les en-têtes de requête ou les paramètres de requête, choisissez **Afficher les options avancées**.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Ajouter des fonctions existantes à des applications logiques

Pour appeler des fonctions Azure existantes à partir de vos applications logiques, vous pouvez ajouter des fonctions Azure comme toute autre action dans le Concepteur d’applications logiques. 

1. Dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a>, ouvrez votre application logique dans le Concepteur d’applications logiques. 

2. Sous l’étape où vous souhaitez ajouter la fonction, choisissez **Nouvelle étape** > **Ajouter une action**. 

3. Dans la zone de recherche, entrez le filtre « azure functions ».
Dans la liste d’actions, sélectionnez cette action : **Choisir une fonction Azure - Azure Functions**. 

   ![Recherchez « Azure Functions »](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Dans la liste d’applications de fonction, sélectionnez la vôtre. Une fois la liste de fonctions affichée, sélectionnez votre fonction. 

   ![Sélectionnez votre application de fonction et votre fonction Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Pour les fonctions qui ont des définitions d’API (description Swagger) et qui sont [configurées pour que votre application logique puisse rechercher ces fonctions et y accéder](#function-swagger), vous pouvez sélectionner **Actions Swagger** :

   ![Sélectionnez votre application de fonction, « Actions Swagger » et votre fonction Azure](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

5. Dans la zone **Corps de la demande**, spécifiez l’entrée de votre fonction, qui doit être au format JSON (JavaScript Objet Notation). 

   Cette entrée est l’*objet de contexte* ou le message que votre application logique envoie à votre fonction. Quand vous cliquez dans le champ **Corps de la demande**, la liste de contenu dynamique s’ouvre afin que vous puissiez sélectionner des jetons pour les sorties issues des étapes précédentes. Cet exemple indique que la charge utile du contexte contient une propriété nommée `content` qui a la valeur du jeton **De** à partir du déclencheur d’e-mail :

   ![Exemple « Corps de la demande » : charge utile d’objet de contexte](./media/logic-apps-azure-functions/function-request-body-example.png)

   Ici, l’objet de contexte n’est pas converti sous forme de chaîne. Le contenu de l’objet est donc ajouté directement à la charge utile JSON. Toutefois, lorsque l’objet de contexte n’est pas un jeton JSON qui renvoie une chaîne, un objet JSON ou un tableau JSON, une erreur se produit. Par conséquent, en utilisant à la place le jeton **Heure de réception**, vous pouvez convertir l’objet de contexte sous forme de chaîne en ajoutant des guillemets doubles : 

   ![Caster un objet en tant que chaîne](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. Pour spécifier d’autres détails tels que la méthode à utiliser, les en-têtes de requête ou les paramètres de requête, choisissez **Afficher les options avancées**.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Appeler des applications logiques à partir de fonctions

Pour déclencher une application logique à partir d’une fonction Azure, l’application logique doit démarrer avec un déclencheur fournissant un point de terminaison pouvant être appelé. Par exemple, vous pouvez démarrer l’application logique avec le déclencheur **HTTP**, **Requête**, **Files d’attente Azure** ou **Event Grid**. À partir de l’intérieur de votre fonction, envoyez une requête HTTP POST à l’URL du déclencheur et incluez la charge utile que cette application logique doit traiter. Pour plus d’informations, consultez [Appeler, déclencher ou imbriquer des applications logiques](../logic-apps/logic-apps-http-endpoint.md). 

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [connecteurs Logic Apps](../connectors/apis-list.md)
