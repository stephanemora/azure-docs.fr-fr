---
title: 'Importer des données : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module importer des données dans le service Azure Machine Learning pour charger des données dans une expérience d’apprentissage à partir des services de données cloud existant.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ed51c4e7b6c7d226c7827d1ba00bc96a7be1e6b0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028304"
---
# <a name="import-data-module"></a>Importer le module de données

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour charger des données dans une expérience d’apprentissage à partir des services de données cloud existant.  
Le module comporte désormais un Assistant pour vous aider à choisir une option de stockage et de choisir parmi les comptes et abonnements existants pour configurer rapidement toutes les options. Vous voulez modifier une connexion de données existante ? Aucun problème ; l’Assistant charge tous les détails de la configuration précédente afin que vous n’êtes pas obligé de redémarrer à partir de zéro. 
  
Après avoir défini les données que vous souhaitez vous connecter à la source, [importer des données](./import-data.md) déduit le type de données de chaque colonne en fonction des valeurs qu’il contienne et charge les données dans votre espace de travail Azure Machine Learning. La sortie de [importer des données](./import-data.md) est un jeu de données qui peut être utilisé avec n’importe quelle expérience.

  
Si votre source de données change, vous pouvez actualiser le jeu de données et ajouter de nouvelles données en réexécutant [importer des données](./import-data.md). Toutefois, si vous ne souhaitez pas ré-lus dans la source chaque fois que vous exécutez l’expérience, sélectionnez le **utiliser mis en cache les résultats** option sur TRUE. Lorsque cette option est sélectionnée, le module vérifie si l’expérience a exécuté précédemment à l’aide de la même source et les mêmes options d’entrée. Si une exécution précédente est trouvé, les données dans le cache sont utilisées, au lieu de rechargement des données à partir de la source.
 

## <a name="data-sources"></a>Sources de données

Le module importer des données prend en charge les sources de données suivantes. Cliquez sur les liens pour obtenir des instructions détaillées et des exemples d’utilisation de chaque source de données. 
 
Si vous n’êtes pas sûr de l’emplacement ou vous devez stocker vos données, consultez ce guide pour les scénarios de données courants dans le processus de science des données :  [Scénarios d’analytique avancée dans Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios). 


|Source de données| Utiliser avec|
|-----------|-----------|  
|[URL Web via HTTP](./import-from-web-url-via-http.md)|Obtenir des données qui sont hébergées sur une URL web qui utilise le protocole HTTP et qui a été fourni dans les formats CSV, TSV, ARFF ou SvmLight|  
|[Importer à partir du stockage d’objets Blob Azure](./import-from-azure-blob-storage.md) |Obtenir des données qui sont stockées dans le service blob Azure|  

## <a name="how-to-use-import-data"></a>Comment utiliser les données d’importation
 
1. Ajouter le **importer des données** module à votre expérience. Vous trouverez ce module dans le **données entrée et sortie** catégorie dans l’interface.

2. Cliquez sur **lancer l’Assistant Importation données** pour configurer la source de données à l’aide d’un Assistant.

    L’Assistant Obtient le nom du compte et les informations d’identification et les aide à configurer d’autres options. Si vous modifiez une configuration existante, il charge tout d’abord les valeurs actuelles.

3. Si vous ne souhaitez pas utiliser l’Assistant, cliquez sur **source de données**, puis choisissez le type de stockage basé sur le cloud vous lisez à partir de. 

    Paramètres supplémentaires varient selon le type de stockage que vous choisissez, et indique si le stockage est sécurisé ou non. Vous devrez peut-être fournir le nom de compte, type de fichier ou informations d’identification. Certaines sources ne nécessitent pas l’authentification ; pour d’autres, vous devrez peut-être connaître le nom du compte, une clé ou le nom du conteneur.

4. Sélectionnez le **utiliser mis en cache les résultats** option si vous souhaitez mettre en cache le jeu de données pour une réutilisation lors des exécutions consécutives.

    En supposant qu’aucune autre modification aux paramètres de module n’ont été, l’expérience charge l’heure de données uniquement le premier, le module est exécuté et par la suite utilise une version mise en cache du jeu de données.

    Désélectionnez cette option si vous avez besoin recharger les données chaque fois que vous exécutez l’expérience.

5. Exécutez l’expérience.

    Lors de l’importation de données charge les données dans l’interface, il déduit le type de données de chaque colonne en fonction des valeurs qu’elle contient, numériques ou catégorielles.

    - Si un en-tête est présent, l’en-tête est utilisé pour nommer les colonnes du jeu de données de sortie.

    - S’il n’y a aucun en-tête de colonne existant dans les données, les nouveaux noms de colonnes sont générés à l’aide du format col1, col2... , coln *.

## <a name="results"></a>Résultats

Lors de l’importation terminée, cliquez sur le jeu de données de sortie et sélectionnez **visualiser** pour voir si les données ont été importées avec succès.

Si vous souhaitez enregistrer les données pour une réutilisation, plutôt que de l’importation d’un nouveau jeu de données à chaque exécution de l’expérience, la sortie avec le bouton droit et sélectionnez **enregistrer comme Dataset**. Choisissez un nom pour le jeu de données. Le jeu de données enregistré préserve les données au moment de l’enregistrement, et les données ne sont pas actualisées lors de l’expérience est réexécutée, même si le jeu de données dans l’expérience est modifiée. Il se peut que cela peut être pratique de captures instantanées de données.

Après avoir importé les données, il devra certaines tâches de préparation supplémentaires pour la modélisation et l’analyse :


- Utilisez [modifier les métadonnées](./edit-metadata.md) pour modifier les noms de colonne, pour gérer une colonne sous la forme d’un autre type de données, ou pour indiquer que certaines colonnes sont des étiquettes ou des fonctionnalités.

- Utilisez [Select Columns in Dataset](./select-columns-in-dataset.md) pour sélectionner un sous-ensemble de colonnes à transformer ou à utiliser dans la modélisation. Les colonnes transformées ou supprimés peuvent facilement être rejoints au jeu de données d’origine à l’aide de la [Add Columns](./add-columns.md) module.  

- Utilisez [Partition and Sample](./partition-and-sample.md) pour diviser le jeu de données, effectuer un échantillonnage ou obtenir les n premières lignes.

## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 