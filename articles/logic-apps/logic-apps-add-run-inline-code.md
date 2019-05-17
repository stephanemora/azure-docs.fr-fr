---
title: Ajouter et exécuter des extraits de code - Azure Logic Apps
description: Ajouter et exécuter des extraits de code avec le code inline dans Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: derek1ee, LADocs
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: 0bfa98396ee3afb80b486a5a17959664dfbe603c
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602123"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Ajouter et exécuter des extraits de code à l’aide de code inline dans Azure Logic Apps

Lorsque vous souhaitez exécuter un morceau de code à l’intérieur de votre application logique, vous pouvez ajouter intégrés **Inline Code** action sous la forme d’une étape dans le flux de travail de votre application logique. Cette action fonctionne mieux lorsque vous souhaitez exécuter du code qui correspond à ce scénario :

* S’exécute dans JavaScript. Davantage de langues sera bientôt disponible.
* Fin de son exécution dans les cinq secondes ou moins.
* Gère les données jusqu'à 50 Mo.
* Utilise Node.js version 8.11.1. Pour plus d’informations, consultez [les objets prédéfinis Standard](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects). 

  > [!NOTE]
  > La fonction require() n’est pas pris en charge par le **Inline Code** action pour l’exécution de JavaScript.

Cette action s’exécute l’extrait de code et retourne la sortie à partir de cet extrait de code sous la forme d’un jeton nommé **résultat**, que vous pouvez utiliser dans les actions suivantes dans votre application logique. Pour d’autres scénarios où vous souhaitez créer une fonction de votre code, essayez [créant et en appelant une fonction Azure](../logic-apps/logic-apps-azure-functions.md) dans votre application logique.

Dans cet article, les déclencheurs d’application logique exemple lorsqu’un nouvel e-mail arrive dans un compte Office 365 Outlook. L’extrait de code extrait et retourne toutes les adresses de messagerie qui s’affichent dans le corps du message.

![Vue d’ensemble de l’exemple](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, [inscrivez-vous pour bénéficier d’un compte Azure gratuit](https://azure.microsoft.com/free/).

* L’application logique dans lequel vous souhaitez ajouter votre extrait de code, y compris un déclencheur. Si vous n’avez pas une application logique, consultez [Guide de démarrage rapide : Créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   L’exemple d’application logique dans cette rubrique utilise ce déclencheur Outlook Office 365 : **When a new email arrives** (Quand un nouveau courrier électronique arrive)

* Un [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) qui est lié à votre application logique

## <a name="add-inline-code"></a>Ajoutez du code incorporé

1. Si vous n’avez pas déjà fait, dans le [Azure portal](https://portal.azure.com), ouvrez votre application logique dans le Concepteur d’application logique.

1. Dans le concepteur, ajoutez le **Inline Code** action à l’emplacement souhaité dans le flux de travail de votre application logique.

   * Pour ajouter l’action à la fin de votre flux de travail, choisissez **nouvelle étape**.

   * Pour ajouter l’action entre des étapes existantes, déplacez votre pointeur de la souris sur la flèche qui connecte ces étapes. Choisissez le signe plus (**+**), puis sélectionnez **ajouter une action**.

   Cet exemple ajoute le **Inline Code** action sous le déclencheur Office 365 Outlook.

   ![Ajouter une nouvelle étape](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. Sous **choisir une action**, dans la zone de recherche, entrez « code inline » comme filtre. Dans la liste des actions, sélectionnez cette action : **Exécuter du Code JavaScript**

   ![Sélectionnez « Exécuter le Code JavaScript »](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   L’action s’affiche dans le concepteur et contient un exemple de code par défaut, y compris une instruction return.

   ![Action de inline Code avec l’exemple de code par défaut](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. Dans le **Code** boîte, supprimer l’exemple de code et entrez le code que vous souhaitez exécuter. Écrire du code, vous placeriez à l’intérieur d’une méthode, mais sans définir la signature de méthode. 

   Lorsque vous tapez un mot clé reconnu, la liste de saisie semi-automatique s’affiche afin que vous puissiez sélectionner des mots clés disponibles, par exemple :

   ![Liste de saisie semi-automatique de mots clés](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Cet extrait de code d’exemple crée d’abord une variable qui stocke un *expression régulière*, qui spécifie un modèle à faire correspondre dans le texte d’entrée. Le code crée ensuite une variable qui stocke les données de corps de courrier électronique à partir du déclencheur.

   ![Créer des variables](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Pour faciliter les résultats à partir du déclencheur et les actions précédentes référence, la liste de contenu dynamique s’affiche pendant que votre curseur se trouve dans le **Code** boîte. Pour cet exemple, la liste affiche les résultats disponibles à partir du déclencheur, y compris le **corps** jeton, que vous pouvez maintenant sélectionner.

   Après avoir sélectionné le **corps** jeton, l’action de code inline résout le jeton à un `workflowContext` objet qui fait référence à la messagerie `Body` valeur de propriété :

   ![Résultat de la sélection](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   Dans le **Code** boîte, votre extrait de code peut utiliser en lecture seule `workflowContext` de l’objet en tant qu’entrée. Cet objet possède des sous-propriétés qui permettent à votre code d’accéder aux résultats à partir du déclencheur et les actions précédentes dans votre flux de travail.
   Pour plus d’informations, consultez cette section plus loin dans cette rubrique : [Référencer les résultats de déclencheur et action dans votre code](#workflowcontext).

   > [!NOTE]
   >
   > Si votre extrait de code fait référence à des noms d’action qui utilisent l’opérateur point (.), vous devez ajouter ces noms d’action à la [ **Actions** paramètre](#add-parameters). Ces références doivent également mettre entre les noms d’action avec des crochets ([]) et les guillemets, par exemple :
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   L’action de code inline ne nécessite pas un `return` instruction, mais les résultats à partir d’un `return` instruction sont disponibles pour la référence dans les actions ultérieures via la **résultat** jeton. 
   Par exemple, l’extrait de code retourne le résultat en appelant le `match()` fonction, qui recherche correspond à dans le corps du message par rapport à l’expression régulière. Le **Compose** action utilise le **résultat** jetons pour référencer les résultats à partir de la ligne action de code et crée un résultat unique.

   ![Application logique terminée](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Lorsque vous avez terminé, enregistrez votre application logique.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Résultats de déclencheur et action de référence dans votre code

Le `workflowContext` objet présente la structure suivante, qui inclut le `actions`, `trigger`, et `workflow` sous-propriétés :

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

Cette table contient plus d’informations sur ces sous-propriétés :

| Propriété | Type | Description  |
|----------|------|-------|
| `actions` | Collection d’objets | Objets de résultats à partir d’actions qui s’exécutent avant l’exécution de votre extrait de code. Chaque objet possède un *clé-valeur* paire où la clé est le nom d’une action, et la valeur équivaut à appeler le [actions() fonction](../logic-apps/workflow-definition-language-functions-reference.md#actions) avec `@actions('<action-name>')`. Nom de l’action utilise le même nom d’action qui est utilisé dans la définition de flux de travail sous-jacent, qui remplace les espaces (« ») dans le nom d’action avec des traits de soulignement (_). Cet objet permet d’accéder aux valeurs de propriété d’action de l’instance de workflow en cours exécution. |
| `trigger` | Object | Objet de résultat à partir du déclencheur et l’équivalent à l’appel le [trigger() fonction](../logic-apps/workflow-definition-language-functions-reference.md#trigger). Cet objet permet d’accéder aux valeurs de propriété de déclencheur de l’instance de workflow en cours exécution. |
| `workflow` | Object | L’objet de flux de travail et l’équivalent à l’appel le [workflow() fonction](../logic-apps/workflow-definition-language-functions-reference.md#workflow). Cet objet permet d’accéder aux valeurs de propriété de flux de travail, telles que le nom de flux de travail, ID d’exécution et ainsi de suite, à partir de l’instance de workflow en cours exécution. |
|||

Dans l’exemple de cette rubrique, le `workflowContext` objet a les propriétés suivantes qui permettre accéder à votre code :

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>Ajouter des paramètres

Dans certains cas, vous devrez peut-être explicitement exigent que le **Inline Code** action comprend les résultats à partir du déclencheur ou des actions spécifiques qui fait référence à votre code en tant que dépendances en ajoutant le **déclencheur** ou **Actions** paramètres. Cette option est utile pour les scénarios où les résultats référencés ne sont pas trouvés au moment de l’exécution.

> [!TIP]
> Si vous projetez de réutiliser votre code, ajoutez des références aux propriétés à l’aide de la **Code** case pour que votre code inclut les références de jeton résolus, plutôt que d’ajouter le déclencheur ou les actions en tant que dépendances explicites.

Par exemple, supposons que vous avez le code qui référence le **SelectedOption** résultent de la **envoyer un e-mail d’approbation** action pour le connecteur Office 365 Outlook. Lors de la création, le moteur Logic Apps analyse votre code pour déterminer si vous avez référencé un déclencheur ou action génère et inclut automatiquement de ces résultats. Au moment de l’exécution devez vous obtenez une erreur que le résultat d’action ou déclencheur référencé n’est pas disponible dans le texte spécifié `workflowContext` de l’objet, vous pouvez ajouter ce déclencheur ou l’action comme une dépendance explicite. Dans cet exemple, vous allez ajouter le **Actions** paramètre et spécifier que le **Inline Code** action inclure explicitement le résultat à partir de la **envoyer un e-mail d’approbation** action.

Pour ajouter ces paramètres, ouvrez le **ajouter un nouveau paramètre** liste, puis sélectionnez les paramètres que vous souhaitez :

   ![Ajouter des paramètres](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Paramètre | Description  |
   |-----------|-------------|
   | **Actions** | Inclure les résultats des actions précédentes. Consultez [incluent les résultats d’action](#action-results). |
   | **Déclencheur** | Inclure les résultats à partir du déclencheur. Consultez [inclure les résultats de déclencheur](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Inclure les résultats de déclencheur

Si vous sélectionnez **déclencheurs**, vous êtes invité pour inclure de déclencheur des résultats.

* À partir de la **déclencheur** liste, sélectionnez **Oui**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Inclure les résultats d’action

Si vous sélectionnez **Actions**, vous êtes invité pour les actions que vous souhaitez ajouter. Toutefois, avant de commencer à ajouter des actions, vous avez besoin de la version du nom d’action qui s’affiche dans la définition de flux de travail de l’application logique sous-jacente.

* Cette fonctionnalité ne prend pas en charge les variables, des boucles et des index de l’itération.

* Les noms dans la définition de flux de travail de votre application logique utiliser un trait de soulignement (_), pas un espace.

* Pour les noms d’action qui utilisent l’opérateur point (.), incluez ces opérateurs, par exemple :

  `My.Action.Name`

1. Dans la barre d’outils Concepteur, choisissez **mode Code**et de recherche à l’intérieur de la `actions` attribut pour le nom d’action.

   Par exemple, `Send_approval_email_` est le nom JSON pour le **envoyer un e-mail d’approbation** action.

   ![Rechercher le nom de l’action dans JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Pour revenir au mode concepteur, la barre d’outils de la vue de code, choisissez **concepteur**.

1. Pour ajouter la première action dans le **élément Actions - 1** , entrez le nom JSON de l’action.

   ![Entrez la première action](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Pour ajouter une autre action, choisissez **ajouter un nouvel élément**.

## <a name="reference"></a>Référence

Pour plus d’informations sur la **exécuter le JavaScript Code** l’action de la structure et syntaxe dans la définition de votre application logique sous-jacente du flux de travail en utilisant le langage de définition de flux de travail, reportez-vous à la section de cette action [section de référence ](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [connecteurs pour Azure Logic Apps](../connectors/apis-list.md)
