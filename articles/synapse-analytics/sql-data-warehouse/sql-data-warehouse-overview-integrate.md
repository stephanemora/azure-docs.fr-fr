---
title: Créer des solutions intégrées
description: Partenaires et outils de solution qui s’intègrent à un pool SQL Synapse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 16cfdfb475ce21ed4b51dc9140e59df701363f27
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85201002"
---
# <a name="integrate-other-services-with-a-synapse-sql-pool-data-warehouse"></a>Intégrer d’autres services à un pool Synapse SQL (entrepôt de données)

La fonctionnalité de pool SQL dans Azure Synapse Analytics permet aux utilisateurs d’opérer une intégration avec bon nombre des autres services dans Azure. Synapse SQL vous permet de créer un entrepôt de données par le biais de sa ressource de pool SQL qui peut ensuite utiliser plusieurs services supplémentaires, dont certains incluent les composants suivants :

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Pour plus d’informations sur les services d’intégration dans Azure, consultez l’article [Partenaires d’intégration](sql-data-warehouse-partner-data-integration.md).

## <a name="power-bi"></a>Power BI

L’intégration de Power BI vous permet de combiner la puissance de calcul d’un entrepôt de données avec la création de rapports dynamiques et la visualisation de Power BI. L’intégration de Power BI inclut actuellement les éléments suivants :

* **Connexion directe** : Connexion plus avancée avec pushdown logique sur un entrepôt de données approvisionné à l’aide d’un pool SQL. Avec le pushdown, les analyses sont plus rapides à une plus grande échelle.
* **Ouvrir dans Power BI** : ce bouton transmet les informations d’instance à Power BI, permettant ainsi une connexion plus simple.

Pour plus d’informations, consultez [Intégrer à Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md) ou la [documentation Power BI](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/).

## <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory offre aux utilisateurs une plateforme gérée pour créer des pipelines d’extraction-chargement complexes. L’intégration de pool SQL avec Azure Data Factory comprend les aspects suivants :

* **Procédures stockées** : Orchestrez l’exécution des procédures stockées.
* **Copier** : Utilisez ADF pour déplacer des données vers un pool SQL. Cette opération peut utiliser le mécanisme de déplacement de données standard d’ADF ou PolyBase en arrière-plan.

Pour plus d’informations, consultez [Intégration dans Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning est un service d’analyse entièrement géré qui vous permet de créer des modèles complexes à l’aide d’un large ensemble d’outils prédictifs. Un pool SQL est pris en charge à la fois comme source et destination de ces modèles, et offre les fonctionnalités suivantes :

* **Lire les données** : piloter des modèles à grande échelle en utilisant T-SQL sur un pool SQL.
* **Écrire les données** : valider en retour des modifications à partir de tout modèle sur un pool SQL.

Pour plus d’informations, consultez [Intégration dans Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md).

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Azure Stream Analytics est une infrastructure complexe, entièrement gérée pour le traitement et l’utilisation des données d’événement générées à partir d’Azure Event Hubs.  L’intégration avec un pool SQL permet de traiter efficacement et de stocker des données de diffusion en continu avec des données relationnelles, ce qui permet une analyse plus approfondie et plus avancée.  

* **Sortie de la tâche** : envoyez une sortie de tâches Stream Analytics directement à un pool SQL.

Pour plus d’informations, consultez [Intégration dans Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md).
