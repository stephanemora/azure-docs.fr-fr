---
title: Ajouter des instructions conditionnelles aux workflows – Azure Logic Apps | Microsoft Docs
description: Comment créer des conditions qui contrôlent des actions dans le workflow dans Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: d4e69d33e07f484b4ccc5343786865230368c7ca
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096374"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Créer des instructions conditionnelles qui contrôlent des actions de workflow dans Azure Logic Apps

Pour exécuter des actions spécifiques dans votre application logique uniquement après avoir rempli une condition spécifiée, ajoutez une *instruction conditionnelle*. Cette structure compare les données de votre workflow à des valeurs ou à des champs spécifiques. Vous pouvez ensuite définir différentes actions à exécuter selon que les données remplissent ou non la condition. Vous pouvez imbriquer les conditions les unes dans les autres.

Par exemple, supposons que vous disposez d’une application logique qui envoie un trop grand nombre d’e-mails lorsque de nouveaux éléments s’affichent dans le flux RSS d’un site web. Vous pouvez ajouter une instruction conditionnelle pour envoyer un e-mail uniquement lorsque le nouvel élément inclut une chaîne spécifique. 

> [!TIP]
> Pour exécuter différentes étapes en fonction de diverses valeurs spécifiques, utilisez plutôt une [*instruction switch*](../logic-apps/logic-apps-control-flow-switch-statement.md).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [vous inscrire pour obtenir un compte Azure gratuitement](https://azure.microsoft.com/free/).

* Des connaissances de base en [création d’applications logiques](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Pour suivre l’exemple présenté dans cet article, [créez cet exemple d’application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md) avec un compte Outlook.com ou Office 365 Outlook.

## <a name="add-a-condition"></a>Ajouter une condition

1. Dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a>, ouvrez votre application logique dans le Concepteur d’applications logiques.

2. Ajoutez une condition à l’emplacement souhaité. 

   Pour ajouter une condition entre des étapes existantes, déplacez le pointeur sur la flèche où vous voulez ajouter la condition. Cliquez sur le **signe plus** (**+**) qui s’affiche, puis choisissez **Ajouter une condition**. Par exemple : 

   ![Ajouter une condition entre des étapes](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

   Quand vous souhaitez ajouter une condition à la fin de votre flux de travail, accédez au bas de votre application logique et sélectionnez **+ Nouvelle étape** > **Ajouter une condition**.

3. Sous **Condition**, générez votre condition. 

   1. Dans la zone de gauche, spécifiez les données ou le champ que vous souhaitez comparer.

      Lorsque vous cliquez dans la case de gauche, la liste de contenu dynamique s’affiche pour vous permettre de sélectionner les sorties des étapes précédentes dans votre application logique. 
      Pour cet exemple, sélectionnez le résumé du flux RSS.

      ![Générer votre condition](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   2. Dans la liste du milieu, sélectionnez l’opération à effectuer. 
   Dans cet exemple, sélectionnez « **contains** ». 

   3. Dans la zone de droite, spécifiez une valeur ou un champ comme critères à respecter. 
   Pour cet exemple, spécifiez la chaîne suivante : **Microsoft**

   Voici la condition complète :

   ![Condition complète](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

5. Sous **If true** et **If false**, ajoutez les étapes à effectuer si la condition est remplie ou non. Par exemple : 

   ![Condition avec les chemins « If true » et « If false »](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Vous pouvez faire glisser des actions existantes dans les chemins **If true** et **If false**.

6. Enregistrez votre application logique.

Désormais, cette application logique envoie un e-mail uniquement lorsque les nouveaux éléments du flux RSS remplissent la condition que vous avez définie.

## <a name="json-definition"></a>Définition JSON

Maintenant que vous avez créé une application logique avec une instruction conditionnelle, examinons la définition de code de niveau élevé derrière l’instruction conditionnelle.

``` json
"actions": {
  "Condition": {
    "type": "If",
    "actions": {
      "Send_an_email": {
        "inputs": {},
        "runAfter": {}
    },
    "expression": {
      "and": [ 
        { 
          "contains": [ 
            "@triggerBody()?['summary']", "Microsoft"
          ]
        } 
      ]
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des fonctionnalités et suggestions ou pour en soumettre, visitez le [site de commentaires des utilisateurs Azure Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* [Instructions switch : Exécuter différentes étapes en fonction de valeurs spécifiques](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Loops: Process arrays or repeat actions until a condition is met](../logic-apps/logic-apps-control-flow-loops.md) (Boucles : Traiter des tableaux ou répéter des actions jusqu’à ce qu’une condition soit remplie)
* [Create or join parallel branches in your logic app](../logic-apps/logic-apps-control-flow-branches.md) (Créer ou joindre des branches parallèles dans votre application logique)
* [Étendues : Exécuter des étapes en fonction de l’état d’un groupe](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
