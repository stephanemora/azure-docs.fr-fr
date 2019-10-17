---
title: Suivre la progression des projets TDSP
description: Méthode de suivi de la progression d’un projet de science des données pour les responsables de groupe, chefs d’équipe et coordinateurs de projet spécialisés dans la science des données.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/26/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 28bd3e558294c000ba65a1c60fe227bbae7e82dd
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244051"
---
# <a name="track-the-progress-of-data-science-projects"></a>Suivre la progression de projets de science des données

Les responsables de groupe, chefs d’équipe et coordinateurs de projet spécialisés dans la science des données peuvent suivre la progression de leurs projets, notamment le travail qui a été fait et par qui ainsi que le travail restant. 

## <a name="azure-devops-dashboards"></a>Tableaux de bord DevOps Azure

Si vous utilisez Azure DevOps, vous pouvez générer des tableaux de bord pour suivre les activités et éléments de travail associés à un projet Agile donné. Pour plus d’informations sur les tableaux de bord, consultez [Tableaux de bord, rapports et widgets](/azure/devops/report/dashboards/).

Pour obtenir des instructions sur la création et la personnalisation de tableaux de bord et de widgets dans Azure DevOps, consultez les guides de démarrage rapide suivants :

- [Ajouter et gérer des tableaux de bord](/azure/devops/report/dashboards/dashboards)
- [Ajouter des widgets à un tableau de bord](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>Exemple de tableau de bord

Voici un exemple simple de tableau de bord qui effectue le suivi des activités des sprints d’un projet de science des données Agile, notamment le nombre de validations dans des dépôts associés. 

- La vignette **countdown** montre le nombre de jours restants dans le sprint actuel. 

- Les deux **vignettes de code** montrent le nombre de validations dans les deux dépôts de projet au cours des sept derniers jours. 

- **Work items for TDSP Customer Project** montre les résultats d’une requête pour tous les éléments de travail et leur état. 

- Un **diagramme de flux cumulatif** montre le nombre d’éléments de travail fermés et actifs.

- Le **graphique Burndown** montre le travail qu’il reste à faire par rapport au temps restant dans le sprint.

- Le **graphique Burnup** montre le travail accompli par rapport à la quantité totale de travail dans le sprint.

![tableau de bord](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>Étapes suivantes

[Guides d’application du processus TDSP (Team Data Science Process)](walkthroughs.md) liste les procédures pas à pas illustrant les étapes du processus pour des scénarios spécifiques avec des liens et des descriptions sous forme de miniatures. Les scénarios liés montrent également comment combiner des outils et des services cloud et locaux dans des workflows ou des pipelines pour créer des applications intelligentes. 
