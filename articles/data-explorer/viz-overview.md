---
title: Visualisation des données dans Azure Data Explorer
description: Découvrez les différentes manières de visualiser vos données Azure Data Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: d1c73d8eb65ed5d67d5250b4a3bca3b80450001e
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536719"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Visualisation des données avec Azure Data Explorer 

Azure Data Explorer est un service d’exploration des données rapide et hautement évolutif pour les données de journal et de télémétrie qui servent à créer des solutions d’analyse complexes pour les grandes quantités de données. Azure Data Explorer s’intègre avec de nombreux outils de visualisation. Vous pouvez ainsi visualiser vos données et partager les résultats dans votre organisation. Ces données peuvent être transformées en informations exploitables pour un plus grand impact sur votre entreprise.

La visualisation et la création de rapports de données sont des étapes essentielles du processus d’analyse des données. Azure Data Explorer prend en charge de nombreux services BI. Vous pouvez ainsi utiliser celui qui convient le mieux à votre scénario et votre budget.

## <a name="kusto-query-language-visualizations"></a>Visualisations du langage de requête Kusto

Le langage de requête Kusto [`render operator`](/azure/kusto/query/renderoperator) propose différentes visualisations, comme des tables, des graphiques en secteurs et des graphiques à barres, afin de décrire les résultats de la requête. Les visualisations de requêtes sont utiles pour la détection et la prévision des anomalies, l’apprentissage automatique et bien plus encore.

## <a name="power-bi"></a>Power BI

Azure Data Explorer permet de se connecter à [Power BI](https://powerbi.microsoft.com) à l’aide de différentes méthodes : 

  * [Connecteur Power BI natif intégré](/azure/data-explorer/power-bi-connector)

  * [Importation de requête Azure Data Explorer dans Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [SQL query](/azure/data-explorer/power-bi-sql-query)

## <a name="microsoft-excel"></a>Microsoft Excel

Azure Data Explorer offre la possibilité de se connecter à [Microsoft Excel](https://products.office.com/excel) à l’aide du connecteur Excel natif intégré, ou d’importer une requête à partir d’Azure Data Explorer dans Excel.

## <a name="grafana"></a>Grafana

[Grafana](https://grafana.com) propose un plug-in Azure Data Explorer, qui vous permet de visualiser des données à partir d’Azure Data Explorer. Vous [configurez Azure Data Explorer en tant que source de données pour Grafana, puis visualisez les données](/azure/data-explorer/grafana). 

## <a name="odbc-connector"></a>Connecteur ODBC

Azure Data Explorer fournit un [connecteur Open Database Connectivity (ODBC)](connect-odbc.md) afin que n’importe quelle application prenant en charge ODBC puisse se connecter à Azure Data Explorer.

## <a name="tableau"></a>Tableau

Azure Data Explorer permet de se connecter à [Tableau](https://www.tableau.com) à l’aide du [connecteur ODBC](/azure/data-explorer/connect-odbc), puis de [visualiser les données dans Tableau](tableau.md).

## <a name="qlik"></a>Qlik

Azure Data Explorer permet de se connecter à [Qlik](https://www.qlik.com) à l’aide du [connecteur ODBC](/azure/data-explorer/connect-odbc), puis de créer des tableaux de bord Qlik Sense et de visualiser les données. À l’aide de la vidéo suivante, apprenez à visualiser les données Azure Data Explorer avec Qlik. 

> [!VIDEO https://www.youtube.com/embed/nhWIiBwxjjU]  

## <a name="sisense"></a>Sisense

Azure Data Explorer permet de se connecter à [Sisense](https://www.sisense.com) à l’aide du connecteur JDBC. Vous [configurez Azure Data Explorer en tant que source de données pour Sisense, puis visualisez les données](/azure/data-explorer/sisense).