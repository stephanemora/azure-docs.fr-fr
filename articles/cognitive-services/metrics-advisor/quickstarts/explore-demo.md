---
title: Commencer à explorer la démonstration de Metrics Advisor
titleSuffix: Azure Cognitive Services
description: Découvrez l’interface web de Metrics Advisor via la démonstration que nous fournissons
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: fbc73e20b8cc2baa1cc5c5a5b2f674fb1b2dde84
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90944619"
---
# <a name="quickstart-explore-the-metrics-advisor-demo-with-example-data"></a>Démarrage rapide : Explorer la démonstration de Metrics Advisor avec des exemples de données

> [!Note]
> La démonstration de Metrics Advisor est en lecture seule et vous ne pourrez pas intégrer vos données. Pour utiliser le service sur vos données, commencez par [créer une ressource Metrics Advisor](web-portal.md).

Utilisez cet article pour explorer rapidement les fonctionnalités clés de Metrics Advisor. Nous fournissons un site de démonstration avec des exemples de données et des configurations prédéfinies, qui vous permettront de vous familiariser avec ce portail web complet.

Cliquez sur [ce lien](https://aka.ms/MetricsAdvisor/Demo) pour accéder au site de démonstration.

## <a name="view-the-available-sample-data"></a>Afficher les exemples de données disponibles

Une fois que vous vous êtes connecté au site de démonstration, vous voyez une page **Flux de données**. Un flux de données est un groupe logique de données de série chronologique interrogées à partir de votre source de données. Pour plus d’informations sur les termes et les concepts utilisés dans Metrics Advisor, consultez le [Glossaire](../glossary.md). 

Plusieurs flux de données sont répertoriés, qui sont ingérés à partir de différents types de sources de données, tels qu’Azure SQL Database ou Azure Table. Chacun d’eux utilise des paramètres de configuration légèrement différents pour se connecter aux magasins de données associés.

:::image type="content" source="../media/sample-datafeeds.png" alt-text="Liste d’exemples de données" lightbox="../media/sample-datafeeds.png":::

## <a name="explore-the-data-feed-configurations"></a>Explorer les configurations de flux de données

Cliquez sur le flux de données *Exemple - Coût/Produit - Ville/Catégorie*. Vous verrez plusieurs sections de détails pour ce flux :

* Nom du flux de données et état de l’ingestion des données.
* Liste des métriques interrogées à partir de la source de données. Par exemple, *coût* et *produit*. 
* Historique des alertes à utiliser lorsque le flux de données devient non disponible. 
* Journaux du moment où le flux de données a été mis à jour.   
* Informations et paramètres de flux de données.

:::image type="content" source="../media/data-feed-view.png" alt-text="Liste d’exemples de données" lightbox="../media/data-feed-view.png":::


## <a name="view-time-series-visualizations-and-configurations"></a>Afficher les visualisations et les configurations des séries chronologiques

Cliquez dans la métrique *coût* du flux de données *Exemple - Coût/Produit - Ville/Catégorie*. Vous verrez les séries chronologiques associées, découpées par dimensions, avec des visualisations selon les données de métrique historiques. La bande bleue autour des données de métrique représente la plage des valeurs attendues à partir des modèles Machine Learning de Metrics Advisor. Les points situés hors de cette bande sont marqués sous la forme de points rouges et correspondent aux anomalies détectées. 

:::image type="content" source="../media/series-visualization.png" alt-text="Liste d’exemples de données" lightbox="../media/series-visualization.png":::

La détection des anomalies peut être configurée en réglant les **configurations de détection** sur le côté gauche de la page de détails de métrique. Plusieurs méthodes de détection d’anomalies sont disponibles et vous pouvez les combiner. Vous pouvez également essayer différentes sensibilités et directions de détection, ainsi que d’autres configurations. Le lien **Configuration avancée** en bas des **configurations de détection** vous permet de créer des paramètres de détection plus complexes et personnalisés, qui peuvent être utilisés sur des groupes ou des séries individuelles. 

Vous pouvez également régler la détection des anomalies en fournissant des commentaires à l’algorithme de détection. Cliquez dans une anomalie et utilisez le panneau **Ajouter des commentaires** pour configurer son état d’anomalie, son caractère saisonnier et son état de point de changement. Ces commentaires seront incorporés dans la détection des futurs points.  

En bas du panneau **Ajouter des commentaires**, un lien **Vers le hub d’incidents** vous dirigera vers la page d’analyse des incidents et analysera la cause racine de l’incident.  

:::image type="content" source="../media/incident-link.png" alt-text="Liste d’exemples de données" lightbox="../media/incident-link.png":::

## <a name="explore-anomaly-detection-results-and-perform-root-cause-analysis"></a>Explorer les résultats de détection des anomalies et effectuer une analyse de la cause racine

Lorsque vous cliquez sur le lien **Vers le hub d’incidents** à partir d’une anomalie, une page d’analyse d’incident s’affiche, qui contient des informations de diagnostic sur l’incident, telles que sa gravité, le nombre d’anomalies impliquées et les heures de début et de fin. La section **Cause racine** affiche des conseils automatisés en analysant l’arborescence de l’incident, en tenant compte des écarts, de la distribution et de la contribution aux anomalies parentes, qui peuvent être la cause racine de l’incident.

La section **Diagnostics** affiche une arborescence de l’incident, ainsi que plusieurs onglets pour diagnostiquer l’incident.

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Liste d’exemples de données" lightbox="../media/incident-diagnostic.png":::

En identifiant la cause racine de l’incident, vous pouvez prendre des mesures pour atténuer le problème avant que la situation empire. Vous pouvez également explorer d’autres insights en cliquant sur les autres fonctionnalités de diagnostic fournies. 

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser le portail web](web-portal.md)
- [Utiliser l’API REST](rest-api.md)
- [Intégrer vos flux de données](../how-tos/onboard-your-data.md)
    - [Gérer les flux de données](../how-tos/manage-data-feeds.md)
    - [Configurations pour différentes sources de données](../data-feeds-from-different-sources.md)
