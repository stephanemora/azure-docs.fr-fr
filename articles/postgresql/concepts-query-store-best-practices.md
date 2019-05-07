---
title: Meilleures pratiques de Query Store dans Azure Database pour PostgreSQL - serveur unique
description: Cet article décrit les meilleures pratiques pour le Store de la requête dans la base de données Azure pour PostgreSQL - serveur unique.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 798a7a3edbf11c8421848871d26ba55b5bada0b6
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65067250"
---
# <a name="best-practices-for-query-store"></a>Bonnes pratiques relatives au Magasin des requêtes

**S’applique à :** Azure Database pour PostgreSQL - monoserveur 9.6 et 10

Cet article décrit les bonnes pratiques relatives à l’utilisation du Magasin des requêtes dans Azure Database pour PostgreSQL.

## <a name="set-the-optimal-query-capture-mode"></a>Définir le mode de capture de requête optimal
Laissez le Magasin des requêtes capturer les données qui vous intéressent. 

|**pg_qs.query_capture_mode** | **Scénario**|
|---|---|
|_Tout_  |Analysez attentivement votre charge de travail concernant toutes les requêtes, leur fréquence d’exécution et d’autres statistiques. Identifiez les nouvelles requêtes dans votre charge de travail. Détecter si les requêtes ad hoc sont utilisées pour identifier les opportunités de paramétrage utilisateur ou automatique. _All_ s’accompagne du coût de la consommation accrue des ressources. |
|_Top_  |Concentrez votre attention sur les principales requêtes : celles émises par les clients.
|_Aucun_ |Vous avez déjà capturé un ensemble de requêtes et une fenêtre de temps que vous souhaitez examiner, et vous voulez éliminer les distractions que d’autres requêtes peuvent introduire. _None_ est adapté pour les environnements de test et d’évaluation. _None_ doit être utilisé avec précaution, car vous risquez de laisser passer l’opportunité de suivre et d’optimiser d’importantes nouvelles requêtes. Vous ne pouvez pas récupérer des données sur ces fenêtres de temps. |

Le Magasin des requêtes inclut également un magasin pour les statistiques d’attente. Il existe une requête en mode de capture supplémentaire qui régit les statistiques d’attente : **pgms_wait_sampling.query_capture_mode** peut être définie sur _none_ ou sur _all_. 

> [!NOTE] 
> **pg_qs.query_capture_mode** remplace **pgms_wait_sampling.query_capture_mode**. Si pg_qs.query_capture_mode a la valeur _none_, le paramètre pgms_wait_sampling.query_capture_mode n’a aucun effet. 


## <a name="keep-the-data-you-need"></a>Conserver les données dont vous avez besoin
Le paramètre **pg_qs.retention_period_in_days** spécifie, en jours, la période de conservation des données pour le Magasin des requêtes. Les données des requêtes et des statistiques plus anciennes sont supprimées. Par défaut, le Magasin des requêtes est configuré pour conserver les données pendant 7 jours. Évitez de conserver les données historiques que vous ne prévoyez pas d’utiliser. Augmentez la valeur si vous avez besoin de conserver les données plus longtemps.


## <a name="set-the-frequency-of-wait-stats-sampling"></a>Définir la fréquence d’échantillonnage des statistiques d’attente 
Le paramètre **pgms_wait_sampling.history_period** spécifie la fréquence (en millisecondes) d’échantillonnage des événements d’attente. Plus la période est courte, plus l’échantillonnage est fréquent. Des informations supplémentaires sont récupérées, mais cela s’accompagne d’une hausse du coût de la consommation des ressources. Prolongez cette période si le serveur est en cours de chargement ou si vous n’avez pas besoin d’autant de précision.


## <a name="get-quick-insights-into-query-store"></a>Obtenir des insights rapides dans le Magasin des requêtes
Vous pouvez utiliser [Query Performance Insight](concepts-query-performance-insight.md) dans le portail Azure pour obtenir des insights rapides des données du Magasin des requêtes. Les visualisations exposent les requêtes dont l’exécution est la plus longue et les événements d’attente les plus longs dans le temps.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment obtenir ou définir des paramètres à l’aide du [portail Azure](howto-configure-server-parameters-using-portal.md) ou de l’interface [Azure CLI](howto-configure-server-parameters-using-cli.md).