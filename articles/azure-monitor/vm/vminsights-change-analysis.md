---
title: Analyse des modifications dans Insights de machine virtuelle
description: L’intégration d’Insights de machine virtuelle avec Analyse des changements d’application vous permet d’afficher toute modification apportée à une machine virtuelle susceptible d’avoir affecté ses performances.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 48f0f0e124040dc070bd5e31f956f75e759303d3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704002"
---
# <a name="change-analysis-in-vm-insights"></a>Analyse des modifications dans Insights de machine virtuelle
L’intégration d’insights de machine virtuelle avec [Analyse des changements d’application](../app/change-analysis.md) vous permet d’afficher toute modification apportée à une machine virtuelle susceptible d’avoir affecté ses performances.

## <a name="overview"></a>Vue d’ensemble
Supposez que vous avez une machine virtuelle qui s’exécute lentement, et que vous souhaitez vérifier si les modifications récemment apportées à sa configuration ont pu affecter ses performances. Vous visualisez les performances de la machine virtuelle à l’aide d’Insight de machine virtuelle et vous constatez une augmentation de l’utilisation de la mémoire au cours de la dernière heure. L’analyse des changements peut vous aider à déterminer si des modifications de configuration apportées à ce moment-là étaient à l’origine de cette augmentation.

Le service Analyse des changements d'application regroupe les modifications d’[Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md) ainsi que les modifications de propriétés imbriquées telles que les règles de sécurité réseau à partir d’Azure Resource Manager. 

## <a name="enabling-change-analysis"></a>Activation de l’analyse des changements
Pour intégrer l’analyse des changements dans Insights de machine virtuelle, vous devez inscrire le fournisseur de *Microsoft.ChangeAnalysis*. La première fois que vous lancez Insights de machine virtuelle ou Analyse des changements d’application dans le portail Azure, ce fournisseur de ressources est inscrit automatiquement pour vous. Analyse des changements d'application est un service gratuit qui n’entraîne aucune surcharge des performances sur les ressources.

## <a name="view-change-analysis"></a>Afficher l’analyse des changements
Vous pouvez accéder à l’analyse des changements à partir de l’onglet **Performances** ou **Carte** d’Insights de machine virtuelle en sélectionnant l’option **Modifier**. 

[![Enquêter sur les changements](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


Cliquez sur le bouton **Investigate changes** (Enquêter sur les changements) pour lancer la page Analyse des changements d'application filtrée pour la machine virtuelle. Vous pouvez examiner les modifications listées pour voir si l’une d’elles peut être à l’origine du problème. Si vous avez des doutes quant à une modification particulière, vous pouvez référencer la colonne **Change by** pour identifier la personne qui a effectué la modification.

[![Détails des modifications](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes
- Apprenez-en davantage sur l’[Analyse des changements d'application](../app/change-analysis.md).
- Apprenez-en davantage sur [Azure Resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md). 

