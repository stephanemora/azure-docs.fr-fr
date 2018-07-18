---
title: Utiliser le test par lot pour améliorer les prédictions dans LUIS | Microsoft Docs
titleSuffix: Azure
description: Charger le test par lot, examiner les résultats et améliorer les prédictions LUIS à l’aide de modifications.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 5788f17f2724a0354a1db506971c2343c1800f01
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266394"
---
# <a name="use-batch-testing-to-find-prediction-accuracy-issues"></a>Utiliser le test par lot pour rechercher des problèmes de précision de prédiction

Ce tutoriel montre comment utiliser le test par lot pour rechercher des problèmes de prédiction dans les énoncés.  

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
* Créer un fichier de test par lot 
* Exécuter un test par lot
* Examiner les résultats du test
* Corriger les erreurs pour les intentions
* Tester à nouveau le lot

## <a name="prerequisites"></a>Prérequis

> [!div class="checklist"]
> * Pour cet article, vous devez aussi disposer d’un compte [LUIS][LUIS] afin de créer votre application LUIS.

> [!Tip]
> Si vous n’avez pas encore d’abonnement, vous pouvez vous inscrire pour un [compte gratuit](https://azure.microsoft.com/free/).

## <a name="create-new-app"></a>Créer une application
Cet article utilise le domaine prédéfini HomeAutomation. Le domaine prédéfini contient des intentions, des entités et des énoncés pour contrôler les appareils HomeAutomation comme les lumières. Créer l’application, ajouter le domaine, former et publier.

1. Sur le site web [LUIS], créez une application en sélectionnant **Créer une application** dans la page **MyApps**. 

    ![Créer une application](./media/luis-tutorial-batch-testing/create-app-1.png)

2. Entrez le nom `Batchtest-HomeAutomation` dans la boîte de dialogue.

    ![Entrer le nom de l’application](./media/luis-tutorial-batch-testing/create-app-2.png)

3. Sélectionnez **Domaines prédéfinis** dans le coin inférieur gauche. 

    ![Sélectionner un domaine prédéfini](./media/luis-tutorial-batch-testing/prebuilt-domain-1.png)

4. Sélectionnez **Ajouter un domaine** pour HomeAutomation.

    ![Ajouter un domaine HomeAutomation](./media/luis-tutorial-batch-testing/prebuilt-domain-2.png)

5. Sélectionnez **Former** dans la barre de navigation supérieure droite.

    ![Sélectionner le bouton Former](./media/luis-tutorial-batch-testing/train-button.png)

## <a name="batch-test-criteria"></a>Critères du test par lot
Le test par lot peut tester jusqu’à 1 000 énoncés à la fois. Le lot ne doit pas contenir de doublons. [Exportez](create-new-app.md#export-app) l’application afin d’afficher la liste des énoncés actuels.  

La stratégie de test pour LUIS utilise trois jeux de données distincts : modèles d’énoncés, énoncés de test par lot et énoncés de point de terminaison. Pour ce tutoriel, veillez à ne pas utiliser les énoncés des modèles d’énoncés (ajoutés à une intention) ou des énoncés de point de terminaison. 

N’utilisez pas les énoncés déjà dans l’application pour le test par lot :

```
'breezeway on please',
'change temperature to seventy two degrees',
'coffee bar on please',
'decrease temperature for me please',
'dim kitchen lights to 25 .',
'fish pond off please',
'fish pond on please',
'illuminate please',
'living room lamp on please',
'living room lamps off please',
'lock the doors for me please',
'lower your volume',
'make camera 1 off please',
'make some coffee',
'play dvd',
'set lights bright',
'set lights concentrate',
'set lights out bedroom',
'shut down my work computer',
'silence the phone',
'snap switch fan fifty percent',
'start master bedroom light .',
'theater on please',
'turn dimmer off',
'turn off ac please',
'turn off foyer lights',
'turn off living room light',
'turn off staircase',
'turn off venice lamp',
'turn on bathroom heater',
'turn on external speaker',
'turn on my bedroom lights .',
'turn on the furnace room lights',
'turn on the internet in my bedroom please',
'turn on thermostat please',
'turn the fan to high',
'turn thermostat on 70 .' 
```

## <a name="create-a-batch-to-test-intent-prediction-accuracy"></a>Créer un lot pour tester la précision de prédiction de l’intention
1. Créez `homeauto-batch-1.json` dans un éditeur de texte comme [VSCode](https://code.visualstudio.com/). 

2. Ajoutez les énoncés avec **l’intention** que vous voulez voir prédire dans le test. Pour ce tutoriel, pour simplifier, prenez les énoncés dans `HomeAutomation.TurnOn` et `HomeAutomation.TurnOff`, et permutez le texte `on` et `off` dans les énoncés. Pour l’intention `None`, ajoutez deux énoncés qui ne font pas partie du [domaine](luis-glossary.md#domain) (sujet). 

    Afin de comprendre la relation entre les résultats du test par lots et le JSON par lot, ajoutez uniquement six intentions.

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        }
    ]
    ```

## <a name="run-the-batch"></a>Exécuter le test par lot
1. Sélectionnez **Test** dans la barre de navigation supérieure. 

    ![Sélectionner Test dans la barre de navigation](./media/luis-tutorial-batch-testing/test-1.png)

2. Sélectionnez le **panneau Test par lot** dans le panneau de droite. 

    ![Sélectionner le panneau Test par lot](./media/luis-tutorial-batch-testing/test-2.png)

3. Sélectionnez **Importer le jeu de données**.

    ![Sélectionner Importer le jeu de données](./media/luis-tutorial-batch-testing/test-3.png)

4. Choisissez l’emplacement du fichier `homeauto-batch-1.json` dans le système de fichiers.

5. Nommez le jeu de données `set 1`.

    ![Sélectionner un fichier](./media/luis-tutorial-batch-testing/test-4.png)

6. Sélectionnez le bouton **Exécuter**. Attendez la fin du test.

    ![Sélectionner Exécuter](./media/luis-tutorial-batch-testing/test-5.png)

7. Sélectionnez **Afficher les résultats**.

    ![Afficher les résultats](./media/luis-tutorial-batch-testing/test-6.png)

8. Passez en revue les résultats dans le graphe et la légende.

    ![Résultats du test par lot](./media/luis-tutorial-batch-testing/batch-result-1.png)

## <a name="review-batch-results"></a>Passer en revue les résultats du test par lot
Les résultats du test par lot sont divisés en deux sections. La section supérieure contient le graphe et la légende. La section inférieure affiche les énoncés lorsque vous sélectionnez un nom de zone du graphe.

Toutes les erreurs sont indiquées en rouge. Le graphe est divisé en quatre sections, avec deux sections affichées en rouge. **Il s’agit des sections sur lesquelles vous devez vous concentrer**. 

La section supérieure droite indique que le test a prédit de façon incorrecte l’existence d’une intention ou d’une entité. La section inférieure gauche indique que le test a prédit de façon incorrecte l’absence d’une intention ou d’une entité.

### <a name="homeautomationturnoff-test-results"></a>Résultat du test HomeAutomation.TurnOff
Dans la légende, sélectionnez l’intention `HomeAutomation.TurnOff`. Celle-ci est accompagnée d’une icône de réussite verte à gauche de son nom dans la légende. Il n’y a pas d’erreurs pour cette intention. 

![Résultats du test par lot](./media/luis-tutorial-batch-testing/batch-result-1.png)

### <a name="homeautomationturnon-and-none-intents-have-errors"></a>Les intentions HomeAutomation.TurnOn et None ne contiennent pas d’erreurs
Les deux autres intentions comportent des erreurs, ce qui signifie que les prédictions du test ne correspondaient pas aux attentes du fichier par lot. Sélectionnez l’intention `None` dans la légende pour passer en revue la première erreur. 

![Intention None](./media/luis-tutorial-batch-testing/none-intent-failures.png)

Les échecs s’affichent dans le graphe dans les sections en rouge : **Faux positif** et **Faux négatif**. Sélectionnez le nom de section **Faux négatif** dans le graphe pour afficher les énoncés ayant échoué sous le graphe. 

![Échecs faux négatifs](./media/luis-tutorial-batch-testing/none-intent-false-negative.png)

L’énoncé défectueux, `help`, était attendu en tant qu’intention `None`, mais le test a prédit l’intention `HomeAutomation.TurnOn`.  

Il existe deux échecs, l’un dans HomeAutomation.TurnOn et l’autre dans None. Les deux ont été causés par l’énoncé `help`, car il n’a pas répondu aux attentes dans None et il constitue une correspondance inattendue pour l’intention HomeAutomation.TurnOn. 

Pour déterminer la raison de l’échec des énoncés `None`, passez en revue les énoncés actuellement dans `None`. 

## <a name="review-none-intents-utterances"></a>Passer en revue les énoncés de l’intention None

1. Fermez le panneau **Test** en sélectionnant le bouton **Tester** dans la barre de navigation supérieure. 

2. Sélectionnez **Générer** dans le volet de navigation supérieur. 

3. Sélectionnez l’intention **None** dans la liste des intentions.

4. Sélectionnez CTRL+E pour afficher la vue de jeton des énoncés 
    
    |Énoncés de l’intention None|Scores de prédictions|
    |--|--|
    |« decrease temperature for me please » (baisser la température pour moi s’il vous plaît)|0.44|
    |« dim kitchen lights to 25. » (tamiser les lumières de la cuisine sur 25)|0.43|
    |« lower your volume » (réduire le volume)|0.46|
    |« turn on the internet in my bedroom please » (activer Internet dans ma chambre s’il vous plaît)|0.28|

## <a name="fix-none-intents-utterances"></a>Corriger les énoncés de l’intention None
    
Les énoncés dans `None` sont censés être en dehors du domaine de l’application. Ces énoncés sont relatifs à HomeAutomation, donc ils se trouvent dans la mauvaise intention. 

LUIS donne également aux énoncés un score de prédiction inférieur à 50 % (<0.50). Si vous examinez les énoncés dans les deux autres intentions, vous verrez des scores de prédiction beaucoup plus élevés. Si LUIS présente des scores faibles pour les exemples d’énoncés, cela indique que les énoncés sont confus pour LUIS entre l’intention actuelle et d’autres intentions. 

Pour corriger l’application, les énoncés actuellement dans l’intention `None` doivent être déplacés vers l’intention correcte et l’intention `None` a besoin de nouvelles intentions appropriées. 

Trois des énoncés dans l’intention `None` sont destinés à réduire les paramètres de l’appareil d’automatisation. Ils utilisent des mots tels que `dim`, `lower` ou `decrease`. Le quatrième énoncé demande d’activer Internet. Étant donné que les quatre énoncés concernent la mise sous tension ou la modification du degré d’alimentation d’un appareil, ils doivent être déplacés vers l’intention `HomeAutomation.TurnOn`. 

Il s’agit d’une solution. Vous pouvez également créer une nouvelle intention `ChangeSetting` et déplacer les énoncés utilisant les mots « tamiser », « baisser » et « réduire » dans cette nouvelle intention. 

## <a name="fix-the-app-based-on-batch-results"></a>Corriger l’application en fonction des résultats du test par lot
Déplacez les quatre énoncés vers l’intention `HomeAutomation.TurnOn`. 

1. Cochez la case au-dessus de la liste des énoncés afin de tous les sélectionner. 

2. Dans la liste déroulante **Réaffecter l’intention**, sélectionnez `HomeAutomation.TurnOn`. 

    ![Déplacer des énoncés](./media/luis-tutorial-batch-testing/move-utterances.png)

    Une fois que les quatre énoncés sont réaffectés, la liste des énoncés pour l’intention `None` est vide.

3. Ajoutez quatre nouvelles intentions pour l’intention None :

    ```
    "fish"
    "dogs"
    "beer"
    "pizza"
    ```

    Ces énoncés sont définitivement en dehors du domaine de HomeAutomation. Lorsque vous entrez chaque énoncé, regardez son score. Le score peut être faible, voire même très faible (avec une zone rouge l’entourant). Après avoir formé l’application, à l’étape 8, le score sera beaucoup plus élevé. 

7. Supprimez les étiquettes en sélectionnant l’étiquette bleue dans l’énoncé et sélectionnez **Supprimer l’étiquette**.

8. Sélectionnez **Former** dans la barre de navigation supérieure droite. Le score de chaque énoncé est beaucoup plus élevé. Tous les scores de l’intention `None` doivent maintenant être supérieurs à 0.80. 

## <a name="verify-the-fix-worked"></a>Vérifier que la correction a fonctionné
Afin de vérifier que les énoncés dans le test par lot sont correctement prédits pour l’intention **None**, réexécutez le test par lot.

1. Sélectionnez **Test** dans la barre de navigation supérieure. 

2. Sélectionnez le **panneau Test par lot** dans le panneau de droite. 

3. Sélectionnez les points de suspension (...) à droite du nom du lot et sélectionnez **Exécuter le jeu de données**. Attendez la fin du test par lot.

    ![Exécuter le jeu de données](./media/luis-tutorial-batch-testing/run-dataset.png)

4. Sélectionnez **Afficher les résultats**. Des icônes vertes doivent s’afficher à gauche du nom des intentions. Avec le bon filtre défini sur l’intention `HomeAutomation.Turnoff`, sélectionnez le vert point dans le panneau supérieur droit le plus proche du centre du graphe. Le nom de l’énoncé s’affiche dans la table sous le graphe. Le score de `breezeway off please` est très faible. Une activité facultative consiste à ajouter plus d’énoncés à l’intention pour augmenter ce score. 

    ![Exécuter le jeu de données](./media/luis-tutorial-batch-testing/turnoff-low-score.png)

<!--
    The Entities section of the legend may have errors. That is the next thing to fix.

## Create a batch to test entity detection
1. Create `homeauto-batch-2.json` in a text editor such as [VSCode](https://code.visualstudio.com/). 

2. Utterances have entities identified with `startPos` and `endPost`. These two elements identify the entity before [tokenization](luis-glossary.md#token), which happens in some [cultures](luis-supported-languages.md#tokenization) in LUIS. If you plan to batch test in a tokenized culture, learn how to [extract](luis-concept-data-extraction.md#tokenized-entity-returned) the non-tokenized entities.

    Copy the following JSON into the file:

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 4
            }
          ]
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Operation",
              "startPos": 7,
              "endPos": 17
            }
          ]
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 9
            }
          ]
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Device",
              "startPos": 0,
              "endPos": 10
            }
          ]
        }
      ]
    ```

3. Import the batch file, following the [same instructions](#run-the-batch) as the first import, and name the dataset `set 2`. Run the test.

## Possible entity errors
Since the intents in the right-side filter of the test panel still pass the test, this section focuses on correct entity identification. 

Entity testing is diferrent than intents. An utterance will have only one top scoring intent, but it may have several entities. An utterance's entity may be correctly identified, may be incorrectly identified as an entity other than the one in the batch test, may overlap with other entities, or not identified at all. 

## Review entity errors
1. Select `HomeAutomation.Device` in the filter panel. The chart changes to show a single false positive and several true negatives. 

2. Select the False positive section name. The utterance for this chart point is displayed below the chart. The labeled intent and the predicted intent are the same, which is consistent with the test -- the intent prediction is correct. 

    The issue is that the HomeAutomation.Device was detected but the batch expected HomeAutomation.Room for the utterance "coffee bar off please". `Coffee bar` could be a room or a device, depending on the environment and context. As the model designer, you can either enforce the selection as `HomeAutomation.Room` or change the batch file to use `HomeAutomation.Device`. 

    If you want to reinforce that coffee bar is a room, you nee to add an utterances to LUIS that help LUIS decide a coffee bar is a room. 

    The most direct route is to add the utterance to the intent but that to add the utterance for every entity detection error is not the machine-learned solution. Another fix would be to add an utterance with `coffee bar`.

## Add utterance to help extract entity
1. Select the **Test** button on the top navigation to close the batch test panel.

2. On the `HomeAutomation.TurnOn` intent, add the utterance, `turn coffee bar on please`. The uttterance should have all three entities detected after you select enter. 

3. Select **Train** on the top navigation panel. Wait until training completes successfully.

3. Select **Test** on the top navigation panel to open the Batch testing pane again. 

4. If the list of datasets is not visible, select **Back to list**. Select the three dots (...) at the end of `Set 2` and select `Run Dataset`. Wait for the test to complete.

5. Select **See results** to review the test results.

6. 
-->
## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les exemples d’énoncés](luis-how-to-add-example-utterances.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions