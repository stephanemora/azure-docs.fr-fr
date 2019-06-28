---
title: 'Importer des données : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module Importer des données dans Azure Machine Learning service pour charger des données dans une expérience de Machine Learning à partir de services de données cloud existants.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ed51c4e7b6c7d226c7827d1ba00bc96a7be1e6b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028304"
---
# <a name="import-data-module"></a>Module Importer des données

Cet article décrit un module de l’interface visuelle (préversion) d’Azure Machine Learning service.

Utilisez ce module pour charger des données dans une expérience de Machine Learning de services de données cloud existants.  
Le module comporte désormais un Assistant pour vous aider à choisir une option de stockage et à sélectionner parmi des abonnements et comptes existants pour configurer rapidement toutes les options. Vous voulez modifier une connexion de données existante ? Pas de problème. L’Assistant charge tous les détails de configuration précédents pour que vous n’ayez pas à recommencer à partir de zéro. 
  
Après avoir défini les données souhaitées et une fois connecté à la source, [Importer des données](./import-data.md) déduit le type de données de chaque colonne en fonction des valeurs qu’elle contient et charge les données dans votre espace de travail Azure Machine Learning. La sortie de [Importer des données](./import-data.md) est un jeu de données qui peut être utilisé avec n’importe quelle expérience.

  
Si votre source de données change, vous pouvez actualiser le jeu de données et ajouter de nouvelles données en réexécutant [Importer des données](./import-data.md). Toutefois, si vous ne souhaitez pas relire la source chaque fois que vous exécutez l’expérience, définissez l’option **Use cached results** (Utiliser les résultats mis en cache) sur TRUE. Lorsque cette option est sélectionnée, le module vérifie si l’expérience a déjà été exécutée avec la même source et les mêmes options d’entrée. Si une exécution précédente est trouvée, les données dans le cache sont utilisées plutôt que de recharger les données de la source.
 

## <a name="data-sources"></a>Sources de données

Le module Importer des données prend en charge les sources de données suivantes. Cliquez sur les liens pour obtenir des instructions détaillées et des exemples d’utilisation de chaque source de données. 
 
Si vous n’êtes pas sûr de l’emplacement ou vous devez stocker vos données, consultez ce guide de scénarios de données courants dans le processus de science des données :  [Scénarios d’analyses avancées dans Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios). 


|Source de données| Utiliser avec|
|-----------|-----------|  
|[URL web via HTTP](./import-from-web-url-via-http.md)|Obtenir des données qui sont hébergées sur une URL web qui utilise le protocole HTTP et qui a été fournie dans les formats CSV, TSV, ARFF ou SvmLight|  
|[Importer à partir du Stockage Blob Azure](./import-from-azure-blob-storage.md) |Obtenir des données qui sont stockées dans le service blob Azure|  

## <a name="how-to-use-import-data"></a>Comment utiliser Importer des données
 
1. Ajoutez le module **Importer des données** à votre expérience. Vous trouverez ce module dans la catégorie **Data Input and Output** (Entrée et sortie de données) de l’interface.

2. Cliquez sur **Launch Data Import Wizard** (Lancer l’Assistant Importation de données) pour configurer la source de données à l’aide d’un Assistant.

    L’Assistant obtient le nom et les informations d’identification du compte, et vous aide à configurer d’autres options. Si vous modifiez une configuration existante, il charge tout d’abord les valeurs actuelles.

3. Si vous ne souhaitez pas utiliser l’Assistant, cliquez sur **Source de données**, puis choisissez le type de stockage basé sur le cloud que vous consultez. 

    Les paramètres supplémentaires varient selon le type de stockage que vous choisissez, et si le stockage est sécurisé ou non. Vous devrez peut-être fournir le nom de compte, le type de fichier ou les informations d’identification. Certaines sources ne nécessitent pas d’authentification. Pour d’autres, vous devrez peut-être connaître le nom du compte, une clé ou le nom du conteneur.

4. Sélectionnez l’option **Use cached results** (Utiliser les résultats mis en cache) si vous souhaitez mettre en cache le jeu de données pour le réutiliser lors des exécutions suivantes.

    Si l’on suppose qu’aucune autre modification n’a été apportée aux paramètres de module, l’expérience charge les données de la première exécution du module uniquement, puis utilise une version mise en cache du jeu de données.

    Désélectionnez cette option si vous devez recharger les données à chaque exécution de l’expérience.

5. Exécutez l’expérience.

    Lorsque le module Importer des données charge les données dans l’interface, il déduit le type de données de chaque colonne en fonction des valeurs qu’elle contient : numériques ou par catégorie.

    - Si un en-tête est présent, il est utilisé pour nommer les colonnes du jeu de données de sortie.

    - S’il n’existe aucun en-tête de colonne dans les données, les nouveaux noms de colonnes sont générés au format col1, col2, ... , coln*.

## <a name="results"></a>Résultats

Lorsque l’importation est terminée, cliquez sur le jeu de données de sortie et sélectionnez **Visualiser** pour voir si les données ont bien été importées.

Si vous souhaitez enregistrer les données pour les réutiliser, plutôt que d’importer un nouveau jeu de données à chaque exécution de l’expérience, cliquez avec le bouton droit sur la sortie et sélectionnez **Enregistrer comme dataset**. Choisissez un nom pour le jeu de données. Le jeu de données enregistré stocke les données au moment de l’enregistrement, et les données ne sont pas mises à jour lorsque l’expérience est réexécutée, même si le jeu de données dans l’expérience change. Cela peut être pratique pour prendre des captures instantanées de données.

Après l’importation des données, d’autres préparations seront peut-être nécessaires pour modélisation et l’analyse :


- Utilisez [Modifier les métadonnées](./edit-metadata.md) pour modifier les noms de colonnes, traiter une colonne comme un autre type de données, ou pour indiquer que certaines colonnes sont des étiquettes ou des fonctionnalités.

- Utilisez [Sélectionner des colonnes dans le jeu de données](./select-columns-in-dataset.md) pour sélectionner un sous-ensemble de colonnes à transformer ou à utiliser dans la modélisation. Les colonnes transformées ou supprimées peuvent facilement être réassociées au jeu de données d’origine à l’aide du module [Ajouter des colonnes](./add-columns.md).  

- Utilisez [Partition and Sample](./partition-and-sample.md) (Partition et échantillon) pour diviser le jeu de données, effectuer un échantillonnage ou obtenir les n premières lignes.

## <a name="next-steps"></a>Étapes suivantes

Consultez l’[ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning service. 