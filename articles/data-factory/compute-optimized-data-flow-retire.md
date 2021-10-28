---
title: Mise hors service de l’option Optimisé pour le calcul
description: L’option de flux de données Optimisé pour le calcul est en cours de mise hors service
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: tutorial
ms.date: 06/29/2021
ms.openlocfilehash: 781841ff8a65d36a9ed712efa5d0bdf8480995bf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229367"
---
# <a name="retirement-of-data-flow-compute-optimized-option"></a>Mise hors service de l’option de flux de données Optimisé pour le calcul

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Les flux de données Azure Data Factory et Azure Synapse Analytics fournissent un mécanisme de faible code pour transformer les données des travaux ETL à l’échelle à l’aide d’un paradigme de conception graphique. Les flux de données s’exécutent sur le runtime d’intégration Azure Data Factory et Azure Synapse Analytics serverless. La nature évolutive des runtimes d’intégration d’Azure Data Factory et d’Azure Synapse Analytics permet trois options de calcul différentes pour l’environnement Azure Databricks Spark utilisé pour exécuter des flux de données à l’échelle : à mémoire optimisée, usage général et optimisé pour le calcul. À mémoire optimisée et usage général sont les classes recommandées de calcul de flux de données à utiliser avec votre runtime d’intégration pour les charges de travail de production. Étant donné que le calcul optimisé n’est souvent pas suffisant pour des cas d’usage courants avec des flux de données, nous vous recommandons d’utiliser des flux de données Usage général ou À mémoire optimisée dans les charges de travail de production.

## <a name="migration-steps"></a>Étapes de la migration

De maintenant jusqu’au 31 août 2024, vos flux de données optimisés pour le calcul continueront de fonctionner dans vos pipelines existants. Pour éviter toute interruption de service, supprimez vos flux de données optimisés pour le calcul existants avant le 31 août 2024 et suivez les étapes ci-dessous pour créer une instance Azure Integration Runtime et une activité de flux de données. Lors de la création d’une activité de flux de données :

1. Créez une instance Azure Integration Runtime avec « usage général » ou « à mémoire optimisée » comme type de calcul.
2. Définissez votre activité de flux de données à l’aide de l’un de ces types de calcul.

   :::image type="content" source="media/data-flow/compute-types.png" alt-text="Types de calcul":::

## <a name="comparison-between-different-compute-options"></a>Comparaison entre les différentes options de calcul 

| Option de calcul              | Performances                                                  |
| :-------------------- | :----------------------------------------------------------- |
| Flux de données à usage général (de base) | Idéal pour les cas d’usage généraux dans les charges de travail de production |
| Flux de données à mémoire optimisée (standard) | Exécution optimale pour les flux de données lors de l’utilisation de jeux de données volumineux et de nombreux calculs |
| Flux de données optimisés pour le calcul (dépréciés) | Non recommandé pour les charges de travail de production |

* [Visitez la page de tarification d’Azure Data Factory pour connaître les derniers tarifs disponibles pour les flux de données à usage général et à mémoire optimisée](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)
* [Vous trouverez plus d’informations sur la FAQ des flux de données ici](./frequently-asked-questions.yml#mapping-data-flows)  
* [Posez des questions et trouvez des réponses sur les flux de données sur Microsoft Q&A](https://aka.ms/datafactoryqa)