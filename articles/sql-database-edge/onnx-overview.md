---
title: Machine Learning et IA avec ONNX dans Azure SQL Database Edge en préversion | Microsoft Docs
description: Dans Azure SQL Database Edge en préversion, le Machine Learning prend en charge les modèles au format ONNX (Open Neural Network Exchange). ONNX est un format ouvert que vous pouvez utiliser pour échanger des modèles entre plusieurs infrastructures et outils de Machine Learning.
keywords: déployer sql database edge
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/07/2019
ms.openlocfilehash: 976c849f9cb48e1c197f70d10e911216a6a7425c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822847"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Machine Learning et IA avec ONNX dans SQL Database Edge en préversion

Dans Azure SQL Database Edge en préversion, le Machine Learning prend en charge les modèles au format [ONNX (Open Neural Network Exchange)](https://onnx.ai/). ONNX est un format ouvert que vous pouvez utiliser pour échanger des modèles entre plusieurs [infrastructures et outils de Machine Learning](https://onnx.ai/supported-tools).

## <a name="overview"></a>Vue d'ensemble

Pour inférer des modèles de machine learning dans Azure SQL Database Edge, vous devez d’abord obtenir un modèle. Il peut s’agir d’un modèle prééntraîné ou d’un modèle personnalisé entraîné avec le framework de votre choix. Azure SQL Database Edge prend en charge le format ONNX et vous devrez convertir le modèle dans ce format. Il ne doit normalement y avoir aucun impact sur la justesse du modèle et, une fois que vous disposez du modèle ONNX, vous pouvez le déployer dans Azure SQL Database Edge et utiliser le [scoring natif avec la fonction T-SQL PREDICT](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>Obtenir des modèles ONNX

Vous pouvez obtenir un modèle au format ONNX de plusieurs façons :

- [ONNX Model Zoo](https://github.com/onnx/models) : Contient de nombreux modèles ONNX préentraînés pour différents types de tâches qui peuvent être téléchargés et sont prêts à être utilisés.

- [Exportation native depuis les frameworks d’entraînement ML](https://onnx.ai/supported-tools) : Plusieurs frameworks d’entraînement prennent en charge la fonctionnalité d’exportation native vers ONNX, ce qui vous permet d’enregistrer votre modèle entraîné dans une version spécifique du format ONNX, notamment [PyTorch](https://pytorch.org/docs/stable/onnx.html), Chainer et Caffe2. De plus, les services de création de modèles, comme la [fonctionnalité Machine Learning automatisé dans Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) et le [service Vision personnalisée Azure](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier), permettent l’exportation ONNX.

- [Convertir des modèles existants](https://github.com/onnx/tutorials#converting-to-onnx-format) : Pour les frameworks qui ne prennent pas en charge l’exportation native, il existe des packages autonomes pour la conversion des modèles au format ONNX. Pour obtenir des exemples et des tutoriels, consultez [Conversion au format ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format). 

### <a name="supported-frameworks"></a>Frameworks pris en charge

Les convertisseurs ONNX vous permettent de convertir des modèles entraînés de différents frameworks de machine learning au format ONNX. Les convertisseurs les plus répandus sont les suivants : 

* [PyTorch](http://pytorch.org/docs/master/onnx.html)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [Scikit-learn](https://github.com/onnx/sklearn-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)

Pour obtenir la liste complète des frameworks pris en charge, consultez [Conversion au format ONNX](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Limites

Actuellement, tous les modèles ONNX ne sont pas pris en charge par Azure SQL Database Edge. La prise en charge est limitée aux modèles avec des **types de données numériques** :

- [int et bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [real et float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql)
  
Les autres types numériques peuvent être convertis vers des types pris en charge avec [CAST et CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Les entrées de modèle doivent être structurées afin que chaque entrée du modèle corresponde à une colonne unique dans une table. Par exemple, si vous utilisez un dataframe pandas pour former un modèle, chaque entrée doit correspondre à une colonne distincte du modèle.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer SQL Database Edge via le portail Azure](deploy-portal.md)
- [Déployer un modèle ONNX dans Azure SQL Database Edge en préversion](deploy-onnx.md)
