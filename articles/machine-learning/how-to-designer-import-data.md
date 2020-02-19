---
title: Importer des données
titleSuffix: Azure Machine Learning
description: Découvrez comment importer vos données dans le concepteur Azure Machine Learning depuis différentes sources de données.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 01/16/2020
ms.openlocfilehash: d41b0499df1a62440e8c1991e29f7963224a50d6
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138165"
---
# <a name="import-your-data-into-azure-machine-learning-designer-preview"></a>Importer vos données dans le concepteur Azure Machine Learning (préversion)

Dans cet article, vous allez apprendre à importer vos propres données dans le concepteur afin de créer des solutions personnalisées. Vous pouvez importer des données dans le concepteur de deux manières : 

* **Jeux de données Azure Machine Learning** - Inscrivez des [jeux de données](concept-data.md#datasets) dans Azure Machine Learning pour activer les fonctionnalités avancées qui vous aident à gérer vos données.
* **Module Importer des données** - Utilisez le module [Importer des données](algorithm-module-reference/import-data.md) pour accéder directement aux données à partir de sources de données en ligne.

## <a name="use-azure-machine-learning-datasets"></a>Utiliser des jeux de données Azure Machine Learning

Nous vous recommandons d’utiliser des [jeux de données](concept-data.md#datasets) pour importer des données dans le concepteur. En inscrivant un jeu de données, vous pouvez tirer pleinement parti des fonctionnalités de données avancées, telles que le [contrôle de version](how-to-version-track-datasets.md) et la [supervision des données](how-to-monitor-datasets.md).

### <a name="register-a-dataset"></a>Inscrire un jeu de données

Vous pouvez inscrire des jeux de données existants [par programmation avec le kit de développement logiciel (SDK)](how-to-create-register-datasets.md#use-the-sdk) ou [visuellement dans Azure Machine Learning Studio](how-to-create-register-datasets.md#use-the-ui).

Vous pouvez également inscrire la sortie de n’importe quel module du concepteur sous forme de jeu de données.

1. Sélectionnez le module qui génère les données que vous souhaitez inscrire.

1. Dans le volet de propriétés, sélectionnez **Sorties** > **Inscrire le jeu de données**.

    ![Capture d’écran montrant comment accéder à l'option Inscrire le jeu de données](media/how-to-designer-import-data/register-dataset-designer.png)

### <a name="use-a-dataset"></a>Utiliser un jeu de données

Vos jeux de données inscrits se trouvent dans la palette de modules, sous **Jeux de données** > **Mes jeux de données**. Pour utiliser un jeu de données, faites-le glisser et déposez-le sur le canevas du pipeline. Ensuite, connectez le port de sortie du jeu de données à d’autres modules de la palette.

![Capture d’écran montrant l’emplacement des jeux de données enregistrés dans la palette du concepteur](media/how-to-designer-import-data/use-datasets-designer.png)



> [!NOTE]
> Actuellement, le concepteur prend uniquement en charge le traitement des jeux de données [tabulaires](how-to-create-register-datasets.md#dataset-types). Si vous souhaitez utiliser des [jeux de données de fichier](how-to-create-register-datasets.md#dataset-types), utilisez le kit de développement logiciel (SDK) Azure Machine Learning disponible pour Python et R.

## <a name="import-data-using-the-import-data-module"></a>Importer des données à l’aide du module Importer des données

Bien que nous recommandions l'utilisation de jeux de données pour importer des données, vous pouvez également utiliser le module [Importer des données](algorithm-module-reference/import-data.md). Le module Importer des données ignore l’inscription de votre jeu de données dans Azure Machine Learning et importe les données directement depuis un [magasin de données](concept-data.md#datastores) ou une URL HTTP.

Pour plus d’informations sur l’utilisation du module Importer des données, consultez la [page de référence Importer des données](algorithm-module-reference/import-data.md).


## <a name="supported-sources"></a>Sources prises en charge

Cette section répertorie les sources de données prises en charge par le concepteur. Les données accèdent au concepteur depuis un magasin de données ou un [jeu de données tabulaires](how-to-create-register-datasets.md#dataset-types).

### <a name="datastore-sources"></a>Sources de magasin de données
Pour obtenir la liste des sources de magasin de données prises en charge, consultez [Accéder aux données dans les services de stockage Azure](how-to-access-data.md#supported-data-storage-service-types).

### <a name="tabular-dataset-sources"></a>Sources du jeu de données tabulaire

Le concepteur prend en charge les jeux de données tabulaires créés à partir des sources suivantes :
 * Fichiers délimités
 * Fichiers JSON
 * Fichiers Parquet
 * Requêtes SQL

## <a name="data-types"></a>Types de données

Le concepteur reconnaît en interne les types de données suivants :

* String
* Integer
* Decimal
* Boolean
* Date

Le concepteur utilise un type de données interne appelé pour transmettre des données entre les modules. Vous pouvez convertir de manière explicite vos données dans un format de table de données à l’aide du module [Convertir en jeu de données](algorithm-module-reference/convert-to-dataset.md). Tout module qui accepte des formats autres que le format interne convertit silencieusement les données avant de les transmettre au module suivant.

## <a name="data-constraints"></a>Contraintes de données

Les modules du concepteur sont limités par la taille de la cible de calcul. En présence de jeux de données plus volumineux, vous devez utiliser une plus grande ressource de calcul Azure Machine Learning. Pour plus d’informations sur le calcul Azure Machine Learning, consultez [Qu’est-ce qu’une cible de calcul dans Azure Machine Learning ?](concept-compute-target.md#azure-machine-learning-compute-managed)

## <a name="next-steps"></a>Étapes suivantes

Découvrez les principes de base du concepteur dans [Tutoriel : Prédire le prix de voitures avec le concepteur](tutorial-designer-automobile-price-train-score.md).