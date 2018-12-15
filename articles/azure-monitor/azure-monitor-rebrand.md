---
title: Repositionnement d’Azure Monitor | Microsoft Docs
description: Décrit les modifications récemment apportées aux services de gestion Azure en matière de marque et de nom.
author: bwren
manager: carmonm
editor: tysonn
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: bwren
ms.openlocfilehash: 68986b3d60566ee190a92c6ccf8cda767824350f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000152"
---
# <a name="azure-monitor-branding-changes"></a>Changements de la marque Azure Monitor
Cet article décrit les modifications récemment apportées aux services de gestion Azure en matière de marque et de nom. 

## <a name="consolidation-of-monitoring-services-into-azure-monitor"></a>Consolidation des services de surveillance dans Azure Monitor
Log Analytics et Application Insights ont été consolidés dans Azure Monitor afin de fournir une expérience intégrée unique pour la surveillance des ressources Azure et des environnements hybrides. Aucune fonctionnalité n’a été supprimée dans ces services, et les utilisateurs peuvent effectuer les mêmes scénarios qu’auparavant sans perte ni compromis dans les fonctionnalités.

La documentation de chacun de ces services a été fusionnée dans un même ensemble de contenu pour Azure Monitor. Cela permet d’aider le lecteur à trouver tout le contenu d’un scénario de surveillance spécifique à un même emplacement au lieu de consulter plusieurs ensembles de contenu. L’intégration du contenu se poursuivra en parallèle de l’évolution du service consolidé.

## <a name="log-analytics-redefinition"></a>Redéfinition de Log Analytics
Log Analytics joue un rôle central dans la gestion Azure : il collecte les données de télémétrie et d’autres données à partir de diverses sources, et fournit un langage de requêtes et un moteur d’analyse qui vous donnent des informations sur le fonctionnement de vos applications et de vos ressources. Il continuera à remplir ce rôle critique en tant que fonctionnalité d’Azure Monitor. D’autres fonctionnalités considérées comme faisant partie de Log Analytics, comme les agents et les solutions, seront repositionnées en tant que fonctionnalités d’Azure Monitor. Ces fonctionnalités n’ont pas changé, à part quelques améliorations potentielles de leur expérience dans le portail Azure.

## <a name="retirement-of-operations-management-suite-brand"></a>Retrait de la marque Operations Management Suite
Operations Management Suite (OMS) était un regroupement des services de gestion Azure suivants pour la gestion des licences :

- Application Insights
- Azure Automation
- Sauvegarde Azure
- Log Analytics
- Site Recovery

[Une nouvelle tarification a été introduite pour ces services](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), et le regroupement OMS n’est plus disponible pour les nouveaux clients. Aucun des services qui faisaient partie d’OMS n’a changé, à l’exception de la consolidation dans Azure Monitor décrite ci-dessus. 



## <a name="next-steps"></a>Étapes suivantes

- Consultez la [vue d’ensemble d’Azure Monitor](overview.md), qui décrit ses différents composants et fonctionnalités.
- En savoir plus sur la [transition du portail OMS](../azure-monitor/platform/oms-portal-transition.md).