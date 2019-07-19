---
title: Tester une tâche Azure Stream Analytics avec des exemples de données
description: Cet article explique comment utiliser le portail Azure pour tester une tâche Azure Stream Analytics, des exemples d’entrées et charger des exemples de données.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2019
ms.custom: seodec18
ms.openlocfilehash: 1a9caf83c6f4cd4ed15290afc872043c11234552
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508707"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Tester une requête Stream Analytics avec des exemples de données

À l’aide d’Azure Stream Analytics, vous pouvez échantillonner des données à partir d’entrées ou charger des exemples de données pour tester des requêtes dans le portail Azure sans démarrer ni arrêter une tâche.

## <a name="upload-or-sample-data-from-a-live-source-to-test-the-query"></a>Charger ou échantillonner des données à partir d’une source live pour tester la requête

1. Connectez-vous au portail Azure. 

2. Localisez votre tâche Stream Analytics existante et sélectionnez-la.

3. Dans la page de la tâche Stream Analytics, sous le titre **Topologie de la tâche**, sélectionnez **Requête** pour ouvrir la fenêtre Éditeur de requête. 

4. Pour tester votre requête, vous pouvez soit échantillonner des données à partir d’une entrée live, soit les charger à partir d’un fichier. Les données doivent être sérialisées au format JSON, CSV ou AVRO. L’exemple d’entrée doit être encodé en UTF-8 et non compressé. Seul le séparateur virgule (,) est pris en charge pour le test d’entrée CSV sur le portail.

    1. Avec des entrées live : cliquez avec le bouton droit sur l’une de vos entrées. Ensuite, sélectionnez **Échantillonner des données à partir de l’entrée**. Dans l’écran suivant, vous pouvez définir la durée de l’échantillonnage. L’échantillonnage d’événements à partir d’une source live permet de récupérer jusqu’à 1 000 événements (ou 1 Mo, selon ce qui survient le premier), donc les données échantillonnées peuvent ne pas représenter l’intégralité de l’intervalle de temps indiqué.

    1. Avec un fichier : cliquez avec le bouton droit sur l’une de vos entrées. Ensuite, sélectionnez **Charger un exemple de données du fichier**. 

    ![Test des requêtes dans l’éditeur de requête Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. Une fois l’échantillonnage ou le chargement terminé, cliquez sur **Tester** pour tester cette requête en la comparant avec l’exemple de données que vous avez fourni.

    ![Exemple de données de test des requêtes dans l’éditeur de requête Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Si vous avez besoin de la sortie du test pour une utilisation ultérieure, la sortie de votre requête s’affiche dans le navigateur avec un lien de téléchargement des résultats. 

7. Modifiez de manière itérative votre requête et testez-la à nouveau pour voir comment la sortie change.

   ![Exemple de sortie de l’éditeur de requête Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Quand vous utilisez plusieurs sorties dans une requête, les résultats sont affichés dans des onglets distincts pour vous permettre de basculer facilement entre eux.

8. Après avoir vérifié les résultats affichés dans le navigateur, **enregistrez** votre requête. Ensuite, **démarrez** la tâche et laissez-la traiter les événements entrants.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
