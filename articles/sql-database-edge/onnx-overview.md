---
title: Machine Learning et IA avec ONNX dans Azure SQL Database Edge en préversion | Microsoft Docs
description: Dans Azure SQL Database Edge en préversion, le Machine Learning prend en charge les modèles au format ONNX (Open Neural Network Exchange). ONNX est un format ouvert que vous pouvez utiliser pour échanger des modèles entre plusieurs infrastructures et outils de Machine Learning.
keywords: déployer sql database edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/04/2019
ms.openlocfilehash: c4c87f7f6f8735c9a50c61b0e083c77b915e0d98
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510646"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Machine Learning et IA avec ONNX dans SQL Database Edge en préversion

Dans Azure SQL Database Edge en préversion, le Machine Learning prend en charge les modèles au format [ONNX (Open Neural Network Exchange)](https://onnx.ai/). ONNX est un format ouvert que vous pouvez utiliser pour échanger des modèles entre plusieurs [infrastructures et outils de Machine Learning](https://onnx.ai/supported-tools).

## <a name="supported-tool-kits"></a>Kits d’outils pris en charge

ONNXMLTools vous permet de convertir des modèles à partir de différents kits d'outils de Machine Learning vers un modèle ONNX. Actuellement, pour les types de données numériques et entrées à une seule colonne, les kits d’outils suivants sont pris en charge :

* [scikit-learn](https://github.com/onnx/sklearn-onnx)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)
* [Spark ML (expérimental)](https://github.com/onnx/onnxmltools/tree/master/onnxmltools/convert/sparkml)
* [LightGBM](https://github.com/onnx/onnxmltools)
* [libsvm](https://github.com/onnx/onnxmltools)
* [XGBoost](https://github.com/onnx/onnxmltools)

## <a name="get-onnx-models"></a>Obtenir des modèles ONNX

Vous pouvez obtenir un modèle au format ONNX de plusieurs façons :

- [ONNX Model Zoo](https://github.com/onnx/models) : Contient plusieurs modèles ONNX pré-formés pour différents types de tâches. Vous pouvez télécharger et utiliser les versions prises en charge par Windows Machine Learning.

- [Exportation native depuis les infrastructures natives de Machine Learning](https://onnx.ai/supported-tools) : Plusieurs infrastructures de formation prennent en charge la fonctionnalité d’exportation native vers ONNX, ce qui vous permet d’enregistrer votre modèle formé dans une version spécifique au format ONNX. Par exemple, Chainer, Caffee2, et PyTorch. En outre, des services tels que [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning-service/) et [Azure Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) proposent également une exportation ONNX native.

  - Pour savoir comment former et exporter un modèle ONNX dans le cloud à l’aide de Custom Vision, consultez [Tutoriel : Utiliser un modèle ONNX à partir de Custom Vision avec Windows ML (préversion)](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/custom-vision-onnx-windows-ml).

- [Convertir des modèles existants à l’aide de WinMLTools](https://docs.microsoft.com/windows/ai/windows-ml/convert-model-winmltools) : Ce package Python permet de convertir les modèles de plusieurs formats d'infrastructure de formation vers ONNX. Vous pouvez spécifier la version ONNX vers laquelle vous souhaitez convertir votre modèle, en fonction des builds de Windows ciblées par votre application. Si vous n’êtes pas familiarisé avec Python, vous pouvez utiliser le [tableau de bord Windows Machine Learning basé sur l’interface utilisateur](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLDashboard) pour convertir vos modèles.

> [!IMPORTANT]
> Toutes les versions ONNX ne sont pas prises en charge par Azure SQL Database Edge. Seul le fait de prédire des types de données numériques via le modèle ONNX est actuellement pris en charge.

Une fois que vous disposez d’un modèle ONNX, vous pouvez le déployer dans Azure SQL Database Edge. Vous pouvez ensuite utiliser le [scoring natif avec la fonction PREDICT T-SQL](/sql/advanced-analytics/sql-native-scoring/).

## <a name="limitations"></a>Limites

Actuellement, cette prise en charge est limitée aux modèles avec **types de données numériques** :

- [int et bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [real et float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql)
  
D’autres types numériques peuvent être convertis vers des types pris en charge à l’aide de CAST et CONVERT [CAST et CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql).

Les entrées de modèle doivent être structurées afin que chaque entrée du modèle corresponde à une colonne unique dans une table. Par exemple, si vous utilisez un dataframe pandas pour former un modèle, chaque entrée doit correspondre à une colonne distincte du modèle.

## <a name="next-steps"></a>Étapes suivantes

- [Déployer SQL Database Edge via le portail Azure](deploy-portal.md)
- [Déployer un modèle ONNX dans Azure SQL Database Edge en préversion](deploy-onnx.md)