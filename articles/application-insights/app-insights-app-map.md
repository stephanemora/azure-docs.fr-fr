---
title: Mise en correspondance d’applications dans Azure Application Insights | Microsoft Docs
description: Surveiller des topologies d’applications complexes avec la mise en correspondance d’applications
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 9b39eef5accec4764f61ab31dd894d368242ee3d
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094648"
---
# <a name="application-map-triage-distributed-applications"></a>Mise en correspondance d’applications : trier des applications distribuées

La mise en correspondance d’applications vous permet d’identifier les baisses de performances ou les défaillances sur l’ensemble des composants de votre application distribuée. Chaque nœud de cette mise en correspondance représente un composant d’application ou ses dépendances, avec des indicateurs de performance clés d’intégrité et l’état des alertes. Vous pouvez cliquer sur n’importe quel composant pour obtenir des diagnostics plus détaillés, par exemple des événements Application Insights. Si votre application utilise les services Azure, vous pouvez également accéder aux diagnostics Azure, et notamment aux recommandations de SQL Database Advisor.

## <a name="what-is-a-component"></a>Qu’est un composant ?

Les composants sont des parties pouvant être déployées de manière indépendante de votre application distribuée/de microservices. Les développeurs et équipes d’opérations disposent d’une visibilité au niveau du code ou d’un accès à la télémétrie générée par ces composants d’application. 

* Les composants sont différents des dépendances externes « observées » telles que SQL, EventHub, etc., auxquelles votre équipe/organisation peut ne pas avoir accès (code ou télémétrie).
* Les composants s’exécutent sur un nombre quelconque d’instances de serveur/rôle/conteneur.
* Les composants peuvent être des clés d’instrumentation Application Insights distinctes (même si les abonnements sont différents) ou des rôles différents rapportant à une clé d’instrumentation Application Insights unique. L’aperçu de mise en correspondance montre les composants, quelle que soit leur configuration.

## <a name="composite-application-map"></a>Cartographie d’application composite

Vous pouvez afficher la topologie complète des applications sur plusieurs niveaux des composants d’application associés. Les composants peuvent représenter différentes ressources Application Insights, ou différents rôles d’une seule ressource. La mise en correspondance d’applications trouve les composants en suivant les appels de dépendance HTTP effectués entre les serveurs sur lesquels le kit SDK Application Insights est installé. 

Cette expérience démarre la découverte progressive des composants. Lorsque vous chargez la cartographie d’application pour la première fois, un ensemble de requêtes est déclenché pour découvrir les composants liés à ce composant. Un bouton dans le coin supérieur gauche permet de mettre à jour le nombre de composants de votre application dès qu’ils sont détectés. 

Lorsque vous cliquez sur « Update map components » (Mettre à jour les composants de cartographie), la carte est actualisée avec tous les composants détectés.

Si tous les composants sont des rôles au sein d’une seule ressource Application Insights, cette étape de découverte n’est pas requise. La charge initiale pour une telle application aura tous ses composants.

![Capture d’écran de la cartographie d’application](media/app-insights-app-map/001.png)

L’un des principaux objectifs de cette expérience est de permettre de visualiser des topologies complexes incluant des centaines de composants.

Cliquez sur n’importe quel composant pour afficher des informations connexes ainsi que les performances et l’expérience de triage de défaillance de ce composant.

![Menu volant](media/app-insights-app-map/application-map-001.png)

### <a name="investigate-failures"></a>Examiner les échecs

Pour lancer le panneau Échecs, sélectionnez **Examiner les échecs**.

![Capture d’écran du bouton Examiner les échecs](media/app-insights-app-map/investigate-failures.png)

![Capture d’écran du panneau Échecs](media/app-insights-app-map/failures.png)

### <a name="investigate-performance"></a>Examiner les performances

Pour résoudre les problèmes de performances, sélectionnez **Examiner les performances**.

![Capture d’écran du bouton Examiner les performances](media/app-insights-app-map/investigate-performance.png)

![Capture d’écran du panneau Performances](media/app-insights-app-map/performance.png)

### <a name="go-to-details"></a>Accéder aux détails

Pour explorer l’expérience de transaction de bout en bout pouvant présenter des vues générées au niveau de la pile des appels, sélectionnez **Accéder aux détails**.

![Capture d’écran du bouton Accéder aux détails](media/app-insights-app-map/go-to-details.png)

![Capture d’écran des détails de transaction de bout en bout](media/app-insights-app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Afficher dans Analytics

Pour interroger et analyser vos données d’application de manière plus approfondie, cliquez sur **Afficher dans Analytics**.

![Capture d’écran du bouton Afficher dans Analytics](media/app-insights-app-map/view-in-analytics.png)

![Capture d’écran de l’expérience d’analyse](media/app-insights-app-map/analytics.png)

### <a name="alerts"></a>Alertes

Pour visualiser les alertes actives et les règles sous-jacentes qui entraînent le déclenchement d’alertes, sélectionnez **Alertes**.

![Capture d’écran du bouton Alertes](media/app-insights-app-map/alerts.png)

![Capture d’écran de l’expérience d’analyse](media/app-insights-app-map/alerts-view.png)

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Commentaires
Merci d’envoyer des commentaires via l’option de commentaires du portail.

![Image MapLink-1](./media/app-insights-app-map/13.png)

## <a name="next-steps"></a>Étapes suivantes

* [Portail Azure](https://portal.azure.com)
