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
ms.openlocfilehash: e8d84944d44588602593c762c4f60c375e480343
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298166"
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

3. Sous **Condition**, créez votre condition. 

   1. Dans la zone de gauche, spécifiez les données ou le champ que vous souhaitez comparer.

      Dans la liste **Ajouter du contenu dynamique**, vous pouvez sélectionner des champs existants de votre application logique.

   2. Dans la liste du milieu, sélectionnez l’opération à effectuer. 
   3. Dans la zone de droite, spécifiez une valeur ou un champ comme critères à respecter.

   Par exemple : 

   ![Modifier la condition en mode de base](./media/logic-apps-control-flow-conditional-statement/edit-condition-basic-mode.png)

   Voici la condition complète :

   ![Condition complète](./media/logic-apps-control-flow-conditional-statement/edit-condition-basic-mode-2.png)

   > [!TIP]
   > Pour créer une condition plus avancée ou utiliser des expressions, choisissez **Edit in advanced mode** (Modifier en mode avancé). Vous pouvez utiliser des expressions définies par le [langage de définition de flux de travail](../logic-apps/logic-apps-workflow-definition-language.md).
   > 
   > Par exemple : 
   >
   > ![Modifier la condition dans le code](./media/logic-apps-control-flow-conditional-statement/edit-condition-advanced-mode.png)

5. Sous **SI OUI** et **SI NON**, ajoutez les étapes à effectuer si la condition est remplie ou non. Par exemple : 

   ![Condition avec les chemins SI OUI et SI NON](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Vous pouvez faire glisser des actions existantes dans les chemins **SI OUI** et **SI NON**.

6. Enregistrez votre application logique.

Désormais, cette application logique envoie un e-mail uniquement lorsque les nouveaux éléments du flux RSS remplissent la condition que vous avez définie.

## <a name="json-definition"></a>Définition JSON

Maintenant que vous avez créé une application logique avec une instruction conditionnelle, examinons la définition de code de niveau élevé derrière l’instruction conditionnelle.

``` json
"actions": {
  "myConditionName": {
    "type": "If",
    "expression": "@contains(triggerBody()?['summary'], 'Microsoft')",
    "actions": {
      "Send_an_email": {
        "inputs": { },
        "runAfter": {}
      }
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
