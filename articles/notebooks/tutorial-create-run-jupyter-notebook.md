---
title: 'Tutoriel : créer et exécuter un notebook Jupyter - Azure Notebooks Preview'
description: Apprenez à créer et exécuter un notebook Jupyter dans Azure Notebooks Preview qui illustre le processus de régression linéaire en science des données.
ms.topic: tutorial
ms.date: 01/11/2019
ms.custom: tracking-python
ms.openlocfilehash: fd7fc324894595a991074c6d83ffc0f9fa3cff75
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85834129"
---
# <a name="tutorial-create-and-run-a-jupyter-notebook-with-python"></a>Tutoriel : Créer et exécuter un notebook Jupyter avec Python

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Ce tutoriel vous guide tout au long du processus d’utilisation d’Azure Notebooks pour créer un notebook Jupyter complet qui illustre une régression linéaire simple. Au cours de ce tutoriel, vous vous familiarisez avec l’interface utilisateur du notebook Jupyter, ce qui inclut la création de différentes cellules, l’exécution des cellules et la présentation du notebook sous forme de diaporama.

Vous trouverez le notebook terminé sur [GitHub - Exemples Azure Notebooks](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps). Toutefois, comme ce tutoriel commence avec un nouveau projet et un notebook vide, vous pouvez assister à sa création étape par étape.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un notebook de projet avec des exemples de données
> * Utiliser l’interface du notebook pour créer divers types de cellules
> * Exécuter le bloc-notes
> * Enregistrer le notebook
> * Déboguer le notebook dans Visual Studio Code

## <a name="create-the-project"></a>Créer le projet

1. Accédez à [Azure Notebooks](https://notebooks.azure.com) et connectez-vous. (Pour plus d’informations, consultez la rubrique [Démarrage rapide sur la connexion à Azure Notebooks](quickstart-sign-in-azure-notebooks.md).)

1. En haut de votre page de profil public, sélectionnez **Mes projets** :

    ![Lien Mes projets en haut de la fenêtre du navigateur](media/quickstarts/my-projects-link.png)

1. Dans la page **Mes projets**, sélectionnez **+ Nouveau projet** (raccourci clavier : n). Le bouton peut apparaître uniquement sous la forme **+** si la fenêtre du navigateur est étroite :

    ![Commande Nouveau projet dans la page Mes projets](media/quickstarts/new-project-command.png)

1. Dans la fenêtre contextuelle **Créer un projet** qui s’affiche, entrez ou définissez les détails suivants, puis sélectionnez **Créer** :

   - **Nom du projet** : Exemple de régression linéaire - Cricket Chirps
   - **ID de projet** : linear-regression-example
   - **Projet public** : (désactivé)
   - **Créer un fichier README.md** : (désactivé)

1. Après quelques instants, Azure Notebooks vous dirige vers le nouveau projet.

## <a name="create-the-data-file"></a>Créer le fichier de données

Le modèle de régression linéaire que vous créez dans le notebook trace des données à partir d’un fichier dans votre projet appelé *cricket_chirps.csv*. Vous pouvez créer ce fichier en le copiant à partir de [GitHub - Exemples Azure Notebooks](https://github.com/Microsoft/AzureNotebooks/tree/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps), ou en entrant les données directement. Les sections suivantes décrivent les deux approches.

### <a name="upload-the-data-file"></a>Charger le fichier de données

1. Dans le tableau de bord de votre projet dans Azure Notebooks, sélectionnez **Charger** > **À partir de l’URL**
1. Dans la fenêtre contextuelle, entrez l’URL suivante dans **URL du fichier** et *cricket_chirps.csv* dans **Nom de fichier**, puis sélectionnez **Terminé**.

    ```url
    https://raw.githubusercontent.com/Microsoft/AzureNotebooks/master/Samples/Linear%20Regression%20-%20Cricket%20Chirps/cricket_chirps.csv
    ```

1. Le fichier *cricket_chirps.csv* doit maintenant apparaître dans la liste des fichiers de votre projet :

    ![Fichier CSV nouvellement créé qui s’affiche dans la liste des fichiers du projet](media/tutorial/csv-file-in-project.png)

### <a name="create-a-file-from-scratch"></a>Créer un fichier à partir de zéro

1. Dans le tableau de bord de votre projet dans Azure Notebooks, sélectionnez **+ Nouveau** > **Fichier vide**
1. Un champ s’affiche dans la liste des fichiers du projet. Entrez *cricket_chirps.csv* et appuyez sur Entrée.
1. Cliquez avec le bouton droit sur *cricket_chirps.csv* et sélectionnez **Modifier le fichier**.
1. Dans l’éditeur qui s’affiche, entrez les données suivantes :

    ```csv
    Chirps/Minute,Temperature
    20,88.6
    16,71.6
    19.8,93.3
    18.4,84.3
    17.1,80.6
    15.5,75.2
    14.7,69.7
    17.1,82
    15.4,69.4
    16.2,83.3
    15,79.6
    17.2,82.6
    16,80.6
    17,83.5
    14.4,76.3
    ```

1. Sélectionnez **Enregistrer le fichier** pour enregistrer le fichier et revenir au tableau de bord du projet.

## <a name="install-project-level-packages"></a>Installer des packages au niveau du projet

Au sein d’un notebook, vous pouvez toujours utiliser des commandes telles que `!pip install` dans une cellule de code pour installer les packages nécessaires. Toutefois, ces commandes sont exécutées chaque fois que vous exécutez les cellules de code du notebook et peuvent prendre beaucoup de temps. Pour cette raison, vous pouvez installer à la place des packages au niveau du projet à l’aide d’un fichier `requirements.txt`.

1. Utilisez la procédure décrite dans [Créer un fichier à partir de zéro](#create-a-file-from-scratch) pour créer un fichier nommé `requirements.txt` avec le contenu suivant :

    ```text
    matplotlib==3.0.0
    numpy==1.15.0
    pandas==0.23.4
    scikit-learn==0.20.0
    ```

    Vous pouvez également charger un fichier `requirements.txt` à partir de votre ordinateur local si vous préférez, comme décrit dans [Charger le fichier de données](#upload-the-data-file).

1. Dans le tableau de bord du projet, sélectionnez **Paramètres du projet**.
1. Dans la fenêtre contextuelle qui s’affiche, sélectionnez l’onglet **Environnement**, puis **+ Ajouter**.
1. Dans le premier contrôle de liste déroulante (l’opération) sous **Étapes de configuration de l’environnement**, choisissez **Requirements.txt**.
1. Dans le deuxième contrôle de liste déroulante (le nom de fichier), choisissez *requirements.txt* (le fichier que vous avez créé).
1. Dans le troisième contrôle de liste déroulante (la version de Python), choisissez **Python Version 3.6** (Version 3.6 de Python).
1. Sélectionnez **Enregistrer**.

![Onglet Environnement dans Paramètres du projet qui spécifie un fichier requirements.txt](media/tutorial/tutorial-requirements-txt.png)

Avec cette étape de configuration en place, n’importe quel notebook que vous exécutez dans le projet s’exécute dans un environnement où ces packages sont installés.

## <a name="create-and-run-a-notebook"></a>Créer et exécuter un notebook

Une fois le fichier de données prêt et l’environnement de projet défini, vous pouvez désormais créer et ouvrir le notebook.

1. Dans le tableau de bord du projet, sélectionnez **+ Nouveau** > **Notebook**.
1. Dans la fenêtre contextuelle, entrez *Exemple de régression linéaire - Cricket Chirps.ipynb* pour **Nom de l’élément**, choisissez **Python 3.6** pour le langage, puis sélectionnez **Nouveau**.
1. Une fois que le nouveau notebook s’affiche dans la liste des fichiers, sélectionnez-le pour le démarrer. Un nouvel onglet de navigateur s’ouvre automatiquement.
1. Comme vous avez un fichier *requirements.txt* dans les paramètres d’environnement, vous voyez le message « Waiting for your container to finish being prepared » (Attente de la fin de la préparation de votre conteneur). Vous pouvez sélectionner **OK** pour fermer le message et continuer à travailler dans le notebook ; toutefois, vous ne pouvez pas exécuter les cellules de code tant que l’environnement n’est pas entièrement configuré.
1. Le notebook s’ouvre dans l’interface Jupyter avec une seule cellule de code vide en tant que valeur par défaut.

    [![Vue initiale d’un nouveau notebook dans Azure Notebooks](media/tutorial/tutorial-new-notebook.png)](media/tutorial/tutorial-new-notebook.png#lightbox)

## <a name="tour-the-notebook-interface"></a>Visiter l’interface du notebook

Quand le notebook est en cours d’exécution, vous pouvez ajouter le code et les cellules Markdown, exécuter ces cellules et gérer le fonctionnement du notebook. Toutefois, il est important tout d’abord de prendre quelques minutes pour vous familiariser avec l’interface. Pour obtenir une documentation complète, sélectionnez la commande de menu **Help** > **Notebook Help** (Aide, Aide sur le notebook).

En haut de la fenêtre, vous voyez les éléments suivants :

(A) Nom de votre notebook, que vous pouvez modifier en cliquant dessus.
(B) Boutons pour accéder au projet contenant et au tableau de bord de vos projets, qui ouvrent de nouveaux onglets dans votre navigateur.
(C) Menu avec des commandes pour utiliser le notebook.
(D) Barre d’outils avec des raccourcis pour les opérations courantes.
(E) Canevas de modification contenant des cellules.
(F) Indicateur qui affiche si le notebook est approuvé (la valeur par défaut est **Non approuvé**).
(G) Noyau utilisé pour exécuter le notebook avec un indicateur d’activité.

[![Zones d’interface utilisateur principales de l’interface Jupyter](media/tutorial/tutorial-notebook-ui.png)](media/tutorial/tutorial-notebook-ui.png#lightbox)

Jupyter propose une visite intégrée des principaux éléments d’interface utilisateur. Démarrez la visite en sélectionnant la commande **Help** > **User Interface Tour** (Aide, Visite de l’interface utilisateur) et en cliquant sur plusieurs fenêtres contextuelles à la suite.

Les groupes de commandes de menu sont les suivants :

| Menu | Description |
| --- | --- |
| Fichier | Commandes pour gérer le fichier notebook, notamment les commandes pour créer et copier des notebooks, afficher un aperçu avant impression et télécharger le notebook sous divers formats. |
| Modifier | Commandes classiques pour couper, copier et coller des cellules, rechercher et remplacer des valeurs, gérer les pièces jointes de cellule et insérer des images.  |
| Affichage | Commandes pour contrôler la visibilité de différentes parties de l’interface utilisateur Jupyter. |
| Insérer | Commandes pour insérer une nouvelle cellule au-dessus ou en dessous de la cellule active. Vous utilisez ces commandes fréquemment lors de la création d’un notebook. |
| Cellule | Les différentes commandes **Exécuter** exécutent une ou plusieurs cellules dans différentes combinaisons. Les commandes **Type de cellule** changent le type d’une cellule entre **Code**, **Markdown** et **Raw NBConvert** (texte brut). Les commandes **Current Outputs** (Sorties actuelles) et **All Outputs** (Toutes les sorties) contrôlent le mode d’affichage de la sortie du code exécuté et incluent une commande pour effacer toutes les sorties. |
| Noyau | Commandes pour gérer le mode d’exécution du code dans le noyau, avec **Modifier le noyau** pour changer la version de Python ou le langage utilisé pour exécuter le notebook. |
| Données | Commandes pour charger et télécharger des fichiers à partir du projet ou de la session. Consultez [Utiliser des fichiers de données de projets](work-with-project-data-files.md) |
| Widgets | Commandes pour gérer les [widgets Jupyter](https://ipywidgets.readthedocs.io/en/stable/examples/Widget%20Basics.html), qui offrent des fonctionnalités supplémentaires pour la visualisation, le mappage et le traçage.|
| Aide | Commandes qui fournissent une aide et de la documentation pour l’interface Jupyter. |

La plupart des commandes de la barre d’outils ont des commandes de menu équivalentes. Une exception est le bouton **Enter/Exit RISE Slideshow** (Entrer dans/Quitter le diaporama RISE), qui est décrit dans [Partager et présenter des notebooks](present-jupyter-notebooks-slideshow.md).

Vous utilisez plusieurs de ces commandes quand vous remplissez le notebook dans les sections qui suivent.

## <a name="create-a-markdown-cell"></a>Créer une cellule Markdown

1. Cliquez dans la première cellule vide indiquée sur le canevas de notebook. Par défaut, une cellule est de type **Code**, ce qui signifie qu’elle est destinée à contenir du code exécutable pour le noyau sélectionné (Python, R ou F#). Le type actuel est indiqué dans la liste déroulante des types dans la barre d’outils :

    ![Liste déroulante des types de cellules dans la barre d’outils](media/tutorial/tutorial-cell-type-drop-down.png)

1. Remplacez le type de cellule par **Markdown** à l’aide de la liste déroulante de la barre d’outils ; sinon, utilisez la commande de menu **Cellule** > **Type de cellule** > **Markdown** :

    ![Commande de menu Type de cellule](media/tutorial/tutorial-cell-type-menu.png)

1. Cliquez dans la cellule pour commencer à modifier, puis entrez le code Markdown suivant :

    ```markdown
    # Example Linear Regression

    This notebook contains a walkthrough of a simple linear regression. The data, obtained from [college.cengage.com](https://college.cengage.com/mathematics/brase/understandable_statistics/7e/students/datasets/slr/frames/frame.html), relates the rate of cricket chirps to temperature from *The Song of Insects*, by Dr. G. W. Pierce, Harvard College Press.

    In this example we're using the count of chirps per minute as the independent varible to then predict the dependent variable, temperature. In short, we're using a little data science to make ourselves a cricket thermometer. (You could also reverse the data and use temperature to predict the number of chirps, but it's more fun to use crickets as the thermometer itself!)

    The methods shown in this example follow what's presented in the Udemy course, [Machine Learning A to Z](https://www.udemy.com/machinelearning/learn/v4/).

    A lovely aspect of Notebooks is that you can use Markdown cells to explain what the code is doing rather than code comments. There are several benefits to doing so:

    - Markdown allows for richer text formatting, like *italics*, **bold**, `inline code`, hyperlinks, and headers.
    - Markdown cells automatically word wrap whereas code cells do not. Code comments typically use explicit line breaks for formatting, but that's not necessary in Markdown.
    - Using Markdown cells makes it easier to run the Notebook as a slide show.
    - Markdown cells help you remove lengthy comments from the code, making the code easier to scan.

    When you run a code cell, Jupyter executes the code; when you run a Markdown cell, Jupyter renders all the formatting into text that's suitable for presentation.
    ```

1. Pour afficher la syntaxe Markdown en HTML pour le navigateur, sélectionnez la commande **Exécuter** dans la barre d’outils ou utilisez la commande **Cellule** > **Run Cells** (Exécuter des cellules). Le code Markdown pour la mise en forme et les liens apparaissent maintenant comme vous le souhaitez dans un navigateur.

1. Quand vous exécutez la dernière cellule dans le notebook, Jupyter crée automatiquement une cellule sous celle que vous avez exécutée. Placez davantage de code Markdown dans cette cellule en répétant les étapes décrites dans cette section avec le code Markdown suivant :

    ```markdown
    ## Install packages using pip or conda

    Because the code in your notebook likely uses some Python packages, you need to make sure the Notebook environment contains those packages. You can do this directly within the notebook in a code block that contains the appropriate pip or conda commands prefixed by `!`:

    \```
    !pip install <pkg name>

    !conda install <pkg name> -y
    \```
    ```

1. Pour modifier à nouveau le code Markdown, double-cliquez dans la cellule affichée. Pour un nouveau rendu HTML après avoir apporté des modifications, exécutez la cellule.

## <a name="create-a-code-cell-with-commands"></a>Créer une cellule de code avec des commandes

Comme décrit dans la cellule Markdown précédente, vous pouvez inclure des commandes directement dans le notebook. Vous pouvez utiliser des commandes pour installer des packages, exécuter curl ou wget pour récupérer des données, ou effectuer toute autre opération. Les notebooks Jupyter s’exécutent efficacement au sein d’une machine virtuelle Linux et vous pouvez utiliser l’intégralité du jeu de commandes Linux.

1. Entrez les commandes ci-dessous dans la cellule de code qui s’affiche une fois que vous avez utilisé **Exécuter** dans la cellule Markdown précédente. Si vous ne voyez pas une nouvelle cellule, créez-en une avec **Insérer** > **Insert Cell Below** (Insérer une cellule ci-dessous) ou utilisez le bouton **+** dans la barre d’outils.

    ```bash
    !pip install numpy
    !pip install matplotlib
    !pip install pandas
    !pip install sklearn
    ```

1. Avant d’exécuter la cellule, créez-en une avec le bouton **+** de la barre d’outils, affectez-lui la valeur Markdown et entrez la description suivante :

    ```markdown
    Note that when you run a code block that contains install commands, and also those with `import` statements, it make take the notebooks a little time to complete the task. To the left of the code block you see `In [*]` to indicate that execution is happening. The Notebook's kernel on the upper right also shows a filled-in circle to indicate "busy."
    ```

1. Sélectionnez la commande **Cellule** > **Exécuter tout** pour exécuter toutes les cellules dans le notebook. Notez que les cellules Markdown sont affichées au format HTML, et la commande est exécutée dans le noyau, et examinez l’indicateur de noyau, comme décrit dans le code Markdown lui-même :

    ![Indicateur d’occupation pour le noyau de notebook](media/tutorial/tutorial-kernel-busy.png)

1. L’exécution de toutes les commandes `pip install` prend également un peu de temps et, comme vous avez déjà installé ces packages dans l’environnement de projet (et comme ils sont aussi inclus dans Azure Notebooks par défaut), vous voyez de nombreux messages qui indiquent « Requirement already satisfied » (Exigence déjà satisfaite). Toutes ces sorties pouvant représenter une gêne visuelle, sélectionnez cette cellule (par un seul clic), puis utilisez **Cellule** > **Cell Outputs** > **Toggle** (Sorties de cellules > Basculer) pour les masquer. Vous pouvez également utiliser la commande **Effacer** dans ce même sous-menu pour supprimer complètement les sorties.

    La commande **Basculer** masque uniquement la sortie la plus récente de la cellule ; si vous réexécutez la cellule, la sortie s’affiche à nouveau.

1. Comme les packages sont installés dans l’environnement de projet, commentez les commandes `! pip install` avec `#` ; de cette manière, elles peuvent rester dans le notebook en tant que documents pédagogiques, mais sans prendre du temps pour s’exécuter ni produire de sortie inutile. Dans ce cas, la conservation des commandes commentées dans le notebook indique également les dépendances du notebook.

    ```bash
    # !pip install numpy
    # !pip install matplotlib
    # !pip install pandas
    # !pip install sklearn
    ```

## <a name="create-the-remaining-cells"></a>Créer les cellules restantes

Pour remplir le reste du notebook, vous créez ensuite une série de cellules Markdown et de code. Pour chaque cellule listée ci-dessous, créez tout d’abord la cellule, définissez le type, puis collez le contenu.

Bien que vous puissiez attendre pour exécuter le notebook une fois que vous avez créé chaque cellule, il est intéressant d’exécuter chaque cellule dès sa création. Les cellules n’affichent pas toutes une sortie ; si vous ne voyez aucune erreur, supposez que la cellule a été exécutée normalement.

Chaque cellule de code repose sur le code qui a été exécuté dans les cellules précédentes et, si vous oubliez d’exécuter l’une des cellules, les cellules ultérieures peuvent produire des erreurs. Si vous constatez que vous avez oublié d’exécuter une cellule, essayez d’utiliser **Cellule** > **Run All Above** (Exécuter tout ci-dessus) avant d’exécuter la cellule active.

Si vous voyez des résultats inattendus (ce qui arrivera probablement !), vérifiez que chaque cellule est définie sur « Code » ou « Markdown » en fonction des besoins. Par exemple, une erreur « Syntaxe non valide » se produit généralement quand vous avez entré Markdown dans la cellule Code.

1. Cellule Markdown :

    ```markdown
    ## Import packages and prepare the dataset

    In this example we're using numpy, pandas, and matplotlib. Data is in the file cricket_chirps.csv. Because this file is in the same project as this present Notebook, we can just load it using a relative pathname.
    ```

1. Cellule de code ; quand elle est exécutée, affiche le contenu de la table en tant que sortie. Vous pouvez supprimer la sortie en commentant l’instruction `print`.

    ```python
    import numpy as np
    import pandas as pd

    dataset = pd.read_csv('cricket_chirps.csv')
    print(dataset)
    X = dataset.iloc[:, :-1].values  # Matrix of independent variables -- remove the last column in this data set
    y = dataset.iloc[:, 1].values    # Matrix of dependent variables -- just the last column (1 == 2nd column)
    ```

    > [!Note]
    > Il est possible que ce code génère des avertissements de type « Taille de numpy.dtype modifiée », qui peuvent être ignorés sans problème.

1. Cellule Markdown :

    ```markdown
    Next, split the dataset into a Training set (2/3rds) and Test set (1/3rd). We don't need to do any feature scaling because there is only one column of independent variables, and packages typically do scaling for you.
    ```

1. Cellule de code ; quand elle est exécutée, cette cellule n’a aucune sortie.

    ```python
    from sklearn.model_selection import train_test_split

    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 1/3, random_state = 0)
    ```

1. Cellule Markdown :

    ```markdown
    ## Fit the data to the training set

    "Fitting" the data to a training set means making the line that describes the relationship between the independent and the dependent variables. With a simple data set like we're using here, you can visualize the line on a simple x-y plot: the x-axis is the independent variable (chirp count in this example), and the y-axis is the independent variable (temperature). Fitting the data means plotting all the points in the training set, then drawing the best-fit line through that data.

    With two independent variables you can imagine a three-dimensional plot with a line fitted to the data. At three or more independent variables, however, it's no longer easy to visualize the fit, but you get the idea. In the end, it's all just mathematics, which a computer can handle easily without having to form a mental picture!

    The regressor's `fit` method here creates the line, which algebraically is of the form `y = x*b1 + b0`, where b1 is the coefficient or slope of the line (which you can get to through `regressor.coef_`), and b0 is the intercept of the line at x=0 (which you can get to through `regressor.intercept_`).
    ```

1. Cellule de code ; quand elle est exécutée, cette cellule produit la sortie `LinearRegression(copy_X=True, fit_intercept=True, n_jobs=None,normalize=False)`.

    ```python
    from sklearn.linear_model import LinearRegression

    regressor = LinearRegression()    # This object is the regressor, that does the regression
    regressor.fit(X_train, y_train)   # Provide training data so the machine can learn to predict using a learned model.
    ```

1. Cellule Markdown :

    ```markdown
    ## Predict the results

    With the regressor in hand, we can predict the test set results using its `predict` method. That method takes a vector of independent variables for which you want predictions.

    Because the regressor is fit to the data by virtue of `coef_` and `intercept_`, a prediction is the result of `coef_ * x + intercept_`. (Indeed, `predict(0)` returns `intercept_` and `predict(1)` returns `intercept_ + coef_`.)

    In the code, the `y_test` matrix (from when we split the set) contains the real observations. `y_pred` assigned here contains the predictions for the same `X_test` inputs. It's not expected that the test or training points exactly fit the regression; the regression is trying to find the model that we can use to make predictions with new observations of the independent variables.
    ```

1. Cellule de code ; quand elle est exécutée, cette cellule affiche des résultats comme `[79.49588055 75.98873911 77.87719989 80.03544077 75.17939878]`.

    ```python
    y_pred = regressor.predict(X_test)
    print(y_pred)
    ```

1. Cellule Markdown :

    ```markdown
    It's interesting to think that all the "predictions" we use in daily life, like weather forecasts, are just regression models of some sort working with various data sets. Those models are much more complicated than what's shown here, but the idea is the same.

    Knowing how predictions work help us understand that the actual observations we would collect in the moment will always be somewhat off from the predictions: the predictions fit exactly to the model, whereas the observations typically won't.

    Of course, such systems feed new observations back into the dataset to continually improve the model, meaning that predictions should get more accurate over time.

    The challenge is determining what data to actually use. For example, with weather, how far back in time do you go? How have weather patterns been changing decade by decade? In any case, something like weather predictions will be doing things hour by hour, day by day, for things like temperature, precipitation, winds, cloud cover, etc. Radar and other observations are of course fed into the model and the predictions are reduced to mathematics.
    ```

1. Cellule Markdown :

    ```markdown
    ## Visualize the results

    The following code generates a plot: green dots are training data, red dots are test data, blue dots are predictions. Gray line is the regression itself. You see that all the blue dots are exactly on the line, as they should be, because the predictions exactly fit the model (the line).
    ```

1. Cellule de code ; quand elle est exécutée, cette cellule produit un tracé graphique. Si vous ne voyez pas le tracé la première fois (et voyez à la place une taille de figure de 640 x 480 avec un objet Axes »), réexécutez la cellule.

    ```python
    import matplotlib.pyplot as plt

    plt.scatter(X_train, y_train, color = 'green')
    plt.scatter(X_test, y_test, color = 'red')   
    plt.scatter(X_test, y_pred, color = 'blue')  # The predicted temperatures of the same X_test input.
    plt.plot(X_train, regressor.predict(X_train), color = 'gray')
    plt.title('Temperature based on chirp count')
    plt.xlabel('Chirps/minute')
    plt.ylabel('Temperature')
    plt.show()
    ```

    ![Sortie de traçage à partir du code matplotlib](media/tutorial/tutorial-plot-output.png)

1. Cellule Markdown :

    ```markdown
    ## Closing comments

    At the end of the day, when you create a model, you use training data. Then you start feeding test data (real observations) to see how well the model actually works. You may find that the model is a little inaccurate over time, in which case you retrain the model with some new data. Retraining the model means you're creating a new fit line that's used for predictions.

    This regression can be used to examine the variability of the relationship between temperature and chirp count. Of course, if the model proves too inaccurate (that is, the predictions aren't very good), then it suggests that we might need to introduce more independent variables like humidity, time of year, latitude, amount of moonlight, and so on. If you have such data, you can do separate lines regressions for each independent variable, and then do multiple regressions with combinations of independent variables. 

    Again, once you are working with more than one or two independent variables, it's much easier to use machine learning to crunch the numbers than to try to visualize it graphically.
    ```

## <a name="clear-outputs-and-rerun-all-cells"></a>Effacer les sorties et réexécuter toutes les cellules

Après avoir suivi les étapes décrites dans la section précédente pour remplir le notebook entier, vous avez créé un bloc de code d’exécution dans le contexte d’un tutoriel complet sur la régression linéaire. Cette combinaison directe de code et de texte est l’un des principaux avantages des notebooks !

Essayez maintenant de réexécuter l’intégralité du notebook :

1. Effacez toutes les sorties de cellules et données de session du noyau en sélectionnant **Noyau** > **Restart & Clear Output** (Redémarrer & Effacer la sortie). Il est toujours judicieux d’exécuter cette commande quand vous avez terminé un notebook, juste pour vous assurer que vous n’avez pas créé de dépendances étranges entre les cellules de code.

1. Réexécutez le notebook en utilisant **Cellule** > **Exécuter tout**. Notez que l’indicateur de noyau est rempli pendant l’exécution de code.

    Si l’exécution du code dure trop longtemps ou se bloque, vous pouvez arrêter le noyau à l’aide de la commande **Noyau** > **Interrompre**.

1. Faites défiler le notebook pour examiner les résultats. (Si le tracé n’apparaît toujours pas, réexécutez cette cellule.)

## <a name="save-halt-and-close-the-notebook"></a>Enregistrer, arrêter et fermer le notebook

Pendant que vous modifiez un notebook, vous pouvez enregistrer son état actuel avec la commande **Fichier** > **Save and Checkpoint** (Enregistrer et point de contrôle) ou le bouton Enregistrer de la barre d’outils. Un « point de contrôle » crée un instantané auquel vous pouvez revenir à tout moment pendant la session. Les points de contrôle vous permettent d’effectuer une série de modifications expérimentales et, si ces dernières ne fonctionnent pas, vous pouvez simplement revenir à un point de contrôle à l’aide de la commande **Fichier** > **Revert to Checkpoint** (Revenir au point de contrôle). Une autre approche consiste à créer des cellules supplémentaires et à commenter tout code que vous ne souhaitez pas exécuter ; les deux méthodes fonctionnent.

Vous pouvez également utiliser la commande **Fichier** > **Faire une copie** à tout moment pour effectuer une copie de l’état actuel du notebook dans un nouveau fichier de votre projet. Cette copie s’ouvre automatiquement dans un nouvel onglet de navigateur.

Quand vous en avez fini avec un notebook, utilisez la commande **Fichier** > **Fermer et arrêter**, qui ferme le notebook et arrête le noyau qui l’a exécuté. Azure Notebooks ferme ensuite automatiquement l’onglet de navigateur.

## <a name="debug-notebooks-using-visual-studio-code"></a>Déboguer des notebooks à l’aide de Visual Studio Code

Si les cellules de code dans votre notebook ne se comportent pas comme vous le souhaitez, vous pouvez être confronté à des bogues de code ou d’autres erreurs. Toutefois, à part l’utilisation d’instructions `print` pour afficher la valeur des variables, un environnement Jupyter type n’offre aucune fonctionnalité de débogage.

Cela étant, vous pouvez télécharger le fichier *.ipynb* du notebook, puis l'ouvrir dans Visual Studio Code à l’aide de l’extension Python. L’extension importe directement un notebook sous forme de fichier de code unique, ce qui vous permet de conserver vos cellules Markdown dans les commentaires. Après avoir importé le notebook, vous pouvez exécuter le débogueur Visual Studio Code pour parcourir votre code, définir des points d’arrêt, examiner l’état et ainsi de suite. Une fois votre code corrigé, vous exportez le fichier *.ipynb* à partir de Visual Studio Code et vous le rechargez dans Azure Notebooks.

Pour plus d’informations, consultez [Déboguer un notebook Jupyter](https://code.visualstudio.com/docs/python/jupyter-support#debug-a-jupyter-notebook) dans la documentation de Visual Studio Code.

Consultez également [Visual Studio Code - Prise en charge de Jupyter](https://code.visualstudio.com/docs/python/jupyter-support) pour découvrir plus fonctionnalités Visual Studio Code pour les notebooks Jupyter.

## <a name="next-steps"></a>Étapes suivantes

- [Explorer des exemples de notebooks](azure-notebooks-samples.md)

Articles de guide pratique :

- [Créer et cloner des projets](create-clone-jupyter-notebooks.md)
- [Configurer et gérer des projets](configure-manage-azure-notebooks-projects.md)
- [Installer des packages à partir d’un notebook](install-packages-jupyter-notebook.md)
- [Présenter un diaporama](present-jupyter-notebooks-slideshow.md)
- [Utiliser des fichiers de données](work-with-project-data-files.md)
- [Accéder aux ressources de données](access-data-resources-jupyter-notebooks.md)
- [Utiliser Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
