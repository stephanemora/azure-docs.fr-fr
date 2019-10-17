---
title: Mappages de régions d’Azure Monitor pour conteneurs
description: Cet article décrit les mappages de région pris en charge entre Azure Monitor pour conteneurs, l’espace de travail Log Analytics et les métriques personnalisées.
services: azure-monitor
ms.service: azure-monitor
ms.workload: infrastructure-services
author: mgoedtel
ms.author: magoedte
ms.date: 06/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ca77277c8d8d405b8fe81f612a8d7600d3c937ab
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388182"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Mappages de régions pris en charge par Azure Monitor pour conteneurs

 Lorsque vous activez Azure Monitor pour conteneurs, seules certaines régions sont prises en charge pour la liaison d’un espace de travail Log Analytics et d’un cluster AKS, ainsi que pour la collecte de métriques personnalisées envoyées à Azure Monitor.

## <a name="log-analytics-workspace-supported-mappings"></a>Mappages pris en charge sur l’espace de travail Log Analytics

Les ressources du cluster AKS ou l’espace de travail Log Analytics peuvent résider dans d’autres régions. Le tableau suivant présente nos mappages.

|**Région du cluster AKS** | **Région de l’espace de travail Log Analytics** |
|-----------------------|------------------------------------|
|**Afrique** | |
|SouthAfricaNorth |WestEurope |
|SouthAfricaWest |WestEurope |
|**Australie** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**Asie-Pacifique** | |
|EastAsia |EastAsia |
|AsieSudEst |AsieSudEst |
|**Brésil** | |
|BrazilSouth | SouthCentralUS |
|**Canada** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**Europe** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|RoyaumeUniSud |RoyaumeUniSud |
|UKWest |RoyaumeUniSud |
|WestEurope |WestEurope |
|**Inde** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**Japon** | |
|JaponEst |JaponEst |
|JapanWest |JaponEst |
|**Corée** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**États-Unis** | |
|CentralUS |CentralUS|
|USAEst |USAEst |
|USAEst2 |USAEst2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|
|Gouvernement américain - Virginie |Gouvernement américain - Virginie |

<sup>1</sup> En raison de restrictions de capacité, la région n’est pas disponible lors de la création de ressources. Cela comprend les espaces de travail Log Analytics. Toutefois, les ressources liées préexistantes dans la région doivent continuer à fonctionner.

## <a name="custom-metrics-supported-regions"></a>Régions prises en charge su les métriques personnalisées

La collecte de métriques des pods et nœuds de cluster Azure Kubernetes Service (AKS) est prise en charge dans le cadre de la publication en tant que métriques personnalisées dans les [régions Azure](../platform/metrics-custom-overview.md#supported-regions) suivantes.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer la surveillance de votre cluster AKS, consultez [Comment activer Azure Monitor pour les conteneurs](container-insights-onboard.md) pour comprendre les exigences et les méthodes disponibles pour activer la surveillance.  
