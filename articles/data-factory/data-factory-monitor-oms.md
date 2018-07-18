---
title: Surveiller Azure Data Factory avec OMS | Microsoft Docs
description: Découvrez comment surveiller Azure Data Factory en acheminant des données vers Operations Management Suite (OMS) pour analyse.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: douglasl
ms.openlocfilehash: 4275a4ddcee51d58949b5bd83e4a898cb3dbb389
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36303980"
---
# <a name="monitor-azure-data-factory-with-operations-management-suite-oms"></a>Surveiller Azure Data Factory avec Operations Management Suite (OMS)

Vous pouvez utiliser l’intégration d’Azure Data Factory avec Azure Monitor pour acheminer des données vers Operations Management Suite (OMS). Cette intégration est utile dans les scénarios suivants :

1.  Vous souhaitez écrire des requêtes complexes sur un ensemble varié de mesures publiées par la Data Factory sur OMS. Vous pouvez également créer des alertes personnalisées sur ces requêtes via OMS.

2.  Vous souhaitez surveiller l’activité de toutes les fabriques de données. Vous pouvez acheminer les données de plusieurs fabriques de données vers un seul espace de travail OMS.

## <a name="get-started"></a>Prise en main

### <a name="configure-diagnostic-settings-and-oms-workspace"></a>Configurer les paramètres de diagnostic et l’espace de travail OMS

Activer les paramètres de diagnostic pour votre fabrique de données.

1.  Sélectionnez **Azure Monitor** -> **Paramètres de diagnostics** -> Sélectionner la fabrique de données -> Activer les diagnostics.

    ![monitor-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  Fournir des paramètres de diagnostic, y compris la configuration de l’espace de travail OMS.

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-oms-pack-from-azure-marketplace"></a>Installer le pack Azure Data Factory Analytics d’OMS à partir de la place de marché Azure

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Cliquez sur**Créer** et sélectionnez l’espace de travail OMS et les paramètres de l’espace de travail OMS.

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

## <a name="monitor-azure-data-factory-metrics-using-oms"></a>Surveiller les métriques d’Azure Data Factory à l’aide d’OMS

L’installation du pack **Azure Data Factory Analytics** d’OMS crée un ensemble d’affichages par défaut qui activent les métriques suivantes :

- Exécution d’ADF- 1) Exécution de pipeline par fabrique de données

- Exécution d’ADF- 2) Exécution d’activité par fabrique de données

- Exécution d’ADF- 3) Exécution de déclencheur par fabrique de données

- Erreur d’ADF-1) 10 premières erreurs de pipeline par fabrique de données

- Erreur d’ADF- 2) 10 premières erreurs de pipeline par fabrique de données

- Erreur d’ADF- 3) 10 premières erreurs de pipeline par fabrique de données

- Statistiques d’ADF- 1) Exécution d’activité par type

- Statistiques d’ADF- 2) Exécution de déclencheur par type

- Statistiques d’ADF- 3) Durée maximale d’exécution du pipeline

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Vous pouvez visualiser les métriques ci-dessus, consulter les requêtes derrière ces métriques, modifier les requêtes, créer des alertes, etc.

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="next-steps"></a>Étapes suivantes

Consultez [Surveiller et gérer les pipelines par programmation](https://docs.microsoft.com/en-us/azure/data-factory/monitor-programmatically) pour en savoir plus sur la surveillance et la gestion des pipelines en exécutant des scripts.
