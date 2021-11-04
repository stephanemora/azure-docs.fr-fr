---
title: Images Docker prédéfinies
titleSuffix: Azure Machine Learning
description: Images Docker prédéfinies pour l’inférence (scoring) dans Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 10/21/2021
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt, curated environments
ms.openlocfilehash: 0f7fc034ee6df962ecfe6e00b62b8899e223505c
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563698"
---
# <a name="prebuilt-docker-images-for-inference"></a>Images Docker prédéfinies pour l’inférence

Les images conteneur Docker prédéfinies pour l’inférence sont utilisées durant le déploiement d’un modèle avec Azure Machine Learning.  Les images sont prédéfinies avec des frameworks de machine learning et des packages Python populaires. Vous pouvez également étendre les packages pour ajouter d’autres packages à l’aide de l’une des méthodes suivantes :

* [Ajoutez des Packages Python](how-to-prebuilt-docker-images-inference-python-extensibility.md).
* [Utilisez une image d’inférence prédéfinie comme base d’un nouveau Dockerfile](how-to-extend-prebuilt-docker-image-inference.md). Avec cette méthode, vous pouvez installer des **packages Python et des packages apt**.

## <a name="why-should-i-use-prebuilt-images"></a>Pourquoi utiliser des images prédéfinies ?

* Cela réduit la latence du déploiement de modèle.
* Cela améliore le taux de réussite du déploiement de modèle.
* Cela évite la génération d’image inutile pendant le déploiement du modèle.
* Cela limite les dépendances et droits d’accès à l’image/au conteneur à ce qui est nécessaire. 

## <a name="list-of-prebuilt-docker-images-for-inference"></a>Liste des images Docker prédéfinies pour l’inférence 

[!INCLUDE [list-of-inference-prebuilt-docker-images](../../includes/aml-inference-list-prebuilt-docker-images.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Ajoutez des packages Python aux images prédéfinies](how-to-prebuilt-docker-images-inference-python-extensibility.md).
* [Utilisez un package prédéfini comme base d’un nouveau Dockerfile](how-to-extend-prebuilt-docker-image-inference.md).