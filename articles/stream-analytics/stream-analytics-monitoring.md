---
title: Présentation de la surveillance des tâches dans Azure Stream Analytics
description: Cet article décrit comment surveiller les travaux Azure Stream Analytics dans le Portail Azure.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 656f749fd2a930c51bfd7d1a99642fae87694846
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53096586"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Présentation de la surveillance des tâches Stream Analytics et des requêtes

## <a name="introduction-the-monitor-page"></a>Introduction : page de surveillance
Le portail Azure affiche les mesures de performances clés qui peuvent servir à surveiller et résoudre les problèmes affectant les performances de vos requêtes et de vos travaux. Pour voir ces métriques, accédez au travail Stream Analytics dont vous souhaitez consulter les métriques et affichez la section **Surveillance** dans la page Vue d’ensemble.  

![Lien vers la page de surveillance des tâches Azure Stream Analytics](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Une fenêtre s’affiche comme suit :

![Tableau de bord de surveillance des tâches Azure Stream Analytics](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Mesures disponibles pour Stream Analytics
| Métrique                 | Définition                               |
| ---------------------- | ---------------------------------------- |
| Événements d'entrée en backlog       | Nombre d’événements d’entrée qui sont en backlog. |
| Erreurs de conversion de données | Nombre d’événements de sortie qui n’ont pas pu être convertis dans le schéma de sortie attendu. |
| Événements d’entrée précoces       | Nombre d’événements reçus en avance. |
| Requêtes de fonction ayant échoué | Nombre d’appels à la fonction Azure Machine Learning ayant échoué (le cas échéant). |
| Événements de fonction        | Nombre d’événements envoyés à la fonction Azure Machine Learning (le cas échéant). |
| Requêtes de fonction      | Nombre d’appels à la fonction Azure Machine Learning (le cas échéant). |
| Erreurs de désérialisation d’entrée       | Nombre d’événements qui n’ont pas pu être désérialisés.  |
| Octets des événements d’entrée      | Quantité de données reçues par le travail Stream Analytics, en octets. Cela permet de valider que les événements sont envoyés à la source d’entrée. |
| Événements d’entrée           | Quantité de données reçues par le travail Stream Analytics, en nombre d’événements. Cela permet de valider que les événements sont envoyés à la source d’entrée. |
| Sources d'entrée reçues       | Nombre d’événements provenant d’une source d’entrée. |
| Événements d’entrée tardifs      | Nombre d’événements qui arrivent en retard de la source qui ont été supprimés ou dont l’horodatage a été réglé, en fonction de la configuration de la stratégie de classement des événements du paramètre de la plage de tolérance d’arrivée tardive. |
| Événements non ordonnés    | Nombre d’événements reçus dans le désordre qui ont été supprimés ou dont l’horodatage a été réglé, en fonction de la stratégie de classement des événements. Cela peut être affecté par la configuration du paramètre de la plage de tolérance pour les événements en désordre. |
| Événements de sortie          | Quantité de données envoyées par le travail Stream Analytics à la cible de sortie, en nombre d’événements. |
| Erreurs d’exécution         | Nombre total d’erreurs liées au traitement des requêtes (à l’exception des erreurs détectées lors de l’ingestion d’événements ou de la génération de résultats) |
| Utilisation de % d’unités de diffusion       | Utilisation des unités de diffusion affectées à une tâche à partir de l’onglet Mettre à l’échelle de la tâche. Si cet indicateur atteint 80 % ou plus, il est fort probable que le traitement des événements soit retardé ou arrêté. |
| Délai en filigrane       | Le délai en filigrane maximal sur toutes les partitions de toutes les sorties du travail. |

## <a name="customizing-monitoring-in-the-azure-portal"></a>Personnalisation de la surveillance dans le portail Azure
Vous pouvez régler le type de graphique, les mesures affichées et la période dans les paramètres Modifier le graphique. Pour plus d’informations, consultez [Personnalisation de la surveillance](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Graphique représentant le temps de surveillance des requêtes Stream Analytics](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Dernière sortie
Un autre point de données intéressant pour surveiller votre travail est l’heure de la dernière sortie, indiquée dans la page Vue d’ensemble.
Cette heure correspond à l’heure d’application (c’est-à-dire l’heure donnée via l’horodatage dans les données d’événement) de la dernière sortie de votre travail.

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

