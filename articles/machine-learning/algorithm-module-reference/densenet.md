---
title: DenseNet
titleSuffix: Azure Machine Learning
description: Découvrez comment créer un modèle de classification d’images avec l’algorithme DenseNet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 2351012738f4cf5697fb29891c9459e4cc86cd3a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536628"
---
# <a name="densenet"></a>DenseNet

Cet article explique comment utiliser le module **DenseNet** dans le concepteur Azure Machine Learning pour créer un modèle de classification d’images à l’aide de l’algorithme Densenet.  

Cet algorithme de classification est une méthode d’apprentissage supervisé qui nécessite un répertoire d’images étiqueté. 

> [!NOTE]
> Ce module ne prend pas en charge le jeu de données étiqueté généré à partir d’*Étiquetage des données* dans le studio ; il ne prend en charge que le répertoire d’images étiqueté généré à partir du module [Convertir en répertoire d’images](convert-to-image-directory.md). 

Pour entraîner le modèle, indiquez ce dernier ainsi que le répertoire d’images étiquetées en tant qu’entrées du module [Train Pytorch Model](train-pytorch-model.md). Vous pouvez ensuite utiliser le modèle entraîné pour prédire les valeurs des nouveaux exemples d’entrée à l’aide du module [Score Image Model](score-image-model.md).

### <a name="more-about-densenet"></a>En savoir plus sur DenseNet

Pour plus d’informations sur DenseNet, consultez le document de recherche [Densely Connected Convolutional Networks](https://arxiv.org/abs/1608.06993).

## <a name="how-to-configure-densenet"></a>Guide pratique pour configurer DenseNet

1.  Ajoutez le module **DenseNet** à votre pipeline dans le concepteur.  

2.  Pour **Model name** (Nom du modèle), indiquez le nom d’une structure DenseNet spécifique que vous pouvez sélectionner parmi les structures DenseNet prises en charge : « densenet121 », « densenet161 », « densenet169 » et « densenet201 ».

3.  Pour **Pretrained** (Préentraîné), indiquez si vous souhaitez utiliser un modèle préentraîné sur ImageNet. Si cette option est sélectionnée, vous pouvez affiner le modèle en fonction du modèle préentraîné sélectionné. Si cette option est désélectionnée, vous pouvez commencer l’entraînement à partir de zéro.

4.  Pour **Memory efficient** (Efficace en mémoire), indiquez si vous souhaitez utiliser les points de contrôle, qui sont beaucoup plus efficaces en mémoire mais également plus lents. Pour plus d’informations, consultez le document de recherche [Memory-Efficient Implementation of DenseNets](https://arxiv.org/pdf/1707.06990.pdf).

5.  Connectez la sortie du module **DenseNet** et du module de jeu de données d’images d’entraînement et de validation au module [Train Pytorch Model](train-pytorch-model.md). 

6. Envoyez le pipeline.


## <a name="results"></a>Résultats

Une fois l’exécution du pipeline effectuée, si vous souhaitez utiliser le modèle à des fins de scoring, connectez le module [Train Pytorch Model](train-pytorch-model.md) (Entraîner un modèle PyTorch) au module [Score Image Model](score-image-model.md) (Scorer un modèle d’image) pour prédire les valeurs des nouveaux exemples d’entrée.

## <a name="technical-notes"></a>Notes techniques  

###  <a name="module-parameters"></a>Paramètres du module  

| Nom             | Plage | Type    | Default     | Description                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| Nom du modèle       | Quelconque   | Mode    | densenet201 | Nom d’une structure DenseNet spécifique     |
| Pretrained (Préentraîné)       | Quelconque   | Boolean | True        | Spécifie si un modèle préentraîné doit être utilisé ou non sur ImageNet |
| Memory efficient (Efficace en mémoire) | Quelconque   | Boolean | False       | Spécifie si les points de contrôle doivent être utilisés ou non, car ils sont beaucoup plus efficaces en mémoire mais également plus lents |

###  <a name="output"></a>Output  

| Nom            | Type                    | Description                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Untrained model (Modèle non entraîné) | UntrainedModelDirectory | Modèle DenseNet non entraîné qui peut être connecté au module Train Pytorch Model. |

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 
