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
ms.date: 09/24/2018
ms.openlocfilehash: 673b8ddf3ed07507cbce4b54e9b8a488cb3ac689
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166603"
---
# <a name="prepare-data-for-modeling-with-azure-machine-learning"></a>Préparer des données pour la modélisation avec Azure Machine Learning
 
La préparation des données est une part importante du flux de travail Machine Learning. Vos modèles seront plus précis et efficaces s’ils ont accès à des données propres dont le format est facile à utiliser. 

Vous pouvez préparer vos données dans Python avec le [SDK de préparation des données d’Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py). 

## <a name="data-preparation-pipeline"></a>Pipeline de préparation des données

Les étapes principales de préparation des données sont :

1. [Charger les données](how-to-load-data.md), qui peuvent être dans différents formats
2. [Transformer](how-to-transform-data.md) les données en une structure plus utilisable
3. [Écrire](how-to-write-data.md) ces données à un emplacement accessible à vos modèles

![Processus de préparation des données](./media/concept-data-preparation/data-prep-process.png)

## <a name="next-steps"></a>Étapes suivantes
Consultez un [exemple de bloc-notes](https://github.com/Microsoft/PendletonDocs/blob/master/Scenarios/GettingStarted/getting-started.ipynb) de préparation des données qui utilise le SDK de préparation des données Azure Machine Learning.
