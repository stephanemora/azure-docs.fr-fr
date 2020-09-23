---
title: Déployer un modèle Machine Learning sur Azure SQL Edge à l'aide d'ONNX
description: Dans la troisième partie de ce tutoriel Azure SQL Edge en trois parties consacré à la prédiction des impuretés contenues dans le minerai de fer, vous allez exécuter les modèles Machine Learning ONNX sur SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: abd1bc9b53c303d094d74683da0d6e3fad4d715f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90887468"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>Déployer un modèle Machine Learning sur Azure SQL Edge à l'aide d'ONNX 

Dans la troisième partie de ce tutoriel en trois parties consacré à la prédiction des impuretés contenues dans le minerai de fer dans Azure SQL Edge, vous allez :

1. utiliser Azure Data Studio pour vous connecter à SQL Database dans l'instance d'Azure SQL Edge ;
2. prédire les impuretés contenues dans le minerai de fer à l'aide d'ONNX dans Azure SQL Edge.

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance"></a>Se connecter à SQL Database dans l'instance d'Azure SQL Edge

1. Ouvrez Azure Data Studio.

2. Sous l'onglet **Bienvenue**, lancez une nouvelle connexion avec les informations suivantes :

   |_Champ_|_Valeur_|
   |-------|-------|
   |Type de connexion| Microsoft SQL Server|
   |Serveur|Adresse IP publique mentionnée dans la machine virtuelle créée pour cette démo|
   |Nom d’utilisateur|SA|
   |Mot de passe|Mot de passe fort utilisé lors de la création de l'instance d'Azure SQL Edge|
   |Base de données|Default|
   |Groupe de serveurs|Default|
   |Name (facultatif)|Entrez un nom facultatif|

3. Cliquez sur **Connexion**

4. Dans la section **Fichier**, ouvrez un nouveau notebook ou utilisez le raccourci clavier Alt + Windows + N. 

5. Définissez le noyau sur Python 3.

## <a name="predict-iron-ore-impurities-with-onnx"></a>Prédire les impuretés contenues dans le minerai de fer à l'aide d'ONNX

Entrez le code Python suivant dans le notebook Azure Data Studio et exécutez-le.

1. Commencez par installer et importer les packages requis.

   ```python
   !pip install azureml.core -q
   !pip install azureml.train.automl -q
   !pip install matplotlib -q
   !pip install pyodbc -q
   !pip install spicy -q
   
   import logging
   from matplotlib import pyplot as plt
   import numpy as np
   import pandas as pd
   import pyodbc
   
   from scipy import stats
   from scipy.stats import skew #for some statistics
   
   import azureml.core
   from azureml.core.experiment import Experiment
   from azureml.core.workspace import Workspace
   from azureml.train.automl import AutoMLConfig
   from azureml.train.automl import constants
   ```

1. Définissez l'espace de travail Azure AutoML et configurez l'expérience AutoML pour l'expérience de régression.

   ```python
   ws = Workspace(subscription_id="<Azure Subscription ID>",
                  resource_group="<resource group name>",
                  workspace_name="<ML workspace name>")
   # Choose a name for the experiment.
   experiment_name = 'silic_percent2-Forecasting-onnx'
   experiment = Experiment(ws, experiment_name)
   ```

1. Importez le jeu de données dans une trame Panda. Dans le cadre de l'apprentissage du modèle, utilisez le jeu de données d'apprentissage [Quality Prediction in a Mining Process](https://www.kaggle.com/edumagalhaes/quality-prediction-in-a-mining-process) (Prédiction de la qualité dans un processus minier) de Kaggle. Téléchargez le fichier de données et enregistrez-le localement sur votre ordinateur de développement. Vous utiliserez ces données pour prédire la quantité d'impuretés contenues dans le concentré de minerai.

   ```python
   df = pd.read_csv("<local path where you have saved the data file>",decimal=",",parse_dates=["date"],infer_datetime_format=True)
   df = df.drop(['date'],axis=1)
   df.describe()
   ```

1. Analysez les données pour identifier toute asymétrie. Au cours de ce processus, examinez la répartition et les informations d'asymétrie dans chacune des colonnes de la trame de données.

   ```python
   ## We can use a histogram chart to view the data distribution for the Dataset. In this example, we are looking at the histogram for the "% Silica Concentrate" 
   ## and the "% Iron Feed". From the histogram, you'll notice the data distribution is skewed for most of the features in the dataset. 
   
   f, (ax1,ax2,ax3) = plt.subplots(1,3)
   ax1.hist(df['% Iron Feed'], bins='auto')
   #ax1.title = 'Iron Feed'
   ax2.hist(df['% Silica Concentrate'], bins='auto')
   #ax2.title = 'Silica Concentrate'
   ax3.hist(df['% Silica Feed'], bins='auto')
   #ax3.title = 'Silica Feed'
   ```

1. Vérifiez et corrigez le niveau d'asymétrie des données.

   ```python
   ##Check data skewness with the skew or the kurtosis function in spicy.stats
   ##Skewness using the spicy.stats skew function
   for i in list(df):
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   
   #Fix the Skew using Box Cox Transform
   from scipy.special import boxcox1p
   for i in list(df):
       if(abs(skew(df[i])) >= 0.20):
           #print('found skew in column - {0}'.format(i))
           df[i] = boxcox1p(df[i], 0.10)
           print('Skew value for column "{0}" is: {1}'.format(i,skew(df[i])))
   ```

1. Examinez la corrélation d'autres fonctionnalités avec la fonctionnalité de prédiction. Si la corrélation n'est pas élevée, supprimez ces fonctionnalités.

   ```python
   silic_corr = df.corr()['% Silica Concentrate']
   silic_corr = abs(silic_corr).sort_values()
   drop_index= silic_corr.index[:8].tolist()
   df = df.drop(drop_index, axis=1)
   df.describe()
   ```

1. Lancez l'expérience AzureML pour rechercher le meilleur algorithme et effectuer son apprentissage. Vous allez ici tester tous les algorithmes de régression, avec la métrique principale Erreur quadratique moyenne normalisée (NRMSE). Pour plus d'informations, consultez [Métrique principale dans les expériences Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#primary-metric). Le code suivant lance une exécution locale de l'expérience Machine Learning.

   ```python
   ## Define the X_train and the y_train data sets for the AutoML experiments. X_Train are the inputs or the features, while y_train is the outcome or the prediction result. 
   
   y_train = df['% Silica Concentrate']
   x_train = df.iloc[:,0:-1]
   automl_config = AutoMLConfig(task = 'regression',
                                primary_metric = 'normalized_root_mean_squared_error',
                                iteration_timeout_minutes = 60,
                                iterations = 10,                        
                                X = x_train, 
                                y = y_train,
                                featurization = 'off',
                                enable_onnx_compatible_models=True)
   
   local_run = experiment.submit(automl_config, show_output = True)
   best_run, onnx_mdl = local_run.get_output(return_onnx_model=True)
   ```

1. Chargez le modèle dans la base de données Azure SQL Edge pour le scoring local.

   ```python
   ## Load the Model into a SQL Database.
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   cursor = conn.cursor()
   
   # Insert the ONNX model into the models table
   query = f"insert into models ([description], [data]) values ('Silica_Percentage_Predict_Regression_NRMSE_New1',?)"
   model_bits = onnx_mdl.SerializeToString()
   insert_params  = (pyodbc.Binary(model_bits))
   cursor.execute(query, insert_params)
   conn.commit()
   cursor.close()
   conn.close()
   ```

1. Enfin, utilisez le modèle Azure SQL Edge pour effectuer des prédictions à l'aide du modèle formé.

   ```python
   ## Define the Connection string parameters. These connection strings will be used later also in the demo.
   server = '<SQL Server IP address>'
   username = 'sa' # SQL Server username
   password = '<SQL Server password>'
   database = 'IronOreSilicaPrediction'
   db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
   conn = pyodbc.connect(db_connection_string, autocommit=True)
   #cursor = conn.cursor()
   query = \
           f'declare @model varbinary(max) = (Select [data] from [dbo].[Models] where [id] = 1);' \
           f' with d as ( SELECT  [timestamp] ,cast([cur_Iron_Feed] as real) [__Iron_Feed] ,cast([cur_Silica_Feed]  as real) [__Silica_Feed]' \
           f',cast([cur_Starch_Flow] as real) [Starch_Flow],cast([cur_Amina_Flow] as real) [Amina_Flow]' \
           f' ,cast([cur_Ore_Pulp_pH] as real) [Ore_Pulp_pH] ,cast([cur_Flotation_Column_01_Air_Flow] as real) [Flotation_Column_01_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_02_Air_Flow] as real) [Flotation_Column_02_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_03_Air_Flow] as real) [Flotation_Column_03_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_07_Air_Flow] as real) [Flotation_Column_07_Air_Flow]' \
           f' ,cast([cur_Flotation_Column_04_Level] as real) [Flotation_Column_04_Level]' \
           f' ,cast([cur_Flotation_Column_05_Level] as real) [Flotation_Column_05_Level]' \
           f' ,cast([cur_Flotation_Column_06_Level] as real) [Flotation_Column_06_Level]' \
           f' ,cast([cur_Flotation_Column_07_Level] as real) [Flotation_Column_07_Level]' \
           f' ,cast([cur_Iron_Concentrate] as real) [__Iron_Concentrate]' \
           f' FROM [dbo].[IronOreMeasurements1]' \
           f' where timestamp between dateadd(hour,-1,getdate()) and getdate()) ' \
           f' SELECT d.*, p.variable_out1' \
           f' FROM PREDICT(MODEL = @model, DATA = d) WITH(variable_out1 numeric(25,17)) as p;' 
     
   df_result = pd.read_sql(query,conn)
   df_result.describe()
   ```

1. À l'aide de Python, créez un graphique représentant le pourcentage de silice prédit par rapport à l'alimentation en fer, la date/heure et l'alimentation en silice.

   ```python
   import plotly.graph_objects as go
   fig = go.Figure()
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Iron_Feed'],mode='lines+markers',name='Iron Feed',line=dict(color='firebrick', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['__Silica_Feed'],mode='lines+markers',name='Silica Feed',line=dict(color='green', width=2)))
   fig.add_trace(go.Scatter(x=df_result['timestamp'],y=df_result['variable_out1'],mode='lines+markers',name='Silica Percent',line=dict(color='royalblue', width=3)))
   fig.update_layout(height= 600, width=1500,xaxis_title='Time')
   fig.show()
   ```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations sur l'utilisation des modèles ONNX dans Azure SQL Edge, consultez [Machine Learning et IA avec ONNX dans SQL Edge](onnx-overview.md).
