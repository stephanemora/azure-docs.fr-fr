---
title: 'Meilleures pratiques d’Azure Monitor : Analyse et visualisations'
description: Aide et recommandations relatives à la personnalisation des visualisations au-delà des fonctionnalités d’analyse standard dans Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: 3f4b671237c842c156e06281926be02ee3cdeb4a
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181682"
---
# <a name="azure-monitor-best-practices---analyze-and-visualize-data"></a>Meilleures pratiques d’Azure Monitor : Analyser et visualiser les données
Cet article fait partie du scénario [Recommandations pour la configuration d’Azure Monitor](best-practices.md). Il décrit les fonctionnalités intégrées d’Azure Monitor pour l’analyse des données collectées et les options de création de visualisations personnalisées pour répondre aux besoins des différents utilisateurs de votre organisation. Les visualisations telles que les diagrammes ou graphiques peuvent vous aider à analyser vos données de supervision pour explorer des problèmes en profondeur et identifier des modèles.


## <a name="builtin-analysis-features"></a>Fonctionnalités d’analyse intégrées
Les sections suivantes décrivent les fonctionnalités d’Azure Monitor qui permettent d’analyser les données collectées sans aucune configuration.
### <a name="overview-page"></a>Page Vue d’ensemble
La plupart des services Azure disposent d’une page **Vue d’ensemble** dans le portail Azure qui comprend une section **Superviser** avec des graphiques récents pour les métriques critiques. Ceci est destiné aux propriétaires de services individuels pour évaluer rapidement les performances de la ressource. Comme cette page est basée sur des métriques de plateforme collectées automatiquement, aucune configuration n’est requise pour cette fonctionnalité.

### <a name="metrics-explorer"></a>Metrics Explorer
Metrics Explorer permet aux utilisateurs d’utiliser des données de métriques et de créer des alertes de métrique de manière interactive. La plupart des utilisateurs pourront utiliser Metrics Explorer avec une formation minimale, mais ils doivent être familiarisés avec les métriques qu’ils souhaitent analyser. Aucune configuration n’est requise pour cette fonctionnalité une fois que la collecte de données a été configurée. Les métriques de plateforme pour les ressources Azure sont automatiquement disponibles. Les métriques d’invité pour les machines virtuelles seront disponibles lorsque l’agent Azure Monitor aura été déployé sur celles-ci, et les métriques d’application seront disponibles lorsqu’Application Insights aura été configuré.


### <a name="log-analytics"></a>Log Analytics
Log Analytics permet aux utilisateurs de créer des requêtes de journal pour travailler avec des données de journal et créer des alertes de requête de journal de manière interactive. Une certaine formation est nécessaire pour que les utilisateurs se familiarisent avec le langage de requête, mais ils peuvent utiliser des requêtes préétablies pour les besoins courants. Vous pouvez également ajouter des [packs de requêtes](logs/query-packs.md) avec des requêtes propres à votre organisation. Cela permet aux utilisateurs qui connaissent bien le langage de requête de créer des requêtes pour d’autres personnes de l’organisation.


## <a name="workbooks"></a>Workbooks
Les [classeurs](./visualize/workbooks-overview.md) sont la plateforme de visualisation de choix pour Azure, offrant un canevas flexible pour l’analyse des données et la création de rapports visuels enrichis. Les classeurs vous permettent d’exploiter plusieurs sources de données à travers l’écosystème Azure et de les combiner dans des expériences interactives unifiées. Ils sont particulièrement utiles pour préparer les vues de surveillance E2E sur plusieurs ressources Azure.

Les insights utilisent des classeurs prédéfinis pour présenter aux utilisateurs des informations critiques sur l’intégrité et les performances d’un service particulier. Vous pouvez accéder à une galerie de classeurs supplémentaires sous l’onglet **Classeurs** du menu Azure Monitor et créer des classeurs personnalisés pour répondre aux besoins de vos différents utilisateurs.

![Schéma illustrant des captures d’écran de trois pages d’un classeur, notamment Analyse des consultations de page, Utilisation et Temps passé sur la page.](media/visualizations/workbook.png)

Voici les scénarios les plus courants pour les classeurs :

- Créer un rapport interactif avec des paramètres où la sélection d’un élément dans une table met dynamiquement à jour les visualisations et graphiques associés.
- Partager un rapport avec d’autres utilisateurs de l’organisation.
- Collaborer avec d’autres auteurs de classeurs de l’organisation à l’aide d’une galerie de modèles GitHub publique.



## <a name="azure-dashboards"></a>Tableaux de bord Azure
Les [tableaux de bord Azure](../azure-portal/azure-portal-dashboards.md) sont utiles, car ils permettent d’avoir une vue d’ensemble de votre infrastructure et de vos services Azure. Alors qu’un classeur offre des fonctionnalités plus riches, un tableau de bord peut combiner les données d’Azure Monitor avec celles d’autres services Azure.

![Capture d’écran montrant un exemple de tableau de bord Azure avec des informations personnalisables.](media/visualizations/dashboard.png)

Voici une procédure vidéo expliquant la création de tableaux de bord :

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

Voici les scénarios les plus courants pour les tableaux de bord :

- Créer un tableau de bord combinant un graphique de métriques et les résultats d’une requête de journal avec des données opérationnelles pour les services associés.
- Partager un tableau de bord avec des propriétaires de services grâce à l’intégration du [contrôle d’accès en fonction du rôle Azure (RBAC Azure)](../role-based-access-control/overview.md).
  

Pour plus d’informations sur la création d’un tableau de bord qui comprend des données de journaux Azure Monitor, consultez [Créer et partager des tableaux de bord de données Log Analytics](visualize/tutorial-logs-dashboards.md). Pour plus d’informations sur la création d’un tableau de bord qui comprend des données Application Insights, consultez [Créer des tableaux de bord d’indicateurs de performance clés personnalisés à l’aide d’Application Insights](app/tutorial-app-dashboards.md). 



## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) est utile pour créer des rapports et des tableaux de bord centrés sur l’entreprise, ainsi que des rapports qui analysent les tendances d’indicateur de performance clé à long terme. Vous pouvez [importer les résultats d’une requête Log](visualize/powerbi.md) dans un jeu de données Power BI et bénéficier ainsi de ses fonctionnalités, comme la combinaison de données provenant de différentes sources et le partage de rapports sur le web et sur des appareils mobiles.

![Capture d’écran montrant un exemple de rapport Power BI pour les opérations IT.](media/visualizations/power-bi.png)

Voici les scénarios les plus courants pour Power BI :

- Visualisations riches.
- Interactivité extensive, notamment par le biais du zoom avant et du filtrage croisé.
- Facilité de partage au sein de votre organisation.
- Intégration aux autres données issues de plusieurs sources de données.
- Meilleures performances avec les résultats mis en cache dans un cube.



## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) est une plateforme ouverte qui convient parfaitement dans les tableaux de bord opérationnels. Elle est utile pour détecter, isoler et trier les incidents opérationnels, en combinant des visualisations de sources de données Azure et non Azure, y compris les sources locales, les outils tiers et les magasins de données dans d’autres clouds. Grafana dispose de plug-ins et de modèles de tableaux de bord populaires pour les outils APM tels que Dynatrace, New Relic et App Dynamics, ce qui permet aux utilisateurs de visualiser les données de la plateforme Azure en même temps que d’autres métriques provenant de sources plus élevées dans la pile et collectées par d’autres outils. Elle dispose également de plug-ins AWS CloudWatch et GCP BigQuery pour une surveillance multicloud dans un volet unique.




Vous pouvez ajouter le [plug-in de source de données Azure Monitor pour Grafana](visualize/grafana-plugin.md) à votre abonnement Azure afin qu’il visualise vos données de métriques Azure.

![Capture d’écran montrant des visualisations Grafana.](media/visualizations/grafana.png)


Voici les scénarios les plus courants pour Grafana :

- Combiner les données de série chronologique et d’événement dans un seul panneau de visualisation.
- Créer un tableau de bord dynamique basé sur la sélection de variables dynamiques par l’utilisateur.
- Créer un tableau de bord à partir d’un modèle créé et pris en charge par la communauté.
- Créer un scénario de continuité d’activité et reprise d’activité indépendant du fournisseur qui s’exécute sur n’importe quel fournisseur de cloud ou localement.

## <a name="azure-monitor-partners"></a>Partenaires Azure Monitor
Certains partenaires Azure Monitor peuvent fournir des fonctionnalités de visualisation. Pour obtenir la liste des partenaires que Microsoft a évalués, consultez [Intégrations des partenaires Azure Monitor](./partners.md). Un partenaire d’Azure Monitor peut fournir des visualisations prêtes à l’emploi pour vous faire gagner du temps, mais ces solutions peuvent avoir un coût supplémentaire.


## <a name="custom-application"></a>Application personnalisée
Vous pouvez ensuite créer vos propres sites web et applications personnalisés en utilisant les données de métrique et de journal d’Azure Monitor accessibles via une API REST. Vous bénéficiez ainsi d’une flexibilité totale en matière d’interface utilisateur, de visualisation, d’interactivité et de fonctionnalités.


## <a name="next-steps"></a>Étapes suivantes
- Consultez [Alertes et actions automatisées](best-practices-alerts.md) pour définir des alertes et des actions automatisées à partir des données Azure Monitor. 