---
title: Déboguer localement du code Azure Data Lake Analytics
description: Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio pour tester et déboguer des travaux U-SQL sur votre station de travail locale.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 9e73fc4db1404842e6d3878d88d8f02511587bc9
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43044975"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Déboguer localement du code Azure Data Lake Analytics

Vous pouvez utiliser Azure Data Lake Tools pour Visual Studio pour exécuter et de déboguer du code Azure Data Lake Analytics sur votre station de travail, tout comme dans le service Azure Data Lake Analytics.

Découvrez comment [exécuter un script U-SQL sur votre ordinateur local](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Déboguer localement des scripts et des assemblys C#

Vous pouvez déboguer des assemblys C# sans les envoyer ou les inscrire sur le service Azure Data Lake Analytics. Vous pouvez définir des points d’arrêt à la fois dans les fichier code-behind et dans un projet C# référencé.

### <a name="debug-local-code-in-a-code-behind-file"></a>Déboguer le code local dans le fichier code-behind

1. Définissez des points d’arrêt dans le fichier code-behind.
2. Appuyez sur **F5** pour déboguer le script localement.

> [!NOTE]
   > La procédure suivante fonctionne uniquement dans Visual Studio 2015. Dans les versions plus anciennes de Visual Studio, vous deviez ajouter les fichiers **PDB** manuellement.  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Déboguer le code local dans un projet C# référencé

1. Créez un projet d’assembly C#, puis générez-le pour obtenir le fichier **DLL** de sortie.
2. Inscrivez le fichier **DLL** à l’aide d’une instruction U-SQL :

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Définissez des points d'arrêt dans le code C#.
4. Sélectionnez **F5** pour déboguer le script en faisant référence au fichier **DLL** C# localement.


## <a name="next-steps"></a>Étapes suivantes

- Pour un exemple de requête plus complexe, voir [Analyse de journaux de sites web à l’aide d’Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Pour afficher les détails d’un travail, voir [Utilisation de l’Explorateur de travaux et de la Vue des travaux pour les travaux Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Pour utiliser la vue d’exécution du vertex, voir [Utilisation de la vue d’exécution du vertex dans Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
