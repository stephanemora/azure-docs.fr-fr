---
title: Visualiser les données à partir d’Azure Monitor | Microsoft Docs
description: Obtenez un récapitulatif des méthodes disponibles pour visualiser les métriques et les données de journal stockées dans Azure Monitor.
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 07/28/2021
ms.openlocfilehash: 257ca33952283ba3989ce11a2d933af3feceb8ac
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612348"
---
# <a name="visualize-data-from-azure-monitor"></a>Visualiser les données à partir d’Azure Monitor
Cet article récapitule les méthodes disponibles pour visualiser les métriques et les données de journal stockées dans Azure Monitor.

Les visualisations telles que les diagrammes ou graphiques peuvent vous aider à analyser vos données de supervision pour explorer des problèmes en profondeur et identifier des modèles. En fonction de l’outil que vous utilisez, vous pourriez également avoir la possibilité de partager les visualisations avec d’autres utilisateurs à l’intérieur et en dehors de votre organisation.

## <a name="workbooks"></a>Workbooks
Les [classeurs](./visualize/workbooks-overview.md) sont des documents interactifs qui fournissent des insights détaillés sur vos données, et favorisent l’investigation et la collaboration au sein de l’équipe. Les classeurs sont particulièrement utiles pour les guides de dépannage et les comptes-rendus après incident.

![Schéma illustrant des captures d’écran de trois pages d’un classeur, notamment Analyse des consultations de page, Utilisation et Temps passé sur la page.](media/visualizations/workbook.png)

Les avantages d’un classeur sont les suivants :

- Prise en charge les métriques et des journaux.
- Prend en charge des paramètres qui permettent de créer des rapports interactifs où la sélection d’un élément dans une table met dynamiquement à jour les visualisations et graphiques associés.
- Flux de type document.
- Possibilités de définir des classeurs personnels ou partagés.
- Expérience de création simple et collaborative.
- Les modèles qui prennent en charge une galerie de modèles publique basée sur GitHub.


## <a name="azure-dashboards"></a>Tableaux de bord Azure
Les [tableaux de bord Azure](../azure-portal/azure-portal-dashboards.md) constituent la principale technologie de création de tableaux de bord pour Azure. Ils sont efficaces pour fournir un panorama unique de vos infrastructure et services Azure, ce qui vous permet d’identifier rapidement les problèmes importants.

![Capture d’écran montrant un exemple de tableau de bord Azure avec des informations personnalisables.](media/visualizations/dashboard.png)

Voici une procédure vidéo expliquant la création de tableaux de bord :

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

Les avantages d’un tableau de bord sont les suivants :

- Intégration approfondie à Azure. Les visualisations peuvent être épinglées aux tableaux de bord à partir de plusieurs pages Azure, notamment [Metrics Explorer](essentials/metrics-charts.md), [Log Analytics](logs/log-analytics-overview.md) et [Application Insights](app/app-insights-overview.md).
- Prise en charge les métriques et des journaux.
- La possibilité de combiner des données provenant de plusieurs sources, notamment une sortie [Metrics Explorer](essentials/metrics-charts.md), des [requêtes de journal](logs/log-query-overview.md) ainsi que des [cartes](app/app-map.md) et des données de disponibilité dans [Application Insights](app/app-insights-overview.md).
- Options pour des tableaux de bord personnels ou partagés via l’intégration avec le [Contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/overview.md).
- Actualisation automatique. L’actualisation des métriques a lieu selon un intervalle de temps de cinq minutes au minimum. Les journaux sont actualisés toutes les heures. Vous pouvez actualiser manuellement à la demande en sélectionnant l’icône d' **actualisation** dans une visualisation ou en actualisant le tableau de bord complet.
- Les tableaux de bord de métriques sont paramétrés avec un horodatage et des paramètres personnalisés.
- Options de disposition flexibles.
- Mode plein écran.


Ces limitations incluent :

- Contrôle limité des visualisations de journal sans prise en charge des tables de données. Le nombre total de séries de données est limité à 50, les nouvelles séries de données étant regroupées sous un _autre_ compartiment.
- Aucune prise en charge de paramètre personnalisé pour les graphiques de journal.
- Une période limitée pour les graphiques de journal (30 derniers jours).
- Exigence selon laquelle les graphiques de journal doivent être épinglés à des tableaux de bord partagés.
- Aucune interactivité avec les données de tableau de bord.
- Exploration contextuelle limitée.

## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) est une plateforme ouverte qui excellent dans les tableaux de bord opérationnels et la visualisation des données de performances et de disponibilité. Elle est utile pour détecter, isoler et trier les incidents opérationnels. Grafana comprend une prise en charge intégrée d’Azure Monitor et comprend le [plug-in Azure Monitor](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/) prêt à l’emploi. Il prend en charge la visualisation des données à partir de trois services Azure :
- Métriques Azure Monitor pour les données numériques de série chronologique à partir des données des ressources Azure. 
- Journaux Azure Monitor pour les données de journal et de performances à partir des ressources Azure qui vous permettent de formuler des requêtes à l’aide du puissant langage de requête Kusto (KQL).
- Azure Resource Graph pour interroger et identifier rapidement les ressources Azure parmi les abonnements.

Grafana fonctionne avec un large éventail de sources de données et vous permet de créer des tableaux de bord et des panneaux qui combinent des données de surveillance à partir de ressources dans Azure, localement ou dans d’autres clouds. 

![Capture d’écran montrant des visualisations Grafana.](media/visualizations/grafana.png)

> [!IMPORTANT]
> Internet Explorer 11 est entièrement pris en charge dans les versions de Grafana antérieures à la version 6.0 voir [Navigateurs pris en charge pour Grafana](https://grafana.com/docs/grafana/latest/installation/requirements/#supported-web-browsers).

Avantages de Grafana :

- Visualisations riches.
- Un écosystème complet de sources de données, y compris la prise en charge de sources de données hors Azure.
- Interactivité des données, notamment par le biais du zoom avant.
- Annotations qui permettent l’intégration de données d’événement dans des graphiques.
- Prise en charge des paramètres, y compris les [variables Azure Monitor](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/template-variables/).

Ces limitations incluent :

- Vous ne pouvez pas gérer les tableaux de bord et les modèles par le biais d’Azure Resource Manager.
- Le coût lié à la prise en charge d’une infrastructure Grafana supplémentaire ou coûts supplémentaires liés à Grafana Cloud.
## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) est utile pour créer des rapports et des tableaux de bord centrés sur l’entreprise, ainsi que des rapports qui analysent les tendances d’indicateur de performance clé à long terme. Vous pouvez [importer les résultats d’une requête Log](visualize/powerbi.md) dans un jeu de données Power BI et bénéficier ainsi de ses fonctionnalités, comme la combinaison de données provenant de différentes sources et le partage de rapports sur le web et sur des appareils mobiles.

![Capture d’écran montrant un exemple de rapport Power BI pour les opérations IT.](media/visualizations/power-bi.png)

Avantages de Power BI :

- Visualisations riches.
- Interactivité extensive, notamment par le biais du zoom avant et du filtrage croisé.
- Facilité de partage au sein de votre organisation.
- Intégration aux autres données issues de plusieurs sources de données.
- Meilleures performances avec les résultats mis en cache dans un cube.

Ces limitations incluent :

- Prise en charge des journaux d’activité, mais pas des métriques.
- Aucune intégration à Azure. Vous ne pouvez pas gérer les tableaux de bord et les modèles par le biais d’Azure Resource Manager.
- Les résultats de requête doivent être importés dans un modèle Power BI pour la configuration. 
- L’actualisation et la taille des résultats sont soumises à des limites.
- L’actualisation des données est limitée (huit exécutions par jour).


## <a name="azure-monitor-partners"></a>Partenaires Azure Monitor
Certains partenaires Azure Monitor peuvent fournir des fonctionnalités de visualisation. Pour obtenir la liste des partenaires que Microsoft a évalués, consultez [Intégrations des partenaires Azure Monitor](./partners.md). 

Un partenaire Azure Monitor peut fournir des visualisations prêtes à l’emploi pour vous faire gagner du temps. 

Ces limitations incluent :

- Potentiel pour des coûts supplémentaires.
- Besoin de temps pour la recherche et l’évaluation des offres des partenaires.

## <a name="your-own-custom-application"></a>Votre propre application personnalisée
Vous pouvez accéder aux données de métriques et de journaux dans Azure Monitor via une API en utilisant un client REST. Vous pouvez ensuite créer vos propres applications et sites web personnalisés.

Les avantages de la création d’une application personnalisée sont les suivants :

- Flexibilité complète de l’interface utilisateur, de la visualisation, de l’interactivité et des fonctionnalités.
- La possibilité de combiner des données de métriques et de journaux avec d’autres sources de données.

L’un des inconvénients majeurs est qu’elle nécessite un effort d’ingénierie.

## <a name="azure-monitor-views"></a>Affichages Azure Monitor

> [!IMPORTANT]
> Les vues sont en cours de dépréciation. Pour obtenir des conseils sur la conversion des affichages en classeurs, consultez le [Guide de transition](visualize/view-designer-conversion-overview.md).

Les [affichages dans Azure Monitor](visualize/view-designer.md) vous permettent de créer des visualisations personnalisées avec les données de journal stockées. Les [solutions de supervision](insights/solutions.md) utilisent des affichages pour présenter les données qu’elles collectent.


![Capture d’écran montrant une vignette Solution de supervision de conteneur et la vue détaillée Azure Monitor qui s’ouvre lorsque vous la sélectionnez.](media/visualizations/view.png)

Les avantages des affichages sont les suivants :

- Visualisations riches des données de journal.
- La possibilité d’exporter et d’importer des affichages pour les transférer vers d’autres groupes de ressources et abonnements.
- Intégration au modèle de gestion Azure Monitor avec des espaces de travail et des solutions de supervision.
- [Filtres](visualize/view-designer-filters.md) pour les paramètres personnalisés.
- Interactivité, avec prise en charge de l’exploration à plusieurs niveaux (un affichage qui explore un autre affichage).

Ces limitations incluent :

- Prise en charge des journaux d’activité, mais pas des métriques.
- Aucun affichage personnel. Un affichage Disponible pour tous les utilisateurs ayant accès à l’espace de travail.
- Aucune actualisation automatique.
- Options de disposition limitées.
- Pas prise en charge de l’interrogation de plusieurs espaces de travail ou applications Application Insights.
- Taille de réponse à la requête limitée (8 Mo) et durée d’exécution limitée de la requête (11 secondes).

## <a name="next-steps"></a>Étapes suivantes
- Découvrez les [données collectées par Azure Monitor](data-platform.md).
- Découvrez les [tableaux de bord Azure](../azure-portal/azure-portal-dashboards.md).
- En savoir plus sur [Metrics Explorer](essentials/metrics-getting-started.md).
- Découvrez les [classeurs](./visualize/workbooks-overview.md).
- Découvrez [l’importation de données de journal dans Power BI](./visualize/powerbi.md).
- Découvrez le [plug-in de source de données Azure Monitor pour Grafana](./visualize/grafana-plugin.md).
- Découvrez les [affichages dans Azure Monitor](visualize/view-designer.md).
