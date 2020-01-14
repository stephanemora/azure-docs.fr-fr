---
title: 'Tutoriel : Tests par lots pour détecter les problèmes - LUIS'
titleSuffix: Azure Cognitive Services
description: Ce tutoriel montre comment utiliser des tests par lot pour rechercher et résoudre les problèmes de prédiction d’énoncés dans votre application.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: diberry
ms.openlocfilehash: 54beb26554fd823c46f961b4cc7057f347ad343c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447974"
---
# <a name="tutorial-batch-test-data-sets"></a>Tutoriel : Tester des jeux de données par lot

Ce tutoriel montre comment utiliser des tests par lot pour rechercher et résoudre les problèmes de prédiction d’énoncés dans votre application.

Les tests de lots permettent de valider l’état du modèle actif entraîné avec un ensemble connu d’entités et d’énoncés étiquetés. Dans le fichier de lot au format JSON, ajoutez les énoncés et définissez les étiquettes d’entités que vous voulez prédire au sein de l’énoncé.

Exigences des tests de lots :

* 1 000 énoncés maximum par test.
* Pas de doublons.
* Types d’entités autorisés : seules les entités apprises automatiquement de type simple et composite. Les tests de lots ne sont utiles que pour les entités et les intentions apprises automatiquement.

Si vous utilisez une application autre que ce tutoriel, *n’utilisez pas* les exemples d’énoncés déjà ajoutés à une intention.



**Dans ce tutoriel, vous allez découvrir comment :**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importer l’exemple d’application
> * Créer un fichier de test par lot
> * Exécuter un test par lot
> * Examiner les résultats du test
> * Corriger les erreurs
> * Tester à nouveau le lot

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importer l’exemple d’application

Continuez avec l’application créée dans le dernier tutoriel, nommée **HumanResources**.

Utiliser les étapes suivantes :

1.  Téléchargez et enregistrez le [fichier JSON de l’application](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-review-HumanResources.json?raw=true).


2. Importez le code JSON dans une nouvelle application.

3. À partir de la section **Manage (Gérer)** , sous l’onglet **Versions**, clonez la version et nommez-la `batchtest`. Le clonage est un excellent moyen de manipuler diverses fonctionnalités de LUIS sans affecter la version d’origine. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL.

4. Effectuez l’apprentissage de l’application.

## <a name="batch-file"></a>Fichier de commandes

1. Créez `HumanResources-jobs-batch.json` dans un éditeur de texte ou [téléchargez-le](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-jobs-batch.json?raw=true).

2. Dans le fichier de lot au format JSON, ajoutez des énoncés avec **l’intention** à prédire dans le test.

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>Exécuter le test par lot

1. Sélectionnez **Test** dans la barre de navigation supérieure.

2. Sélectionnez le **panneau Test par lot** dans le panneau de droite.

    [![Capture d’écran de l’application LUIS avec Panneau de test de lot en surbrillance](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Sélectionnez **Importer le jeu de données**.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de l’application LUIS avec Importer le jeu de données en surbrillance](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)

4. Choisissez l’emplacement du fichier `HumanResources-jobs-batch.json`.

5. Nommez le jeu de données `intents only` et sélectionnez **Terminé**.

    ![Sélectionner un fichier](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Sélectionnez le bouton **Exécuter**.

7. Sélectionnez **Afficher les résultats**.

8. Passez en revue les résultats dans le graphe et la légende.

    [![Capture d’écran de l’application LUIS avec les résultats du test de lot](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>Passer en revue les résultats du test par lot

Le graphique de lot présente quatre quadrants de résultats. À droite du graphique se trouve un filtre. Le filtre contient des intentions et des entités. Lorsque vous sélectionnez un point ou une [section du graphique](luis-concept-batch-test.md#batch-test-results), le ou les énoncés associés s’affichent sous le graphique.

Au passage de la souris sur le graphique, la roulette permet d’agrandir ou de réduire l’affichage du graphique, ce qui est utile en présence de nombreux points très rapprochés.

Le graphique est divisé en quatre quadrants, dont deux s’affichent en rouge. **Il s’agit des sections sur lesquelles vous devez vous concentrer**.

### <a name="getjobinformation-test-results"></a>Résultats de test GetJobInformation

Les résultats de test **GetJobInformation** présentés dans le filtre indiquent que deux des quatre prédictions ont réussi. Sélectionnez le nom **Faux négatif** dans le quadrant inférieur gauche pour afficher les énoncés sous le graphique.

Appuyez sur les touches Ctrl+E pour passer en mode étiquette et voir le texte exact de l’énoncé d’utilisateur.

L’énoncé `Is there a database position open in Los Colinas?` est étiqueté _GetJobInformation_, mais le modèle actuel a prédit l’énoncé _ApplyForJob_.

Il y a presque trois fois plus d’exemples d’énoncés pour **ApplyForJob** que pour **GetJobInformation**. Ce déséquilibre joue en faveur de l’intention **ApplyForJob**, ce qui entraîne une prédiction incorrecte.

Comme on peut le constater, les deux intentions comportent le même nombre d’erreurs. Une prédiction incorrecte dans l’une affecte l’autre. Elles présentent toutes deux des erreurs, car les énoncés ont été prédits à tort pour une intention, et non prédits à tort également pour l’autre.

<a name="fix-the-app"></a>

## <a name="how-to-fix-the-app"></a>Comment corriger l’application

L’objectif de cette section est de corriger l’application afin que tous les énoncés soient correctement prédits pour **GetJobInformation**.

Il serait en apparence tout aussi rapide de résoudre le problème en ajoutant ces énoncés de fichier de lot à la bonne intention. Mais ce n’est pas ce que vous souhaitez faire. Le but est que LUIS prédise correctement ces énoncés sans les ajouter comme exemples.

On peut aussi envisager de supprimer des énoncés de **ApplyForJob** jusqu’à ce que la quantité d’énoncés soit identique à **GetJobInformation**. Cela corrigerait peut-être les résultats de test, mais LUIS ne parviendrait pas à prédire correctement cette intention la fois suivante.

La correction consiste à ajouter des énoncés à **GetJobInformation**. N’oubliez pas de modifier la longueur de l’énoncé, ainsi que le choix et la disposition des mots, tout en visant l’objectif de rechercher des informations de travail ne s’appliquant _pas_ au travail.

### <a name="add-more-utterances"></a>Ajouter des énoncés

1. Fermez le panneau de test de lot en sélectionnant le bouton **Tester** dans le volet de navigation supérieur.

2. Sélectionnez **GetJobInformation** dans la liste des intentions.

3. Ajoutez des énoncés variés du point de vue de la longueur, du choix et de l’ordre des mots et comportant les termes `resume`, `c.v.` et `apply` :

    |Exemples d’énoncés pour l’intention **GetJobInformation**|
    |--|
    |Does the new job in the warehouse for a stocker require that I apply with a resume?|
    |Where are the roofing jobs today?|
    |I heard there was a medical coding job that requires a resume.|
    |I would like a job helping college kids write their c.v.s. |
    |Here is my resume, looking for a new post at the community college using computers.|
    |What positions are available in child and home care?|
    |Is there an intern desk at the newspaper?|
    |My c.v. shows I’m good at analyzing procurement, budgets, and lost money. Is there anything for this type of work?|
    |Where are the earth drilling jobs right now?|
    |I’ve worked 8 years as an EMS driver. Any new jobs?|
    |New food handling jobs require application?|
    |How many new yard work jobs are available?|
    |Is there a new HR post for labor relations and negotiations?|
    |I have a masters in library and archive management. Any new positions?|
    |Are there any babysitting jobs for 13 year olds in the city today?|

    N’étiquetez pas l’entité **Job** dans les énoncés. Cette section du tutoriel est exclusivement consacrée à la prédiction des intentions.

4. Effectuez l’apprentissage de l’application en sélectionnant **Effectuer l’apprentissage** dans le volet de navigation supérieur droit.

## <a name="verify-the-new-model"></a>Vérifier le nouveau modèle

Pour vérifier que les énoncés du test de lot sont correctement prédits, réexécutez le test de lot.

1. Sélectionnez **Test** dans la barre de navigation supérieure. Si les résultats du lot sont toujours ouverts, sélectionnez **Revenir à la liste**.

1. Sélectionnez les points de suspension (***…***) à droite du nom du lot, puis **Exécuter**. Attendez la fin du test par lot. Vous remarquerez que le bouton **Voir les résultats** est maintenant vert. Cela signifie que l’exécution de l’ensemble du lot a réussi.

1. Sélectionnez **Afficher les résultats**. Des icônes vertes doivent s’afficher à gauche du nom des intentions.

## <a name="create-batch-file-with-entities"></a>Créer un fichier de lot avec des entités

Les entités doivent être étiquetées dans le fichier JSON de lot pour pouvoir être vérifiées dans le test de lot.

Les variations du nombre total de mots ([tokens](luis-glossary.md#token)) dans les entités peuvent avoir un impact sur la qualité des prédictions. Veillez à ce que les données d’apprentissage fournies à l’intention avec des énoncés étiquetés présentent des longueurs variables d’entité.

Il est préférable de commencer à écrire et à tester des fichiers de lots avec quelques énoncés et entités qui fonctionnent bien, ainsi que quelques autres qui seront probablement prédits de façon incorrecte. Vous pourrez ainsi vous concentrer rapidement sur les points problématiques. Après les tests des intentions **GetJobInformation** et **ApplyForJob** avec différents noms de postes, qui n’étaient pas prédits, ce fichier de test de lot a été développé pour voir si certaines valeurs de l’entité **Job** posent un problème de prédiction.

La valeur d’une entité **Job**, fournie dans les énoncés de test, est généralement composée d’un ou deux mots, ou plus dans quelques exemples. Si _votre_ application de ressources humaines comporte en général des noms de postes longs, les exemples d’énoncés étiquetés avec l’entité **Job** dans cette application ne fonctionneront pas correctement.

1. Créez `HumanResources-entities-batch.json` dans un éditeur de texte comme [VSCode](https://code.visualstudio.com/) ou [téléchargez-le](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-entities-batch.json?raw=true).

2. Dans le fichier de lot au format JSON, ajoutez un tableau d’objets comportant des énoncés liés à **l’Intention** à prédire dans le test, ainsi que les emplacements des éventuelles entités de l’énoncé. Dans la mesure où les entités se présentent sous forme de tokens, commencez-les et terminez-les par un caractère et non par un espace, car cela provoquerait une erreur lors de l’importation du fichier de lot.

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>Exécuter le lot avec des entités

1. Sélectionnez **Test** dans la barre de navigation supérieure.

2. Sélectionnez le **panneau Test par lot** dans le panneau de droite.

3. Sélectionnez **Importer le jeu de données**.

4. Choisissez l’emplacement du fichier `HumanResources-entities-batch.json` dans le système de fichiers.

5. Nommez le jeu de données `entities` et sélectionnez **Terminé**.

6. Sélectionnez le bouton **Exécuter**. Attendez la fin du test.

7. Sélectionnez **Afficher les résultats**.

## <a name="review-entity-batch-results"></a>Vérifier les résultats du lot d’entités

Le graphique s’ouvre sur toutes les intentions correctement prédites. Faites défiler le filtre de droite pour trouver les prédictions d’entités erronées.

1. Sélectionnez l’entité **Job** dans le filtre.

    ![Prédictions d’entités erronées dans le filtre](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    Le graphique affiche maintenant les prédictions d’entités.

2. Sélectionnez **Faux négatif** dans le quadrant qui se trouve en bas à gauche du graphique. Utilisez ensuite la combinaison de touches Ctrl+E pour passer à l’affichage en tokens.

    [![Affichage des jetons des prédictions d’entités](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)

    L’examen des énoncés sous le graphique révèle une erreur récurrente lorsque le nom du poste comporte `SQL`. Dans les exemples d’énoncés et la liste d’expressions de postes, SQL n’est utilisé qu’une seule fois et uniquement dans le cadre d’un nom de poste plus long, `sql/oracle database administrator`.

## <a name="fix-the-app-based-on-entity-batch-results"></a>Corriger l’application en fonction des résultats du test de lot d’entités

Pour corriger l’application, il faut que LUIS détermine correctement les variantes des postes SQL. Il existe plusieurs possibilités.

* Ajoutez explicitement d’autres exemples d’énoncés qui utilisent SQL et étiquetez ces mots comme étant une entité Job.
* Ajoutez explicitement d’autres postes SQL à la liste d’expressions.

Il ne vous reste plus qu’à effectuer l’une de ces tâches.

Le fait d’ajouter un [modèle](luis-concept-patterns.md) avant que l’entité ne soit correctement prédite ne résoudra pas le problème. En effet, le modèle n’aura pas de correspondance tant que toutes ses entités n’auront pas été détectées.

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Étapes suivantes

Le tutoriel a utilisé un test par lot pour rechercher les problèmes au niveau du modèle actuel. Le modèle a été corrigé et retesté avec le fichier de commandes pour vérifier que la modification était correcte.

> [!div class="nextstepaction"]
> [En savoir plus sur les modèles](luis-tutorial-pattern.md)

