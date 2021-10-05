---
title: Installer la solution Azure Data Factory Analytics à partir de la Place de marché Azure
description: Découvrez comment installer une solution Azure Data Factory Analytics à partir de la Place de marché Azure.
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 4ae7015c575dc10c9f8c2ec2b896e75e1a2a2a91
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838423"
---
# <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Installer la solution Azure Data Factory Analytics à partir de la Place de marché Azure

Cette solution fournit un résumé de l’intégrité globale de votre Data Factory, avec des options permettant d’examiner les détails et de résoudre les problèmes de comportements inattendus. Avec des affichages enrichis et prêts à l’emploi, vous pouvez vous familiariser avec le traitement des clés, notamment :

* Un récapitulatif des exécutions de pipeline, d’activité et de déclencheur Data Factory
* Une possibilité d’effectuer des recherches dans les exécutions d’activités Data Factory par type
* Un résumé des erreurs d’activités rencontrées et du meilleur pipeline Data Factory

1. Accédez à la **Place de marché Azure**, sélectionnez **Filtre Analytics**, puis cherchez **Azure Data Factory Analytics (préversion)**

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image3.png" alt-text="Accédez à la &quot;Place de marché Azure&quot;, entrez &quot;Filtre Analytics&quot;, puis sélectionnez &quot;Azure Data Factory Analytics (préversion)&quot;":::

1. Détails sur **Azure Data Factory Analytics (préversion)**

   :::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image4.png" alt-text="Détails sur &quot;Azure Data Factory Analytics (préversion)&quot;":::

1. Sélectionnez **Créer**, puis créez ou sélectionnez l’**espace de travail Log Analytics**.

   :::image type="content" source="media/data-factory-monitor-oms/monitor-log-analytics-image-5.png" alt-text="Création d’une solution":::

## <a name="monitor-data-factory-metrics"></a>Surveiller les métriques de Data Factory

L’installation de cette solution crée un ensemble de vues par défaut à l’intérieur de la section classeurs de l’espace de travail Log Analytics choisi. Par conséquent, les métriques suivantes sont activés :

* Exécutions d’ADF - 1) Exécutions de pipeline par Data Factory
* Exécution d’ADF - 2) Exécutions d’activités par Data Factory
* Exécutions d’ADF- 3) Exécutions de déclencheur par Data Factory
* Erreurs d’ADF - 1) 10 premières erreurs de pipeline par Data Factory
* Erreurs d’ADF - 2) 10 premières exécution d'activité par Data Factory
* Erreur d’ADF - 3) 10 premières erreurs de déclencheur par Data Factory
* Statistiques d’ADF - 1) Exécutions d’activité par type
* Statistiques d’ADF - 2) Exécutions de déclencheur par type
* Statistiques d’ADF - 3) Durée maximale d’exécutions du pipeline

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image6.png" alt-text="Fenêtre avec &quot;Classeurs (préversion)&quot; et &quot;AzureDataFactoryAnalytics&quot; en évidence":::

Vous pouvez visualiser les métriques ci-dessus, consulter les requêtes derrière ces métriques, modifier les requêtes, créer des alertes, et exécuter d’autres action.

:::image type="content" source="media/data-factory-monitor-oms/monitor-oms-image8.png" alt-text="Représentation graphique des exécutions de pipeline par Data Factory":::

> [!NOTE]
> Azure Data Factory Analytics (préversion) envoie des journaux de diagnostic à des tables de destination _Spécifique de la ressource_. Vous pouvez écrire des requêtes portant sur les tables suivantes : _ADFPipelineRun_, _ADFTriggerRun_ et _ADFActivityRun_.

## <a name="next-steps"></a>Étapes suivantes

[Surveiller et gérer les pipelines par programmation](monitor-programmatically.md)
