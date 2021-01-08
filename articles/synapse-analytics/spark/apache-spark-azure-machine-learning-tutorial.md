---
title: 'Tutoriel : Entraîner un modèle dans Python avec le Machine Learning automatisé'
description: Tutoriel sur l’entraînement d’un modèle Machine Learning dans Python en utilisant Apache Spark et le Machine Learning automatisé.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: e547d047e8d736acbd1cdda5ffe3a78dbe8259f7
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901031"
---
# <a name="tutorial-train-a-model-in-python-with-automated-machine-learning"></a>Tutoriel : Entraîner un modèle dans Python avec le Machine Learning automatisé

Azure Machine Learning est un environnement informatique qui vous permet d’effectuer l’apprentissage, le déploiement, l’automatisation, la gestion et le suivi des modèles Machine Learning. 

Dans ce tutoriel, vous utilisez le [Machine Learning automatisé](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) d’Azure Machine Learning pour créer un modèle de régression permettant de prédire les prix des courses de taxi. Ce processus parvient au meilleur modèle en acceptant des données d’entraînement et des paramètres de configuration, et en bouclant automatiquement dans des combinaisons de différentes méthodes, de différents modèles et de différentes valeurs pour les hyperparamètres.

Dans ce tutoriel, vous allez apprendre à :
- Télécharger les données en utilisant Apache Spark et d’Azure Open Datasets.
- Transformer et nettoyer des données en utilisant des dataframes Apache Spark.
- Entraîner un modèle de régression de Machine Learning automatisé.
- Calculer la précision d’un modèle.

## <a name="before-you-begin"></a>Avant de commencer

- Créez un pool Apache Spark serverless en suivant le [Démarrage rapide Créer un pool Apache Spark serverless](../quickstart-create-apache-spark-pool-studio.md).
- Suivez le [tutoriel de configuration de l’espace de travail Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup) si vous n’avez pas d’espace de travail Azure Machine Learning existant. 

## <a name="understand-regression-models"></a>Comprendre les modèles de régression

Les *modèles de régression* prédisent des valeurs de sortie numériques basées sur des prédicteurs indépendants. Dans une régression, l’objectif est d’aider à établir la relation entre ces variables de prédiction indépendantes en estimant comment une variable affecte les autres variables.  

### <a name="example-based-on-new-york-city-taxi-data"></a>Exemple basé sur les données relatives aux taxis de New York

Dans cet exemple, vous utilisez Spark pour effectuer une analyse sur les données des pourboires des courses de taxi à New York. Les données sont disponibles dans [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Ce sous-ensemble du jeu de données contient des données sur les courses de taxis jaunes, avec des informations sur chaque course, les heures et les lieux de départ et d’arrivée, et le prix.

> [!IMPORTANT]
> Des frais supplémentaires peuvent s’appliquer pour l’extraction de ces données à partir de leur emplacement de stockage. Dans les étapes suivantes, vous développez un modèle permettant de prédire les prix des courses de taxi à New York. 

##  <a name="download-and-prepare-the-data"></a>Télécharger et préparer les données

Voici comment procéder :

1. Créez un notebook en utilisant le noyau PySpark. Pour obtenir des instructions, consultez [Créer un notebook](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook).
   
    > [!Note]
    > Grâce au noyau PySpark, il est inutile de créer des contextes explicitement. Le contexte Spark est créé automatiquement pour vous lorsque vous exécutez la première cellule de code.
  
2. Étant donné que les données brutes sont au format Parquet, vous pouvez utiliser le contexte Spark pour extraire le fichier directement en mémoire en tant que dataframe. Créez un DataFrame Spark en extrayant les données via l’API Open Datasets. Nous utilisons ici les propriétés `schema on read` du dataframe Spark pour inférer les types de données et le schéma. 
   
    ```python
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow Spark to read from Blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # Spark read parquet, note that it won't load any data yet by now
    df = spark.read.parquet(wasbs_path)

    ```

3. Selon la taille de votre pool Spark, les données brutes peuvent être trop grandes ou leur exploitation peut prendre trop de temps. Vous pouvez filtrer ces données pour en réduire le volume à l’aide des filtres ```start_date``` et ```end_date```. Cela applique un filtre qui retourne un mois de données. Une fois que le dataframe est filtré, nous exécutons aussi la fonction ```describe()``` sur le nouveau dataframe pour voir des statistiques récapitulatives pour chaque champ. 

   D’après les statistiques récapitulatives, vous pouvez voir qu’il existe quelques irrégularités dans les données. Par exemple, les statistiques montrent que la distance de trajet minimale est inférieure à 0. Vous devez filtrer ces points de données irréguliers.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime< "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. Ensuite, vous allez générer des caractéristiques à partir du jeu de données en sélectionnant un ensemble de colonnes et en créant différentes caractéristiques basées sur le temps à partir du champ DateHeure de la prise en charge. Filtrez les valeurs hors norme qui ont été identifiées à l’étape précédente, puis supprimez les quelques dernières colonnes qui ne sont pas nécessaires pour l’entraînement.
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster and less expensive down sample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   taxi_df = sampled_taxi_df.select('vendorID', 'passengerCount', 'tripDistance',  'startLon', 'startLat', 'endLon' \
                                   , 'endLat', 'paymentType', 'fareAmount', 'tipAmount'\
                                   , column('puMonth').alias('month_num') \
                                   , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                   , date_format('tpepPickupDateTime', 'EEEE').alias('day_of_week')\
                                   , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month')
                                   ,(unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('trip_time'))\
                           .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                   & (sampled_taxi_df.tipAmount >= 0)\
                                   & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                   & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                   & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 200)\
                                   & (sampled_taxi_df.rateCodeId <= 5)\
                                   & (sampled_taxi_df.paymentType.isin({"1", "2"})))
   taxi_df.show(10)
   ```
   
   Comme vous pouvez le voir, cela crée un nouveau DataFrame avec des colonnes supplémentaires pour le jour du mois, l’heure de collecte, le jour de la semaine et la durée totale de la course. 

   ![Image du DataFrame du taxi.](./media/azure-machine-learning-spark-notebook/dataset.png#lightbox)

## <a name="generate-test-and-validation-datasets"></a>Générer des jeux de données de test et de validation

Une fois que vous avez votre jeu de données final, vous pouvez diviser les données en jeux d’entraînement et de test en utilisant la fonction ```random_ split ``` de Spark. À l’aide des poids fournis, cette fonction divise de manière aléatoire les données dans le jeu de données d’apprentissage pour l’apprentissage du modèle et le jeu de données de validation à des fins de test.

```python
# Random split dataset using Spark, convert Spark to Pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```
Cette étape garantit que les points de données pour tester le modèle fini n’ont pas été utilisées pour l’entraînement du modèle. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Se connecter à un espace de travail Azure Machine Learning
Dans Azure Machine Learning, un espace de travail est une classe qui accepte les informations concernant votre abonnement et vos ressources Azure. Il crée également une ressource cloud pour superviser et suivre les exécutions de votre modèle. Dans cette étape, vous créez un objet d’espace de travail à partir de l’espace de travail Azure Machine Learning existant.
   
```python
from azureml.core import Workspace

# Enter your workspace subscription, resource group, name, and region.
subscription_id = "<enter your subscription ID>" #you should be owner or contributor
resource_group = "<enter your resource group>" #you should be owner or contributor
workspace_name = "<enter your workspace name>" #your workspace name
workspace_region = "<enter workspace region>" #your region

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>Convertir un dataframe en jeu de données Azure Machine Learning
Pour soumettre une expérience à distance, convertissez votre jeu de données en ```TabularDatset``` Azure Machine Learning. Un [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) représente les données sous forme de tableau en analysant les fichiers fournis.

Le code suivant obtient l’espace de travail existant et le magasin de données Azure Machine Learning par défaut. Ensuite, il transmet les emplacements du magasin de données et des fichiers au paramètre de chemin d’accès pour créer un nouvel objet ```TabularDataset```. 

```python
import pandas 
from azureml.core import Dataset

# Get the Azure Machine Learning Default Datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into Azure Machine Learning Tabular Dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```
![Image du jeu de données chargé.](./media/azure-machine-learning-spark-notebook/upload-dataset.png)

## <a name="submit-an-automated-experiment"></a>Soumettre une expérience automatisée

Les sections suivantes vous guident tout au long du processus de soumission d’une expérience de Machine Learning automatisé.

### <a name="define-training-settings"></a>Définir les paramètres d’entraînement
1. Pour soumettre une expérience, vous devez définir le paramètre de l’expérience et les paramètres du modèle pour l’entraînement. Pour obtenir la liste complète des paramètres, consultez [Configurer des expériences de Machine Learning automatisé dans Python](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train).

   ```python
   import logging

   automl_settings = {
       "iteration_timeout_minutes": 10,
       "experiment_timeout_minutes": 30,
       "enable_early_stopping": True,
       "primary_metric": 'r2_score',
       "featurization": 'auto',
       "verbosity": logging.INFO,
       "n_cross_validations": 2}
   ```

1. Passez les paramètres d’entraînement définis en tant que paramètre `kwargs` à un objet `AutoMLConfig`. Comme vous utilisez Spark, vous devez aussi passer le contexte Spark, qui est automatiquement accessible par la variable ```sc```. Vous spécifiez aussi les données d’entraînement et le type de modèle, qui dans ce cas est « régression ».

   ```python
   from azureml.train.automl import AutoMLConfig

   automl_config = AutoMLConfig(task='regression',
                                debug_log='automated_ml_errors.log',
                                training_data = dataset_training,
                                spark_context = sc,
                                model_explainability = False, 
                                label_column_name ="fareAmount",**automl_settings)
   ```

> [!NOTE]
>Les étapes de prétraitement du Machine Learning automatisé deviennent partie intégrante du modèle sous-jacent. Ces étapes incluent la normalisation des caractéristiques, la gestion des données manquantes et la conversion du texte en type numérique. Quand vous utilisez le modèle pour des prédictions, les étapes de prétraitement qui sont appliquées pendant l’entraînement sont appliquées automatiquement à vos données d’entrée.

### <a name="train-the-automatic-regression-model"></a>Entraîner le modèle de régression automatique 
Ensuite, vous créez un objet d’expérience dans votre espace de travail Azure Machine Learning. Une expérience fait office de conteneur pour vos exécutions individuelles. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```
Une fois l’expérience terminée, la sortie retourne des détails sur les itérations effectuées. Pour chaque itération, vous voyez le type de modèle, la durée d’exécution et la précision de l’entraînement. Le champ **BEST** effectue un suivi du meilleur score obtenu à l’entraînement en fonction de votre type de métrique.

![Capture d’écran de la sortie du modèle.](./media/azure-machine-learning-spark-notebook/model-output.png)

> [!NOTE]
> Une fois l’expérience de Machine Learning automatisé soumise, elle exécute différentes itérations avec différents types de modèles. Cette exécution nécessite généralement de 60 à 90 minutes. 

### <a name="retrieve-the-best-model"></a>Récupérer le meilleur modèle
Pour sélectionner le meilleur modèle parmi vos itérations, utilisez la fonction ```get_output``` pour retourner le modèle le mieux adapté. Le code ci-dessous récupère le modèle le mieux adapté pour toutes les métriques journalisées ou pour une itération particulière.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

### <a name="test-model-accuracy"></a>Tester la précision du modèle
1. Pour tester la précision du modèle, utilisez le meilleur modèle pour faire des prédictions des prix des courses de taxi sur le jeu de données de test. La fonction ```predict``` utilise le meilleur modèle et prédit les valeurs de y (montant du tarif) à partir du jeu de données de validation. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

1. L’erreur quadratique moyenne est une mesure fréquemment utilisée des différences entre les exemples de valeurs prédites par un modèle et les valeurs observées. Vous calculez l’erreur quadratique moyenne des résultats en comparant le dataframe `y_test` aux valeurs prédites par le modèle. 

   La fonction ```mean_squared_error``` accepte deux tableaux et calcule l’erreur quadratique moyenne entre ces tableaux. Vous prenez ensuite la racine carrée du résultat. Cette métrique indique l’écart approximatif entre les prédictions des prix des courses de taxi et les tarifs réels.

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate Root Mean Square Error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   L’erreur quadratique moyenne est une bonne mesure de la manière dont le modèle prédit la réponse. Dans les résultats, vous voyez que le modèle est assez bon dans la prédiction des tarifs de taxi à partir des caractéristiques du jeu de données, en général avec un écart de ± 2,00 dollars.

1. Exécutez le code suivant pour calculer l’erreur absolue moyenne en pourcentage. Cette mesure exprime la précision sous la forme d’un pourcentage de l’erreur. Pour ce faire, elle calcule une différence absolue entre chaque valeur prédite et réelle, puis additionne toutes les différences. Ensuite, elle exprime cette somme sous forme de pourcentage du total des valeurs réelles.

   ```python
   # Calculate MAPE and Model Accuracy 
   sum_actuals = sum_errors = 0

   for actual_val, predict_val in zip(y_actual, y_predict):
       abs_error = actual_val - predict_val
       if abs_error < 0:
           abs_error = abs_error * -1

       sum_errors = sum_errors + abs_error
       sum_actuals = sum_actuals + actual_val

   mean_abs_percent_error = sum_errors / sum_actuals

   print("Model MAPE:")
   print(mean_abs_percent_error)
   print()
   print("Model Accuracy:")
   print(1 - mean_abs_percent_error)
   ```

   ```Output
   Model MAPE:
   0.03655071038487368

   Model Accuracy:
   0.9634492896151263
   ```
   Dans les deux métriques de précision de la prédiction, vous voyez que le modèle est assez bon dans la prédiction des tarifs de taxi à partir des caractéristiques du jeu de données. 

1. Après avoir ajusté un modèle de régression linéaire, vous devez maintenant déterminer dans quelle mesure le modèle est adapté aux données. Pour ce faire, vous tracez les valeurs des tarifs réels par rapport au résultat prédit. En outre, vous calculez la racine carrée de la mesure pour comprendre le degré de proximité des données avec la ligne de régression ajustée.

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the Actual vs Predicted Fare Amount Values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amont R^2={}".format(r2))
   plt.show()

   ```
   ![Capture d’écran du tracé de régression.](./media/azure-machine-learning-spark-notebook/fare-amount.png)

   Dans les résultats, vous pouvez voir que la racine carrée de la mesure compte pour 95 % de la variance. Ceci est également vérifié par le tracé réel comparé au tracé observé. Plus la variance prise en compte par le modèle de régression est importante, plus les points de données sont proches de la ligne de régression ajustée.  

## <a name="register-model-to-azure-machine-learning"></a>Inscrire le modèle auprès d’Azure Machine Learning
Une fois que vous avez vérifié votre meilleur modèle, vous pouvez l’inscrire auprès d’Azure Machine Learning. Vous pouvez ensuite télécharger ou déployer le modèle inscrit, et recevoir tous les fichiers que vous avez inscrits.

```python
description = 'My automated ML model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```
```Output
NYCGreenTaxiModel 1
```
## <a name="view-results-in-azure-machine-learning"></a>Afficher les résultats dans Azure Machine Learning
Enfin, vous pouvez aussi consulter les résultats des itérations en accédant à l’expérience dans votre espace de travail Azure Machine Learning. Ici, vous pourrez voir des détails supplémentaires sur l’état de votre exécution, sur les modèles essayés et sur d’autres métriques des modèles. 

![Capture d’écran de l’espace de travail Azure Machine Learning](./media/azure-machine-learning-spark-notebook/azure-machine-learning-workspace.png)

## <a name="next-steps"></a>Étapes suivantes
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Tutoriel : Créer une application d’apprentissage automatique avec Apache Spark MLlib et Azure Synapse Analytics](./apache-spark-machine-learning-mllib-notebook.md)
