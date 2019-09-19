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
ms.openlocfilehash: 5d0c43fbcc1c59c3281f412aad96a3942a5c79b1
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "70392906"
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

**Portail Azure**

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
| Régions ayant atteint la capacité maximale | Centre-USA Ouest | Vous ne pouvez actuellement pas créer un nouvel espace de travail dans cette région dans la mesure où elle a atteint sa limite de capacité temporaire. Cette limite devrait être traitée d’ici à la fin du mois d’octobre 2019. |
| Exportation de données | Actuellement non disponible | Utilisez Azure Function ou Logic App pour agréger et exporter des données. | 

>[!NOTE]
>En fonction de la durée pendant laquelle vous utilisez Log Analytics, vous pouvez avoir accès aux niveaux de tarification hérités. En savoir plus sur les [niveaux tarifaires hérités de Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers). 