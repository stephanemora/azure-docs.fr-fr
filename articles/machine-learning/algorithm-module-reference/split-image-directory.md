---
title: Diviser un répertoire d’images
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Score Image Model dans Azure Machine Learning pour générer des prédictions à l’aide d’un modèle d’image entraîné.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 9673b3260425fd9244c635beaf77d367a14cac54
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84809916"
---
# <a name="split-image-directory"></a>Diviser un répertoire d’images

Cette rubrique explique comment utiliser le module Diviser un répertoire d’images dans Azure Machine Learning Designer (préversion) pour diviser les images d’un répertoire d’images en deux jeux distincts.

Ce module est particulièrement utile quand vous devez séparer des données d’image en jeux de formation et de test. 

## <a name="how-to-configure-split-image-directory"></a>Comment configurer le module Diviser un répertoire d’images

1. Ajoutez le module **Diviser un répertoire d’images** à votre pipeline. Vous trouverez ce module dans la catégorie 'Computer Vision/Image Data Transformation' (Vision par ordinateur/Transformation de données d’image).

2. Connectez-le au module dont la sortie est le répertoire d’images.

3. Entrez **Fraction d’images dans la première sortie** pour spécifier le pourcentage de données à placer dans le fractionnement de gauche, par défaut 0.9.


## <a name="technical-notes"></a>Notes techniques

### <a name="expected-inputs"></a>Entrées attendues

| Nom                  | Type           | Description              |
| --------------------- | -------------- | ------------------------ |
| Répertoire d’images d’entrée | ImageDirectory | Répertoire d’images à fractionner |

### <a name="module-parameters"></a>Paramètres du module

| Nom                                   | Type  | Plage | Facultatif | Description                            | Default |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| Fraction d’images dans la première sortie | Float | 0-1   | Obligatoire | Fraction d’images dans la première sortie | 0.9     |

### <a name="outputs"></a>Sorties

| Nom                    | Type           | Description                              |
| ----------------------- | -------------- | ---------------------------------------- |
| Répertoire d’images de sortie 1 | ImageDirectory | Répertoire d’images qui contient les images sélectionnées |
| Répertoire d’images de sortie 2 | ImageDirectory | Répertoire d’images qui contient toutes les autres images |

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 

