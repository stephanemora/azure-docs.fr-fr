---
title: Pipelines des données opérationnelles
description: Découvrez comment fournir des contrats de niveau de service pour les pipelines de données
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: tutorial
ms.date: 09/22/2021
ms.openlocfilehash: d80d2abf4d51d99fb207ec728e29b362079ba64a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207542"
---
# <a name="deliver-service-level-agreement-for-data-pipelines"></a>Fournir un contrat de niveau de service pour les pipelines de données

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory a les faveurs et la confiance d’entreprises du monde entier. En tant que service ETL cloud natif d’Azure pour l’intégration et la transformation des données serverless avec scale-out, il est largement utilisé pour implémenter des pipelines de données afin de préparer, traiter et charger des données dans le lac de données ou l’entrepôt de données de l’entreprise.

Une fois les pipelines de données publiés, via l’[intégration et la livraison continues (CI/CD)](continuous-integration-delivery.md) en mode Git ou directement en mode réel, ils s’exécutent généralement sur Autopilot. Ils peuvent s’exécuter selon un calendrier prédéfini, avec un [déclencheur de planification](how-to-create-schedule-trigger.md) ou un [déclencheur de fenêtre bascule](how-to-create-tumbling-window-trigger.md), ou ils peuvent s’exécuter dans une architecture pilotée par les événements, avec un [déclencheur d’événements de stockage](how-to-create-event-trigger.md) ou un [déclencheur d’événements personnalisé](how-to-create-custom-event-trigger.md). Ils se voient confier des charges de travail stratégiques, afin de préparer les données pour des rapports métier, l’analytique données et les projets de Machine Learning.

## <a name="preemptive-warnings-for-long-running-jobs"></a>Avertissements préemptifs pour les travaux de longue durée

La livraison de contrats de niveau de service (SLA) pour ces pipelines de données doit relever deux défis majeurs :

* L’environnement de calcul pour les activités, par exemple SQL pour une activité de procédure stockée, peut présenter une limitation, ralentissant l’ensemble du pipeline de données et compromettant le contrat SLA du pipeline.
* Les développeurs de pipeline ne supervisent pas toujours activement la fabrique, ni ne recherchent de manière proactive les pipelines de longue durée pour lesquels des contrats SLA font défaut.

Pour résoudre ces problèmes, quand elles sont configurées correctement, les exécutions de pipeline émettent des métriques _Exécution de pipeline - Temps écoulé_ dans le cas d’un contrat SLA manquant. Si nous prenons également en compte les [alertes Data Factory](monitor-metrics-alerts.md#data-factory-alerts), nous donnons la possibilité aux développeurs de pipelines de données de mieux livrer des contrats SLA à leurs clients : vous nous indiquez la durée d’exécution d’un pipeline et nous vous informons, de manière proactive, quand le pipeline s’exécute plus longtemps que prévu.

Pour chaque pipeline sur lequel vous souhaitez créer des alertes, au cours de la phase de création, accédez aux paramètres du pipeline en cliquant sur l’espace vide dans son canevas.

:::image type="content" source="media/tutorial-operationalize-pipelines/elapsed-time-1-set-up.png" alt-text="Capture d’écran montrant comment spécifier la durée d’exécution attendue pour un pipeline.":::

Sous l’onglet __Paramètres__, cochez la case Métrique de temps écoulé et spécifiez la durée d’exécution du pipeline attendue, au format `D.HH:MM:SS`. Nous vous recommandons vivement de définir dans votre contrat SLA métier le temps que le pipeline peut prendre pour répondre à vos besoins métier. Dès qu’une durée de pipeline dépasse ce paramètre, Data Factory enregistre une métrique _Exécution de pipeline - Temps écoulé_ (ID de métrique : `PipelineElapsedTimeRuns`) dans Azure Monitor. En d’autres termes, vous êtes informé des pipelines de longue durée _de manière préventive_, avant la fin de l’exécution des pipelines.

Nous comprenons que certains pipelines prennent naturellement plus de temps que d’autres, car ils ont plus d’étapes ou déplacent plus de données. Il n’existe aucune définition unique pour les pipelines de longue durée. Nous vous demandons de bien vouloir définir le seuil pour chaque pipeline pour lequel vous avez besoin d’un contrat SLA. Quand la métrique est journalisée pour un pipeline particulier, nous la comparons à son paramètre défini par l’utilisateur pour la durée d’exécution attendue.

> [!NOTE]
> Il s’agit d’une fonctionnalité qu’il faut activer pour chaque pipeline. Aucune métrique n’est jamais journalisée pour un pipeline si aucune durée d’exécution attendue n’est spécifiée pour ce pipeline.

Suivez les étapes permettant de configurer des [alertes Data Factory](monitor-metrics-alerts.md#data-factory-alerts) sur la métrique. Vos ingénieurs sont informés qu’ils peuvent intervenir et prendre des mesures pour remplir les contrats SLA par e-mail ou SMS.

## <a name="next-steps"></a>Étapes suivantes

[Métriques et alertes Data Factory](monitor-metrics-alerts.md)

[Surveiller visuellement](monitor-visually.md#alerts)
