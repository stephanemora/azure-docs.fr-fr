---
title: Générez et déployez un modèle de classification de texte à l’aide du package Azure Machine Learning pour Analyse de texte.
description: Découvrez comment générer, former, tester et déployer un modèle de classification de texte à l’aide du package Azure Machine Learning pour Analyse de texte.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 05/07/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 73f95280cc02b6f891c4ef67cd11084768d7d282
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2018
ms.locfileid: "51853011"
---
# <a name="build-and-deploy-text-classification-models-with-azure-machine-learning"></a>Générer et déployer des modèles de classification de texte avec Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


Dans cet article, vous apprenez à former et à déployer un modèle de classification de texte avec AMLPTA, le **package Azure Machine Learning pour Analyse de texte**. L’objectif de la classification de texte vise à affecter une portion de texte à une ou plusieurs classes ou catégories prédéfinies. Ce texte, par exemple, peut être un document, un article de presse, une requête de recherche, un e-mail, un tweet, des tickets de support.

Il existe de vastes applications en matière de classification de texte, parmi celles-ci : 
+ Catégorisation d’articles de presse et de fils d’actualité en rubriques
+ Organisation en catégories hiérarchiques de pages web 
+ Filtrage de courrier indésirable
+ analyse de sentiments
+ Prédiction d’intention de l’utilisateur à partir de requêtes de recherche
+ Routage de tickets de support
+ Analyse de commentaires de clients 

Le flux de travail lié à la génération et au déploiement d’un modèle de classification de texte avec AMLPTA est le suivant :

1. Chargement des données
2. Formation du modèle
3. Application du classifieur 
4. Évaluation des performances du modèle
5. Enregistrement du pipeline
6. Test du pipeline
8. Déploiement du modèle en tant que service web

Consultez la [documentation de référence du package](https://aka.ms/aml-packages/text) pour obtenir des informations détaillées sur chaque module et chaque classe.

Dans cet article, l’exemple de code utilise un pipeline scikit-learn.

## <a name="prerequisites"></a>Prérequis 

1. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

1. Les comptes et l’application suivants doivent être configurés et installés :
   - Un compte Azure Machine Learning - Expérimentation 
   - Un compte Azure Machine Learning - Gestion des modèles
   - Azure Machine Learning Workbench installé.

   Si ces trois éléments ne sont pas déjà créés ou installés, suivez le [Guide de démarrage rapide d’Azure Machine Learning et l’installation de Workbench](../desktop-workbench/quickstart-installation.md). 

1. Le package Azure Machine Learning pour Analyse de texte doit être installé. Découvrez comment [installer ce package ici](https://aka.ms/aml-packages/text).


## <a name="sample-data-and-jupyter-notebook"></a>Exemples de données et bloc-notes Jupyter

### <a name="get-the-jupyter-notebook"></a>Se procurer le bloc-notes Jupyter

Essayez-le. Téléchargez le bloc-notes et utilisez-le.

> [!div class="nextstepaction"]
> [Se procurer le bloc-notes Jupyter](https://aka.ms/aml-packages/text/notebooks/text_classification_sentiment_data)

### <a name="download-and-explore-the-sample-data"></a>Télécharger et explorer l’exemple de données
L’exemple suivant utilise le [jeu de données de 20 groupes de discussion](http://qwone.com/~jason/20Newsgroups/), qui est disponible via la bibliothèque scikit-learn, pour illustrer la création d’un classifieur de texte avec le package Azure Machine Learning pour Analyse de texte. 

Le jeu de données de 20 groupes de discussion contient environ 18 000 billets de discussion sur 20 sujets différents qui sont divisés en deux sous-ensembles : l’un pour la formation, l’autre pour l’évaluation des performances. Le fractionnement entre la formation et le test est effectué en fonction des dates de publication des messages, selon qu’elles se situent avant ou après une date spécifique.

```python
# Import Packages 
# Use Azure Machine Learning history magic to control history collection
# History is off by default, options are "on", "off", or "show"
#%azureml history on
%matplotlib inline
# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
import os

logger = get_azureml_logger()

# Log cell runs into run history
logger.log('Cell','Set up run')
# from tatk.utils import load_newsgroups_data, data_dir, dictionaries_dir, models_dir
import pip
pip.main(["show", "azureml-tatk"])
```

### <a name="set-the-location-of-the-data"></a>Définir l’emplacement des données
Définissez l’emplacement où vous avez téléchargé les données dans le paramètre de répertoire de données (data_dir). Vous pouvez également utiliser vos propres données, le jeu de données d’entrée devant être un fichier au format *.tsv.

```python
import os
import pandas as pd

#set the working directory where to save the training data files
resources_dir = os.path.join(os.path.expanduser("~"), "tatk", "resources")
data_dir = os.path.join(os.path.expanduser("~"), "tatk", "data")

from sklearn.datasets import fetch_20newsgroups
twenty_train = fetch_20newsgroups(data_home=data_dir, subset='train')
X_train, y_train = twenty_train.data, twenty_train.target
df_train = pd.DataFrame({"text":X_train, "label":y_train})

twenty_test = fetch_20newsgroups(data_home=data_dir, subset='test')
X_test, y_test = twenty_test.data, twenty_test.target   
df_test = pd.DataFrame({"text":X_test, "label":y_test})
    
# Training Dataset Location
#training_file_path = <specify-your-own-training-data-file-path-here>
# df_train = pd.read_csv(training_file_path,
#                        sep = '\t',                        
#                        header = 0, names= <specify-your-column-name-list-here>)
df_train.head()
print("df_train.shape= {}".format(df_train.shape))

# Test Dataset Location
#test_file_path = <specify-your-own-test-data-file-path-here>
# df_test = pd.read_csv(test_file_path,
#                       sep = '\t',                        
#                       header = 0, names= <specify-your-column-name-list-here>)

print("df_test.shape= {}".format(df_test.shape))
df_test.head()
```
    df_train.shape= (11314, 2)
    df_test.shape= (7532, 2)
 
 Les données comportent du texte (text) et des étiquettes (label)
    
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>label</th>
      <th>texte</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7</td>
      <td>From: v064mb9k@ubvmsd.cc.buffalo.edu (NEIL B. ...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>5.</td>
      <td>From: Rick Miller &lt;rick@ee.uwm.edu&gt;\nSubject: ...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>From: mathew &lt;mathew@mantis.co.uk&gt;\nSubject: R...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>17</td>
      <td>From: bakken@cs.arizona.edu (Dave Bakken)\nSub...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>19</td>
      <td>From: livesey@solntze.wpd.sgi.com (Jon Livesey...</td>
    </tr>
  </tbody>
</table>
</div>

Obtenez la correspondance entre chaque catégorie et le nom qui lui est associé.

```python
int_to_categories = pd.DataFrame({'category':range(20), 'category_name': list(twenty_train.target_names)})
int_to_categories 
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>category</th>
      <th>category_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>alt.atheism</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>comp.graphics</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>comp.os.ms-windows.misc</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>comp.sys.ibm.pc.hardware</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>comp.sys.mac.hardware</td>
    </tr>
    <tr>
      <th>5.</th>
      <td>5.</td>
      <td>comp.windows.x</td>
    </tr>
    <tr>
      <th>6.</th>
      <td>6.</td>
      <td>misc.forsale</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7</td>
      <td>rec.autos</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8</td>
      <td>rec.motorcycles</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9</td>
      <td>rec.sport.baseball</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10</td>
      <td>rec.sport.hockey</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11</td>
      <td>sci.crypt</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12</td>
      <td>sci.electronics</td>
    </tr>
    <tr>
      <th>13</th>
      <td>13</td>
      <td>sci.med</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14</td>
      <td>sci.space</td>
    </tr>
    <tr>
      <th>15</th>
      <td>15</td>
      <td>soc.religion.christian</td>
    </tr>
    <tr>
      <th>16</th>
      <td>16</td>
      <td>talk.politics.guns</td>
    </tr>
    <tr>
      <th>17</th>
      <td>17</td>
      <td>talk.politics.mideast</td>
    </tr>
    <tr>
      <th>18</th>
      <td>18</td>
      <td>talk.politics.misc</td>
    </tr>
    <tr>
      <th>19</th>
      <td>19</td>
      <td>talk.religion.misc</td>
    </tr>
  </tbody>
</table>
</div>

À présent, vous pouvez créer un histogramme des tracés de l’exploration préliminaire pour la fréquence des classes dans les jeux de données de formation et de test. 

```python
import numpy as np
import math
from matplotlib import pyplot as plt

data = df_train["label"].values
labels = set(data)
print(labels)
bins = range(len(labels)+1) 

#plt.xlim([min(data)-5, max(data)+5])

plt.hist(data, bins=bins, alpha=0.8)
plt.title('training data distribution over the class labels)')
plt.xlabel('class label')
plt.ylabel('frequency')
plt.grid(True)
plt.show()

data = df_test["label"].values
labels = set(data)
print(labels)
bins = range(len(labels)+1) 

#plt.xlim([min(data)-5, max(data)+5])

plt.hist(data, bins=bins, alpha=0.8)
plt.title('test data distribution over the class labels)')
plt.xlabel('class label')
plt.ylabel('frequency')
plt.grid(True)
plt.show()
```

Lors de l’exécution du bloc-notes Jupyter, les tracés s’affichent après l’exécution du bloc de code précédent.


## <a name="train-the-model"></a>Formation du modèle

### <a name="specify-scikit-learn-algorithm-and-define-the-text-classifier"></a>Spécifier l’algorithme scikit-learn et définir le classifieur de texte

Cette étape implique la formation d’un modèle de classification de texte scikit-learn à l’aide d’un algorithme d’apprentissage One-versus-Rest Logistic Regression (régression logistique Un contre le reste).

Pour obtenir la liste complète des apprentissages, reportez-vous à la documentation des [Utilisateurs de Scikit](http://scikit-learn.org/stable/supervised_learning).

```python
from sklearn.linear_model import LogisticRegression
import tatk
from tatk.pipelines.text_classification.text_classifier import TextClassifier

log_reg_learner =  LogisticRegression(penalty='l2', dual=False, tol=0.0001, 
                            C=1.0, fit_intercept=True, intercept_scaling=1, 
                            class_weight=None, random_state=None, 
                            solver='lbfgs', max_iter=100, multi_class='ovr',
                            verbose=1, warm_start=False, n_jobs=3) 

#train the model a text column "tweets"
text_classifier = TextClassifier(estimator=log_reg_learner, 
                                text_cols = ["text"], 
                                label_cols = ["label"], 
#                                 numeric_cols = None,
#                                 cat_cols = None, 
                                extract_word_ngrams=True, extract_char_ngrams=True)

```

    TextClassifier::create_pipeline ==> start
    :: number of jobs for the pipeline : 12
    0   text_nltk_preprocessor
    1   text_word_ngrams
    2   text_char_ngrams
    3   assembler
    4   learner
    TextClassifier::create_pipeline ==> end
    
### <a name="fit-the-model"></a>Ajuster le modèle

Utilisez les paramètres par défaut du package. Par défaut, le classifieur de texte extrait les éléments suivants :
+ Bigrammes et unigrammes de mot
+ 4-grammes de caractère

```python
text_classifier.fit(df_train)        
```
   
    TextClassifier::fit ==> start
    schema: col=label:I4:0 col=text:TX:1 header+
    NltkPreprocessor::tatk_fit_transform ==> start
    NltkPreprocessor::tatk_fit_transform ==> end     Time taken: 0.08 mins
    NGramsVectorizer::tatk_fit_transform ==> startNGramsVectorizer::tatk_fit_transform ==> start
    
                vocabulary size=216393
    NGramsVectorizer::tatk_fit_transform ==> end     Time taken: 0.41 mins
                vocabulary size=67230
    NGramsVectorizer::tatk_fit_transform ==> end     Time taken: 0.49 mins
    VectorAssembler::transform ==> start, num of input records=11314
    (11314, 216393)
    (11314, 67230)
    all_features::
    (11314, 283623)
    Time taken: 0.06 mins
    VectorAssembler::transform ==> end
    LogisticRegression::tatk_fit ==> start
    
    [Parallel(n_jobs=3)]: Done  20 out of  20 | elapsed:  2.4min finished
    
    LogisticRegression::tatk_fit ==> end     Time taken: 2.4 mins
    Time taken: 3.04 mins
    TextClassifier::fit ==> end

    TextClassifier(add_index_col=False, callable_proprocessors_list=None,
            cat_cols=None, char_hashing_original=False, col_prefix='tmp_00_',
            decompose_n_grams=False, detect_phrases=False,
            dictionary_categories=None, dictionary_file_path=None,
            embedding_file_path=None, embedding_file_path_fastText=None,
            estimator=None, estimator_vectorizers_list=None,
            extract_char_ngrams=True, extract_word_ngrams=True,
            label_cols=['label'], numeric_cols=None,
            pos_tagger_vectorizer=False,
            preprocessor_dictionary_file_path=None, regex_replcaement='',
            replace_regex_pattern=None, scale_numeric_cols=False,
            text_callable_list=None, text_cols=['text'], text_regex_list=None,
            weight_col=None)


Pendant la formation, vous devez avoir les colonnes de texte et d’étiquette. Cependant, seule la colonne de texte est nécessaire pour les prédictions. 

### <a name="examine-and-set-the-parameters-of-the-different-pipeline-steps"></a>Examiner et définir les paramètres des différentes étapes du pipeline
    
En règle générale, vous définissez les paramètres avant d’adapter un modèle. 

***Exemple illustré avec text_word_ngrams*** (n-grammes_mot_texte) 

Les exemples de code suivants montrent comment former le modèle à l’aide des paramètres de pipeline et de modèle par défaut. 

Pour voir quels paramètres sont inclus pour « text_word_ngrams », utilisez [get_step_param_names_by_name](/python/api/msft-tatk/tatk.core.base_text_model.basetextmodel#get-step-param-names-by-name). Cette fonction retourne les paramètres, tels que lowercase, input_col, output_col et ainsi de suite. 

```python
text_classifier.get_step_param_names_by_name("text_word_ngrams")
```

    ['min_df',
     'strip_accents',
     'max_df',
     'decode_error',
     'max_features',
     'binary',
     'input',
     'vocabulary',
     'analyzer',
     'token_pattern',
     'encoding',
     'use_idf',
     'save_overwrite',
     'output_col',
     'stop_words',
     'sublinear_tf',
     'input_col',
     'lowercase',
     'ngram_range',
     'preprocessor',
     'tokenizer',
     'hashing',
     'dtype',
     'norm',
     'smooth_idf',
     'n_hashing_features']

Ensuite, vérifiez les valeurs des paramètres pour « text_char_ngrams » (n-grammes_carac_texte) :

```python
text_classifier.get_step_params_by_name("text_char_ngrams")        
```
    {'analyzer': 'char_wb',
     'binary': False,
     'decode_error': 'strict',
     'dtype': numpy.float32,
     'encoding': 'utf-8',
     'hashing': False,
     'input': 'content',
     'input_col': 'NltkPreprocessor5283a730506549cc880f074e750607b0',
     'lowercase': True,
     'max_df': 1.0,
     'max_features': None,
     'min_df': 3,
     'n_hashing_features': None,
     'ngram_range': (4, 4),
     'norm': 'l2',
     'output_col': 'NGramsVectorizer8eb11031f6b64eaaad9ff0fd3b0f5b80',
     'preprocessor': None,
     'save_overwrite': True,
     'smooth_idf': True,
     'stop_words': None,
     'strip_accents': None,
     'sublinear_tf': False,
     'token_pattern': '(?u)\\b\\w\\w+\\b',
     'tokenizer': None,
     'use_idf': True,
     'vocabulary': None}

Au besoin, vous pouvez modifier les paramètres par défaut.  Avec le code suivant, vous pouvez modifier la plage des n-grammes de caractère extraits, de (4,4) à (3,4), pour extraire des trigrammes et 4-grammes de caractère :

```python
text_classifier.set_step_params_by_name("text_char_ngrams", ngram_range =(3,4)) 
text_classifier.get_step_params_by_name("text_char_ngrams")
```
    {'analyzer': 'char_wb',
     'binary': False,
     'decode_error': 'strict',
     'dtype': numpy.float32,
     'encoding': 'utf-8',
     'hashing': False,
     'input': 'content',
     'input_col': 'NltkPreprocessor5283a730506549cc880f074e750607b0',
     'lowercase': True,
     'max_df': 1.0,
     'max_features': None,
     'min_df': 3,
     'n_hashing_features': None,
     'ngram_range': (3, 4),
     'norm': 'l2',
     'output_col': 'NGramsVectorizer8eb11031f6b64eaaad9ff0fd3b0f5b80',
     'preprocessor': None,
     'save_overwrite': True,
     'smooth_idf': True,
     'stop_words': None,
     'strip_accents': None,
     'sublinear_tf': False,
     'token_pattern': '(?u)\\b\\w\\w+\\b',
     'tokenizer': None,
     'use_idf': True,
     'vocabulary': None}

### <a name="export-the-parameters-to-a-file"></a>Exporter les paramètres dans un fichier
Si nécessaire, vous pouvez optimiser les performances du modèle en réexécutant l’étape d’ajustement du modèle avec les paramètres révisés :

```python
import os
params_file_path = os.path.join(data_dir, "params.tsv")
text_classifier.export_params(params_file_path)
```

## <a name="apply-the-classifier"></a>Application du classifieur

Appliquez le classifieur de texte formé sur le jeu de données de test pour générer des prédictions de classe :

```python
 df_test = text_classifier.predict(df_test)
```

    TextClassifier ::predict ==> start
    NltkPreprocessor::tatk_transform ==> start
    NltkPreprocessor::tatk_transform ==> end     Time taken: 0.05 mins
    NGramsVectorizer::tatk_transform ==> startNGramsVectorizer::tatk_transform ==> start
    
    NGramsVectorizer::tatk_transform ==> end     Time taken: 0.15 mins
    NGramsVectorizer::tatk_transform ==> end     Time taken: 0.37 mins
    VectorAssembler::transform ==> start, num of input records=7532
    (7532, 216393)
    (7532, 67230)
    all_features::
    (7532, 283623)
    Time taken: 0.03 mins
    VectorAssembler::transform ==> end
    LogisticRegression::tatk_predict_proba ==> start
    LogisticRegression::tatk_predict_proba ==> end   Time taken: 0.01 mins
    LogisticRegression::tatk_predict ==> start
    LogisticRegression::tatk_predict ==> end     Time taken: 0.01 mins
    Time taken: 0.46 mins
    TextClassifier ::predict ==> end
    Order of Labels in predicted probabilities saved to attribute label_order of the class object
    
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>label</th>
      <th>texte</th>
      <th>probabilities</th>
      <th>prediction</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>7</td>
      <td>From: v064mb9k@ubvmsd.cc.buffalo.edu (NEIL B. ...</td>
      <td>[0.0165036341329, 0.0548664746458, 0.020549685...</td>
      <td>12</td>
    </tr>
    <tr>
      <th>1</th>
      <td>5.</td>
      <td>From: Rick Miller &lt;rick@ee.uwm.edu&gt;\nSubject: ...</td>
      <td>[0.025145498995, 0.125877400021, 0.03947047877...</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0</td>
      <td>From: mathew &lt;mathew@mantis.co.uk&gt;\nSubject: R...</td>
      <td>[0.67566338235, 0.0150749738583, 0.00992439163...</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>17</td>
      <td>From: bakken@cs.arizona.edu (Dave Bakken)\nSub...</td>
      <td>[0.146063943868, 0.00232465192179, 0.002442807...</td>
      <td>18</td>
    </tr>
    <tr>
      <th>4</th>
      <td>19</td>
      <td>From: livesey@solntze.wpd.sgi.com (Jon Livesey...</td>
      <td>[0.670712265297, 0.017332269703, 0.01062429663...</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="evaluate-model-performance"></a>Évaluation des performances du modèle
Le [module d’évaluation](/python/api/msft-tatk/tatk.evaluation) évalue la précision du classifieur de texte formé sur le jeu de données de test. La fonction evaluate génère une matrice de confusion et fournit un score de macro-F1.

```python
 text_classifier.evaluate(df_test)          
```

    TextClassifier ::evaluate ==> start
    Time taken: 0.0 mins
    TextClassifier ::evaluate ==> end
    

Tracez la confusion sans matrice de normalisation pour la visualisation.


```python
evaluator.plot_confusion_matrix(normalize=False,
                                title='Confusion matrix, without normalization', 
                                print_confusion_matrix=False,
                                figsize=(8,8),
                                colors=None)
```

Matrice de confusion, sans normalisation
    
La matrice de confusion s’affiche lors de l’exécution du bloc-notes.



Tracez la matrice de confusion normalisée pour la visualisation.


```python
evaluator.plot_confusion_matrix(normalize=True,
                                title='Normalized Confusion matrix', 
                                print_confusion_matrix=False,
                                figsize=(8,8),
                                colors=None)
```

    Normalized confusion matrix
   
La matrice de confusion s’affiche lors de l’exécution du bloc-notes.

## <a name="save-the-pipeline"></a>Enregistrement du pipeline
Enregistrez le pipeline de classification dans un fichier zip. Enregistrez également les n-grammes de mot et de caractère dans des fichiers texte.

```python
import os
working_dir = os.path.join(data_dir, 'outputs')  
if not os.path.exists(working_dir):
    os.makedirs(working_dir)

# you can save the trained model as a folder or a zip file
model_file = os.path.join(working_dir, 'sk_model.zip')    
text_classifier.save(model_file)
# %azureml upload outputs/models/sk_model.zip

```
    BaseTextModel::save ==> start
    TatkPipeline::save ==> start
    Time taken: 0.28 mins
    TatkPipeline::save ==> end
    Time taken: 0.38 mins
    BaseTextModel::save ==> end
    
```python
# for debugging, you can save the word n-grams vocabulary to a text file
word_vocab_file_path = os.path.join(working_dir, 'word_ngrams_vocabulary.tsv')
text_classifier.get_step_by_name("text_word_ngrams").save_vocabulary(word_vocab_file_path) 
# %azureml upload outputs/dictionaries/word_ngrams_vocabulary.pkl

# for debugging, you can save the character n-grams vocabulary to a text file
char_vocab_file_path = os.path.join(working_dir, 'char_ngrams_vocabulary.tsv')
text_classifier.get_step_by_name("text_char_ngrams").save_vocabulary(char_vocab_file_path) 
# %azureml upload outputs/dictionaries/char_ngrams_vocabulary.pkl
```

    save_vocabulary ==> start
    saving 216393 n-grams ...
    Time taken: 0.01 mins
    save_vocabulary ==> end
    save_vocabulary ==> start
    saving 67230 n-grams ...
    Time taken: 0.0 mins
    save_vocabulary ==> end
 
## <a name="load-the-pipeline"></a>Charger le pipeline
Chargez le pipeline de classification ainsi que les n-grammes de mot et de caractère pour l’inférence :

```python
# in order to deploy the trained model, you have to load the zip file of the classifier pipeline
loaded_text_classifier = TextClassifier.load(model_file)

from tatk.feature_extraction import NGramsVectorizer
word_ngram_vocab = NGramsVectorizer.load_vocabulary(word_vocab_file_path)
char_ngram_vocab = NGramsVectorizer.load_vocabulary(char_vocab_file_path)
```
    BaseTextModel::load ==> start
    TatkPipeline::load ==> start
    Time taken: 0.14 mins
    TatkPipeline::load ==> end
    Time taken: 0.15 mins
    BaseTextModel::load ==> end
    loading 216393 n-grams ...
    loading 67230 n-grams ...
    

## <a name="test-the-pipeline"></a>Tester le pipeline

Pour évaluer un jeu de données de test, appliquez le pipeline de classification de texte chargé :

```python
predictions = loaded_text_classifier.predict(df_test)
loaded_evaluator = loaded_text_classifier.evaluate(predictions)
loaded_evaluator.get_metrics('macro_f1')
```
    TextClassifier ::predict ==> start
    NltkPreprocessor::tatk_transform ==> start
    NltkPreprocessor::tatk_transform ==> end     Time taken: 0.05 mins
    NGramsVectorizer::tatk_transform ==> startNGramsVectorizer::tatk_transform ==> start
    
    NGramsVectorizer::tatk_transform ==> end     Time taken: 0.14 mins
    NGramsVectorizer::tatk_transform ==> end     Time taken: 0.36 mins
    VectorAssembler::transform ==> start, num of input records=7532
    (7532, 216393)
    (7532, 67230)
    all_features::
    (7532, 283623)
    Time taken: 0.03 mins
    VectorAssembler::transform ==> end
    LogisticRegression::tatk_predict_proba ==> start
    LogisticRegression::tatk_predict_proba ==> end   Time taken: 0.01 mins
    LogisticRegression::tatk_predict ==> start
    LogisticRegression::tatk_predict ==> end     Time taken: 0.01 mins
    Time taken: 0.45 mins
    TextClassifier ::predict ==> end
    Order of Labels in predicted probabilities saved to attribute label_order of the class object
    TextClassifier ::evaluate ==> start
    Time taken: 0.0 mins
    TextClassifier ::evaluate ==> en
    
    0.82727029243808903

## <a name="operationalization-deploy-and-consume"></a>Opérationnalisation : déployer et consommer

Dans cette section, vous déployez le pipeline de classification de texte en tant que service web Azure Machine Learning avec [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning). Ensuite, vous utilisez le service web pour la formation et l’attribution de score.

**Connexion à votre abonnement Azure avec Azure CLI**

À l’aide d’un compte [Azure](https://azure.microsoft.com/) et d’un abonnement valide, connectez-vous au moyen de la commande CLI suivante :
<br>`az login`

+ Pour basculer vers un autre abonnement Azure, utilisez la commande :
<br>`az account set --subscription [your subscription name]`

+ Pour afficher le compte de gestion des modèles actif, utilisez la commande :
  <br>`az ml account modelmanagement show`

**Création et configuration de votre environnement de déploiement**

Vous n’avez besoin de configurer votre environnement de déploiement qu’une seule fois. Si vous n’en possédez pas déjà un, configurez votre environnement de déploiement maintenant en suivant [ces instructions](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup). 

1. Assurez-vous que votre environnement Azure Machine Learning, le compte de gestion des modèles et le groupe de ressources se trouvent dans la même région.

1. Téléchargez le fichier de configuration de déploiement depuis le stockage Blob et enregistrez-le localement :

   ```python
   # Download the deployment config file from Blob storage `url` and save it locally under `file_name`:
   deployment_config_file_url = 'https://aztatksa.blob.core.windows.net/dailyrelease/tatk_deploy_config.yaml'
   deployment_config_file_path=os.path.join(resources_dir, 'tatk_deploy_config.yaml')
   import urllib.request
   urllib.request.urlretrieve(deployment_config_file_url, deployment_config_file_path)
   ```

1. Mettez à jour le fichier de configuration de déploiement téléchargé pour refléter vos ressources :

   ```python
   web_service_name = 'please type your web service name'
   working_directory= os.path.join(resources_dir, 'deployment') 

   web_service = text_classifier.deploy(web_service_name= web_service_name, 
                          config_file_path=deployment_config_file_path,
                          working_directory= working_directory)  
   ```

1. Dans la mesure où le modèle formé est correctement déployé, appelez le service web d’attribution de score sur le nouveau jeu de données :

   ```python
   print("Service URL: {}".format(web_service._service_url))
   print("Service Key: {}".format(web_service._api_key))
   ```

1. Chargez le service web à tout moment à l’aide de son nom :

   ```python
   from tatk.operationalization.csi.csi_web_service import CsiWebService
   url = "<please type the service URL here>"
   key = "<please type the service Key here>"
   web_service = CsiWebService(url, key)
   ```

1. Testez le service web avec le corps de deux e-mails récupérés dans le jeu de données des 20 groupes de discussion :

   ```python
   # Example input data for scoring
   import json
   dict1 ={}
   dict1["recordId"] = "a1" 
   dict1["data"]= {}
   dict1["data"]["text"] = """
   I'd be interested in a copy of this code if you run across it.
   (Mail to the author bounced)
    > / hpldsla:comp.graphics / email-address-removed / 12:53 am  May 13,
    1993 /
    > I fooled around with this problem a few years ago, and implemented a
    > simple method that ran on a PC.
    > was very simple - about 40 or 50 lines of code.
    . . .
    > Somewhere I still have it
    > and could dig it out if there was interest.
   """
   
   dict2 ={}
   dict2["recordId"] = "b2"
   dict2["data"] ={}
   dict2["data"]["text"] = """
   >>Could the people discussing recreational drugs such as mj, lsd, mdma, etc.,
   >>take their discussions to alt.drugs? Their discussions will receive greatest
   >>contribution and readership there. The people interested in strictly
   >>"smart drugs" (i.e. Nootropics) should post to this group. The two groups
   >>(alt.drugs & alt.psychoactives) have been used interchangably lately.
   >>I do think that alt.psychoactives is a deceiving name. alt.psychoactives
   >>is supposedly the "smart drug" newsgroup according to newsgroup lists on
   >>the Usenet. Should we establish an alt.nootropics or alt.sdn (smart drugs &
   >>nutrients)? I have noticed some posts in sci.med.nutrition regarding
   >>"smart nutrients." We may lower that groups burden as well.
   >   

   I was wondering if a group called 'sci.pharmacology' would be relevent.
   This would be used for a more formal discussion about pharmacological
   issues (pharmacodynamics, neuropharmacology, etc.)      

   Just an informal proposal (I don't know anything about the net.politics
   for adding a newsgroup, etc.)

   """

   dict_list =[dict1, dict2]
   data ={}
   data["values"] = dict_list
   input_data_json_str = json.dumps(data)
   print (input_data_json_str)
   prediction = web_service.score(input_data_json_str)
   prediction
   ```

   ```
   {"values": [{"recordId": "a1", "data": {"text": "\nI'd be interested in a copy of this code if you run across it.\n(Mail to the author bounced)\n > / hpldsla:comp.graphics / email-address-removed / 12:53 am  May 13,\n 1993 /\n > I fooled around with this problem a few years ago, and implemented a\n > simple method that ran on a PC.\n > was very simple - about 40 or 50 lines of code.\n . . .\n > Somewhere I still have it\n > and could dig it out if there was interest.\n"}}, {"recordId": "b2", "data": {"text": "\n>>Could the people discussing recreational drugs such as mj, lsd, mdma, etc.,\n>>take their discussions to alt.drugs? Their discussions will receive greatest\n>>contribution and readership there. The people interested in strictly\n>>\"smart drugs\" (i.e. Nootropics) should post to this group. The two groups\n>>(alt.drugs & alt.psychoactives) have been used interchangably lately.\n>>I do think that alt.psychoactives is a deceiving name. alt.psychoactives\n>>is supposedly the \"smart drug\" newsgroup according to newsgroup lists on\n>>the Usenet. Should we establish an alt.nootropics or alt.sdn (smart drugs &\n>>nutrients)? I have noticed some posts in sci.med.nutrition regarding\n>>\"smart nutrients.\" We may lower that groups burden as well.\n>\n\nI was wondering if a group called 'sci.pharmacology' would be relevent.\nThis would be used for a more formal discussion about pharmacological\nissues (pharmacodynamics, neuropharmacology, etc.)\n\nJust an informal proposal (I don't know anything about the net.politics\nfor adding a newsgroup, etc.)\n\n"}}]}
   F1 2018-05-02 00:10:58,272 INFO Web service scored. 
    
   '{"values": [{"recordId": "b2", "data": {"text": "\\n>>Could the people discussing recreational drugs such as mj, lsd, mdma, etc.,\\n>>take their discussions to alt.drugs? Their discussions will receive greatest\\n>>contribution and readership there. The people interested in strictly\\n>>\\"smart drugs\\" (i.e. Nootropics) should post to this group. The two groups\\n>>(alt.drugs & alt.psychoactives) have been used interchangably lately.\\n>>I do think that alt.psychoactives is a deceiving name. alt.psychoactives\\n>>is supposedly the \\"smart drug\\" newsgroup according to newsgroup lists on\\n>>the Usenet. Should we establish an alt.nootropics or alt.sdn (smart drugs &\\n>>nutrients)? I have noticed some posts in sci.med.nutrition regarding\\n>>\\"smart nutrients.\\" We may lower that groups burden as well.\\n>\\n\\nI was wondering if a group called \'sci.pharmacology\' would be relevent.\\nThis would be used for a more formal discussion about pharmacological\\nissues (pharmacodynamics, neuropharmacology, etc.)\\n\\nJust an informal proposal (I don\'t know anything about the net.politics\\nfor adding a newsgroup, etc.)\\n\\n", "class": 13}}, {"recordId": "a1", "data": {"text": "\\nI\'d be interested in a copy of this code if you run across it.\\n(Mail to the author bounced)\\n > / hpldsla:comp.graphics / email-address-removed / 12:53 am  May 13,\\n 1993 /\\n > I fooled around with this problem a few years ago, and implemented a\\n > simple method that ran on a PC.\\n > was very simple - about 40 or 50 lines of code.\\n . . .\\n > Somewhere I still have it\\n > and could dig it out if there was interest.\\n", "class": 1}}]}'
   ```


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le package Azure Machine Learning pour Analyse de texte dans les articles suivants :

+ Lire la [vue d’ensemble du package](https://aka.ms/aml-packages/text).

+ Explorer la [documentation de référence](https://aka.ms/aml-packages/text) de ce package.

+ En savoir plus sur les [autres packages Python pour Azure Machine Learning](reference-python-package-overview.md).