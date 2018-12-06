---
title: Préparer des données avec le SDK de préparation des données Azure Machine Learning pour Python - Azure
description: Découvrez comment utiliser le SDK de préparation des données Azure Machine Learning pour Python dans le but de charger des données de différents formats, les transformer pour qu’elles soient davantage utilisables et les écrire dans un emplacement auquel peuvent accéder vos modèles.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 11/27/2018
ms.openlocfilehash: 91d0f3565db484504a67a3b6ae0989b9291cd24f
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446422"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Préparer des données pour la modélisation avec Azure Machine Learning

Dans cet article, vous découvrez les cas d’usage et les fonctionnalités uniques du kit de développement logiciel (SDK) de préparation des données Azure Machine Learning. La préparation des données est la part la plus importante du flux de travail Machine Learning. Sans une transformation et un nettoyage significatifs, les données réelles sont souvent rompues, incohérentes ou inutilisables comme données de formation. La correction des erreurs et anomalies dans les données brutes et la création de nouvelles fonctionnalités en fonction du problème que vous essayez de résoudre augmentera la précision du modèle. Le kit de développement logiciel (SDK) est conçu pour être familier aux utilisateurs d’autres bibliothèques de préparation des données courantes, tout en offrant des avantages pour les scénarios clés et en maintenant l’interopérabilité avec ces autres bibliothèques.

Vous pouvez préparer vos données dans Python avec le [SDK de préparation des données d’Azure Machine Learning](https://aka.ms/data-prep-sdk).

## <a name="azure-machine-learning-data-prep-sdk"></a>Kit de développement logiciel (SDK) de préparation de données Azure Machine Learning

Le [kit de développement logiciel (SDK) de préparation des données Azure Machine Learning](https://aka.ms/data-prep-sdk) est une bibliothèque Python qui propose :

* des transformations intelligentes rapides telles que le regroupement probable, la colonne dérivée par exemple, le fractionnement automatique, le fichier de lecture intelligent et le traitement des schémas Ragged-right ;
* une seule API qui fonctionne localement sur les données de petite taille ou dans le cloud pour les données volumineuses, avec **peu ou pas de modifications du code** ;
* la possibilité de mettre à l’échelle plus efficacement sur une seule machine en utilisant une approche de diffusion en continu pour le traitement des données, plutôt que le chargement en mémoire.

### <a name="install-the-sdk"></a>Installer le Kit de développement logiciel (SDK)

Installez le kit de développement logiciel (SDK) dans votre environnement Python à l’aide de la commande suivante.

```shell
pip install azureml-dataprep
```

Pour importer le package Go, utilisez le code suivant.

```python
import azureml.dataprep as dprep
```

### <a name="examples-and-reference"></a>Exemples et référence

Pour en savoir plus sur les modules et les fonctions de ce kit de développement logiciel (SDK), consultez les [documents de référence du kit de développement logiciel (SDK) de préparation des données](https://aka.ms/data-prep-sdk).

Les exemples suivants mettent en évidence certaines des fonctionnalités uniques du kit de développement logiciel (SDK),notamment :

* La détection automatique des types de fichiers
* Les transformations intelligentes
* Le résumé des statistiques
* Les fonctionnalités d’environnements multiples


#### <a name="automatic-file-type-detection"></a>La détection automatique des types de fichiers

Utilisez la fonction `smart_read_file()` pour charger vos données sans avoir à spécifier le type de fichier. Cette fonction reconnaît et analyse automatiquement le type de fichier.

```python
dataflow = dprep.smart_read_file(path="<your-file-path>")
```

#### <a name="intelligent-transforms"></a>Transformations intelligentes

Utilisez le kit de développement logiciel (SDK) pour fractionner et dériver des colonnes par exemple et par inférence afin automatiser la conception des fonctionnalités. Supposons que vous avez dans votre objet de flux de données un champ appelé `datetime` avec la valeur `2018-09-15 14:30:00`.

Pour fractionner automatiquement le champ `datetime`, appelez la fonction suivante.

```python
new_dataflow = dataflow.split_column_by_example(source_column="datetime")
```

Si vous ne définissez pas le paramètre d’exemple, la fonction fractionnera automatiquement le champ `datetime` en deux nouveaux champs `datetime_1` et `datetime_2`. Les valeurs obtenues sont respectivement `2018-09-15` et `14:30:00`. Il est également possible de fournir un exemple de modèle, et le kit de développement logiciel (SDK) prédira et exécutera la transformation souhaitée. Avec le même objet `datetime`, le code suivant créera une nouvelle colonne `datetime_weekday` pour le jour de la semaine en fonction de l’exemple fourni.

```python
new_dataflow = dataflow.derive_column_by_example(
        source_columns="datetime", 
        new_column_name="datetime_weekday", 
        example_data=[("2009-01-04 10:12:00", "Sunday"), ("2013-08-22 17:00:00", "Thursday")]
    )
```

#### <a name="summary-statistics"></a>Résumé des statistiques

Vous pouvez générer rapidement des résumés de statistiques pour un flux de données avec une seule ligne de code. Cette méthode est un moyen pratique de comprendre vos données et la manière dont elles sont distribuées.

```python
dataflow.get_profile()
```

L’appel de cette fonction sur un objet de flux de données entraînera une sortie comme celle du tableau suivant.

![Sortie du résumé des statistiques](./media/concept-data-preparation/output-example.png)

## <a name="multiple-environment-compatibilities"></a>Compatibilités pour des environnements multiples

Le kit de développement logiciel (SDK) permet également de sérialiser des objets de flux de données et de les ouvrir dans *n’importe quel* environnement Python. L’environnement dans lequel ils sont ouverts peut être différent de celui où ils sont enregistrés. Cette fonctionnalité facilite le transfert entre des environnements Python et permet une intégration rapide avec les modèles Azure Machine Learning.

Utilisez le code suivant pour enregistrer vos objets de flux de données.

```python
package = dprep.Package([dataflow_1, dataflow_2])
package.save("<your-local-path>")
```

Utilisez le code suivant pour rouvrir votre package dans n’importe quel environnement et récupérer une liste d’objets de flux de données.

```python
package = dprep.Package.open("<your-local-path>")
dataflow_list = package.dataflows
```

## <a name="data-preparation-pipeline"></a>Pipeline de préparation des données

Pour voir des exemples et un code détaillés pour chaque étape de préparation, utilisez les guides pratiques suivants :

1. [Charger les données](how-to-load-data.md), qui peuvent être dans différents formats
2. [Transformer](how-to-transform-data.md) les données en une structure plus utilisable
3. [Écrire](how-to-write-data.md) ces données à un emplacement accessible à vos modèles

![Processus de préparation des données](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>Étapes suivantes

Consultez un [exemple de bloc-notes](https://github.com/Microsoft/AMLDataPrepDocs/tree/master/tutorials/getting-started/getting-started.ipynb) de préparation des données qui utilise le SDK de préparation des données Azure Machine Learning.

[Documentation de référence](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py) du kit de développement logiciel (SDK) de préparation de données Azure Machine Learning.
