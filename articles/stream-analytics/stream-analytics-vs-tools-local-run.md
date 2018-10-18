---
title: Tester des requêtes Azure Stream Analytics localement avec Visual Studio
description: Cet article décrit comment tester des requêtes localement avec Azure Stream Analytics Tools pour Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: cc4372a8a8a8f39bf4f77f637b459c5281b8852d
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299523"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Tester des requêtes Stream Analytics localement avec Visual Studio

Vous pouvez utiliser Azure Stream Analytics Tools pour Visual Studio pour tester vos tâches Stream Analytics localement à l’aide d’exemples de données.

Utilisez ce [Démarrage rapide](stream-analytics-quick-create-vs.md) pour apprendre à créer une tâche Stream Analytics à l’aide de Visual Studio.

## <a name="test-your-query"></a>Tester votre requête

Dans votre projet Azure Stream Analytics, double-cliquez sur **Script.asaql** pour ouvrir le script dans le l’éditeur. Vous pouvez compiler la requête pour voir si elle contient des erreurs de syntaxe. L’éditeur de requête prend en charge IntelliSense, la coloration syntaxique et le marquage d’erreurs.

![Éditeur de requête](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Ajouter une entrée locale

Pour valider votre requête par rapport aux données statiques locales, cliquez avec le bouton droit sur l’entrée, et sélectionnez **Ajouter une entrée locale**.
   
![Ajouter une entrée locale](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
Dans la fenêtre indépendante, sélectionnez l’exemple de données dans votre chemin local, puis **Enregistrer**.
   
![Ajouter une entrée locale](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
Un fichier nommé **local_EntryStream.json** est automatiquement ajouté à votre dossier d’entrées.
   
![Liste des fichiers du dossier d’entrées locales](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
Dans l’éditeur de requête, sélectionnez **Exécution locale**. Ou vous pouvez appuyer sur F5.
   
![Exécution locale](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
La sortie peut être affichée dans un format de tableau directement à partir de Visual Studio.

![Sortie au format tableau](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

Vous pouvez trouver le chemin de sortie à partir de la sortie de console. Appuyez sur n’importe quelle touche pour ouvrir le dossier des résultats.
   
![Exécution locale](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Vérifiez les résultats dans le dossier local.
   
![Résultat du dossier local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Exemple d’entrée
Vous pouvez également collecter des exemples de données d’entrée à partir de vos sources d’entrée dans un fichier local. Cliquez avec le bouton droit sur le fichier de configuration d’entrée et sélectionnez **Exemple de données**. 

![Exemple de données](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Vous pouvez échantillonner uniquement une diffusion en continu de données à partir d’Event Hubs ou d’IoT Hubs. Les autres sources d’entrée ne sont pas prises en charge. Dans la boîte de dialogue contextuelle, entrez le chemin local utilisé pour enregistrer les exemples de données et sélectionnez **Échantillon**.

![Configuration des exemples de données](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Vous pouvez voir la progression de l’opération dans la fenêtre **Sortie**. 

![Sortie des exemples de données](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser Visual Studio pour afficher les tâches Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Démarrage rapide : créer un travail Stream Analytics à l’aide de Visual Studio](stream-analytics-quick-create-vs.md)
* [Tutoriel : Déployer un travail Azure Stream Analytics avec CI/CD à l’aide d’Azure DevOps](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Intégrer et développer en continu avec les outils Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)