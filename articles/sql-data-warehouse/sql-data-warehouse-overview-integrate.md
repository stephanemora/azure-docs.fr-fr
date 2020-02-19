---
title: Créer des solutions intégrées
description: Outils et partenaires de la solution qui s’intègrent avec un entrepôt de données approvisionné à l’aide de SQL Analytics.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 8b38b50401c50ecfb9cd37c53ad013ca05e126c0
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153296"
---
# <a name="integrate-other-services-with-a-sql-analytics-data-warehouse"></a>Intégrer d’autres services avec un entrepôt de données SQL Analytics 
La fonctionnalité SQL Analytics dans Azure Synapse Analytics permet aux utilisateurs d’opérer une intégration avec bon nombre des autres services dans Azure. SQL Analytics vous permet de créer un entrepôt de données via sa ressource de pool SQL qui peut ensuite utiliser plusieurs services supplémentaires, dont certains incluent les composants suivants :

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Pour plus d’informations sur les services d’intégration dans Azure, voir l’article [Partenaires d’intégration](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI
L’intégration de Power BI vous permet de combiner la puissance de calcul d’un entrepôt de données avec la création de rapports dynamiques et la visualisation de Power BI. L’intégration de Power BI inclut actuellement les éléments suivants :

* **Connexion directe** : Connexion plus avancée avec pushdown logique sur un entrepôt de données approvisionné à l’aide d’un pool SQL. Avec le pushdown, les analyses sont plus rapides à une plus grande échelle.
* **Ouvrir dans Power BI** : ce bouton transmet les informations d’instance à Power BI, permettant ainsi une connexion plus simple.

Pour plus d’informations, consultez [Intégrer à Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) ou la [documentation Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory
Azure Data Factory offre aux utilisateurs une plateforme gérée pour créer des pipelines d’extraction-chargement complexes. L’intégration de pool SQL avec Azure Data Factory comprend les aspects suivants :

* **Procédures stockées** : Orchestrez l’exécution des procédures stockées.
* **Copier** : Utilisez ADF pour déplacer des données vers un pool SQL. Cette opération peut utiliser le mécanisme de déplacement de données standard d’ADF ou PolyBase en arrière-plan. 

Pour plus d’informations, consultez [Intégration dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?toc=/azure/sql-data-warehouse/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning
Azure Machine Learning est un service d’analyse entièrement géré qui vous permet de créer des modèles complexes à l’aide d’un large ensemble d’outils prédictifs. Un pool SQL est pris en charge à la fois comme source et destination de ces modèles, et offre les fonctionnalités suivantes :

* **Lire les données** : piloter des modèles à grande échelle en utilisant T-SQL sur un pool SQL.
* **Écrire les données** : valider en retour des modifications à partir de tout modèle sur un pool SQL.

Pour plus d’informations, consultez [Intégration dans Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Azure Stream Analytics est une infrastructure complexe, entièrement gérée pour le traitement et l’utilisation des données d’événement générées à partir d’Azure Event Hubs.  L’intégration avec un pool SQL permet de traiter efficacement et de stocker des données de diffusion en continu avec des données relationnelles, ce qui permet une analyse plus approfondie et plus avancée.  

* **Sortie de la tâche** : envoyez une sortie de tâches Stream Analytics directement à un pool SQL.

Pour plus d’informations, consultez [Intégration dans Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).


