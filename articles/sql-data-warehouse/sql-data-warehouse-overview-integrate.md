---
title: Développer des solutions intégrées avec SQL Data Warehouse | Microsoft Docs
description: 'Outils et partenaires proposant des solutions s’intégrant avec SQL Data Warehouse. '
services: sql-data-warehouse
author: kavithaj
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 0d9d10214f8bc61a914de3f72ddae13679f2811e
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358505"
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

* **Connexion directe** : une connexion plus avancée avec un menu déroulant logique dans SQL Data Warehouse. Avec le pushdown, les analyses sont plus rapides à une plus grande échelle.
* **Ouvrir dans Power BI** : ce bouton transmet les informations d’instance à Power BI, permettant une connexion plus simple.

Pour plus d’informations, consultez [Intégrer à Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) ou la [documentation Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/06/24/exploring-azure-sql-data-warehouse-with-power-bi.aspx).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory offre aux utilisateurs une plateforme gérée pour créer des pipelines d’extraction-chargement complexes. L’intégration de SQL Data Warehouse à Azure Data Factory inclut :

* **Procédures stockées** : orchestrez l’exécution de procédures stockées dans SQL Data Warehouse.
* **Copier** : utilisez ADF pour déplacer les données dans SQL Data Warehouse. Cette opération peut utiliser le mécanisme de déplacement de données standard d’ADF ou PolyBase en arrière-plan. 

Pour plus d’informations, consultez [Intégration dans Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning est un service d’analyse entièrement géré qui vous permet de créer des modèles complexes à l’aide d’un large ensemble d’outils prédictifs. SQL Data Warehouse est pris en charge à la fois comme source et destination de ces modèles avec les fonctionnalités suivantes :

* **Lire les données** : pilotez des modèles à l’échelle à l’aide de T-SQL dans SQL Data Warehouse.
* **Écrire les données** : validez les modifications d’un modèle dans SQL Data Warehouse.

Pour plus d’informations, consultez [Intégration dans Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics est une infrastructure complexe, entièrement gérée pour le traitement et l’utilisation des données d’événement générées à partir d’Azure Event Hubs.  L’intégration à SQL Data Warehouse permet de traiter efficacement et de stocker les données de diffusion en continu avec des données relationnelles, ce qui permet une analyse plus approfondie et plus avancée.  

* **Sortie de la tâche** : envoyez une sortie des tâches Stream Analytics directement à SQL Data Warehouse.

Pour plus d’informations, consultez [Intégration dans Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).

## <a name="next-steps"></a>Étapes suivantes
Pour une intégration dans Azure SQL Database, consultez [Configurer une requête élastique SQL Database](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md).

