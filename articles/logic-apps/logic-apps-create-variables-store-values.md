---
title: Créer des variables pour enregistrer des valeurs - Azure Logic Apps | Microsoft Docs
description: Comment enregistrer et gérer des valeurs en créant des variables dans Azure Logic Apps
services: logic-apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.topic: article
ms.date: 05/30/2018
ms.service: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: c0f2802bae366637fd93d47e33619746b7142f53
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231625"
---
# <a name="create-variables-for-saving-and-managing-values-in-azure-logic-apps"></a>Créer des variables pour l’enregistrement et la gestion de valeurs dans Azure Logic Apps

Cet article explique comment stocker et utiliser des valeurs dans l’ensemble de votre application logique en créant des variables. Des variables peuvent, par exemple, vous aider à compter le nombre d’exécution d’une boucle. En cas d’itération sur un tableau ou de vérification d’un tableau pour un élément spécifique, vous pouvez utiliser une variable pour référencer le numéro d’index de chaque élément du tableau. 

Vous pouvez créer des variables pour des types de données tels que entier, flottant, booléen, chaîne, tableau et objet. Après avoir créé une variable, vous pouvez effectuer d’autres tâches, par exemple :

* Obtenir ou référencer la valeur de la variable.
* Augmenter ou réduire la variable d’une valeur constante, opérations également appelées *incrémenter* et *décrémenter*.
* Affecter une valeur différente à la variable.
* Insérer, ou *ajouter*, la valeur de la variable à la fin d’une chaîne ou d’un tableau.

Les variables existent et sont globales uniquement au sein de l’instance d’application logique qui les crée. En outre, elles sont conservées dans toutes les itérations de boucle à l’intérieur d’une instance d’application logique. Lorsque vous référencez une variable, utilisez le nom de celle-ci en tant que jeton, pas le nom de l’action qui est la façon habituelle de référencer les résultats d’une action.

Si vous n’avez pas encore d’abonnement Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscrivez-vous pour bénéficier d’un compte Azure gratuit</a>. 

## <a name="prerequisites"></a>Prérequis

Pour suivre cet article, voici les éléments que vous avez besoin :

* L’application logique dans laquelle vous souhaitez créer une variable 

  Si vous ne connaissez pas les applications logiques, consultez les sections [Présentation d’Azure Logic Apps](../logic-apps/logic-apps-overview.md) et [Démarrage rapide : créer votre première application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un [déclencher](../logic-apps/logic-apps-overview.md#logic-app-concepts) en tant que première étape de votre application logique 

  Pour que vous puissiez ajouter des actions pour la création et l’utilisation de variables, votre application logique doit démarrer avec un déclencheur.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Initialiser la variable

Vous pouvez créer une variable et déclarer son type de données et sa valeur initiale, le tout au sein d’une seule action dans votre application logique. Vous ne pouvez déclarer des variables qu’au niveau global, pas dans des étendues, des conditions ou des boucles. 

1. Dans le <a href="https://portal.azure.com" target="_blank">portail Azure</a> ou dans Visual Studio, ouvrez votre application logique dans le Concepteur d’application logique. 

   Cet exemple utilise le portail Azure et une application logique avec un déclencheur existant.

2. Dans votre application logique, à l’étape où vous souhaitez ajouter une variable, effectuez l’une des opérations suivantes : 

   * Pour ajouter une action à la dernière étape, choisissez **Nouvelle étape** > **Ajouter une action**.

     ![Ajouter une action](./media/logic-apps-create-variables-store-values/add-action.png)

   * Pour ajouter une action entre des étapes, positionnez votre souris sur la flèche de connexion de façon à ce que le signe plus (+) s’affiche. 
   Cliquez sur le signe plus, puis choisissez **Ajouter une action**.

3. Dans la zone de recherche, entrez « variables » en tant que filtre. Dans la liste d’actions, sélectionnez **Variables - Initialiser la variable**.

   ![Action select](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

4. Fournissez les informations suivantes pour votre variable :

   | Propriété | Obligatoire | Valeur |  Description |
   |----------|----------|-------|--------------|
   | NOM | Oui | <*variable-name*> | Nom de la variable à incrémenter | 
   | type | Oui | <*variable-type*> | Type de données de la variable. | 
   | Valeur | Non  | <*start-value*> | Valeur initiale de votre variable. <p><p>**Conseil** : bien que cette valeur soit facultative, il est recommandé de la définir afin de toujours savoir la valeur initiale de votre variable. | 
   ||||| 

   ![Initialiser la variable](./media/logic-apps-create-variables-store-values/initialize-variable.png)

5. À présent, continuez à ajouter les actions de votre choix. Une fois terminé, dans la barre d’outils du concepteur, choisissez **Enregistrer**.

Si vous passez du concepteur à l’éditeur en mode Code, voici comment l’action **Initialiser la variable** s’affiche à l’intérieur de la définition de votre application logique, qui est au format JavaScript Objet Notation (JSON) :

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   }
},
```

Voici quelques exemples pour d’autres types de variables :

*Variable de chaîne*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myStringVariable",
               "type": "String",
               "value": "lorem ipsum"
          } ]
      },
      "runAfter": {}
   }
},
```

*Variable booléenne*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myBooleanVariable",
               "type": "Boolean",
               "value": false
          } ]
      },
      "runAfter": {}
   }
},
```

*Tableau d’entiers*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": [1, 2, 3]
          } ]
      },
      "runAfter": {}
   }
},
```

*Tableau de chaînes*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": ["red", "orange", "yellow"]
          } ]
      },
      "runAfter": {}
   }
},
```

<a name="get-value"></a>

## <a name="get-the-variables-value"></a>Obtenir la valeur de la variable

Pour extraire ou référencer le contenu d’une variable, vous pouvez également utiliser la [fonction variables()](../logic-apps/workflow-definition-language-functions-reference.md#variables) dans le Concepteur d’application logique et l’éditeur en mode Code.
Lorsque vous référencez une variable, utilisez le nom de celle-ci en tant que jeton, pas le nom de l’action qui est la façon habituelle de référencer les résultats d’une action. 

Par exemple, cette expression obtient les éléments à partir de la variable tableau [créée précédemment dans cet article](#append-value) à l’aide de la **fonction variables()**. La fonction **string()** retourne le contenu de la variable sous forme de chaîne : `"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Incrémenter une variable 

Pour augmenter, ou *incrément*, une variable d’une valeur constante, ajoutez l’action **Variables - Incrémenter une variable** à votre application logique. Cette action fonctionne uniquement avec les variables de type entier et flottant.

1. Dans le Concepteur d’application logique, à l’étape où vous souhaitez augmenter une variable existante, choisissez **Nouvelle étape** > **Ajouter une action**. 

   Par exemple, cette application logique a déjà un déclencheur et une action qui a créé une variable. Par conséquent, ajoutez une nouvelle action sous ces étapes :

   ![Ajouter une action](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Pour ajouter une action entre des étapes existantes, positionnez votre souris sur la flèche de connexion de façon à ce que le signe plus (+) s’affiche. Cliquez sur le signe plus, puis choisissez **Ajouter une action**.

2. Dans la zone de recherche, entrez « Incrémenter une variable » comme filtre. Dans la liste des actions, sélectionnez **Variables - Incrémenter une variable**.

   ![Sélectionnez l’action « Incrémenter une variable ».](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

3. Fournissez les informations suivantes pour l’incrémentation de votre variable :

   | Propriété | Obligatoire | Valeur |  Description |
   |----------|----------|-------|--------------|
   | NOM | Oui | <*variable-name*> | Nom de la variable à incrémenter | 
   | Valeur | Non  | <*increment-value*> | Valeur utilisée pour incrémenter la variable. La valeur par défaut est 1. <p><p>**Conseil** : bien que cette valeur soit facultative, il est recommandé de la définir afin de toujours savoir la valeur spécifique de l’incrémentation de votre variable. | 
   |||| 

   Par exemple :  
   
   ![Exemple de valeur d’incrément](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

4. Une fois terminé, dans la barre d’outils du concepteur, choisissez **Enregistrer**. 

Si vous passez du concepteur à l’éditeur en mode Code, voici comment l’action **Incrémenter une variable** s’affiche à l’intérieur de la définition de votre application logique, qui est au format JSON :

```json
"actions": {
   "Increment_variable": {
      "type": "IncrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

## <a name="example-create-loop-counter"></a>Exemple : créer un compteur de boucle

Des variables sont couramment utilisées pour compter le nombre d’exécutions d’une boucle. Cet exemple montre comment créer et utiliser des variables pour cette tâche en créant une boucle qui compte les pièces jointes à un e-mail.

1. Dans le portail Azure, créez une application logique vide. Ajoutez un déclencheur qui vérifie la présence de nouveaux e-mails et pièces jointes. 

   Cet exemple utilise le déclencheur Office 365 Outlook **quand un nouvel e-mail arrive**. 
   Vous pouvez configurer ce déclencheur de façon à ce qu’il s’active uniquement lorsque l’e-mail contient des pièces jointes.
   En revanche, vous pouvez utiliser n’importe quel connecteur qui vérifie la présence de nouveaux e-mails contenant des pièces jointes, tel que le connecteur Outlook.com.

2. Dans le déclencheur, choisissez **Afficher les options avancées**. Pour que le déclencheur vérifie la présence de pièces jointes et passe celles-ci au flux de travail de votre application logique, sélectionnez **Oui** pour ces propriétés :
   
   * **Contient une pièce jointe** 
   * **Inclure des pièces jointes** 

   ![Rechercher et inclure des pièces jointes](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

3. Ajoutez l’action [**Initialiser la variable**](#create-variable). Créez une variable de type entier nommée **Nombre** avec la valeur initiale zéro.

   ![Ajouter une action pour « Initialiser la variable »](./media/logic-apps-create-variables-store-values/initialize-variable.png)

4. Pour parcourir les pièces jointes, ajoutez une boucle *Pour chaque* en choisissant **Nouvelle étape** > **Plus** > **Ajouter un pour chaque**.

   ![Ajouter une boucle « pour chaque »](./media/logic-apps-create-variables-store-values/add-loop.png)

5. Dans la boucle, cliquez dans la zone **Select an output from previous steps (Sélectionner un résultat des étapes précédentes)**. Lorsque la liste de contenus dynamiques s’affiche, sélectionnez **Pièces jointes**. 

   ![Sélectionner « Pièces jointes »](./media/logic-apps-create-variables-store-values/select-attachments.png)

   Le champ **Pièces jointes** passe à votre boucle un tableau contenant les pièces jointes aux e-mails à partir de la sortie du déclencheur.

6. Dans la boucle « pour chaque », sélectionnez **Ajouter une action**. 

   ![Sélectionner « Ajouter une action »](./media/logic-apps-create-variables-store-values/add-action-2.png)

7. Dans la zone de recherche, entrez « Incrémenter une variable » comme filtre. Dans la liste des actions, sélectionnez **Variables - Incrémenter une variable**.

   > [!NOTE]
   > Assurez-vous que l’action **Incrémenter une variable** apparaît dans la boucle. Si l’action apparaît hors de la boucle, faites-la glisser dans la boucle.

8. Dans l’action **Incrémenter une variable**, dans la liste **Nom**, sélectionnez la variable **Nombre**. 

   ![Sélectionner la variable « Nombre »](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

9. Sous la boucle, ajoutez une action qui vous envoie le nombre de pièces jointes. Dans l’action, incluez la valeur de la variable **Nombre**, par exemple : 

   ![Ajouter une action qui envoie les résultats](./media/logic-apps-create-variables-store-values/send-email-results.png)

10. Enregistrez votre application logique. Dans la barre d’outils du concepteur, choisissez **Enregistrer**. 

### <a name="test-your-logic-app"></a>Tester votre application logique

1. Si votre application logique n’est pas activée, dans le menu de votre application logique, choisissez **Vue d’ensemble**. Dans la barre d’outils, choisissez **Activer**. 

2. Dans la barre d’outils du Concepteur d’application logique, choisissez **Exécuter**. Cette étape démarre manuellement votre application logique.

3. Envoyez un e-mail avec une ou plusieurs des pièces jointes au compte de courrier que vous avez utilisé dans cet exemple.

   Cette étape active le déclencheur de l’application logique, qui crée et exécute une instance de flux de travail de votre application logique.
   Ensuite, l’application logique vous envoie un message ou un e-mail indiquant le nombre de pièces jointes dans l’e-mail que vous avez envoyé.

Si vous passez du concepteur à l’éditeur en mode Code, voici comment la boucle « pour chaque » s’affiche avec l’action **Incrémenter une variable** à l’intérieur de la définition de votre application logique, qui est au format JSON.

```json
"actions": {
   "For_each": {
      "type": "Foreach",
      "actions": {
         "Increment_variable": {
           "type": "IncrementVariable",
            "inputs": {
               "name": "Count",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['Attachments']",
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="decrement-value"></a>

## <a name="decrement-variable"></a>Décrémenter une variable

Pour réduire, ou *décrémenter*, une variable d’une valeur constante, procédez de la même manière que pour [augmenter une variable](#increment-value), mais en recherchant et sélectionnant l’action **Variables - Décrémenter une variable**. Cette action fonctionne uniquement avec les variables de type entier et flottant.

Voici les propriétés disponibles pour l’action **Décrémenter une variable** :

| Propriété | Obligatoire | Valeur |  Description |
|----------|----------|-------|--------------|
| NOM | Oui | <*variable-name*> | Nom de la variable à décrémenter | 
| Valeur | Non  | <*increment-value*> | Valeur de la décrémentation de la variable. La valeur par défaut est 1. <p><p>**Conseil** : bien que cette valeur soit facultative, il est recommandé de la définir afin de toujours savoir la valeur spécifique de la décrémentation de votre variable. | 
||||| 

Si vous passez du concepteur à l’éditeur en mode Code, voici comment l’action **Décrémenter une variable** s’affiche à l’intérieur de la définition de votre application logique, qui est au format JSON.

```json
"actions": {
   "Decrement_variable": {
      "type": "DecrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```


<a name="assign-value"></a>

## <a name="set-variable"></a>Définir une variable

Pour attribuer une valeur différente à une variable, procédez de la même manière que pour [augmenter une variable](#increment-value) sauf que vous devez : 

1. Rechercher et sélectionner l’action **Variables - Définir une variable** à la place. 

2. Fournissez le nom de la variable et la valeur à lui attribuer. Le type de données de la nouvelle valeur et de la variable doivent être le même.
La valeur est requise, car cette action n’a pas de valeur par défaut. 

Voici les propriétés disponibles pour l’action **Définir une variable** :

| Propriété | Obligatoire | Valeur |  Description | 
|----------|----------|-------|--------------| 
| NOM | Oui | <*variable-name*> | Nom de la variable à modifier | 
| Valeur | Oui | <*new-value*> | Valeur que vous souhaitez attribuer à la variable. Le type de données des deux doit être identique. | 
||||| 

> [!NOTE]
> Sauf si vous incrémentez ou décrémentez des variables, la modification de variables dans des boucles *peut* produire des résultats inattendus, car les boucles s’exécutent en parallèle ou simultanément par défaut. Dans ce cas, essayez de définir votre boucle pour qu’elle s’exécute de manière séquentielle. Par exemple, lorsque vous souhaitez référencer la valeur de la variable à l’intérieur de la boucle et attendez la même valeur au début et à la fin de cette instance de boucle, procédez comme suit pour modifier la façon dont la boucle s’exécute : 
>
> 1. Dans l’angle supérieur droit de votre boucle, sélectionnez le bouton représentant des points de suspension (...), puis **Paramètres**.
> 
> 2. Sous **Contrôle d’accès concurrentiel**, définissez le paramètre **Remplacer les valeurs par défaut** sur **Activé**.
>
> 3. Positionnez le curseur **Degré de parallélisme** sur **1**.

Si vous passez du concepteur à l’éditeur en mode Code, voici comment l’action **Définir une variable** s’affiche à l’intérieur de la définition de votre application logique, qui est au format JSON. Cet exemple modifie la valeur actuelle de la variable « Nombre ». 

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   },
   "Set_variable": {
      "type": "SetVariable",
      "inputs": {
         "name": "Count",
         "value": 100
      },
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="append-value"></a>

## <a name="append-to-variable"></a>Ajouter à une variable

Pour les variables qui stockent des chaînes ou des tableaux, vous pouvez insérer ou *ajouter* la valeur d’une variable à la fin de ces chaînes ou des tableaux. Vous pouvez procéder de la même manière que pour [augmenter une variable](#increment-value), à l’exception des étapes suivantes : 

1. Recherchez et sélectionnez une des actions suivantes, selon que votre variable est une chaîne ou un tableau : 

  * **Variables - Ajouter à une variable chaîne**
  * **Variables - Ajouter à une variable tableau** 

2. Spécifiez la valeur à ajouter à la fin de la chaîne ou du tableau. Cette valeur est obligatoire. 

Voici les propriétés disponibles pour les actions **Ajouter à...**  :

| Propriété | Obligatoire | Valeur |  Description | 
|----------|----------|-------|--------------| 
| NOM | Oui | <*variable-name*> | Nom de la variable à modifier | 
| Valeur | Oui | <*append-value*> | Valeur que vous souhaitez ajouter, qui peut être de tout type | 
|||||  

Si vous passez du concepteur à l’éditeur en mode Code, voici comment l’action **Ajouter à une variable tableau** s’affiche à l’intérieur de la définition de votre application logique, qui est au format JSON.
Cet exemple crée une variable tableau et ajoute une autre valeur à la fin du tableau. Le résultat est une variable mise à jour qui contient ce tableau : `[1,2,3,"red"]` 

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
            "name": "myArrayVariable",
            "type": "Array",
            "value": [1, 2, 3]
         } ]
      },
      "runAfter": {}
   },
   "Append_to_array_variable": {
      "type": "AppendToArrayVariable",
      "inputs": {
         "name": "myArrayVariable",
         "value": "red"
      },
      "runAfter": {
        "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [connecteurs Logic Apps](../connectors/apis-list.md)
