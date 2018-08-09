---
title: Ajouter et exécuter du code personnalisé dans Azure Logic Apps avec Azure Functions | Microsoft Docs
description: Découvrez comment ajouter et exécuter des extraits de code personnalisés dans Azure Logic Apps avec Azure Functions
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/25/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 20ad738541554279ff9fd6dd6babe90a38676c00
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263188"
---
# <a name="add-and-run-custom-code-snippets-in-azure-logic-apps-with-azure-functions"></a>Ajouter et exécuter des extraits de code personnalisés dans Azure Logic Apps avec Azure Functions

Quand vous souhaitez créer et exécuter uniquement du code qui résout un problème spécifique dans vos applications logiques, vous pouvez créer vos propres fonctions à l’aide d’[Azure Functions](../azure-functions/functions-overview.md). Ce service permet de créer et d’exécuter des extraits de code personnalisés écrits avec Node.js ou C# dans vos applications logiques sans avoir à vous soucier de la création d’une application entière ou de l’infrastructure pour l’exécution de votre code. Azure Functions fournit une informatique sans serveur dans le cloud et est utile pour effectuer des tâches telles que les suivantes :

* Étendre le comportement de votre application logique avec des fonctions prises en charge par Node.js ou C#
* Effectuer des calculs dans le workflow de votre application logique
* Appliquer une mise en forme avancée ou calculer des champs dans vos applications logiques

Vous pouvez également [appeler des applications logiques à l’intérieur de fonctions Azure](#call-logic-app).

## <a name="prerequisites"></a>Prérequis

Pour suivre cet article, voici les éléments que vous avez besoin :

* Si vous n’avez pas encore d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a> 

* L’application logique dans laquelle vous souhaitez ajouter la fonction

  Si vous ne connaissez pas les applications logiques, consultez les sections [Présentation d’Azure Logic Apps](../logic-apps/logic-apps-overview.md) et [Démarrage rapide : créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un [déclencher](../logic-apps/logic-apps-overview.md#logic-app-concepts) en tant que première étape de votre application logique 

  Pour que vous puissiez ajouter des actions pour l’exécution de fonctions, votre application logique doit démarrer avec un déclencheur.

* Une application de fonction Azure, qui est un conteneur pour les fonctions Azure, et votre fonction Azure. Si vous n’avez pas d’application de fonction, vous devez [d’abord créer votre application de fonction](../azure-functions/functions-create-first-azure-function.md). Vous pouvez ensuite créer votre fonction soit [séparément à l’extérieur de votre application logique](#create-function-external), soit [à partir de l’intérieur de votre application logique](#create-function-designer) dans le Concepteur d’applications logiques

  Les applications de fonction et les fonctions Azure nouvelles et existantes ont les mêmes exigences pour travailler avec vos applications logiques :

  * Votre application de fonction doit appartenir au même abonnement Azure que votre application logique

  * Votre fonction doit utiliser le modèle de fonction **webhook générique** pour **JavaScript** ou **C#**. Ce modèle peut accepter du contenu ayant le type `application/json` à partir de votre application logique. Ces modèles aident également le Concepteur d’applications logiques à rechercher et à afficher les fonctions personnalisées que vous créez avec ces modèles quand vous ajoutez ces fonctions à vos applications logiques

  * Vérifiez que la propriété **Mode** de votre modèle de fonction a la valeur **Webhook** et que la propriété **Type de webhook** a la valeur **JSON générique**

    1. Connectez-vous au <a href="https://portal.azure.com" target="_blank">Portail Azure</a>.
    2. Dans le menu Azure principal, sélectionnez **Applications de fonctions**. 
    3. Dans la liste **Applications de fonctions**, sélectionnez votre application de fonction, développez votre fonction, puis sélectionnez **Intégrer**. 
    4. Vérifiez que la propriété **Mode** de votre modèle a la valeur **Webhook** et que la propriété **Type de webhook** a la valeur **JSON générique** 

  * Si votre fonction a une [définition d’API](../azure-functions/functions-openapi-definition.md), anciennement connue sous le nom de [fichier Swagger](http://swagger.io/), le Concepteur d’applications logiques offre une expérience enrichie pour travailler avec des paramètres de fonction. 
  Pour que votre application logique puisse trouver et accéder aux fonctions qui ont des descriptions Swagger, vous devez [configurer votre application de fonction en suivant ces étapes](#function-swagger)

<a name="create-function-external"></a>

## <a name="create-functions-outside-logic-apps"></a>Créer des fonctions en dehors des applications logiques

Dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a>, créez votre application de fonction Azure, qui doit avoir le même abonnement Azure que votre application logique, puis créez votre fonction Azure. Si vous débutez avec Azure Functions, découvrez comment [créer votre première fonction dans le portail Azure](../azure-functions/functions-create-first-azure-function.md), mais notez ces exigences relatives à la création de fonctions Azure que vous pouvez ajouter et appeler à partir d’applications logiques.

* Veillez à sélectionner le modèle de fonction **webhook générique** pour **JavaScript** ou **C#**.

  ![Webhook générique - JavaScript ou C#](./media/logic-apps-azure-functions/generic-webhook.png)

* Après avoir créé votre fonction Azure, vérifiez que les propriétés **Mode** et **Type de webhook** du modèle sont définies correctement.

  1. Dans la liste **Applications de fonctions**, développez votre fonction et sélectionnez **Intégrer**. 

  2. Vérifiez que la propriété **Mode** de votre modèle a la valeur **Webhook** et que la propriété **Type de webhook** a la valeur **JSON générique** 

     ![Propriétés « Intégrer » de votre modèle de fonction](./media/logic-apps-azure-functions/function-integrate-properties.png)

<a name="function-swagger"></a>

* Si vous le souhaitez, si vous [générez une définition d’API](../azure-functions/functions-openapi-definition.md) (anciennement connue sous le nom de [fichier Swagger](http://swagger.io/)) pour votre fonction, vous pouvez bénéficier d’une expérience enrichie quand vous travaillez avec des paramètres de fonction dans le Concepteur d’applications logiques. Pour configurer votre application de fonction afin que votre application logique puisse trouver et accéder aux fonctions qui ont des descriptions Swagger :

  * Vérifiez que votre application de fonction est en cours d’exécution.

  * Dans votre application de fonction, configurez le [Partage des ressources cross-origin (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) afin que toutes les origines soient autorisées :

    1. À partir de la liste **Applications de fonction**, sélectionnez votre application de fonction > **Fonctionnalités de la plateforme** > **CORS**.

       ![Sélectionnez votre application de fonction > « Fonctionnalités de la plateforme » > « CORS »](./media/logic-apps-azure-functions/function-platform-features-cors.png)

    2. Sous **CORS**, ajoutez le caractère générique `*`, mais supprimez toutes les autres origines de la liste, puis choisissez **Enregistrer**.

       ![Sélectionnez votre application de fonction > « Fonctionnalités de la plateforme » > « CORS »](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

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

2. Sous l’étape où vous souhaitez créer et ajouter la fonction, choisissez **Nouvelle étape** > **Ajouter une action**. 

3. Dans la zone de recherche, entrez le filtre « azure functions ».
Dans la liste d’actions, sélectionnez cette action : **Choisir une fonction Azure - Azure Functions**. 

   ![Recherchez « Azure Functions »](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Dans la liste d’applications de fonction, sélectionnez la vôtre. Une fois la liste d’actions ouverte, sélectionnez cette action : **Azure Functions - Créer une nouvelle fonction**

   ![Sélectionnez votre application de fonction](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. Dans l’éditeur de définition de fonction, définissez votre fonction :

   1. Dans la zone **Nom de la fonction**, fournissez un nom pour votre fonction. 

   2. Dans la zone **Code**, ajoutez votre code de fonction au modèle, y compris la réponse et la charge utile que vous souhaitez retourner à votre application logique une fois l’exécution de votre fonction terminée. 
   L’objet de contexte dans le code du modèle décrit le message et le contenu passés par votre application logique à votre fonction, par exemple :

      ![Définissez votre fonction](./media/logic-apps-azure-functions/function-definition.png)

      À l’intérieur de votre fonction, vous pouvez référencer les propriétés dans l’objet de contexte à l’aide de cette syntaxe :

      ```text
      context.<token-name>.<property-name>
      ```
      Pour cet exemple, voici la syntaxe que vous utiliseriez :

      ```text
      context.body.content
      ```

   3. Lorsque vous êtes prêt, choisissez **Créer**.

6. Dans la zone **Corps de la demande**, spécifiez l’objet de contexte (au format JSON [JavaScript Objet Notation]) à transmettre comme entrée de votre fonction. Quand vous cliquez dans la zone **Corps de la demande**, la liste de contenu dynamique s’ouvre afin que vous puissiez sélectionner des jetons pour les propriétés disponibles suite aux étapes précédentes. 

   Cet exemple passe l’objet dans le jeton **Corps** à partir du déclencheur d’e-mail :  

   ![Exemple « Corps de la demande » : charge utile d’objet de contexte](./media/logic-apps-azure-functions/function-request-body-example.png)

   En fonction du contenu dans l’objet de contexte, le Concepteur d’applications logiques génère un modèle de fonction que vous pouvez ensuite modifier inline. 
   Logic Apps crée également des variables en fonction de l’objet de contexte d’entrée.

   Dans cet exemple, l’objet de contexte n’est pas converti sous forme de chaîne. Le contenu est donc ajouté directement à la charge utile JSON. 
   Toutefois, si l’objet n’est pas un jeton JSON (qui doit être une chaîne, un objet JSON ou un tableau JSON), une erreur se produit. 
   Pour effectuer un cast de l’objet de contexte sous forme de chaîne, ajoutez des guillemets doubles, par exemple :

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

5. Dans la zone **Corps de la demande**, spécifiez l’objet de contexte (au format JSON [JavaScript Objet Notation]) à transmettre comme entrée de votre fonction. Cet objet de contexte décrit le message et le contenu que votre application logique envoie à votre fonction. 

   Quand vous cliquez dans la zone **Corps de la demande**, la liste de contenu dynamique s’ouvre afin que vous puissiez sélectionner des jetons pour les propriétés disponibles suite aux étapes précédentes. 
   Cet exemple passe l’objet dans le jeton **Corps** à partir du déclencheur d’e-mail :

   ![Exemple « Corps de la demande » : charge utile d’objet de contexte](./media/logic-apps-azure-functions/function-request-body-example.png)

   Dans cet exemple, l’objet de contexte n’est pas converti sous forme de chaîne. Le contenu est donc ajouté directement à la charge utile JSON. 
   Toutefois, si l’objet n’est pas un jeton JSON (qui doit être une chaîne, un objet JSON ou un tableau JSON), une erreur se produit. 
   Pour effectuer un cast de l’objet de contexte sous forme de chaîne, ajoutez des guillemets doubles, par exemple :

   ![Caster un objet en tant que chaîne](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. Pour spécifier d’autres détails tels que la méthode à utiliser, les en-têtes de requête ou les paramètres de requête, choisissez **Afficher les options avancées**.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Appeler des applications logiques à partir de fonctions

Pour déclencher une application logique à partir d’une fonction Azure, cette application logique doit avoir un point de terminaison pouvant être appelé, ou plus précisément un déclencheur **Request**. Ensuite, à partir de l’intérieur de votre fonction, envoyez une requête HTTP POST à l’URL pour ce déclencheur **Request** et incluez la charge utile que cette application logique doit traiter. Pour plus d’informations, consultez [Appeler, déclencher ou imbriquer des applications logiques](../logic-apps/logic-apps-http-endpoint.md). 

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [connecteurs Logic Apps](../connectors/apis-list.md)
