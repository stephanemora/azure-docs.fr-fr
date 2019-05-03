---
title: 'Créer un modèle Python : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le modèle de création de modèle Python dans le service Azure Machine Learning pour créer le module de modélisation ou de traitement de données personnalisé.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ea9b50cede3e2d264ca0476b6a987ca6896c3c79
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029744"
---
# <a name="create-python-model"></a>Créer le modèle Python

Cet article décrit comment utiliser le **créer un modèle Python** module permettant de créer un modèle non formé à partir d’un script Python. 

Vous pouvez baser le modèle sur n’importe quel apprenant qui est inclus dans un package de Python dans l’environnement Azure Machine Learning. 

Après avoir créé le modèle, vous pouvez utiliser [former le modèle](train-model.md) pour former le modèle sur un jeu de données, comme n’importe quel autre apprenant dans Azure Machine Learning. Le modèle formé peut être passé à [noter le modèle](score-model.md) pour utiliser le modèle pour élaborer des prédictions. Le modèle formé peut ensuite être enregistré et le workflow de score peut être publié comme un service web.

> [!WARNING]
> Actuellement il n’est pas possible de passer les résultats notés d’un modèle Python pour [évaluer le modèle](evaluate-model.md). Si vous avez besoin évaluer un modèle, vous pouvez écrire Python personnalisé de script et exécutez-le à l’aide de la [exécuter le Script Python](execute-python-script.md) module.  


## <a name="how-to-configure-create-python-model"></a>Comment configurer la création de modèle Python

Utilisation de ce module nécessite des connaissances intermédiaire ou expert de Python. Le module prend en charge l’utilisation de n’importe quel apprenant qui est inclus dans les packages Python déjà installés dans Azure Machine Learning. Consultez la liste des packages Python préinstallé dans [Execute Python Script](execute-python-script.md).
  

Cet article explique comment utiliser le **créer un modèle Python** avec une expérience simple. Voici le graphique de l’expérience.

![create-python-model](./media/module/aml-create-python-model.png)

1.  Cliquez sur **créer un modèle Python**, modifiez le script pour implémenter votre modélisation ou processus de gestion des données. Vous pouvez baser le modèle sur n’importe quel apprenant qui est inclus dans un package de Python dans l’environnement Azure Machine Learning.


    Voici un exemple de code de classifieur Naive Bayes à deux classes à l’aide de la célèbre *sklearn* package.

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


2. Connecter le **créer un modèle Python** module que vous venez de créer pour un **former le modèle** et **noter le modèle**

3. Si vous avez besoin évaluer le modèle, ajoutez un [exécuter le Script Python](execute-python-script.md) et modifiez le script Python pour implémenter l’évaluation.

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
