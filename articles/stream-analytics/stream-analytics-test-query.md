---
title: Tester une tâche Azure Stream Analytics avec des exemples de données
description: Cet article explique comment utiliser le portail Azure pour tester une tâche Azure Stream Analytics, des exemples d’entrées et charger des exemples de données.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/23/2019
ms.custom: seodec18
ms.openlocfilehash: 85d32e96335a9fffa874f1f370f72883ff420df0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369862"
---
# <a name="test-an-azure-stream-analytics-job-with-sample-data"></a>Tester une tâche Azure Stream Analytics avec des exemples de données

Dans Azure Stream Analytics, vous pouvez tester votre requête sans démarrer ou arrêter votre travail. Vous pouvez tester des requêtes sur des données entrantes à partir de votre récepteur d’entrée ou des exemples de données chargés à partir d’un fichier local sur le portail Azure. Vous pouvez également tester les requêtes localement à partir de vos exemples de données locaux ou de vos données actives dans [Visual Studio](stream-analytics-live-data-local-testing.md) et [Visual Studio Code](visual-studio-code-local-run-live-input.md).

## <a name="sample-incoming-data-from-input"></a>Exemple de données entrantes à partir d’une entrée

Azure Stream Analytics récupère automatiquement les événements à partir de votre entrée de streaming. Vous pouvez exécuter des requêtes sur l’exemple par défaut ou définir un délai d’exécution spécifique pour l’exemple.

1. Connectez-vous au portail Azure.

2. Localisez votre tâche Stream Analytics existante et sélectionnez-la.

3. Dans la page de la tâche Stream Analytics, sous le titre **Topologie de la tâche**, sélectionnez **Requête** pour ouvrir la fenêtre Éditeur de requête. 

4. Pour afficher un exemple de liste d’événements entrants, sélectionnez l’icône d’entrée avec un fichier et les exemples d’événements s’affichent automatiquement dans **l’aperçu d’entrée**.

   a. Le type de sérialisation de vos données est automatiquement détecté (JSON ou CSV). Vous pouvez le modifier manuellement en JSON, CSV, AVRO, en modifiant l’option dans le menu déroulant.
    
   b. Utilisez le sélecteur pour afficher vos données au format **Tableau** ou **Brut**.
    
   c. Si les données qui apparaissent ne sont pas à jour, sélectionnez **Actualiser** pour afficher les événements les plus récents.

   Voici un exemple de données au **format tableau** :

   ![Exemple d’entrée Azure Stream Analytics au format tableau](./media/stream-analytics-test-query/asa-sample-table.png)

   Voici un exemple de données au **format brut** :

   ![Exemple d’entrée Azure Stream Analytics au format brut](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Pour tester votre requête avec des données entrantes, sélectionnez **Tester la requête**. Les résultats s’affichent dans l’onglet **Résultats du test**. Vous pouvez également sélectionner **Télécharger les résultats** pour télécharger les résultats.

   ![Exemple de résultats de test de requête Azure Stream Analytics](./media/stream-analytics-test-query/asa-test-query.png)

6. Pour tester votre requête sur un intervalle de temps spécifique d’événements entrants, sélectionnez **Sélectionner un intervalle de temps**.
   
   ![Intervalle de temps Azure Stream Analytics pour un exemple d’événements entrants](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Définissez l’intervalle de temps des événements que vous souhaitez utiliser pour tester votre requête et sélectionnez **Exemple**. Dans cet intervalle de temps, vous pouvez récupérer jusqu’à 1 000 événements ou 1 Mo, selon ce qui se produit en premier.

   ![Intervalle de temps Azure Stream Analytics défini pour un exemple d’événements entrants](./media/stream-analytics-test-query/asa-set-time-range.png)

8. Une fois les événements échantillonnés pour l’intervalle de temps sélectionné, ils s’affichent dans l’onglet **Aperçu d’entrée**.

   ![Afficher les résultats du test Azure Stream Analytics](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Sélectionnez **Réinitialiser** pour voir l’exemple de liste d’événements entrants. Si vous sélectionnez **Réinitialiser**, votre sélection d’intervalle de temps sera perdue. Sélectionnez **Tester la requête** pour tester votre requête et passez en revue les résultats dans l’onglet **Résultats du test**.

10. Lorsque vous apportez des modifications à votre requête, sélectionnez **Enregistrer la requête** pour tester la nouvelle logique de requête. Cela vous permet de modifier de manière itérative votre requête et de la tester à nouveau pour voir comment la sortie change.

11. Après avoir vérifié les résultats affichés dans le navigateur, vous êtes prêt à **Démarrer** le travail.

## <a name="upload-sample-data-from-a-local-file"></a>Charger un exemple de données à partir d’un fichier local

Au lieu d’utiliser des données actives, vous pouvez utiliser des exemples de données à partir d’un fichier local pour tester votre requête Azure Stream Analytics.

1. Connectez-vous au portail Azure.
   
2. Localisez votre tâche Stream Analytics existante et sélectionnez-la.

3. Dans la page de la tâche Stream Analytics, sous le titre **Topologie de la tâche**, sélectionnez **Requête** pour ouvrir la fenêtre Éditeur de requête.

4. Pour tester votre requête avec un fichier local, sélectionnez **Charger un exemple d’entrée** dans l’onglet **Aperçu d’entrée**. 

   ![Charger un exemple de fichier Azure Stream Analytics](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Chargez votre fichier local pour tester la requête. Vous pouvez uniquement charger des fichiers au format JSON, CSV ou AVRO. Sélectionnez **OK**.

   ![Charger un exemple de fichier Azure Stream Analytics](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Dès que vous chargez le fichier, vous pouvez également voir le contenu du fichier sous la forme d’un tableau ou au format brut. Si vous sélectionnez **Réinitialiser**, les exemples de données retournent vers les données d’entrée entrantes expliquées dans la section précédente. Vous pouvez charger n’importe quel autre fichier pour tester la requête à tout moment.

7. Sélectionnez **Tester la requête** pour tester votre requête par rapport à l’exemple de fichier chargé.

8. Les résultats du test sont affichés en fonction de votre requête. Vous pouvez modifier votre requête et sélectionner **Enregistrer la requête** pour tester la nouvelle logique de requête. Cela vous permet de modifier de manière itérative votre requête et de la tester à nouveau pour voir comment la sortie change.

9. Lorsque vous utilisez plusieurs sorties dans la requête, les résultats sont affichés en fonction de la sortie sélectionnée. 

   ![Sortie sélectionnée Azure Stream Analytics](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. Après avoir vérifié les résultats affichés dans le navigateur, vous pouvez **Démarrer** le travail.

## <a name="next-steps"></a>Étapes suivantes

* [Références sur le langage des requêtes d'Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

* [Exemples de requête pour les modes d’utilisation courants dans Stream Analytics](stream-analytics-stream-analytics-query-patterns.md)

* [Comprendre les entrées d’Azure Stream Analytics](stream-analytics-add-inputs.md)

* [Comprendre les sorties d’Azure Stream Analytics](stream-analytics-define-outputs.md)
