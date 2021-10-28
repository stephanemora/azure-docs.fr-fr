---
title: Utiliser AutoML pour détecter les petits objets dans les images
description: Configurez le Machine Learning automatisé Azure Machine Learning pour entraîner des modèles de détection des petits objets.
author: PhaniShekhar
ms.author: phmantri
ms.service: machine-learning
ms.subservice: automl
ms.topic: how-to
ms.date: 10/13/2021
ms.openlocfilehash: 7800399be259dddf2782c47696ce014442d8bf55
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234450"
---
# <a name="train-a-small-object-detection-model-with-automl-preview"></a>Entraîner un modèle de détection de petits objets avec AutoML (préversion)

> [!IMPORTANT]
> Cette fonctionnalité est actuellement disponible en préversion publique. Cette préversion est fournie sans contrat de niveau de service. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dans cet article, vous allez apprendre à entraîner un modèle de détection d’objet pour détecter des petits objets dans des images haute résolution avec le [Machine Learning automatisé](concept-automated-ml.md) dans Azure Machine Learning.

En règle générale, les modèles de vision par ordinateur pour la détection d’objet fonctionnent bien pour les jeux de données avec des objets relativement volumineux. Toutefois, en raison des contraintes de mémoire et de calcul, ces modèles ont tendance à être peu performants lorsqu’ils sont chargés de détecter de petits objets dans des images haute résolution. Étant donné que les images haute résolution sont généralement volumineuses, elles sont redimensionnées avant leur entrée dans le modèle, ce qui limite leur capacité à détecter des objets plus petits, par rapport à la taille initiale de l’image.

Pour résoudre ce problème, le Machine Learning automatisé prend en charge la mise en mosaïque dans le cadre des fonctionnalités de vision par ordinateur en préversion publique. La fonctionnalité de mise en mosaïque dans le Machine Learning automatisé est basée sur les concepts présentés dans [The Power of Tiling for Small Object Detection](https://openaccess.thecvf.com/content_CVPRW_2019/papers/UAVision/Unel_The_Power_of_Tiling_for_Small_Object_Detection_CVPRW_2019_paper.pdf) (Puissance de la mise en mosaïque pour la détection de petits objets).

Lors de la mise en mosaïque, chaque image est divisée en une grille de vignettes. Les vignettes adjacentes se chevauchent dans la largeur et dans la hauteur. Les vignettes sont rognées à partir de l’original comme indiqué dans l’image suivante. 

![Génération de vignettes](./media/how-to-use-automl-small-object-detect/tiles-generation.png)

## <a name="prerequisites"></a>Prérequis

* Un espace de travail Azure Machine Learning. Pour créer l’espace de travail, voir [Créer un espace de travail Azure Machine Learning](how-to-manage-workspace.md).

* Cet article suppose que vous disposez de certaines connaissances sur la façon de configurer une [expérience de Machine Learning automatisé pour les tâches de vision par ordinateur](how-to-auto-train-image-models.md). 

## <a name="supported-models"></a>Modèles pris en charge

La détection de petits objets à l’aide de la mise en mosaïque est actuellement prise en charge pour les modèles suivants :

* fasterrcnn_resnet18_fpn
* fasterrcnn_resnet50_fpn
* fasterrcnn_resnet34_fpn
* fasterrcnn_resnet101_fpn
* fasterrcnn_resnet152_fpn
* retinanet_resnet50_fpn

## <a name="enable-tiling-during-training"></a>Activer la mise en mosaïque pendant la formation

Pour activer la mise en mosaïque, vous pouvez définir le paramètre `tile_grid_size` sur une valeur telle que (3, 2), où 3 est le nombre de vignettes le long de la dimension de largeur et 2 le nombre de vignettes le long de la dimension de hauteur. Quand ce paramètre est défini sur (3, 2), chaque image est divisée en une grille de 3 x 2 vignettes. Chaque vignette chevauche les vignettes adjacentes, afin que tous les objets qui se trouvent sur la bordure de la vignette soient entièrement inclus dans l’une des vignettes. Ce chevauchement peut être contrôlé par le paramètre `tile_overlap_ratio`, dont la valeur par défaut est 25 %.

Lorsque la mise en mosaïque est activée, l’image entière et les vignettes générées à partir de celle-ci sont transmises par le biais du modèle. Ces images et vignettes sont redimensionnées en fonction des paramètres `min_size` et `max_size` avant d’être envoyées au modèle. Le temps de calcul augmente proportionnellement en raison du traitement de ces données supplémentaires. 

Par exemple, lorsque le paramètre `tile_grid_size` a la valeur (3, 2), le temps de calcul est environ sept fois supérieur au temps de calcul en l’absence de mosaïque.

Vous pouvez spécifier la valeur de `tile_grid_size` dans votre espace des hyperparamètres en tant que chaîne.

```python
parameter_space = {
    'model_name': choice('fasterrcnn_resnet50_fpn'),
    'tile_grid_size': choice('(3, 2)'),
    ...
}
```

La valeur du paramètre `tile_grid_size` dépend des dimensions de l’image et de la taille des objets dans l’image. Par exemple, un plus grand nombre de vignettes est utile quand il y a des objets plus petits dans les images.

Afin de choisir la valeur optimale pour ce paramètre dans votre jeu de données, vous pouvez utiliser la recherche des hyperparamètres. Pour ce faire, vous pouvez spécifier un choix de valeurs pour ce paramètre dans votre espace des hyperparamètres.

```python
parameter_space = {
    'model_name': choice('fasterrcnn_resnet50_fpn'),
    'tile_grid_size': choice('(2, 1)', '(3, 2)', '(5, 3)'),
    ...
}
```
## <a name="tiling-during-inference"></a>Mise en mosaïque pendant l’inférence

Lorsqu’un modèle entraîné avec la mise en mosaïque est déployé, la mise en mosaïque se produit également pendant l’inférence. Le Machine Learning automatisé utilise la valeur `tile_grid_size` de la formation pour générer les vignettes pendant l’inférence. L’image entière et les vignettes correspondantes sont transmises par le biais du modèle, et les propositions d’objets qui en découlent sont fusionnées dans les prédictions finales de sortie, comme dans l’image suivante.

![Fusion des propositions d’objets](./media/how-to-use-automl-small-object-detect/tiles-merge.png)

> [!NOTE] 
> Il est possible que le même objet soit détecté à partir de plusieurs vignettes. La détection de la duplication est effectuée pour supprimer ces doublons.
>
> La détection des doublons est effectuée en exécutant NMS sur les propositions issues des vignettes et de l’image. Lorsque plusieurs propositions se chevauchent, celle avec le score le plus élevé est choisie et les autres sont ignorées en tant que doublons. Deux propositions sont considérées comme se chevauchant lorsque l’intersection sur l’union (iou) entre elles est supérieure au paramètre `tile_predictions_nms_thresh`.

Vous avez également la possibilité d’activer la mise en mosaïque uniquement pendant l’inférence sans l’activer pendant la formation. Pour ce faire, définissez le paramètre `tile_grid_size` uniquement pendant l’inférence, et non pour la formation. 

Cela peut améliorer les performances de certains jeux de données et n’entraînera pas le coût supplémentaire lié à la mise en mosaïque pendant la formation. 

## <a name="tiling-hyperparameters"></a>Hyperparamètres de la mise en mosaïque 

Voici les paramètres que vous pouvez utiliser pour contrôler la fonctionnalité de mise en mosaïque.

| Nom du paramètre    | Description   | Default |
| --------------- |-------------| -------|
| `tile_grid_size` |  Taille de la grille à utiliser pour la mise en mosaïque de chaque image. Disponible pour une utilisation au cours de la formation, de la validation et de l’inférence.<br><br>Tuple de deux entiers passés sous forme de chaîne, par exemple `'(3, 2)'`<br><br> *Remarque : la définition de ce paramètre augmente le temps de calcul proportionnellement, puisque toutes les vignettes et les images sont traitées par le modèle.*| pas de valeur par défaut |
| `tile_overlap_ratio` | Contrôle le ratio de chevauchement entre les vignettes adjacentes dans chaque dimension. Lorsque les objets qui se trouvent sur la bordure de la vignette sont trop grands pour tenir entièrement dans l’une des vignettes, augmentez la valeur de ce paramètre pour que les objets tiennent au moins dans l’une des vignettes.<br> <br>  Doit être une valeur flottante dans [0, 1).| 0,25 |
| `tile_predictions_nms_thresh` | Intersection sur le seuil d’union à utiliser pour effectuer une suppression non maximale (nms) lors de la fusion des prédictions à partir des vignettes et de l’image. Disponible pendant la validation et l’inférence. Modifiez ce paramètre s’il existe plusieurs zones détectées par objet dans les prédictions finales.  <br><br> Doit être une valeur flottante dans [0, 1]. | 0,25 |


## <a name="example-notebooks"></a>Exemples de notebooks

Pour obtenir des exemples de code détaillés de configuration et de formation d’un modèle de détection d’objets, consultez [l’exemple de notebook de détection d’objet](https://github.com/Azure/azureml-examples/tree/main/python-sdk/tutorials/automl-with-azureml/image-object-detection/auto-ml-image-object-detection.ipynb).

>[!NOTE]
> Toutes les images de cet article sont mises à disposition conformément à la section d’utilisation autorisée du [contrat de licence MIT](https://choosealicense.com/licenses/mit/).  
> Copyright © 2020 Roboflow, Inc.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez plus d’informations sur [comment et où déployer un modèle](how-to-deploy-and-where.md).
* Pour obtenir des définitions et des exemples des graphiques et métriques de performances fournis pour chaque exécution, consultez [Évaluer les résultats de l’expérience de Machine Learning automatisé](how-to-understand-automated-ml.md). 
