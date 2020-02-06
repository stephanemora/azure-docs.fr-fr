---
title: Résoudre les problèmes liés aux requêtes Azure Stream Analytics
description: Cet article décrit des techniques pour résoudre les problèmes liés aux requêtes dans les travaux Azure Stream Analytics.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: bf0740bbdd4754aeba43e64f1076a1bea33cffc6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844415"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Résoudre les problèmes liés aux requêtes Azure Stream Analytics

Cet article décrit les problèmes courants liés au développement de requêtes Stream Analytics et les procédures à suivre pour les résoudre.

## <a name="query-is-not-producing-expected-output"></a>La requête ne produit pas la sortie attendue
1.  Examinez les erreurs en effectuant un test local :
    - Sur le Portail Azure, sous l’onglet **Requête**, sélectionnez **Test**. Utilisez les exemples de données téléchargés pour [tester la requête](stream-analytics-test-query.md). Examinez les éventuelles erreurs et tentez de les corriger.   
    - Vous pouvez également [tester votre requête localement](stream-analytics-live-data-local-testing.md) à l’aide des outils Azure Stream Analytics pour Visual Studio ou [Visual Studio Code](visual-studio-code-local-run-live-input.md). 

2.  [Déboguer des requêtes étape par étape localement à l’aide du diagramme de travail](debug-locally-using-job-diagram.md) dans les outils Azure Stream Analytics pour Visual Studio. Le diagramme de travail vise à montrer la façon dont les données circulent des sources d’entrée (Event Hub, IoT Hub, etc.), via plusieurs étapes de requête et finalement sortent dans les récepteurs. Chaque étape de la requête est mappée à un jeu de résultats temporaire défini dans le script à l’aide de l’instruction WITH. Vous pouvez afficher les données et les métriques à chaque étape de la requête dans chaque jeu de résultats intermédiaire pour trouver la source du problème.
    ![Aperçu des résultats dans le diagramme de travail](./media/debug-locally-using-job-diagram/preview-result.png)

3.  Si vous utilisez l’objet [**Timestamp By**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics), assurez-vous que les événements présentent des horodatages postérieurs à [l’heure de début du travail](stream-analytics-out-of-order-and-late-events.md).

4.  Éliminez les pièges les plus courants, comme par exemple :
    - Une clause [**WHERE**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) dans la requête a filtré l’ensemble des événements, ce qui empêche la génération des sorties.
    - Une fonction [**CAST**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) échoue, ce qui provoque l’échec du travail. Pour éviter les échecs de conversion de type, utilisez plutôt [**TRY_CAST**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics).
    - Lorsque vous utilisez des fonctions de fenêtre, attendez la durée totale de la fenêtre pour obtenir une sortie.
    - L’horodatage des événements est antérieur à l’heure de début du travail, provoquant l’abandon des événements.

5.  Vérifiez que les stratégies d’ordre des événements sont configurées comme prévu. Accédez à **Paramètres** et sélectionnez [**Ordre des événements**](stream-analytics-out-of-order-and-late-events.md). La stratégie n’est *pas* appliquée lorsque vous utilisez le bouton **Test** pour tester la requête. C’est une des différences entre le test en navigateur et l’exécution réelle du travail. 

6. Débogage à l’aide des journaux de diagnostic et d’audit :
    - Appliquez des filtres aux [journaux d’activité d’audit](../azure-resource-manager/resource-group-audit.md) pour identifier et déboguer les erreurs.
    - Utilisez les [journaux de diagnostic du travail](stream-analytics-job-diagnostic-logs.md) pour identifier et déboguer les erreurs.

## <a name="job-is-consuming-too-many-streaming-units"></a>Le travail consomme trop d’unités de streaming
Veillez à tirer parti de la parallélisation dans Azure Stream Analytics. Vous pouvez apprendre à [mettre à l’échelle des travaux Stream Analytics avec parallélisation des requêtes](stream-analytics-parallelization.md) en configurant des partitions d’entrée et en réglant la définition des requêtes Analytics.

## <a name="debug-queries-progressively"></a>Déboguer les requêtes progressivement

Lors du traitement de données en temps réel, savoir à quoi ressemblent les données au milieu de la requête peut être utile. Étant donné que les entrées ou les étapes d’un travail Azure Stream Analytics peuvent être lues plusieurs fois, vous pouvez écrire des instructions SELECT INTO supplémentaires. Cela génère des données intermédiaires dans le stockage et vous permet d’inspecter l’exactitude des données, tout comme le font les *variables espionnes* lorsque vous déboguez un programme.

L’exemple de requête suivant dans un travail Azure Stream Analytics a un flux d’entrée, deux entrées de données de référence et une sortie pour Stockage Table Azure. La requête joint les données du Event Hub et deux objets blob de référence pour obtenir les informations de nom et de catégorie :

![Exemple de requête SELECT INTO Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Notez que le travail est en cours d’exécution, mais aucun événement n’est produit dans la sortie. Dans la vignette **Surveillance**, illustrée ici, vous pouvez voir que l’entrée génère des données, mais vous ne savez pas quelle étape de **JOIN** a causé la suppression de tous les événements.

![Vignette Surveillance Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

Dans ce cas, vous pouvez ajouter quelques instructions SELECT INTO supplémentaires pour « journaliser » les résultats JOIN intermédiaires et les données lues à partir de l’entrée.

Dans cet exemple, nous avons ajouté deux nouvelles « sorties temporaires ». Il peut s’agir du récepteur que vous voulez. Ici, nous utilisons Stockage Azure comme exemple :

![Ajout d’instructions SELECT INTO supplémentaires à la requête Stream Analytics](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Vous pouvez ensuite réécrire la requête comme suit :

![Requête Stream Analytics SELECT INTO réécrite](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

À présent, relancez le travail et laissez-le s’exécuter pendant quelques minutes. Ensuite, interrogez temp1 et temp2 avec Visual Studio Cloud Explorer pour produire les tables suivantes :

**table temp1**
![Requête Stream Analytics table temp1 SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**table temp2**
![Requête Stream Analytics table temp2 SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Comme vous pouvez le voir, temp1 et temp2 présentent tous deux des données, et la colonne de nom est remplie correctement dans temp2. Toutefois, étant donné qu’il n’y a toujours aucune donnée dans la sortie, quelque chose ne va pas :

![Requête Stream Analytics table output1 SELECT INTO sans aucune donnée](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

En échantillonnant les données, vous pouvez être presque certain que le problème concerne le deuxième JOIN. Vous pouvez télécharger les données de référence à partir de l’objet blob et jeter un coup de œil :

![Requête Stream Analytics table ref SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Comme vous pouvez le voir, le format du GUID dans ces données de référence est différent du format de la colonne [from] dans temp2. C’est pourquoi les données ne sont pas arrivées dans output1 comme prévu.

Vous pouvez corriger le format de données, les télécharger pour référencer des objets blob, puis réessayer :

![Requête Stream Analytics table temp SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Cette fois, les données dans la sortie sont mises en forme et remplies comme prévu.

![Requête Stream Analytics table finale SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Obtenir de l’aide

Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d'Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
