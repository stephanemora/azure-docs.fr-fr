---
title: Tester votre tâche Azure Stream Analytics avec des exemples de données | Microsoft Docs
description: Test des requêtes dans les travaux Stream Analytics
keywords: tester une tâche, échantillonnage des entrées, charger les exemples de données
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: stream-analytics
ms.workload: data-services
ms.date: 03/18/2018
ms.author: sngun
ms.openlocfilehash: c026a91fff5b8ef5774993b335f8d61877aa5d39
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="test-your-stream-analytics-query-with-sample-data"></a>Tester votre requête Stream Analytics avec des exemples de données

À l’aide d’Azure Stream Analytics, vous pouvez charger des exemples de données et tester des requêtes dans le portail sans démarrer ni arrêter une tâche.

## <a name="upload-sample-data-and-test-the-query"></a>Charger des exemples de données et tester la requête

1. Accédez à l’une de vos tâches Stream Analytics > cliquez sur **Requête** afin d’ouvrir la fenêtre de l’éditeur de requête. 

2. Pour tester votre requête avec des exemples de données d’entrée, cliquez avec le bouton droit sur l’une de vos entrées, puis sélectionnez **Upload sample data from file** (Charger les exemples de données à partir d’un fichier). Actuellement, vous pouvez charger uniquement des données au format JSON. Si vos données sont dans un autre format tel que CSV, vous devez les convertir au format JSON avant de les charger. Vous pouvez utiliser n’importe quel outil de conversion Open Source comme le [convertisseur CSV à JSON](http://www.convertcsv.com/csv-to-json.htm) pour convertir vos données au format JSON.

    ![Test des requêtes dans l’éditeur de requête Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

3. Une fois que le téléchargement est terminé, cliquez sur **Tester** pour tester cette requête dans les exemples de données que vous avez fournis.

    ![Exemple de données de test des requêtes dans l’éditeur de requête Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

4. Si vous souhaitez enregistrer la sortie test pour une utilisation ultérieure, la sortie de votre requête s’affiche dans le navigateur avec un lien vers les résultats du téléchargement. Vous pouvez maintenant facilement et de manière itérative modifier votre requête et la tester à plusieurs reprises pour voir comment la sortie change.

   ![Exemple de sortie de l’éditeur de requête Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

Lorsque vous utilisez plusieurs sorties dans une requête, vous pouvez voir les résultats pour chaque sortie séparément et basculer facilement entre eux. Une fois que vous avez vérifié les résultats affichés dans le navigateur, vous pouvez enregistrer votre requête, lancer votre tâche et le regarder traiter les événements sans erreur.
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
