---
title: Tester une requête à l’aide d’exemples de données dans Azure Stream Analytics
description: Cet article décrit comment tester une requête à l’aide de quelques exemples de données d’entrée dans Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: 305b767ee86de6c8b04fed17514a9092afc2d735
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/12/2018
ms.locfileid: "30961688"
---
# <a name="test-a-query-and-sample-input-in-azure-stream-analytics"></a>Tester une requête et des exemples d’entrées dans Azure Stream Analytics 

À l’aide d’Azure Stream Analytics, vous pouvez échantillonner des événements d’entrée qui proviennent d’un fichier et tester des requêtes dans le portail sans avoir à démarrer ou arrêter un travail.

## <a name="testing-your-query"></a>Test de votre requête

Dans le volet Détails du travail Stream Analytics, ouvrez le panneau **Éditeur de requête** en cliquant sur le nom de la requête sous **Requête**. (Dans notre exemple de scénario, comme aucune requête n’a encore été créée, cliquez sur l’espace réservé **< >**.)

![Éditeur de requête Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor.png)

Le panneau de l’éditeur enrichi pour la création de votre requête s’affiche comme dans la version précédente. Le panneau a été mis à jour avec un nouveau volet gauche qui affiche les entrées et sorties utilisées par la requête et définies pour ce travail.

![Listes d’entrées et de sorties de l’éditeur de requête Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-highlight.png)

Sont également affichées une entrée et une sortie supplémentaires, qui ne sont pas définies. Celles-ci proviennent du nouveau modèle de requête avec lequel vous avez commencé. Elles changent, ou disparaissent même totalement, lorsque vous modifiez la requête. Vous pouvez ignorer ces erreurs pour l’instant.

Pour tester les exemples de données d’entrée, cliquez sur une de vos entrées, puis sélectionnez **Charger un exemple de données du fichier**.

![Éditeur de requête Stream Analytics - Charger les données d’échantillonnage à partir de la commande de fichier](media/stream-analytics-sample-data-input/stream-analytics-query-editor-upload.png)

Une fois que le téléchargement est terminé, cliquez sur **Tester** pour tester cette requête dans les exemples de données que vous venez de fournir.

![Bouton de test de l’éditeur de requête Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-test.png)

Si vous souhaitez enregistrer la sortie test pour une utilisation ultérieure, la sortie de votre requête s’affiche dans le navigateur avec un lien vers les résultats du téléchargement. Vous pouvez maintenant facilement et de manière itérative modifier votre requête et la tester à plusieurs reprises pour voir comment la sortie change.

![Exemple de sortie de l’éditeur de requête Stream Analytics](media/stream-analytics-sample-data-input/stream-analytics-query-editor-samples-output.png)

Dans l’illustration précédente, une deuxième sortie a été ajoutée. Elle est nommée **HighAvgTempOutput**.

Lorsque vous utilisez plusieurs sorties dans une requête, vous pouvez voir les résultats pour chaque sortie séparément et basculer facilement entre eux.

Une fois que vous êtes satisfait des résultats, vous pouvez enregistrer votre requête, lancer votre travail, vous installer confortablement et regarder la magie de Stream Analytics opérer.

## <a name="get-help"></a>Obtenir de l’aide

Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
