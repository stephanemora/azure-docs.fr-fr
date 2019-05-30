---
title: Consigner les métriques pendant les exécutions d’apprentissage
titleSuffix: Azure Machine Learning service
description: Découvrez comment ajouter la journalisation à votre script d’entraînement, envoyer l’expérimentation, vérifier la progression d’une tâche en cours d’exécution et afficher les résultats d’une exécution. Vous pouvez effectuer le suivi de vos expériences et surveiller les mesures pour améliorer le processus de création de modèle.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: d3cbc2d5be1f7addf833162b23c5db0786e9d361
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297474"
---
# <a name="log-metrics-during-training-runs-in-azure-machine-learning"></a>Métriques de journal pendant la formation s’exécute dans Azure Machine Learning

Dans cet article, découvrez comment ajouter la journalisation à votre script de formation, envoyer une exécution d’expérience, surveillez l’exécution et afficher les résultats d’une exécution dans le service Azure Machine Learning. Améliorer le processus de création de modèle, par le suivi de vos expériences et de métriques de surveillance. 

## <a name="list-of-training-metrics"></a>Liste des métriques d’entraînement 

Les métriques suivantes peuvent être ajoutées à une exécution pendant l’entraînement d’une expérimentation. Pour afficher une liste plus détaillée des éléments qui peuvent être suivis lors d’une exécution, consultez la [documentation de référence sur l’exécution de la classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Type| Fonction Python | Notes|
|----|:----|:----|
|Valeurs scalaires |Fonction :<br>`run.log(name, value, description='')`<br><br>Exemple :<br>run.log("accuracy", 0.95) |Journalisez une valeur numérique ou de chaîne dans l’exécution avec le nom donné. La journalisation d’une métrique dans une exécution entraîne le stockage de cette métrique dans l’enregistrement d’exécution dans l’expérimentation.  Vous pouvez consigner la même métrique plusieurs fois pendant une exécution, le résultat étant considéré comme un vecteur de cette métrique.|
|Listes|Fonction :<br>`run.log_list(name, value, description='')`<br><br>Exemple :<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | Journalisez une liste de valeurs dans l’exécution avec le nom donné.|
|Ligne|Fonction :<br>`run.log_row(name, description=None, **kwargs)`<br>Exemple :<br>run.log_row("Y over X", x=1, y=0.4) | L’utilisation de *log_row* crée une métrique avec plusieurs colonnes, comme décrit dans kwargs. Chaque paramètre nommé génère une colonne avec la valeur spécifiée.  Vous pouvez appeler *log_row* une seule fois pour consigner un tuple arbitraire ou plusieurs fois dans une boucle pour générer une table complète.|
|Table|Fonction :<br>`run.log_table(name, value, description='')`<br><br>Exemple :<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | Journalisez un objet dictionnaire dans l’exécution avec le nom donné. |
|Images|Fonction :<br>`run.log_image(name, path=None, plot=None)`<br><br>Exemple :<br>`run.log_image("ROC", plt)` | Journalisez une image dans l’enregistrement d’exécution. Utilisez log_image pour consigner un fichier image ou un tracé matplotlib dans l’exécution.  Ces images seront visibles et comparables dans l’enregistrement d’exécution.|
|Étiqueter une exécution|Fonction :<br>`run.tag(key, value=None)`<br><br>Exemple :<br>run.tag("selected", "yes") | Étiquetez l’exécution avec une clé de chaîne et une valeur de chaîne facultative.|
|Charger un fichier ou un répertoire|Fonction :<br>`run.upload_file(name, path_or_stream)`<br> <br> Exemple :<br>run.upload_file("best_model.pkl", "./model.pkl") | Chargez un fichier sur l’enregistrement d’exécution. Les exécutions capturent automatiquement le fichier dans le répertoire de sortie spécifié, par défaut « ./outputs » pour la plupart des types d’exécutions.  Utilisez upload_file uniquement quand des fichiers supplémentaires doivent être chargés ou qu’aucun répertoire de sortie n’est spécifié. Nous vous suggérons d’ajouter `outputs` au nom afin qu’il soit chargé sur le répertoire outputs. Vous pouvez répertorier tous les fichiers qui sont associés à cet enregistrement d’exécution en appelant `run.get_file_names()`|

> [!NOTE]
> Les métriques pour les valeurs scalaires, listes, lignes et tables peuvent être de type float, integer ou string.

## <a name="start-logging-metrics"></a>Démarrer les métriques de journalisation

Si vous souhaitez suivre ou superviser votre expérimentation, vous devez ajouter du code pour démarrer la journalisation quand vous envoyez l’exécution. Voici comment déclencher l’envoi d’exécution :
* __Run.start_logging__ : ajoutez des fonctions de journalisation à votre script d’entraînement et démarrez une session de journalisation interactive dans l’expérimentation spécifiée. **start_logging** crée une exécution interactive pour une utilisation dans des scénarios tels que des notebooks. Toutes les métriques qui sont consignées pendant la session sont ajoutées à l’enregistrement d’exécution dans l’expérimentation.
* __ScriptRunConfig__ : ajoutez des fonctions de journalisation à votre script d’entraînement et chargez l’intégralité du dossier de script avec l’exécution.  **ScriptRunConfig** est une classe pour la définition des configurations pour les exécutions de script. Avec cette option, vous pouvez ajouter le code de supervision pour être informé de la fin de l’opération ou pour obtenir un widget visuel à superviser.

## <a name="set-up-the-workspace"></a>Configurer l’espace de travail
Avant d’ajouter la journalisation et d’envoyer une expérience, vous devez configurer l’espace de travail.

1. Chargez l’espace de travail. Pour en savoir plus sur la définition de la configuration de l’espace de travail, suivez les étapes de [créer un espace de travail du service Azure Machine Learning](setup-create-workspace.md#sdk).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```
  
## <a name="option-1-use-startlogging"></a>Option 1 : Utiliser start_logging

**start_logging** crée une exécution interactive pour une utilisation dans des scénarios tels que des notebooks. Toutes les métriques qui sont consignées pendant la session sont ajoutées à l’enregistrement d’exécution dans l’expérimentation.

L’exemple suivant entraîne un simple modèle Ridge sklearn localement dans un notebook Jupyter local. Pour en savoir plus sur l’envoi d’expérimentations dans différents environnements, consultez [Configurer des cibles de calcul pour l’entraînement de modèle avec le service Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Créez un script d’entraînement dans un notebook Jupyter local. 

   ```python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. Ajoutez le suivi d’expérimentation à l’aide du kit SDK du service Azure Machine Learning et chargez un modèle persistant sur l’enregistrement d’exécution de l’expérimentation. Le code suivant ajoute des balises, des journaux d’activité, puis charge un fichier de modèle sur l’exécution de l’expérimentation.

   ```python
   # Get an experiment object from Azure Machine Learning
   experiment = Experiment(workspace = ws, name = "train-within-notebook")
  
   # Create a run object in the experiment
   run = experiment.start_logging()# Log the algorithm parameter alpha to the run
   run.log('alpha', 0.03)

   # Create, fit, and test the scikit-learn Ridge regression model
   regression_model = Ridge(alpha=0.03)
   regression_model.fit(data['train']['X'], data['train']['y'])
   preds = regression_model.predict(data['test']['X'])

   # Output the Mean Squared Error to the notebook and to the run
   print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
   run.log('mse', mean_squared_error(data['test']['y'], preds))

   # Save the model to the outputs directory for capture
   joblib.dump(value=regression_model, filename='outputs/model.pkl')

   # Take a snapshot of the directory containing this notebook
   run.take_snapshot('./')

   # Complete the run
   run.complete()
  
   ```

Le script se termine par ```run.complete()```, ce qui marque l’exécution comme terminée.  Cette fonction est généralement utilisée dans les scénarios de notebooks interactifs.

## <a name="option-2-use-scriptrunconfig"></a>Option 2 : Utiliser ScriptRunConfig

[**ScriptRunConfig** ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) est une classe pour le paramètre des configurations pour le script s’exécute. Avec cette option, vous pouvez ajouter le code de supervision pour être informé de la fin de l’opération ou pour obtenir un widget visuel à superviser.

Cet exemple s’appuie sur le modèle Ridge sklearn de base ci-dessus. Il effectue un simple balayage des paramètres sur les valeurs alpha du modèle pour capturer les métriques et les modèles entraînés dans les exécutions sous l’expérimentation. L’exemple est exécuté localement dans un environnement géré par l’utilisateur. 

1. Créez un script d’entraînement `train.py`.

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. Le script `train.py` fait référence à `mylib.py`, qui permet d’obtenir la liste des valeurs alpha à utiliser dans le modèle Ridge.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Configurez un environnement local géré par l’utilisateur.

   ```python
   from azureml.core.runconfig import RunConfiguration

   # Editing a run configuration property on-fly.
   run_config_user_managed = RunConfiguration()

   run_config_user_managed.environment.python.user_managed_dependencies = True

   # You can choose a specific Python environment by pointing to a Python path 
   #run_config.environment.python.interpreter_path = '/home/user/miniconda3/envs/sdk2/bin/python'
   ```

4. Envoyez le script ```train.py``` à exécuter dans l’environnement géré par l’utilisateur. L’intégralité de ce dossier de script est envoyée pour l’entraînement, dont le fichier ```mylib.py```.

   ```python
   from azureml.core import ScriptRunConfig
  
   experiment = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory = './', script = 'train.py', run_config = run_config_user_managed)
   run = experiment.submit(src)
   ```

## <a name="manage-a-run"></a>Gérer une exécution

Le [Démarrer, surveiller et annuler les exécutions d’apprentissage](how-to-manage-runs.md) article met en évidence des flux de travail Azure Machine Learning spécifiques pour savoir comment gérer vos expériences.

## <a name="view-run-details"></a>Afficher les détails de l’exécution

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>Superviser l’exécution avec les widgets de notebook Jupyter
Quand vous utilisez la méthode **ScriptRunConfig** pour envoyer des exécutions, vous pouvez vérifier la progression de l’exécution avec un widget de notebook Jupyter. Comme l’envoi de l’exécution, le widget est asynchrone et fournit des mises à jour automatiques toutes les 10 à 15 secondes jusqu’à ce que la tâche soit terminée.

1. Affichez le widget Jupyter en attendant la fin de l’exécution.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Capture d’écran du widget de notebook Jupyter](./media/how-to-track-experiments/widgets.PNG)

2. **[Pour l’exécution de Machine Learning automatisé]**  Pour accéder aux graphiques à partir d’une exécution précédente. Remplacez `<<experiment_name>>` avec le nom d’une expérience appropriée :

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Widget de notebook Jupyter pour le Machine Learning automatisé](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Pour afficher d’autres détails sur un pipeline, cliquez sur le pipeline que vous souhaitez explorer dans la table. Les graphiques apparaissent dans une fenêtre contextuelle du Portail Azure.

### <a name="get-log-results-upon-completion"></a>Obtenir des résultats du journal lors de l’achèvement

L’entraînement et la supervision du modèle se produisent en arrière-plan afin que vous puissiez exécuter d’autres tâches pendant que vous attendez. Vous pouvez également attendre que l’entraînement du modèle soit terminé avant d’exécuter davantage de code. Quand vous utilisez **ScriptRunConfig**, vous pouvez employer ```run.wait_for_completion(show_output = True)``` pour indiquer quand l’entraînement du modèle est terminé. L’indicateur ```show_output``` vous donne une sortie détaillée. 
  
### <a name="query-run-metrics"></a>Interroger les métriques d’exécution

Vous pouvez afficher les métriques d’un modèle entraîné à l’aide de ```run.get_metrics()```. Vous pouvez désormais obtenir toutes les métriques qui ont été consignées dans l’exemple ci-dessus pour déterminer le meilleur modèle.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal"></a>Afficher l’expérimentation dans le portail Azure

Une fois l’exécution d’une expérimentation terminée, vous pouvez accéder à l’enregistrement d’exécution de l’expérimentation. Vous pouvez faire accéder à l’historique de deux manières :

* Obtenez l’URL vers l’exécution directement ```print(run.get_portal_url())```
* Affichez les détails de l’exécution en envoyant son nom (dans ce cas, ```run```). Cette méthode vous oriente vers le nom de l’expérience, l’ID, le type, l’état, la page de détails, un lien vers le Portail Azure et un lien vers la documentation.

Le lien pour l’exécution vous amène directement à la page de détails de l’exécution dans le portail Azure. Ici, vous pouvez voir les propriétés, les métriques suivies, les images et les graphiques consignés dans l’expérimentation. Dans ce cas, nous avons consigné MSE et les valeurs alpha.

  ![Détails de l’exécution dans le Portail Azure](./media/how-to-track-experiments/run-details-page-web.PNG)

Vous pouvez également afficher les sorties ou les journaux d’activité de l’exécution, ou télécharger la capture instantanée de l’expérimentation que vous avez envoyée afin de pouvoir partager le dossier de l’expérimentation avec d’autres utilisateurs.

### <a name="viewing-charts-in-run-details"></a>Affichage de graphiques dans des détails d’exécution

Il existe différentes manières d’utiliser les API de journalisation pour enregistrer différents types de métriques en cours d’exécution, et les afficher sous forme de graphiques dans le portail Azure. 

|Valeur connectée|Exemple de code| Afficher dans le portail|
|----|----|----|
|Journaliser un tableau de valeurs numériques| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|graphique en courbes à variable unique|
|Journaliser une valeur numérique avec le même nom de métrique utilisé à plusieurs reprises (comme à l’intérieur d’une boucle for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| Graphique en courbes à variable unique|
|Journaliser une ligne avec 2 colonnes numériques à plusieurs reprises|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Graphique en courbes à deux variables|
|Journaliser un table avec 2 colonnes numériques|`run.log_table(name='Sine Wave', value=sines)`|Graphique en courbes à deux variables|

<a name="auto"></a>
## <a name="understanding-automated-ml-charts"></a>Présentation des graphiques de ML automatisé

Après avoir soumis un travail de ML automatisé dans un notebook, vous pouvez trouver un historique de ces exécutions dans votre espace de travail de service de Machine Learning. 

Pour en savoir plus :
+ [Graphiques et courbes pour les modèles de classification](#classification)
+ [Graphiques pour les modèles de régression](#regression)
+ [Explication du modèle](#model-explain-ability-and-feature-importance)


### <a name="view-the-run-charts"></a>Afficher les graphiques d’exécution

1. Accédez à votre espace de travail. 

1. Sélectionnez **Expériences** dans le volet le plus à gauche de votre espace de travail.

   ![Capture d’écran du menu Expérience](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_menu.PNG)

1. Sélectionnez l’expérience qui vous intéresse.

   ![Liste d’expériences](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_list.PNG)

1. Dans la table, sélectionnez le numéro d’exécution.

   ![Exécution de l’expérience](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_run.PNG)

1. Dans la table, sélectionnez le numéro d’itération pour le modèle que vous souhaitez explorer.

   ![Modèle d’expérience](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_model.PNG)



### <a name="classification"></a>classification ;

Pour chaque modèle de classification que vous créez à l’aide des fonctionnalités de Machine Learning automatisé d’Azure Machine Learning, vous pouvez voir les graphiques suivants : 
+ [Matrice de confusion](#confusion-matrix)
+ [Graphique de rappel de précision](#precision-recall-chart)
+ [ROC (Receiver operating characteristic)](#roc)
+ [Courbe d’élévation](#lift-curve)
+ [Courbe de gains](#gains-curve)
+ [Tracé d’étalonnage](#calibration-plot)

#### <a name="confusion-matrix"></a>Matrice de confusion

Une matrice de confusion décrit les performances d’un modèle de classification. Chaque ligne affiche les instances de la classe true, et chaque colonne représente les instances de la classe prévue. La matrice de confusion montre les étiquettes bien et mal classées pour un modèle donné.

Pour les problèmes de classification, Azure Machine Learning fournit automatiquement une matrice de confusion associée à chaque modèle généré. Pour chaque matrice de confusion, le ML automatisé affiche les étiquettes bien classées en vert, et les étiquettes mal classées en rouge. La taille du cercle représente le nombre d’échantillons dans cet emplacement. En outre, la fréquence de chaque étiquette prévue et chaque étiquette true est fournie dans les graphiques à barres adjacents. 

Exemple 1 : Modèle de classification avec précision médiocre ![Modèle de classification avec précision médiocre](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix1.PNG)

Exemple 2 : Modèle de classification avec précision élevée (idéal) ![Modèle de classification avec précision élevée](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix2.PNG)


#### <a name="precision-recall-chart"></a>Graphique de rappel de précision

Avec ce graphique, vous pouvez comparer les courbes de rappel de précision pour chaque modèle afin de déterminer quel modèle présente une relation acceptable entre précision et rappel pour votre problème d’entreprise spécifique. Ce graphique montre le rappel de précision de macro-moyenne, le rappel de précision de micro-moyenne et le rappel de précision associé à toutes les classes d’un modèle.

Le terme Précision représente la capacité d’un classifieur à étiqueter correctement toutes les instances. Le rappel représente la capacité d’un classifieur à rechercher toutes les instances d’une étiquette spécifique. La courbe de rappel de précision montre la relation entre ces deux concepts. Dans l’idéal, le modèle aurait une précision de 100 % et une exactitude de 100 %.

Exemple 1 : Modèle de classification avec une précision faible et un rappel faible ![Modèle de classification avec une précision faible et un rappel faible](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall1.PNG)

Exemple 2 : Modèle de classification avec une précision d’environ 100 % et un rappel d’environ 100 % (idéal)![Modèle de classification avec une précision élevée et un rappel élevé](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall2.PNG)

#### <a name="roc"></a>ROC

Le ROC (Receiver Operating Characteristic) est un tracé d’étiquettes bien classées et mal classées pour un modèle spécifique. La courbe ROC peut être moins informative lors de la formation de modèles sur des jeux de données présentant un biais élevé, car elle n’affiche pas les étiquettes de type faux positif.

Exemple 1 : Modèle de classification avec des étiquettes true faibles et des étiquettes false élevées![Modèle de classification avec des étiquettes true faibles et des étiquettes false élevées](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc1.PNG)

Exemple 2 : Modèle de classification avec des étiquettes true élevées et des étiquettes false faibles![Modèle de classification avec des étiquettes true élevées et des étiquettes false faibles](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc2.PNG)

#### <a name="lift-curve"></a>Courbe d’élévation

Vous pouvez comparer l’élévation du modèle généré automatiquement avec Azure Machine Learning par rapport à la ligne de base afin d’afficher le gain de valeur de ce modèle spécifique.

Les graphiques de courbes d’élévation permettent d’évaluer les performances d’un modèle de classification. Ils montrent tous les atouts d’un modèle par rapport à une utilisation sans ce modèle. 

Exemple 1 : Les performances du modèle sont inférieures à celles d’un modèle de sélection aléatoire ![Modèle de classification dont les performances sont inférieures à celles d’un modèle de sélection aléatoire](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve1.PNG)

Exemple 2 : Les performances du modèle sont supérieures à celles d’un modèle de sélection aléatoire ![Modèle de classification qui offre de meilleures performances](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve2.PNG)

#### <a name="gains-curve"></a>Courbe de gains

Un graphique de gains évalue les performances d’un modèle de classification pour chaque partie des données. Il montre, pour chaque centile du jeu de données, les résultats que vous pouvez attendre par rapport à un modèle de sélection aléatoire.

Utilisez le graphique de gains cumulés pour choisir la limite de classification au moyen d’un pourcentage qui correspond à un gain souhaité à partir du modèle. Ces informations offrent une autre façon d’observer les résultats dans le graphique de courbes d’élévation associé.

Exemple 1 : Modèle de classification avec gain minimal ![Modèle de classification avec gain minimal](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve1.PNG)

Exemple 2 : Modèle de classification avec gain significatif ![Modèle de classification avec gain significatif](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve2.PNG)

#### <a name="calibration-plot"></a>Tracé d’étalonnage

Pour tous les problèmes de classification, vous pouvez consulter la ligne d’étalonnage concernant la micro-moyenne, la macro-moyenne et chaque classe dans un modèle prédictif donné. 

Un tracé d’étalonnage permet d’afficher le niveau de confiance d’un modèle prédictif. Pour ce faire, il affiche la relation entre la probabilité prévue et la probabilité réelle, où le terme « probabilité » représente la vraisemblance pour une instance spécifique d’appartenir à une étiquette. Un modèle bien étalonné s’aligne avec la ligne y=x, où il est raisonnablement confiant dans ses prédictions. Un modèle trop confiant s’aligne sur la ligne y=0, où la probabilité prévue est présente alors qu’il n’existe aucune probabilité réelle.

Exemple 1 : Modèle mieux étalonné ![ Modèle mieux étalonné](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve1.PNG)

Exemple 2 : Modèle trop confiant ![Modèle trop confiant](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve2.PNG)

### <a name="regression"></a>régression ;
Pour chaque modèle de régression que vous créez à l’aide des fonctionnalités de Machine Learning automatisé d’Azure Machine Learning, vous pouvez voir les graphiques suivants : 
+ [Prédiction et True](#pvt)
+ [Histogramme des résidus](#histo)

<a name="pvt"></a>

#### <a name="predicted-vs-true"></a>Prédiction et True

Prédiction et True indique la relation entre une valeur prévue et sa valeur true en corrélation pour un problème de régression. Ce graphique peut servir à mesurer les performances d’un modèle, car plus les valeurs prévues sont proches de la ligne y=x, plus le modèle prédictif est précis.

Après chaque exécution, vous pouvez afficher un graphique de type Prédiction et True pour chaque modèle de régression. Pour protéger la confidentialité des données, les valeurs sont réunies dans un conteneur et la taille de chaque emplacement est affichée sous la forme d’un graphique à barres au bas de la zone de graphique. Vous pouvez comparer le modèle prédictif, dont la zone la plus claire indique les marges d’erreur, par rapport à la valeur idéale du modèle.

Exemple 1 : Modèle de régression avec faible précision dans les prédictions ![Modèle de régression avec faible précision dans les prédictions](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression1.PNG)

Exemple 2 : Modèle de régression avec précision élevée dans les prédictions ![Modèle de régression avec précision élevée dans les prédictions](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression2.PNG)

<a name="histo"></a>

#### <a name="histogram-of-residuals"></a>Histogramme des résidus

Un résidu représente une valeur y observée : la valeur y prévue. Pour afficher une marge d’erreur avec un biais faible, l’histogramme des résidus doit avoir la forme d’une cloche centrée sur 0. 

Exemple 1 : Modèle de régression avec biais dans ses erreurs ![Modèle de régression avec biais dans ses erreurs](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression3.PNG)

Exemple 2 : Modèle de régression avec distribution plus équilibrée des erreurs ![Modèle de régression avec distribution plus équilibrée des erreurs](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression4.PNG)

### <a name="model-explain-ability-and-feature-importance"></a>Explication du modèle et importance des fonctionnalités

L’importance des fonctionnalités fournit un score indiquant l’importance de chaque fonctionnalité dans la construction d’un modèle. Vous pouvez consulter le score d’importance des fonctionnalités pour le modèle global, mais aussi pour chaque classe dans un modèle prédictif. Vous pouvez comparer l’importance d’une fonctionnalité dans chaque classe et de manière globale.

![Explication des fonctionnalités](./media/how-to-track-experiments/azure-machine-learning-auto-ml-feature_explain1.PNG)

## <a name="example-notebooks"></a>Exemples de notebooks
Les notebooks suivants illustrent les concepts de cet article :
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/training/logging-api/logging-api.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Étapes suivantes

Essayez de suivre les étapes suivantes pour savoir comment utiliser le kit SDK Azure Machine Learning pour Python :

* Examinez un exemple d’inscription et de déploiement du meilleur modèle dans le tutoriel [Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md).

* Découvrez comment [entraîner des modèles PyTorch avec Azure Machine Learning](how-to-train-pytorch.md).
