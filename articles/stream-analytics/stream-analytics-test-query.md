---
title: Tester une tâche Azure Stream Analytics avec des exemples de données
description: Test des requêtes dans les travaux Stream Analytics
keywords: Cet article explique comment utiliser le portail Azure pour tester une tâche Azure Stream Analytics, des exemples d’entrées et charger des exemples de données.
services: stream-analytics
author: jasonwhowell
ms.author: mamccrea
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/27/2018
ms.openlocfilehash: 139ad76826f3e67577beba0369f6d4425e242af9
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978347"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Tester une requête Stream Analytics avec des exemples de données

À l’aide d’Azure Stream Analytics, vous pouvez charger des exemples de données et tester des requêtes dans le portail Azure sans démarrer ni arrêter une tâche.

## <a name="upload-sample-data-and-test-the-query"></a>Charger des exemples de données et tester la requête

1. Connectez-vous au portail Azure. 

2. Localisez votre tâche Stream Analytics existante et sélectionnez-la.

3. Dans la page de la tâche Stream Analytics, sous le titre **Topologie de la tâche**, sélectionnez **Requête** pour ouvrir la fenêtre Éditeur de requête. 

4. Pour tester votre requête avec un exemple de données d’entrée, cliquez avec le bouton droit sur l’une de vos entrées.  Ensuite, sélectionnez **Charger un exemple de données du fichier**. Les données doivent être sérialisées au format JSON, CSV ou AVRO. L’exemple d’entrée doit être encodé en UTF-8 et non compressé. Seul le séparateur virgule (,) est pris en charge pour le test d’entrée CSV sur le portail.

    ![Test des requêtes dans l’éditeur de requête Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. Une fois le chargement terminé, cliquez sur **Tester** pour tester cette requête avec l’exemple de données que vous avez fourni.

    ![Exemple de données de test des requêtes dans l’éditeur de requête Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Si vous avez besoin de la sortie du test pour une utilisation ultérieure, la sortie de votre requête s’affiche dans le navigateur avec un lien de téléchargement des résultats. 

7. Modifiez de manière itérative votre requête et testez-la à nouveau pour voir comment la sortie change.

   ![Exemple de sortie de l’éditeur de requête Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Quand vous utilisez plusieurs sorties dans une requête, les résultats sont affichés dans des onglets distincts pour vous permettre de basculer facilement entre eux.

8. Après avoir vérifié les résultats affichés dans le navigateur, **enregistrez** votre requête. Ensuite, **démarrez** la tâche et laissez-la traiter les événements entrants.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
