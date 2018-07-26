---
title: Déboguer localement du code Azure Data Lake Analytics | Microsoft Docs
description: Découvrez comment utiliser Azure Data Lake Tools pour Visual Studio pour tester et déboguer des travaux U-SQL sur votre station de travail locale.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: 181512c12c1e72e6aa8205aabd5ea22816d4c5df
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37890709"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Déboguer localement du code Azure Data Lake Analytics

Comme dans le service Azure Data Lake, Azure Data Lake Tools pour Visual Studio peut être utilisé afin d’exécuter et de déboguer du code Azure Data Lake Analytics sur votre station de travail.

Découvrez [comment exécuter un script U-SQL sur votre ordinateur local](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="debug-scripts-and-c-assemblies-locally"></a>Déboguer localement des scripts et des assemblys C#

Vous pouvez déboguer des assemblys C# sans les envoyer ni les inscrire auprès du service Azure Data Lake Analytics. Vous pouvez définir des points d'arrêt à la fois dans les fichier code-behind et dans un projet C# référencé.

### <a name="to-debug-local-code-in-code-behind-file"></a>Pour déboguer le code local dans le fichier code-behind

1. Définissez des points d'arrêt dans le fichier code-behind.
2. Appuyez sur F5 pour déboguer le script localement.

> [!NOTE]
   > La procédure suivante fonctionne uniquement dans Visual Studio 2015. Dans les versions Visual Studio plus anciennes, vous devrez peut-être ajouter manuellement les fichiers pdb.  
   >
   >

### <a name="to-debug-local-code-in-a-referenced-c-project"></a>Pour déboguer le code local dans un projet C# référencé

1. Créez un projet d'assembly C# et générez-le pour obtenir la dll de sortie.
2. Inscrivez la dll à l'aide d'une instruction SQL-U :

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Définissez des points d'arrêt dans le code C#.
4. Appuyez sur F5 pour déboguer le script en faisant référence à la DLL C# localement.


## <a name="next-steps"></a>Étapes suivantes

- Pour consulter une requête plus complexe, voir [Analyse de journaux des sites web à l’aide d’Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Pour afficher les détails d’un travail, voir [Utilisation de l’Explorateur de travaux et de la Vue des travaux pour les travaux Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md).
- Pour utiliser la vue d’exécution du vertex, voir [Utilisation de la vue d’exécution du vertex dans Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
