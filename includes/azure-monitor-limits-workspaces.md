---
title: Fichier Include
description: Fichier Include
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 49dc69eb73bd19509b5e14c5a4976cfa7208545a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392325"
---
**Volume et rétention de collecte de données** 

| Niveau | Limite par jour | Conservation des données | Commentaire |
|:---|:---|:---|:---|
| Niveau tarifaire actuel par Go<br>(introduit en avril 2018) | Aucune limite | 30 à 730 jours | La conservation des données au-delà de 31 jours est disponible contre des frais supplémentaires. En savoir plus sur la tarification Azure Monitor. |
| Niveaux gratuits hérités<br>(introduit en avril 2016) | 500 Mo | 7 jours | Lorsque votre espace de travail atteint la limite de 500 Mo par jour, l’ingestion de données s’interrompt et reprend au début de la journée suivante. Les journées sont basées sur l’heure UTC. Notez que les données collectées par Azure Security Center ne sont pas incluses dans cette limite de 500 Mo par jour et continuent à être collectées au-delà de cette limite.  |
| Niveau autonome par Go hérité<br>(introduit en avril 2016) | Aucune limite | 30 à 730 jours | La conservation des données au-delà de 31 jours est disponible contre des frais supplémentaires. En savoir plus sur la tarification Azure Monitor. |
| Par nœud hérité (OMS)<br>(introduit en avril 2016) | Aucune limite | 30 à 730 jours | La conservation des données au-delà de 31 jours est disponible contre des frais supplémentaires. En savoir plus sur la tarification Azure Monitor. |
| Niveau standard hérité | Aucune limite | 30 jours  | La rétention ne peut pas être ajustée |
| Niveau Premium hérité | Aucune limite | 365 jours  | La rétention ne peut pas être ajustée |

**Nombre d’espaces de travail par abonnement.**

| Niveau tarifaire    | Limite d’espace de travail | Commentaires
|:---|:---|:---|
| Niveau Gratuit  | 10 | Cette limite ne peut pas être augmentée. |
| Tous les autres niveaux | Aucune limite | Vous êtes limité par le nombre de ressources au sein d’un groupe de ressources et le nombre de groupes de ressources par abonnement. |

**Azure portal**

| Category | limites | Commentaires |
|:---|:---|:---|
| Nombre maximum d’enregistrements retournés par une requête de journal | 10 000 | Réduisez les résultats à l’aide d’une étendue de requête, d’un intervalle de temps et de filtres dans la requête. |


**API de collecte de données**

| Category | limites | Commentaires |
|:---|:---|:---|
| Taille maximale d’une publication | 30 Mo | Fractionner les volumes plus importants en plusieurs publications. |
| Taille maximale des valeurs de champ  | 32 Ko | Les champs de plus de 32 Ko de champs sont tronqués. |

**API Recherche**

| Category | limites | Commentaires |
|:---|:---|:---|
| Nombre maximal d’enregistrements retournés dans une requête | 500 000 | |
| Taille maximale des données retournées | 64 000 000 octets (~ 61 Mio)| |
| Durée maximale d’exécution de requête | 10 minutes | Consultez [Délais d’expiration](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) pour plus d’informations.  |
| Taux maximum de requêtes | 200 requêtes par 30 secondes par utilisateur AAD ou adresse IP du client | Consultez [Limites de taux](https://dev.loganalytics.io/documentation/Using-the-API/Limits) pour plus d’informations. |

**Limites d’espace de travail général**

| Category | limites | Commentaires |
|:---|:---|:---|
| Nombre maximum de colonnes dans une table         | 500 | |
| Nombre maximum de caractères pour le nom de colonne | 500 | |
| Exportation de données | Actuellement non disponible | Utilisez Azure Function ou Logic App pour agréger et exporter des données. | 

**Débit d’ingestion de données**


Azure Monitor est un service de données à grande échelle servant des milliers de clients envoyant des téraoctets de données chaque mois à un rythme croissant. La limite par défaut du débit d’ingestion de données envoyées à partir de ressources Azure à l’aide des [paramètres de diagnostic](../articles/azure-monitor/platform/diagnostic-settings.md) est d’environ **6 Go/min** par espace de travail. Il s’agit d’une valeur approximative dans la mesure où la taille réelle peut varier d’un type de données à l’autre en fonction de la longueur du journal et de son taux de compression. Cette limite ne s’applique pas aux données envoyées à partir d’agents ou de l’[API de collecteur de données](../articles/azure-monitor/platform/data-collector-api.md).

Si vous envoyez des données vers un espace de travail unique à un débit supérieur, certaines données sont supprimées et un événement est envoyé toutes les 6 heures à la table *Operation* de votre espace de travail tant que le seuil est dépassé. Si votre volume d’ingestion continue de dépasser la limite du débit ou si vous pensez l’atteindre bientôt, vous pouvez demander une augmentation de votre espace de travail en effectuant une demande de support.
 
Pour être averti de la survenue d’un tel événement dans votre espace de travail, créez une [règle d’alerte de journal](../articles/azure-monitor/platform/alerts-log.md) à l’aide de la requête suivante, où la logique d’alerte est basée sur le nombre de résultats supérieurs à zéro.

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>En fonction de la durée pendant laquelle vous utilisez Log Analytics, vous pouvez avoir accès aux niveaux de tarification hérités. En savoir plus sur les [niveaux tarifaires hérités de Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 