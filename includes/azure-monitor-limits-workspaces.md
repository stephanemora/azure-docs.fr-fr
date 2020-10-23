---
title: Fichier include
description: Fichier include
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: e206c12a85cfbaed3297f2a44bf0a5d694c2d170
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92323478"
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

| Category | Limite | Commentaires |
|:---|:---|:---|
| Nombre maximum d’enregistrements retournés par une requête de journal | 10 000 | Réduisez les résultats à l’aide d’une étendue de requête, d’un intervalle de temps et de filtres dans la requête. |


**API de collecte de données**

| Category | Limite | Commentaires |
|:---|:---|:---|
| Taille maximale d’une publication | 30 Mo | Fractionner les volumes plus importants en plusieurs publications. |
| Taille maximale des valeurs de champ  | 32 Ko | Les champs de plus de 32 Ko de champs sont tronqués. |

**API Recherche**

| Category | Limite | Commentaires |
|:---|:---|:---|
| Nombre maximal d’enregistrements retournés dans une requête | 500 000 | |
| Taille maximale des données retournées | 64 000 000 octets (~ 61 Mio)| |
| Durée maximale d’exécution de requête | 10 minutes | Consultez [Délais d’expiration](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) pour plus d’informations.  |
| Taux maximum de requêtes | 200 requêtes par 30 secondes par utilisateur Azure AD ou adresse IP du client | Consultez [Limites de taux](https://dev.loganalytics.io/documentation/Using-the-API/Limits) pour plus d’informations. |

**Limites d’espace de travail général**

| Category | Limite | Commentaires |
|:---|:---|:---|
| Nombre maximum de colonnes dans une table         | 500 | |
| Nombre maximum de caractères pour le nom de colonne | 500 | |
| Exportation de données | Actuellement non disponible | Utilisez Azure Function ou Logic App pour agréger et exporter des données. | 

**<a name="data-ingestion-volume-rate">Débit d’ingestion de données</a>**

Azure Monitor est un service de données à grande échelle servant des milliers de clients envoyant des téraoctets de données chaque mois à un rythme croissant. La limite du débit de volume vise à isoler les clients Azure Monitor des pics d’ingestion soudains dans un environnement multilocataire. Un seuil de débit de volume d’ingestion par défaut de 500 Mo (compressé) est défini dans les espaces de travail, ce qui se traduit par **6 Go/min** non compressé -- la taille réelle peut varier entre les types de données en fonction de la longueur du journal et du taux de compression. La limite du débit de volume s’applique à toutes les données ingérées, qu’elles soient envoyées à partir de ressources Azure en utilisant les [paramètres de diagnostic](../articles/azure-monitor/platform/diagnostic-settings.md),de l’[API du collecteur de données](../articles/azure-monitor/platform/data-collector-api.md) ou d’agents.

Quand vous envoyez des données vers un espace de travail à un débit supérieur à 80 % du seuil configuré dans votre espace de travail, un événement est envoyé au tableau *Opération* de votre espace de travail toutes les 6 heures pendant que le seuil continue d’être dépassé. Quand le débit de volume ingéré est plus élevé que le seuil, des données sont supprimées et un événement est envoyé toutes les 6 heures au tableau *Opération* de votre espace de travail pendant que le seuil continue d’être dépassé. Si votre débit de volume d’ingestion continue de dépasser le seuil ou si vous pensez l’atteindre bientôt, vous pouvez demander de l’augmenter en effectuant une demande de support. 

Consultez [Superviser l’intégrité de l’espace de travail Log Analytics dans Azure Monitor](../articles/azure-monitor/platform/monitor-workspace.md) pour créer des règles d’alerte qui vous informeront de manière proactive si vous atteignez les limites d’ingestion.

>[!NOTE]
>En fonction de la durée pendant laquelle vous utilisez Log Analytics, vous pouvez avoir accès aux niveaux de tarification hérités. En savoir plus sur les [niveaux tarifaires hérités de Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 
