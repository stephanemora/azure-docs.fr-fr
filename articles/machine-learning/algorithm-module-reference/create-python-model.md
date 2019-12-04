---
title: 'Create Python Model : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le modèle Create Python Model dans Azure Machine Learning pour créer un module personnalisé de modélisation ou de traitement de données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 11/19/2019
ms.openlocfilehash: 0c1a4f33da7e1f39951d641ed1d563c46fb664ca
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232646"
---
# <a name="create-python-model"></a>Créer un modèle Python

Cet article décrit un module dans le concepteur Azure Machine Learning (version préliminaire).

Découvrez comment utiliser le module **Create Python Model** pour créer un modèle non formé à partir d’un script Python. Vous pouvez baser le modèle sur tout apprenant inclus dans un package Python dans l’environnement du concepteur Azure Machine Learning. 

Après avoir créé le modèle, vous pouvez utiliser le module [Train Model](train-model.md) pour former le modèle sur un jeu de données, comme tout autre apprenant dans Azure Machine Learning. Le modèle formé peut être transmis au module [Score Model](score-model.md) pour utiliser le modèle afin d’effectuer des prédictions. Le modèle formé peut ensuite être enregistré et le workflow de notation publié en tant que service web.

> [!WARNING]
> Actuellement, il n’est pas possible de transmettre les résultats notés d’un modèle Python au module [Evaluate Model](evaluate-model.md). Si vous avez besoin évaluer un modèle, vous pouvez écrire un script Python personnalisé et l’exécuter à l’aide du module [Execute Python Script](execute-python-script.md).  


## <a name="how-to-configure-create-python-model"></a>Comment configurer un module Create Python Model

L’utilisation de ce module nécessite une connaissance intermédiaire ou approfondie de Python. Le module prend en charge l’utilisation de tout qui est inclus dans les packages Python déjà installés dans Azure Machine Learning. Consultez la liste des packages Python préinstallés dans le module [Execute Python Script](execute-python-script.md).
  

Cet article explique comment utiliser le module **Créer un modèle Python** avec un pipeline simple. Ci-dessous ce trouve le graphique du pipeline.

![create-python-model](./media/module/aml-create-python-model.png)

1.  Cliquez sur **Créer un modèle Python**, modifiez le script pour implémenter votre processus de modélisation ou de gestion des données. Vous pouvez baser le modèle sur tout apprenant inclus dans un package Python dans l’environnement Azure Machine Learning.


    Voici un exemple de code de classifieur Naive Bayes à deux classes utilisant le package populaire *sklearn*.

```Python

# The script MUST define a class named AzureMLModel.
# This class MUST at least define the following three methods:
    # __init__: in which self.model must be assigned,
    # train: which trains self.model, the two input arguments must be pandas DataFrame,
    # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
# The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


import pandas as pd
from sklearn.naive_bayes import GaussianNB


class AzureMLModel:
    def __init__(self):
        self.model = GaussianNB()
        self.feature_column_names = list()

    def train(self, df_train, df_label):
        self.feature_column_names = df_train.columns.tolist()
        self.model.fit(df_train, df_label)

    def predict(self, df):
        return pd.DataFrame(
            {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
             'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
        )


```


2. Connecter le module **Create Python Model** que vous venez de créer à un module **Train Model** et **Score Model**

3. Si vous devez évaluer le modèle, ajoutez un module [Execute Python Script](execute-python-script.md) et modifiez le script Python pour implémenter l’évaluation.

Voici un exemple de code d’évaluation.

```Python


# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to 
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):
    
    from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
    import pandas as pd
    import numpy as np
    
    scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
    ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
    ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
    probabilities = scores["probabilities"]
    
    accuracy, precision, recall, auc = \
    accuracy_score(ytrue, ypred),\
    precision_score(ytrue, ypred),\
    recall_score(ytrue, ypred),\
    roc_auc_score(ytrue, probabilities)
    
    metrics = pd.DataFrame();
    metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
    metrics["Value"] = [accuracy, precision, recall, auc]
    
    return metrics,

```
