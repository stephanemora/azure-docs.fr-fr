---
title: Visualisation de données de l’Explorateur de données Azure
description: En savoir plus sur les différentes méthodes que vous pouvez visualiser vos données de l’Explorateur de données Azure
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 85c37b6d626fc9942f5df956e738431d2727d282
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481833"
---
# <a name="data-visualization-with-azure-data-explorer"></a>Visualisation des données avec l’Explorateur de données Azure 

Explorateur de données Azure est un service d’exploration de données rapide et hautement évolutive pour les données de journal et les données de télémétrie qui sert à créer des solutions d’analytique complexe pour les grandes quantités de données. Explorateur de données Azure s’intégrant aux divers outils de visualisation, vous pouvez visualiser vos données et partager les résultats dans votre organisation. Ces données peuvent être transformées en informations exploitables pour avoir un impact sur votre entreprise.

Visualisation des données et la création de rapports est une étape essentielle du processus de données analytique. Explorateur de données Azure prend en charge de nombreux services de BI afin de pouvoir utiliser celui qui convient le mieux à votre scénario et votre budget.

* Visualisations de l’Explorateur de données Azure : À l’aide du langage de requête Kusto le [ `render operator` ](/azure/kusto/query/renderoperator) offre différents types de visualisation pour représenter des résultats de la requête. Visualisations des requêtes sont utiles pour la détection d’anomalie et prévision, apprentissage automatique et bien plus encore.

* [Power BI](https://powerbi.microsoft.com) : Explorateur de données Azure permet de se connecter à Power BI à l’aide de différentes méthodes : 

  * [Connecteur de Power BI natif intégré](/azure/data-explorer/power-bi-connector)

  * [Importation de la requête à partir de l’Explorateur de données Azure dans Power BI](/azure/data-explorer/power-bi-imported-query)
 
  * [Requête SQL](/azure/data-explorer/power-bi-sql-query).

* [Microsoft Excel](https://products.office.com/excel): Explorateur de données Azure permet de vous connecter à Excel à l’aide du connecteur Excel natif intégré, ou importer une requête à partir de l’Explorateur de données Azure dans Excel.

* [Grafana](https://grafana.com): Grafana fournit un plug-in Explorateur de données Azure qui vous permet de visualiser des données à partir de l’Explorateur de données Azure. Vous [configuré de l’Explorateur de données Azure comme source de données pour Grafana et puis visualiser les données](/azure/data-explorer/grafana)

* [Sisense](https://www.sisense.com): Explorateur de données Azure permet de se connecter à Sisense à l’aide du Connecteur JDBC. Vous [configuré de l’Explorateur de données Azure comme source de données pour Sisense et puis visualiser les données](/azure/data-explorer/sisense).

* [Tableau](https://www.tableau.com): Explorateur de données Azure offre la possibilité de se connecter au Tableau à l’aide de la [connecteur ODBC et visualiser les données dans le Tableau](/azure/data-explorer/connect-odbc).

* [Qlik](https://www.qlik.com): Explorateur de données Azure offre la possibilité de se connecter à l’aide de Qlik le [connecteur ODBC](/azure/data-explorer/connect-odbc).