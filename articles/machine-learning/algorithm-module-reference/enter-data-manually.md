---
title: 'Entrer manuellement les données : Référence de module'
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le module entrer manuellement les données dans le service Azure Machine Learning pour créer un petit jeu de données en tapant des valeurs. Le jeu de données peut avoir plusieurs colonnes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ee15b6fb7160ece907d55e790b0ae38ee458ab96
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028604"
---
# <a name="enter-data-manually-module"></a>Entrez manuellement les données du module

Cet article décrit un module de l’interface visuelle (version préliminaire) pour le service Azure Machine Learning.

Utilisez ce module pour créer un petit jeu de données en tapant des valeurs. Le jeu de données peut avoir plusieurs colonnes.
  
Ce module peut être utile dans les scénarios suivants :  
  
- Génération d’un petit ensemble de valeurs pour le test  
  
- Création d’une liste courte des étiquettes
  
- Saisie d’une liste de noms de colonnes à insérer dans un jeu de données

## <a name="enter-data-manually"></a>Entrer manuellement les données 
  
1.  Ajouter le [entrer manuellement les données](./enter-data-manually.md) module à votre expérience. Vous trouverez ce module dans le **données entrée et sortie** catégorie dans Azure Machine Learning. 
  
2.  Pour **DataFormat**, sélectionnez une des options suivantes. Ces options déterminent la façon dont les données que vous fournissez doivent être analysées. La configuration requise pour chaque format diffèrent considérablement, par conséquent, veillez à lire les rubriques connexes.  
  
    -   **ARFF**. Le format de fichier de relation d’attribut, utilisé par Weka.   
  
    -   **CSV**. Format de valeurs séparées par des virgules. Pour plus d’informations, consultez [Convert to CSV](./convert-to-csv.md).  
  
    -   **SVMLight**. Un format utilisé par Vowpal Wabbit et d’autres frameworks d’apprentissage automatique.  
  
    -   **TSV**. Format de valeurs séparées par une tabulation.

     Si vous choisissez un format et ne fournissez pas de données qui répond aux spécifications de format, une erreur d’exécution se produit.
  
3.  Cliquez à l’intérieur du **données** commencez à entrer des données de la zone de texte. Les formats suivants nécessitent une attention particulière :  
  
    - **CSV**:  Pour créer plusieurs colonnes, Coller texte séparés par des virgules, ou plusieurs colonnes à l’aide de virgules entre les champs de type.
  
        Si vous sélectionnez le **HasHeader** option, vous pouvez utiliser la première ligne des valeurs en tant que l’en-tête de colonne.  
  
        Si vous désélectionnez cette option, les noms de colonnes, Col1, Col2 et ainsi de suite, sont utilisées. Vous pouvez ajouter ou modifier des colonnes ultérieurement à l’aide des noms [modifier les métadonnées](./edit-metadata.md).  
  
    - **TSV**: Pour créer plusieurs colonnes, Coller texte séparé par des tabulations, ou plusieurs colonnes à l’aide des onglets entre les champs de type.  
  
        Si vous sélectionnez le **HasHeader** option, vous pouvez utiliser la première ligne des valeurs en tant que l’en-tête de colonne.  
  
        Si vous désélectionnez cette option, les noms de colonnes, Col1, Col2 et ainsi de suite, sont utilisées. Vous pouvez ajouter ou modifier des colonnes ultérieurement à l’aide des noms [modifier les métadonnées](./edit-metadata.md).  
  
    -   **ARFF**:  Collez dans un fichier de format ARFF existant. Si vous tapez des valeurs directement, veillez à ajouter l’en-tête facultatif et les champs d’attribut requis au début des données. 
    
        Par exemple, les lignes d’en-tête et d’attribut suivantes peuvent être ajoutés à une liste simple. L’en-tête de colonne serait `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: Tapez ou collez les valeurs en utilisant le format SVMLight.  
  
        Par exemple, l’exemple suivant représente les premières lignes du jeu de données don de sang, au format de SVMight :  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Lorsque vous exécutez le [entrer manuellement les données](./enter-data-manually.md) module, ces lignes sont converties en un jeu de données de colonnes et indexer les valeurs comme suit :  
  
        |Col1|Col2|Col3|Col4|Étiquettes|  
        |-|-|-|-|-|  
        |0.00016|0.004|0.999961|0.00784|1|  
        |0|0.004|0.999955|0.008615|1|  
  
4.  Appuyez sur ENTRÉE après chaque ligne, pour démarrer une nouvelle ligne.  
  
     **Veillez à appuyez sur ENTRÉE après la dernière ligne.** 
     
     Si vous appuyez sur entrée vide de plusieurs fois pour ajouter plusieurs lignes de fin, la dernière ligne vide est supprimée tronqué, mais les autres lignes vides sont traitées comme des valeurs manquantes.  
  
     Si vous créez des lignes avec des valeurs manquantes, vous pouvez toujours filtrer les ultérieurement.  
  
5.  Cliquez sur le module et sélectionnez **exécuter sélection** pour analyser les données et les charger dans votre espace de travail comme un jeu de données.  
  
     Pour afficher le jeu de données, cliquez sur le port de sortie, puis sélectionnez **visualiser**.  
## <a name="next-steps"></a>Étapes suivantes

Consultez le [ensemble de modules disponibles](module-reference.md) au service Azure Machine Learning. 