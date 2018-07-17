---
title: 'Tutoriel : Utiliser des modèles pour améliorer les prédictions de LUIS – Azure | Microsoft Docs'
titleSuffix: Azure
description: Ce tutoriel utilise les modèles d’intentions afin d’améliorer les prédictions de LUIS en matière d’intentions et d’entités.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: ff5572366be548132b28e5ce03b9595e7f98128c
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265314"
---
# <a name="tutorial-use-patterns-to-improve-predictions"></a>Tutoriel : Utiliser des modèles pour améliorer les prédictions

Ce tutoriel utilise les modèles pour améliorer les prédictions en matière d’intentions et d’entités.  

> [!div class="checklist"]
* Identifier quand un modèle serait utile à votre application.
* Créer un modèle. 
* Utiliser des entités prédéfinies et personnalisées dans un modèle. 
* Vérifier l’amélioration des prédictions de modèle.
* Ajouter un rôle à une entité pour trouver des entités liées au contexte.
* Ajouter une entité Pattern.any pour rechercher des entités à structure libre.

Pour les besoins de cet article, il vous faudra un compte [LUIS][LUIS] gratuit pour créer votre application LUIS.

## <a name="import-humanresources-app"></a>Importer une application HumanResources
Ce tutoriel permet d’importer une application HumanResources. Cette application a trois intentions : None, GetEmployeeOrgChart et GetEmployeeBenefits. Elle comporte deux entités : le nombre prédéfini et Employees. L’entité Employee est une entité simple permettant d’extraire le nom d’un employé. 

1. Créez un fichier d’application LUIS et nommez-le `HumanResources.json`. 

2. Copiez la définition d’application suivante dans le fichier :

   [!code-json[Add the LUIS model](~/samples-luis/documentation-samples/tutorial-patterns/HumanResources.json?range=1-164 "Add the LUIS model")]

3. Sur la page **Applications** LUIS, sélectionnez **Importer une nouvelle application**. 

4. Dans la boîte de dialogue **Importer une nouvelle application**, sélectionnez le fichier `HumanResources.json` que vous avez créé à l’étape 1.

5. Sélectionnez l’intention **GetEmployeeOrgChart**, puis passez de **Vue Entités** à **Vue Tokens**. Plusieurs exemples d’énoncés sont listés. Chacun comporte un nom, qui correspond à une entité Employee. Tous les noms sont différents et l’ordre des mots diffère pour chaque énoncé. Cette diversité aide LUIS à apprendre un large éventail d’énoncés.

    ![Capture d’écran de la page Intention avec Vue Entités](media/luis-tutorial-pattern/utterances-token-view.png)

6. Sélectionnez **Entraîner** dans la barre de navigation supérieure pour effectuer l’apprentissage de l’application. Attendez que la barre de réussite verte apparaisse.

7. Sélectionnez **Tester** dans le volet supérieur. Entrez `Who does Patti Owens report to?`, puis sélectionnez Entrée. Sélectionnez **Inspecter** sous l’énoncé pour voir plus d’informations sur le test.
    
    Le nom de l’employée, Patti Owens, n’a pas encore été utilisé dans un exemple d’énoncé. Il s’agit d’un test pour voir si LUIS a bien appris cet énoncé pour l’intention `GetEmployeeOrgChart` ; l’entité Employee doit être `Patti Owens`. Le résultat doit être inférieur à 50 % (0,50) pour l’intention `GetEmployeeOrgChart`. L’intention est correcte, mais le score est faible. L’entité Employee est aussi correctement identifiée comme `Patti Owens`. Les modèles augmentent ce score de prédiction initial. 

    ![Capture d’écran du volet Tester](media/luis-tutorial-pattern/original-test.png)

8. Fermez le panneau de test en sélectionnant le bouton **Tester** dans le volet de navigation supérieur. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Apprendre à LUIS les énoncés courants avec moins d’exemples grâce aux modèles
En raison de la nature du domaine des ressources humaines, il existe plusieurs moyens courants de poser des questions sur les relations des employés dans les organisations. Par exemple : 

```
Who does Mike Jones report to?
Who reports to Mike Jones? 
```

Ces énoncés sont trop proches pour permettre de déterminer l’unicité contextuelle de chacun sans fournir trop d’exemples d’énoncé. Si l’on ajoute un modèle pour une intention, LUIS apprend les modèles d’énoncés courants de cette intention avec peu d’exemples d’énoncés. 

Quelques exemples d’énoncés de modèle pour cette intention :

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

Le modèle est une combinaison de correspondance par expression régulière et de Machine Learning. Ensuite, indiquez quelques exemples d’énoncés de modèle pour que LUIS puisse apprendre le modèle. Ces exemples, ainsi que les énoncés de l’intention, permettent à LUIS de mieux comprendre quels énoncés correspondent à l’intention et où se trouve l’entité dans l’énoncé. <!--A pattern is specific to an intent. You can't duplicate the same pattern on another intent. That would confuse LUIS, which lowers the prediction score. -->

## <a name="add-the-template-utterances"></a>Ajouter les énoncés de modèle

1. Dans le volet de navigation gauche, sous **Améliorer les performances de l’application**, sélectionnez **Modèles**.

2. Sélectionnez l’intention **GetEmployeeOrgChart**, puis entrez les énoncés de modèle suivants, un par un, en sélectionnant Entrée après chacun :

    ```
    Does {Employee} have {number} subordinates?
    Does {Employee} have {number} direct reports?
    Who does {Employee} report to?
    Who reports to {Employee}?
    Who is {Employee}'s manager?
    Who are {Employee}'s subordinates?
    ```

    La syntaxe `{Employee}` marque le type et l’emplacement de l’entité dans l’énoncé de modèle. 

    ![Capture d’écran de la saisie d’énoncés de modèle pour une intention](./media/luis-tutorial-pattern/enter-pattern.png)

3. Sélectionnez **Entraîner** dans la barre de navigation supérieure. Attendez que la barre de réussite verte apparaisse.

4. Sélectionnez **Tester** dans le volet supérieur. Entrez `Who does Patti Owens report to?` dans la zone de texte. Sélectionnez Entrée. Il s’agit de l’énoncé testé dans la section précédente. Le résultat devrait être supérieur pour l’intention `GetEmployeeOrgChart`. 

    Le score est maintenant bien meilleur. LUIS a appris le modèle correspondant à l’intention sans qu’il soit nécessaire de fournir de nombreux exemples.

    ![Capture d’écran du volet Tester avec un score élevé](./media/luis-tutorial-pattern/high-score.png)

    L’entité est trouvée en premier, suivie du modèle, indiquant l’intention. Si, dans votre résultat de test, l’entité n’est pas détectée et, par conséquent, le modèle est introuvable, vous devrez ajouter des exemples d’énoncés sur l’intention (et non le modèle). 

5. Fermez le panneau de test en sélectionnant le bouton **Tester** dans le volet de navigation supérieur.

## <a name="use-an-entity-with-a-role-in-a-pattern"></a>Utiliser une entité avec un rôle dans un modèle
L’application LUIS est utilisée pour déplacer des employés d’un endroit à un autre. Exemple d’énoncé : `Move Bob Jones from Seattle to Los Colinas`. Chacun des lieux de l’énoncé a une signification différente. Seattle est l’emplacement d’origine et Los Colinas l’emplacement de destination du déplacement. Afin de différencier ces lieux dans le modèle, vous allez créer dans les sections suivantes une entité simple pour l’emplacement avec deux rôles : origine et destination. 

### <a name="create-a-new-intent-for-moving-people-and-assets"></a>Créer une intention pour déplacer des personnes et des ressources
Créez une intention pour tous les énoncés liés au déplacement de personnes ou de ressources.

1. Sélectionnez **Intentions** dans le volet de navigation gauche.
2. Sélectionnez **Créer une intention**.
3. Nommez la nouvelle intention `MoveAssetsOrPeople`.
4. Ajoutez des exemples d’énoncés :

    ```
    Move Bob Jones from Seattle to Los Colinas
    Move Dave Cooper from Redmond to Seattle
    Move Jim Smith from Toronto to Vancouver
    Move Jill Benson from Boston to London
    Move Travis Hinton from Portland to Orlando
    ```
    ![Capture d’écran d’exemple d’énoncé pour l’intention MoveAssetsOrPeople](./media/luis-tutorial-pattern/intent-moveasserts-example-utt.png)

    L’objectif des exemples d’énoncés est de donner suffisamment d’exemples. Si, dans la suite du test, l’entité Location n’est pas détectée et, par conséquent, le modèle non plus, revenez à cette étape et ajoutez d’autres d’exemples. Effectuez à nouveau l’apprentissage et les tests. 

5. Marquez les entités des exemples d’énoncés avec l’entité Employee en sélectionnant le prénom puis le nom dans l’énoncé, puis en sélectionnant l’entité Employee dans la liste.

    ![Capture d’écran d’énoncés dans MoveAssetsOrPeople marqués avec l’entité Employee](./media/luis-tutorial-pattern/intent-moveasserts-employee.png)

6. Sélectionnez le texte `portland` dans l’énoncé `move travis hinton from portland to orlando`. Dans la boîte de dialogue contextuelle, entrez le nouveau nom d’entité `Location`, puis sélectionnez **Créer une entité**. Choisissez le type d’entité **Simple** et sélectionnez **Terminé**.

    ![Capture d’écran de la création d’une nouvelle entité Location](./media/luis-tutorial-pattern/create-new-location-entity.png)

    Marquez le reste des noms de lieux dans les énoncés. 

    ![Capture d’écran de toutes les entités marquées](./media/luis-tutorial-pattern/moveasset-all-entities-labeled.png)

    Le modèle de choix et d’ordre des mots est évident dans l’image précédente. Si, alors que les énoncés de l’intention ont une structure évidente, vous ne vous servez **pas** de modèles, c’est le signe que vous devriez en utiliser. 

    Si vous attendez une grande variété d’énoncés, vous aurez les mauvais exemples d’énoncés au lieu d’un modèle. Il vous faudra dans ce cas des énoncés très variables dans le choix des mots, la longueur de l’énoncé et la place des entités. 

<!--TBD: what guidance to move from hier entities to patterns with roles -->
<!--    The [Hierarchical entity quickstart](luis-quickstart-intent-and-hier-entity.md) uses the  same idea of location but uses child entities to find origin and destination locations. 
-->
### <a name="add-role-to-location-entity"></a>Ajouter un rôle à une entité Location 
Les rôles ne servent que pour les modèles. Ajoutez les rôles Origin et Destination à l’entité Location. 

1. Sélectionnez **Entités** dans le volet de navigation gauche, puis **Location** dans la liste des entités.

2. Ajoutez les rôles `Origin` et `Destination` à l’entité.

    ![Capture d’écran de la nouvelle entité avec les rôles](./media/luis-tutorial-pattern/location-entity.png)

    Les rôles ne sont pas marqués sur la page de l’intention MoveAssetsOrPeople, car ils n’existent pas sur les énoncés des intentions. Ils ne sont présents que sur les énoncés de modèle. 

### <a name="add-template-utterances-that-uses-location-and-destination-roles"></a>Ajouter des énoncés de modèle qui utilisent des rôles de lieu et de destination
Ajoutez des énoncés de modèle qui utilisent la nouvelle entité.

1. Sélectionnez **Modèles** dans le volet de navigation gauche.

2. Sélectionnez l’intention **MoveAssetsOrPeople**.

3. Entrez un nouvel énoncé de modèle à l’aide de la nouvelle entité `Move {Employee} from {Location:Origin} to {Location:Destination}`. La syntaxe d’une entité et d’un rôle au sein d’un énoncé de modèle est `{entity:role}`.

    ![Capture d’écran de la nouvelle entité avec les rôles](./media/luis-tutorial-pattern/pattern-moveassets.png)

4. Effectuez l’apprentissage de l’application pour la nouvelle intention, l’entité et le modèle.

### <a name="test-the-new-pattern-for-role-data-extraction"></a>Tester l’extraction de données de rôle dans le nouveau modèle
Validez le nouveau modèle par un test.

1. Sélectionnez **Tester** dans le volet supérieur. 
2. Entrez l’énoncé `Move Tammi Carlson from Bellingham to Winthrop`.
3. Sélectionnez **Inspecter** sous le résultat afin de voir les résultats des tests pour l’entité et l’intention.

    ![Capture d’écran de la nouvelle entité avec les rôles](./media/luis-tutorial-pattern/test-with-roles.png)

    Les entités sont trouvées en premier, suivies du modèle, indiquant l’intention. Si, dans votre résultat de test, les entités ne sont pas détectées et, par conséquent, le modèle est introuvable, vous devrez ajouter des exemples d’énoncés sur l’intention (et non le modèle). 

4. Fermez le panneau de test en sélectionnant le bouton **Tester** dans le volet de navigation supérieur.

## <a name="use-a-patternany-entity-to-find-free-form-entities-in-a-pattern"></a>Utiliser une entité Pattern.any pour rechercher des entités à structure libre dans un modèle
Cette application HumanResources permet également aux employés de trouver les formulaires de l’entreprise. La plupart des formulaires ont des titres de longueur variable. Certaines expressions peuvent être sources de confusion pour LUIS, qui ne sait pas où se termine le nom du formulaire. Dans un modèle, l’entité **Pattern.any** permet de spécifier le début et la fin du nom du formulaire afin que LUIS puisse extraire correctement son nom. 

### <a name="create-a-new-intent-for-the-form"></a>Créer une intention pour le formulaire
Créez une intention pour les énoncés qui recherchent des formulaires.

1. Sélectionnez **Intentions** dans le volet de navigation gauche.

2. Sélectionnez **Créer une intention**.

3. Nommez la nouvelle intention `FindForm`.

4. Ajoutez un exemple d’énoncé.

    ```
    `Where is the form What to do when a fire breaks out in the Lab and who needs to sign it after I read it?`
    ```

    ![Capture d’écran de la nouvelle entité avec les rôles](./media/luis-tutorial-pattern/intent-findform.png)

    Le titre du formulaire est `What to do when a fire breaks out in the Lab`. L’énoncé demande l’emplacement du formulaire et la personne chargée de le signer pour confirmer que l’employé l’a lu. Sans entité Pattern.any, il serait difficile de comprendre où se termine le titre du formulaire et de l’extraire en tant qu’entité de l’énoncé.

### <a name="create-a-patternany-entity-for-the-form-title"></a>Créer une entité Pattern.any pour le titre du formulaire
L’entité Pattern.any gère des entités de longueur variable. Elle fonctionne seulement dans un modèle, car le modèle marque le début et la fin de l’entité. Si vous constatez que votre modèle, quand il comporte une entité Pattern.any, extrait mal les entités, utilisez une [liste explicite](luis-concept-patterns.md#explicit-lists) pour corriger ce problème. 

1. Sélectionnez **Entités** dans le volet de navigation gauche.

2. Sélectionnez **Créer une entité**. 

3. Nommez l’entité `FormName` avec le type **Pattern.any**. Dans le cadre de ce tutoriel, il n’est pas nécessaire d’ajouter des rôles à l’entité.

    ![Image de la boîte de dialogue pour le nom et le type d’entité](./media/luis-tutorial-pattern/create-entity-pattern-any.png)

### <a name="add-a-pattern-that-uses-the-patternany"></a>Ajouter un modèle qui utilise l’entité Pattern.any

1. Sélectionnez **Modèles** dans le volet de navigation gauche.

2. Sélectionnez l’intention **FindForm**.

3. Entrez un énoncé de modèle à l’aide de la nouvelle entité `Where is the form {FormName} and who needs to sign it after I read it?`.

    ![Capture d’écran d’un énoncé de modèle utilisant l’entité Pattern.any](./media/luis-tutorial-pattern/pattern.any-template-utterance.png)

4. Effectuez l’apprentissage de l’application pour la nouvelle intention, l’entité et le modèle.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Tester l’extraction de données à structure libre dans le nouveau modèle
1. Sélectionnez **Tester** dans la barre supérieure pour ouvrir le panneau de test. 

2. Entrez l’énoncé `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`.

3. Sélectionnez **Inspecter** sous le résultat afin de voir les résultats des tests pour l’entité et l’intention.

    ![Capture d’écran d’un énoncé de modèle utilisant l’entité Pattern.any](./media/luis-tutorial-pattern/test-pattern.any-results.png)

    L’entité est trouvée en premier, suivie du modèle, indiquant l’intention. Si, dans votre résultat de test, les entités ne sont pas détectées et, par conséquent, le modèle est introuvable, vous devrez ajouter des exemples d’énoncés sur l’intention (et non le modèle).

4. Fermez le panneau de test en sélectionnant le bouton **Tester** dans le volet de navigation supérieur.

## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous n’en avez plus besoin, supprimez l’application LUIS. Pour cela, sélectionnez le menu représentant trois points (…) à droite du nom de l’application dans la liste des applications, puis **Supprimer**. Dans la boîte de dialogue contextuelle **Supprimer l’application ?**, sélectionnez **OK**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Utilisez une liste d’expressions pour améliorer la prédiction](luis-tutorial-interchangeable-phrase-list.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions