---
title: Caractérisation dans les expériences AutoML
titleSuffix: Azure Machine Learning
description: Découvrez les paramètres de caractérisation proposés par les offres Azure Machine Learning et la manière dont l’ingénierie de caractéristiques est prise en charge dans les expériences de ML automatisé.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 05/28/2020
ms.custom: seodec18
ms.openlocfilehash: 11bb692027d8a2e5033c7bdaf8eb2c565d1562b0
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86205692"
---
# <a name="featurization-in-automated-machine-learning"></a>Caractérisation dans le Machine Learning automatisé

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans ce guide, vous allez apprendre :

- Quels paramètres de caractérisation sont proposés par le Azure Machine Learning.
- Comment personnaliser ces fonctionnalités pour vos [expériences de machine learning automatisé](concept-automated-ml.md).

*L’ingénierie des caractéristiques* est le processus qui consiste à utiliser la connaissance du domaine des données pour créer des fonctionnalités qui aident les algorithmes de ML à améliorer leur apprentissage. Dans Azure Machine Learning, des techniques de mise à l’échelle et de normalisation des données sont appliquées pour faciliter l’ingénierie de caractéristiques. Généralement, ces techniques et l’ingénierie des caractéristiques sont appelées *caractérisation* dans les expériences de Machine Learning automatisé *(AutoML)* .

Cet article part du principe que vous savez déjà comment configurer une expérience AutoML. Pour plus d'informations sur la configuration, consultez les articles suivants :

- Pour une première expérience basée sur le code : [Configurer des expériences de ML automatisé à l’aide du Kit de développement logiciel (SDK) Azure Machine Learning pour Python](how-to-configure-auto-train.md).
- Pour une expérience avec peu ou pas de code  : [Créer, examiner et déployer des modèles de machine learning automatisé avec Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md).

## <a name="configure-featurization"></a>Configuration de la caractérisation

Dans chaque expérience de Machine Learning automatisée, des [techniques de mise à l’échelle automatique et de normalisation](#featurization) sont appliquées par défaut à vos données. Ces techniques sont des types de caractérisation qui aident *certains* algorithmes sensibles aux caractéristiques à des échelles différentes. Toutefois, vous pouvez également activer des caractérisations supplémentaires, telles que l’*imputation des valeurs manquantes*, *l’encodage* et les *transformations*.

> [!NOTE]
> Les étapes de caractérisation du Machine Learning automatisé (normalisation des fonctionnalités, gestion des données manquantes, conversion de texte en valeurs numériques) font partie du modèle sous-jacent. Lorsque vous utilisez le modèle pour des prédictions, les étapes de caractérisation qui sont appliquées pendant la formation sont appliquées automatiquement à vos données d’entrée.

Pour les expériences configurées avec le Kit de développement logiciel (SDK) Python, vous pouvez activer ou désactiver le paramètre de caractérisation et spécifier les étapes de caractérisation qui doivent être utilisées pour votre expérience. Si vous utilisez le studio Azure Machine Learning, consultez [les étapes pour activer la caractérisation](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

Le tableau suivant présente les paramètres acceptés pour `featurization` dans la [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) :

|Configuration de la caractérisation | Description|
------------- | ------------- |
|`"featurization": 'auto'`| Indique que, dans le cadre du prétraitement, des [étapes de garde-fous des données et de caractérisation](#featurization) doivent être effectuées automatiquement. Il s’agit du paramètre par défaut.|
|`"featurization": 'off'`| Indique que des étapes de caractérisation ne doivent pas être effectuées automatiquement.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Indique que des étapes de caractérisation personnalisées doivent être utilisées. [Découvrez comment personnaliser la caractérisation](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Caractérisation automatique

Le tableau suivant récapitule les techniques appliquées automatiquement à vos données. Ces techniques sont appliquées pour les expériences configurées à l’aide du kit de développement logiciel (SDK) ou du studio. Pour désactiver ce comportement, définissez `"featurization": 'off'` dans votre objet `AutoMLConfig`.

> [!NOTE]
> Si vous envisagez d’exporter vos modèles créés par ML automatisé vers un [modèle ONNX](concept-onnx.md), seules les options de caractérisation marquées d’un astérisque (*) sont prises en charge dans le format ONNX. Apprenez-en davantage sur la [conversion de modèles au format ONNX](concept-automated-ml.md#use-with-onnx).

|Étapes&nbsp;de caractérisation| Description |
| ------------- | ------------- |
|**Supprimer les caractéristiques de cardinalité élevée ou d’absence de variance*** |Supprime ces fonctionnalités des ensembles de formation et de validation. S’applique aux fonctionnalités dont toutes les valeurs sont manquantes, avec la même valeur sur toutes les lignes, ou avec une cardinalité élevée (par exemple, les codes de hachage, les ID, ou les GUID).|
|**Imputer des valeurs manquantes*** |Pour les fonctionnalités numériques, remplacement par la moyenne des valeurs dans la colonne.<br/><br/>Pour les fonctionnalités catégorielles, remplacement par la valeur la plus fréquente.|
|**Générer des caractéristiques supplémentaires*** |Pour les caractéristiques de type date/heure : Année, Mois, Jour, Jour de la semaine, Jour de l’année, Trimestre, Semaine de l’année, Heure, Minute, Seconde.<br/><br/>Pour les caractéristiques de type texte : Fréquence des termes basée sur les unigrammes, les bigrammes et les trigrammes.|
|**Transformer et encoder***|Les fonctionnalités numériques avec très peu de valeurs uniques sont transformées en fonctionnalités catégorielles.<br/><br/>L’encodage one-hot est utilisé pour les fonctionnalités catégoriques de faible cardinalité. L’encodage one-hot-hash est utilisé pour les fonctionnalités catégoriques de haute cardinalité.|
|**Incorporations de mots**|Caractériseur de texte convertissant les vecteurs de jetons de texte en vecteurs de phrase à l’aide d’un modèle déjà formé. Le vecteur d’incorporation de chaque mot d’un document est agrégé avec le reste pour produire un vecteur de fonctionnalité de document.|
|**Encodages cibles**|Pour les fonctionnalités catégorielles, cette étape mappe chaque catégorie avec une valeur cible moyenne pour les problèmes de régression, et à la probabilité de chaque classe pour les problèmes de classification. Une pondération basée sur la fréquence et une validation croisée par échantillons (« k-fold ») sont appliquées pour réduire l’ajustement du mappage et le bruit provoqué par les catégories de données éparses.|
|**Encodage de texte cible**|Pour l'entrée de texte, un modèle linéaire empilé avec « bag-of-words » est utilisé afin de générer la probabilité de chaque classe.|
|**WoE (Poids de la preuve)**|Calcule la valeur WoE en tant que mesure de corrélation des colonnes catégorielles vers la colonne cible. Le WoE est calculé en tant qu'enregistrement du ratio de probabilités à l'intérieur et à l'extérieur de la classe. Cette étape produit une colonne de fonctionnalités numériques par classe et évite d'avoir à imputer les valeurs manquantes et le traitement de valeur hors norme.|
|**Distance de cluster**|Effectue l’apprentissage d’un modèle de clustering k-moyennes sur toutes les colonnes numériques. Génère de nouvelles fonctionnalités *k*, une nouvelle fonctionnalité numérique par cluster, contenant la distance de chaque échantillon par rapport au centroïde de chaque cluster.|

## <a name="data-guardrails"></a>Garde-fous des données

*Les Garde-fous des données* vous aident à identifier les problèmes potentiels liés à vos données (par exemple, valeurs manquantes ou [déséquilibre de classe](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)). Ils vous aident également à prendre des mesures correctives pour améliorer les résultats.

Les Garde-fous des données sont appliqués :

- **Pour les expériences du Kit de développement logiciel (SDK)** : Lorsque les paramètres `"featurization": 'auto'` ou `validation=auto` sont spécifiés dans votre objet `AutoMLConfig`.
- **Pour les expériences de studio**: Quand l’option Caractérisation automatique est activée.

Vous pouvez consulter les Garde-fous des données pour votre expérience :

- En définissant `show_output=True` lorsque vous soumettez une expérience à l’aide du kit de développement logiciel (SDK).

- Dans le studio, sous l’onglet **Garde-fous des données** de votre exécution de ML automatisé.

### <a name="data-guardrail-states"></a>États des Garde-fous des données

Les garde-fous des données affichent l’un des trois états suivants :

|State| Description |
|----|---- |
|**Passed**| Aucun problème de données n’a été détecté et aucune action n’est requise de votre part. |
|**Done**| Des modifications ont été appliquées à vos données. Nous vous encourageons à passer en revue les actions correctives prises par le ML automatisé pour vérifier que les modifications s’alignent sur les résultats attendus. |
|**Alerted**| Un problème de données a été détecté mais n’a pas pu être résolu. Nous vous encourageons à examiner et à résoudre le problème.|

### <a name="supported-data-guardrails"></a>Garde-fous des données pris en charge

Le tableau suivant décrit les garde-fous des données pris en charge ainsi que les états associés que vous pouvez rencontrer lors l’envoi de votre expérience :

Garde-fou|Statut|Condition&nbsp;pour&nbsp;le déclencheur
---|---|---
**Imputation des valeurs de caractéristique manquantes** |Passed <br><br><br> Terminé| Aucune valeur de caractéristique manquante n’a été détectée dans vos données d’entraînement. Découvrez plus d’informations sur l’[imputation d’une valeur manquante](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options). <br><br> Des valeurs de caractéristique manquantes ont été détectées dans vos données de formation et imputées.
**Gestion des caractéristiques à cardinalité élevée** |Passed <br><br><br> Terminé| Aucune caractéristique à cardinalité élevée n’a été détectée après analyse de vos entrées. <br><br> Des caractéristiques à cardinalité élevée ont été détectées dans vos entrées et ont été gérées.
**Gestion du fractionnement de la validation** |Terminé| La configuration de la validation a été définie sur `'auto'` et les données de formation contenaient *moins de 20 000 lignes*. <br> Chaque itération du modèle entraîné a été validée à l’aide de la validation croisée. Découvrez-en plus sur les [données de validation](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data). <br><br> La configuration de la validation a été définie sur `'auto'` et les données de formation contenaient *plus de 20 000 lignes*. <br> Les données d’entrée ont été divisées en un jeu de données d’entraînement et un jeu de données de validation pour la validation du modèle.
**Détection de l’équilibrage des classes** |Passed <br><br><br><br><br> Alerté | Vos entrées ont été analysées et toutes les classes sont équilibrées dans vos données d’entraînement. Un jeu de données est considéré comme équilibré si chaque classe a une bonne représentation dans le jeu de données, telle que mesurée par le nombre et le ratio des échantillons. <br><br><br> Des classes déséquilibrées ont été détectées dans vos entrées. Pour corriger le biais du modèle, résolvez le problème d’équilibrage. Découvrez-en plus sur les [données déséquilibrées](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data).
**Détection des problèmes de mémoire** |Passed <br><br><br><br> Terminé |<br> Aucun problème potentiel d’insuffisance de mémoire n’a été détecté après analyse des valeurs {horizon, décalage, fenêtre dynamique} sélectionnées. Découvrez-en plus sur les [configurations de prévision](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) de série chronologique. <br><br><br>Les valeurs {horizon, décalage, fenêtre dynamique} sélectionnées ont été analysées et peuvent entraîner une insuffisance de mémoire dans votre expérience. Les configurations de la fenêtre dynamique ou de décalage ont été désactivées.
**Détection de la fréquence** |Passed <br><br><br><br> Terminé |<br> La série chronologique a été analysée et tous les points de données sont alignés sur la fréquence détectée. <br> <br> La série chronologique a été analysée et les points de données qui ne sont pas alignés sur la fréquence détectée ont été détectés. Ces points de données ont été supprimés du jeu de données. Découvrez-en plus sur la [préparation des données pour la prévision de série chronologique](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data).

## <a name="customize-featurization"></a>Personnaliser la caractérisation

Vous pouvez personnaliser vos paramètres de caractérisation pour vous assurer que les données et les caractéristiques utilisées pour l’apprentissage de votre modèle ML génèrent des prédictions pertinentes.

Pour personnaliser les caractérisations, spécifiez `"featurization": FeaturizationConfig` dans votre objet `AutoMLConfig`. Si vous utilisez le studio Azure Machine Learning pour votre expérience, consultez le [guide pratique](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

Les personnalisations prises en charge sont notamment les suivantes :

|Personnalisation|Définition|
|--|--|
|**Mise à jour de l’objectif de la colonne**|Remplacer le type de caractéristique pour la colonne spécifiée.|
|**Mise à jour des paramètres du transformateur** |Mettre à jour les paramètres du transformateur spécifié. Prend actuellement en charge *Imputer* (moyen, le plus fréquent et médian) et *HashOneHotEncoder*.|
|**Suppression de colonnes** |Indique les colonnes à supprimer de la caractérisation.|
|**Transformateurs de blocs**| Indique les transformateurs de blocs à utiliser dans le processus de caractérisation.|

Créez l’objet `FeaturizationConfig` à l’aide d’appels d’API :

```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment configurer vos expériences de ML automatisé :

    * Pour une première expérience basée sur le code : [Configurer des expériences de ML automatisé à l’aide du Kit de développement logiciel (SDK) Azure Machine Learning](how-to-configure-auto-train.md).
    * Pour une expérience avec peu ou pas de code  : [Créez vos expériences de Machine Learning automatisé dans le studio Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md).

* Découvrez plus d’informations sur [comment et où déployer un modèle](how-to-deploy-and-where.md).

* Découvrez plus d’informations sur [comment entraîner un modèle de régression à l’aide du machine learning automatisé](tutorial-auto-train-models.md) ou [comment entraîner avec le machine learning automatisé sur une ressource distante](how-to-auto-train-remote.md).
