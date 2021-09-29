---
title: Copie en bloc de fichiers dans une base de données
description: Découvrez comment utiliser un modèle de solution pour copier des données en bloc depuis Azure Data Lake Storage Gen2 vers Azure Synapse Analytics/Azure SQL Database.
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 59189f0c197294ca74e01d331663c51fa0e2bd7d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757892"
---
# <a name="bulk-copy-from-files-to-database"></a>Copie en bloc de fichiers dans une base de données

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cet article décrit un modèle de solution qui permet de copier des données en bloc depuis Azure Data Lake Storage Gen2 vers Azure Synapse Analytics/Azure SQL Database.

## <a name="about-this-solution-template"></a>À propos de ce modèle de solution

Ce modèle récupère les fichiers depuis la source Azure Data Lake Storage Gen2. Ensuite, il effectue une itération sur chaque fichier figurant dans la source et copie le fichier vers le magasin de données de destination. 

Actuellement, ce modèle prend uniquement en charge la copie de données au format **DelimitedText**. Les fichiers dans d’autres formats de données peuvent également être récupérés à partir du magasin de données source, mais ils ne peuvent pas être copiés vers le magasin de données de destination.  

Le modèle comporte trois activités :
- L’activité **Obtenir des métadonnées** permet de récupérer des fichiers depuis Azure Data Lake Storage Gen2 et de les transmettre à l’activité *ForEach* suivante.
- L’activité **ForEach** obtient des fichiers à partir de l’activité *Obtenir des métadonnées* et itère chaque fichier sur l’activité *Copier*.
- L’activité **Copier** réside dans l’activité *ForEach* pour copier chaque fichier du magasin de données source vers le magasin de données de destination.

Le modèle définit les deux paramètres suivants :
- *SourceContainer* est le chemin de conteneur racine d’où les données sont copiées dans Azure Data Lake Storage Gen2. 
- *SourceDirectory* est le chemin de répertoire sous le conteneur racine d’où les données sont copiées dans Azure Data Lake Storage Gen2.

## <a name="how-to-use-this-solution-template"></a>Utiliser ce modèle de solution

1. Accédez au modèle **Copie en bloc de fichiers dans une base de données**. Créez une **nouvelle** connexion au magasin Gen2 source. Sachez que « GetMetadataDataset » et « SourceDataset » sont des références à la même connexion de votre magasin de fichiers source.

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/source-connection.png" alt-text="Créer une connexion au magasin de données source":::

2. Créez une **nouvelle** connexion au magasin de données récepteur vers lequel vous copiez des données.

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/destination-connection.png" alt-text="Créer une connexion au magasin de données récepteur":::
    
3. Sélectionnez **Utiliser ce modèle**.

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/use-template.png" alt-text="Utiliser ce modèle":::
    
4. Vous pouvez voir un pipeline créé comme indiqué dans l’exemple suivant :

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png" alt-text="Passer en revue le pipeline":::

    > [!NOTE]
    > Si vous choisissez **Azure Synapse Analytics** comme destination des données à l’**étape 2** mentionnée ci-dessus, vous devez entrer une connexion à un stockage Blob Azure pour la préproduction, comme le demande Azure Synapse Analytics Polybase. Comme le montre la capture d’écran suivante, le modèle génère automatiquement un *chemin de stockage* pour le stockage d’objets blob. Vérifiez si le conteneur a été créé après l’exécution du pipeline.
        
    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/staging-account.png" alt-text="Paramètre Polybase":::

5. Sélectionnez **Déboguer**, entrez les **Paramètres**, puis sélectionnez **Terminer**.

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/debug-run.png" alt-text="Cliquez sur **Déboguer**":::

6. Lorsque l’exécution du pipeline s’effectue correctement, vous pouvez voir des résultats similaires à l’exemple suivant :

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png" alt-text="Vérifier le résultat":::

       
## <a name="next-steps"></a>Étapes suivantes

- [Présentation du service Azure Data Factory](introduction.md)