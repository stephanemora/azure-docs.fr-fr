---
title: 'Importer des données : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Importer des données dans Azure Machine Learning pour charger des données dans un pipeline de Machine Learning à partir de services de données cloud existants.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 8d099d0b8f8e31bf77f5ddfad320ac792c5c01e5
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529607"
---
# <a name="import-data-module"></a>Module Importer des données

Cet article décrit un module dans le concepteur Azure Machine Learning (version préliminaire).

Utilisez ce module pour charger des données dans un pipeline de Machine Learning des services de données cloud existants. 

> [!Note]
> Toutes les fonctionnalités fournies par ce module peuvent être effectuées par le **magasin de données** et les **jeux de données** dans la page d’accueil de l’espace de travail. Nous vous recommandons d’utiliser le **magasin de données** et le **jeu de données** qui comprend des fonctionnalités supplémentaires telles que la surveillance des données. Pour plus d’informations, consultez l’article [Comment accéder aux données](../how-to-access-data.md) et [Comment inscrire des jeux de données](../how-to-create-register-datasets.md).
> Une fois que vous avez inscrit un jeu de données, vous pouvez le trouver dans la catégorie **Jeux de données** -> **Mes jeux de données** dans l’interface du concepteur. Ce module est réservé aux utilisateurs Studio (classique) pour une expérience familière. 
>

Tout d’abord, choisissez la source à partir de laquelle vous effectuez la lecture et terminez les paramétrages supplémentaires. Le module **Importer des données** prend en charge les données lues à partir des sources suivantes :

- URL via HTTP
- Stockages cloud Azure via les [**magasin de données**](../how-to-access-data.md))
    - Conteneur d’objets blob Azure
    - Partage de fichiers Azure
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL Database
    - Azure PostgreSQL    

Avant d’utiliser le stockage cloud, vous devez inscrire un magasin de données dans votre espace de travail Azure Machine Learning. Pour plus d’informations, consultez la page [Accès aux données](../how-to-access-data.md). 

Après avoir défini les données souhaitées et une fois connecté à la source, **[Importer des données](./import-data.md)** déduit le type de données de chaque colonne en fonction des valeurs qu’elle contient et charge les données dans votre pipeline de concepteur. La sortie de **Importer des données** est un jeu de données qui peut être utilisé avec n’importe quel pipeline de concepteur.

Si votre source de données change, vous pouvez actualiser le jeu de données et ajouter de nouvelles données en réexécutant [Importer des données](./import-data.md). Toutefois, si vous ne souhaitez pas relire la source chaque fois que vous exécutez le pipeline, définissez l’option **Utiliser les résultats mis en cache** sur TRUE. Lorsque cette option est sélectionnée, le module vérifie si le pipeline a déjà été exécuté avec la même source et les mêmes options d’entrée. Si une exécution précédente est trouvée, les données dans le cache sont utilisées plutôt que de recharger les données de la source.

## <a name="how-to-configure-import-data"></a>Comment configurer Importer des données

1. Ajoutez le module **Importer des données** à votre pipeline. Vous trouverez ce module dans la catégorie **Data Input and Output** (Entrée et sortie de données) du concepteur.

1. Cliquez sur **Launch Data Import Wizard** (Lancer l’Assistant Importation de données) pour configurer la source de données à l’aide d’un Assistant.

    L’Assistant obtient le nom et les informations d’identification du compte, et vous aide à configurer d’autres options. Si vous modifiez une configuration existante, il charge tout d’abord les valeurs actuelles.

1. Sélectionnez **Source de données**, puis choisissez le type de source de données. Cela peut être HTTP ou un magasin de données.

    Si vous choisissez le magasin de données, vous pouvez sélectionner les magasins de données existants déjà inscrits dans votre espace de travail Azure Machine Learning ou créer un nouveau magasin de données. Ensuite, définissez le chemin d’accès des données à importer dans le magasin de données. Vous pouvez facilement parcourir le chemin d’accès en cliquant sur **Parcourir le chemin d’accès**![import-data-path](media/module/import-data-path.png)

1. Sélectionnez le schéma d’aperçu pour filtrer les colonnes que vous souhaitez inclure. Vous pouvez également définir des paramètres avancés tels que les délimiteurs dans les options d’analyse.

    ![import-data-preview](media/module/import-data.png)

1. Sélectionnez l’option **Use cached results** (Utiliser les résultats mis en cache) si vous souhaitez mettre en cache le jeu de données pour le réutiliser lors des exécutions suivantes.

    Si l’on suppose qu’aucune autre modification n’a été apportée aux paramètres du module, le pipeline charge les données de la première exécution du module uniquement, puis utilise une version mise en cache du jeu de données.

    Décochez cette option si vous devez recharger les données à chaque exécution du pipeline.

1. Exécuter le pipeline.

    Lorsque le module Importer des données charge les données dans le concepteur, il déduit le type de données de chaque colonne en fonction des valeurs qu’elle contient : numériques ou par catégorie.

    Si un en-tête est présent, il est utilisé pour nommer les colonnes du jeu de données de sortie.

    S’il n’existe aucun en-tête de colonne dans les données, les nouveaux noms de colonnes sont générés au format col1, col2, ... , coln*.

## <a name="results"></a>Résultats

Lorsque l’importation est terminée, cliquez sur le jeu de données de sortie et sélectionnez **Visualiser** pour voir si les données ont bien été importées.

Si vous souhaitez enregistrer les données pour les réutiliser, plutôt que d’importer un nouveau jeu de données à chaque exécution du pipeline, cliquez avec le bouton droit sur la sortie et sélectionnez **Enregistrer comme jeu de données**. Choisissez un nom pour le jeu de données. Le jeu de données enregistré préserve les données au moment de l’enregistrement et les données ne sont pas mises à jour au moment de la ré-exécution du pipeline, même si le jeu de données dans le pipeline est modifié. Cela peut être pratique pour prendre des captures instantanées de données.

Après l’importation des données, d’autres préparations seront peut-être nécessaires pour modélisation et l’analyse :

- Utilisez [Modifier les métadonnées](./edit-metadata.md) pour modifier les noms de colonnes, traiter une colonne comme un autre type de données, ou pour indiquer que certaines colonnes sont des étiquettes ou des fonctionnalités.

- Utilisez [Sélectionner des colonnes dans le jeu de données](./select-columns-in-dataset.md) pour sélectionner un sous-ensemble de colonnes à transformer ou à utiliser dans la modélisation. Les colonnes transformées ou supprimées peuvent facilement être réassociées au jeu de données d’origine à l’aide du module [Ajouter des colonnes](./add-columns.md).  

- Utilisez [Partition and Sample](./partition-and-sample.md) (Partition et échantillon) pour diviser le jeu de données, effectuer un échantillonnage ou obtenir les n premières lignes.

## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 
