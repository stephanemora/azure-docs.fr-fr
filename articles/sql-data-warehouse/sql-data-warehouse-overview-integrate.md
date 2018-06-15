---
title: Développer des solutions intégrées avec SQL Data Warehouse | Microsoft Docs
description: 'Outils et partenaires proposant des solutions s’intégrant avec SQL Data Warehouse. '
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: f198a99fc03a079be77c7f8167580bb7b758579e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2018
ms.locfileid: "31599830"
---
# <a name="integrate-other-services-with-sql-data-warehouse"></a>Intégrer d’autres services dans SQL Data Warehouse
Outre ses fonctionnalités principales, SQL Data Warehouse permet aux utilisateurs d’intégrer de nombreux autres services dans Azure. Ces services comprennent notamment :

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

SQL Data Warehouse continue à s’intégrer dans d’autres services Azure et à rejoindre d’autres [partenaires d’intégration](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
L’intégration de Power BI vous permet de combiner la puissance de calcul de SQL Data Warehouse avec la création de rapports dynamiques et la visualisation de Power BI. L’intégration de Power BI inclut actuellement les éléments suivants :

* **Connexion directe**: une connexion plus avancée avec un menu déroulant logique dans SQL Data Warehouse. Avec le pushdown, les analyses sont plus rapides à une plus grande échelle.
* **Ouvrir dans Power BI**: ce bouton transmet les informations d’instance à Power BI, permettant une connexion plus simple.

Pour plus d’informations, consultez [Intégrer à Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) ou la [documentation Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory offre aux utilisateurs une plateforme gérée pour créer des pipelines d’extraction-chargement complexes. L’intégration de SQL Data Warehouse à Azure Data Factory inclut :

* **Procédures stockées**: orchestrez l’exécution de procédures stockées dans SQL Data Warehouse.
* **Copy**: utilisez ADF pour déplacer les données dans SQL Data Warehouse. Cette opération peut utiliser le mécanisme de déplacement de données standard d’ADF ou PolyBase en arrière-plan. 

Pour plus d’informations, consultez [Intégration dans Azure Data Factory](sql-data-warehouse-get-started-visualize-with-power-bi.md).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning est un service d’analyse entièrement géré qui vous permet de créer des modèles complexes à l’aide d’un large ensemble d’outils prédictifs. SQL Data Warehouse est pris en charge à la fois comme source et destination de ces modèles avec les fonctionnalités suivantes :

* **Lire les données :** pilotez des modèles à l’échelle à l’aide de T-SQL dans SQL Data Warehouse.
* **Écrire des données :** validez les modifications d’un modèle dans SQL Data Warehouse.

Pour plus d’informations, consultez [Intégration dans Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics est une infrastructure complexe, entièrement gérée pour le traitement et l’utilisation des données d’événement générées à partir d’Azure Event Hubs.  L’intégration à SQL Data Warehouse permet de traiter efficacement et de stocker les données de diffusion en continu avec des données relationnelles, ce qui permet une analyse plus approfondie et plus avancée.  

* **Sortie de la tâche :** envoyez une sortie des tâches Stream Analytics directement à SQL Data Warehouse.

Pour plus d’informations, consultez [Intégration dans Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).

## <a name="next-steps"></a>Étapes suivantes
Pour une intégration dans Azure SQL Database, consultez [Configurer une requête élastique SQL Database](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md).

