---
title: 'Entrer des données manuellement : Informations de référence sur les modules'
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le module Entrer des données manuellement dans Azure Machine Learning pour créer un petit jeu de données en saisissant des valeurs. Le jeu de données peut avoir plusieurs colonnes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 949cdb4c53531a548380a6f0116fb45f91d7ba66
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268772"
---
# <a name="enter-data-manually-module"></a>Module Entrer des données manuellement

Cet article décrit un module dans le concepteur Azure Machine Learning (version préliminaire).

Utilisez ce module pour créer un petit jeu de données en saisissant des valeurs. Le jeu de données peut avoir plusieurs colonnes.
  
Ce module peut être utile dans des scénarios tels que les suivants :  
  
- Génération d’un petit ensemble de valeurs à des fins de test  
  
- Création d’une courte liste d’étiquettes
  
- Saisie d’une liste de noms de colonnes à insérer dans un jeu de données

## <a name="enter-data-manually"></a>Entrer des données manuellement 
  
1.  Ajoutez le module [Entrer des données manuellement](./enter-data-manually.md) à votre pipeline. Vous trouverez ce module dans la catégorie **Data Input and Output** (Entrée et sortie de données) d’Azure Machine Learning. 
  
2.  Pour **DataFormat**, sélectionnez l’une des options suivantes. Ces options déterminent comment les données que vous fournissez doivent être analysées. Les exigences relatives à chaque format sont considérablement différentes. Veillez par conséquent à lire les rubriques connexes.  
  
    -   **ARFF** : Format de fichier de relation d’attribut, utilisé par Weka.   
  
    -   **CSV** : Format de valeurs séparées par des virgules. Pour plus d’informations, consultez [Convertir au format CSV](./convert-to-csv.md).  
  
    -   **SVMLight** : Format utilisé par Vowpal Wabbit et d’autres infrastructures de Machine Learning.  
  
    -   **TSV** : Format de valeurs séparées par des tabulations.

     Si vous choisissez un format et ne fournissez pas de données conformes aux spécifications de format, une erreur d’exécution se produit.
  
3.  Cliquez à l’intérieur de la zone de texte **Données** pour commencer à entrer des données. Les formats suivants requièrent une attention particulière :  
  
    - **CSV** :  Pour créer plusieurs colonnes, collez du texte séparé par des virgules, ou saisissez plusieurs colonnes en utilisant des virgules entre les champs.
  
        Si vous sélectionnez l’option **HasHeader**, vous pouvez utiliser la première ligne de valeurs comme en-tête de colonne.  
  
        Si vous désélectionnez cette option, les noms de colonnes, Col1, Col2 et ainsi de suite, sont utilisés. Vous pouvez ajouter ou modifier des noms de colonnes ultérieurement à l’aide de [Modifier les métadonnées](./edit-metadata.md).  
  
    - **TSV** : Pour créer plusieurs colonnes, collez du texte séparé par des tabulations, ou saisissez plusieurs colonnes en utilisant des tabulations entre les champs.  
  
        Si vous sélectionnez l’option **HasHeader**, vous pouvez utiliser la première ligne de valeurs comme en-tête de colonne.  
  
        Si vous désélectionnez cette option, les noms de colonnes, Col1, Col2 et ainsi de suite, sont utilisés. Vous pouvez ajouter ou modifier des noms de colonnes ultérieurement à l’aide de [Modifier les métadonnées](./edit-metadata.md).  
  
    -   **ARFF** :  Collez dans un fichier de format ARFF existant. Si vous saisissez des valeurs directement, veillez à ajouter l’en-tête facultatif et les champs d’attribut obligatoires au début des données. 
    
        Par exemple, les lignes d’en-tête et d’attribut suivantes peuvent être ajoutées à une liste simple. L’en-tête de colonne serait `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight** : Saisissez ou collez des valeurs en utilisant le format SVMLight.  
  
        Par exemple, l’exemple suivant représente les deux premières lignes du jeu de données Blood Donation, au format SVMight :  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Lorsque vous exécutez le module [Entrer des données manuellement](./enter-data-manually.md), ces lignes sont converties en un jeu de données de colonnes et de valeurs d’index comme suit :  
  
        |Col1|Col2|Col3|Col4|Étiquettes|  
        |-|-|-|-|-|  
        |0,00016|0,004|0,999961|0,00784|1|  
        |0|0,004|0,999955|0,008615|1|  
  
4.  Appuyez sur ENTRÉE après chaque ligne, pour aller à la ligne.      
     
     Si vous appuyez plusieurs fois sur Entrée pour ajouter plusieurs lignes de fin vides, les lignes vides sont supprimées.  
  
     Si vous créez des lignes avec des valeurs manquantes, vous pouvez toujours les filtrer ultérieurement.  
  
5.  Connectez le port de sortie à d’autres modules, puis exécutez le pipeline.  
  
     Pour afficher le jeu de données, cliquez avec le bouton droit sur le module, puis sélectionnez **Visualiser**.  
## <a name="next-steps"></a>Étapes suivantes

Consultez [l’ensemble des modules disponibles](module-reference.md) pour Azure Machine Learning. 