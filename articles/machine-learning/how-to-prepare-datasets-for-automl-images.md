---
title: Préparer les données pour les tâches de vision par ordinateur
titleSuffix: Azure Machine Learning
description: Préparation des données d’image pour ML automatisé Azure Machine Learning en vue de l’apprentissage de modèles de vision par ordinateur sur la classification, la détection d’objets et la segmentation
author: vadthyavath
ms.author: rvadthyavath
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: template-how-to
ms.date: 10/13/2021
ms.openlocfilehash: 7f2f3cf3c0af623c6c33e46fe9735a2af03c14c6
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007365"
---
# <a name="prepare-data-for-computer-vision-tasks-with-automated-machine-learning-preview"></a>Préparer les données pour les tâches de vision par ordinateur avec Machine Learning automatisé (préversion)

> [!IMPORTANT]
> La prise en charge de l’apprentissage des modèles de vision par ordinateur avec ML automatisé dans Azure Machine Learning est une fonctionnalité en préversion publique expérimentale. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dans cet article, vous allez apprendre à préparer les données d’image pour l’apprentissage des modèles de vision par ordinateur avec des [Machine Learning automatisé dans Azure Machine Learning](concept-automated-ml.md). 

Pour générer des modèles destinés à des tâches de vision par ordinateur avec Machine Learning automatisé, vous devez apporter les données d’image étiquetées comme entrées pour l’apprentissage de modèles sous la forme d’un [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) Azure Machine Learning. 

Pour vous assurer que votre TabularDataset contient le schéma accepté pour la consommation dans le ML automatisé, vous pouvez utiliser l’outil d’étiquetage des données Azure Machine Learning ou un script de conversion. 

## <a name="prerequisites"></a>Prérequis

* Familiarisez-vous avec les [schémas acceptés pour les fichiers JSONL pour les expériences de vision d’ordinateur AutoML](reference-automl-images-schema.md).

* Les données étiquetées que vous souhaitez utiliser pour l’apprentissage de modèles de vision par ordinateur avec ML automatisé.

## <a name="azure-machine-learning-data-labeling"></a>Étiquetage des données Azure Machine Learning

Si vous n’avez pas de données étiquetées, vous pouvez utiliser l'[outil d’étiquetage des données](how-to-create-image-labeling-projects.md) d’Azure Machine Learning pour étiqueter manuellement les images. Cet outil génère automatiquement les données requises pour l’apprentissage au format accepté.

Il permet de créer, gérer et surveiller les tâches d’étiquetage des données 

+ Classification d’images (multi-classe et multi-étiquette)
+ Détection d’objets (cadre englobant)
+ Segmentation d'instance (polygone)

Si vous disposez déjà d’un projet d’étiquetage des données et que vous souhaitez utiliser ces données, vous pouvez [exporter vos données étiquetées comme Azure Machine Learning TabularDataset](how-to-create-image-labeling-projects.md#export-the-labels), qui peut ensuite être utilisé directement avec la ML automatisée pour la formation des modèles de vision par ordinateur.

## <a name="use-conversion-scripts"></a>Utiliser des scripts de conversion

Si vous avez étiqueté des données dans des formats de données de vision par ordinateur populaires, par exemple COV ou COCO, nous fournissons des scripts permettant de générer des fichiers JSONL pour les données d’apprentissage et de validation. Pour obtenir des instructions et des scripts détaillés, reportez-vous à nos [notebooks](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml).

Si vos données ne suivent pas l’un des formats mentionnés précédemment, vous pouvez utiliser votre propre script pour générer des fichiers de lignes JSON basés sur les schémas définis dans [Schéma pour les fichiers JSONL pour les expériences d’image AutoML](reference-automl-images-schema.md).

Une fois vos fichiers de données convertis au format JSONL accepté, vous pouvez les charger dans votre compte de stockage sur Azure. 

## <a name="upload-the-jsonl-file-and-images-to-storage"></a>Charger le fichier JSONL et les images vers le stockage

Pour utiliser les données pour l’apprentissage ML automatisé, chargez les données dans votre [espace de travail Azure Machine Learning](concept-workspace.md) via un [magasin de données](how-to-access-data.md). Le magasin de données fournit un mécanisme vous permettant de charger/télécharger des données dans le stockage sur Azure, et d’interagir avec elles à partir de vos cibles de calcul distantes.

Chargez le répertoire parent entier constitué d’images et de fichiers JSONL dans le magasin de données par défaut automatiquement généré lors de la création de l’espace de travail.  Ce magasin de banques se connecte au conteneur de stockage d’objets blob Azure par défaut généré lors de la création de l’espace de travail.

```python
# Retrieve default datastore that's automatically created when we setup a workspace
ds = ws.get_default_datastore()
ds.upload(src_dir='./fridgeObjects', target_path='fridgeObjects')
```
Une fois le chargement des données terminé, vous pouvez créer une [Azure Machine Learning TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) et l’enregistrer dans votre espace de travail pour une utilisation ultérieure en tant qu’entrée de vos expériences ML automatisées pour les modèles de vision par ordinateur.

```python
from azureml.core import Dataset
from azureml.data import DataType

training_dataset_name = 'fridgeObjectsTrainingDataset'
# create training dataset
training_dataset = Dataset.Tabular.from_json_lines_files(path=ds.path("fridgeObjects/train_annotations.jsonl"),
                                                         set_column_types={"image_url": DataType.to_stream(ds.workspace)}
                                                        )
training_dataset = training_dataset.register( workspace=ws,name=training_dataset_name)

print("Training dataset name: " + training_dataset.name)
```

## <a name="next-steps"></a>Étapes suivantes

* [Former des modèles de vision par ordinateur avec Machine Learning automatisé](how-to-auto-train-image-models.md).
* [Entraîner un modèle de détection des petits objets avec Machine Learning automatisé](how-to-use-automl-small-object-detect.md). 