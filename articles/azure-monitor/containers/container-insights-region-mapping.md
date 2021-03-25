---
title: Mappages de régions Container Insights
description: Décrit les mappages de région pris en charge entre Container Insights, l’espace de travail Log Analytics et les métriques personnalisées.
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: f9e910b1352109608becb82609e85e26d27d2cd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728873"
---
# <a name="region-mappings-supported-by-container-insights"></a>Mappages de régions pris en charge par Container Insights

 Lorsque vous activez Container Insights, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un cluster AKS, ainsi que la collecte de métriques personnalisées soumises à Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mappages pris en charge sur l’espace de travail Log Analytics

Les régions AKS prises en charge sont répertoriées dans [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service). L’espace de travail Log Analytics doit se trouver dans la même région, à l’exception des régions répertoriées dans le tableau suivant. Pour les mises à jour, consultez les [notes de publication AKS](https://github.com/Azure/AKS/releases).


|**Région du cluster AKS** | **Région de l’espace de travail Log Analytics** |
|-----------------------|------------------------------------|
|**Afrique** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Australie** | |
|AustraliaCentral2 |AustraliaCentral |
|**Brésil** | |
|BrazilSouth | SouthCentralUS |
|**Canada** ||
|CanadaEast |CanadaCentral |
|**Europe** | |
|FranceSouth |FranceCentral |
|UKWest |RoyaumeUniSud |
|**Inde** | |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japon** | |
|JapanWest |JaponEst |
|**Corée** | |
|KoreaSouth |KoreaCentral |
|**États-Unis** | |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|


<sup>1</sup> En raison de restrictions de capacité, la région n’est pas disponible lors de la création de ressources. Cela comprend les espaces de travail Log Analytics. Toutefois, les ressources liées préexistantes dans la région doivent continuer à fonctionner.

## <a name="custom-metrics-supported-regions"></a>Régions prises en charge su les métriques personnalisées

La collecte de métriques des pods et nœuds de cluster Azure Kubernetes Service (AKS) est prise en charge dans le cadre de la publication en tant que métriques personnalisées dans les [régions Azure](../essentials/metrics-custom-overview.md#supported-regions) suivantes.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer la surveillance de votre cluster AKS, consultez [Comment activer Container Insights](container-insights-onboard.md) afin de comprendre les exigences et les méthodes disponibles pour activer la surveillance.  
