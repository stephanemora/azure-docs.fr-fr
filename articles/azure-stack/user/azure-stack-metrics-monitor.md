---
title: Consommer des données de surveillance à partir d’Azure Stack| Microsoft Docs
description: Découvrez les différentes façons de consommer de données de surveillance à partir d’Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: mabrigg
ms.openlocfilehash: 949dee06089ed381010a03ac2fae73aaf4a00c54
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889031"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Comment consommer des données de surveillance à partir d’Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Tout comme avec Azure Monitor dans Azure global, vous pouvez trouver des données de surveillance dans un emplacement unique avec le pipeline Azure Monitor. Cependant, toutes les données de surveillance trouvées dans Azure global ne sont pas disponibles dans Azure Stack. Cet article synthétise les différentes méthodes que vous pouvez utiliser pour ingérer des données de surveillance à partir du service par programmation.
 
## <a name="options-for-data-consumption"></a>Options pour la consommation de données

| Type de données | Catégorie | Services pris en charge | Méthodes d’accès |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Mesures au niveau de la plateforme Azure Monitor | Mesures | [Mesures prises en charge avec Azure Monitor sur Azure Stack](azure-stack-metrics-supported.md) | API REST |
| Mesures de SE invité Compute (calcul des performances, par exemple) | Mesures | Machines virtuelles Windows et Linux | Blob ou table de stockage :<br>Azure Diagnostics (Windows ou Linux) <br>Event Hub :<br>Diagnostics Windows Azure |
| Métriques de stockage | Mesures | Stockage Azure | Table de stockage :<br>Storage Analytics |
| Journal d’activité | Événements | Tous les services Azure | API REST :<br>API d’événements Azure Monitor |
| Journaux de SE invité Compute (par exemple, IIS, ETW et journaux syslog) | Événements | Machines virtuelles Windows et Linux | Blob ou table de stockage :<br>Azure Diagnostics (Windows ou Linux) <br>Event Hub :<br>Diagnostics Windows Azure |
| Journaux de stockage | Événements | Stockage Azure | Table de stockage :<br>Storage Analytics<br>`Vita: how about hybrid OMS/AppInsights, shall we mention?` |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [Azure Monitor sur Azure Stack](azure-stack-metrics-azure-data.md).