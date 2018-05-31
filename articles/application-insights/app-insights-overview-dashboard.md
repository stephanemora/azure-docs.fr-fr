---
title: Tableau de bord de vue d’ensemble d’Azure Application Insights | Microsoft Docs
description: Analysez les applications avec des fonctionnalités Azure Application Insights et de tableau de bord de vue d’ensemble.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mbullwin
ms.openlocfilehash: 0be54c47965c6a27c3506fd37a7bf67e4b2b7924
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356084"
---
# <a name="application-insights-overview-dashboard-preview"></a>Tableau de bord de vue d’ensemble Insights Overview (préversion)

Application Insights a toujours fourni un volet d’aperçu du résumé pour permettre l’évaluation en un coup d’œil rapide de l’intégrité et des performances de votre application. Le nouveau tableau de bord de vue d’ensemble fournit une expérience plus flexible et plus rapide.

## <a name="how-do-i-test-out-the-new-experience"></a>Comment tester la nouvelle expérience ?

 Dans Application Insights, sous _Vue d’ensemble_, sélectionnez _Please try new Overview before it becomes the default experience_ (Essayez la nouvelle vue d’ensemble avant qu’elle ne devienne l’expérience par défaut).

![Aperçu de la vue d’ensemble](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-01.png)

Cela permet de lancer le nouveau tableau de bord de vue d’ensemble par défaut :

![Volet d’aperçu de la vue d’ensemble](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-02.png)

## <a name="better-performance"></a>Meilleures performances

La sélection de plage horaire a été simplifiée en une interface en un clic simple.

![Période](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-03.png)

Les performances globales ont considérablement augmenté. Chaque valeur par défaut mettant à jour de façon dynamique la vignette de l’indicateur de performance clé est liée à la fonctionnalité Application Insights correspondante. Par exemple, la sélection des demandes ayant échoué lance le volet _Échecs_ :

![Échecs](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Tableau de bord de l’application

Le tableau de bord de l’application tire parti de la technologie de tableau de bord existante dans Azure pour fournir une vue unique entièrement personnalisable de l’intégrité des applications et de leurs performances.

Pour accéder au tableau de bord par défaut, sélectionnez _Tableau de bord de l’application_ dans le coin supérieur gauche.

![Vue du tableau de bord](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-05.png)

Si c’est la première fois que vous accédez au tableau de bord, cela lance une vue par défaut :

![Vue du tableau de bord](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-06.png)

Vous pouvez conserver la vue par défaut si vous le souhaitez, mais vous pouvez également ajouter et supprimer des éléments du tableau de bord pour mieux répondre aux besoins de votre équipe.

> [!NOTE]
> Tous les utilisateurs ayant accès à la ressource Application Insights partagent la même expérience de tableau de bord d’Application Insights. Si un utilisateur apporte des changements, l’affichage est modifié pour tous les utilisateurs.

Pour revenir à l’expérience de vue d’ensemble, sélectionnez simplement :

![Bouton de la vue d’ensemble](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-07.png)

## <a name="next-steps"></a>Étapes suivantes

- [Entonnoirs](usage-funnels.md)
- [Rétention](app-insights-usage-retention.md)
- [Flux d’utilisateurs](app-insights-usage-flows.md)
- [Tableaux de bord](app-insights-dashboards.md)
