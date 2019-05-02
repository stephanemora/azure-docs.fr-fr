---
title: 'Didacticiel sur un modèle de régression : ML automatisé'
titleSuffix: Azure Machine Learning service
description: Découvrez comment générer un modèle Machine Learning à l’aide du machine learning automatisé. Azure Machine Learning peut effectuer le prétraitement des données, la sélection de l’algorithme et la sélection des hyperparamètres de manière automatisée. Le modèle final doit ensuite être déployé avec Azure Machine Learning service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: trbye
ms.date: 04/11/2019
ms.custom: seodec18
ms.openlocfilehash: 4d09fca68af55b577c47316363ed386e98525baf
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925758"
---
# <a name="tutorial-use-automated-machine-learning-to-build-your-regression-model"></a>Didacticiel : Utiliser le Machine Learning automatisé pour générer votre modèle de régression

Ce tutoriel est le **deuxième d’une série de deux**. Dans le tutoriel précédent, vous [avez préparé des données concernant les taxis new-yorkais en vue d’une modélisation de régression](tutorial-data-prep.md).

Maintenant, vous êtes prêt à créer votre modèle avec Azure Machine Learning service. Dans cette partie du tutoriel, vous utilisez les données préparées et générez automatiquement un modèle de régression afin de prédire les tarifs des taxis. En utilisant les fonctionnalités de machine learning automatisé du service, vous définissez vos contraintes et objectifs de machine learning. Vous lancez le processus de machine learning automatisé. Ensuite, vous autorisez la sélection de l’algorithme et le réglage des hyperparamètres à se produire en votre nom. La technique de machine learning automatisé itère de nombreuses combinaisons d’algorithmes et d’hyperparamètres jusqu’à ce qu’elle trouve le meilleur modèle en fonction de vos critères.

![Diagramme de flux](./media/tutorial-auto-train-models/flow2.png)

Dans ce tutoriel, vous avez appris à effectuer les tâches suivantes :

> [!div class="checklist"]
> * Configurer un environnement Python et importer les packages de SDK
> * Configurer un espace de travail pour Azure Machine Learning service
> * Entraîner automatiquement un modèle de régression
> * Exécuter le modèle localement avec des paramètres personnalisés
> * Explorer les résultats

Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez dès aujourd'hui la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree).

>[!NOTE]
> Le code présenté dans cet article a été testé avec le kit SDK Azure Machine Learning version 1.0.0.

## <a name="prerequisites"></a>Prérequis

Passez à la section [Configurer votre environnement de développement](#start) pour lire les étapes relatives aux notebooks, ou utilisez les instructions ci-dessous pour obtenir le notebook et l’exécuter sur Azure Notebooks ou votre propre serveur de notebooks. Pour exécuter le notebook, vous devez disposer des éléments suivants :

* [Avoir suivi le tutoriel sur la préparation des données](tutorial-data-prep.md)
* Un serveur de notebooks Python 3.6 sur lequel les éléments suivants sont installés :
    * Le SDK Azure Machine Learning pour Python avec les suppléments `automl` et `notebooks`
    * `matplotlib`
* Le notebook du tutoriel
* Un espace de travail Machine Learning
* Le fichier de configuration pour l’espace de travail dans le même répertoire que le notebook

Obtenir tous ces prérequis à partir de l’une des sections ci-dessous.

* Utiliser [Azure Notebooks](#azure)
* Utiliser [votre propre serveur de notebooks](#server)

### <a name="azure"></a>Utiliser Azure Notebooks : Notebooks Jupyter gratuits dans le cloud

La prise en main d’Azure Notebooks est simple. Le [kit SDK Azure Machine Learning pour Python](https://aka.ms/aml-sdk) est déjà installé et configuré dans [Azure Notebooks](https://notebooks.azure.com/). L’installation et les futures mises à jour sont gérées automatiquement par le biais des services Azure.

Après avoir terminé les étapes ci-dessous, exécutez le notebook **tutorials/regression-part2-automated-ml.ipynb** dans votre projet **Bien démarrer**.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

### <a name="server"></a>Utiliser votre propre serveur de notebooks Jupyter

Utilisez ces étapes pour créer une instance locale de serveur de notebooks Jupyter sur votre ordinateur.  Veillez à installer `matplotlib` et les suppléments `automl` et `notebooks` dans votre environnement.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Après avoir terminé les étapes, exécutez le notebook **tutorials/regression-part2-automated-ml.ipynb**.

## <a name="start"></a>Configurer votre environnement de développement

Toute la configuration pour votre travail de développement peut être effectuée dans un bloc-notes Python. La configuration comprend les actions suivantes :

* Installer le Kit de développement logiciel (SDK)
* Importer des packages Python
* Configurer votre espace de travail

### <a name="install-and-import-packages"></a>Installer et importer des packages

Si vous suivez le tutoriel dans votre propre environnement Python, utilisez le code suivant pour installer les packages nécessaires.

```shell
pip install azureml-sdk[automl,notebooks] matplotlib
```

Importez les packages Python dont vous avez besoin dans ce tutoriel :

```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
import logging
import os
```

### <a name="configure-workspace"></a>Configurer l’espace de travail

Créez un objet d’espace de travail à partir de l’espace de travail existant. Un [espace de travail](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) est une classe qui accepte les informations concernant vos abonnements et vos ressources Azure. Il crée également une ressource cloud pour superviser et suivre les exécutions de votre modèle.

`Workspace.from_config()` lit le fichier **config.json** et charge les détails dans un objet nommé `ws`.  `ws` est utilisé dans tout le reste du code de ce didacticiel.

Une fois que vous avez un objet d’espace de travail, spécifiez un nom pour l’expérience. Créez et inscrivez un répertoire local avec l’espace de travail. L’historique de toutes les exécutions est enregistré sous l’expérience spécifiée ainsi que dans le [portail Azure](https://portal.azure.com).


```python
ws = Workspace.from_config()
# choose a name for the run history container in the workspace
experiment_name = 'automated-ml-regression'
# project folder
project_folder = './automated-ml-regression'

output = {}
output['SDK version'] = azureml.core.VERSION
output['Subscription ID'] = ws.subscription_id
output['Workspace'] = ws.name
output['Resource Group'] = ws.resource_group
output['Location'] = ws.location
output['Project Directory'] = project_folder
pd.set_option('display.max_colwidth', -1)
pd.DataFrame(data=output, index=['']).T
```

## <a name="explore-data"></a>Explorer les données

Utilisez l’objet de dataflow créé dans le tutoriel précédent. En résumé, la première partie de ce tutoriel a nettoyé les données NYC Taxi afin qu’il puisse être utilisé dans un modèle Machine Learning. À présent, vous utilisez diverses caractéristiques du jeu de données et permettez à un modèle automatisé de créer des relations entre les caractéristiques et le prix d’un trajet en taxi. Ouvrez et exécutez le dataflow, puis regardez les résultats :


```python
import azureml.dataprep as dprep

file_path = os.path.join(os.getcwd(), "dflows.dprep")

dflow_prepared = dprep.Dataflow.open(file_path)
dflow_prepared.get_profile()
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>max</th>
      <th>Nombre</th>
      <th>Manquant</th>
      <th>Non manquant</th>
      <th>Manquant (%)</th>
      <th>Erreurs</th>
      <th>Vide</th>
      <th>Quantile 0,1 %</th>
      <th>Quantile 1 %</th>
      <th>Quantile 5 %</th>
      <th>Quantile 25 %</th>
      <th>Quantile 50 %</th>
      <th>Quantile 75 %</th>
      <th>Quantile 95 %</th>
      <th>Quantile 99 %</th>
      <th>Quantile 99,9 %</th>
      <th>Moyenne</th>
      <th>Écart standard</th>
      <th>Variance</th>
      <th>Asymétrie</th>
      <th>Kurtosis</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>vendor</th>
      <td>FieldType.STRING</td>
      <td>1</td>
      <td>VTS</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_weekday</th>
      <td>FieldType.STRING</td>
      <td>Vendredi</td>
      <td>Mercredi</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>2,90047</td>
      <td>2,69355</td>
      <td>9,72889</td>
      <td>16</td>
      <td>19,3713</td>
      <td>22,6974</td>
      <td>23</td>
      <td>23</td>
      <td>14,2731</td>
      <td>6,59242</td>
      <td>43,46</td>
      <td>-0,693723</td>
      <td>-0,570403</td>
    </tr>
    <tr>
      <th>pickup_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>4,99701</td>
      <td>4,95833</td>
      <td>14,1528</td>
      <td>29,3832</td>
      <td>44,6825</td>
      <td>56,4444</td>
      <td>58,9909</td>
      <td>59</td>
      <td>29,427</td>
      <td>17,4333</td>
      <td>303,921</td>
      <td>0,0120999</td>
      <td>-1,20981</td>
    </tr>
    <tr>
      <th>pickup_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5,28131</td>
      <td>5.</td>
      <td>14,7832</td>
      <td>29,9293</td>
      <td>44,725</td>
      <td>56,7573</td>
      <td>59</td>
      <td>59</td>
      <td>29,7443</td>
      <td>17,3595</td>
      <td>301,351</td>
      <td>-0,0252399</td>
      <td>-1,19616</td>
    </tr>
    <tr>
      <th>dropoff_weekday</th>
      <td>FieldType.STRING</td>
      <td>Vendredi</td>
      <td>Mercredi</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>dropoff_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>2,57153</td>
      <td>2</td>
      <td>9,58795</td>
      <td>15,9994</td>
      <td>19,6184</td>
      <td>22,8317</td>
      <td>23</td>
      <td>23</td>
      <td>14,2105</td>
      <td>6,71093</td>
      <td>45,0365</td>
      <td>-0,687292</td>
      <td>-0,61951</td>
    </tr>
    <tr>
      <th>dropoff_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5,44383</td>
      <td>4,84694</td>
      <td>14,1036</td>
      <td>28,8365</td>
      <td>44,3102</td>
      <td>56,6892</td>
      <td>59</td>
      <td>59</td>
      <td>29,2907</td>
      <td>17,4108</td>
      <td>303,136</td>
      <td>0,0222514</td>
      <td>-1,2181</td>
    </tr>
    <tr>
      <th>dropoff_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5,07801</td>
      <td>5.</td>
      <td>14,5751</td>
      <td>29,5972</td>
      <td>45,4649</td>
      <td>56,2729</td>
      <td>59</td>
      <td>59</td>
      <td>29,772</td>
      <td>17,5337</td>
      <td>307,429</td>
      <td>-0,0212575</td>
      <td>-1,226</td>
    </tr>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>O</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>74,0781</td>
      <td>-73,7459</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74,0578</td>
      <td>-73,9639</td>
      <td>-73,9656</td>
      <td>-73,9508</td>
      <td>-73,9255</td>
      <td>-73,8529</td>
      <td>-73,8302</td>
      <td>-73,8238</td>
      <td>-73,7697</td>
      <td>-73,9123</td>
      <td>0,0503757</td>
      <td>0,00253771</td>
      <td>0,352172</td>
      <td>-0,923743</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40,5755</td>
      <td>40,8799</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40,632</td>
      <td>40,7117</td>
      <td>40,7115</td>
      <td>40,7213</td>
      <td>40,7565</td>
      <td>40,8058</td>
      <td>40,8478</td>
      <td>40,8676</td>
      <td>40,8778</td>
      <td>40,7649</td>
      <td>0,0494674</td>
      <td>0,00244702</td>
      <td>0,205972</td>
      <td>-0,777945</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74,0857</td>
      <td>-73,7209</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74,0775</td>
      <td>-73,9875</td>
      <td>-73,9882</td>
      <td>-73,9638</td>
      <td>-73,935</td>
      <td>-73,8755</td>
      <td>-73,8125</td>
      <td>-73,7759</td>
      <td>-73,7327</td>
      <td>-73,9202</td>
      <td>0,0584627</td>
      <td>0,00341789</td>
      <td>0,623622</td>
      <td>-0,262603</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40,5835</td>
      <td>40,8797</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40,5973</td>
      <td>40,6928</td>
      <td>40,6911</td>
      <td>40,7226</td>
      <td>40,7567</td>
      <td>40,7918</td>
      <td>40,8495</td>
      <td>40,868</td>
      <td>40,8787</td>
      <td>40,7583</td>
      <td>0,0517399</td>
      <td>0,00267701</td>
      <td>0,0390404</td>
      <td>-0,203525</td>
    </tr>
    <tr>
      <th>passengers</th>
      <td>FieldType.DECIMAL</td>
      <td>1</td>
      <td>6.</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>5.</td>
      <td>5.</td>
      <td>6.</td>
      <td>6.</td>
      <td>2,39249</td>
      <td>1,83197</td>
      <td>3,3561</td>
      <td>0,763144</td>
      <td>-1,23467</td>
    </tr>
    <tr>
      <th>distance</th>
      <td>FieldType.DECIMAL</td>
      <td>0.01</td>
      <td>32,34</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0,0108744</td>
      <td>0,743898</td>
      <td>0,738194</td>
      <td>1,243</td>
      <td>2,40168</td>
      <td>4,74478</td>
      <td>10,5136</td>
      <td>14,9011</td>
      <td>21,8035</td>
      <td>3,5447</td>
      <td>3,2943</td>
      <td>10,8524</td>
      <td>1,91556</td>
      <td>4,99898</td>
    </tr>
    <tr>
      <th>coût</th>
      <td>FieldType.DECIMAL</td>
      <td>0.1</td>
      <td>88</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>6148,0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2,33837</td>
      <td>5,00491</td>
      <td>5.</td>
      <td>6,93129</td>
      <td>10,524</td>
      <td>17,4811</td>
      <td>33,2343</td>
      <td>50,0093</td>
      <td>63,1753</td>
      <td>13,6843</td>
      <td>9,66571</td>
      <td>93,426</td>
      <td>1,78518</td>
      <td>4,13972</td>
    </tr>
  </tbody>
</table>

Préparez les données de l’expérience en ajoutant des colonnes à `dflow_x` en tant que caractéristiques pour la création de notre modèle. Définissez `dflow_y` comme valeur de prédiction, **cost** :

```python
dflow_X = dflow_prepared.keep_columns(['pickup_weekday','pickup_hour', 'distance','passengers', 'vendor'])
dflow_y = dflow_prepared.keep_columns('cost')
```

### <a name="split-the-data-into-train-and-test-sets"></a>Diviser les données entre un jeu d’entraînement et un jeu de test

À présent, divisez les données entre un jeu d’entraînement et un jeu de test à l’aide de la fonction `train_test_split` de la bibliothèque `sklearn`. Cette fonction sépare les données entre le jeu de données x **caractéristiques** pour l’entraînement du modèle, et le jeu de données y pour les tests **valeurs à prédire**. Le paramètre `test_size` détermine le pourcentage de données à allouer pour les tests. Le paramètre `random_state` définit une valeur initiale pour le générateur aléatoire, afin que les deux jeux de données soient toujours déterministes :

```python
from sklearn.model_selection import train_test_split

x_df = dflow_X.to_pandas_dataframe()
y_df = dflow_y.to_pandas_dataframe()

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
# flatten y_train to 1d array
y_train.values.flatten()
```

L’objectif de cette étape est d’obtenir des points de données qui n’ont pas été utilisés pour entraîner le modèle, afin de mesurer la véritable précision en testant le modèle terminé. En d’autres termes, un modèle bien entraîné doit être en mesure d’établir avec précision des prédictions à partir de données qu’il n’a pas encore vues. Vous disposez maintenant des packages et données nécessaires pour l’entraînement automatique de votre modèle.

## <a name="automatically-train-a-model"></a>Entraîner automatiquement un modèle

Pour entraîner automatiquement un modèle, effectuez les étapes suivantes :
1. Définissez des paramètres pour l’exécution de l’expérience. Attachez vos données d’entraînement à la configuration et modifiez les paramètres qui contrôlent le processus d’entraînement.
1. Soumettez l’expérience pour l’optimisation du modèle. Une fois l’expérience envoyée, le processus itère dans différents algorithmes et paramètres hyperparamètres de Machine Learning tout en respectant vos contraintes définies. Il choisit le modèle le mieux adapté en optimisant une métrique de précision.

### <a name="define-settings-for-autogeneration-and-tuning"></a>Définir des paramètres pour la génération automatique et l’optimisation

Définissez les paramètres du modèle et de l’expérience pour la génération automatique et l’optimisation. Affichez la liste complète des [paramètres](how-to-configure-auto-train.md). La soumission de l’expérience avec ces paramètres par défaut peut prendre entre 10 et 15 min, mais si vous souhaitez raccourcir la durée d’exécution, diminuez `iterations` ou `iteration_timeout_minutes`.


|Propriété| Valeur dans ce didacticiel |Description|
|----|----|---|
|**iteration_timeout_minutes**|10|Limite de temps (en minutes) pour chaque itération. Diminuez cette valeur afin de réduire le runtime total.|
|**iterations**|30|Nombre d’itérations. Dans chaque itération, un nouveau modèle Machine Learning est entraîné avec vos données. Il s’agit de la valeur principale qui influe sur le runtime total.|
|**primary_metric**| spearman_correlation | Métrique que vous souhaitez optimiser. Le modèle le mieux adapté est choisi en fonction de cette métrique.|
|**preprocess**| True | En utilisant **True**, l’expérience peut traiter les données d’entrée au préalable (gestion des données manquantes, conversion du texte en caractères numériques, etc.)|
|**verbosity**| logging.INFO | Contrôle le niveau de journalisation.|
|**n_cross_validations**|5.|Nombre de divisions de validation croisée à effectuer lorsque les données de validation ne sont pas spécifiées.|



```python
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 30,
    "primary_metric" : 'spearman_correlation',
    "preprocess" : True,
    "verbosity" : logging.INFO,
    "n_cross_validations": 5
}
```

Utilisez vos paramètres d’entraînement définis en tant que paramètre sur un objet `AutoMLConfig`. De plus, spécifiez vos données d’entraînement et le type de modèle, c’est-à-dire `regression` dans ce cas.

```python
from azureml.train.automl import AutoMLConfig

# local compute
automated_ml_config = AutoMLConfig(task = 'regression',
                             debug_log = 'automated_ml_errors.log',
                             path = project_folder,
                             X = x_train.values,
                             y = y_train.values.flatten(),
                             **automl_settings)
```

### <a name="train-the-automatic-regression-model"></a>Entraîner le modèle de régression automatique

Lancez l’exécution de l’expérience en local. Passez l’objet `automated_ml_config` défini à l’expérience. Affectez la valeur `True` à la sortie pour afficher la progression au cours de l’expérience :


```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, experiment_name)
local_run = experiment.submit(automated_ml_config, show_output=True)
```

La sortie illustrée se met à jour en temps réel à mesure que l’expérience s’exécute. Pour chaque itération, vous voyez le type de modèle, la durée d’exécution et la précision de l’entraînement. Le champ `BEST` effectue un suivi du meilleur score d’entraînement en cours d’exécution, en fonction de votre type de métrique.

    Parent Run ID: AutoML_02778de3-3696-46e9-a71b-521c8fca0651
    *******************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    *******************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler ExtremeRandomTrees                0:00:08       0.9447    0.9447
             1   StandardScalerWrapper GradientBoosting         0:00:09       0.9536    0.9536
             2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.8580    0.9536
             3   StandardScalerWrapper RandomForest             0:00:08       0.9147    0.9536
             4   StandardScalerWrapper ExtremeRandomTrees       0:00:45       0.9398    0.9536
             5   MaxAbsScaler LightGBM                          0:00:08       0.9562    0.9562
             6   StandardScalerWrapper ExtremeRandomTrees       0:00:27       0.8282    0.9562
             7   StandardScalerWrapper LightGBM                 0:00:07       0.9421    0.9562
             8   MaxAbsScaler DecisionTree                      0:00:08       0.9526    0.9562
             9   MaxAbsScaler RandomForest                      0:00:09       0.9355    0.9562
            10   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            11   MaxAbsScaler LightGBM                          0:00:09       0.9553    0.9602
            12   MaxAbsScaler DecisionTree                      0:00:07       0.9484    0.9602
            13   MaxAbsScaler LightGBM                          0:00:08       0.9540    0.9602
            14   MaxAbsScaler RandomForest                      0:00:10       0.9365    0.9602
            15   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            16   StandardScalerWrapper ExtremeRandomTrees       0:00:49       0.9171    0.9602
            17   SparseNormalizer LightGBM                      0:00:08       0.9191    0.9602
            18   MaxAbsScaler DecisionTree                      0:00:08       0.9402    0.9602
            19   StandardScalerWrapper ElasticNet               0:00:08       0.9603    0.9603
            20   MaxAbsScaler DecisionTree                      0:00:08       0.9513    0.9603
            21   MaxAbsScaler SGD                               0:00:08       0.9603    0.9603
            22   MaxAbsScaler SGD                               0:00:10       0.9602    0.9603
            23   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            24   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            25   MaxAbsScaler SGD                               0:00:09       0.9603    0.9603
            26   TruncatedSVDWrapper ElasticNet                 0:00:09       0.9602    0.9603
            27   MaxAbsScaler SGD                               0:00:12       0.9413    0.9603
            28   StandardScalerWrapper ElasticNet               0:00:07       0.9603    0.9603
            29    Ensemble                                      0:00:38       0.9622    0.9622

## <a name="explore-the-results"></a>Lire les résultats

Explorez les résultats de l’entraînement automatique à l’aide d’un widget Jupyter ou dans l’historique de l’expérience.

### <a name="option-1-add-a-jupyter-widget-to-see-results"></a>Option 1 : Ajouter un widget Jupyter pour voir les résultats

Si vous utilisez un notebook Jupyter, utilisez ce widget Jupyter Notebook pour afficher les résultats sous forme de graphe ou de tableau :


```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Détails de l’exécution du widget Jupyter](./media/tutorial-auto-train-models/automl-dash-output.png)
![Tracé du widget Jupyter](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="option-2-get-and-examine-all-run-iterations-in-python"></a>Option 2 : Obtenir et examiner toutes les itérations d’exécution dans Python

Vous pouvez également récupérer l’historique de chaque expérience et explorer les métriques associées à chaque exécution d’itération. En examinant RMSE (root_mean_squared_error) pour chaque exécution de modèle, vous constatez que la plupart des itérations prédisent le prix correct du taxi à l’intérieur d’une marge raisonnable (3 à 4 dollars US).

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items() if isinstance(v, float)}
    metricslist[int(properties['iteration'])] = metrics

rundata = pd.DataFrame(metricslist).sort_index(1)
rundata
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5.</th>
      <th>6.</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>20</th>
      <th>21</th>
      <th>22</th>
      <th>23</th>
      <th>24</th>
      <th>25</th>
      <th>26</th>
      <th>27</th>
      <th>28</th>
      <th>29</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>explained_variance</th>
      <td>0,811037</td>
      <td>0,880553</td>
      <td>0,398582</td>
      <td>0,776040</td>
      <td>0,663869</td>
      <td>0,875911</td>
      <td>0,115632</td>
      <td>0,586905</td>
      <td>0,851911</td>
      <td>0,793964</td>
      <td>...</td>
      <td>0,850023</td>
      <td>0,883603</td>
      <td>0,883704</td>
      <td>0,880797</td>
      <td>0,881564</td>
      <td>0,883708</td>
      <td>0,881826</td>
      <td>0,585377</td>
      <td>0,883123</td>
      <td>0,886817</td>
    </tr>
    <tr>
      <th>mean_absolute_error</th>
      <td>2,189444</td>
      <td>1,500412</td>
      <td>5,480531</td>
      <td>2,626316</td>
      <td>2,973026</td>
      <td>1,550199</td>
      <td>6,383868</td>
      <td>4,414241</td>
      <td>1,743328</td>
      <td>2,294601</td>
      <td>...</td>
      <td>1,797402</td>
      <td>1,415815</td>
      <td>1,418167</td>
      <td>1,578617</td>
      <td>1,559427</td>
      <td>1,413042</td>
      <td>1,551698</td>
      <td>4,069196</td>
      <td>1,505795</td>
      <td>1,430957</td>
    </tr>
    <tr>
      <th>median_absolute_error</th>
      <td>1,438417</td>
      <td>0,850899</td>
      <td>4,579662</td>
      <td>1,765210</td>
      <td>1,594600</td>
      <td>0,869883</td>
      <td>4,266450</td>
      <td>3,627355</td>
      <td>0,954992</td>
      <td>1,361014</td>
      <td>...</td>
      <td>0,973634</td>
      <td>0,774814</td>
      <td>0,797269</td>
      <td>1,147234</td>
      <td>1,116424</td>
      <td>0,783958</td>
      <td>1,098464</td>
      <td>2,709027</td>
      <td>1,003728</td>
      <td>0,851724</td>
    </tr>
    <tr>
      <th>normalized_mean_absolute_error</th>
      <td>0,024908</td>
      <td>0,017070</td>
      <td>0,062350</td>
      <td>0,029878</td>
      <td>0,033823</td>
      <td>0,017636</td>
      <td>0,072626</td>
      <td>0,050219</td>
      <td>0,019833</td>
      <td>0,026105</td>
      <td>...</td>
      <td>0,020448</td>
      <td>0,016107</td>
      <td>0,016134</td>
      <td>0,017959</td>
      <td>0,017741</td>
      <td>0,016076</td>
      <td>0,017653</td>
      <td>0,046293</td>
      <td>0,017131</td>
      <td>0,016279</td>
    </tr>
    <tr>
      <th>normalized_median_absolute_error</th>
      <td>0,016364</td>
      <td>0,009680</td>
      <td>0,052101</td>
      <td>0,020082</td>
      <td>0,018141</td>
      <td>0,009896</td>
      <td>0,048538</td>
      <td>0,041267</td>
      <td>0,010865</td>
      <td>0,015484</td>
      <td>...</td>
      <td>0,011077</td>
      <td>0,008815</td>
      <td>0,009070</td>
      <td>0,013052</td>
      <td>0,012701</td>
      <td>0,008919</td>
      <td>0,012497</td>
      <td>0,030819</td>
      <td>0,011419</td>
      <td>0,009690</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_error</th>
      <td>0,047968</td>
      <td>0,037882</td>
      <td>0,085572</td>
      <td>0,052282</td>
      <td>0,065809</td>
      <td>0,038664</td>
      <td>0,109401</td>
      <td>0,071104</td>
      <td>0,042294</td>
      <td>0,049967</td>
      <td>...</td>
      <td>0,042565</td>
      <td>0,037685</td>
      <td>0,037557</td>
      <td>0,037643</td>
      <td>0,037513</td>
      <td>0,037560</td>
      <td>0,037465</td>
      <td>0,072077</td>
      <td>0,037249</td>
      <td>0,036716</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_log_error</th>
      <td>0,055353</td>
      <td>0,045000</td>
      <td>0,110219</td>
      <td>0,065633</td>
      <td>0,063589</td>
      <td>0,044412</td>
      <td>0,123433</td>
      <td>0,092312</td>
      <td>0,046130</td>
      <td>0,055243</td>
      <td>...</td>
      <td>0,046540</td>
      <td>0,041804</td>
      <td>0,041771</td>
      <td>0,045175</td>
      <td>0,044628</td>
      <td>0,041617</td>
      <td>0,044405</td>
      <td>0,079651</td>
      <td>0,042799</td>
      <td>0,041530</td>
    </tr>
    <tr>
      <th>r2_score</th>
      <td>0,810900</td>
      <td>0,880328</td>
      <td>0,398076</td>
      <td>0,775957</td>
      <td>0,642812</td>
      <td>0,875719</td>
      <td>0,021603</td>
      <td>0,586514</td>
      <td>0,851767</td>
      <td>0,793671</td>
      <td>...</td>
      <td>0,849809</td>
      <td>0,880142</td>
      <td>0,880952</td>
      <td>0,880586</td>
      <td>0,881347</td>
      <td>0,880887</td>
      <td>0,881613</td>
      <td>0,548121</td>
      <td>0,882883</td>
      <td>0,886321</td>
    </tr>
    <tr>
      <th>root_mean_squared_error</th>
      <td>4,216362</td>
      <td>3,329810</td>
      <td>7,521765</td>
      <td>4,595604</td>
      <td>5,784601</td>
      <td>3,398540</td>
      <td>9,616354</td>
      <td>6,250011</td>
      <td>3,717661</td>
      <td>4,392072</td>
      <td>...</td>
      <td>3,741447</td>
      <td>3,312533</td>
      <td>3,301242</td>
      <td>3,308795</td>
      <td>3,297389</td>
      <td>3,301485</td>
      <td>3,293182</td>
      <td>6,335581</td>
      <td>3,274209</td>
      <td>3,227365</td>
    </tr>
    <tr>
      <th>root_mean_squared_log_error</th>
      <td>0,243184</td>
      <td>0,197702</td>
      <td>0,484227</td>
      <td>0,288349</td>
      <td>0,279367</td>
      <td>0,195116</td>
      <td>0,542281</td>
      <td>0,405559</td>
      <td>0,202666</td>
      <td>0,242702</td>
      <td>...</td>
      <td>0,204464</td>
      <td>0,183658</td>
      <td>0,183514</td>
      <td>0,198468</td>
      <td>0,196067</td>
      <td>0,182836</td>
      <td>0,195087</td>
      <td>0,349935</td>
      <td>0,188031</td>
      <td>0,182455</td>
    </tr>
    <tr>
      <th>spearman_correlation</th>
      <td>0,944743</td>
      <td>0,953618</td>
      <td>0,857965</td>
      <td>0,914703</td>
      <td>0,939846</td>
      <td>0,956159</td>
      <td>0,828187</td>
      <td>0,942069</td>
      <td>0,952581</td>
      <td>0,935477</td>
      <td>...</td>
      <td>0,951287</td>
      <td>0,960335</td>
      <td>0,960195</td>
      <td>0,960279</td>
      <td>0,960288</td>
      <td>0,960323</td>
      <td>0,960161</td>
      <td>0,941254</td>
      <td>0,960293</td>
      <td>0,962158</td>
    </tr>
    <tr>
      <th>spearman_correlation_max</th>
      <td>0,944743</td>
      <td>0,953618</td>
      <td>0,953618</td>
      <td>0,953618</td>
      <td>0,953618</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>...</td>
      <td>0,960303</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,962158</td>
    </tr>
  </tbody>
</table>
<p>12 lignes × 30 colonnes</p>
</div>

## <a name="retrieve-the-best-model"></a>Récupérer le meilleur modèle

Sélectionnez le meilleur pipeline des itérations. La méthode `get_output` de `automl_classifier` retourne la meilleure exécution ainsi que le modèle ajusté pour le dernier appel d’ajustement. En utilisant des surcharges sur `get_output`, vous pouvez récupérer la meilleure exécution et le modèle ajusté pour toute métrique journalisée ou itération :

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-the-best-model-accuracy"></a>Tester la précision du meilleur modèle

Utilisez le meilleur modèle pour exécuter des prédictions sur le jeu de données de test et prédire des tarifs de taxi. La fonction `predict` utilise le meilleur modèle et prédit les valeurs de y **coût du trajet** à partir du jeu de données `x_test`. Affichez les 10 premières valeurs de coût à partir de `y_predict` :

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Créer un tracé de points pour visualiser les valeurs de coût prédites par rapport aux valeurs de coût réelles. Le code suivant utilise la caractéristique `distance` comme axe X et le trajet `cost` comme axe Y. Pour comparer l’écart de coût prédit à chaque valeur de distance de trajet, les 100 premières valeurs de coût prédites et réelles sont créées en tant que séries distinctes. Le tracé montre que la relation de distance/coût est quasi linéaire, et les valeurs de coût prédites sont dans la plupart des cas très proches des valeurs de coût réelles pour la même distance de trajet.

```python
import matplotlib.pyplot as plt

fig = plt.figure(figsize=(14, 10))
ax1 = fig.add_subplot(111)

distance_vals = [x[4] for x in x_test.values]
y_actual = y_test.values.flatten().tolist()

ax1.scatter(distance_vals[:100], y_predict[:100], s=18, c='b', marker="s", label='Predicted')
ax1.scatter(distance_vals[:100], y_actual[:100], s=18, c='r', marker="o", label='Actual')

ax1.set_xlabel('distance (mi)')
ax1.set_title('Predicted and Actual Cost/Distance')
ax1.set_ylabel('Cost ($)')

plt.legend(loc='upper left', prop={'size': 12})
plt.rcParams.update({'font.size': 14})
plt.show()
```

![Tracé de points de prédiction](./media/tutorial-auto-train-models/automl-scatter-plot.png)

Calculez le(la) `root mean squared error` des résultats. Utilisez le dataframe `y_test`. Convertissez-le en liste à comparer aux valeurs prédites. La fonction `mean_squared_error` accepte deux tableaux de valeurs et calcule l’erreur au carré moyenne entre ces tableaux. En prenant la racine carrée du résultat, vous obtenez une erreur dans les mêmes unités que la variable y, **cost**. Cela indique à peu près l’écart existant entre les prédictions de tarifs de taxi et les tarifs réels :

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

    3.2204936862688798

Exécutez le code suivant pour calculer le pourcentage d’erreur de l’absolue moyenne (MAPE) à l’aide des jeux de données `y_actual` et `y_predict` complets. Cette métrique calcule une différence absolue entre chaque valeur prédite et réelle et additionne toutes les différences. Ensuite, elle exprime cette somme sous forme de pourcentage du total des valeurs réelles :

```python
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

    Model MAPE:
    0.10545153869569586

    Model Accuracy:
    0.8945484613043041

Dans les métriques de précision de la prédiction finale, vous voyez que le modèle est assez bon dans la prédiction de tarifs de taxi à partir des caractéristiques du jeu de données, en général à ± 3,00 dollars US. Le processus traditionnel de développement des modèles d’entraînement est très gourmand en ressources, et nécessite des investissements importants en temps et en connaissances pour exécuter et comparer les résultats de dizaines de modèles. Le machine learning automatisé se révèle un excellent moyen de tester rapidement de nombreux modèles pour votre scénario.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel sur le machine learning automatisé, vous avez effectué les tâches suivantes :

> [!div class="checklist"]
> * Configuration d’un espace de travail et préparation des données pour une expérience
> * Entraînement à l’aide d’un modèle de régression automatisé local avec des paramètres personnalisés
> * Exploration et analyse des résultats de l’entraînement

[Déployez votre modèle](tutorial-deploy-models-with-aml.md) avec Azure Machine Learning.
